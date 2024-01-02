pipeline {
    agent any

    stages {
        
        stage("Git Checkout"){
            steps{
               git branch: 'main', credentialsId: '43c0ce5e-a862-4298-ad2d-269ad14beb70', url: 'https://github.com/Charykarthik/usecase3'
            }
        }
        stage('GCP Auth') {
            steps {
                withCredentials([file(credentialsId: '979edf96-af96-44c5-be7c-f8cde4c77e66', variable: 'GCP_credentials')]) {
                        sh "gcloud auth activate-service-account --key-file=$GCP_CREDENTIALS"
                }
            }
        }
        stage('Database Dump') {
            steps {
                script {
                    // Define your SQL Server connection details
                    def vmInstance = 'sql-server'
                    def username = 'root'
                    def password = 'Password@123'
                    def databaseName = 'karthik'
                    def dumpFileName = 'dump.sql'

                    // Command to perform SQL dump using sqlcmd utility
                    sh "sqlcmd -S ${vmInstance} -U ${username} -P ${password} -d ${databaseName} -Q 'BACKUP DATABASE ${databaseName} TO DISK = ''/${dumpFileName}'''"
                }
            }
        }
        
        
        stage('Create GCS Bucket and BigQuery Dataset') {
            steps {
                script {

                    def projectId = 'bold-guide-405907'              // Replace with your GCP project ID
                    
                    def gcsBucketName = 'sceg_dump_buck'    // Replace with your GCS bucket name

                    def datasetId = 'dump'             // Replace with your BigQuery dataset ID
                                        
                    sh "gcloud storage buckets create gs://${gcsBucketName} --uniform-bucket-level-access"         // Create GCS bucket using gsutil command

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
v
