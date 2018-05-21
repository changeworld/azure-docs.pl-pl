---
title: Trwałe połączenie z usługi Automatyzacja Azure
description: Trwałe połączenie z usługi Automatyzacja Azure zawiera informacje wymagane do połączenia z zewnętrznej usługi lub aplikacji z elementu runbook lub konfiguracji DSC. W tym artykule szczegółowo opisano połączenia i jak pracować z nimi w tworzeniu zarówno tekstową i graficznego.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ee866248ae7f0c1f1c49c449b777c2b68d884c5b
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="connection-assets-in-azure-automation"></a>Trwałe połączenie z usługi Automatyzacja Azure

Zasób połączenia usługi Automatyzacja zawiera informacje wymagane do połączenia z zewnętrznej usługi lub aplikacji z elementu runbook lub konfiguracji DSC. Może to obejmować informacje wymagane do uwierzytelniania, takie jak nazwa użytkownika i hasło, oprócz informacji o połączeniu, takie jak adres URL lub portu. Wartość połączenia jest przechowywanie wszystkich właściwości podłączania do konkretnej aplikacji w jeden zasób zamiast tworzenia wiele zmiennych. Użytkownik może edytować wartości połączenia w jednym miejscu, a nazwa połączenia można przekazać do elementu runbook lub konfiguracji DSC w jeden parametr. Właściwości połączenia można uzyskać w element runbook lub Konfiguracja DSC o **Get AutomationConnection** działania. 

Podczas tworzenia połączenia, należy określić *typ połączenia*. Typ połączenia to szablon, który definiuje zestaw właściwości. Połączenie definiuje wartości dla każdej właściwości w jego typie połączenia. Typy połączeń są dodawane do usługi Automatyzacja Azure w modułach integracji lub utworzone za pomocą [interfejsu API usługi Automatyzacja Azure](http://msdn.microsoft.com/library/azure/mt163818.aspx) Jeśli modułu integracji zawiera typ połączenia i jest importowany do Twojego konta automatyzacji. W przeciwnym razie należy utworzyć plik metadanych, aby określić typ połączenia usługi Automatyzacja.  Aby uzyskać dodatkowe informacje dotyczące tego, zobacz [moduły integracji](automation-integration-modules.md).  

>[!NOTE]
>Bezpiecznych zasobów w automatyzacji Azure obejmują poświadczeń, certyfikatów, połączeń i szyfrowane zmienne. Te zasoby są szyfrowane i przechowywane w automatyzacji Azure za pomocą Unikatowy klucz, który jest generowany dla każdego konta automatyzacji. Ten klucz jest przechowywany w magazynie kluczy. Przed zapisaniem zabezpieczonym zasobem, klucz jest załadowany z magazynu kluczy i następnie używany do szyfrowania elementu zawartości.

## <a name="windows-powershell-cmdlets"></a>Polecenia cmdlet programu Windows PowerShell

Polecenia cmdlet w poniższej tabeli służą do tworzenia i zarządzania połączeniami automatyzacji za pomocą środowiska Windows PowerShell. One dostarczane jako część [modułu Azure PowerShell](/powershell/azure/overview) która jest dostępna na potrzeby automatyzacji elementów runbook i konfiguracji DSC.

|Polecenie cmdlet|Opis|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Pobiera połączenie. Zawiera tabelę wyznaczania wartości skrótu z wartościami pól połączeń.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Tworzy nowe połączenie.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Usuń istniejące połączenie.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Ustawia wartość określonego pola dla istniejącego połączenia.|

## <a name="activities"></a>Działania

Działania w poniższej tabeli umożliwiają dostęp do połączeń w element runbook lub konfiguracji DSC.

|Działania|Opis|
|---|---|
|[Get-AutomationConnection](/powershell/module/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Pobiera połączenie do użycia. Zwraca tabelę wyznaczania wartości skrótu z właściwości połączenia.|

>[!NOTE] 
>Należy unikać używania zmiennych z parametrem nazwa **Get - AutomationConnection** ponieważ może to skomplikować wykrywanie zależności między elementami runbook lub konfiguracji DSC i zasoby połączenia w czasie projektowania.

 
## <a name="python2-functions"></a>Funkcje Python2 
W poniższej tabeli funkcji służy do połączenia w elemencie runbook Python2 dostępu. 

| Funkcja | Opis | 
|:---|:---| 
| automationassets.get_automation_connection | Pobiera połączenie. Zwraca słownik z właściwości połączenia. | 

> [!NOTE] 
> Aby uzyskać dostęp do funkcji zasobów, należy zaimportować modułu "automationassets" w górnej części elementu runbook języka Python.

## <a name="creating-a-new-connection"></a>Tworzenie nowego połączenia

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Aby utworzyć nowe połączenie za pomocą portalu Azure

1. Twoje konto usługi Automatyzacja kliknij **zasoby** , aby otworzyć **zasoby** bloku.
2. Kliknij przycisk **połączeń** , aby otworzyć **połączeń** bloku.
3. Kliknij przycisk **dodać połączenie** w górnej części bloku.
4. W **typu** listy rozwijanej wybierz typ połączenia, którego chcesz utworzyć. Formularz przedstawia właściwości dla tego typu.
5. Wypełnij formularz, a następnie kliknij przycisk **Utwórz** można zapisać nowego połączenia.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Aby utworzyć nowe połączenie za pomocą środowiska Windows PowerShell

Utwórz nowe połączenie przy użyciu programu Windows PowerShell [AzureRmAutomationConnection nowy](/powershell/module/azurerm.automation/new-azurermautomationconnection) polecenia cmdlet. To polecenie cmdlet ma parametr o nazwie **ConnectionFieldValues** który oczekuje [tablicy skrótów](http://technet.microsoft.com/library/hh847780.aspx) definiowanie wartości dla każdej właściwości zdefiniowane przez typ połączenia.

Jeśli znasz automatyzację [konta Uruchom jako](automation-sec-configure-azure-runas-account.md) do uwierzytelniania przy użyciu nazwy głównej usługi elementów runbook, skrypt programu PowerShell dostarczane jako alternatywę dla tworzenia konta Uruchom jako z portalu, tworzy nowe połączenie zasobów przy użyciu następujących przykładowych poleceniach.  

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues 
```

Można użyć skryptu, aby utworzyć zasób połączenia, ponieważ podczas tworzenia konta automatyzacji automatycznie zawiera kilka modułów globalnych domyślnie wraz z typu połączenia **AzurServicePrincipal** do Utwórz **AzureRunAsConnection** trwałego połączenia.  Jest to ważne, należy wziąć pod uwagę, ponieważ Jeśli próbujesz utworzyć nowy zasób połączenia, aby nawiązać połączenie z usługą lub aplikacją z innej metody uwierzytelniania, go zakończy się niepowodzeniem, ponieważ typ połączenia nie jest już zdefiniowany na Twoim koncie automatyzacji.  Aby uzyskać więcej informacji na temat tworzenia własnych typ połączenia dla niestandardowych lub modułu na podstawie [galerii programu PowerShell](https://www.powershellgallery.com), zobacz [moduły integracji](automation-integration-modules.md)
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Za pomocą połączenia w element runbook lub konfiguracji DSC

Pobieranie połączenia elementu runbook lub Konfiguracja DSC o **Get-AutomationConnection** polecenia cmdlet.  Nie można użyć [Get AzureRmAutomationConnection](https://docs.microsoft.com/powershell/resourcemanager/azurerm.automation/v1.0.12/Get-AzureRmAutomationConnection?redirectedfrom=msdn) działania.  To działanie pobiera wartości różnych pól w połączeniu i zwraca je jako [tablicy skrótów](http://go.microsoft.com/fwlink/?LinkID=324844) której następnie można użyć za pomocą odpowiednich poleceń w konfiguracji DSC lub elementu runbook.

### <a name="textual-runbook-sample"></a>Przykład tekstowy

W następujących przykładowych poleceniach pokazano, jak używać konta Uruchom jako wspomniano wcześniej, do uwierzytelniania za pomocą usługi Azure Resource Manager zasobów w elemencie runbook.  Zasób połączenia reprezentującą konta Uruchom jako, który odwołuje się do nazwy głównej usługi oparte na certyfikatach, poświadczenia nie jest używany.  

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection 
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

### <a name="graphical-runbook-samples"></a>Przykłady graficznym elementem runbook

Możesz dodać **Get-AutomationConnection** działanie graficzny element runbook prawym przyciskiem myszy połączenie w okienku Biblioteka edytora graficznego i wybierając **Dodaj do kanwy**.

![](media/automation-connections/connection-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład za pomocą połączenia w graficznym elementem runbook.  To jest tym samym przykładzie pokazano powyżej w celu uwierzytelniania przy użyciu konta Uruchom jako z tekstowy.  W tym przykładzie użyto **wartości stałej** zestaw danych na potrzeby **uzyskać połączenia RunAs** działania do uwierzytelniania używa obiektu połączenia.  A [linku potoku](automation-graphical-authoring-intro.md#links-and-workflow) jest tu używany, ponieważ zestaw parametrów ServicePrincipalCertificate oczekuje pojedynczego obiektu.

![](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Przykładowy element runbook Python2
Poniższy przykład przedstawia sposób uwierzytelniania przy użyciu połączenia Uruchom jako w elemencie runbook Python2.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
  """ Returns credentials to authenticate against Azure resoruce manager """
  from OpenSSL import crypto
  from msrestazure import azure_active_directory
  import adal

  # Get the Azure Automation Run As service principal certificate
  cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
  pks12_cert = crypto.load_pkcs12(cert)
  pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

  # Get Run As connection information for the Azure Automation service principal
  application_id = runas_connection["ApplicationId"]
  thumbprint = runas_connection["CertificateThumbprint"]
  tenant_id = runas_connection["TenantId"]

  # Authenticate with service principal certificate
  resource = "https://management.core.windows.net/"
  authority_url = ("https://login.microsoftonline.com/" + tenant_id)
  context = adal.AuthenticationContext(authority_url)
  return azure_active_directory.AdalAuthentication(
    lambda: context.acquire_token_with_client_certificate(
      resource,
      application_id,
      pem_pkey,
      thumbprint)
  )

# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>Kolejne kroki

- Przegląd [łącza w tworzeniu graficznego](automation-graphical-authoring-intro.md#links-and-workflow) sposób bezpośredni i sterowanie przepływem logikę w elementach runbook.  

- Aby dowiedzieć się więcej na temat automatyzacji Azure użycie moduły programu PowerShell i najlepsze rozwiązania do tworzenia własnych modułów programu PowerShell do pracy jako moduły integracji w automatyzacji Azure, zobacz [moduły integracji](automation-integration-modules.md).  
