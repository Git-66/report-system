# report-system

## What I changed and added to the project.

### 1. Setup AWS account and IAM role, created the following AWS resources and made them connected in order to run the project successfully.
AWS Resources I created:
1. One SNS: reporting_topic
2. Five SQS: Excel_Request_Queue, Excel_Response_Queue, PDF_Request_Queue, PDF_Response_Queue and email_queue.
3. One S3 bucket: reporting-system-generated-files
4. One Lambda function: sendGeneralEmail

Changes I made:
1. Configure the correct properties in all the files application.properties of three services.
2. Fix sqs.url and s3.bucket in the application.properties to generate PDF/Excel correctly.

### 2. Add the new function that uploads the generated Excel file into S3 bucket, in the meantime remove the local file in the disk. Code change is in the Java class ExcelServiceImpl.java, I added S3Client to update file in the function generateFile().

### 3. Fix the function "Download Excel" in the ClientService. As Excel file is updloaded to the S3 and local file is removed, we have to do code change to download Excel file from S3 instead of the disk in the ReportController.java

### 4. Add the new feature for deleting report. Add the delete operation in the different layers such as repository, service, controller.
1. In the repository layer, use the existing function in the CrudRepository to delete report from H2.
2. In the service layer, in addition to delete the report including PDF and Excel from S3 via S3Client in the Java class ReportServiceImpl.java.
3. In the controller layer, implement the Restful API delete operation in the ReportController.java. First of all, I delete S3 file and then remove it from memory database H2.

### 5. Fix the function "Create Using Async API" when genereate report, so that report can be successfully generated with Async and listed in the frontend by correct configurations.

### 6. Improve sync API performance by using multithreading and sending request concurrently to both services. I amn't only using multithreading in the service call, but also in the phase of saving file to H2 and sending email.

### 7. Use a database instead of hashmap in the ExcelRepositoryImpl. I used MongoDB to replace hashmap, the detailed code change is as following.
1. Add MongoDB dependency into the pom.xml
2. Change the POJO ExcelFile.java to support MongoDB
3. Let interface ExcelRepository extend MongoRepository<ExcelFile, String> and don't need ExcelRepositoryImpl.java
4. In the service layer, use MongoRepository crud in the ExcelServiceImpl.java
