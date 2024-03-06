### Update Dependencies

1. Go through Pharmacy, Clinic, Sapphire Patient App,
   Roky and Common library repos and run

       npm update // on the frontend(s)

       pdm update // on the backend
       pdm run test // to check that it didn't break anything

2. After updating both in a repo, run the localhost app to double check that
   nothing is broken.
   
