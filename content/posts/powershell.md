+++
title = "Practical PowerShell"
date = "2023-08-24T20:04:56+02:00"
author = "Greg Loyse"
authorTwitter = "" #do not include @
cover = ""
tags = ["PowerShell", "Programming Languages", "Comparison", "Scripting", "Automation"]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++

# intro

- built on `.net`, written in `C#`, [open source](https://github.com/PowerShell/PowerShell), easy `C#` interop.
- object-oriented pipelines (as opposed to text based `bash`)
- `Cmdlets` are commands written in `C#`, follow naming `Verb-Noun` naming (eg. `Get-Process`).
- limited `bash` experience, with commands aliased to `cmdlet` equivalents. eg. `ls` -> `Get-ChildItem`.
- strong integration with `azure`

# using cmdlets

pre-compiled `C#` objects.

```powershell
Get-Command                            # array of cmdlets
Get-Command -ParameterType Process

Get-Help Get-Command                   # cmdlet docs
Get-Verb                               # verbs are standard

# filter
Get-Command -Verb 'Get'                # filters
Get-Command -Noun 'Job'                # parrallelism

# explore objects
Get-Process | Get-Member       
$cmds = Get-Command                    # assign array to variable
$cmds.<TAB>                            # see methods of array
$cmds[0].<TAB>                         # see methods of element

```

functions Vs cmdlets: use functions to script, cmdlets for complex logic.

# `.net` foundation

PowerShell 7 runs on .NET Core. It's packaged with its own minimal version of .NET Core that isn't 
exposed to the rest of the system or usable by other applications.

```bash
dotnet --version
zsh: command not found: dotnet
```



# bash aliases

```powershell
Get-Command ls
Get-Command -CommandType Alias | Where-Object { -not $_.Source }
```

```
ls → Get-ChildItem
cd → Set-Location
pwd → Get-Location
rm → Remove-Item
mv → Move-Item
cp → Copy-Item
cat → Get-Content
echo → Write-Output or Write-Host
ps → Get-Process
kill → Stop-Process
man → Get-Help
mkdir → New-Item -Type Directory
rmdir → Remove-Item
find → Get-ChildItem
grep → Select-String
```

# types

```powershell

# strings
$bob = 'bob'
"$($bob) is great"     # interpolation
"$bob is great"        # also works
```


# slice and dice with pipes `|`

Using names evocative of SQL pipes provide a succinct way to select objects.

## file IO 

```powershell
Get-Command | Select-Object -Last 1 -Property Name, Source
ls -l | grep ".md"                                                # bash
Get-ChildItem -File | Where-Object {$_.Extension -eq ".md"}       # powershell
```

## process

```powershell
Get-ChildItem -File | Where-Object {$_.Extension -eq ".md"} | Select-Object -Property UnixMode, Name
Get-Process | Where-Object {$_.ProcessName -like "dock*"}
```

## powershell and bash

`bash` pipes text between commands. Simple and powerful but text parsing can become complex and error-prone.

`powershell` pipes `.NET` objects between commands. Objects carry data and metadata (properties and methods).


# azure


```powershell
Install-Module -Name Az -AllowClobber -Scope CurrentUser             # install module
Connect-AzAccount                                                    # login (`az login`)

# explore
Get-Command | Where-Object { $_.Source -eq "Az.Websites" }

# resources query
Get-AzVM
Get-AzResourceGroup | Sort-Object ResourceGroupName | Format-Table -GroupBy Location
Get-AzResourceGroup | Where-Object {$_.location -eq "francecentral" } |  Sort-Object ResourceGroupName | Format-Table -GroupBy Location
Get-AzResourceGroup | Get-Member

# resources update
Get-AzResourceGroup | ForEach-Object { $_.ToString() }
Get-AzResourceGroup | ForEach-Object { Remove-AzResourceGroup -Name $_.ResourceGroupName -Force -AsJob }
```

https://learn.microsoft.com/en-us/powershell/azure/queries-azureps?view=azps-10.2.0

`-AsJob` runs removals as background jobs


# modules 

Get list of installed modules and the file path.

```powershell
Get-Module -ListAvailable
Get-Module -ListAvailable | Select-Object -Property Path        # see where they are installed
```

```powershell
install-Module dbaTools
```

[PSGallery](https://www.powershellgallery.com/) is the official registry.

# local environment

```bash
# module locations on MacOs
/usr/local/microsoft/powershell/7/Modules/         # Core
~/.local/share/powershell/Modules/                 # install-Module
```

```powershell
# local configuration: ~/.config/powershell/profile.ps1
$profile.CurrentUserAllHosts
cat $profile.CurrentUserAllHosts
```

```powershell
# your history of commands
cat ~/.local/share/powershell/PSReadLine/ConsoleHost_history.txt
```


## inside an installed module

```powershell
# a module can contain `.psd1`, `.psm1` `.dll` files.
ls ~/.local/share/powershell/Modules/Az.DataFactory/1.17.0/
```

.dll         - compiled file that can contain code and data.
.psm1 module - modules' powershell code
.psd1 data   - modules' metatdata (manifest)


## other modules to explore

```powershell
PowerShellGet\Install-Module posh-git -Scope CurrentUser -Force
Import-Module posh-git
Add-PoshGitToProfile -AllHosts
```

```powershell
Install-Module -Name PSReadline

Install-Module -Name Az.Tools.Predictor
Enable-AzPredictor -AllSession
```

## config

```
Get-AzConfig
```

can apply settings that are only effective in a particular scope
```powershell
Update-AzConfig -DisplayBreakingChangeWarning $false -AppliesTo Az.Compute

```

Set default subscription: (avoid `Connect-AzAccount`)

```powershell
Update-AzConfig -DefaultSubscriptionForLogin <Subscription ID or Name>
```


# language reference

## operators

```powershell
+ - * / %                 # arithmetic
-eq -ne -gt -ge -lt -le   # comparison
-and -or -not -xor        # logical
= += -= *= /=             # assignment

-replace                  # str
-match -notmatch          # str regex
-like  -notlike           # str wildcard

-contains -notcontains    # array membership
-in       -notin

|                         # pipeline
||                        # exec next if prev fails
&&                        # exec next if prev succeeds

. ::                      # member access

```
## control flow

### conditional statements

```powershell
0 ? 'yes' : 'no'

if ($condition) {
    # Code to execute if condition is true
} elseif ($anotherCondition) {
    # Code to execute if anotherCondition is true
} else {
    # Code to execute if no conditions are true
}

switch ($value) {
    "Case1" { # Code for Case1 }
    "Case2" { # Code for Case2 }
    default { # Code for no matching case }
}
```

### looping statements

```powershell
for ($i = 0; $i -lt 10; $i++) {
    # Code to execute
}

foreach ($item in $collection) {
    # Code to execute
}

while ($condition) {
    # Code to execute
}

do {
    # Code to execute
} while ($condition)

do {
    # Code to execute
} until ($condition)

```

### exception handling

```powershell
try {
    # Code that might throw an exception
} catch {
    # Code to handle the exception
} finally {
    # Cleanup code that always runs
}

```

### exit and return

```powershell
return $value

# exit a script or the current PowerShell session
exit
```

## collections

All from `C#`!

```powershell
# with special syntax
$myArray      = 1, 2, 3                               # default: fixed size
$anotherArray = @(4, 5, 6)                            # [System.Collections.ArrayList] resizeable
$myHashTable  = @{ Key1 = 'Value1'; Key2 = 'Value2' } # [System.Collections.Hashtable]

# useful explicit C# references
$myDictionary = New-Object 'System.Collections.Generic.Dictionary[String,Int]'
$myDictionary.Add("five",5)

$myTuple      = New-Object 'System.Tuple[String,Int]' ('example', 42)
$tuple        = [System.Tuple]::Create(1, 2, 3)

```

## functions

```powershell
<#
.SYNOPSIS
   A demonstration function that showcases various PowerShell function features.

.DESCRIPTION
   This function takes several parameters, including required and optional, positional, and default values.

.PARAMETER Name
   A required parameter representing the name of a person.

.PARAMETER Age
   An optional parameter representing the age of a person, with a default value of 30.

.PARAMETER City
   An optional parameter representing the city, positioned at the third place in the command line.

.EXAMPLE
   Example-Function -Name "John" -City "New York"
   Outputs: "John is 30 years old and lives in New York."

.INPUTS
   String, Int

.OUTPUTS
   String
#>

function Example-Function {
    [CmdletBinding()]                                           # now advanced function
    param (
        [Parameter(Mandatory=$true)]
        [string]$Name,

        [Parameter(Mandatory=$false)]
        [int]$Age = 30,

        [Parameter(Mandatory=$false, Position=2)]
        [string]$City = "Unknown"
    )

    Write-Host "$Name is $Age years old and lives in $City."
}

```

`param()` to use or not? generally recommended to use.

```powershell
$num2 = 2

function Add-Numbers {
    param (
        [int]$num1 = 0,
        [int]$num2 = 0
    )
    # $num2 is not available here
    return ($num1 + $num2)
}

$result = Add-Numbers -num1 5 -num2 10


function Add-Numbers ($num1, $num2) {
    # $num2 is available here
    return ($num1 + $num2)
}

$result = Add-Numbers 5 10

```

### pipelining functions

```powershell
function Add-Numbers {
    [CmdletBinding()]
    param (
        [Parameter(ValueFromPipeline=$true, Mandatory=$true)]
        [int]$Number
    )
    
    # BEGIN:   Runs once before processing any input items. 
    BEGIN { 
        $sum = 0 
        Write-Host "Starting the addition process."
    }
    
    # PROCESS: Runs for each item in the pipeline.
    PROCESS { 
        $sum += $Number 
        Write-Host "Adding $Number. Current sum is $sum."
    }
    
    # END:     Runs once after all input items have been processed.
    END { 
        Write-Host "Finished adding numbers. Total sum is $sum."
        Write-Output $sum 
    }
}

1..5 | Add-Numbers

# Starting the addition process.
# Adding 1. Current sum is 1.
# Adding 2. Current sum is 3.
# Adding 3. Current sum is 6.
# Adding 4. Current sum is 10.
# Adding 5. Current sum is 15.
# Finished adding numbers. Total sum is 15.
# 15

```

## object oriented programming and inheritance

```powershell
<#
.SYNOPSIS
This script demonstrates object-oriented programming through a Bicycle class and its derived class, ElectricBicycle.
#>

<#
.DESCRIPTION
The Bicycle class serves as a base class for bicycles. It contains properties like brand, type, and gears.
#>
class Bicycle {
    [string]$brand
    [string]$type
    [int]$gears

    Bicycle([string]$brand, [string]$type, [int]$gears) {
        $this.brand = $brand
        $this.type = $type
        $this.gears = $gears
    }

    [void]ShowInfo() {
        Write-Host "Brand: $($this.brand)"
        Write-Host "Type: $($this.type)"
        Write-Host "Gears: $($this.gears)"
    }
}

<#
.DESCRIPTION
The ElectricBicycle class is derived from the Bicycle class and adds an additional property for battery life.
#>
class ElectricBicycle : Bicycle {
    [int]$batteryLife  # in hours

    ElectricBicycle([string]$brand, [string]$type, [int]$gears, [int]$batteryLife) : base($brand, $type, $gears) {
        $this.batteryLife = $batteryLife
    }

    # Overridden method
    [void]ShowInfo() {
        # Call base class method to display common information
        base.ShowInfo()
        Write-Host "Battery Life: $($this.batteryLife) hours"
    }
}

# Create an instance of the ElectricBicycle class
$myElectricBike = [ElectricBicycle]::new('Trek', 'Mountain', 21, 5)

# Call methods
$myElectricBike.ShowInfo()

```

## concurrency

### jobs - processes

```powershell
Get-Command -Noun 'Job'

```

```powershell
$job = Start-Job -ScriptBlock { Get-Process }

Wait-Job -Job $job

# Retrieve and display job results
$results = Receive-Job -Job $job
$results | Select-Object -First 5

Remove-Job -Job $job
```

### runspaces - threads


```powershell
$runspace = [runspacefactory]::CreateRunspace()
$runspace.Open()

$powershell = [powershell]::Create()
$powershell.Runspace = $runspace

$powershell.AddScript({ Get-Process })

$handle = $powershell.BeginInvoke()

$handle.AsyncWaitHandle.WaitOne()
$results = $powershell.EndInvoke($handle)

$powershell.Dispose()
$runspace.Close()
$runspace.Dispose()

```

### ForEach-Object -Parrallel

```powershell
# Process elements in an array in parallel
$numbers = 1..10
$scriptBlock = {
    param($number)
    $number * 2
}
$numbers | ForEach-Object -Parallel $scriptBlock -ArgumentList $_

```

# resources

- [ms learn powershell](https://learn.microsoft.com/en-us/powershell/scripting/how-to-use-docs?view=powershell-7.3)
- [ms learn powershell 101 tutorial](https://learn.microsoft.com/en-us/powershell/scripting/learn/ps101/00-introduction?view=powershell-7.3)
- [ms learn powershell azure](https://learn.microsoft.com/en-us/powershell/azure/?view=azps-10.2.0)

