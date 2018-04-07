pipeline {
	agent {
		label 'master'
    }
    environment {
		MAJOR_VERSION = 1
    }
    stages {
        stage('Unit Tests') {
            agent {
				label 'master'
            }
            steps {
                sh 'ant -f test.xml -v'
                junit 'reports/result.xml'
            }
        }
        stage('build') {
            agent {
                label 'master'
            }
            steps {
               sh 'ant -f build.xml -v'
            }
	    	post {
        		always {
            		archiveArtifacts artifacts: 'dist/*.jar' , fingerprint: true
        		}
	    	}
        }
        stage('deploy') {
            agent {
                label 'master'
            }
            steps {
                sh "echo Branch: ${env.BRANCH_NAME}"
                sh "pwd"
                sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
            }
        }
		stage('Running on CentOS') {
	  		agent{
	    		label 'master'
	  		}
			steps {
				sh "wget http://alvinwang6.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
	    		sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
	  		}
		}
		stage('promote to green') {
	  		agent{
	    		label 'master'
	  		}
	  		when {
	    		branch 'master'
	  		}
	  		steps {
            	sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/"
	  		}
		}
		stage('Promote development branch to master') {
	  		agent{
	    		label 'master'
	  		}
	  		when {
	    		branch 'development'
	  		}
	  		steps {
	    		echo "Stashing any local changes"
	    		sh 'git stash'
	    		echo "eching out deveopment branch"
	    		sh 'git checkout development'
	    		echo "checking out master branch"
	    		sh 'git pull origin'
	    		sh 'git checkout master'
	    		echo 'merging development into master branch'
	    		sh 'git merge development'
	    		echo 'PUshing to origin master'
	    		sh 'git push origin master'
	    		echo 'tagging release'
	    		sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
	    		sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
	  		}
			post {
	  			success {
	    			emailext(
	    			  subject: "${env.JOB_NAME} [env.{BUILD_NUMBER}] succeeded.",
	    			  body: "check ${env.JOB_NAME} [env.{BUILD_NUMBER}]",
	    			  to: "wangnan.alvin@gmail.com"
	    			)
	  			}
			}
		}

    }
    post {
    	failure {
        	emailext(
        	  subject: "${env.JOB_NAME} [env.{BUILD_NUMBER}] Failed!",
        	  body: "check ${env.JOB_NAME} [env.{BUILD_NUMBER}]",
        	  to: "wangnan.alvin@gmail.com"
        )
      } 
    } 
}

