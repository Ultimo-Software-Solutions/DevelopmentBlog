# Introduction

We have setup the required infrastructure to run Ultimo using powershell. This worked very well for the initial setup, but changes to the platform are often done manually. Because of this, it is harder to have a proper process that allows reviewing and gatekeeping any changes to the platform. To improve on this, we wanted a definition that describes the infrastructure and an automated process to apply the changes. We looked into various techniques like 'Pulumi', 'Terraform', 'ARM' and 'Bicep'. In this exercise we will be using bicep to define the infrastructure.

# Setup Bicep development environment

Install bicep through Azure CLI.
```
> az bicep install
> az bicep upgrade
```

Add bicep install directory to path. It is installed in 'C:\Users\[user]\.azure\bin'.

I recommend using VS Code for editing bicep files. Optionally install the bicep VS Code extensions. This extension is pretty good, it has syntax highlighting, shows basic errors and has intellisense.

# Export ARM template and convert

All resources on Azure are internally descibed in ARM templates. ARM is the internal infrastructure definition used to describe the desired state. Any modification made using either the portal or using script will result in a modification to these ARM templates. If you logon into the resource explorer, you can view the current state (https://resources.azure.com/).  
![ResourceExplorer](ResourceExplorer.png)

It is possible to exort any existing resource from azure to an ARM template.
```
> az group export --name "[ResourceGroupName]" > [ResourceGroupName].json
```

In my case, I ended up with a directory containing templates for all resources groups.  
![ArmResources](ArmResources.png)

 This can be then converted into a bicep templates. It will take the input file and generates a file with a .bicep extensions.
```
> az bicep decompile --file [ResourceGroupName].json
```

# Modify generated bicep template

The decompiled bicep file is not ready to be used yet. It is a good starting point, but it will contain a lot of noise that is not needed. It also generates variables that you probably want to change or at least rename.  
![CleanUpBicep](CleanUpBicep.png)

My approach was to start by converting the bicep files into generic modules that can be reused. Then make a main.bicep with a parameter file so it can be used to deploy multiple instances.  
![FinalStructure](FinalStructure.png)

The scope of main.bicep is 'subscription', this allows deploying multiple resource groups. The resource groups are created in main.bicep and used as scope for the modules.  
![MainBicepFragment](MainBicepFragment.png)

I am not going to describe all the details on how to create clean bicep files. A very good starting point is the Microsoft learning path 'Deploy and manage resources in Azure by using Bicep' (https://docs.microsoft.com/en-us/learn/paths/bicep-deploy/).

# Dry run bicep to predict changes

It is possible to predict changes that will be made if you deploy the template. To do this, use the what-if option.
```
> az deployment sub what-if --location $location --template-file $bicepFile --parameters $parameterFile
```

The output is shown like this:  
![Legend](AzDeploymentWhatIfLegend.png)
![WhatIf](AzDeploymentWhatIf.png)

This whould have been very cool, if the output was reliable. Unfortunately, this is a best effort prediction. Many changes are accurate, but some are ignored. I did notice that changes to kubernetes are currently ignored. In the output this is visible, it shows the kubernetes cluster in gray to mark that it is ignored. For what it is worth, it is at least a good test run to find any errors. For example if the account you are using doesn't have sufficient rights to deploy all resources.  
![Ignored](AzDeploymentWhatIfIgnored.png)

# Deploy bicep to azure

If you are confident enough, now it is time to deploy.
```
> az deployment sub create --location $location --template-file $bicepFile --parameters $parameterFile
```

# References

Microsoft learning path 'Deploy and manage resources in Azure by using Bicep':  
https://docs.microsoft.com/en-us/learn/paths/bicep-deploy/

Find bicep templates examples for all resource types:  
https://docs.microsoft.com/en-us/azure/templates/microsoft.network/dnszones/a?tabs=bicep

Browse Azure resource definitions:  
https://resources.azure.com/