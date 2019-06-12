Objective: 
  Build a cron job management system. 

Requiremnts:
    Jobs have three states:
        RUNNING
        SCHEDULED
        COMPLETED   
    Admin UI uses JSON / REST
    SDK for start/stop job
    Log metadata regarding job invocation and status
    Log output of job
    UI shows job status

Considerations:
    How to handle long running jobs?
        - Client is in charge of cancelling jobs
    Thread Safety
        - For multiple users we will need to ensure DB read/write locks
    How do we update job list?
        -repoll
        



Design Overview:
    Backend
     Will be in charge of all processing. 
     Have REST API for job creation and stopping
        - Use an event-based message system for job processing
        - Seperate concerns for fault tolerance and scaling
    Frontend
        Send jobs to backend
        Display status of jobs
            - should be live ( updates as job status changes)
        Can cancel jobs
