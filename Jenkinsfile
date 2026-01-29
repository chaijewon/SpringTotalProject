pipeline {
	agent any
	stages {
		stage('Git Check Test') {
			steps {
				git branch: 'main',
				url: https://github.com/chaijewon/SpringTotalProject.git
			}
		}
		
		stage('Check Git Info') {
			steps {
				sh '''
				    echo "===Git Info==="
				    git branch
				    git log -1
				   '''
			}
		}
	}
}