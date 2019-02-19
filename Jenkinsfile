pipeline{
    agent none
    stages{
        stage('Running arangodb'){
            steps{
                echo "stage1"
                script {
			    docker.image('arangodb').withRun("--add-host=www.arangodb.com:127.0.0.1 -e ARANGO_NO_AUTH=1 -p 8530:8529") { container ->
			        /* Wait for ArangoDB to be up and ready */
				    timeout(time: 120, unit: 'SECONDS') {
				        /* To continue execution when command fails: command || true
				            In this case curl command has typically an exit code of 52 (Nothing was returned from the server) */
				        sh '''
				        while [ "$code" != "200" ]
				        do
				            code="$(curl -o /dev/null -s -w "%{http_code}" http://localhost:8530/_db/_system/_admin/aardvark/index.html)" || true
				            sleep 1
				        done'''
				    }
			        
			      }
			    }
			   
            }
        }
        
      stage('Running mysql'){
          steps{
              script{
              echo "running my sql"
               docker.image('mysql:5').withRun('-e "MYSQL_ROOT_PASSWORD=my-secret-pw" -p 3307:3306') { c ->
                      /* Wait until mysql service is up */
                     sh 'while ! mysqladmin ping -h0.0.0.0 --silent; do sleep 1; done'
                     /* Run some tests which require MySQL */
                    // sh 'make check'
                }
              }
          }
      }
      
        stage('Running Rabbitmq'){
          steps{
              script{
              echo "running rabbitmq"
              // docker.image('rabbitmq:3.7.8-alpine').withRun('-p 5672:5672') { c ->
              //  -d --hostname my-rabbit --name some-rabbit -p 8080:15672 rabbitmq:3-management
                docker.image('rabbitmq:3-management').withRun('-p 8088:15672') { c ->
                  timeout(time: 120, unit: 'SECONDS') {
				        /* To continue execution when command fails: command || true
				            In this case curl command has typically an exit code of 52 (Nothing was returned from the server) */
				        sh '''
				        while [ "$code" != "200" ]
				        do
				            code="$(curl -o /dev/null -s -w "%{http_code}" http://localhost:8088)" || true
				            sleep 1
				        done'''
				    }
                }
              }
          }
      }
      
      
    }
}
