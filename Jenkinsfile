pipeline {
    agent {
        label 'master'
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
		sh "mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
                sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
            }
        }
	stage('Running on CentOS') {
	  agent{
	    label 'CentOS'
	  }
	  steps {
	    sh "wget http://alvinwang6.mylabserver.com/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
	    sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
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
            sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/"
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
	    sh 'git checkout master'
	    echo 'merging development into master branch'
	    sh 'git merge development'
	    echo 'PUshing to origin master'
	    sh 'git push origin master'
	  }
	}
    }

}
