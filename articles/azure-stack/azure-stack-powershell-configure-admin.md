---
title: Konfigurowanie środowiska Azure PowerShell stosu | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania środowiska Azure PowerShell stosu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 48a765b4e613e45699099330e4ff5c3ce7512686
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300741"
---
# <a name="configure-the-azure-stack-powershell-environment"></a>Konfigurowanie środowiska Azure PowerShell stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można skonfigurować stosu Azure przy użyciu programu PowerShell do zarządzania zasobami, takich jak tworzenie oferty, planów, przydziały i alerty. Ten temat ułatwia skonfigurowanie środowiska operatora.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom następujące wymagania wstępne, albo z [zestaw deweloperski](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), lub z systemem Windows klienta zewnętrznych w przypadku [połączone za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

 - Zainstaluj [modułów programu Azure PowerShell platformy Azure zgodnego stosu](azure-stack-powershell-install.md).  
 - Pobierz [narzędzia niezbędne do pracy z stosu Azure](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Skonfiguruj środowisko operatora i zaloguj się do stosu Azure

Skonfiguruj środowisko operatora stosu Azure przy użyciu programu PowerShell. Na podstawie typu wdrożenia usługi Azure AD lub AD FS, uruchom jedno z poniższych skryptów: Zastąp wartości ArmEndpoint tenantName usługi Azure AD, GraphAudience punktu końcowego i konfiguracji środowiska.

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Azure Active Directory (Azure AD), na podstawie wdrożenia

````PowerShell  
# Set the Azure Stack Tools location
$AzSTools = "C:\AzureStack-Tools-master"

# Set the Azure Active Directory Tenant Name that is used to deploy Azure Stack
$AadTenantName = "<myDirectoryTenantName>.onmicrosoft.com"

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
# To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is set to https://graph.windows.net/.
# To get this value for Azure Stack integrated systems, contact your service provider.
$GraphAudience = "<Graph Audience endpoint for your environment>"

# Navigate to the Azure Stack Tools folder and import the **Connect** PowerShell module
cd $AzSTools
Set-ExecutionPolicy RemoteSigned
Import-Module .\Connect\AzureStack.Connect.psm1

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience

# Get the Active Directory tenantId that is used to deploy Azure Stack
$TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $AadTenantName `
    -EnvironmentName "AzureStackAdmin"

# After signing in to your environment, Azure Stack cmdlets
# can be easily targeted at your Azure Stack instance.
Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Wdrożenia na podstawie usługi Active Directory Federation Services (AD FS)

````PowerShell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````

## <a name="test-the-connectivity"></a>Testowanie łączności

Teraz, gdy masz wszystkie elementy konfiguracji, umożliwia tworzenie zasobów w stosie Azure przy użyciu programu PowerShell. Można na przykład utworzyć grupę zasobów dla aplikacji i Dodaj maszynę wirtualną. Aby utworzyć grupę zasobów o nazwie "MyResourceGroup", użyj następującego polecenia:

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Kolejne kroki
 - [Tworzenie szablonów dla stosu Azure](user/azure-stack-develop-templates.md)
 - [Wdrażanie szablonów za pomocą programu PowerShell](user/azure-stack-deploy-template-powershell.md)
