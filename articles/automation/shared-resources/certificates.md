---
title: Zasoby certyfikatów w Azure Automation
description: Certyfikaty są bezpiecznie w Azure Automation, dzięki czemu można uzyskiwać dostęp do nich za pomocą elementów Runbook lub konfiguracji DSC w celu uwierzytelniania względem zasobów platformy Azure i innych firm.  W tym artykule opisano szczegóły certyfikatów i sposób pracy z nimi zarówno w tworzeniu tekstu, jak i w formie graficznej.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a66f73e028594cf90f1fa1765910a3df3adbad1a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849483"
---
# <a name="certificate-assets-in-azure-automation"></a>Zasoby certyfikatów w Azure Automation

Certyfikaty są bezpiecznie przechowywane w Azure Automation, dzięki czemu można uzyskać do nich dostęp za pomocą elementów Runbook lub konfiguracji DSC przy użyciu działania **Get-AzureRmAutomationCertificate** dla Azure Resource Manager zasobów. Ta funkcja umożliwia tworzenie elementów Runbook i konfiguracji DSC, które używają certyfikatów do uwierzytelniania, lub dodaje je do platformy Azure lub zasobów innych firm.

>[!NOTE]
>Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zmienne zaszyfrowane. Te zasoby są szyfrowane i przechowywane w Azure Automation przy użyciu unikatowego klucza wygenerowanego dla każdego konta usługi Automation. Ten klucz jest przechowywany w Key Vault zarządzanym przez system. Przed zapisaniem bezpiecznego elementu zawartości klucz jest ładowany z Key Vault a następnie używany do szyfrowania elementu zawartości. Ten proces jest zarządzany przez Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell AzureRM

W przypadku AzureRM polecenia cmdlet w poniższej tabeli służą do tworzenia zasobów poświadczeń usługi Automation i zarządzania nimi za pomocą programu Windows PowerShell. Są one dostarczane jako część [modułu AzureRM. Automation](/powershell/azure/overview), który jest dostępny do użycia w elementach Runbook usługi Automation i konfiguracjach DSC.

|Polecenia cmdlet|Opis|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Pobiera informacje o certyfikacie do użycia w elemencie Runbook lub konfiguracji DSC. Sam certyfikat można pobrać tylko z działania Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Tworzy nowy certyfikat do Azure Automation.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Usuwa certyfikat z Azure Automation.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Ustawia właściwości istniejącego certyfikatu, włącznie z przekazywaniem pliku certyfikatu i ustawieniem hasła dla PFX.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Przekazuje certyfikat usługi dla określonej usługi w chmurze.|

## <a name="activities"></a>Działania

Działania w poniższej tabeli służą do uzyskiwania dostępu do certyfikatów w konfiguracjach elementów Runbook i DSC.

| Działania | Opis |
|:---|:---|
|Get-AutomationCertificate|Pobiera certyfikat do użycia w konfiguracji elementu Runbook lub DSC. Zwraca obiekt [System. Security. Cryptography. x509. X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) .|

> [!NOTE] 
> Należy unikać używania zmiennych w parametrze-Name polecenia **Get-AutomationCertificate** w elemencie Runbook lub konfiguracji DSC, ponieważ powoduje to komplikację odnajdywania zależności między elementami Runbook i konfiguracją DSC oraz zmiennymi automatyzacji w czasie projektowania.

## <a name="python2-functions"></a>Funkcje python2

Funkcja w poniższej tabeli służy do uzyskiwania dostępu do certyfikatów w elemencie Runbook python2.

| Funkcja | Opis |
|:---|:---|
| automationassets.get_automation_certificate | Pobiera informacje o zasobach certyfikatu. |

> [!NOTE]
> Aby uzyskać dostęp do funkcji zasobów, należy zaimportować moduł **automationassets** na początku elementu Runbook języka Python.

## <a name="creating-a-new-certificate"></a>Tworzenie nowego certyfikatu

Podczas tworzenia nowego certyfikatu należy przekazać plik cer lub PFX do Azure Automation. Jeśli oznaczesz certyfikat jako eksportowalny, możesz go przenieść z magazynu certyfikatów Azure Automation. Jeśli nie można go eksportować, może być używany tylko do podpisywania w ramach elementu Runbook lub konfiguracji DSC. Azure Automation wymaga, aby certyfikat miał dostawcę: **Zaawansowany dostawca usług kryptograficznych RSA i AES firmy Microsoft**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Aby utworzyć nowy certyfikat z Azure Portal

1. Na koncie usługi Automation kliknij kafelek **elementy zawartości** , aby otworzyć stronę **zasoby** .
2. Kliknij kafelek **Certyfikaty** , aby otworzyć stronę **Certyfikaty** .
3. Kliknij pozycję **Dodaj certyfikat** w górnej części strony.
4. Wpisz nazwę certyfikatu w **nazwa** pole.
5. Aby wyszukać plik cer lub PFX, kliknij pozycję **Wybierz plik** w obszarze **Przekaż plik certyfikatu**. W przypadku wybrania pliku PFX należy określić hasło i zdecydować, czy można je wyeksportować.
6. Kliknij przycisk **Utwórz** , aby zapisać nowy zasób certyfikatu.

### <a name="to-create-a-new-certificate-with-powershell"></a>Aby utworzyć nowy certyfikat przy użyciu programu PowerShell

W poniższym przykładzie pokazano, jak utworzyć nowy certyfikat automatyzacji i oznaczyć go jako możliwy do eksportu. Spowoduje to zaimportowanie istniejącego pliku PFX.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Utwórz nowy certyfikat przy użyciu szablonu Menedżer zasobów

W poniższym przykładzie pokazano, jak wdrożyć certyfikat na koncie usługi Automation przy użyciu szablonu Menedżer zasobów za pomocą programu PowerShell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Korzystanie z certyfikatu

Aby użyć certyfikatu, Użyj działania **Get-AutomationCertificate** . Nie można użyć polecenia cmdlet [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) , ponieważ zwraca informacje o zasobie certyfikatu, ale nie sam certyfikat.

### <a name="textual-runbook-sample"></a>Przykład tekstu elementu Runbook

Poniższy przykładowy kod pokazuje, jak dodać certyfikat do usługi w chmurze w elemencie Runbook. W tym przykładzie hasło jest pobierane z zaszyfrowanej zmiennej automatyzacji.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Przykład graficzny elementu Runbook

Aby dodać element **Get-AutomationCertificate** do graficznego elementu Runbook, kliknij prawym przyciskiem myszy certyfikat w okienku biblioteka i wybierz polecenie **Dodaj do kanwy**.

![Dodawanie certyfikatu do kanwy](../media/certificates/automation-certificate-add-to-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia certyfikatu w graficznym elemencie Runbook. Jest to takie samo jak w poprzednim przykładzie, który pokazuje, jak dodać certyfikat do usługi w chmurze z tekstowego elementu Runbook.

![Przykład tworzenia graficznego](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Przykład python2

Poniższy przykład pokazuje, jak uzyskać dostęp do certyfikatów w elementach Runbook python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat pracy z linkami w celu kontrolowania logicznego przepływu działań, które są przeznaczone do wykonania przez element Runbook, zobacz [linki w temacie Tworzenie graficzne](../automation-graphical-authoring-intro.md#links-and-workflow). 
