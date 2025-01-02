pipeline {
	agent{							
        label "buildAgent"	
    }
	
	stages {
		stage ('Clean Workspace'){
			steps {
                		echo "****** Workspace Cleanup running....******"
				cleanWs()
			}
		}
	
		stage ('Git Checkout'){
			steps {
                		echo "****** Git Checkout running....******"
				git branch: 'dev', credentialsId: 'git-cred', url: 'https://github.com/mokadir/cafeapp.git'
			}
		}
/*		This app do not need compilation. its already compiled and ready to run. so no need to build it again. */
		
		
		stage ('Docker Build & Tag'){
			steps {
				script {
                    			echo "****** Docker Build and Tag Image running....******"
					withDockerRegistry(credentialsId: 'docer-cred') {
						sh "docker build -t mskr7/mkadir-cafeapp:3 ."
					}
				}
			}
		}

 		stage ('Docker Image Scan'){
			steps {
                echo "****** Docker Image Scan by Trivy running....******"
				sh "trivy image --scanners vuln --format table -o trivyscandocr.html mskr7/mkadir-cafeapp:3"
			}
		} 
		
		stage ('Docker Push'){
			steps {
				script {
                    			echo "****** Docker Push Image running....******"
					withDockerRegistry(credentialsId: 'docer-cred') {
						sh "docker push mskr7/mkadir-cafeapp:3"
					}
				}
			}
		}
		
 		stage('Smoke Test') {
			steps { 
				echo "****** Smoke Test Image running....******"
				sh "docker run -d --name smokerun -p 8080:8080 mskr7/mkadir-cafeapp:3"
				sh "sleep 90"
				sh "docker rm --force smokerun"
			}
		} 
		
		stage('Trigger Deployment'){
			steps { 
			   script {
                    			echo "****** Deployment running.... ******"
					echo "Next: Trigger CD Pipeline ......" 
				}		
			}
		}
    	}		
}
