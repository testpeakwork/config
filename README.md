# config
1. Build images by running this commands (make sure to replace peakwork-test-233614 with your project id):

docker build -t gcr.io/peakwork-test-233614/endpoint-service:v1 -f Dockerfile_endpoint .
docker build -t gcr.io/peakwork-test-233614/poll-service:v1 -f Dockerfile_poll .

2. Push images to gcloud

gcloud docker -- push gcr.io/peakwork-test-233614/poll-prices:v1
gcloud docker -- push gcr.io/peakwork-test-233614/endpoint-prices:v1

3.1 Create secret using json file with credentials that allows application communicate with Google Datastore Service
3.2 Update sa_json fild value in secret.yaml with base64 hash of your credentials json file:
	base64 <cred_file_name>.json
3.3 Insert returned hash as sa_json value in secret.yaml
3.4 Create Secret in Kubernetes cluster
	kubectl create -f secret.yaml

4. Deploy services
	kubectl create -f poll.yaml - deploys poll-prices
	kubectl create -f endpoint.yaml - deploys endpoint-prices

5. When poll service adds items to datastore, create indexes for endpoint:
	gcloud datastore indexes create index.yaml

6. For local testing add env params for
   poll-service:
			GOOGLE_APPLICATION_CREDENTIALS=<path to sa_credentials.json>
        	SYMBOLS=AAPL,FB
            CRON_CURRENT=0 0/1 * * * *
	endpoint-service:
			GOOGLE_APPLICATION_CREDENTIALS=<path to sa_credentials.json>

poll-service will get data every 1 minute.
endpoint-service will be available by link: http://localhost:8080/v1/stocks,
swagger : http://localhost:8080/swagger-ui.html

