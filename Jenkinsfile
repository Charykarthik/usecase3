pipeline {
    agent any

    stages {
        
        stage("Git Checkout"){
            steps{
               git branch: 'main', credentialsId: 'karthik', url: 'https://github.com/Charykarthik/usecase3'
            }
        }
        stage('Database Dump') {
            steps {
                script {
                    // Install AWS CLI
                    sh 'apt-get update && apt-get install -y awscli'

                    // Replace with your AWS CLI command to create a database dump
                    sh 'aws ec2 run-instances --instance-id sql-server --command "mysqldump -u root -pPassword@123 karthik > /tmp/dump.sql"'
                }
            }
        }

        stage('Create S3 Bucket & Copy Dump File') {
            steps {
                script {
                    // Replace with your desired S3 bucket name
                    def s3BucketName = 'sceg_dmp_bucket'

                    // Create S3 bucket
                    sh "aws s3api create-bucket --bucket ${s3BucketName} --region us-east-1"

                    // Copy dump file to S3 bucket
                    sh 'aws s3 cp /tmp/dump.sql s3://${s3BucketName}/dump.sql'
                }
            }
        }
        
        stage('Create GCS Bucket and BigQuery Dataset') {
            steps {
                script {

                    def projectId = 'bold-guide-405907'              // Replace with your GCP project ID
                    
                    def gcsBucketName = 'sceg_dump_buck'    // Replace with your GCS bucket name

                    def datasetId = 'dump'             // Replace with your BigQuery dataset ID
                                        
                    sh "gsutil mb gs://${gcsBucketName}"          // Create GCS bucket using gsutil command

                    sh "bq mk --project_id=${projectId} ${projectId}:${datasetId}"  // Create BigQuery dataset using bq command
                }
            }
        }
        
        
        stage('Create BigQuery Table') {
            steps {
                script {
                    // Replace with your BigQuery table schema
                    def tableSchema = 'field1:STRING, field2:STRING, field3:STRING, field4:STRING, field5:STRING,'

                    // Replace with your BigQuery table name
                    def tableName = 'sql'

                    // Create BigQuery table using bq command
                    sh "bq mk --schema='${tableSchema}' ${projectId}:${datasetId}.${tableName}"
                }
            }
        }

        
        stage('Transfer File from S3 to GCS') {
            steps {
                script {
                    // Replace with your AWS access key, secret key, region, and S3 bucket details
                    def awsAccessKeyId = 'AKIAQEKUEUA6XDKAHP7N'
                    def awsSecretAccessKey = 'BTag6DecoMb//4y2ZjyIzxXws68sqNmlRntr55rm'
                    def awsRegion = 'us-east-1'  // Replace with your AWS region
                    def s3BucketName = 'sceg_dmp_bucket'
                    def s3ObjectKey = 'path/to/your/file.txt'

                    // Replace with your GCS service account key path
                    def gcsServiceAccountKeyPath = '/bold-guide-405907-cc6391da68fb.json'
                    def gcsBucketName = 'sceg_dump_buck'

                    // Configure AWS CLI credentials
                    //sh "aws configure set aws_access_key_id ${awsAccessKeyId}"
                    //sh "aws configure set aws_secret_access_key ${awsSecretAccessKey}"
                    //sh "aws configure set region ${awsRegion}"

                    // Download file from S3
                    sh "aws s3 cp s3://${s3BucketName}/${s3ObjectKey} /tmp/file.txt"

                    // Upload file to GCS
                    sh "gsutil cp /tmp/file.txt gs://${gcsBucketName}/path/to/destination/file.txt -i ${gcsServiceAccountKeyPath}"
                }
            }
        }

    
    
        stage('Transfer File to BigQuery') {
            steps {
                script {
                    // Replace with your GCP project ID
                    def projectId = 'bold-guide-405907'

                    // Replace with your BigQuery dataset ID
                    def datasetId = 'dump'

                    // Replace with your BigQuery table name
                    def tableName = 'sql'

                    // Replace with your GCS bucket name and file path
                    def gcsBucket = 'sceg_dump_buck'
                    def gcsFile = 'dump.sql'

                    // Transfer file from GCS to BigQuery table using bq command
                    sh "bq load --project_id=${projectId} --skip_leading_rows=0 --source_format=CSV ${projectId}:${datasetId}.${tableName} gs://${gcsBucket}/${gcsFile}"
                }
            }
        }
    }
}

