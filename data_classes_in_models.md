# Data classes in the model

Every single file we write can technically contain a custom data class
that's being used in that file or it can import a dataclass (pydantic 
BaseModel) from another place if it's shared between files. 

BUT in case of files in "models" these dataclasses need to reflect the 
data structure from the table and are not supposed to deviate from that.

