pipeline {
    agent {
	docker{
	image 'maven:3.6.1-jdk-8-alpine'
	args '-v $HOME/.m2:/root/.m2'
	}
    }
    
    stages {
        stage('worker build') {
            when{
                changeset "**/worker/**"
            }
            steps {
                echo 'Compiling worker app'
		dir('worker'){
		  sh 'mvn compile'
		}
            }
        }
        stage('worker test') {
            when{
                changeset "**/worker/**"
            }
            steps {
                echo 'Running Unit Test on worker app'
                dir('worker'){
                  sh 'mvn clean test'
                }
            }
        }
        stage('worker-docker-package') {
	    when{
		changeset "**/worker/**"
	    }
            steps {
                echo 'Packaging worker app with docker'
		script{
		docker.withRegistry('https://index.docker.io/v1', 'dockerlogin' {
			def workerImage = docker.build("avsdom/worker:v${env.BUILD_ID}", "./worker"}
			workerImage.push()
			workerImage.push("${env.BRANCH_NAME}")
		}
            }
        }
        stage('result build') {
            when{
                changeset "**/result/**"
            }
            steps {
                echo 'Compiling result app'
		dir('result'){
		  sh 'npm install'
		}
            }
        }
        stage('result test') {
            when{
                changeset "**/result/**"
            }
            steps {
                echo 'Running Unit Test on result app'
                dir('result'){
		  sh 'npm install'
                  sh 'npm test'
                }
            }
        }
        stage('vote build') {
            when{
                changeset "**/vote/**"
            }
            steps {
                echo 'Compiling vote app'
		dir('vote'){
		  sh 'pip install -r requirements.txt'
		}
            }
        }
        stage('vote test') {
            when{
                changeset "**/vote/**"
            }
            steps {
                echo 'Running Unit Test on vote app'
                dir('vote'){
		  sh 'nosetests -v'
                }
            }
        }


    }
    post{
        always{
            echo 'Pipeline for instavote app is complete ...'
        }
    }
}
