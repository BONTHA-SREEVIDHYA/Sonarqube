# Sonarqube

In security point of view we have two type of analysis, SAST & DAST 
SAST- Static Application Security Testing -- code has just been written and not deployed yet. Security checks will be on code ( Ex: Sonarqube -- scans the source code)
DAST - Dynamic Application Security Testing -- Application is deployed & checks will be on that Ex: OWAS ZAP (penetration testing -- checks the week points on the application deployed)

Sonarqube available in 3 versions: communnity version ( free , scans only main branch, limited no of lines scan )
                                    Developer Version ( Licensed , scan any branch code, some lakhs lines of code ) 
                                    Enterprise Version ( Bigger project, huge src code )
Sonarqube does -- code quality check & code coverage

There could be bugs, vulnerabilities ( weak points in src code), code smell( a section of code where there might be an issue in future), duplication ( senses duplicate code). If such issues are less -- code quality is good, if mode-- code quality is less

Code Coverage: No. of lines code covered by test cases 
Also gives us where such issues are there and suggestion to fix. Gives severity of source code 
Quality Gate: We can define some treshold here based on which code can be moved to next stage ( accept / decline and send the code back to dev ) 
Sonarqube uses quality profile ( each programming lang has set of rules based on which sonarqube decides quality ) 

take 2 ec2 machines one for jenkins , one for sonarqube 

Sonar Machine 
Install java, docker 
Install sonarqube as a docker container : sudo docker run -d -p 9000:9000 sonarqube:lts-community 
Access sonar at <public ip>:9000
default access: admin, admin

Jenkins Machine:
Install Java, jenkins 
Access jenkins @ <public ip>: 8080

Jenkins plugins -- install sonarscanner plugin 
tools -- jdk 17
sonarqube scanner -- 6.x
maven -- maven3

Sonarqube scanner is a tool ( installed via jenkins plugin, tool ) used to generate reports -- these reports are then published on sonar server ( configured @ 9000 plugin ). So we need to establish connection between them 
On Sonar server -- administrator -- generate a token -- store in jenkins credentials as secret text 

Jenkins -- system ( configure the sonar server details ) 
<img width="998" height="649" alt="image" src="https://github.com/user-attachments/assets/59c0864e-397b-49bc-a52a-26428a822c2e" />


Create a token which can be used by jenkins to authorize to your sonar-scanner ( sonarqube -- admin -- users -- token )

<img width="1773" height="510" alt="image" src="https://github.com/user-attachments/assets/5a037fbe-e681-462b-a05c-565342b0df1a" />

Also create a webhook in sonarqube to store results
<img width="1695" height="458" alt="image" src="https://github.com/user-attachments/assets/729aa0c7-b4b3-462e-ba17-c1220b59a6c8" />


<img width="1919" height="637" alt="image" src="https://github.com/user-attachments/assets/2ff0ae1f-f411-469d-9415-068053937a85" />
<img width="1900" height="962" alt="image" src="https://github.com/user-attachments/assets/96004f09-a5ff-43e7-a397-17d0a9f293dd" />



Create the Jenkins Pipeline 
To configure Sonar-scanner tool as it is not installed directly by jenkins plugins you can't configure in tools section, you need to create environment for it 

Create a custom quality gate as per requirements 

```groovy
pipeline {
    agent any
    tools {
        maven 'maven3'
    }
    environment{
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('Git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/BONTHA-SREEVIDHYA/FullStack-Blogging-App.git'
            }
        }
        stage('compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Git test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Sonar Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=bloggingApp -Dsonar.projectKey=bloggingApp \
                    -Dsonar.java.binaries=target'''
    
                }
            }
        }
        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline:true
                }
            }
        }
    }
}

```

<img width="1872" height="1018" alt="image" src="https://github.com/user-attachments/assets/b44b1232-8205-4298-8dfb-6d250b1b8228" />

for a nodejs Application 
``` groovy
pipeline {
    agent any
    tools {
        maven 'maven3'
    }
    environment{
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('Git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/BONTHA-SREEVIDHYA/NodeJS-Sample-Application.git'
            }
        }
        stage('install') {
            steps {
                nodejs('nodejs20') { 
                    sh 'npm install'
                }
            }
        }
        stage(' test') {
            steps {
                nodejs('nodejs20') { 
                    sh 'npm run test'
                }
            }
        }
         stage('SonarQube Analysis') { 
            steps { 
                withSonarQubeEnv('sonar') { 
                        sh ' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=NodeJSTest Dsonar.projectKey=NodeJSTest -Dsonar.sources=. -Dsonar.tests=. -Dsonar.test.inclusions=**/*.test.js Dsonar.javascript.lcov.reportPaths=coverage/lcov.info '
    
                }
            }
        }
    }
}
```




