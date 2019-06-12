# Overall Design:
  Each component reads from a message queue, performs some work and writes back.
  Components should only be concerned with messages for them and ignore others.
  Each component will Log when a message is recieved,  Job state changes, and message sent.
  We keep the DB as a single source of truth in case there are failures, outages, etc.

# Packages to Consider
  Maven - package handling
  Guice - Dependency Injection
  Hibernate - DB access
  Jackson for JSON parsing
  Log4j - Logging
  RabbitMQ - message pipelines
  Jersey - REST library

  `Note: This was based on familiarty. I didn't have time to consider different packages`

# JOB STATES
  CREATING - job has been created but has not been submitted for processing
  
  SCHEDULED - job has been submitting for processing
  
  RUNNING  - Job is currently running
  
  COMPLETED - Job has finished
  
  CANCELLING - Job is scheduled to be cancelled but has not been submitted to SDK
  
  CANCELLED - Job has been cancelled by user

# MESSAGE TYPES
  CREATE - create a new job
  
  SCHEDULE - schedule job
  
  CANCEL - cancle a currently running job
  
  STATUS - get status of ALL or one job

# Job API:
  create - construct CREATE message
  
  cancel - construct CANCEL message, pass JobID
  
  status - construct STATUS message

# CreateJob
  Reads CREATE message type
  
  Construct a Job object and assigns an id
  
  Assigns a scheduled time if provided, otherwise SDK.now
  
  Sets Job status to CREATING
  
  Submits Job to database
  
  Writes to Message Queue. 

# JobScheduler Interface
  Reads SCHEDULE messages
  
  Submit job to SDK interface.
  
  If successful update Job State to SCHEDULED for scheduled jobs or RUNNING 
  
  otherwise log error message
    -(should implment a retry given what the error message is)
    
  Update job in DB  
  
  Write to Message Queue

  Reads CANCEL messages
  
  Submit CANCEl TO SDK 
  
  on SUCCESS
 
     update Job status to CANCELLED
     LOG
     update JOB within DB
     Write to Message Queue
  on FAILURE:
  
      LOG
      if SDK / Communication error: retry after sometime
      otherwise 

# CancelJob
  Reads CANCEL messages
  
  Sets Job state to CANCELLING
  
  update JOB within DB

# Jobs SDK 
  Upon submitting a job, returns a the Job's New State or ERROR

# GetJobs

  Reads STATUS message
  
  Payload Options
  
    ALL - get all jobs 
    TimeFrame- get jobs within set time
    JobState - filter jobs by state (  COMPLETED, SCHEDULED, etc. etc.)
        

# Post Design Analysis

  ## Database issues
  Interacting with the DB this often can lead latency as writing locks a db
  
  ### Consideration when selecting a DB:
  
  - locking at a row level
  
  - seperate tables per client so two clients aren't preventing each other from updating jobs
  ### Possible Improvements
  
  Create a cache that will be referened when updating jobs 
  
  if a job can't be found read from DB
  
  Cache dumps to DB after X amount of time elapses
  
  ## Message Queue Issues
  
  ### Possible Impormvents
  
  Seperate Message Queue into multiple queues with a message router that handles message delivery
  
  ## Security
  
  User Validation
  
  Prevent overload of REST endpoint with IP whitelist ( only allow clients who bought software )
  
  Check messages haven't been corrupted 
