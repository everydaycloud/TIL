Service-orientated architecture at the moment. 
4+ bigger services - clinic, pharmacy etc

Microservices are usually defined by a subset of the data.

Network calls are expensive

Microservices make communication easier, but are more complex to monitor

Monoliths are less costly

Monolith on 3 servers,
but more services need to be split across instances

AWS will distribute this automatically for you via ECS.

You can also use Kubernetes for this (EKS) and it will figure out where to put all the services.
But someone has to manage Kubernetes full time.

ECS is an Amazon product, so you are tied to them. 

A few different types
 - ECS fargate --> sorts this part of infrastructure for you
 - EC2 --> manual instance setup and then you need to tell ECS what to do with it
 - EKS --> the Kubernetes managed option
 - You can also buy virtual servers and install everythign yourself, but that's
   tricky and labour intensive.
