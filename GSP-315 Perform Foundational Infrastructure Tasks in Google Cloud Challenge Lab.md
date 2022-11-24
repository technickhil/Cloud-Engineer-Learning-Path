# GSP-315 Perform Foundational Infrastructure Tasks in Google Cloud Challenge Lab


## Task 1. Create a bucket

1) Navigation menu > Cloud Storage > Browser > Create Bucket
2) Name your bucket > [***Enter Bucket Name which in on left panal***] > Continue
3) Choose where to store your data > Region: us-east1 > Continue
4) Use default for the remaining
5) Create



## Task 2. Create a Pub/Sub topic
1) Navigation menu > Pub/Sub > Topics
2) Create Topic > Name: [***Enter Topic Name which in on left panal***] > Create Topic



## Task 3. Create the thumbnail Cloud Function
1) Navigation menu > Cloud Functions > Create Function
2) [***Function Name which in on left panal***]
3) HTTP -> Trigger type -> Cloud Storage
	   -> Event Type -> On (finalizing/creating) file in the selected bucket
	   -> Bucket -> select bucket you created
4) Save
5) Next
6) Entry Point -> thumbnail
7) Node.js 14
8) n line 15 of index.js replace the text ***REPLACE_WITH_YOUR_TOPIC ID*** with the Topic Name you created in task 2.
9) Copy Paste index.js and package.js with necessary changes
10) Deploy
11) Download the img from URL {Map.jpg}
12) Navigation menu > Cloud Storage > Browser > Select your bucket > Upload files
13) Upload the map.jpg img in bucket



## Task 4. Remove the previous cloud engineer
1) Navigation menu > IAM & Admin > IAM
2) Search for the "Username 2" > Edit > Delete Role
