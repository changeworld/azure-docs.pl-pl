---
title: Zasobów certyfikatów w usłudze Azure Automation
description: Certyfikaty są bezpieczne w usłudze Azure Automation, dzięki czemu są one dostępne przez elementy runbook i konfiguracje DSC do uwierzytelniania względem platformy Azure i innych zasobów.  W tym artykule opisano szczegóły certyfikaty i sposób pracy z nimi w tworzeniu tekstową i graficznego.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: da116dd8c84e4aa96cc3254218f1ab5d14a8bd6b
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478190"
---
# <a name="certificate-assets-in-azure-automation"></a>Zasobów certyfikatów w usłudze Azure Automation

Certyfikaty są bezpiecznie przechowywane w usłudze Azure Automation, dzięki czemu są one dostępne przez elementy runbook lub konfiguracji DSC przy użyciu **Get AzureRmAutomationCertificate** działania dla zasobów usługi Azure Resource Manager. Ta funkcja służy do tworzenia elementów runbook i konfiguracji DSC, które używają certyfikatów do uwierzytelniania lub dodaje je do platformy Azure lub zasoby innych firm.

>[!NOTE]
>Bezpiecznych zasobów w usłudze Azure Automation obejmują poświadczeń, certyfikatów, połączeń i szyfrowane zmienne. Te zasoby są zaszyfrowane i przechowywane w usłudze Azure Automation za pomocą Unikatowy klucz, który jest generowany dla każdego konta usługi automation. Ten klucz jest przechowywany w systemie zarządzane usługi Key Vault. Przed zapisaniem zabezpieczonym zasobem, klucz jest ładowane z usługi Key Vault i następnie używany do szyfrowania elementu zawartości. Ten proces jest zarządzane przez usługę Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>Polecenia cmdlet usługi AzureRM PowerShell

Dla usługi AzureRM poleceń cmdlet w poniższej tabeli służą do tworzenia i obsługi zasobów poświadczeń usługi automation przy użyciu programu Windows PowerShell. Są dostarczane jako część systemu [modułu z wersjami AzureRM.Automation](/powershell/azure/overview), który jest dostępny do użycia w elementach runbook automatyzacji i konfiguracji DSC.

|Polecenia cmdlet|Opis|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Pobiera informacje o certyfikat do użycia w elemencie runbook lub konfiguracji DSC. Sam certyfikat można pobierać tylko z działania Get AutomationCertificate.|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Tworzy nowy certyfikat do usługi Azure Automation.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Usuwa certyfikat z usługi Azure Automation.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Ustawia właściwości istniejącego certyfikatu, włącznie z przekazywaniem pliku certyfikatu i ustawianiem hasła dla pliku pfx.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Służy do przekazywania certyfikatu usługi dla określona usługa w chmurze.|

## <a name="activities"></a>Działania

Działania w poniższej tabeli są używane do dostępu do certyfikatów w elemencie runbook i konfiguracjach DSC.

| Działania | Opis |
|:---|:---|
|Get-AutomationCertificate|Pobiera certyfikat do użycia w elemencie runbook lub konfiguracji DSC. Zwraca [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) obiektu.|

> [!NOTE] 
> Należy unikać używania zmiennych w parametrze Name **Get AutomationCertificate** w elemencie runbook lub konfiguracji DSC w postaci, w jakiej komplikuje wykrywanie zależności między elementami runbook lub konfiguracji DSC i zmienne automatyzacji w czasie projektowania.

## <a name="python2-functions"></a>Funkcje Python2

Funkcja w poniższej tabeli umożliwia dostęp certyfikaty na element runbook programu Python2.

| Funkcja | Opis |
|:---|:---|
| automationassets.get_automation_certificate | Pobiera informacje o zasób certyfikatu. |

> [!NOTE]
> Należy zaimportować **automationassets** modułu na początku elementu runbook języka Python w celu uzyskania dostępu do zasobów funkcji.

## <a name="creating-a-new-certificate"></a>Tworzenie nowego certyfikatu

Podczas tworzenia nowego certyfikatu, możesz przekazać plik cer lub PFX do usługi Azure Automation. Po oznaczeniu certyfikatu jako eksportowalny dopiero później przenieść je z magazynu certyfikatów w usłudze Azure Automation. Jeśli nie jest eksportowalny, następnie go należy używać tylko do podpisywania w ramach elementu runbook lub konfiguracji DSC. Usługa Azure Automation wymaga certyfikatu, aby dostawca: **Microsoft Enhanced RSA and AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Aby utworzyć nowy certyfikat za pomocą witryny Azure portal

1. Na koncie usługi Automation kliknij **zasoby** Kafelek, aby otworzyć **zasoby** strony.
2. Kliknij przycisk **certyfikaty** Kafelek, aby otworzyć **certyfikaty** strony.
3. Kliknij przycisk **Dodaj certyfikat** w górnej części strony.
4. Wpisz nazwę certyfikatu w **nazwa** pole.
5. Aby przeglądać w poszukiwaniu pliku cer lub PFX, kliknij przycisk **wybierz plik** w obszarze **Przekaż plik certyfikatu**. Wybranie pliku PFX, należy określić hasło i czy mogą być eksportowane.
6. Kliknij przycisk **Utwórz** Aby zapisać nowy element zawartości certyfikat.

### <a name="to-create-a-new-certificate-with-powershell"></a>Aby utworzyć nowy certyfikat za pomocą programu PowerShell

Poniższy przykład pokazuje sposób tworzenia nowego certyfikatu usługi Automation i oznacz go jako możliwy do eksportu. Zaimportowane istniejącego pliku pfx.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Utwórz nowy certyfikat za pomocą szablonu usługi Resource Manager

Poniższy przykład pokazuje, jak wdrożyć certyfikat do konta usługi Automation przy użyciu szablonu usługi Resource Manager za pomocą programu PowerShell:

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

## <a name="using-a-certificate"></a>Za pomocą certyfikatu

Aby używać certyfikatu, należy użyć **Get AutomationCertificate** działania. Nie można użyć [Get AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) polecenia cmdlet, ponieważ zwraca informacje o zasób certyfikatu, ale nie sam certyfikat.

### <a name="textual-runbook-sample"></a>Przykład tekstowy element runbook

Poniższy przykład kodu pokazuje, jak dodać certyfikat do usługi w chmurze w elemencie runbook. W tym przykładzie hasło zostanie pobrana z zmiennej automatyzacji zaszyfrowane.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Przykładowy graficzny element runbook

Możesz dodać **Get AutomationCertificate** do graficznego elementu runbook, klikając prawym przyciskiem myszy certyfikat w okienku Biblioteka i wybierając polecenie **Dodaj do kanwy**.

![Dodawanie certyfikatu do obszaru roboczego](../media/certificates/automation-certificate-add-to-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia certyfikatu w graficznego elementu runbook. To jest taka sama, jak w poprzednim przykładzie, który pokazuje, jak dodać certyfikat do usługi w chmurze z tekstowej elementu runbook.

![Przykład tworzenie graficzne](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Przykładowe Python2

Poniższy przykład pokazuje sposób certyfikaty elementami runbook programu Python2 dostępu.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat pracy z łączami do sterowania przepływem logiczne element runbook jest przeznaczony do wykonywania działań, zobacz [łącza w tworzenie graficzne](../automation-graphical-authoring-intro.md#links-and-workflow). 
