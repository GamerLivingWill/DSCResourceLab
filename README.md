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
    

      PS C:\Users\willa\Documents\GitHub\> Get-Command -Module xDSCResourceDesigner

      CommandType     Name                                               Version    Source
      -----------     ----                                               -------    ------
      Function        Import-xDscSchema                                  1.9.0.0    xDSCResourceDesigner
      Function        New-xDscResource                                   1.9.0.0    xDSCResourceDesigner
      Function        New-xDscResourceProperty                           1.9.0.0    xDSCResourceDesigner
      Function        Test-xDscResource                                  1.9.0.0    xDSCResourceDesigner
      Function        Test-xDscSchema                                    1.9.0.0    xDSCResourceDesigner
      Function        Update-xDscResource                                1.9.0.0    xDSCResourceDesigner
  
## Lab B - Write a command to create a NO_SMS_ON_DRIVE.sms file and validate it exists.

- Write the following command to understand what is needed to create the NO_SMS_ON_DRIVE.sms file:
  ```powershell
    
    New-Item -Name 'NO_SMS_ON_DRIVE.sms' -Path C:\ -ItemType File  
  
  ```
- Execute the Command.
- Verify the return:

  ```
    PS C:\Users\willa\Documents\GitHub\> New-Item -Name 'NO_SMS_ON_DRIVE.sms' -Path C:\ -ItemType File


    Directory: C:\


    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----        4/19/2018  11:14 AM              0 NO_SMS_ON_DRIVE.sms
  ```

- Generalize the command by creating a variable called $Disk.  Replace the value 'C:\' with $Disk.
  ```powershell
    
    $Disk = 'C:\'
    New-Item -Name 'NO_SMS_ON_DRIVE.sms' -Path $Disk -ItemType File  
  
  ```
- Execute the command.  Note the error returned.

```
  PS C:\Users\willa\Documents\GitHub\> $Disk = 'c:\'
  PS C:\Users\willa\Documents\GitHub\> New-Item -Name 'NO_SMS_ON_DRIVE.sms' -Path $Disk -ItemType File
  New-Item : The file 'C:\NO_SMS_ON_DRIVE.sms' already exists.
  At line:1 char:1
  + New-Item -Name 'NO_SMS_ON_DRIVE.sms' -Path $Disk -ItemType File
  + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : WriteError: (C:\NO_SMS_ON_DRIVE.sms:String) [New-Item], IOException
      + FullyQualifiedErrorId : NewItemIOError,Microsoft.PowerShell.Commands.NewItemCommand
```

***The purpose of this exercise is to check against expected behaviours and see if errors are thrown.  If the given command throws an exception, it can cause your DSC resource to fail.  You can take care of these errors with alternate commands as checks, or a Try/Catch statement.

We will use our parameter and the file name to validate if the file exists or not with the Get-Item command and return a true or false statement.  In this instance, since we are building our test, we will use an if statement to validate that the file exists.

***Using an if statement
```powershell

  If (Get-Item -Path ($Drive + 'NO_SMS_ON_DRIVE.sms')){

    return $true

  }
  Else{

    return $false

  }

```

If you run this statement when the file exists, it will return true.  If you delete the file and run the statement, it will return false.

Now we have a command to create the file, and we have script block to test that the file exists.  We will now create the DSC resource.

## Lab C - Create a DSC Resource with xDSCResourceDesigner.

DSC Resource Designer was created to make it easy for you to build the file templates and directory structure in the manner that DSC needs to be able to read it.  In order to create the 



