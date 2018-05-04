---
title: PowerShell w chmurze Azure powłoki (wersja zapoznawcza) — Szybki Start | Dokumentacja firmy Microsoft
description: Szybki Start dla środowiska PowerShell w chmurze powłoki
services: Azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: damaerte
ms.openlocfilehash: e3e59395b7066169b8a7863f45a446051b830a71
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell-preview"></a>Szybki Start dla środowiska PowerShell w powłoce chmury Azure (wersja zapoznawcza)

Ten dokument zawiera szczegóły dotyczące używania programu PowerShell w chmurze powłoki w [portalu Azure](https://aka.ms/PSCloudPreview).

> [!NOTE]
> A [Bash w powłoce chmury Azure](quickstart.md) szybkiego startu jest również dostępna.

## <a name="start-cloud-shell"></a>Uruchom powłokę chmury

1. Polecenie **powłoki chmury** przycisk z górnym pasku nawigacyjnym portalu Azure

  ![](media/quickstart-powershell/shell-icon.png)

2. Wybierz z listy rozwijanej środowiska PowerShell i będzie dysku platformy Azure `(Azure:)`

  ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Uruchom polecenia programu PowerShell

Uruchamianie regularne poleceń programu PowerShell w powłoce chmury, takich jak:

```PowerShell
PS Azure:\> Get-Date
Monday, September 25, 2017 08:55:09 AM

PS Azure:\> Get-AzureRmVM -Status

ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Przejdź do zasobów platformy Azure

 1. Lista subskrypcji

    ``` PowerShell
    PS Azure:\> dir
    ```

 2. `cd` do preferowanego subskrypcji

    ``` PowerShell
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Wyświetlanie wszystkich zasobów na platformie Azure w ramach bieżącej subskrypcji
 
    Typ `dir` na liście wiele widoków zasobów platformy Azure.
 
    ``` PowerShell
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
     ```

### <a name="allresources-view"></a>Widok AllResources 
Typ `dir` w obszarze `AllResources` katalogu, aby wyświetlić zasobów platformy Azure.
    
    PS Azure:\MySubscriptionName> dir AllResources

### <a name="explore-resource-groups"></a>Eksploruj grup zasobów

 Można przejść do `ResourceGroups` katalogu i wewnątrz określonej grupy zasobów można znaleźć maszyny wirtualnej.

``` PowerShell
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271

```
> [!NOTE]
> Należy zauważyć, że podczas wpisywania po raz drugi `dir`, powłoka chmury jest możliwość wyświetlania elementów znacznie szybsze.
> Jest to spowodowane elementy podrzędne są buforowane w pamięci dla wygody użytkowników.
Jednak zawsze można użyć `dir -Force` można pobrać nowe dane.

### <a name="navigate-storage-resources"></a>Przejdź do zasobów magazynu
    
Wprowadzając w `StorageAccounts` katalogu można łatwo przejść zasobów magazynu
    
``` PowerShell 
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files


Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>


```

Przy użyciu parametrów połączenia służy następujące polecenie aby zainstalować udział plików Azure.
        
``` PowerShell
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>


```

Aby uzyskać więcej informacji, zobacz [zainstalować udział plików Azure i uzyskać dostępu do udziału w systemie Windows][azmount].

Można także przechodzić katalogi w ramach udziału plików Azure w następujący sposób:

            
``` PowerShell
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1

    
```

### <a name="interact-with-virtual-machines"></a>Współdziałanie z maszynami wirtualnymi

Można znaleźć sieci maszyn wirtualnych w ramach bieżącej subskrypcji za pośrednictwem `VirtualMachines` katalogu.
    
``` PowerShell
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running


```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Wywołanie skryptu programu PowerShell na maszynach wirtualnych zdalnego

 > [!WARNING]
 > Zapoznaj się z [Rozwiązywanie problemów z zdalne zarządzanie maszynami wirtualnymi Azure](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Zakładając, że maszyny wirtualnej, MyVM1, można użyć `Invoke-AzureRmVMCommand` do wywołania blok skryptu programu PowerShell na komputerze zdalnym.

  ``` Powershell
  Invoke-AzureRmVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```
  Można również przejść do katalogu VirtualMachines najpierw i uruchomić `Invoke-AzureRmVMCommand` w następujący sposób.

  ``` PowerShell
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzureRmVMCommand -Scriptblock{Get-ComputerInfo}
  ```
  Zostaną wyświetlone dane wyjściowe podobne do następującego:

  ``` PowerShell
  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Interakcyjnego logowania się do zdalnego maszyny Wirtualnej

Można użyć `Enter-AzureRmVM` do interaktywnego zalogowania się do maszyny Wirtualnej z systemem w usłudze Azure.

  ``` PowerShell
  Enter-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

Można także przechodzić do `VirtualMachines` katalogu pierwszej i uruchom `Enter-AzureRmVM` w następujący sposób

  ``` PowerShell
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzureRmVM
 ```

### <a name="discover-webapps"></a>Odnajdywanie WebApps

Wprowadzając w `WebApps` katalogu można łatwo przejść zasobów aplikacji sieci web

``` PowerShell
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US



# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzureRmWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

```

## <a name="ssh"></a>Protokół SSH

[Win32 — OpenSSH](https://github.com/PowerShell/Win32-OpenSSH) jest dostępna w powłoce chmury programu PowerShell.
Na potrzeby uwierzytelniania na serwerach lub maszyn wirtualnych przy użyciu protokołu SSH, wygenerowanie pary kluczy publiczno prywatnych w powłoce chmury i opublikować klucza publicznego do `authorized_keys` na zdalnym komputerze, takich jak `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> Można utworzyć przy użyciu kluczy prywatny publiczny SSH `ssh-keygen` i opublikuj je do `$env:USERPROFILE\.ssh` w powłoce chmury.

### <a name="using-a-custom-profile-to-persist-git-and-ssh-settings"></a>Przy użyciu niestandardowego profilu w celu utrzymania ustawień usługi GIT i SSH

Ponieważ sesje nie zostaną utrwalone po wylogowania, Zapisz z `$env:USERPROFILE\.ssh` do katalogu `clouddrive` lub utworzyć łącza symbolicznego, gdy uruchamiana pobiera powłoki chmury.
Dodaj następujący fragment kodu w Twojej profile.ps1 do utworzenia łącza symbolicznego do `clouddrive`.

``` PowerShell
# Check if the .ssh directory exists
if( -not (Test-Path $home\clouddrive\.ssh)){
    mkdir $home\clouddrive\.ssh
}

# .ssh path relative to this script
$script:sshFolderPath = Join-Path $PSScriptRoot .ssh

# Create a symlink to .ssh in user's $home
if(Test-Path $script:sshFolderPath){
   if(-not (Test-Path (Join-Path $HOME .ssh ))){
        New-Item -ItemType SymbolicLink -Path $HOME -Name .ssh -Value $script:sshFolderPath
   }
}

```

### <a name="using-ssh"></a>Przy użyciu protokołu SSH

Postępuj zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) Aby utworzyć nową konfigurację maszyny Wirtualnej za pomocą poleceń cmdlet AzureRM.
Przed wywołaniem do `New-AzureRmVM` na rozpoczęcie wdrażania, Dodaj klucz publiczny SSH do konfiguracji maszyny Wirtualnej.
Nowo utworzona maszyna wirtualna będzie zawierać klucz publiczny w `~\.ssh\authorized_keys` lokalizacji, umożliwiając w ten sposób bez poświadczeń sesji SSH z maszyną wirtualną.

``` PowerShell

# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzureRmVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com

```


## <a name="list-available-commands"></a>Listę dostępnych poleceń.

W obszarze `Azure` dysku, wpisz `Get-AzureRmCommand` uzyskać poleceń Azure specyficznej dla kontekstu.

Alternatywnie możesz za pomocą `Get-Command *azurerm* -Module AzureRM.*` Aby sprawdzić dostępne polecenia platformy Azure.

## <a name="install-custom-modules"></a>Instalowanie niestandardowych modułów

Można uruchomić `Install-Module` zainstalować moduły z [galerii programu PowerShell][gallery].

## <a name="get-help"></a>Get-Help

Typ `Get-Help` Aby uzyskać informacje dotyczące programu PowerShell w powłoce chmury Azure.

``` PowerShell
PS Azure:\> Get-Help
```

Dla danego polecenia, można nadal wykonywać `Get-Help` następuje polecenia cmdlet.

``` PowerShell
PS Azure:\> Get-Help Get-AzureRmVM
```

## <a name="use-azure-files-to-store-your-data"></a>Użyj plików Azure do przechowywania danych

Można utworzyć skrypt, powiedz `helloworld.ps1`i zapisać go do Twojego `clouddrive` go używać w wielu sesjach powłoki.

``` PowerShell
cd C:\users\ContainerAdministrator\clouddrive
PS C:\users\ContainerAdministrator\clouddrive> vim .\helloworld.ps1
# Add the content, such as 'Hello World!'
PS C:\users\ContainerAdministrator\clouddrive> .\helloworld.ps1
Hello World!
```

Następnym razem, kiedy używać środowiska PowerShell w chmurze powłoki, `helloworld.ps1` pliku będzie istnieć w obszarze `clouddrive` katalogu, który instaluje na udział plików Azure.

## <a name="use-custom-profile"></a>Użyj profilu niestandardowego

Można dostosować środowiska PowerShell, tworząc PowerShell profile - `profile.ps1` lub `Microsoft.PowerShell_profile.ps1`. Zapisz go w obszarze `clouddrive` , dzięki czemu mogą być ładowane w każdej sesji programu PowerShell podczas uruchamiania powłoki chmury.

Jak utworzyć profil, zapoznaj się [profile o][profile].

## <a name="use-git"></a>Przy użyciu narzędzia Git

Klonowanie repozytorium Git w powłoce chmury, musisz utworzyć [osobisty token dostępu] [ githubtoken] i używać go jako nazwy użytkownika. Raz masz z tokenu, klonowanie repozytorium w następujący sposób:

 ``` PowerShell
  git clone https://<your-access-token>@github.com/username/repo.git

```
Ponieważ sesje w powłoce chmury nie zostaną utrwalone po wyrejestrowaniu lub limit czasu sesji, nie istnieje w pliku config Git podczas następnego logowania. Aby zachować konfigurację Git, musisz zapisać Twoje .gitconfig do Twojej `clouddrive` i skopiuj go lub utworzyć łącza symbolicznego, gdy uruchamiana pobiera powłoki chmury. Poniższy fragment kodu w Twojej profile.ps1 używać do tworzenia łącza symbolicznego do `clouddrive`.

 ``` PowerShell
 
# .gitconfig path relative to this script
$script:gitconfigPath = Join-Path $PSScriptRoot .gitconfig

# Create a symlink to .gitconfig in user's $home
if(Test-Path $script:gitconfigPath){

    if(-not (Test-Path (Join-Path $home .gitconfig ))){
         New-Item -ItemType SymbolicLink -Path $home -Name .gitconfig -Value $script:gitconfigPath
    }
}

```
## <a name="exit-the-shell"></a>Wyjdź z powłoki:

Typ `exit` zakończenie sesji.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
