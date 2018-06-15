---
title: Zasoby certyfikatu usługi Automatyzacja Azure
description: Certyfikaty można bezpiecznie przechowywane w usłudze Automatyzacja Azure, są one dostępne przez elementy runbook lub konfiguracji DSC do uwierzytelniania Azure i innych zasobów.  W tym artykule szczegółowo opisano certyfikaty i sposobu pracy z nimi w tworzeniu zarówno tekstową i graficznego.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 70d5c777f98a1e09125c7ef42ba597009bde084b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194341"
---
# <a name="certificate-assets-in-azure-automation"></a>Zasoby certyfikatu usługi Automatyzacja Azure

Certyfikaty mogą być bezpiecznie przechowywane w automatyzacji Azure, są one dostępne przez elementy runbook lub przy użyciu konfiguracji DSC **Get AzureRmAutomationCertificate** działania dla zasobów usługi Azure Resource Manager. Ta funkcja służy do tworzenia elementów runbook i konfiguracji DSC, które korzystają z certyfikatów do uwierzytelniania i dodaje je do platformy Azure lub innych zasobów.

>[!NOTE]
>Bezpiecznych zasobów w automatyzacji Azure obejmują poświadczeń, certyfikatów, połączeń i szyfrowane zmienne. Te zasoby są szyfrowane i przechowywane w automatyzacji Azure za pomocą Unikatowy klucz, który jest generowany dla każdego konta automatyzacji. Ten klucz jest przechowywany w magazynie kluczy. Przed zapisaniem zabezpieczonym zasobem, klucz jest załadowany z magazynu kluczy i następnie używany do szyfrowania elementu zawartości.

## <a name="azurerm-powershell-cmdlets"></a>Polecenia cmdlet programu AzureRM PowerShell
Dla AzureRM poleceń cmdlet w poniższej tabeli służą do tworzenia i zarządzania zasobami poświadczenie automatyzacji przy użyciu programu Windows PowerShell. One dostarczane jako część [modułu AzureRM.Automation](/powershell/azure/overview) która jest dostępna na potrzeby automatyzacji elementów runbook i konfiguracji DSC.

|Polecenia cmdlet|Opis|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Pobiera informacje o certyfikat do użycia w element runbook lub konfiguracji DSC. Sam certyfikat można pobierać tylko z działania Get AutomationCertificate.|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Tworzy nowy certyfikat w automatyzacji Azure.|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Usuwa certyfikat usługi Automatyzacja Azure.|Tworzy nowy certyfikat w automatyzacji Azure.
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Ustawia właściwości istniejącego certyfikatu, włącznie z przekazywaniem pliku certyfikatu i ustawianiem hasła dla pliku pfx.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Przekazywanie certyfikatu usługi dla usługi określonej chmury.|

## <a name="activities"></a>Działania
Działania w poniższej tabeli umożliwiają dostęp certyfikaty w elemencie runbook i konfiguracji DSC.

| Działania | Opis |
|:---|:---|
|Get-AutomationCertificate|Pobiera certyfikat do użycia w element runbook lub konfiguracji DSC. Zwraca [System.Security.Cryptography.X509Certificates.X509Certificate2](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate2.aspx) obiektu.|

> [!NOTE] 
> Należy unikać używania zmiennych w parametrze-Name z **Get-AutomationCertificate** runbook lub konfiguracji DSC komplikuje wykrywanie zależności między elementami runbook lub konfiguracji DSC i zmienne automatyzacji w czasie projektowania.

## <a name="python2-functions"></a>Funkcje Python2

Uzyskano dostęp certyfikaty w elemencie runbook Python2 funkcji w poniższej tabeli.

| Funkcja | Opis |
|:---|:---|
| automationassets.get_automation_certificate | Pobiera informacje o zawartości certyfikatu. |

> [!NOTE]
> Należy zaimportować **automationassets** modułu na początku elementu runbook z języka Python, aby uzyskać dostęp do funkcji zasobów.

## <a name="creating-a-new-certificate"></a>Tworzenie nowego certyfikatu

Podczas tworzenia nowego certyfikatu, możesz przekazać plik cer lub PFX do automatyzacji Azure. Jeśli zostanie oznaczony jako możliwy do wyeksportowania certyfikatu, następnie można przenieść go z magazynu certyfikatów usługi Automatyzacja Azure. Jeśli nie jest możliwy do wyeksportowania, następnie można można używać tylko do podpisywania w ramach elementu runbook lub konfiguracji DSC. Automatyzacja Azure wymaga certyfikatu do dostawcy: **Microsoft Enhanced RSA and AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Aby utworzyć nowy certyfikat za pomocą portalu Azure

1. Twoje konto usługi Automatyzacja kliknij **zasoby** Kafelek, aby otworzyć **zasoby** bloku.
1. Kliknij przycisk **certyfikaty** Kafelek, aby otworzyć **certyfikaty** bloku.
1. Kliknij przycisk **Dodawanie certyfikatu** w górnej części bloku.
1. Wpisz nazwę certyfikatu w **nazwa** pole.
1. Aby wyszukać plik cer lub PFX, kliknij przycisk **wybierz plik** w obszarze **przekazać plik certyfikatu**. W przypadku wybrania pliku PFX, określ hasło oraz czy jest dozwolone do wyeksportowania.
1. Kliknij przycisk **Utwórz** do zapisywania nowego elementu zawartości certyfikatu.

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Aby utworzyć nowy certyfikat za pomocą środowiska Windows PowerShell

W poniższym przykładzie pokazano sposób tworzenia nowego certyfikatu usługi Automatyzacja i oznacz ją jako eksportowalny. To importuje istniejący plik pfx.

```powershell-interactive
$certName = 'MyCertificate'
$certPath = '.\MyCert.pfx'
$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup
```

## <a name="using-a-certificate"></a>Przy użyciu certyfikatu

Aby użyć certyfikatu, użyj **Get AutomationCertificate** działania. Nie można użyć [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx) polecenia cmdlet, ponieważ zwraca informacje o zasób certyfikatu, ale nie sam certyfikat.

### <a name="textual-runbook-sample"></a>Przykład tekstowy

Następujący przykładowy kod przedstawia sposób dodawania certyfikatu do usługi w chmurze w elemencie runbook. W tym przykładzie hasło jest pobierana z zmiennej automatyzacji zaszyfrowane.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Przykładowe graficznym elementem runbook

Możesz dodać **Get-AutomationCertificate** na graficzny element runbook prawym przyciskiem myszy certyfikat w okienku Biblioteka edytora graficznego i wybierając **Dodaj do kanwy**.

![Dodawanie certyfikatu do obszaru roboczego](media/automation-certificates/automation-certificate-add-to-canvas.png)

Na poniższej ilustracji przedstawiono przykład za pomocą certyfikatu w graficznym elementem runbook. Jest to ten sam, jak w poprzednim przykładzie dla Dodawanie certyfikatu usługi w chmurze z tekstowy.

![Przykład tworzenia graficznego ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Przykładowe Python2
Poniższy przykład przedstawia sposób uzyskać dostępu do certyfikatów w elementach runbook Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert 
```

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o pracy z łącza w celu kontroli przepływu logicznego wynikającego z elementem runbook służy do wykonywania działań, zobacz [łącza w tworzeniu graficznego](automation-graphical-authoring-intro.md#links-and-workflow). 
