---
title: Tworzenie obrazów maszyn wirtualnych Windows za pomocą usługi Packer na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć obrazy maszyn wirtualnych Windows na platformie Azure za pomocą usługi Packer
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/22/2019
ms.author: cynthn
ms.openlocfilehash: 81dbd8082d5a7ab473cc0cbe5fcb6e564fbd750c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65951135"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Jak utworzyć obrazy maszyn wirtualnych Windows na platformie Azure za pomocą usługi Packer
Każda maszyna wirtualna (VM) na platformie Azure jest tworzony z obrazu, który definiuje Windows dystrybucji i wersji systemu operacyjnego. Obrazy mogą obejmować wstępnie zainstalowane aplikacje i konfiguracje. W portalu Azure Marketplace zawiera wiele obrazów pierwszy i innych firm dla typowych systemu operacyjnego i środowiska aplikacji albo można utworzyć własne niestandardowe obrazy dopasowany do Twoich potrzeb. Ten artykuł szczegółowo opisuje sposób użycia narzędzia typu open-source [Packer](https://www.packer.io/) do definiowania i tworzenie niestandardowych obrazów na platformie Azure.

Ostatniego testowania w tym artykule na temat korzystania z 21/2/2019 [modułu Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) wersji 1.3.0 i [Packer](https://www.packer.io/docs/install/index.html) wersji 1.3.4.

> [!NOTE]
> Platforma Azure zapewnia teraz usługę, Kreator obrazów platformy Azure (wersja zapoznawcza), definiowanie i tworzenie własnych niestandardowych obrazów. Kreator obrazów platformy Azure w oparciu Packer, więc nawet za pomocą istniejących skryptów administracyjnej powłoki usługi Packer go. Aby rozpocząć korzystanie z Kreatora obrazów platformy Azure, zobacz [tworzenie maszyny Wirtualnej z systemem Windows przy użyciu Kreatora obrazów platformy Azure](image-builder.md).

## <a name="create-azure-resource-group"></a>Utwórz grupę zasobów platformy Azure
Podczas procesu kompilacji narzędzia Packer tworzy tymczasowy zasobów platformy Azure opiera się źródłowa maszyna wirtualna. Aby przechwycić tego źródła do użycia jako obraz maszyny Wirtualnej, należy zdefiniować grupy zasobów. Dane wyjściowe z procesu kompilacji narzędzia Packer znajduje się w tej grupie zasobów.

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurepowershell
$rgName = "mypackerGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Tworzenie poświadczeń platformy Azure
Narzędzia packer uwierzytelnia się za pomocą platformy Azure przy użyciu nazwy głównej usługi. Jednostka usługi platformy Azure to tożsamość zabezpieczeń używanej z aplikacjami, usługami i narzędzia automatyzacji, takie jak usługi Packer. Ty określasz i zdefiniować uprawnienia dotyczące jakie operacje nazwy głównej usługi można wykonać na platformie Azure.

Tworzenie usługi podmiotu zabezpieczeń za pomocą [New AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) i przypisać uprawnienia dla jednostki usługi do tworzenia i zarządzania zasobami przy użyciu [New AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment). Wartość `-DisplayName` musi być unikatowa; Zastąp własną wartość zgodnie z potrzebami.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerServicePrincipal"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Następnie dane wyjściowe identyfikatora hasła i aplikacji.

```powershell
$plainPassword
$sp.ApplicationId
```


Aby uwierzytelniać się na platformie Azure, trzeba będzie również uzyskać Azure identyfikatorów dzierżawę i subskrypcję z [Get AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription):

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Definiowanie szablonu usługi Packer
Aby skompilować obrazy, Utwórz szablon w formacie JSON. W szablonie i są definiowane Konstruktorzy provisioners wykonujących procesu rzeczywistej kompilacji. Zawiera narzędzia packer [konstruktora dla platformy Azure](https://www.packer.io/docs/builders/azure.html) , pozwala na zdefiniowanie zasobów platformy Azure, takich jak poświadczenia nazwy głównej usługi, utworzony w poprzednim kroku.

Utwórz plik o nazwie *windows.json* i wklej następującą zawartością. Wprowadź własne wartości dla następujących elementów:

| Parametr                           | Gdzie można uzyskać |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Wyświetl identyfikator jednostki usługi przy użyciu `$sp.applicationId` |
| *client_secret*                     | Wyświetl hasło generowane automatycznie za pomocą `$plainPassword` |
| *tenant_id*                         | Dane wyjściowe z `$sub.TenantId` polecenia |
| *subscription_id*                   | Dane wyjściowe z `$sub.SubscriptionId` polecenia |
| *managed_image_resource_group_name* | Nazwa grupy zasobów, utworzonego w pierwszym kroku |
| *managed_image_name*                | Nazwa obrazu dysku zarządzanego, który jest tworzony |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myPackerGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Ten szablon tworzy maszynę Wirtualną z systemem Windows Server 2016, instaluje usługi IIS, a następnie stanowi uogólnienie maszyny Wirtualnej za pomocą programu Sysprep. Instalację usług IIS pokazuje, jak można użyć administracyjnej programu PowerShell do uruchamiania dodatkowych poleceń. Finalnego obrazu usługi Packer będzie obejmować instalowanie wymaganego oprogramowania i konfiguracji.


## <a name="build-packer-image"></a>Tworzenie obrazu usługi Packer
Jeśli nie masz jeszcze usługi Packer zainstalowane na komputerze lokalnym [postępuj zgodnie z instrukcjami instalacji narzędzia Packer](https://www.packer.io/docs/install/index.html).

Tworzenie obrazu, otwierając wiersz polecenia i określanie usługi Packer pliku szablonu w następujący sposób:

```
./packer build windows.json
```

Przykład danych wyjściowych z poprzedniego polecenia jest następująca:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Trwa kilka minut, zanim Packer tworzenie maszyny Wirtualnej, uruchom provisioners i wyczyścić wdrożenia.


## <a name="create-a-vm-from-the-packer-image"></a>Tworzenie maszyny Wirtualnej na podstawie obrazu usługi Packer
Teraz można utworzyć Maszynę wirtualną z obrazu za pomocą [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Pomocnicze zasoby sieciowe zostaną utworzone, jeśli jeszcze nie istnieje. Po wyświetleniu monitu wprowadź administracyjne nazwę użytkownika i hasło, które ma zostać utworzony na maszynie Wirtualnej. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* z *myPackerImage*:

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Jeśli chcesz utworzyć maszyny wirtualne w regionie niż obrazu usługi Packer lub innej grupy zasobów, należy określić identyfikator obrazu, a nie nazwę obrazu. Można uzyskać identyfikator obrazu z [Get AzImage](https://docs.microsoft.com/powershell/module/az.compute/Get-AzImage).

Trwa kilka minut, aby utworzyć maszynę Wirtualną z obrazu usługi Packer.


## <a name="test-vm-and-webserver"></a>Test maszyny Wirtualnej, a serwer sieci Web
Uzyskaj publiczny adres IP maszyny wirtualnej za pomocą polecenia [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). W poniższym przykładzie uzyskano utworzony wcześniej adres IP *myPublicIP*:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Do maszyny Wirtualnej, która obejmuje instalację usług IIS z administracyjnej Packer, w akcji, zobacz Wprowadź publiczny adres IP w przeglądarce internetowej.

![Domyślna witryna usług IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Kolejne kroki
Można również użyć istniejących skryptów administracyjnej narzędzia Packer za pomocą [kreatora obrazów platformy Azure](image-builder.md).
