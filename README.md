"# Postman_Collection" 
--integrated to the azure pipeline and ran through newman--

Steps:

Step 1:Install Node.js
verify->node -v, npm -v

Step 2: Install Newman
verify->newman -v

Step 3: Export Collection from the postman (.json)
and Export environments(if required)

Step 4: Run collection using newman
newman run collection_name.json
newman run MyCollection.json -e DevEnvironment.json

Step 5: Generate HTML report
npm install -g newman-reporter-html
newman run MyCollection.json -e DevEnvironment.json -r html

Step 6: Generate Detailed HTML Report (Recommended for CI/CD)
npm install -g newman-reporter-htmlextra
newman run MyCollection.json -e DevEnvironment.json -r htmlextra

Step 7: Using Data file
create:
username,password
user1,pass1
user2,pass2
user3,pass3
newman run collections/MyCollection.json -e environments/Dev.json -d data/data.csv


API_Testing
│
├── collections
│     └── MyCollection.json
│
├── environments
│     └── Dev.json
│
├── data
│     └── data.csv
│
├── reports
│
└── run command

Start Newman
     ↓
Read Collection JSON
     ↓
Load Environment Variables
     ↓
Run Pre-request Script
     ↓
Send API Request
     ↓
Receive Response
     ↓
Run Tests Script
     ↓
Save Results
     ↓
Next Request
     ↓
Generate Report
     ↓
Finish


Project
│
├── collections
├── environments
├── reports
├── data
└── newman command

INTEGRATING TO THE PIPELINE(AZURE):

Azure DevOps Repo
        ↓
Pipeline Trigger
        ↓
Install Node.js
        ↓
Install Newman
        ↓
Run Collection
        ↓
Generate Report
        ↓
Publish Report

step 1 : push files to repo (azure repo)
step 2 : create pipeline in azure devops (test.yaml)
step 3 : basic nemman yaml
copy below and paste in the yaml.file
Push to main → pipeline runs
Push to develop → pipeline runs
PR to main → pipeline runs


trigger: (Whenever a developer pushes code to the main branch Pipeline will automatically start)
- main
trigger:
  branches:
    include:
      - main

pr:
  branches:
    include:
      - main


/*  pool:                                       
  vmImage: 'windows-latest' */
  pool:
  name: Default

steps:

- task: NodeTool@0
  inputs:
    versionSpec: '18.x'
  displayName: 'Install Node.js'

- script: |
    npm install -g newman
    npm install -g newman-reporter-htmlextra
  displayName: 'Install Newman'

- script: |
    newman run collections/MyCollection.json ^
    -e environments/Dev.json ^
    -d data/data.csv ^
    -r cli,htmlextra ^
    --reporter-htmlextra-export reports/report.html
  displayName: 'Run Newman Collection'

- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: 'reports'
    ArtifactName: 'NewmanReport'
  displayName: 'Publish Report'
