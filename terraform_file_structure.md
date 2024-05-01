# Terraform File Structure & Formatting

Terraform files tsrat out with the main.tf file. Everything is added in 
there and only once it grows too big it gets split into more and more 
files.

This is why, when searching for smething in terraform repos, start with 
main.tf and then go through the others. 

## Formatting

To format a terraform repo after making changes run 

      terraform fmt -recursive

      
