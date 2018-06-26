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
ms.date: 06/22/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 74a5a9408a78dd0da12fb3f8ed721774030cc438
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749865"
---
# <a name="configure-the-azure-stack-powershell-environment"></a>Konfigurowanie środowiska Azure PowerShell stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można skonfigurować stosu Azure przy użyciu programu PowerShell do zarządzania zasobami, takich jak tworzenie oferty, planów, przydziały i alerty. Ten temat ułatwia skonfigurowanie środowiska operatora.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom następujące wymagania wstępne, albo z [zestaw deweloperski](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), lub z systemem Windows klienta zewnętrznych w przypadku [połączone za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

 - Zainstaluj [modułów programu Azure PowerShell platformy Azure zgodnego stosu](azure-stack-powershell-install.md).  
 - Pobierz [narzędzia niezbędne do pracy z stosu Azure](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Skonfiguruj środowisko operatora i zaloguj się do stosu Azure

Skonfiguruj środowisko operatora stosu Azure przy użyciu programu PowerShell. Uruchom jedno z poniższych skryptów: Zastąp wartości ArmEndpoint tenantName usługi Azure AD, GraphAudience punktu końcowego i konfiguracji środowiska.

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
````

## <a name="test-the-connectivity"></a>Testowanie łączności

Teraz, gdy masz wszystkie elementy konfiguracji, zasobów w stosie Azure za pomocą programu PowerShell. Można na przykład utworzyć grupę zasobów dla aplikacji i Dodaj maszynę wirtualną. Użyj następującego polecenia, aby utworzyć grupę zasobów o nazwie **MyResourceGroup**.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Kolejne kroki

 - [Tworzenie szablonów dla stosu Azure](user/azure-stack-develop-templates.md)
 - [Wdrażanie szablonów za pomocą programu PowerShell](user/azure-stack-deploy-template-powershell.md)
