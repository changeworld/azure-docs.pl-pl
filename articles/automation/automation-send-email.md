---
title: Wyślij wiadomość e-mail z elementu runbook usługi Azure Automation
description: Dowiedz się, jak używać usługi SendGrid, aby wysłać wiadomość e-mail z w elemencie runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 07/15/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: ce05aadb53cc3ad24ed65ea139594010e1aef047
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234983"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Samouczek: Wyślij wiadomość e-mail z elementu runbook usługi Azure Automation

Możesz wysłać wiadomość e-mail z elementem runbook za pomocą [SendGrid](https://sendgrid.com/solutions) przy użyciu programu PowerShell. W tym samouczku opisano, jak utworzyć element runbook wielokrotnego użytku, który wysyła wiadomość e-mail przy użyciu klucza interfejsu API, przechowywane w [Azure KeyVault](/azure/key-vault/).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie usługi Azure KeyVault
> * Store swój klucz interfejsu API usługi SendGrid w magazynie KeyVault
> * Tworzenie elementu runbook, który pobiera klucz interfejsu API i wysyła wiadomość e-mail

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności przedstawionych w tym samouczku są wymagane następujące elementy:

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Utwórz konto usługi SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Konto usługi Automation](automation-offering-get-started.md) z **Az** moduły, i [połączenie Uruchom jako](automation-create-runas-account.md), w celu przechowywania i wykonywania elementu runbook.

## <a name="create-an-azure-keyvault"></a>Tworzenie usługi Azure KeyVault

Można utworzyć magazyn kluczy platformy Azure, za pomocą następującego skryptu programu PowerShell. Zastąp wartości zmiennych wartościami dla danego środowiska. Użyj osadzonego Azure Cloud Shell za pomocą <kbd>wypróbuj</kbd> przycisk znajdujący się w prawym górnym rogu bloku kodu. Możesz również kopiować i uruchomienia kodu lokalnie, jeśli masz [modułu programu PowerShell Azure](/powershell/azure/install-az-ps) zainstalowane na komputerze lokalnym.

> [!NOTE]
> Aby pobrać klucz interfejsu API, należy wykonać kroki w [znaleźć klucz interfejsu API usługi SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the KeyVault to the Automation RunAs account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Inne sposoby tworzenia magazynu kluczy Azure, a następnie umieszczanie wpisu tajnego, zobacz [KeyVault przewodników Szybki Start](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Zaimportować wymagane moduły do swojego konta usługi Automation

Za pomocą usługi Azure KeyVault w elemencie runbook, konto usługi Automation muszą następujących modułów:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile).
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault).

Kliknij przycisk <kbd>wdrażanie w usłudze Azure Automation</kbd> na karcie usługi Azure Automation w ramach opcji instalacji. Ta akcja powoduje otwarcie witrynę Azure portal. Na stronie Import, wybierz konto usługi Automation, a następnie kliknij przycisk <kbd>OK</kbd>.

Aby uzyskać dodatkowe metody dodawania wymaganych modułów, zobacz [modułów importu](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Tworzenie elementu runbook, aby wysłać wiadomość e-mail

Po utworzeniu magazynu kluczy i przechowywany klucz interfejsu API usługi SendGrid, nadszedł czas na tworzenie elementu runbook, który pobierze klucz interfejsu API i Wyślij wiadomość e-mail.

Ten element runbook używa AzureRunAsConnection [konta Uruchom jako](automation-create-runas-account.md) do uwierzytelniania za pomocą platformy Azure, aby pobrać wpisu tajnego z magazynu kluczy Azure.

Użyj tego przykładu, aby utworzyć element runbook o nazwie **GridMailMessage wysyłania**. Możesz zmodyfikować skrypt programu PowerShell i użyć go ponownie w różnych scenariuszach.

1. Przejdź do swojego konta usługi Azure Automation.
2. W obszarze **automatyzacji procesów**, wybierz opcję **elementów Runbook**.
3. W górnej części listy elementów runbook, zaznacz **+ Utwórz element runbook**.
4. Na **Dodawanie elementu Runbook** wpisz **GridMailMessage wysyłania** dla nazwy elementu runbook. Wybierz typ elementu runbook **PowerShell**. Następnie wybierz przycisk **Create** (Utwórz).
   ![Tworzenie elementu Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Element runbook zostanie utworzony i zostanie otworzona strona **Edytuj element runbook programu PowerShell**.
   ![Edytuj element Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Skopiuj poniższy przykład programu PowerShell do **Edytuj** strony. Upewnij się, że `$VaultName` jest nazwa określona podczas tworzenia usługi KeyVault.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Wybierz **Publikuj** zapisywanie i publikowanie elementu runbook.

Aby sprawdzić, czy element runbook wykonuje z powodzeniem możesz wykonać kroki w obszarze [przetestować element runbook](manage-runbooks.md#test-a-runbook) lub [uruchamiania elementu runbook](start-runbooks.md).
Jeśli nie ma wstępnie testową wiadomość e-mail, sprawdź swoje **wiadomości-śmieci** i **spamu** folderów.

## <a name="clean-up"></a>Czyszczenie

Gdy element runbook nie będzie już potrzebny, usuń go. Aby to zrobić, zaznacz element runbook na liście i kliknij pozycję **Usuń**.

Usuwanie magazynu kluczy przy użyciu [Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps) polecenia cmdlet.

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Następne kroki

* W przypadku problemów, tworzenie i uruchamianie elementu runbook, zobacz [Rozwiązywanie problemów z elementami runbook](./troubleshoot/runbooks.md).
* Aby zaktualizować moduły na koncie usługi Automation, zobacz [jak aktualizowanie modułów programu Azure PowerShell w usłudze Azure Automation](automation-update-azure-modules.md)].
* Aby monitorować wykonanie elementu runbook, zobacz [przekazywania strumienie zadania i stan zadania z usługi Automation do usługi Azure Monitor dzienników](automation-manage-send-joblogs-log-analytics.md).
* Aby wyzwolić elementu runbook za pomocą alertu, zobacz [umożliwia alertu można wyzwolić elementu runbook usługi Azure Automation](automation-create-alert-triggered-runbook.md).
