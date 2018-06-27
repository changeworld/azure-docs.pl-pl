---
title: Zarejestruj ASDK Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób rejestrowania stosu Azure przy użyciu platformy Azure marketplace zespolonego oraz raportowanie użycia.
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
ms.date: 06/26/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 08a300d0e2d1565428f282a2073d91b5dd08c060
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017003"
---
# <a name="azure-stack-registration"></a>Rejestracja w usłudze Azure stosu
Można zarejestrować instalacji programu Azure stosu Development Kit (ASDK) z platformy Azure, aby pobrać elementów marketplace z platformy Azure i ustaw commerce dane raportowania z powrotem do firmy Microsoft. Rejestracja jest wymagany do obsługi pełnej funkcjonalności stosu Azure, łącznie z witryny marketplace zespolonego. Rejestracja jest zalecane, ponieważ umożliwia testowanie ważne funkcje stosu Azure marketplace zespolonego i raportowanie użycia. Po zarejestrowaniu stosu Azure użycia jest zgłaszane do handlu platformy Azure. Można to sprawdzić w ramach subskrypcji, używanego do rejestracji. ASDK użytkownicy nie są jednak obciążony bez użycia, które zgłaszają.

Jeśli nie zarejestrujesz ASDK Twojego, mogą pojawić **wymagana aktywacja** alert ostrzeżenia, zaleceniem można zarejestrować zestawu Azure stosu Development Kit. To zachowanie jest oczekiwane.

## <a name="prerequisites"></a>Wymagania wstępne
Przed użyciem tych instrukcji do zarejestrowania ASDK z platformy Azure, upewnij się, że masz zainstalowany program Azure PowerShell stosu i pobrane narzędzia stosu Azure, zgodnie z opisem w [konfiguracji po wdrożeniu](asdk-post-deploy.md) artykułu.

Ponadto tryb języka programu PowerShell musi mieć ustawioną **FullLanguageMode** na komputerze używanym do zarejestrowania ASDK z platformy Azure. Aby sprawdzić, czy bieżący tryb język jest ustawiony na pełne, Otwórz okno programu PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenia programu PowerShell:

```powershell
$ExecutionContext.SessionState.LanguageMode
```

Upewnij się, zwraca dane wyjściowe **FullLanguageMode**. Jeśli inne tryb języka jest zwracany, rejestracji musi być uruchomiony na innym komputerze lub tryb języka musi być równa **FullLanguageMode** przed kontynuowaniem.

## <a name="register-azure-stack-with-azure"></a>Zarejestruj stosu Azure przy użyciu platformy Azure
Wykonaj te kroki, aby zarejestrować ASDK w usłudze Azure.

> [!NOTE]
> Kroki te należy uruchomić z komputera, który ma dostęp do uprzywilejowanych punktu końcowego. W przypadku ASDK, który jest development kit hosta.

1. Otwórz konsolę programu PowerShell jako administrator.  

2. Uruchom następujące polecenia programu PowerShell, można zarejestrować instalacji ASDK z platformy Azure. Musisz zalogować się do subskrypcji platformy Azure i lokalnej instalacji ASDK. Jeśli nie masz subskrypcji platformy Azure, ale możesz [utworzyć tutaj bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?b=17.06). Rejestrowanie stosu Azure wiąże się bez kosztów w ramach subskrypcji platformy Azure.

  ```powershell
  # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
  Add-AzureRmAccount -EnvironmentName "AzureCloud"

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  #Register Azure Stack
  $AzureContext = Get-AzureRmContext
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01 `
      -BillingModel Development
  ```
3. Po ukończeniu działania skryptu, powinien zostać wyświetlony ten komunikat: **środowiska został zarejestrowany i aktywować przy użyciu podanych parametrów.**

    ![](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>Sprawdź, czy rejestracja zakończyła się pomyślnie
Wykonaj następujące kroki, aby zweryfikować, że ASDK rejestracji w usłudze Azure zakończyła się pomyślnie.

1. Zaloguj się do [portalu administracyjnego platformy Azure stosu](https://adminportal.local.azurestack.external).

2. Kliknij przycisk **zarządzania Marketplace** > **dodać z platformy Azure**.

    ![](media/asdk-register/2.PNG)

3. Jeśli zobaczysz listę elementów dostępnej w sklepie Azure, proces aktywacji zakończyło się pomyślnie.

    ![](media/asdk-register/3.PNG)

## <a name="next-steps"></a>Kolejne kroki
[Dodawanie elementu stosu Azure marketplace](.\.\azure-stack-marketplace.md)
