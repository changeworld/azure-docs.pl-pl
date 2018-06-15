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
ms.date: 5/15/2018
ms.author: mabrigg
ms.reviewer: Balsu.G
ms.openlocfilehash: 2655b682d35dd1879c649ed58d524ecd80808896
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258026"
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Konfigurowanie środowiska PowerShell użytkownika Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Do konfigurowania środowiska PowerShell dla użytkownika stosu Azure, wykonaj instrukcje w tym artykule.
Po skonfigurowaniu środowiska może zarządzać zasobami stosu Azure za pomocą programu PowerShell. Na przykład umożliwia PowerShell subskrybować oferty, Tworzenie maszyn wirtualnych i wdrażanie szablonów usługi Azure Resource Manager.

>[!NOTE]
>W tym artykule znajduje się w zakresie stosu Azure środowiska użytkownika. Ustanowienie środowiska PowerShell dla operatora środowiska chmury, zapoznaj się [konfigurowania środowiska PowerShell operator stosu Azure](../azure-stack-powershell-configure-admin.md) artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

Możesz skonfigurować te składniki z [zestaw deweloperski](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), lub z systemem Windows klienta zewnętrznych w przypadku [połączone za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Zainstaluj [modułów programu Azure PowerShell platformy Azure zgodnego stosu](azure-stack-powershell-install.md).
* Pobierz [narzędzia niezbędne do pracy z stosu Azure](azure-stack-powershell-download.md).

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Konfigurowanie środowiska użytkownika i zaloguj się do stosu Azure

Na podstawie typu wdrożenia stosu Azure (Azure AD lub AD FS), uruchom jedno z poniższych skryptów można skonfigurować programu PowerShell dla usługi Azure stosu.

Upewnij się, że następujące zmienne skryptu zastąpić wartościami z konfiguracji Azure stosu:

* TenantName usługi AAD
* Punkt końcowy GraphAudience
* ArmEndpoint

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
  Login-AzureRmAccount `
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
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
  ```

## <a name="register-resource-providers"></a>Zarejestruj dostawców zasobów

Dostawcy zasobów nie są automatycznie zarejestrowane dla nowej subskrypcji użytkownika, które nie mają żadnych zasobów wdrożone za pośrednictwem portalu. Jawnie można zarejestrować dostawcy zasobów za pomocą następującego skryptu:

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Testowanie łączności

Jeśli masz wszystko Konfigurowanie testowanie łączności za pomocą programu PowerShell na tworzenie zasobów Azure stosu. Badanie Utwórz grupę zasobów dla aplikacji i Dodaj maszynę wirtualną. Uruchom następujące polecenie, aby utworzyć grupę zasobów o nazwie "MyResourceGroup":

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie szablonów dla stosu Azure](azure-stack-develop-templates.md)
* [Wdrażanie szablonów za pomocą programu PowerShell](azure-stack-deploy-template-powershell.md)
