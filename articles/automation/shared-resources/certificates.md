---
title: Zasoby certyfikatów w usłudze Azure Automation
description: Certyfikaty są bezpiecznie w usłudze Azure Automation, dzięki czemu są dostępne dla liczeni lub konfiguracji DSC do uwierzytelniania na platformie Azure i zasobów innych firm.  W tym artykule opisano szczegóły dotyczące certyfikatów i sposobu pracy z nimi zarówno w tekście, jak i w formie graficznej.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a66f73e028594cf90f1fa1765910a3df3adbad1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849483"
---
# <a name="certificate-assets-in-azure-automation"></a>Zasoby certyfikatów w usłudze Azure Automation

Certyfikaty są bezpiecznie przechowywane w usłudze Azure Automation, dzięki czemu są dostępne dla liczeni lub konfiguracji DSC przy użyciu działania **Get-AzureRmAutomationCertificate** dla zasobów usługi Azure Resource Manager. Ta funkcja umożliwia tworzenie śmięty uruchomieniu i konfiguracje DSC, które używają certyfikatów do uwierzytelniania lub dodaje je do platformy Azure lub zasobów innych firm.

>[!NOTE]
>Bezpieczne zasoby w usłudze Azure Automation obejmują poświadczenia, certyfikaty, połączenia i zaszyfrowane zmienne. Te zasoby są szyfrowane i przechowywane w usłudze Azure Automation przy użyciu unikatowego klucza, który jest generowany dla każdego konta automatyzacji. Ten klucz jest przechowywany w zarządzanym przez system magazynie kluczy. Przed zapisaniem bezpiecznego zasobu klucz jest ładowany z usługi Key Vault, a następnie używany do szyfrowania zasobu. Ten proces jest zarządzany przez usługę Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>Polecenia cmdlet programu AzureRM programu PowerShell

W przypadku usługi AzureRM polecenia cmdlet w poniższej tabeli są używane do tworzenia zasobów poświadczeń automatyzacji i zarządzania nimi za pomocą programu Windows PowerShell. Są one dostarczane jako część [modułu AzureRM.Automation](/powershell/azure/overview), który jest dostępny do użycia w systemach runbook automatyzacji i konfiguracjach DSC.

|Polecenia cmdlet|Opis|
|:---|:---|
|[Certyfikat Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Pobiera informacje o certyfikacie do użycia w konfiguracji żyli lub dsc. Sam certyfikat można pobrać tylko z działania Get-AutomationCertificate.|
|[Nowa autoryzacja AzureRmAutomationCertytutat](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Tworzy nowy certyfikat w usłudze Azure Automation.|
[Certyfikat Usuń-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Usuwa certyfikat z usługi Azure Automation.|
|[Certyfikat Certyfikat CertyfikatUj certyfikat Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Ustawia właściwości istniejącego certyfikatu, w tym przekazywanie pliku certyfikatu i ustawianie hasła dla .pfx.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Przekazuje certyfikat usługi dla określonej usługi w chmurze.|

## <a name="activities"></a>Działania

Działania w poniższej tabeli są używane do uzyskiwania dostępu do certyfikatów w konfiguracjach elementów runbook i DSC.

| Działania | Opis |
|:---|:---|
|Get-AutomationCertificate|Pobiera certyfikat do użycia w konfiguracji uruchomieniu lub DSC. Zwraca obiekt [System.Security.Cryptography.X509Certificates.X509Certificate2.](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)|

> [!NOTE] 
> Należy unikać używania zmiennych w parametrze –Name **get-AutomationCertificate** w konfiguracji likwidacyjnej lub DSC, ponieważ komplikuje odnajdowanie zależności między elementami runbook lub konfiguracją DSC i zmiennymi automatyzacji w czasie projektowania.

## <a name="python2-functions"></a>Funkcje Python2

Funkcja w poniższej tabeli służy do uzyskiwania dostępu do certyfikatów w uruchomieniu elementu runbook Języka Python2.

| Funkcja | Opis |
|:---|:---|
| automationassets.get_automation_certificate | Pobiera informacje o zasobie certyfikatu. |

> [!NOTE]
> Aby uzyskać dostęp do funkcji zasobu, należy zaimportować moduł **automationassets** na początku elementów runbook języka Python.

## <a name="creating-a-new-certificate"></a>Tworzenie nowego certyfikatu

Podczas tworzenia nowego certyfikatu należy przekazać plik .cer lub .pfx do usługi Azure Automation. Jeśli oznaczysz certyfikat jako eksportowalny, możesz przenieść go z magazynu certyfikatów usługi Azure Automation. Jeśli nie można go wyeksportować, można go używać tylko do podpisywania w ramach konfiguracji systemu runbook lub DSC. Usługa Azure Automation wymaga, aby certyfikat miał dostawcę: **Microsoft Enhanced RSA i AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Aby utworzyć nowy certyfikat za pomocą witryny Azure Portal

1. Na koncie automatyzacji kliknij kafelek **Zasoby,** aby otworzyć stronę **Zasoby.**
2. Kliknij kafelek **Certyfikaty,** aby otworzyć stronę **Certyfikaty.**
3. Kliknij **pozycję Dodaj certyfikat** u góry strony.
4. Wpisz nazwę certyfikatu w polu **Nazwa.**
5. Aby wyszukać plik cer lub pfx, kliknij pozycję **Wybierz plik w** obszarze Przekaż plik **certyfikatu**. Jeśli wybierzesz plik .pfx, określ hasło i czy można je wyeksportować.
6. Kliknij **przycisk Utwórz,** aby zapisać nowy zasób certyfikatu.

### <a name="to-create-a-new-certificate-with-powershell"></a>Aby utworzyć nowy certyfikat za pomocą programu PowerShell

W poniższym przykładzie pokazano, jak utworzyć nowy certyfikat automatyzacji i oznaczyć go do eksportu. Spowoduje to zaimportować istniejący plik pfx.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Tworzenie nowego certyfikatu z szablonem Menedżera zasobów

W poniższym przykładzie pokazano, jak wdrożyć certyfikat na koncie automatyzacji przy użyciu szablonu Menedżera zasobów za pośrednictwem programu PowerShell:

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

Aby użyć certyfikatu, należy użyć **get-AutomationCertificate** działania. Nie można użyć polecenia cmdlet [Get-AzureRmAutomationCertificate,](/powershell/module/azurerm.automation/get-azurermautomationcertificate) ponieważ zwraca informacje o zasobie certyfikatu, ale nie o samym certyfikacie.

### <a name="textual-runbook-sample"></a>Przykładowy tekstowy podręcznik

Poniższy przykładowy kod pokazuje, jak dodać certyfikat do usługi w chmurze w elementów runbook. W tym przykładzie hasło jest pobierane z zaszyfrowanej zmiennej automatyzacji.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Przykład graficznego podręcznika runbooka

Dodaj **certyfikat Get-AutomationCertificate** do graficznego podręcznika runbooka, klikając prawym przyciskiem myszy certyfikat w okienku Biblioteka i wybierając pozycję **Dodaj do kanwy**.

![Dodawanie certyfikatu do kanwy](../media/certificates/automation-certificate-add-to-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia certyfikatu w graficznym uruchomieniu bie. Jest to taka sama jak w poprzednim przykładzie, który pokazuje, jak dodać certyfikat do usługi w chmurze z podręcznika podstawowego.

![Przykładowe tworzenie graficzne](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Przykład Python2

W poniższym przykładzie pokazano, jak uzyskać dostęp do certyfikatów w podręcznikach runbook języka Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o pracy z łączami w celu kontrolowania logicznego przepływu działań, do wykonania którego jest przeznaczony element runbook, zobacz [Łącza w tworzenia graficznym](../automation-graphical-authoring-intro.md#links-and-workflow). 
