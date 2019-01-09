---
title: Nawiązywanie połączenia usługi Azure Stack przy użyciu programu PowerShell jako operator | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć się z usługi Azure Stack przy użyciu programu PowerShell, operator
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
ms.date: 12/07/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 7a744520fe2a3b53b1306d4c80a5eca7d86258a7
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104543"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Nawiązywanie połączenia usługi Azure Stack przy użyciu programu PowerShell jako operator

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Można skonfigurować usługi Azure Stack przy użyciu programu PowerShell do zarządzania zasobami, takimi jak tworzenie oferty, planów, limity przydziału i alerty. W tym temacie pomaga skonfigurować środowisko operatora.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom następujące wymagania wstępne, albo z [deweloperski](./asdk/asdk-connect.md#connect-with-rdp) lub z systemem Windows klient zewnętrznych przypadku [połączone ASDK za pośrednictwem sieci VPN](./asdk/asdk-connect.md#connect-with-vpn). 

 - Zainstaluj [modułów programu Azure PowerShell dla usługi Azure Stack zgodnego](azure-stack-powershell-install.md).  
 - Pobierz [narzędzia wymagane do pracy z usługą Azure Stack](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Łączenie z usługą Azure AD

Za pomocą programu PowerShell, należy skonfigurować środowisko operatora usługi Azure Stack. Uruchom jedno z następujących skryptów: Zamień tenantName usługi Azure Active Directory (Azure AD) i wartości punktu końcowego usługi Azure Resource Manager konfiguracji środowiska. <!-- GraphAudience endpoint -->

```PowerShell  
    # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Łączenie z usługami AD FS

Podłącz do środowiska operatora usługi Azure Stack przy użyciu programu PowerShell przy użyciu usługi Azure Active Directory Sfederowana Services (Azure AD FS). Zestaw Azure Stack development kit tego punktu końcowego usługi Azure Resource Manager jest ustawiona na `https://adminmanagement.local.azurestack.external`. Aby uzyskać punkt końcowy usługi Azure Resource Manager dla usługi Azure Stack zintegrowane systemy, skontaktuj się z dostawcą usług.

<!-- GraphAudience endpoint -->

  ```PowerShell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
  $tenantId = (invoke-restmethod "$($AuthEndpoint)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment

  $cred = get-credential

  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $tenantId `
    -Credential $cred
  ```



## <a name="test-the-connectivity"></a>Testowanie łączności

Teraz, gdy masz wszystkie elementy konfiguracji, za pomocą programu PowerShell do tworzenia zasobów w ramach usługi Azure Stack. Na przykład można utworzyć grupę zasobów dla aplikacji i Dodaj maszynę wirtualną. Użyj następującego polecenia, aby utworzyć grupę zasobów o nazwie **MyResourceGroup**.

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Kolejne kroki

 - [Tworzenie szablonów usługi Azure Stack](user/azure-stack-develop-templates.md)
 - [Wdrażanie szablonów za pomocą programu PowerShell](user/azure-stack-deploy-template-powershell.md)
