---
title: Rejestrowania ASDK na platformie Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób rejestrowania usługi Azure Stack z platformą Azure, umożliwiając syndykacji portalu marketplace i raportowanie użycia.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: d06ad47dc2962b249b4e7aef5667492e642be35e
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830127"
---
# <a name="azure-stack-registration"></a>Rejestracja w usłudze Azure Stack
Instalacja usługi Azure Stack Development Kit (ASDK) można zarejestrować za pomocą platformy Azure, aby pobieranie elementów portalu marketplace z platformy Azure i Ustaw dane handlu odsyłanie do firmy Microsoft. Rejestracja jest wymagany do obsługi pełnego funkcje usługi Azure Stack, w tym portalu marketplace syndykacji. Rejestracja jest zalecane, ponieważ umożliwia testowanie ważnych funkcji usługi Azure Stack, takich jak syndykacji portalu marketplace i raportowanie użycia. Po zarejestrowaniu usługi Azure Stack, użycie jest zgłaszany do handlu platformy Azure. Można to sprawdzić w ramach subskrypcji, których użyto podczas rejestracji. Jednak użytkownicy ASDK nie są naliczane za dotychczasowe użycie raportowane.

Jeśli nie zarejestrujesz swoje ASDK, możesz zobaczyć **wymagana aktywacja** alert ostrzeżenia, zaleceniem rejestrowania usługi Azure Stack Development Kit. Takie zachowanie jest oczekiwane.

## <a name="prerequisites"></a>Wymagania wstępne
Przed użyciem tych instrukcji do rejestrowania ASDK na platformie Azure, upewnij się, że zainstalowany program Azure PowerShell stosu i pobrać narzędzia usługi Azure Stack, zgodnie z opisem w [konfiguracji powdrożeniowej](asdk-post-deploy.md) artykułu.

Ponadto tryb języka programu PowerShell musi być równa **FullLanguageMode** na komputerze używanym do rejestrowania ASDK na platformie Azure. Aby sprawdzić, czy bieżący tryb języka jest ustawiony na pełne, Otwórz okno programu PowerShell z podwyższonym i uruchom następujące polecenia programu PowerShell:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Upewnij się, zwraca dane wyjściowe **FullLanguageMode**. Jeśli inny tryb języka jest zwracany, rejestracja będzie należy uruchomić na innym komputerze lub będą musieli można ustawić tryb języka **FullLanguageMode** przed kontynuowaniem.

## <a name="register-azure-stack-with-azure"></a>Rejestrowania usługi Azure Stack na platformie Azure
Wykonaj następujące kroki, aby zarejestrować ASDK z platformą Azure.

> [!NOTE]
> Wszystkie te kroki należy uruchomić z komputera, który ma dostęp do uprzywilejowanych punktu końcowego. ASDK to komputerze-hoście development kit.

1. Otwórz konsolę programu PowerShell jako administrator.  

2. Uruchom następujące polecenia programu PowerShell w celu rejestrowania instalacji ASDK na platformie Azure. Musisz zalogować się do subskrypcji platformy Azure i lokalnej instalacji ASDK. Jeśli nie masz subskrypcji platformy Azure, ale możesz [Utwórz w tym miejscu Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?b=17.06). Rejestrowanie usługi Azure Stack jest naliczana opłata bez kosztów związanych z subskrypcją platformy Azure.<br><br>Jeśli skrypt rejestracji są uruchomione na więcej niż jedno wystąpienie usługi Azure Stack przy użyciu tego samego Identyfikatora subskrypcji platformy Azure, należy ustawić unikatową nazwę dla rejestracji po uruchomieniu **AzsRegistration zestaw** polecenia cmdlet. **RegistrationName** parametr ma wartość domyślną **AzureStackRegistration**. Jednak jeśli używasz tej samej nazwie w więcej niż jedno wystąpienie usługi Azure Stack, skrypt zakończy się niepowodzeniem.

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    $UsageReporting = True # Set to False if you using a Capacity Billing Model
    Set-AzsRegistration `
        -PrivilegedEndpointCredential $CloudAdminCred `
        -PrivilegedEndpoint AzS-ERCS01 `
        -BillingModel Development `
        -RegistrationName $RegistrationName `
        -EnableUsageReporting $UsageReporting
    ```
3. Po ukończeniu działania skryptu, powinien zostać wyświetlony ten komunikat: **środowiska jest teraz zarejestrowany i aktywować przy użyciu podanych parametrów.**

    ![Środowisko jest obecnie zarejestrowany.](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>Sprawdź, czy rejestracja zakończyła się pomyślnie
Wykonaj następujące kroki, aby sprawdzić, czy rejestracji ASDK z platformą Azure zakończyła się pomyślnie.

1. Zaloguj się do [portalu administratora usługi Azure Stack](https://adminportal.local.azurestack.external).

2. Kliknij przycisk **zarządzania Marketplace** > **Dodaj na platformie Azure**.

    ![](media/asdk-register/2.PNG)

3. Jeśli zobaczysz listę elementów dostępnych na platformie Azure, proces aktywacji zakończyło się pomyślnie.

    ![](media/asdk-register/3.PNG)

## <a name="move-a-registration-resource"></a>Przenieś zasób rejestracji
Przenoszenie zasobów rejestracji między grupami zasobów w ramach tej samej subskrypcji **jest** obsługiwane. Aby uzyskać więcej informacji na temat przenoszenia zasobów do nowej grupy zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Kolejne kroki
[Dodawanie elementu portalu marketplace usługi Azure Stack](.\.\azure-stack-marketplace.md)
