# Lab 3 - Manage multi-cluster security with NeuVector federation mode 



## Task 1 - Setup NeuVector federation mode between cluster1 and cluster2



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



### Task 1.2 - Configuring NV for multi-cluster federation

cluster1 => master

cluster2 => managed (join the cluster)



## Task 2 - Create admission policy in cluster1 (master)

1.  to deny deployment into default namepsace.
2. to verify federated admission policy is appearing in cluster2 (managed)
3. Navigate to Policy > Admission Control (applies to both clusters)
   1. Observe the federated rule appearing
   2. Status change to Enabled
   3. Switch to Protect mode



## Task 3 - Verify deploy spring-petclinic app using fleet

with both cluster1 and 2 denying deploying of app into default namespace. Let's try with fleet continuous delivery to deploy a sample app into the default namespace of both cluster1 and 2.

deploy spring-petclinic using fleet:

* Repo: https://github.com/dsohk/spring-petclinic-helmchart
* Branch name: main
* Target cluster: All clusters
* (deployed to default namespace)

