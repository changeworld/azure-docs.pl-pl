---
title: Program PowerShell w przewodniku Szybki Start usługi Azure Cloud Shell | Dokumentacja firmy Microsoft
description: Przewodnik Szybki Start dla programu PowerShell w usłudze Cloud Shell
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
ms.date: 10/18/2018
ms.author: damaerte
ms.openlocfilehash: 1fc9883e0ea35c384c3bfc83e76b8eded48cbcba
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905073"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Przewodnik Szybki Start dla programu PowerShell w usłudze Azure Cloud Shell

W tym dokumencie opisano, jak za pomocą programu PowerShell w usłudze Cloud Shell w [witryny Azure portal](https://portal.azure.com/).

> [!NOTE]
> A [Bash w usłudze Azure Cloud Shell](quickstart.md) Szybki Start jest również dostępna.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="start-cloud-shell"></a>Uruchom usługę Cloud Shell

1. Kliknij pozycję **Cloud Shell** przycisk na górnym pasku nawigacyjnym witryny Azure Portal

   ![](media/quickstart-powershell/shell-icon.png)

2. Wybierz środowisko programu PowerShell z listy rozwijanej i będzie na dysku platformy Azure `(Azure:)`

   ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Uruchom polecenia programu PowerShell

Uruchom regularne poleceń programu PowerShell w usłudze Cloud Shell, takich jak:

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

## <a name="navigate-azure-resources"></a>Nawigowanie po zasobach platformy Azure

 1. Wyświetl wszystkie swoje subskrypcje z `Azure` dysku

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. `cd` do preferowanego subskrypcji

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Wyświetlanie wszystkich zasobów platformy Azure w ramach bieżącej subskrypcji

    Typ `dir` Aby wyświetlić listę wielu widoków zasobów platformy Azure.

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

Typ `dir` w obszarze `AllResources` katalogu do wyświetlania zasobów platformy Azure.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Zapoznaj się z grupy zasobów

 Możesz przejść do `ResourceGroups` katalogu i w określonej grupie zasobów można znaleźć maszyny wirtualne.

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
> Należy zauważyć, że podczas wpisywania po raz drugi `dir`, usłudze Cloud Shell jest w stanie wyświetlić elementy, które znacznie szybciej.
> Jest to spowodowane elementy podrzędne są buforowane w pamięci dla wygody użytkowników.
Jednak zawsze można użyć `dir -Force` można pobrać aktualne dane.

### <a name="navigate-storage-resources"></a>Nawigowanie po zasobach magazynu

Przystępując do `StorageAccounts` katalogu umożliwia łatwe nawigowanie zasobów magazynu

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

Przy użyciu parametrów połączenia można użyć następującego polecenia, aby zainstalować udział plików platformy Azure.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

Aby uzyskać więcej informacji, zobacz [zainstalować udział plików platformy Azure i uzyskać dostęp do udziału w Windows][azmount].

Możesz także przejść katalogów w udziale plików platformy Azure w następujący sposób:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Współdziałanie z maszynami wirtualnymi

Możesz znaleźć wszystkie swoje maszyny wirtualne w ramach bieżącej subskrypcji za pośrednictwem `VirtualMachines` katalogu.

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
 > Zapoznaj się [Rozwiązywanie problemów z zdalnego zarządzania maszynami wirtualnymi platformy Azure](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Zakładając, że masz Maszynę MyVM1, użyjemy `Invoke-AzVMCommand` do wywołania blok skryptu programu PowerShell na komputerze zdalnym.

  ```azurepowershell-interactive
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```

  Możesz również najpierw przejdź do katalogu, maszyn wirtualnych i uruchomić `Invoke-AzVMCommand` w następujący sposób.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo}

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

#### <a name="interactively-log-on-to-a-remote-vm"></a>Interaktywnie Zaloguj się na zdalnej maszynie Wirtualnej

Możesz użyć `Enter-AzVM` interakcyjnie zalogować się do maszyny Wirtualnej działającej na platformie Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

Możesz także przejść do `VirtualMachines` katalogu pierwszy i uruchom `Enter-AzVM` w następujący sposób

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM
 ```

### <a name="discover-webapps"></a>Odnajdywanie aplikacji internetowych

Przystępując do `WebApps` katalogu umożliwia łatwe nawigowanie zasobów aplikacji sieci web

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

Aby uwierzytelniać się na serwerach lub maszynach wirtualnych przy użyciu protokołu SSH, generowanie pary kluczy publiczny prywatny w usłudze Cloud Shell, a następnie opublikuje klucz publiczny do `authorized_keys` na maszynie zdalnej, takich jak `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> Można utworzyć przy użyciu kluczy prywatny publiczny SSH `ssh-keygen` i opublikuj je `$env:USERPROFILE\.ssh` w usłudze Cloud Shell.

### <a name="using-ssh"></a>Przy użyciu protokołu SSH

Postępuj zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) do utworzenia nowej konfiguracji maszyny Wirtualnej przy użyciu poleceń cmdlet programu Azure PowerShell.
Przed wywołaniem do `New-AzVM` Konferencję wdrożenia, należy dodać klucz publiczny SSH do konfiguracji maszyny Wirtualnej.
Nowo utworzonej maszyny Wirtualnej będzie zawierać klucz publiczny w `~\.ssh\authorized_keys` lokalizacji, umożliwiając w ten sposób bez poświadczeń sesję SSH z maszyną wirtualną.

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

W obszarze `Azure` dysku, wpisz `Get-AzCommand` można pobrać poleceń platformy Azure od kontekstu.

Alternatywnie, zawsze można użyć `Get-Command *az* -Module Az.*` Aby sprawdzić dostępne polecenia platformy Azure.

## <a name="install-custom-modules"></a>Zainstaluj moduły niestandardowe

Możesz uruchomić `Install-Module` Aby zainstalować moduły z [galerii programu PowerShell][gallery].

## <a name="get-help"></a>Get-Help

Typ `Get-Help` Aby uzyskać informacje na temat programu PowerShell w usłudze Azure Cloud Shell.

```azurepowershell-interactive
Get-Help
```

Dla danego polecenia, można nadal wykonywać `Get-Help` następuje polecenia cmdlet.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Użyj usługi Azure Files do przechowywania danych

Można utworzyć skrypt, załóżmy, że `helloworld.ps1`i zapisz go na swoje `clouddrive` z niej korzystać w sesjach powłoki.

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

Następnym razem, gdy używasz programu PowerShell w usłudze Cloud Shell `helloworld.ps1` pliku będzie istnieć w ramach `$HOME\clouddrive` katalogu, który instaluje udziału plików platformy Azure.

## <a name="use-custom-profile"></a>Użycie niestandardowego profilu

Można dostosować środowisko programu PowerShell, tworząc PowerShell profile - `profile.ps1` (lub `Microsoft.PowerShell_profile.ps1`).
Zapisz go w folderze `$profile.CurrentUserAllHosts` (lub `$profile.CurrentUserAllHosts`), dzięki czemu mogą być ładowane w każdym środowisku PowerShell w usłudze Cloud Shell sesji.

Jak utworzyć profil, można znaleźć [o profilach][profile].

## <a name="use-git"></a>Za pomocą narzędzia Git

Aby sklonować repozytorium Git w usłudze Cloud Shell, musisz utworzyć [osobisty token dostępu] [ githubtoken] i używać go jako nazwa użytkownika. Gdy masz klonie tokenu, repozytorium w następujący sposób:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Wyjdź z powłoki:

Typ `exit` zakończenie sesji.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
