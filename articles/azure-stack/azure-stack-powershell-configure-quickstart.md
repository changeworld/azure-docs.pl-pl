---
title: Instalowanie i konfigurowanie programu PowerShell dla usługi Azure Stack przewodnika Szybki Start | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat instalowania i konfigurowania programu PowerShell dla usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.openlocfilehash: 5d988e8a8a32924b8424a07cf20c75f0e8f8cf4d
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391078"
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Szybkie rozpoczynanie pracy przy użyciu programu PowerShell w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Ten przewodnik Szybki Start pomaga zainstalować i skonfigurować środowisko usługi Azure Stack przy użyciu programu PowerShell. Skrypt, który firma Microsoft zapewnia, że w tym artykule jest ograniczone do **operatora infrastruktury Azure Stack** tylko.

Ten artykuł jest skrócona wersja kroków, które są opisane w [Instalowanie programu PowerShell]( azure-stack-powershell-install.md), [Pobierz narzędzia]( azure-stack-powershell-download.md), i [konfigurowania środowiska PowerShell operatora infrastruktury Azure Stack]( azure-stack-powershell-configure-admin.md) artykułów. Za pomocą skryptów, w tym artykule, można skonfigurować programu PowerShell dla usługi Azure Stack środowisk, w których są wdrażane przy użyciu usługi Azure Active Directory lub usługi Active Directory Federation Services (AD FS).  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Konfigurowanie programu PowerShell dla wdrożenia oparte na usłudze Azure Active Directory

<a name="sign-in-to-your-azure-stack-development-kit-or-a-windows-based-external-client-if-you-are-connected-through-vpn-open-an-elevated-powershell-ise-session-and-then-run-the-following-script"></a>Zaloguj się do usługi Azure Stack Development Kit lub klienckich zewnętrznych z systemem Windows po nawiązaniu połączenia za pośrednictwem sieci VPN. Otwórz sesję programu PowerShell ISE z podwyższonym poziomem uprawnień, a następnie uruchom następujący skrypt. 
-  
- Upewnij się zaktualizować **TenantName**, **ArmEndpoint**, i **GraphAudience** zmienne jako niezbędne do konfiguracji środowiska:

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Konfigurowanie programu PowerShell dla usługi AD FS, na podstawie wdrożenia

W przypadku usługi Azure Stack po podłączeniu do Internetu, można użyć poniższego skryptu. Jednak w przypadku usługi Azure Stack bez łączności z Internetem, użyj [odłączony sposób instalowania programu PowerShell](azure-stack-powershell-install.md) i poleceń cmdlet do konfigurowania programu PowerShell pozostanie taka sama, jak pokazano w tym skrypcie. Zaloguj się do usługi Azure Stack Development Kit lub klienckich zewnętrznych z systemem Windows po nawiązaniu połączenia za pośrednictwem sieci VPN. Otwórz sesję programu PowerShell ISE z podwyższonym poziomem uprawnień, a następnie uruchom następujący skrypt. Upewnij się zaktualizować **ArmEndpoint** i **GraphAudience** zmienne jako niezbędne do konfiguracji środowiska:

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>Testowanie łączności

Po skonfigurowaniu programu PowerShell można przetestować konfigurację, tworząc grupę zasobów:

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

> [!note]  
> Aby określić grupę zasobów, należy mieć grupę zasobów w ramach subskrypcji. Aby uzyskać więcej informacji o subskrypcjach, zobacz [omówienie planu, oferty, przydziału i subskrypcji](azure-stack-plan-offer-quota-overview.md)

Po utworzeniu grupy zasobów, **stan aprowizacji** właściwość jest ustawiona na **Powodzenie**.

## <a name="next-steps"></a>Kolejne kroki

* [Instalowanie i Konfigurowanie interfejsu wiersza polecenia](azure-stack-connect-cli.md)

* [Tworzenie szablonów](user/azure-stack-develop-templates.md)
