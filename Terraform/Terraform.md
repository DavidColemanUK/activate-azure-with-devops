
# Activate Azure with DevOps
## Module: Automating infrastructure deployment in the Cloud with Terraform and Azure Pipelines
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

For more information, see **Use of Microsoft Copyrighted Content** at [http://www.microsoft.com/about/legal/permissions/](http://www.microsoft.com/about/legal/permissions/)

Microsoft®, Internet Explorer®, and Windows® are either registered trademarks or trademarks of Microsoft Corporation in the United States and/or other countries. Other Microsoft products mentioned herein may be either registered trademarks or trademarks of Microsoft Corporation in the United States and/or other countries. All other trademarks are property of their respective owners.

</br>

## Contents
[**Overview**](#Overview)   
[Exercise 1: Examine the Terraform file in your Source code](#exercise-1-examine-the-terraform-file-in-your-source-code)  
[Exercise 2: Build your application using Azure CI Pipeline](#exercise-2-build-your-application-using-azure-ci-pipeline)  
[Exercise 3: Deploy resources using Terraform in Azure CD pipeline](#exercise-3-deploy-resources-using-terraform-in-azure-cd-pipeline)    

## Overview

[Terraform](https://www.terraform.io/intro/index.html) is a tool for building, changing and versioning infrastructure safely and efficiently. Terraform can manage existing and popular cloud service providers as well as custom in-house solutions.

Configuration files describe to **Terraform** the components needed to run a single application or your entire datacenter. Terraform generates an execution plan describing what it will do to reach the desired state, and then executes it to build the described infrastructure. As the configuration changes, Terraform is able to determine what changed and create incremental execution plans which can be applied. 

<div class="bg-slap"><img src="./images/mslearn.png" class="img-icon-cloud" alt="MS teams" style="width: 48px; height: 48px;">Want additional learning? Check out the <a href="https://docs.microsoft.com/en-us/learn/modules/provision-infrastructure-azure-pipelines/" target="_blank"><b><u> Provision infrastructure
in Azure Pipelines</u></b></a> module on Microsoft Learn.</div>

### What’s covered in this lab

In this lab, you will see

1. How open source tools, such as Terraform can be leveraged to implement Infrastructure as Code (**IaC**)
1. How to automate your infrastructure deployments in the Cloud with Terraform and Azure Pipelines

### Before you begin

1. Use the [Azure DevOps Demo Generator](https://azuredevopsdemogenerator.azurewebsites.net/?TemplateId=77382&Name=Terraform) to provision the project on your Azure DevOps organization.
   This URL will automatically select **Terraform** template in the demo generator. If you want to try other projects, use this URL instead -[azuredevops generator](https://azuredevopsdemogenerator.azurewebsites.net/)

   Follow the [simple walkthrough](https://docs.microsoft.com/en-us/azure/devops/demo-gen/use-vsts-demo-generator-v2?view=vsts) to know how to use the Azure DevOps Demo Generator.

## Exercise 1: Examine the Terraform file in your Source code
In this lab, you will use PartsUnlimited which is an example eCommerce website developed using .Net Core. You will examine the terraform file which helps you to provision the Azure Resources required to deploy PartsUnlimited website.

1. Navigate to the project you created above using [Azure DevOps Demo Generator](https://azuredevopsdemogenerator.azurewebsites.net/?TemplateId=77382&Name=Terraform)

1. Select **Repos**. Switch to **terraform** branch.

    ![](images/select-terraform-branch.png)

    Make sure that you are now on the **terraform** branch and **Terraform** folder is there in the repo.

    ![](images/terraformrepo.png)

1. Select the **webapp.tf** file under the Terraform folder. Go through the code.
     
      ![](images/terraformfile.png)

    **webapp.tf** is a terraform configuration file. Terraform uses its own file format, called HCL (Hashicorp Configuration Language). This is very similar to YAML.

    In this example, we want to deploy an Azure Resource group, App service plan and App service required to deploy the website. And we have added Terraform file (Infrastructure as Code) to source control repository in your Azure DevOps project which can deploy the required Azure resources. 
    
    Learn more about the Terraform workflow [here](https://azurecitadel.com/automation/terraform/lab1/).
  

## Exercise 2: Build your application using Azure CI Pipeline
  In this exercise, you will build your application and publish the required files to an artifact called drop.

  1. Navigate to **Pipelines --> Pipelines**. Select **Terraform-CI** and click **Edit**.

      ![](images/editbuild.png)

  1. Your build pipeline will look like as below. This CI pipeline has tasks to compile .Net Core project. The `dotnet` tasks in the pipeline will restore dependencies, build, test and publish the build output into a zip file (package)  which can be deployed to a web application.
    
      ![](images/ci-pipeline.png)

     For more guidance on how to build .Net Core projects with Azure Pipelines see [here](https://docs.microsoft.com/en-us/azure/devops/pipelines/languages/dotnet-core?view=vsts&tabs=designer#build-your-project).

 1. In addition to the application build, we need to publish terraform files to build artifacts so that it will be available in CD pipeline. So we have added **Copy files** task to copy Terraform file to Artifacts directory.

     ![](images/copyfiles.png)

1. Now click **Queue** to trigger the build. Once the build succeeds, verify that the artifacts have **Terraform** folder and **PartsUnlimitedwebsite.zip** file in the drop.

      ![](images/queuebuild2.gif)

## Exercise 3: Deploy resources using Terraform in Azure CD pipeline

In this exercise, you will create azure resources using Terraform as part of your deployment(CD) pipeline and deploy the PartsUnlimited application to the App service provisioned by Terraform.

1. Navigate to **Pipelines --> Releases**. Select **Terraform-CD** and click **Edit**.
   
    ![](images/editrelease.png)

1. Select **Dev** stage and click **View stage tasks** to view the pipeline tasks.

      ![](images/viewstagetasks.png)

1. You will see the tasks as below.

      ![](images/releasetasks.png)

1. Select the **Azure CLI** task. Select the Azure subscription from the drop-down list and click **Authorize** to configure Azure service connection.
      
    ![](images/azureclitask.png)

   > By default, Terraform stores state locally in a file named terraform.tfstate. When working with Terraform in a team, use of a local file makes Terraform usage complicated. With remote state, Terraform writes the state data to a remote data store. Here we are using Azure CLI task to create **Azure storage account** and **storage container** to store Terraform state. For more information on Terraform remote state click [here](https://www.terraform.io/docs/state/remote.html)

1. Select the **Azure PowerShell** task. Select Azure service connection from the drop-down.
     
     ![](images/azurepowershelltask.png)

   > To configure the Terraform [backend](https://www.terraform.io/docs/backends/) we need Storage account access key. Here we are using Azure PowerShell task to get the Access key of the storage account provisioned in the previous step.

1. Select the **Replace tokens** task.
    
     ![](images/replacetokens.png)

    If you observe the **webapp.tf** file in **Exercise 1, Step 3** you will see there are few values are suffixed and prefixed with **__**. For example **__terraformstorageaccount__**. Using **Replace tokens** task we will replace those values with the variable values defined in the release pipeline.
     
      ![](images/variables.png)

1. Terraform tool installer task is used to install  a specified version of Terraform from the Internet or the tools cache and prepends it to the PATH of the Azure Pipelines Agent (hosted or private).
      
    ![](images/installterraform.png)
1. When running Terraform in automation, the focus is usually on the core plan/apply cycle.

   The main Terraform workflow is shown below:

      ![](images/terraformworkflow.png)

      
    i. Initialize the Terraform working directory.

   ii. Produce a plan for changing resources to match the current configuration.
   
   iii. Apply the changes described by the plan.
    
    The next Terraform tasks in your release pipeline help you to implement this workflow.
1. Select the **Terraform init** task. Select Azure service connection from the drop-down. And make sure to enter the container name  as **terraform**. For the other task parameters information see [here](https://github.com/microsoft/azure-pipelines-extensions/blob/master/Extensions/Terraform/Src/Tasks/TerraformTaskV1/README.md)
       
      ![](images/terraform-init2.png)

      ![](images/terraform-init3.png)

    > This task runs `terraform init` command. The `terraform init` command looks through all of the *.tf files in the current working directory and automatically downloads any of the providers required for them. In this example, it will download [Azure provider](https://www.terraform.io/docs/providers/azurerm/) as we are going to deploy Azure resources. For more information about `terraform init` command click [here](https://www.terraform.io/docs/commands/init.html)

1. Select the **Terraform plan** task. Select Azure service connection from the drop-down.
       
      ![](images/terraform-plan.png)
 
    > The `terraform plan` command is used to create an execution plan. Terraform determines what actions are necessary to achieve the desired state specified in the configuration files. This is a dry run and shows which actions will be made.  For more information about `terraform plan` command click [here](https://www.terraform.io/docs/commands/plan.html)

1. Select the **Terraform Apply** task. Select Azure service connection from the drop-down.
  
    ![](images/terraform-approve.png)

    > This task will run the `terraform apply` command to deploy the resources. By default, it will also prompt for confirmation that you want to apply those changes. Since we are automating the deployment we are adding `auto-approve` argument to not prompt for confirmation.

1. Select **Azure App Service Deploy** task. Select Azure service connection from the drop-down.

   ![](images/appservicetask.png)

   > This task will deploy the PartsUnlimited package to Azure app service which is provisioned by Terraform tasks in previous steps.

1. Once you are done **Save** the changes and **Create a release**.
   
   ![](images/releasetrigger.gif)

1. Once the release is success navigate to your Azure portal. Search for **pulterraformweb** in App services. Select **pulterraformweb-xxxx** and browse to view the application deployed.

   ![](images/browseappinportal.png)

   ![](images/webapp.png)

   Do you want to learn more about Terraform? If yes click [here](https://www.terraform.io/) for Terraform documentation.


## Summary

In this lab, you have learned how to automate repeatable deployments with Terraform on Azure using Azure Pipelines.
