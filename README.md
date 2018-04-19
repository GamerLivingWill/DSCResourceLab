# DSCResourceLab
A walkthrough of how to create your own DSC resources.

# Lab Setup
Have Visual Studio Code installed.  It's free!
  - https://code.visualstudio.com/download
Install the PowerShell add-on for VSCode.  Also free!
  - https://github.com/PowerShell/vscode-powershell

# Understanding the Three Constructs

Desired State Configuration resources have three constructs: Get-, Set- and Test-TargetResource.  In order to create a DSC resource, you need to understand what these constructs do.

## Get-TargetResource

Get-TargetResource, like the GetScript block of the Script resource, pulls a hash table of the input parameters.  This is essentially used to validate the input parameters.

## Test-TargetResource

Test-TargetResource validates whether or not your target is in its desired state.  The scripts in this statement must return a SINGLE true or false statement.  If the statement returns true, then the configuration goes to the next item.  If it returns a false, it will invoke the Set-TargetResource.

## Set-TargetResource

Set-TargetResource is the 'make it so' portion of a DSC resource.  If your machine is not in its desired state, this is the code that will fix that.

# Lab - Download the xDSCResourceDesigner Module
  - Open up your Visual Studio Code instance.  
  - Run the following:
    ```powershell
    
      Find-Module xDSCResourceDesigner | Install-Module
    
    ```
  You may be prompted to install a NuGet package provider and install the package.  Select Yes to both.
  
  - Run the following:
  ```powershell
  
    Import-Module xDSCResourceDesigner
    Get-Command xDSCResourceDesigner
  
  ```
  
