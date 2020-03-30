---
title: Szybki start usługi Azure Cloud Shell — program PowerShell
description: Dowiedz się, jak korzystać z programu PowerShell w przeglądarce za pomocą usługi Azure Cloud Shell.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: 72261989b7cee9d2251eb18b36431ec807b0e874
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273009"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Szybki start dla programu PowerShell w usłudze Azure Cloud Shell

W tym dokumencie opisano, jak używać programu PowerShell w usłudze Cloud Shell w [witrynie Azure portal.](https://portal.azure.com/)

> [!NOTE]
> Bash [w usłudze Azure Cloud Shell](quickstart.md) Szybki start jest również dostępny.

## <a name="start-cloud-shell"></a>Uruchamianie usługi Cloud Shell

1. Kliknij przycisk **Cloud Shell** na górnym pasku nawigacyjnym witryny Azure portal

   ![](media/quickstart-powershell/shell-icon.png)

2. Wybierz środowisko programu PowerShell z listy rozwijanej, a będziesz na dysku platformy Azure`(Azure:)`

   ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Uruchamianie poleceń programu PowerShell

Uruchom regularne polecenia programu PowerShell w usłudze Cloud Shell, takie jak:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Poruszanie się po zasobach platformy Azure

 1. Wyświetl listę wszystkich `Azure` subskrypcji z dysku

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. `cd`do preferowanej subskrypcji

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Wyświetlanie wszystkich zasobów platformy Azure w ramach bieżącej subskrypcji

    Wpisz, `dir` aby wyświetlić wiele widoków zasobów platformy Azure.

    ```azurepowershell-interactive
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

Wpisz `dir` `AllResources` w katalogu, aby wyświetlić zasoby platformy Azure.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Eksplorowanie grup zasobów

 Można przejść do `ResourceGroups` katalogu i wewnątrz określonej grupy zasobów można znaleźć maszyny wirtualne.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271
```

> [!NOTE]
> Można zauważyć, że po raz `dir`drugi po wpisaniu, Cloud Shell jest w stanie wyświetlić elementy znacznie szybciej.
> Jest to spowodowane elementy podrzędne są buforowane w pamięci dla lepszego środowiska użytkownika.
Jednak zawsze można `dir -Force` użyć, aby uzyskać świeże dane.

### <a name="navigate-storage-resources"></a>Nawigacja po zasobach magazynu

Wprowadzając do `StorageAccounts` katalogu, można łatwo poruszać się po wszystkich zasobach magazynu

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

Za pomocą ciągu połączenia można użyć następującego polecenia do zainstalowania udziału usługi Azure Files.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

Aby uzyskać szczegółowe informacje, zobacz [Instalowanie udziału usługi Azure Files i uzyskiwanie dostępu do udziału w systemie Windows][azmount].

Katalogi w udziale usługi Azure Files można również nawigować w następujący sposób:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Interakcja z maszynami wirtualnymi

Wszystkie maszyny wirtualne można znaleźć w `VirtualMachines` ramach bieżącej subskrypcji za pośrednictwem katalogu.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Wywoływanie skryptu programu PowerShell na zdalnych maszynach wirtualnych

 > [!WARNING]
 > Zapoznaj się z [temat rozwiązywania problemów z zdalnym zarządzaniem maszynami wirtualnymi platformy Azure](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Zakładając, że masz maszynę wirtualną, MyVM1, użyjmy `Invoke-AzVMCommand` do wywołania bloku skryptów programu PowerShell na komputerze zdalnym.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  Można również przejść do katalogu VirtualMachines `Invoke-AzVMCommand` pierwszy i uruchomić w następujący sposób.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

  # You will see output similar to the following:

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

#### <a name="interactively-log-on-to-a-remote-vm"></a>Interaktywne logowanie się do zdalnej maszyny Wirtualnej

Można użyć `Enter-AzVM` do interaktywnego logowania do maszyny Wirtualnej uruchomionej na platformie Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

Można również przejść `VirtualMachines` do katalogu najpierw `Enter-AzVM` i uruchomić w następujący sposób

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>Odkryj WebApps

Wprowadzając do `WebApps` katalogu, można łatwo poruszać się po zasobach aplikacji internetowych

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

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

Aby uwierzytelnić się na serwerach lub maszynach wirtualnych przy użyciu funkcji SSH, `authorized_keys` wygeneruj `/home/user/.ssh/authorized_keys`parę kluczy publiczno-prywatnych w aplikacji Cloud Shell i opublikuj klucz publiczny na komputerze zdalnym, na przykład .

> [!NOTE]
> Można tworzyć klucze prywatno-publiczne SSH przy użyciu `ssh-keygen` i publikować `$env:USERPROFILE\.ssh` je w usłudze Cloud Shell.

### <a name="using-ssh"></a>Korzystanie z SSH

Postępuj zgodnie z instrukcjami [tutaj,](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) aby utworzyć nową konfigurację maszyny Wirtualnej przy użyciu poleceń cmdlet programu Azure PowerShell.
Przed wywołaniem, `New-AzVM` aby rozpocząć wdrożenie, dodaj klucz publiczny SSH do konfiguracji maszyny Wirtualnej.
Nowo utworzona maszyna wirtualna będzie zawierać `~\.ssh\authorized_keys` klucz publiczny w lokalizacji, umożliwiając w ten sposób bezaparsjową sesję SSH na maszynie wirtualnej.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Lista dostępnych poleceń

W `Azure` obszarze `Get-AzCommand` dysk wpisz, aby uzyskać polecenia platformy Azure specyficzne dla kontekstu.

Alternatywnie zawsze można `Get-Command *az* -Module Az.*` użyć, aby dowiedzieć się dostępne polecenia platformy Azure.

## <a name="install-custom-modules"></a>Instalowanie modułów niestandardowych

Można uruchomić, `Install-Module` aby zainstalować moduły z [Galerii programu PowerShell][gallery].

## <a name="get-help"></a>Uzyskaj pomoc

Wpisz, `Get-Help` aby uzyskać informacje o programie PowerShell w usłudze Azure Cloud Shell.

```azurepowershell-interactive
Get-Help
```

W przypadku określonego polecenia nadal `Get-Help` można wykonać polecenie cmdlet.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Przechowywanie danych za pomocą plików platformy Azure

Można utworzyć skrypt, `helloworld.ps1`powiedzmy , i `clouddrive` zapisać go do wykorzystania go w sesjach powłoki.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Następnym razem, gdy używasz programu PowerShell w usłudze Cloud Shell, `helloworld.ps1` plik będzie istnieć w `$HOME\clouddrive` katalogu, który instaluje udział usługi Azure Files.

## <a name="use-custom-profile"></a>Korzystanie z profilu niestandardowego

Środowisko programu PowerShell można dostosować, tworząc profile programu PowerShell `profile.ps1` `Microsoft.PowerShell_profile.ps1`- (lub ).
Zapisz go `$profile.CurrentUserAllHosts` w `$profile.CurrentUserAllHosts`obszarze (lub ), tak aby można było załadować w każdym programie PowerShell w sesji Cloud Shell.

Aby dowiedzieć się, jak utworzyć profil, zobacz [Informacje o profilach][profile].

## <a name="use-git"></a>Użyj Git

Aby sklonować repozytorium Git w usłudze Cloud Shell, musisz utworzyć [osobisty token dostępu][githubtoken] i użyć go jako nazwy użytkownika. Po uzyskaniu tokenu sklonuj repozytorium w następujący sposób:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Wyjdź z powłoki:

Wpisz, `exit` aby zakończyć sesję.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
