---
title: Zasobów certyfikatów w usłudze Azure Automation
description: Certyfikaty można bezpiecznie przechowywane w usłudze Azure Automation, dzięki czemu są one dostępne przez elementy runbook i konfiguracje DSC do uwierzytelniania względem platformy Azure i innych zasobów.  W tym artykule opisano szczegóły certyfikaty i sposób pracy z nimi w tworzeniu tekstową i graficznego.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a778fcc6c22fa7f2a88eb2591261e273a79ea28
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58442349"
---
# <a name="certificate-assets-in-azure-automation"></a>Zasobów certyfikatów w usłudze Azure Automation

Certyfikaty mogą być bezpiecznie przechowywane w usłudze Azure Automation, dzięki czemu są one dostępne przez elementy runbook lub konfiguracji DSC przy użyciu **Get AzureRmAutomationCertificate** działania dla zasobów usługi Azure Resource Manager. Ta funkcja służy do tworzenia elementów runbook i konfiguracji DSC, które używają certyfikatów do uwierzytelniania lub dodaje je do platformy Azure lub zasoby innych firm.

>[!NOTE]
>Bezpiecznych zasobów w usłudze Azure Automation obejmują poświadczeń, certyfikatów, połączeń i szyfrowane zmienne. Te zasoby są zaszyfrowane i przechowywane w usłudze Azure Automation za pomocą Unikatowy klucz, który jest generowany dla każdego konta usługi automation. Ten klucz jest przechowywany w systemie zarządzane usługi Key Vault. Przed zapisaniem zabezpieczonym zasobem, klucz jest ładowane z usługi Key Vault i następnie używany do szyfrowania elementu zawartości. Ten proces jest zarządzane przez usługę Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>Polecenia cmdlet usługi AzureRM PowerShell
Dla usługi AzureRM poleceń cmdlet w poniższej tabeli służą do tworzenia i obsługi zasobów poświadczeń usługi automation przy użyciu programu Windows PowerShell. Są dostarczane jako część systemu [modułu z wersjami AzureRM.Automation](/powershell/azure/overview) który jest dostępny do użycia w elementach runbook automatyzacji i konfiguracji DSC.

|Polecenia cmdlet|Opis|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Pobiera informacje o certyfikat do użycia w elemencie runbook lub konfiguracji DSC. Sam certyfikat można pobierać tylko z działania Get AutomationCertificate.| 
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Tworzy nowy certyfikat do usługi Azure Automation.| 
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Usuwa certyfikat z usługi Azure Automation.|
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Ustawia właściwości istniejącego certyfikatu, włącznie z przekazywaniem pliku certyfikatu i ustawianiem hasła dla pliku pfx.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Służy do przekazywania certyfikatu usługi dla określona usługa w chmurze.|


## <a name="activities"></a>Działania
Działania w poniższej tabeli są używane do dostępu do certyfikatów w elemencie runbook i konfiguracjach DSC.

| Działania | Opis |
|:---|:---|
|Get-AutomationCertificate|Pobiera certyfikat do użycia w elemencie runbook lub konfiguracji DSC. Zwraca [System.Security.Cryptography.X509Certificates.X509Certificate2](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate2.aspx) obiektu.|

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

1. Na koncie usługi Automation kliknij **zasoby** Kafelek, aby otworzyć **zasoby** bloku.
2. Kliknij przycisk **certyfikaty** Kafelek, aby otworzyć **certyfikaty** bloku.
3. Kliknij przycisk **Dodaj certyfikat** w górnej części bloku.
4. Wpisz nazwę certyfikatu w **nazwa** pole.
5. Aby przeglądać w poszukiwaniu pliku cer lub PFX, kliknij przycisk **wybierz plik** w obszarze **Przekaż plik certyfikatu**. Wybranie pliku PFX, należy określić hasło i czy jest dozwolone do wyeksportowania.
6. Kliknij przycisk **Utwórz** Aby zapisać nowy element zawartości certyfikat.

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Aby utworzyć nowy certyfikat za pomocą programu Windows PowerShell

Poniższy przykład pokazuje sposób tworzenia nowego certyfikatu usługi Automation i oznacz go jako możliwy do eksportu. Zaimportowane istniejącego pliku pfx.

```powershell-interactive
$certName = 'MyCertificate'
$certPath = '.\MyCert.pfx'
$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup
```

## <a name="using-a-certificate"></a>Za pomocą certyfikatu

Aby używać certyfikatu, należy użyć **Get AutomationCertificate** działania. Nie można użyć [Get AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate) polecenia cmdlet, ponieważ zwraca informacje o zasób certyfikatu, ale nie sam certyfikat.

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

Możesz dodać **Get AutomationCertificate** do graficznego elementu runbook, klikając prawym przyciskiem myszy certyfikat w okienku Biblioteka edytor graficzny i wybierając polecenie **Dodaj do kanwy**.

![Dodawanie certyfikatu do obszaru roboczego](media/automation-certificates/automation-certificate-add-to-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia certyfikatu w graficznego elementu runbook. To jest taka sama jak w poprzednim przykładzie Dodawanie certyfikatu do usługi w chmurze z tekstowej elementu runbook.

![Przykład tworzenie graficzne](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Przykładowe Python2
Poniższy przykład pokazuje sposób certyfikaty elementami runbook programu Python2 dostępu.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert 
```

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat pracy z łączami do sterowania przepływem logiczne element runbook jest przeznaczony do wykonywania działań, zobacz [łącza w tworzenie graficzne](automation-graphical-authoring-intro.md#links-and-workflow). 

