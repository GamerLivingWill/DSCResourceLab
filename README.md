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

# Lab Scenario

  You are an administrator for a company that is implementing System Center Configuration Manager.  On the distribution point servers, a file called NO_SMS_ON_DRIVE.sms must exist on drives that you do not wish to have SCCM packages installed on.  You must create a DSC resource that will create the files on all of the drives, except the ones you specify not to.

## Lab A - Download the xDSCResourceDesigner Module
  - Open up your Visual Studio Code instance.  
  - Run the following:
    ```powershell
    
      Find-Module xDSCResourceDesigner | Install-Module
    
    ```
  You may be prompted to install a NuGet package provider and install the package.  Select Yes to both.
  
  - Run the following:
    ```powershell
  
      Import-Module xDSCResourceDesigner
      Get-Command -Module xDSCResourceDesigner
  
    ```
  You should see the following commands:
    

      PS C:\Users\willa\Documents\GitHub\AltairAutomation> Get-Command -Module xDSCResourceDesigner

      CommandType     Name                                               Version    Source
      -----------     ----                                               -------    ------
      Function        Import-xDscSchema                                  1.9.0.0    xDSCResourceDesigner
      Function        New-xDscResource                                   1.9.0.0    xDSCResourceDesigner
      Function        New-xDscResourceProperty                           1.9.0.0    xDSCResourceDesigner
      Function        Test-xDscResource                                  1.9.0.0    xDSCResourceDesigner
      Function        Test-xDscSchema                                    1.9.0.0    xDSCResourceDesigner
      Function        Update-xDscResource                                1.9.0.0    xDSCResourceDesigner
  
## Lab B - Write a command to create a NO_SMS_ON_DRIVE.sms file.

- Write the following command to understand what is needed to create the NO_SMS_ON_DRIVE.sms file:
  ```powershell
    
    New-Item -Name 'NO_SMS_ON_DRIVE.sms' -Path C:\ -ItemType File  
  
  ```
- Execute the Command.
- Verify the return

```
  PS C:\Users\willa\Documents\GitHub\AltairAutomation> New-Item -Name 'NO_SMS_ON_DRIVE.sms' -Path C:\ -ItemType File


  Directory: C:\


  Mode                LastWriteTime         Length Name
  ----                -------------         ------ ----
  -a----        4/19/2018  11:14 AM              0 NO_SMS_ON_DRIVE.sms
```

