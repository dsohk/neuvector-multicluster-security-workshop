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

#### Task 2.1 - Observing your application network flow.

We have already done this previously in the task 1, while taking a look at the Network Tab. This give us a high level network communication flow. 

![Cluster1-NeuVector-Network-Filter-pg3](../images/Cluster1-NeuVector-Network-Filter-pg3.png)

Next step is to look at the containers which are part of the Application. 

NeuVector > Policy > Groups > Filter `eshop` and this will filter what NeuVector has learned about the eshop application. 

We will see all the Services within the namespace & the Policy Mode which they are in & other information. 

NeuVector prefix the services with nv.container-name which you can easily notice in the sample image below.

![Cluster1-Policy-Groups-Filter-eshop](../images/Cluster1-Policy-Groups-Filter-eshop.PNG)

Let's pick up one container to observe what NeuVector has learned about it. 

In our example, let's pick the `nv.eshop-demo-online-boutique-frontend.eshop-demo`

Members 

![Cluster1-NeuVector-Policy-Groups-Members](../images/Cluster1-NeuVector-Policy-Groups-Members.PNG)

Process Profiles Rules

NeuVector shows all process which are spin up during container creation. 

![Cluster1-NeuVector-Policy-Groups-Process-Profile-Rules](../images/Cluster1-NeuVector-Policy-Groups-Process-Profile-Rules.PNG)

Network Rules

![Cluster1-NeuVector-Policy-Network-Rules](../images/Cluster1-NeuVector-Policy-Network-Rules.PNG)

If you wish to add DLP & WAF Rules, 

![Cluster1-NeuVector-Policy-Add-DLP-WAF](../images/Cluster1-NeuVector-Policy-Add-DLP-WAF.png)

DLP Rule added

![Cluster1-NeuVector-Policy-DLP](../images/Cluster1-NeuVector-Policy-DLP.PNG)



WAF Rules

![Cluster1-NeuVector-Policy-WAF](../images/Cluster1-NeuVector-Policy-WAF.PNG)



NeuVector learn all these under the Discovery Mode, which is the default mode of NeuVector. 

![Cluster1-NeuVector-Configuration-New Service Mode-Default-Value](../images/Cluster1-NeuVector-Configuration-New Service Mode-Default-Value.png)



Service Group Mode Automation. 

You can decide how long, you would want to run the Discovery Mode & if the no changes are made to the learn application, you can configure NeuVector to automatically move to Monitor or Protect Mode. 

You can click on the i button for more detailed explanation. 

![Cluster1-NeuVector-Configuration-ServiceGroup-Mode-Default-Value](../images/Cluster1-NeuVector-Configuration-ServiceGroup-Mode-Default-Value.png)



Network Service Policy Mode -  `set the network node at the global level`

You can also set Network Service Policy Mode (Disabled by Default),

![Cluster1-NeuVector-Configuration-Network-Service-Policy-Mode-Default-Value](../images/Cluster1-NeuVector-Configuration-Network-Service-Policy-Mode-Default-Value.png)

#### Task 2.2 - Realtime Process Profile Rule (whitelisting)

Earlier we had identified in the discovery mode, what process did the container start with. 

![Cluster1-NeuVector-Policy-Groups-Process-Profile-Rules](../images/Cluster1-NeuVector-Policy-Groups-Process-Profile-Rules.PNG)

NeuVector continuously watch the process inside the container.  
Let's assume, we need to make some configuration changes to the configuration file residing on container's volume. For this we need access to the shell. Let see if NeuVector can identify new shell process when we access the shell in realtime. 

For this we need to go to Rancher 

Rancher > Cluster Management > Cluster1 > Cluster > Workload > Pods > Filter eshop-demo namespace and we should see all containers running in the namespace. 

Let's select the `eshop-demo-online-boutique-frontend-6798496f46-4sxcv` container and click on the 3 vertical dot on right hand side which further provide options. Select `ExecuteShell`  ![Cluster1-Access-to-frontend-conatainer-shell](../images/Cluster1-Access-to-frontend-conatainer-shell.png)

Access to the container shell. 

![Cluster1-Access-to-frontend-conatainer-shell-prompt-success](../images/Cluster1-Access-to-frontend-conatainer-shell-prompt-success.png)

Now let validate if NeuVector was able to identify the new shell process and no surprise, we do see the new process in NeuVector

NeuVector > Policy > Groups > Filter `frontend` > Process Profile Rules. 

![Cluster1-NeuVector-learned-new-process-shell](../images/Cluster1-NeuVector-learned-new-process-shell.png)

Task 2.3 - Realtime Network Rule (whitelisting)

NeuVector > Policy > Groups > Filter `frontend` > Network Rules Rules. 

NeuVector create the whitelisting rule under the Discovery mode. 

![Cluster1-NeuVector-Policy-Network-Rules](../images/Cluster1-NeuVector-Policy-Network-Rules.PNG)

In this way NeuVector can learn our application behaviour. we can continue to test the application & it will continue to learn & create whitelist rules. Once we happy with our testing we can have NeuVector change it's mode from Discovery to Monitor & or Protect 

While you are testing, you can start with Discovery to learn & than process to Monitor Mode. Monitor mode will report any volition based on the whitelisting Process & Network Rules we have create in Discovery mode & report them as Security Event.  

To change our application mode from Discovery to Monitor 

 NeuVector > Policy > Groups > Filter `frontend` > Switch Mode

![Cluster1-Switch-mode-discovery-monitor-pg1](../images/Cluster1-Switch-mode-discovery-monitor-pg1-1669313660415-28.png)

Selection of Mode - `Monitor`

![Cluster1-Switch-mode-discovery-monitor-pg2](../images/Cluster1-Switch-mode-discovery-monitor-pg2.PNG)

Confirmation of Change of Mode to Monitor

![Cluster1-Switch-mode-discovery-monitor-pg3](../images/Cluster1-Switch-mode-discovery-monitor-pg3.PNG)

Going forward any violations in Process/Network will be alerted and we can see them in Notification > Security Events.

If we toggle back to Rancher > Cluster Management > Cluster1 > Cluster > Workload > Pods > Filter eshop-demo namespace and we should see all containers running in the namespace. 

Execute into the shell & try & execute a ping command to any website of your choice, it will result in success, however since we have not kept the application service in Monitor Mode,we expect to see Security Event notifying that ping was never part of whitelisting rule & hence this was a violation and would be reported accordingly.   

![Cluster1-mode-monitor-proces-ping](../images/Cluster1-mode-monitor-proces-ping.png)

Viewing Process Violation

NeuVector > Notification > Security Event - Filter `www.google.com`

![Cluster1-mode-monitor-notification-security-event-violation-ping](../images/Cluster1-mode-monitor-notification-security-event-violation-ping.png)



