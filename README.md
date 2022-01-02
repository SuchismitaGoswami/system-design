## Fundmentals of system design

## System Design Components
- Logical Entities  
  - Data Storing system - (Database)
  - Presentation
- Tangible Entities
  - Technologies used to achieve the points discussed above

- PIC : TODO

## Client Server Architecture
  - Thick Clients: Client contains heavy logics in it. Example: Gameing app, Video Editing client, etc.
  - Thin Clients: Client contains less business logic  Example: web client talking to the server to ftech data 
  

## Deployment
- Canary deployment vs rolling deployment

## Data Flow and Data system
- Data flow
- Presentation layer (text/audio/video etc.) -> Application layer (json/xml etc) -> Database layer (table/collection etc.)
- Factors to be taken care of 
  - Data types system is working on
  - Data storage
  - Type of system: whether the sytem needs to store a huge amount of data frequently or retrieve data very often. (e.g. streaming system, transaction system)


## Apprach to system design
- Finalize the function and non-functional requirement
- Design the high level component diagram
- Talk about connecting multiple componenets by designing APIS
- Specify why you have choosen this design by mentioning drawback of other approaches
- Database and caching design to meet the non functional requirement
- Talk about the critical part of the application
- How to improve performance of the system as well as the customer 
- Monitoring & Operation - logging, metrics, auditing, making system more reliable, single point of failure, load balancer, etc..

