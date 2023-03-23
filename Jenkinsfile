pipeline 
{
  agent 
  {
        label 'docker-node'
  }
  environment 
  {
        DOCKERHUB_CREDENTIALS=credentials('docker-hub')
  }
  stages 
  {
    stage('Downloading the project from Gitea') 
    {
      steps 
      {
        git branch: 'main', url: 'http://192.168.99.102:3000/baiasen/M5_Homework'
      }
    }
    stage('Build the images, create a common network and run the containers')
    {
      steps
      {
        sh 'docker compose -f docker-compose-first.yaml up -d'
      }
    }
    stage('Testing the application')
    {
      steps
      { 
           script 
           {
                sleep 10
                echo 'Testing the application for reachability'
                sh 'echo $(curl --write-out "%{http_code}" --silent --output /dev/null http://localhost:8080) | grep 200'
                echo 'Testing if Plovdiv is displayed'
                sh 'echo $(curl -s --silent http://localhost:8080) | grep -o "Пловдив"'
           }
       }
    }
    stage('Stopping the application and removing the containers')
    {
      steps
      {
           sh 'docker compose stop'
           sh 'docker compose rm -f'
      }
    }
    stage('Publishing the images to Docker Hub')
    {
      steps
      {
           sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
           sh 'docker image tag m5_homework-web baiasen72/m5-homework-web'
           sh 'docker image tag m5_homework-db baiasen72/m5-homework-db'
           sh 'docker push baiasen72/m5-homework-web'
           sh 'docker push baiasen72/m5-homework-db' 
      }
    }
    stage('Create a common network and run the containers ( web on port 80 )')
    {
      steps
      {
           sh 'docker compose -f docker-compose-second.yaml up -d'
      }
    }
  }           
}
