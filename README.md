# Project1

35.154.125.50:8080/gameoflife

**Created Jenkins Pipeline and deployed application on Tomcat server.**

Technology - AWS (EC2,S3), GitHub, Maven, Jenkins, Tomcat

Steps 
Create AWS EC2 instance.
Set-up Java, Tomcat, Maven and Jenkins on AWS instance.


*********Pipeline**************


pipeline {
agent any
environment {
PATH="/project/build/apache-maven-3.8.6/bin:$PATH"
}
stages {
stage ("clone") {                    /// cloning of source code from GitHub
steps {
dir ("/project/code") {
sh "rm -rf /project/code/*"
sh "git clone https://github.com/tanrana97/game-of-life.git"
}
}
}
stage ("build") {                 /// test and build source code with the help of Maven build tool
steps {
dir ("/project/code/game-of-life") {
sh "mvn clean package"
}
}
}
stage ("copy_s3") {             /// copy war package to S3 bucket
steps {
sh 'aws s3 cp /project/code/game-of-life/gameoflife-web/target/gameoflife.war s3://pros3'
}
}
stage ("deploy") {                /// deploy war package on tomcat server
steps {
dir ('/project/s3'){
sh 'aws s3 cp s3://pros3/gameoflife.war /project/s3'
sh 'cp gameoflife.war /project/server/apache-tomcat-9.0.68/webapps'
}
}
}
}
}

