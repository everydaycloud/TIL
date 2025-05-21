      files_ = await request.files.getlist("file")
        descriptions = request.form.getlist("description")

        for file_, description in zip(files_, descriptions):


      class zip(
          iter1: Iterable[_T1@__new__],
          iter2: Iterable[_T2@__new__],
          /,
          *,
          strict: bool = ...
      )

The zip object yields n-length tuples, where n is the number of iterables passed as positional arguments to zip(). The i-th element in every tuple comes from the i-th iterable argument to zip(). This continues until the shortest argument is exhausted.
If strict is true and one of the arguments is exhausted before the others, raise a ValueError.
>>> list(zip('abcdefg', range(3), range(4)))
