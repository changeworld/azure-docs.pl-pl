---
title: Automatyzacja sprawdzania poprawności usługi Azure Stack, przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Azure Stack weryfikacji przy użyciu programu PowerShell można zautomatyzować.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7f9a4ce4f1e16f69a1d8998e24c1bfe955d17d92
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767111"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatyzacja sprawdzania poprawności usługi Azure Stack, przy użyciu programu PowerShell

Weryfikacji jako usługa (VaaS) zapewnia możliwość automatyzacji, uruchamiania testów za pomocą **LaunchVaaSTests.ps1** skryptu.

> [!NOTE]  
> Usługi Automation jest dostępna tylko dla przepływu pracy przebiegu testu. Sprawdzanie poprawności pakietu i sprawdzanie poprawności rozwiązania przepływy pracy są obsługiwane tylko za pośrednictwem portalu VaaS.

Ten skrypt może służyć do:

> [!div class="checklist"]
> * Instalacja wymagań wstępnych
> * Zainstaluj i uruchom agent lokalny
> * Uruchom kategorii testów, takie jak *integracji*, *funkcjonalności*, *niezawodności*
> * Raport wyników testu

## <a name="launch-the-test-pass-workflow"></a>Uruchamianie przepływu pracy przebiegu testu

1. Otwórz wiersz PowerShell z podwyższonym poziomem uprawnień.

2. Uruchom następujący skrypt, aby pobrać skrypt automatyzacji:

    ```PowerShell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. Uruchom następujący skrypt przy użyciu wartości odpowiednich parametrów:

    ```PowerShell
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
                          -VaaSAccountTenantId <VaaSAccountTenantId> `
                          -VaaSSolutionName <VaaSSolutionName> `
                          -VaaSTestPassName <VaaSTestPassName> `
                          -VaaSTestCategories Integration,Functional `
                          -MaxScriptWaitTimeInHours 12 `
                          -ServiceAdminUserName <AzSServiceAdminUser> `
                          -ServiceAdminUserPassword <AzSServiceAdminPassword> `
                          -TenantAdminUserName <AzSTenantAdminUser> `
                          -TenantAdminUserPassword <AzSTenantAdminPassword> `
                          -CloudAdminUserName <AzSCloudAdminUser> `
                          -CloudAdminUserPassword <AzSCloudAdminPassword>
    ```

    **Parametry**

    | Parametr | Opis |
    | --- | --- |
    | VaaSUserId | Identyfikator VaaS użytkownika. |
    | VaaSUserPassword | Hasło VaaS. |
    | VaaSAccountTenantId | Dzierżawy VaaS identyfikatora GUID. |
    | VaaSSolutionName | Nazwa rozwiązania VaaS, pod którym test zakończy się pomyślnie, zostanie uruchomiony. |
    | VaaSTestPassName | Nazwa testu VaaS przekazać przepływu pracy do utworzenia. |
    | VaaSTestCategories | `Integration`, `Functional`, lub. `Reliability`. Jeśli używasz wielu wartości, Oddziel poszczególne wartości oddzielonych przecinkami.  |
    | ServiceAdminUserName | Konta administratora usługi Azure Stack.  |
    | ServiceAdminPassword | Hasło użytkownika usługi Azure Stack.  |
    | TenantAdminUserName | Administrator dzierżawy podstawowej.  |
    | TenantAdminPassword | Hasło dla dzierżawy podstawowej.  |
    | CloudAdminUserName | Nazwa użytkownika administratora chmury.  |
    | CloudAdminPassword | Hasło administratora chmury.  |

4. Przejrzyj wyniki testu. Innych opcjach, zobacz temat [monitorowanie i zarządzanie testami w portalu VaaS](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat programu PowerShell w usłudze Azure Stack, należy przejrzeć najnowsze moduły.

> [!div class="nextstepaction"]
> [Moduł usługi Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
