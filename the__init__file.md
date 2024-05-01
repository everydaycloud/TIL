# The purpose of the __init__.py files

## Registering blueprints as part of a bigger blueprint

Every folder in the src/backend parts of the repos contains an __init__.py file. 
We don't use them all.  If a folder such as "staff" in the pharmacy contains 
files with routes (blueprints) then these can be registered in the __init__.py
file in the folder, so that they will become parts of a bigger blueprint, 
which is delcared in that file and then registered in run.py.

## Shortening the import path in run.py

In the workers folder we import all the workers into __init__.py like this

      import backend.workers.update_shipment_labels
      import backend.workers.update_shipments

This means that in run.py we don't need to write out these file paths again
and we can import the workers this way.

      import backend.workers


