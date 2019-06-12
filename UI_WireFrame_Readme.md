#Packages
    d3 - graphing
    React - UI
    NPM - package handling
    Babel - ES6 Transpiling
    

#Admin Panel
    Display helpful info about current states of job
     - live graph of COMPLETED, and ERRORed jobs
     - Create New Job Call to action

#Create New Job
    Input form for necessary info
    Name ( no duplicates, required)
    Description ( optional)     
    Scheduler ( NOW or DateTime)
    Job ( code to run / dropdown for selectable jobs)
    Create button 
        - POSTs data to server
        - hits /create REST endpoint
        - display CREATED / ERROR  status message
    
#Monitor Jobs
    View List of Currently Running Jobs
    hits /getJobs endpoint  
        - send JobState = [Running]
    Repolls every 15 seconds
    Users may select the X to cancel a currently running job

#Job History
    View List of Completed Jobs
    hit /getJobs endpoint
        - send JobState = [Completed, Error]
    Repolls every 15 seconds
    Upon selection of a job, dispalys more information
        - Job Description
        - Job that was Running
        - Error message if needed

#Post Design Analysis
 UI feels a little clunky, but gets the "job" done.
 Repolling to get new job status leaves a bad taste in my mouth.
 ##Redesign
  `On app load get state of all jobs
  Open Websocket where Job state changes will be sent through
  Upon recieving a wss message, parse and upate corresponding job`
  This allows new data to be reflected in the UI immediately. 
  Esp important for viewing ERROR
  ##Addition Funcionatliy for Consideration
  Search for specific job
  Cancelling Multiple Jobs (perhaps own UI)
  Login Panel

