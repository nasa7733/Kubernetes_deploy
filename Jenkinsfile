pipeline {
 agent any 

	tools {
	maven  "Maven.3.6.3"
	}
	stages {
		stage ('Checkout') {
			steps {
			git branch : 'master' , url: 'https://github.com/nasa7733/kuberenetes_deploy.git'

			}
		}

		stage ('Execute Maven') {
			steps {
	    
			sh 'mvn package'   }
	}
		stage ('Building docker image') {
			steps {
			sh 'docker build -t webapp:dev01 .'
			sh  'docker images'

			}
		}

		stage ('Pushing Image to GCR') {
			steps {
			sh 'docker tag webapp:dev01 gcr.io/midevops/webapp:dev01'
			sh 'docker push gcr.io/midevops/webapp:dev01'
			}
		}
		stage ('Create GKE Cluster') {
			steps {
			sh """ gcloud  container --project "midevops" clusters create "gcp-grp-cluster01" --zone "us-central1-c" \
				--no-enable-basic-auth --cluster-version "1.20.10-gke.1600" --release-channel "None" \
				--machine-type "g1-small" --image-type "COS_CONTAINERD" --disk-type "pd-standard"  \
				--disk-size "100" --metadata disable-legacy-endpoints=true \
				--max-pods-per-node "10" --num-nodes "1" --enable-ip-alias \
				--network "projects/midevops/global/networks/default" \
				--subnetwork "projects/midevops/regions/us-central1/subnetworks/default" \
				--no-enable-intra-node-visibility --default-max-pods-per-node "110" \
				--no-enable-master-authorized-networks --addons HorizontalPodAutoscaling,HttpLoadBalancing \
				--no-enable-autoupgrade --no-enable-autorepair --max-surge-upgrade 1 \
				--max-unavailable-upgrade 0 --enable-shielded-nodes --no-shielded-integrity-monitoring --tags "http" --node-locations "us-central1-c" """
	    	}
		} 	
		stage ('Check GKE Connection') {
			steps {
			sh 'gcloud container clusters get-credentials gcp-grp-cluster01  --zone "us-central1-c" '
			sh 'sleep 120'
			}
		}
		stage ('Create a Deployment') {
			steps {
			sh 'kubectl apply -f ./deploy.yaml'
			sh 'sleep 120'
			}
		}
		
		
		stage ("Get Pod's status") {
			steps {
			sh 'kubectl get pods'
			}
		}
	 
		stage ('Exposing the App') {
			steps {
			sh 'kubectl expose deployment webapp-deployment --name=web-app-service --type=LoadBalancer --port 80 --target-port 8081'
			sh 'sleep 120'
			}
		}
		stage ('Get service details'){
			steps {
			sh 'kubectl get service'
			}
		}
		
		 stage ('TEST the Application'){
			steps {
			sh """
		    #!/bin/bash
			cluster_ip=\$(kubectl describe svc/web-app-service | grep "LoadBalancer Ingress:" | awk '{print \$3}')
			echo "this is cluster Ip \$cluster_ip "
			curl http://\$cluster_ip/health
			"""
			}
		}
		
		stage ('Delete the GKE Cluster ENV'){
			steps {
			sh 'gcloud container clusters delete gcp-grp-cluster01  --zone "us-central1-c" --quiet'
			sh 'sleep 10'
			}
		}
	}	
}
