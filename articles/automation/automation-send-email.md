---
title: Wyślij wiadomość e-mail z elementu Runbook Azure Automation
description: Dowiedz się, jak wysyłać wiadomości e-mail z poziomu elementu Runbook za pomocą usługi SendGrid.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: 6acb68b7bbaa54db2e4143a42e43aede2caed35f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420705"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Samouczek: wysyłanie wiadomości e-mail z Azure Automation elementu Runbook

Możesz wysłać wiadomość e-mail z elementu Runbook za pomocą usługi [SendGrid](https://sendgrid.com/solutions) przy użyciu programu PowerShell. W tym samouczku przedstawiono sposób tworzenia elementu Runbook wielokrotnego użytku, który wysyła wiadomość e-mail przy użyciu klucza interfejsu API przechowywanego w [magazynie kluczy platformy Azure](/azure/key-vault/).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie magazynu kluczy platformy Azure
> * Przechowywanie klucza interfejsu API SendGrid w magazynie kluczy
> * Utwórz element Runbook, który pobierze klucz interfejsu API i wyśle wiadomość e-mail

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności przedstawionych w tym samouczku są wymagane następujące elementy:

* Subskrypcja platformy Azure: Jeśli jeszcze jej nie masz, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Utwórz konto SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Konto usługi Automation](automation-offering-get-started.md) z poleceniem **AZ** modules i poleceniem [Run As](automation-create-runas-account.md)do przechowywania i wykonywania elementu Runbook.

## <a name="create-an-azure-keyvault"></a>Tworzenie magazynu kluczy platformy Azure

Magazyn kluczy platformy Azure można utworzyć przy użyciu następującego skryptu programu PowerShell. Zamień wartości zmiennej na wartości specyficzne dla danego środowiska. Użyj osadzonego Azure Cloud Shell za pomocą przycisku <kbd>Wypróbuj</kbd> , znajdującego się w prawym górnym rogu bloku kodu. Możesz również skopiować i uruchomić kod lokalnie, jeśli masz [moduł Azure PowerShell](/powershell/azure/install-az-ps) zainstalowany na komputerze lokalnym.

> [!NOTE]
> Aby pobrać klucz interfejsu API, wykonaj kroki opisane w sekcji [Znajdowanie klucza interfejsu API usługi SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

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

Aby uzyskać inne sposoby tworzenia magazynu kluczy platformy Azure i przechowywania wpisu tajnego, zobacz [Przewodnik Szybki Start](/azure/key-vault/)dotyczący magazynu kluczy.

## <a name="import-required-modules-to-your-automation-account"></a>Importowanie wymaganych modułów do konta usługi Automation

Aby można było korzystać z magazynu kluczy platformy Azure w ramach elementu Runbook, konto usługi Automation będzie wymagało następujących modułów:

* [AZ. profile](https://www.powershellgallery.com/packages/Az.Profile).
* W [magazynie](https://www.powershellgallery.com/packages/Az.KeyVault)kluczy.

Kliknij przycisk <kbd>Wdróż, aby Azure Automation</kbd> na karcie Azure Automation w obszarze Opcje instalacji. Ta akcja spowoduje otwarcie Azure Portal. Na stronie Importowanie wybierz konto usługi Automation i kliknij przycisk <kbd>OK</kbd>.

Aby uzyskać dodatkowe metody dodawania wymaganych modułów, zobacz [Importowanie modułów](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Tworzenie elementu Runbook w celu wysłania wiadomości e-mail

Po utworzeniu magazynu kluczy i zapisaniu klucza interfejsu API SendGrid należy utworzyć element Runbook, który pobierze klucz interfejsu API i wyśle wiadomość e-mail.

Ten element Runbook używa [konta Uruchom jako](automation-create-runas-account.md) AzureRunAsConnection do uwierzytelniania na platformie Azure w celu pobrania klucza tajnego z magazynu kluczy platformy Azure.

Użyj tego przykładu, aby utworzyć element Runbook o nazwie **send-GridMailMessage**. Możesz zmodyfikować skrypt programu PowerShell, a następnie użyć go ponownie w różnych scenariuszach.

1. Przejdź do swojego konta Azure Automation.
2. W obszarze **Automatyzacja procesów**wybierz pozycję **elementy Runbook**.
3. W górnej części listy elementów Runbook wybierz pozycję **+ Utwórz element Runbook**.
4. Na stronie **Dodawanie elementu Runbook** wprowadź polecenie **send-GridMailMessage** dla nazwy elementu Runbook. W polu Typ elementu Runbook wybierz pozycję **PowerShell**. Następnie wybierz przycisk **Create** (Utwórz).
   ![utworzyć elementu Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Element runbook zostanie utworzony i zostanie otworzona strona **Edytuj element runbook programu PowerShell**.
   ![edytować elementu Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Skopiuj poniższy przykład programu PowerShell do strony **Edycja** . Upewnij się, że `$VaultName` to nazwa określona podczas tworzenia magazynu kluczy.

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

7. Wybierz pozycję **Publikuj** , aby zapisać i opublikować element Runbook.

Aby sprawdzić, czy element Runbook został wykonany pomyślnie, możesz wykonać czynności opisane w sekcji [testowanie elementu Runbook](manage-runbooks.md#test-a-runbook) lub [Uruchamianie elementu Runbook](start-runbooks.md).
Jeśli nie widzisz początkowo testową pocztą e-mail, Sprawdź foldery **wiadomości-śmieci** i **spamu** .

## <a name="clean-up"></a>Oczyść

Gdy element runbook nie będzie już potrzebny, usuń go. Aby to zrobić, zaznacz element runbook na liście i kliknij pozycję **Usuń**.

Usuń Magazyn kluczy za pomocą polecenia cmdlet [Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps) .

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Następne kroki

* Problemy związane z tworzeniem lub uruchamianiem elementu Runbook można znaleźć w temacie [Rozwiązywanie problemów z elementami Runbook](./troubleshoot/runbooks.md).
* Aby zaktualizować moduły na koncie usługi Automation, zobacz [temat jak aktualizować moduły Azure PowerShell w Azure Automation](automation-update-azure-modules.md)].
* Aby monitorować wykonywanie elementu Runbook, zobacz temat [przekazywanie stanu zadań i strumieni zadań z automatyzacji do Azure monitor dzienników](automation-manage-send-joblogs-log-analytics.md).
* Aby wyzwolić element Runbook przy użyciu alertu, zobacz [Używanie alertu do wyzwalania elementu runbook Azure Automation](automation-create-alert-triggered-runbook.md).
