Overall Design:
  Each component reads from a message queue, performs some work and writes back.
  Components should only be concerned with messages for them and ignore others.
  Each component will Log when a message is recieved,  Job state changes, and message sent.

JOB STATES
        CREATING - job has been created but has not been submitted for processing
        SCHEDULED - job has been submitting for processing
        RUNNING  - Job is currently running
        COMPLETED - Job has finished
        CANCELLING - Job is scheduled to be cancelled but has not been submitted to SDK

MESSAGE TYPES
    CREATE - create a new job
    SCHEDULE - schedule job
    CANCEL - cancle a currently running job

Job API:
   create - construct CREATE message
   cancel - construct CANCEL message, pass JobID

CreateJob
    Reads CREATE message type
    Construct a Job object and assigns an id
    Assigns a scheduled time if provided, otherwise SDK.now
    Sets Job status to CREATING
    Submits Job to database
    Writes to Message Queue. 

JobProcessor
    Reads SCHEDULE messages
    Submit job to SDK interface.
    If successful update Job State to SCHEDULED / RUNNING and update job in DB
    otherwise log error message
    (should implment a retry given what the error message is)

    Reads CANCEL messages
    Submit CANCEl TO SDK 
    on SUCCESS:
       update Job status to CANCELLED
       LOG
       update JOB within DB
    on FAILURE:
        LOG
       if SDK / Communication error: retry after sometime
       otherwise 

CancelJob
    Reads CANCEL messages
    Sets Job state to CANCELLING
    update JOB within DB

    