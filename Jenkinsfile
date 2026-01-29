pipeline {
	agent any
	stages {
		
		
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