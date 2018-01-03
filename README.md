# Setting up for the Tutorial

The following steps will get you up and running with the tutorial. For the sake of consistency, we have attempted to automate as many of the steps as possible to avoid running into issues during the setup phase, but as much as possible we try to do so in a transparent fashion so users know what is happening under the hood. We also try to give you more than one way to perform the setup using the commandline via the [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest), the [Azure portal](https://portal.azure.com/), and/or [AI Quickstarts](https://quickstart.azure.ai/). In addition to getting your environment ready for the tutorial, the following outline can also serve as a short tutorial on how to manage Azure resource like a power user by leveraging the Azure CLI.

## Part 0: What are the Prerequisites I need on my Computer and where do I get the Setup Files?

### Prerequisites

 * [Git](https://git-scm.com/downloads)
 * [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) 
 * [Visual Studio Code](https://code.visualstudio.com/)

### Setup Files
The files for setup are available on GitHub and can be cloned or downloaded from [here](https://github.com/DataSnowman/BigDataAI-Readiness)

`git clone https://github.com/DataSnowman/BigDataAI-Readiness.git`


## Part 1: Provisioning a Windows Data Science Virtual Machine

We will be using the [Windows Data Science Virtual Machine (DSVM)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.windows-data-science-vm) on Azure. Please follow these steps to get the DSVM ready for the lab. There are three ways to provision a DSVM: through the [Azure portal UI](https://portal.azure.com/), programatically using the [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) and an [ARM template](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates), or through [AI Quickstarts](https://quickstart.azure.ai/). 

### 1.1 Provisioning a Windows DSVM using the Azure portal (15 minutes)

1. Open this location and follow the instructions [shown here](https://docs.microsoft.com/en-us/azure/machine-learning/data-science-virtual-machine/provision-vm).
  a) Select the Data Science Virtual Machine - Windows 2016;
  b) Use `aiuser` and `BigDataAI2018!` as the admin user name and password.  Feel free to use an user name and password of your own.  These are only suggestions and are the default in the parameters.json files we will use in the Azure CLI in section 1.3
  c) Select SDD Drives;
  d) Select D4S_v3 as the size (only available in [certain locations](https://azure.microsoft.com/en-us/blog/introducing-the-new-dv3-and-ev3-vm-sizes)).

If we do not wish to learn how to provision a DSVM using the Azure CLI instead of the Azure portal, we can now skip to [Preparing the environment](#preparing-the-environment).

### 1.2 **(Optinal)** Installing the Linux `bash` command prompt on Windows (15 minutes)

The Azure CLI is a command line utility for provisioning and managing Azure resources. It works on both Windows and Linux. Windows users can use it from the `cmd` command prompt, or alternatively, we can install the `bash` command prompt on Windows.

1. From the **Start Menu**, search for **Turn Windows features on or off**, then scroll down and make sure **Windows subsystem for Linux** is activated. If it is not, activate it and restart the computer.
2. Type `cmd` from the **Start Menu** and right-click on **Command Prompt** to launch it as administrator.
3. In the command prompt, run `bash`. If `bash` is already installed on Windows then we will be presented with the `bash` command prompt. Otherwise, it will get installed for us.

### 1.3 Provisioning a Windows DSVM using the Azure CLI (30 minutes)

1. Download and install the [Azure CLI Installer (MSI)](https://aka.ms/InstallAzureCliWindows). Once the installation is complete open the command prompt and run `az login`, then copy the access code returned to us. In a browser, open a **private tab** and enter the URL `aka.ms/devicelogin`. When prompted, paste in the access code from above. You will be prompted to authenticate using our Azure account.  Go through the appropriate multifaction authenication.
2. Log into the Azure Portal and click on the Create a resource green + sign in the left panel and search for and select **Data Science Virtual Machine - Windows 2016**. On the resources' page, click on the link in the buttom that says <u>Want to deploy programmatically? Get started</u>
Scroll down and make sure that the status is enabled for the intended subscription.
2. From **Windows Explorer** navigate to the course folder and from there launch the command prompt by going to the address bar and typing `cmd` (for the Windows command prompt) or `bash` (for the Linux command prompt assuming it is installed already) and type `az --version` to check the installation. 

3. When you logged in to the CLI in step 1 above you will see a json list of all the Azure account you have access to. Run `az account show` to see you current active account.  Run `az account list -o table` if you want to see all of you Azure account in a table. If you would like to switch to another Azure account run `az account set --subscription <your SubscriptionId>` to set the active subcription.  Run `az group create -n aitutorial -l eastus2` to create a resource group called `aitutorial`. Change directory to `cd arm/wdsvm`. Next run the following command to provision the DSVM:
```
az group deployment create -g aitutorial --template-file template-wdsvm.json --parameters @parameters-wdsvm.json
```
Once the provisioning is finished, we can run `az resource list -g aitutorial -o table` to check what resources were launched. Our listed resources includes a DSVM called `bdaireadywdsvm`, which we provided in the `parameters.json` file.  
FYI, you can start/stop/restart your VM using the corresponding commands below:
```
az vm start -g aitutorial -n bdaireadywdsvm
az vm stop -g aitutorial -n bdaireadywdsvm
az vm restart -g aitutorial -n bdaireadywdsvm
```
Note: You can also start/stop/restart your VM in the Azure portal UI.

### 1.3 Provisioning a Windows DSVM using the Azure AI Quickstart (15 minutes)
To provision a Windows Data Science VM from Azure AI Quickstarts click [here](https://quickstart.azure.ai/Deployments/new/datasciencevm?source=CiqsGallery)
Enter a Deployment name, choose a Subscription and Location and click Create. Next you will be asked to provide configuration parameters.  Enter a Username, Password, and Name, and select a VM Size and click Next.

## Part 2: Preparing the DSVM to run Azure ML Workbench

### 2.1 Installing Docker and Workbench and creating an ML account (30 minutes)

We now have a DSVM provisioned and almost ready to use. In this part, we log into the DSVM and install the necessary pre-requsites in order to start using Azrue ML Workbench (or Workbench for short). In order to log into the DSVM, we type `rdp` from the **Start Menu** and click on **Remote Desktop Connection**. We then enter the DSVM's Public IP address or DNS name, which we can find by logging into the Azure portal and clicking on the DSVM under our resource group `aitutorial`.
You can also click on Connect on the VMs Overview page to download the Remote Desktop Connection .rdp file.

1. On the DSVM, open the browser and navigate to [https://aka.ms/azureml-wb-msi](https://aka.ms/azureml-wb-msi), run the downloader to install Workbench by double clicking on the AmlWorkbenchSetup.msi in the Downloads directory. This will take between 10 and 20 minutes.
2. Sign in to the Azure portal and [Create your Azure Machine Learning accounts](https://docs.microsoft.com/en-us/azure/machine-learning/preview/quickstart-installation#create-azure-machine-learning-accounts) by following the directions in the link.

### 2.2 Preparing the Workbench to run a project (10 minutes)

For the remainder of this tutorial, you will be working exclusively on the DSVM. So log into the DSVM and then run through the steps outlined here.

1. Launch the Azure Machine Learning Workbench. When prompted to authenticate using an Azure account, please do so.
2. Click on initials at the bottom-left corner of the Workbench and make sure that we are using the correct account (namely the Experimentation account and matching Model Management account we created in section 2.1.2).
3. Go to **File > Configure Project IDE** and name the IDE `Code` with the following path `C:\Program Files\Microsoft VS Code\Code.exe`. Click OK to configure IDE.  This will allow us to open the entire project in Visual Studio Code, which is our editor of choice for this Tutorial.
4. Go to **File > New Project** to creat a new project.  Enter a Project name like `FirstProject`, and Project directory like `C:\Users\aiuser\Documents\workbench`, and search Project Templates for `MMLSpark` to bring bring up the `MMLSpark on Adult Census` template. Select the template and click on Create.
5. Go to **File > Open Project (VSCode)** to open the project in Visual Studio Code. It is not necessary to use Code to make edit our course files but it is much more convenient. We will return to Code when we need to make changes to the existing scripts.
6. We now log into the Azure CLI using our Azure account. Return to the Workbench and go to **File > Open Command Prompt**. Check that the Azure CLI is installed on the DSVM by typing `az -h`. Now type `az login` and copy the access code. In Firefox open a **private tab** using **CTRL+SHIFT+P** then enter the URL `aka.ms/devicelogin` and when prompted, paste in the access code. Next, authenticate using an Azure account.
7. We now set the Azure CLI to use the right Azure account. From the command prompt, enter `az account list –o table` to see available accounts. Then copy the subscription ID from the Azure account used to create an AML Workbench account and type `az account set –s <subscription_id>`, replacing `<subscription_id>` with the account ID. You can enter `az account show` to see if the account is set correctly.
