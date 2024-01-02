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
                withCredentials([file(credentialsId: '112090211538399136158', variable: 'cc6391da68fb78ec04dc985787da4d97ed871839')]) {
                        sh "gcloud auth activate-service-account --key-file=-----BEGIN PRIVATE KEY-----\nMIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQCbzVznbOAMRUbF\nHwtWdzw5P7KG9VNjmSA6gcFsjL+vKdwEvJYGLuXRtcVyo5oTKMvo173gZ33AMJwM\nvmDXic4/9aaeT79BTzKYzpvHjYqh7UrfssyAxU3lt5UMcOHWNX5u8xntcCJ55EnS\nR/suOaWEeXsr7dWyZeTZQ9+Blc17QFO6+7fgkv/jPBgugIQgbZwTmw+E+1mDPG8g\nSoFmAKhuJaSTsLa0JDGJebtIndhmAD5X1qsaXmJcd0xXgR2sOtrCVcZWHTb3iGwd\nWgo//oIDG8p0LlwrYX26eA2DtGIZq1bTXbeOqGMF0C85BLZQvL5As5ZZbVqGX02e\nLXAtOAKNAgMBAAECggEABqN/AGtutocjJ1YVzRbijYvtxvtgFL/bFDMgM0hiCIwv\nkFIa0GOiLkfhBGv9LjDQU3kR4aR3Qr+vfomEJdr821PTyC4ct6MwxDsVfzDERfcO\n+C4tsZT2p0Gly7Rpow9/dinMooXjKi90h83CYataJqVofqwxLVtTH4DZXrOr4EMB\n2/1yk7hCQgKRyX8lv4Vbq5vawOD6g6UnNjF8x+m7/OlyEeGUD5WTp3w/VstkhhFa\nZuxPtuXjh/fM7UyFeIaFu9xB6dIN33FdnjmKfSWt78nmo85GLSPzgsomSBImqGoZ\nc7cjveBw6or1pPduNVcJj5jzCxk3UX4gSMGSxi6qnwKBgQDX9MlsDiJwJRwlk2P2\n5skhVNGvzfZW6m/e0Z0RMyx/u9ub+UEu8zlmboWTga++neGM8q3rdfhzJXkaYz6r\nKQjdaTRQtBhuU3Pi2vKYkqHe9x7CZLBnlaeF5F1TAOF8GT3YA+fhi/E0KLTs9xO6\nTsXsyTzqjK1S10dd6cczRFJbuwKBgQC4sSEOJ+UgkCqCQNGKLzpNpQYe4UKUQXbQ\n0GyJDSY3ZZBlKgeTv3vMy6olBeOTKXF6OlPqHStcdSnXdnEJ9C8reJNipjFdkNpF\njLy6LJcSS9q0wXB7H94eo2TXL1CbyRQr0QKmwEfo3znz1k8eIakt6Fv7I63i249/\n9vfEkGQiVwKBgQDCGJHcm4uaTdBReXqUU/McERbxsuUgU08+7hkEd4EcQSTtuAY9\nA4BorxdBb4MciNxLCWcFxR8LpveEnwSZY6VDEyZhDq0So9D5aR43wTrZojQ0NblT\nMG/D6G90hcsj9a/e8gu8bUHaRmfuv1cs0VYVLv2gNdYmKbVMFCzkyBhZtwKBgGRh\nCMkrz7EyCZh/aCNFZde7YCc6YLg5GD2HeeXG+sVS8va4sCOSB9QXwSKcH8Lk3GME\n/oSYU+/NUe9j1S4Gw/hJcTlFgEENMERqqEw7XeQ4y+Y/2EVi3ZMjVPRLJ/FziP4D\nfABrMuoTIOkwyhFjVBK6XfuRubk5gbWX4bO4kmPDAoGBALIezJlbYdcU6qPnK87S\nBycZOfhStOaMcmBuPMmSEgzXfDUTK+vBa7O5ALZj7F2OQUth2WHINj+pkV1Z/cjt\nSApbYKD8oFf7tedZrx0Sen6BDk9PeCdJHbp2hGz2DF4yMV7fmAJAU3iJdN0TIArD\nsWOnj0qZMwwS0ihQjPOh89Ls\n-----END PRIVATE KEY-----\n"
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
