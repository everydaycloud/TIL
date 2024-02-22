# Pytest details

1. You can run an individual test or a folder of test by running

          pytest <path-to-test>

2. If you want to print anything out in the tests Pytest will ignore
   this by default. To see any print statements run

          pytest -s

3. It also doesn't show full error output messages, so to see them use

         pytest -vv

4. In Pytest you can also print out the value if the original value isn't asserted.

        assert = response.status == 200, response

   In this example if the response status isn't 200, the response will be
   printed instead.

