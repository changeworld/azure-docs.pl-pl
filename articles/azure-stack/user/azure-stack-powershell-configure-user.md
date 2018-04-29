---
title: Konfigurowanie środowiska PowerShell użytkownika stosu Azure | Dokumentacja firmy Microsoft
description: Konfigurowanie środowiska PowerShell użytkownika Azure stosu
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: mabrigg
ms.openlocfilehash: 44a5030bd9dfb694968a6b0f64ff9198ecccd84a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Konfigurowanie środowiska PowerShell użytkownika Azure stosu

Jako użytkownik stosu Azure można skonfigurować sieci Azure stosu Development Kit dla środowiska PowerShell. Po skonfigurowaniu, służy programu PowerShell do zarządzania stosu Azure zasobów takich jak subskrybować oferty, Tworzenie maszyn wirtualnych, wdrażanie szablonów usługi Azure Resource Manager itp. Ten temat obejmuje środowisk, jeśli chcesz skonfigurować środowiska PowerShell dla operatora środowiska chmury, odwoływać się tylko do użytkownika za pomocą [konfigurowania środowiska PowerShell operator stosu Azure](../azure-stack-powershell-configure-admin.md) artykułu. 

## <a name="prerequisites"></a>Wymagania wstępne 

Uruchom następujące wymagania wstępne, albo z [zestaw deweloperski](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), lub z systemem Windows klienta zewnętrznych w przypadku [połączone za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Zainstaluj [modułów programu Azure PowerShell platformy Azure zgodnego stosu](azure-stack-powershell-install.md).  
* Pobierz [narzędzia niezbędne do pracy z stosu Azure](azure-stack-powershell-download.md). 

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Konfigurowanie środowiska użytkownika i zaloguj się do stosu Azure

Na podstawie typu wdrożenia (Azure AD lub AD FS), uruchom jedno z poniższych skryptów można skonfigurować programu PowerShell dla usługi Azure stosu (Upewnij się, że zastąpienie AAD tenantName, GraphAudience punktu końcowego i wartości ArmEndpoint zgodnie z konfiguracją środowiska):

### <a name="azure-active-directory-aad-based-deployments"></a>Azure Active Directory (AAD) na podstawie wdrożenia
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Connect-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Wdrożenia na podstawie usługi Active Directory Federation Services (AD FS) 
          
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

  # Get the Active Directory tenantId that is used to deploy Azure Stack     
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Connect-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
  ```

## <a name="register-resource-providers"></a>Zarejestruj dostawców zasobów

Podczas pracy w przypadku subskrypcji nowo utworzony użytkownik, który nie zawiera żadnych zasobów wdrożone za pośrednictwem portalu, dostawców zasobów nie są automatycznie zarejestrowane. Jawnie należy zarejestrować za pomocą następującego skryptu:

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    } 
```

## <a name="test-the-connectivity"></a>Testowanie łączności

Teraz, gdy mamy wszystkie elementy konfiguracji, umożliwia tworzenie zasobów w stosie Azure przy użyciu programu PowerShell. Można na przykład utworzyć grupę zasobów dla aplikacji i Dodaj maszynę wirtualną. Aby utworzyć grupę zasobów o nazwie "MyResourceGroup", użyj następującego polecenia:

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie szablonów dla stosu Azure](azure-stack-develop-templates.md)
* [Wdrażanie szablonów za pomocą programu PowerShell](azure-stack-deploy-template-powershell.md)
