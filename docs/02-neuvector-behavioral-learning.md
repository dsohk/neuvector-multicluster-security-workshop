# Lab 2 - Exploring Learning Behaviour of NeuVector

In this task, we are going to explore the learning behaviour of NeuVector to intelligently learn the behaviour of any microservices application, automatically discover all the valid east-west network traffic within the cluster, and generate the security code as Kubernetes CRD resources.



Task 1 - Observe the behaviour of the App from NV perspective in cluster1

* It's monitoring process, network, filesystem every pod is running.

* Demonstrate how the app behaves under discover, monitor and protect mode.

(Highlight learning behaviour of NV and its benefits)

Task 3 - Export the policy under protect mode in cluster1

Task 4 - Import the policy into cluster 2

Task 5 - Validate your security posture.



## Task 1 - Explore Application in NeuVector

In task 1 we have deployed the microservices app `eshop-demo app`

As DevOps team, we are tasked with testing the application. 

Rancher > Cluster Management > Cluster 1 > Explore > Cluster > NeuVector > Click the `Full lifecycle Container Security` link in the NeuVector box to open up a new browser tab accessing to the NeuVector user interface. You should see the interface like below.

### Task 1. 1 - Explore NeuVector. 

#### Dashboard Tab 

Show you a high-level security posture of your cluster with Security Risk Score, Service Connection Risk, Ingress/Egress Exposure, Vulnerability Exploits. It would further guide you on how you can improve your Overall Security Risk Score by following the guidance from NeuVector.

![Cluster1-NeuVector-Homepage](../images/Cluster1-NeuVector-Homepage.PNG)

#### Network Tab 

will show us the communication between the containers and the various services which are communicating with each other. This gives us a good overview of network traffic (East/West)

![Cluster1-NeuVector-Network-page](../images/Cluster1-NeuVector-Network-page.PNG)

We can narrow down your search using the filter available. 

![Cluster1-NeuVector-Network-Filter](../images/Cluster1-NeuVector-Network-Filter-1669305906976-8.png)

With the Filter you get much focused view. 

![Cluster1-NeuVector-Network-Filter-pg3](../images/Cluster1-NeuVector-Network-Filter-pg3.png)



#### Assets Tab 

give you more insight within the cluster, starting with the Kubernetes Management Platform itself, Nodes in the cluster, Containers in the clusters, registries the cluster is connected with if any & finally the System Components of NeuVector itself. 

At high level, you can scan Platform

Similarly if you click on Nodes, it give you details of all the nodes in the cluster & you can scan them for Compliance, Vulnerability. for container running on them etc...

Containers provide you the list of the containers along with their additional details like namespace, Nodes, Application & it's associated ports etc....

Registry page show you details on registry if any.

Finally the System Components are the NeuVector Components (Controllers, Scanners & Enforcers). 

Covering each section in details is beyond this scope of this workshop.

#### Policy Tabs

For this exercise, we will be focusing in Policy Tabs mostly. 

We create admission control rule in `Admission Control` tab. We are covering this in details in exercise 3. 

Group & Network Rules which will provide us with in-depth details about our application, it's container processes, network rule etc. We will cover this indepth in this exercise. 

Response rule is where xxxxx (Need t explain)

DLP Sensors & WAT Sensors are available out of box & they keep looking for anomaly. 

#### Security Risk 

Cover Vulnerability & Compliance. You scan results are display in depth detail here. 

#### Notification 

Security Event & Risk Report. 

All Network & Process violation are captured in Security Event.

All Scan & Admission Control violation are display here. 

#### Setting 

You can configure users & role, configuration option for Syslogs, Webhooks etc are available under Configuration page

You can also configure LDAP/AD, SAML & OpenID Connect which work in conjunction with Rancher as Rancher provides centralized Authentication & Authorization Capabilities which NeuVector leverages. 



### Task 2 - Application Learning using NeuVector. 

NeuVector provider full life-cycle container security.

NeuVector provides layered security & in this task we will be specifically looking at Process & Network Violation. We will discover application behaviour and create a whilelist based policy to detect violations of normal behaviour. 

We can also do Threat Detections against attach such as DDoS, DNS attacks on containers, Use DLP & WAF Sensor to inspect network traffic for Data Loss Prevention of sensitive data & detect OWASP Top 10 WAF attacks - **Subjective to Derek's creating a rogue container to simulate the attach which can send data to external network**





NeuVector > Policy > Groups > Filter `eshop` and this will filter what NeuVector has learned about the eshop application. 

We will see all the Services within the namespace & the Policy Mode which they are in & other information. 

![Cluster1-Policy-Groups-Filter-eshop](../images/Cluster1-Policy-Groups-Filter-eshop.PNG)





