pipeline {
 agent any 


	stages {
		
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
		
		stage ('Delete the GKE Cluster ENV'){
			steps {
       sh 'sleep 600'
			sh 'gcloud container clusters delete gcp-grp-cluster01  --zone "us-central1-c" --quiet'
			sh 'sleep 10'
			}
		}
	}	
}
