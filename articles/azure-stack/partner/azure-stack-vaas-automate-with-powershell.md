---
title: Automatyzacja sprawdzania poprawności usługi Azure Stack, przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Można zautomatyzować walidacji usługi Azure Stack przy użyciu programu PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 1cb4b1a7cfd72ea302676244a53af58e77215aa9
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235456"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatyzacja sprawdzania poprawności usługi Azure Stack, przy użyciu programu PowerShell 

Weryfikacji jako usługa (VaaS) zapewnia możliwość automatyzacji, uruchamiania testów za pomocą **LaunchVaaSTests.ps1** skryptu.

Za pomocą programu PowerShell dla następującego przepływu pracy:

- Przepływ pracy w przebiegu testu

Ten skrypt obejmuje cztery elementy przepływu pracy:

- Instaluje wstępnie wymagane składniki.
- Służy do instalowania i uruchamiania lokalnego agenta.
- Uruchamia kategorii testów, takich jak integracja funkcjonalności, niezawodności.
- Raporty każdy test przekazać wynik do monitorowania i raportowania pliku generacji.

## <a name="launch-the-test-pass-workflow"></a>Uruchamianie przepływu pracy przebieg testu

1. Otwórz wiersz PowerShell z podwyższonym poziomem uprawnień.

2. Uruchom następujący skrypt, aby pobrać skrypt automatyzacji:

    ````PowerShell  
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://vaastestpacksprodeastus.blob.core.windows.net/packages/Microsoft.VaaS.Scripts.3.0.0.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ````

3. Uruchom poniższy skrypt z własnymi wartościami:

    ````PowerShell  
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>"  -AsPlainText -Force)
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<ServiceAdminUser>", (ConvertTo-SecureString "<ServiceAdminPassword>" -AsPlainText -Force)
    $TenantAdminCreds = New-Object System.Management.Automation.PSCredential "<TenantAdminUser>", (ConvertTo-SecureString "<TenantAdminPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
        -VaaSAccountTenantId <VaaSAccountTenantId> `
        -VaaSSolutionName <VaaSSolutionName> `
        -VaaSTestPassName <VaaSTestPassName> `
        -VaaSTestCategories Integration,Functional `
        -MaxScriptWaitTimeInHours 12 `
        -ServiceAdminCreds $ServiceAdminCreds `
    ````

    **Parametry**

    | Parametr | Opis |
    | --- | --- |
    | VaaSUserld | Identyfikator VaaS użytkownika. | 
    | VaaSUserPassword | Hasło VaaS. |
    | ServiceAdminUser | Konta administratora usługi Azure Stack.  |
    | ServiceAdminPassword | Hasło użytkownika usługi Azure Stack.  |
    | TenantAdminUser | Administrator dzierżawy podstawowej.  |
    | TenantAdminPassword | Hasło dla dzierżawy podstawowej.  |
    | FunctionalCategory| Integracja z funkcjonalności, lub. Niezawodność. Jeśli używasz wielu wartości, Oddziel poszczególne wartości oddzielonych przecinkami.  |

4. Przejrzyj wyniki testu. Aby dowiedzieć się, jak odczytywanie wyników testu, zobacz [monitorowanie testów](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Kolejne kroki

 - Aby dowiedzieć się więcej na temat [walidacji usługi Azure Stack jako usługa](https://docs.microsoft.com/azure/azure-stack/partner).
 - Aby dowiedzieć się więcej na temat programu PowerShell w usłudze Azure Stack, zobacz [modułu usługi Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) odwołania.