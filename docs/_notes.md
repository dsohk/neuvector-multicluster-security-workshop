

Business Challenges:



We are going to launch a new ecommerce site on kubernetes. 

But the security team requires security done properly to protect the data and the system from malicious attack.

PCI-DSS standard compliance;

They disallow deployment into default namespace;

Want to visualize the east-west network traffic across the pods within the cluster.

Ensure consistent security policy across the kubernetes landscape in the organization is important.



----



You should have been provided a Rancher-managed infrastructure with 2 identical RKE2 clusters.



Great ideas:

* deploy apps into multiple clusters using fleet
  * Ecommerce app
  * CRD from NV export
  * If we bring into the fleet, we need everyone to prepare their own github account.
* * 
* Demonstrating the Admission Controller Policy (for fed)
  * Allow/deny deploy into default namespace

* Show Neuvector compliance check (PCI-DSS) after ecommerce app is deployed.

* Assets / registry scanning

  * Name: susesamples
  * Repo: https://registry.hub.docker.com/
  * Filter: susesamples/spring-petclinic-demo:v1.0.7*

  



Lab 1 - Setup NV

Task 1 - Install NeuVector into both cluster1 and cluster2.

Task 2 - Verification

* Check if NV is working... and update login timout to 300s.

* Goto settings > Configuration > change cluster name to cluster1/2 and then submit.

Task 3 - Deploy eCommerce app (microservice based) into both cluster1 and cluster2

- add rancher rodeo repo
- deploy eCommerce app into both cluster1 and cluster2



Lab 2 - Understand learning behavior of NV

Task 1 - Observe the behavior of the App from NV perspective in cluster1

* It's monitoring process, network, filesystem every pod is running.

* Demonstrate how the app behaves under discover, monitor and protect mode.

(Highlight learning behavior of NV and its benefits)

Task 3 - Export the policy under protect mode in cluster1

Task 4 - Import the policy into cluster 2

Task 5 - Validate your security posture.



-- break --



Lab 3 - Using federation model to manage multi-clusters by NeuVector

Task 1 - Setup NeuVector federation mode between cluster1 and cluster2

Task 1.1 - Identify the federation ports and IP address



Goto "Service Discovery" > Services 

Switch to namespace as cattle-neuvector-system

locate the 

cluster1(master)

Node external IP: 4.224.45.13

neuvector-svc-controller-fed-managed service nodeport number: 31416

neuvector-svc-controller-fed-master 31409

cluster2 (managed)

node external IP: 4.224.44.165

token: eyJzIjoiNC4yMjQuNDUuMTMiLCJwIjozMTQwOSwidCI6InMwNmtLaFpMSHdPZFV2WnlZejZtV0VCeE8yaUNYcVBUZnQ0V1VGckdvK0FYcjBXbHFDb2FiTnU4ZFNtZi9PVT0ifQ==

neuvector-svc-controller-fed-managed  31116

neuvector-svc-controller-fed-master 30412



Task 1.2 - Configuring NV for multi-cluster federation

cluster1 => master

cluster2 => managed (join the cluster)

Task 2 - Create admission policy in cluster1 (master)

1.  to deny deployment into default namepsace.
2. to verify federated admission policy is appearing in cluster2 (managed)
3. Navigate to Policy > Admission Control (applies to both clusters)
   1. Observe the federated rule appearing
   2. Status change to Enabled
   3. Switch to Protect mode



Task 3 - Verify deploy spring-petclinic app using fleet

with both cluster1 and 2 denying deploying of app into default namespace. Let's try with fleet continuous delivery to deploy a sample app into the default namespace of both cluster1 and 2.

deploy spring-petclinic using fleet:

* Repo: https://github.com/dsohk/spring-petclinic-helmchart
* Branch name: main
* Target cluster: All clusters
* (deployed to default namespace)













