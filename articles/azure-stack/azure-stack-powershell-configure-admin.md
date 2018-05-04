---
title: Konfigurowanie środowiska PowerShell operator stosu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować środowisko PowerShell operator stosu Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 37D9CAC9-538B-4504-B51B-7336158D8A6B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: mabrigg
ms.openlocfilehash: a8ab52de6c57e84bb2c90ce6bcf53ef1b92e30af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>Konfigurowanie środowiska PowerShell Azure stosu — operator

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można skonfigurować stosu Azure przy użyciu programu PowerShell do zarządzania zasobami, takich jak tworzenie oferty, planów, przydziały i alerty. Ten temat ułatwia skonfigurowanie środowiska operatora. Można skonfigurować programu PowerShell dla środowiska użytkownika, zobacz temat [konfigurowania środowiska PowerShell użytkownika stosu Azure](user/azure-stack-powershell-configure-user.md) artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom następujące wymagania wstępne, albo z [zestaw deweloperski](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), lub z systemem Windows klienta zewnętrznych w przypadku [połączone za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

* Zainstaluj [modułów programu Azure PowerShell platformy Azure zgodnego stosu](azure-stack-powershell-install.md).  
* Pobierz [narzędzia niezbędne do pracy z stosu Azure](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Skonfiguruj środowisko operatora i zaloguj się do stosu Azure

Skonfiguruj środowisko operatora stosu Azure przy użyciu programu PowerShell. Na podstawie typu wdrożenia usługi Azure AD lub AD FS, uruchom jedno z poniższych skryptów: Zastąp wartości ArmEndpoint tenantName usługi Azure AD, GraphAudience punktu końcowego i konfiguracji środowiska.

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Azure Active Directory (Azure AD), na podstawie wdrożenia

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -AADTenantName "<mydirectorytenant>.onmicrosoft.com" -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Wdrożenia na podstawie usługi Active Directory Federation Services (AD FS)

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -ADFS -EnvironmentName AzureStackAdmin

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
* [Tworzenie szablonów dla stosu Azure](user/azure-stack-develop-templates.md)
* [Wdrażanie szablonów za pomocą programu PowerShell](user/azure-stack-deploy-template-powershell.md)
