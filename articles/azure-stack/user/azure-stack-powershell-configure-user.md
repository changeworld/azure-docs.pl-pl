---
title: Nawiązywanie połączenia usługi Azure Stack przy użyciu programu PowerShell jako użytkownik | Dokumentacja firmy Microsoft
description: Kroki, aby nawiązać połączenie z wystąpieniem usługi Azure Stack przez użytkownika.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2018
ms.author: sethm
ms.reviewer: Balsu.G
ms.openlocfilehash: 7710a08fffed304d4d9dd9b036e4c00bcd02a9a9
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52965961"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Nawiązywanie połączenia usługi Azure Stack przy użyciu programu PowerShell jako użytkownik

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Ten artykuł zawiera kroki, aby nawiązać połączenie z wystąpieniem usługi Azure Stack. Należy połączyć do zarządzania zasobami usługi Azure Stack przy użyciu programu PowerShell. Na przykład można użyć programu PowerShell do subskrybować oferty, Tworzenie maszyn wirtualnych i wdrażania szablonów usługi Azure Resource Manager. Aby można było wykonać polecenia cmdlet programu PowerShell.

Aby skonfigurować:
  - Upewnij się, że masz wymagania.
  - Połącz się z usługi Azure Active Directory (Azure AD) lub usługi Active Directory Federation Services (AD FS). 
  - Zarejestruj dostawców zasobów.
  - Przetestuj połączenie.

## <a name="prerequisites"></a>Wymagania wstępne

Można skonfigurować następujące wstępnie wymagane składniki z [deweloperski](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), lub z systemem Windows klient zewnętrznych przypadku [połączone za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Zainstaluj [modułów programu Azure PowerShell dla usługi Azure Stack zgodnego](azure-stack-powershell-install.md).
* Pobierz [narzędzia wymagane do pracy z usługą Azure Stack](azure-stack-powershell-download.md).

Upewnij się, że należy zastąpić następujące zmienne skryptu wartości z konfiguracji usługi Azure Stack:

- **Nazwa dzierżawy usługi Azure AD**  
  Nazwa dzierżawy usługi Azure AD używany do zarządzania usługi Azure Stack, na przykład yourdirectory.onmicrosoft.com.
- **Punkt końcowy usługi Azure Resource Manager**  
  Dla zestawu SDK usługi Azure Stack, ta wartość jest równa https://management.local.azurestack.external. Aby zyskać tę wartość dla usługi Azure Stack zintegrowane systemy, skontaktuj się z dostawcą usług.

## <a name="connect-with-azure-ad"></a>Łączenie z usługą Azure AD

  ```PowerShell
  $AADTenantName = "yourdirectory.onmicrosoft.com"
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantId
   ```

## <a name="connect-with-ad-fs"></a>Łączenie z usługami AD FS

  ```PowerShell  
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $tenantId = (invoke-restmethod "$($AuthEndpoint)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment

  $cred = get-credential

  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $tenantId `
     $cred = get-credential
  ```

## <a name="register-resource-providers"></a>Rejestrowanie dostawcy zasobów

Dostawcy zasobów nie są automatycznie rejestrowane w przypadku nowych subskrypcji użytkownika, które nie mają żadnych zasobów wdrożonych za pośrednictwem portalu. Można jawnie zarejestrować dostawcę zasobów, uruchamiając następujący skrypt:

```PowerShell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Testowanie łączności

Gdy masz wszystko, konfigurowanie, przetestuj łączność przy użyciu programu PowerShell do tworzenia zasobów w usłudze Azure Stack. Jako test Utwórz grupę zasobów dla aplikacji, a następnie dodaj maszynę wirtualną. Uruchom następujące polecenie, aby utworzyć grupę zasobów o nazwie "MyResourceGroup":

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie szablonów usługi Azure Stack](azure-stack-develop-templates.md)
- [Wdrażanie szablonów za pomocą programu PowerShell](azure-stack-deploy-template-powershell.md)
- Jeśli chcesz skonfigurować program PowerShell w środowisku — operator chmury, odnoszą się do [Konfigurowanie środowiska PowerShell usługi Azure Stack — operator](../azure-stack-powershell-configure-admin.md) artykułu.