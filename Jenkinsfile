pipeline{
    agent{
        label "worker-01-jenkins"
    }
    tools{
        maven 'MAVEN_HOME'
    }
    stages{
        stage("Pull the code from SCM"){
            steps{
                echo "========Pull the code from SCM=========="
                git branch: 'master',
                    url: 'https://github.com/anilsri102/weshopify-api-gateway.git'
                    echo "=====Pull the code from SCM==========="
            }
        }
        stage("Build the Source code"){
            steps{
                echo "================Code building is starting=============="
                sh 'mvn clean package -DskipTests=true'
                echo "================Artifact Generated============="
            }
        }
        stage("Sonar Quality Analysis"){
            steps{
                echo "================Sonar Quality Gate Starting=============="
                sh  'mvn verify sonar:sonar -Dsonar.projectKey=weshopify-api-gateway -Dsonar.projectName=weshopify-api-gateway -Dsonar.host.url=http://3.109.8.134:9000 -Dsonar.token=sqp_4de7271045148f21168c6cf67ed825a92b3c06a2 -DskipTests=true'
                echo "================Sonar Quality Gate Analyzed the Artifact ============="
            }
        }
         stage("Deploy to Artifactory"){
            steps{
                echo "================Deploying to artifacory Started=============="
                sh 'mvn deploy'
                echo "================Artifact Deploy is Completed============="
            }
        }
         stage("Copy the files to ansible server"){
            steps{
                echo "Connecting to Ansible Server"
                sshagent(['ANSIBLE_SERVER']){
                    sh 'scp Dockerfile ansible-admin@172.31.0.106:/opt/weshopify-api-gateway/ci-files'
                    sh 'scp weshopify-api-gateway-playbook.yml ansible-admin@172.31.0.106:/opt/weshopify-api-gateway/ci-files'
                    sh 'scp jfrog.sh ansible-admin@172.31.0.106:/opt/weshopify-api-gateway/ci-files'
                    sh '''
                        ssh -tt ansible-admin@172.31.0.106 << EOF
                            ansible-playbook /opt/weshopify-api-gateway/ci-files/weshopify-api-gateway-playbook.yml
                            exit
                        EOF    

                    '''
                }
            }
        }
    }    

}
