pipeline {
    agent any

    environment {
        VERCEL_TOKEN = credentials('VERCEL_TOKEN')
    }

    options {
        skipDefaultCheckout(true) // Skip the default checkout
    }
    stages {

        stage ('Clean up code') {
            steps {
                cleanWs()
            }   
        }

        stage('Checkout using SCM') {
            steps {
                checkout scm // Checkout the code
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:22.11.0-alpine3.20'
                    args '-u root'
                    reuseNode true // Reuse the node for the next stages
                }
            }

            steps {

                    sh '''
                        ls -l
                        node --version
                        npm --version
                        npm install
                        npm run build
                        ls -l
                    '''
                
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:22.11.0-alpine3.20'
                    args '-u root'
                    reuseNode true // Reuse the node for the next stages
                }
            }

            steps {
                sh '''
                    npm run test
                '''
            }
        }
        stage('Deploy') {
            agent {
                docker {
                    image 'node:22.11.0-alpine3.20'
                    args '-u root'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install -g vercel
                    vercel --prod --token=${{ secrets.VERCEL_TOKEN }} --confirm --name=cicdproject
                '''
            }
        }

    }
}