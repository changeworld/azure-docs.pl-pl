---
title: Zarejestruj ASDK Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób rejestrowania stosu Azure przy użyciu platformy Azure marketplace zespolonego oraz raportowanie użycia.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: f7398945efe6b9b5ad148f6fa0d303363da9abaa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="register-azure-stack-with-azure"></a>Zarejestruj stosu Azure przy użyciu platformy Azure
Można zarejestrować instalacji programu Azure stosu Development Kit (ASDK) z platformy Azure, aby pobrać elementów marketplace z platformy Azure i ustaw commerce dane raportowania z powrotem do firmy Microsoft. Rejestracja jest zalecane, ponieważ umożliwia testowanie ważne funkcje stosu Azure marketplace zespolonego i raportowanie użycia. Po zarejestrowaniu stosu Azure użycia jest zgłaszane do handlu platformy Azure. Można to sprawdzić w ramach subskrypcji, używanego do rejestracji. ASDK użytkownicy nie są jednak obciążony bez użycia, które zgłaszają.


## <a name="register-azure-stack-with-azure"></a>Zarejestruj stosu Azure przy użyciu platformy Azure 
Wykonaj te kroki, aby zarejestrować ASDK w usłudze Azure.

> [!NOTE]
> Kroki te należy uruchomić z komputera, który ma dostęp do uprzywilejowanych punktu końcowego. W przypadku ASDK, który jest development kit hosta. 

Przed użyciem tych kroków można zarejestrować ASDK w usłudze Azure, upewnij się, że masz zainstalowany program Azure PowerShell stosu i pobrane narzędzia stosu Azure, zgodnie z opisem w [konfiguracji po wdrożeniu](asdk-post-deploy.md) artykułu. 

1. Otwórz konsolę programu PowerShell jako administrator.  

2. Uruchom następujące polecenia programu PowerShell można zarejestrować instalacji ASDK z platformy Azure (musisz zalogować się do subskrypcji platformy Azure i lokalnej instalacji ASDK):

    ```PowerShell
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

3. When the script completes, you should see this message: **Your environment is now registered and activated using the provided parameters.**

    ![](media/asdk-register/1.PNG) 

## Verify the registration was successful
Follow these steps to verify that the ASDK registration with Azure was successful.

1. Sign in to the [Azure Stack administration portal](https://adminportal.local.azurestack.external).

2. Click **Marketplace Management** > **Add from Azure**.

    ![](media/asdk-register/2.PNG) 

3. If you see a list of items available from Azure, your activation was successful.

    ![](media/asdk-register/3.PNG) 

## Next steps
[Add an Azure Stack marketplace item](asdk-marketplace-item.md)
