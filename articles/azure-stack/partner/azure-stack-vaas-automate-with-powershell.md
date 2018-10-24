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
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: aa33db56910871891003866ef62431cfd7095d2a
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954130"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatyzacja sprawdzania poprawności usługi Azure Stack, przy użyciu programu PowerShell

Weryfikacji jako usługa (VaaS) zapewnia możliwość automatyzacji, uruchamiania testów za pomocą **LaunchVaaSTests.ps1** skryptu.

Za pomocą programu PowerShell dla następującego przepływu pracy:

- Przebieg testu

W tym samouczku dowiesz się, jak utworzyć skrypt, który:

> [!div class="checklist"]
> * Wymagania wstępne instalacji
> * Instaluje i uruchamia agent lokalny
> * Uruchamia kategorii testów, np. integrację funkcjonalności, niezawodności
> * Wyniki testu raportów

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
    | VaaSUserld | Identyfikator VaaS użytkownika. |
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

Aby dowiedzieć się więcej na temat programu PowerShell w usłudze Azure Stack, przeanalizuj moduły najpóźniejsza.

> [!div class="nextstepaction"]
> [Moduł usługi Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.5.0)
