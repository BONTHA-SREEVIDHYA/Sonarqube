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


