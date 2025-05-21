This is a mock s3 server for testing communication with AWS


Install aioboto3
Install “moto[server]”

Conftest.py

      
      class SessionProtocol(Protocol):
          def client(self, *args: Any, **kwargs: Any) -> Any: ...
          def resource(self, *args: Any, **kwargs: Any) -> Any: ...
      
      
      def create_fake_session(base_class: Any, url_overrides: Dict[str, str]) -> Any:
          class FakeSession(base_class):  # type: ignore[misc]
              def __init__(self, *args: Any, **kwargs: Any):
                  super(FakeSession, self).__init__(*args, **kwargs)
                  self.__url_overrides = url_overrides
                  self.__secret_key = "test-key-id"
                  self.__access_key = "test-access-key"
      
              def client(self, *args: Any, **kwargs: Any) -> Any:
                  if "endpoint_url" not in kwargs and args[0] in self.__url_overrides:
                      kwargs["endpoint_url"] = self.__url_overrides[args[0]]
      
                  kwargs["aws_access_key_id"] = self.__secret_key
                  kwargs["aws_secret_access_key"] = self.__access_key
      
                  return super(FakeSession, self).client(*args, **kwargs)
      
              def resource(self, *args: Any, **kwargs: Any) -> Any:
                  if "endpoint_url" not in kwargs and args[0] in self.__url_overrides:
                      kwargs["endpoint_url"] = self.__url_overrides[args[0]]
      
                  kwargs["aws_access_key_id"] = self.__secret_key
                  kwargs["aws_secret_access_key"] = self.__access_key
      
                  return super(FakeSession, self).resource(*args, **kwargs)
      
          return FakeSession
      
      
      _proxy_bypass: Dict[str, str] = {
          "http": "",
          "https": "",
      }
      
      
      def start_service(host: str, port: int) -> sp.Popen[bytes]:
          moto_svr_path = shutil.which("moto_server")
          if not moto_svr_path:
              raise RuntimeError("moto_server not found in PATH")
          args = [moto_svr_path, "-H", host, "-p", str(port)]
          process = sp.Popen(args, stdin=sp.PIPE, stdout=sp.PIPE, stderr=sp.PIPE)
          url = f"http://{host}:{port}"
      
          for _ in range(30):
              if process.poll() is not None:
                  stdout, stderr = process.communicate()
                  logger.error("moto_server exited with status %s", process.returncode)
                  logger.error("stdout:\n%s", stdout.decode())
                  logger.error("stderr:\n%s", stderr.decode())
                  pytest.fail("Cannot start moto_server")
      
              try:
                  requests.get(url, timeout=5, proxies=_proxy_bypass)
                  break
              except requests.exceptions.ConnectionError:
                  time.sleep(0.5)
          else:
              stop_process(process)
              pytest.fail("Could not connect to moto_server after 30 retries")
      
          return process
      
      
      def stop_process(process: sp.Popen[bytes]) -> None:
          try:
              process.send_signal(signal.SIGTERM)
              process.communicate(timeout=20)
          except sp.TimeoutExpired:
              process.kill()
              outs, errors = process.communicate(timeout=20)
              exit_code = process.returncode
              msg = "Child process finished {} not in clean way: {} {}".format(
                  exit_code, outs.decode(), errors.decode()
              )
              raise RuntimeError(msg)
      
      
      @pytest.fixture(scope="session")
      def s3_server() -> Generator[str, None, None]:
          host = "localhost"
          port = 5002
          url = "http://{host}:{port}".format(host=host, port=port)
          process = start_service(host, port)
          yield url
          stop_process(process)


Test_file_store.py

        
        @pytest.mark.asyncio
        async def test_file_store_tagging(
            app: Quart, tmp_path: Path, s3_server: str, monkeypatch: pytest.MonkeyPatch
        ) -> None:
            FakeSession = create_fake_session(Session, {"s3": s3_server})
            monkeypatch.setattr("aioboto3.Session", FakeSession)
            session = FakeSession()
            async with session.client(
                "s3", region_name="us-east-1", endpoint_url=s3_server
            ) as client:
                await client.create_bucket(Bucket="test-bucket")
        
                app.config["FILESTORE_PATH"] = str(tmp_path)
                app.config["FILESTORE_BUCKET"] = "test-bucket"
                app.config["AWS_ACCESS_KEY_ID"] = "test-key-id"
                app.config["AWS_SECRET_ACCESS_KEY"] = "test-access-key"
        
                file_store = FileStoreExtension()
                file_store.init_app(app)
        
                key = uuid4()
                data = BytesIO(b"Hello")
        
                await file_store.client.put_file(key, data)
                await file_store.client.tag_file_for_deletion(key, delete_after_days=14)
        
                response = await client.get_object_tagging(Bucket="test-bucket", Key=str(key))
                tags = {tag["Key"]: tag["Value"] for tag in response["TagSet"]}
                assert tags.get("delete_after") == "14_days"
        
        
        OPTION 2 - just mocking the context manager   class AsyncMockContextManager:
            def __init__(self, mock_obj: Any) -> None:
                self.mock_obj = mock_obj
        
            async def __aenter__(self) -> Any:
                return self.mock_obj
        
            async def __aexit__(
                self,
                exc_type: Optional[Type[BaseException]],
                exc: Optional[BaseException],
                tb: Optional[Any],
            ) -> None:
                pass
        
        
        @pytest.mark.asyncio
        async def test_file_store_tagging(app: Quart, tmp_path: Path) -> None:
            mock_client = mock.AsyncMock()
        
            mock_client.create_bucket.return_value = {}
            mock_client.get_object_tagging.return_value = {
                "TagSet": [{"Key": "delete_after", "Value": "14_days"}]
            }
            mock_session = mock.MagicMock()
            mock_session.client.return_value = AsyncMockContextManager(mock_client)
        
            with mock.patch(
                "common.lib.file_store.aioboto3.Session", return_value=mock_session
            ):
                async with mock_session.client(
                    "s3", region_name="us-east-1", endpoint_url="test"
                ) as client:
                    await client.create_bucket(Bucket="test-bucket")
        
                    app.config["FILESTORE_PATH"] = str(tmp_path)
                    app.config["FILESTORE_BUCKET"] = "test-bucket"
                    app.config["AWS_ACCESS_KEY_ID"] = "test-key-id"
                    app.config["AWS_SECRET_ACCESS_KEY"] = "test-access-key"
        
                    file_store = FileStoreExtension()
                    file_store.init_app(app)
        
                    key = uuid4()
                    data = BytesIO(b"Hello")
        
                    await file_store.client.put_file(key, data)
                    await file_store.client.tag_file_for_deletion(key, delete_after_days=14)
        
                    response = await client.get_object_tagging(
                        Bucket="test-bucket", Key=str(key)
                    )
                    tags = {tag["Key"]: tag["Value"] for tag in response["TagSet"]}
                    assert tags.get("delete_after") == "14_days"
