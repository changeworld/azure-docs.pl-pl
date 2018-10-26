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
ms.date: 10/24/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: bbd20df7f002d6072110e3b94887bac24ce13cd2
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087444"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Nawiązywanie połączenia usługi Azure Stack przy użyciu programu PowerShell jako operator

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Można skonfigurować usługi Azure Stack przy użyciu programu PowerShell do zarządzania zasobami, takimi jak tworzenie oferty, planów, limity przydziału i alerty. W tym temacie pomaga skonfigurować środowisko operatora.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom następujące wymagania wstępne, albo z [deweloperski](.\asdk\asdk-connect.md#connect-with-rdp) lub z systemem Windows klient zewnętrznych przypadku [połączone ASDK za pośrednictwem sieci VPN](.\asdk\asdk-connect.md#connect-with-vpn). 

 - Zainstaluj [modułów programu Azure PowerShell dla usługi Azure Stack zgodnego](azure-stack-powershell-install.md).  
 - Pobierz [narzędzia wymagane do pracy z usługą Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Skonfiguruj środowisko operatora i zaloguj się do usługi Azure Stack

Za pomocą programu PowerShell, należy skonfigurować środowisko operatora usługi Azure Stack. Uruchom jedno z następujących skryptów: Zastąp wartości ArmEndpoint tenantName usługi Azure AD, GraphAudience punktu końcowego i konfiguracji środowiska.

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
````

## <a name="test-the-connectivity"></a>Testowanie łączności

Teraz, gdy masz wszystkie elementy konfiguracji, za pomocą programu PowerShell do tworzenia zasobów w ramach usługi Azure Stack. Na przykład można utworzyć grupę zasobów dla aplikacji i Dodaj maszynę wirtualną. Użyj następującego polecenia, aby utworzyć grupę zasobów o nazwie **MyResourceGroup**.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Kolejne kroki

 - [Tworzenie szablonów usługi Azure Stack](user/azure-stack-develop-templates.md)
 - [Wdrażanie szablonów za pomocą programu PowerShell](user/azure-stack-deploy-template-powershell.md)