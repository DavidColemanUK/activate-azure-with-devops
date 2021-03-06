# Activate Azure with DevOps
## Module: End-to-End DevOps - Lab - Monitoring Application Performance with Application Insights
### Student Lab Manual
**Conditions and Terms of Use**  
**Microsoft Confidential - For Internal Use Only**

This training package is proprietary and confidential and is intended only for uses described in the training materials. Content and software is provided to you under a Non-Disclosure Agreement and cannot be distributed. Copying or disclosing all or any portion of the content and/or software included in such packages is strictly prohibited.

The contents of this package are for informational and training purposes only and are provided "as is" without warranty of any kind, whether express or implied, including but not limited to the implied warranties of merchantability, fitness for a particular purpose, and non-infringement.

Training package content, including URLs and other Internet Web site references, is subject to change without notice. Because Microsoft must respond to changing market conditions, the content should not be interpreted to be a commitment on the part of Microsoft, and Microsoft cannot guarantee the accuracy of any information presented after the date of publication. Unless otherwise noted, the companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place, or event is intended or should be inferred.

**Copyright and Trademarks**

© 2020 Microsoft Corporation. All rights reserved.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

For more information, see **Use of Microsoft Copyrighted Content** at [https://www.microsoft.com/en-us/legal/intellectualproperty/permissions/default](https://www.microsoft.com/en-us/legal/intellectualproperty/permissions/default)

Microsoft®, Internet Explorer®, and Windows® are either registered trademarks or trademarks of Microsoft Corporation in the United States and/or other countries. Other Microsoft products mentioned herein may be either registered trademarks or trademarks of Microsoft Corporation in the United States and/or other countries. All other trademarks are property of their respective owners.

## Contents  
[**Introduction**](#introduction)  
[**Prerequisites**](#prerequisites)  
[**Exercise 1: Monitoring Application Performance with Application Insights**](#exercise-1-monitoring-application-performance-with-application-insights)  
[Task 1: Creating Azure resources](#task-1-creating-azure-resources)  
[Task 2: Deploying a web app to Azure](#task-2-deploying-a-web-app-to-azure)  
[Task 3: Generating and reviewing application traffic](#task-3-generating-and-reviewing-application-traffic)  
[Task 4: Investigating application performance](#task-4-investigating-application-performance)  
[Task 5: Tracking application usage](#task-5-tracking-application-usage)  
[Task 6: Creating application alerts](#task-6-creating-application-alerts)  
  
<a name="Introduction"></a>

## Introduction ##

Application Insights is an extensible Application Performance Management (APM) service for web developers on multiple platforms. You can use it to monitor your live web applications and other services. It automatically detects performance anomalies, includes powerful analytics tools to help you diagnose issues, and helps you continuously improve performance and usability. It works for apps on a wide variety of platforms including .NET, Node.js and Java EE, hosted on-premises, hybrid, or any public cloud. It even integrates with your DevOps process with connection points available in a variety of development tools. It can even monitor and analyze telemetry from mobile apps by integrating with Visual Studio App Center.

In this lab, you'll learn about how you can add Application Insights to an existing web application, as well as how to monitor the application via the Azure portal.

<a name="Prerequisites"></a>
### Prerequisites ###

- This lab requires you to complete the End to End Prerequisite instructions.

**Estimated Time to Complete This Lab**  
60 minutes

<div style="page-break-after: always;"></div>

<a name="Exercise1"></a>
## Exercise 1: Monitoring Application Performance with Application Insights ##

<a name="Ex1Task1"></a>
### Task 1: Creating Azure resources ###

1. This lab requires a deployment of the Parts Unlimited project out to an Azure app service. To do this, you will need to spin up the necessary infrastructure. Log in to your Azure account at [https://portal.azure.com](https://portal.azure.com/).

1.  Click **Create a resource** and search for **"sql"**.

    ![](images/000.png)

1. Select **SQL Database**.

    ![](images/001.png)

1. Click **Create**.

    ![](images/002.png)

1. Under **Resource group**, click **Create new**.

    ![](images/003.png)

1. Enter a **Name** of **"partsunlimited"** and click **OK**.

    ![](images/004.png)

1. Enter a **Database name** of **"partsunlimited"** and click **Create new** to create a new server.

    ![](images/005.png)

1. Enter a unique name for **Server name**, such as by including your name. Enter an admin username and password you can remember. Note that **"P2ssw0rd"** meets the password requirements. Enable **Allow Azure services to access server**. Click **OK** to confirm these options. Be sure to remember the password because we will need it later in this lab.

    ![](images/007.png)

1. The web app in this lab will need to communicate with the server. By default, the server has no network connectivity. Select the **Networking** tab and enable **Public endpoint** and toggle **Allow Azure Services and resources to access this server**.

    ![](images/firewallSetting.png)

1. Click **Review + create**.

    ![](images/008.png)

1. Click **Create**. It'll take some time to complete, but you can move on to the next step while it works in the background.

    ![](images/009.png)

1. Click **Create a resource** and search for **"web"**.

    ![](images/010.png)

1. Select the **Web App** template.

    ![](images/011.png)

1. Click **Create**.

    ![](images/createWebApp.png)

1. Under **Project Details**, select the same **Subscription** and **Resource Group** used for the database.

    ![](images/013.png)

1. For **Name**, enter a unique name such as by including your name or initials. Select the **ASP.NET 4.8 Runtime stack**.

    ![](images/014.png)

1. By default, the template will generate an Application Insights resource and attach it to the app service.

    ![](images/WebAppMonitorAI.png)

1. Click **Review and create**.

    ![](images/015.png)

1. Click **Create**.

    ![](images/016.png)

1. It will take a few minutes for the resources to provision, so you can move on to the next task.

<a name="Ex1Task2"></a>
### Task 2: Deploying a web app to Azure ###

1. Navigate to your project in Azure DevOps.
1. Navigate to **Pipelines | Pipelines**.

    ![](images/navigateToPipelines.png)

1. Click new pipeline and select **Azure Repos Git** as the source.    

    ![](images/selectAzureRepoGit.png)

1. Next, select the **PartsUnlimited** repo.    

    ![](images/selectPUrepo.png)

1. On the next screen, pick the existing file. We will reuse most of the multi-stage pipeline again but for the new application. 

    ![](images/existingPipeline.png)

1. From the dropdown select **/azure-pipelines.yml** as the file path.

    ![](images/selectExistingYAML.png)

1. We need to add a pipeline reference to the new application. Click on **Variables** at the top-right of the screen.

    ![](images/selectPipelineVariables.png)
    
1. Add **Name** as WebsiteName and **Value** to reflect the Web App you created earlier in this lab. Click **Ok** and **Save**.

    ![](images/updateVariable.png)

1. The web app is already live, so there is no need to deploy new infrastructure. Remove the **AzureResourceManagerTemplateDeployment** task. 

    ![](images/ARMdeployTask.png)

1. Click on **Save** at the top-right, then **Run** if trigger: none is defined. 

    ![](images/savePipeline.png)  
 
1. Now that the pipeline is in place, let's focus on the application configurations to link the repo content with the new resources. Navigate to the **Repos** view.

    ![](images/029.png)

1. Use the navigation to open the **PartsUnlimited-aspnet45/src/PartsUnlimitedWebsite/Web.config**.

    ![](images/030.png)

1. This application already has configuration settings for the Application Insights key and for a SQL connection. Make a note of these keys. We will reference these within the App Service configuration.

    ![](images/031.png)

1. Switch to the Azure portal and navigate to the app service created earlier.

    ![](images/034.png)

1. Select the **Configuration** tab.

    ![](images/035.png)

1. Click the **Edit** button for the **APPINSIGHTS_INSTRUMENTATIONKEY**. This is the default setting added during the app service creation and already contains the Application Insights ID. We will need to add a new setting for the one expected by our app, so we can copy the value from here. Note that this is a specific case for our sample app because the source has the Application Insights SDK added. The app doesn't do anything with the SDK at this time, but it does expect the key, so we have to supply it.

    ![](images/036.png)

1. Copy the **Value** and click **Cancel**.

    ![](images/037.png)

1. Click **New application setting**.

    ![](images/038.png)

1. Set the **Name** to **"Keys:ApplicationInsights:InstrumentationKey"** and paste the **Value** we just copied. Click **OK**.

    ![](images/039.png)


1. Next, we will need to add a connection string to allow the web app to connection to the database. In a new tab, open the resource group for this lab.

    ![](images/selectRG.png)

1. Click your SQL database (something like **pul-johndoe/partsunlimited**). Make sure you click the database you created and not the server. 

    ![](images/selectDB.png)

1. In the new blade, click **Show database connection strings**.

    ![](images/showConnectionString.png)

1. This will provide you with a list of connection strings based on platform. Copy the **ADO.NET** string to your clipboard so you can configure your new web site to use it. Close this blade.

    ![](images/copyConnectionString.png)

1. Open a new instance of **Notepad** and paste the connection string into it. This will make it easier to edit and retrieve later on in case anything happens to the clipboard copy.

1. Switch tabs back to the app service settings. Click **New connection string**.

    ![](images/createNewConnectionString.png)

1. Locate the **Connection strings** section and add a new entry with the key **"DefaultConnectionString"** and the value pasted from the clipboard. You'll need to locate the "{your_password}" section and replace it (including braces) with the actual SQL credentials entered earlier. Be sure the **Type** is set to **SQLAzure** and click **OK**.

    ![](images/connectionCredentials.png)

1. Click **Save** to commit.

    ![](images/saveAppSettings.png)

1. Return to the Azure DevOps tab.

1. Navigate to **Pipelines | Pipelines**.

    ![](images/navigateToPipelines.png)

1. If the pipeline has not yet completed, track it through until it does.

    ![](images/pipelineCompletion.png)

1. Return to the Azure portal tab and navigate to the **Overview** tab.

    ![](images/047.png)

1. Click the **URL** to open the site in a new tab.

    ![](images/048.png)

1. The site should load as expected. If it's not ready yet, refresh the site tab every minute or so until it loads.

    ![](images/049.png)

<a name="Ex1Task3"></a>
### Task 3: Generating and reviewing application traffic ###

1. Navigate around the site to produce some traffic.

1. After generating some traffic, click the **Brakes** menu item.

    ![](images/050.png)

1. Manually append a **"1"** to the URL and press **Enter**. This will produce a site error since that category does not exist. Refresh the page a few times to generate more errors.

    ![](images/051.png)

1. Return to the Azure portal browser tab.

1. Select the **Application Insights** tab.

    ![](images/052.png)

1. This tab includes instructions for how you can deeply integrate Application Insights into your apps. While the default experience produces a wealth of data for tracking and monitoring apps, the API provides everything else you need to support specialized scenarios and custom event tracking.

    ![](images/053.png)

1. Click **View Application Insights data**.

    ![](images/054.png)

1. The dashboard view should already have the traffic you generated earlier. If not, refresh the data every minute or so until you see some activity in the charts.

    ![](images/055.png)

<a name="Ex1Task4"></a>
### Task 4: Investigating application performance ###

1. Select the **Application map** tab.

    ![](images/056.png)

1. Application Map helps you spot performance bottlenecks or failure hotspots across all components of your distributed application. Each node on the map represents an application component or its dependencies, as well as health KPI and alerts status. You can click through from any component to more detailed diagnostics, such as Application Insights events. If your app uses Azure services, you can also click through to Azure diagnostics, such as SQL Database Advisor recommendations.

    ![](images/057.png)

1. Select the **Smart Detection** tab. Smart Detection automatically warns you of potential performance problems in your web application. It performs proactive analysis of the telemetry that your app sends to Application Insights. If there is a sudden rise in failure rates, or abnormal patterns in client or server performance, you get an alert. This feature needs no configuration. It operates if your application sends enough telemetry. However, there won't be any data in there yet since our app has just deployed.

    ![](images/058.png)

1. Select the **Live Metrics**.

    ![](images/059.png)

1. Return to the site browser tab and perform some navigation to produce live traffic. Generate some successful traffic with a few errors as well.

1. Return to the Azure portal tab to see the live traffic as it arrives. Live Metrics Stream enables you to probe the beating heart of your live, in-production web application. You can select and filter metrics and performance counters to watch in real time, without any disturbance to your service. You can also inspect stack traces from sample failed requests and exceptions.

    ![](images/060.png)

1. Select the **Transaction Search** tab.

    ![](images/062.png)

1. Search provides a flexible interface to locate the exact telemetry you need to answer questions. Click **See all data in the last 24 hours** to see data from the past 24 hours.

    ![](images/063.png)

1. The results will include all telemetry data, which can be filtered down by multiple properties.

    ![](images/064.png)

1. Click **Grouped results**. These results are generated based on how they share common properties.

    ![](images/065.png)

1. Expand the **Event types** dropdown.

    ![](images/066.png)

1. Deselect everything except **Exception**.

    ![](images/067.png)

1. There should be some exceptions available from the errors generated earlier. Click one of them.

    ![](images/exceptionAI.png)

1. This will provide a full timeline view of the exception within the context of its request. Click **View all telemetry**.

    ![](images/069.png)

1. The **Telemetry** view provides the same data in a flat view.

    ![](images/070.png)

1. You can also review the details of the exception itself, such as its properties and call stack.

    ![](images/071.png)

1. Close the current blade.

    ![](images/072.png)

1. Select the **Availability** tab.

    ![](images/073.png)

1. After you've deployed your web app or web site to any server, you can set up tests to monitor its availability and responsiveness. Application Insights sends web requests to your application at regular intervals from points around the world. It alerts you if your application doesn't respond or responds slowly. Click **Add test**.

    ![](images/074.png)

1. Enter a **Test name** of **"Home page"** and set the **URL** to the root of your site. Click **Create**.

    ![](images/075.png)

1. The test will not run immediately, so there won't be any data.

    ![](images/076.png)

1. If you check back later, you should see the availability data updated to reflect the tests against your live site. Don't wait for this now.

    ![](images/077.png)

1. Select the **Failures** tab.

    ![](images/078.png)

1. The Failures view aggregates all exception reports into a single dashboard. From here you can easily zero in on dependencies, exceptions, and other filters. From the **Top 3 response codes** list, click the link for the **500** errors.

    ![](images/079.png)

1. This will present a list of exceptions from this HTTP response code. Selecting the suggested exception will lead to the exception view covered earlier.

    ![](images/080.png)

1. Close the current blade.

    ![](images/081.png)

1. Select the **Performance** tab.

    ![](images/082.png)

1. The Performance view provides a dashboard that simplifies the details of application performance based on the collected telemetry.

    ![](images/083.png)

1. Select the **Metrics** tab.

    ![](images/084.png)

1. Metrics in Application Insights are measured values and counts of events that are sent in telemetry from your application. They help you detect performance issues and watch trends in how your application is being used. There's a wide range of standard metrics, and you can also create your own custom metrics and events. Set the **Metric** to **Server requests**.

    ![](images/085.png)

1. You can also segment your data using splitting. Click **Apply splitting**.

    ![](images/086.png)

1. Set **Values** to **Operation name**. This will split the server requests by what pages they're requesting, which you can see from the different colors in the chart.

    ![](images/087.png)

<a name="Ex1Task5"></a>
### Task 5: Tracking application usage ###

1. Application Insights provides a broad set of features to track application usage. Select the **Users** tab.

    ![](images/088.png)

1. There aren't many users for our application yet, but we can still learn about them. Click **View More Insights**.

    ![](images/089.png)

1. Scroll down to review details about the geographies, operating systems, and browsers.

    ![](images/090.png)

1. You can also drill into specific users to get a better understanding of their usage.

    ![](images/091.png)

1. Select the **Events** tab.

    ![](images/092.png)

1. Click **View More Insights**.

    ![](images/093.png)

1. There will be a variety of built-in events raised so far for site navigation. You can programmatically add custom events with custom data to meet your needs.

    ![](images/094.png)

1. Select the **Funnels** tab.

    ![](images/095.png)

1. Understanding the customer experience is of the utmost importance to your business. If your application involves multiple stages, you need to know if most customers are progressing through the entire process, or if they are ending the process at some point. The progression through a series of steps in a web application is known as a funnel. You can use Azure Application Insights Funnels to gain insights into your users, and monitor step-by-step conversion rates.

    ![](images/096.png)

1. Select the **User Flows** tab.

    ![](images/097.png)

1. The User Flows tool starts from an initial page view, custom event, or exception that you specify. Given this initial event, User Flows shows the events that happened before and afterwards during user sessions. Lines of varying thickness show how many times each path was followed by users. Special Session Started nodes show where the subsequent nodes began a session. Session Ended nodes show how many users sent no page views or custom events after the preceding node, highlighting where users probably left your site.

    ![](images/098.png)

1. Select the **Retention** tab.

    ![](images/099.png)

1. The retention feature in Application Insights helps you analyze how many users return to your app, and how often they perform particular tasks or achieve goals. For example, if you run a game site, you could compare the numbers of users who return to the site after losing a game with the number who return after winning. This knowledge can help you improve both your user experience and your business strategy.

    ![](images/100.png)

1. Select the **Impact** tab.

    ![](images/101.png)

1. Impact analyzes how load times and other properties influence conversion rates for various parts of your app. To put it more precisely, it discovers how any dimension of a page view, custom event, or request affects the usage of a different page view or custom event.

    ![](images/102.png)

1. Select the **Cohorts** tab.

    ![](images/103.png)

1. A cohort is a set of users, sessions, events, or operations that have something in common. In Application Insights, cohorts are defined by an analytics query. In cases where you have to analyze a specific set of users or events repeatedly, cohorts can give you more flexibility to express exactly the set you're interested in. Cohorts are used in ways similar to filters. But cohort definitions are built from custom analytics queries, so they're much more adaptable and complex. Unlike filters, you can save cohorts so other members of your team can reuse them.

    ![](images/104.png)

1. Select the **More** tab. This view includes a variety of reports and templates for review.

    ![](images/105.png)

1. From the **Usage** category, select **Analysis of Page Views**.

    ![](images/106.png)

1. This particular report offers insight regarding the page views. There are many other reports available by default, and you can customize and save new ones.

    ![](images/107.png)

<a name="Ex1Task6"></a>
### Task 6: Creating application alerts ###

1. Select the **Alerts** tab. Alerts enable you to set triggers that perform actions when Application Insights measurements reach specified conditions.

    ![](images/108.png)

1. Click **New alert rule**.

    ![](images/109.png)

1. The current Application Insights resource will be selected by default. Click **Select conditon** under **Condition**.

    ![](images/110.png)

1. Search for **"failed"**and select the **Failed requests** metric.

    ![](images/111.png)

1. Set the **Threshold value** to **"1"**. This will trigger the alert once a second failed request is reported.

    ![](images/112.png)

1. By default, the conditions will be evaluated every minute and based on the aggregation of measurements over the past 5 minutes. Click **Done**.

    ![](images/113.png)

1. Now that the condition is created, we need to define an **Action Group** for it to execute. Click **Select action group** then **Create action group**.

    ![](images/114.png)

1. Set the **Action group name** and **Display name** to **"Admin alert"**. 

    ![](images/115.png)

1. Select **Notifications** or **Next:Notifications >** to naviagate to the next section. Set the **Notifcation type** to **Email/SMS/Push/Voice** and **Name** to **Alert**. 

    ![](images/116.png)

1. Click the edit pencil next to the alert. Check the **Email** box and enter your email address then click **OK**.

    ![](images/117.png)

1. Click **Review and create** then **Create** to save the action group.


1. Now that the action group has been created, it may still need to be selected If not already. Click **Select action group**.

    ![](images/118.png)

1. Check the newly created action group and click **Select**.

    ![](images/119.png)

1. Set the **Alert rule name** to **"Any failure"** and click **Create alert rule**.

    ![](images/120.png)

1. Once the rule has been created, return to the web site browser tab and invoke some errors using the method from earlier.

1. Around five minutes later, you should receive an email indicating that your alert was triggered.

    ![](images/121.png)

