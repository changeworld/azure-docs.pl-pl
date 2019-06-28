---
title: Elementy zawartości połączenia w usłudze Azure Automation
description: Elementy zawartości połączenia w usłudze Azure Automation zawiera informacje wymagane do połączenia z zewnętrzną usługą lub aplikacją z elementu runbook lub konfiguracji DSC. W tym artykule opisano szczegóły połączenia i sposób pracy z nimi w tworzeniu tekstową i graficznego.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6f0ebd45f9b2a837813c53cceb9423a5ed81c352
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340357"
---
# <a name="connection-assets-in-azure-automation"></a>Elementy zawartości połączenia w usłudze Azure Automation

Zasób połączenia usługi Automation zawiera informacje wymagane do połączenia z zewnętrzną usługą lub aplikacją z elementu runbook lub konfiguracji DSC. Może to obejmować informacje wymagane do uwierzytelniania, takiego jak nazwę użytkownika i hasło, oprócz informacji o połączeniu, takich jak adres URL lub port. Wartość połączenia jest zachowanie wszystkich właściwości do łączenia się z określonej aplikacji w jeden zasób, w przeciwieństwie do wielu zmiennych. Użytkownik może edytować wartości połączenia w jednym miejscu, a nazwa połączenia można przekazać do elementu runbook lub konfiguracji DSC w jeden parametr. Właściwości połączenia można uzyskać w element runbook lub konfiguracji DSC przy użyciu **Get AutomationConnection** działania. 

Podczas tworzenia połączenia należy określić *typu połączenia*. Typ połączenia to szablon, który definiuje zestaw właściwości. Połączenie definiuje wartości dla każdej właściwości zdefiniowane w jej typ połączenia. Typy połączeń są dodawane do usługi Azure Automation w modułach integracji lub utworzone za pomocą [interfejsu API usługi Azure Automation](/previous-versions/azure/reference/mt163818(v=azure.100)) Jeśli moduł integracji zawiera typ połączenia i jest importowany do konta usługi Automation. W przeciwnym razie należy utworzyć plik metadanych, aby określić typ połączenia usługi Automation.  Aby uzyskać więcej informacji dotyczących tego, zobacz [moduły integracji](automation-integration-modules.md).  

>[!NOTE]
>Bezpiecznych zasobów w usłudze Azure Automation obejmują poświadczeń, certyfikatów, połączeń i szyfrowane zmienne. Te zasoby są zaszyfrowane i przechowywane w usłudze Azure Automation za pomocą Unikatowy klucz, który jest generowany dla każdego konta usługi automation. Ten klucz jest przechowywany w systemie zarządzane usługi Key Vault. Przed zapisaniem zabezpieczonym zasobem, klucz jest ładowane z usługi Key Vault i następnie używany do szyfrowania elementu zawartości. Ten proces jest zarządzane przez usługę Azure Automation.

## <a name="connection-types"></a>Typy połączeń

Istnieją trzy typy wbudowane połączeń dostępnych w usłudze Azure Automation:

* **Azure** — to połączenie może służyć do zarządzania zasobami klasycznymi.
* **AzureClassicCertificate** — to połączenie jest używane przez **AzureClassicRunAs** konta.
* **AzureServicePrincipal** — to połączenie jest używane przez **AzureRunAs** konta.

W większości przypadków nie trzeba utworzyć zasobu połączenia, ponieważ jest on tworzony podczas tworzenia [konto Uruchom jako](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Polecenia cmdlet programu Windows PowerShell

Polecenia cmdlet w poniższej tabeli służą do tworzenia i zarządzania nimi połączenia usługi Automation przy użyciu programu Windows PowerShell. Są dostarczane jako część systemu [modułu Azure PowerShell](/powershell/azure/overview) który jest dostępny do użycia w elementach runbook automatyzacji i konfiguracji DSC.

|Polecenie cmdlet|Opis|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Pobiera połączenie. Zawiera tabelę mieszania z wartościami pól połączeń.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Tworzy nowe połączenie.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Usuń istniejące połączenie.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Ustawia wartość określonego pola dla istniejącego połączenia.|

## <a name="activities"></a>Działania

Działania w poniższej tabeli są używane do dostępu do połączenia w elemencie runbook lub konfiguracji DSC.

|Działania|Opis|
|---|---|
|[Get-AutomationConnection](/powershell/module/servicemanagement/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Pobiera połączenie do użycia. Zwraca tabelę mieszania, za pomocą właściwości połączenia.|

>[!NOTE] 
>Należy unikać używania zmiennych za pomocą parametru Name **Get AutomationConnection** ponieważ może to skomplikować wykrywanie zależności między elementami runbook lub konfiguracji DSC i zasobów połączeń w czasie projektowania.

 
## <a name="python2-functions"></a>Funkcje Python2 
Funkcja w poniższej tabeli umożliwia dostęp do połączeń w elemencie runbook Python2. 

| Funkcja | Opis | 
|:---|:---| 
| automationassets.get_automation_connection | Pobiera połączenie. Zwraca słownik za pomocą właściwości połączenia. | 

> [!NOTE] 
> Aby uzyskać dostęp do funkcji zasobów, należy zaimportować moduł "automationassets" w górnej części elementu runbook języka Python.

## <a name="creating-a-new-connection"></a>Tworzenie nowego połączenia

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Aby utworzyć nowe połączenie za pomocą witryny Azure portal

1. Na koncie usługi automation kliknij **zasoby** , aby otworzyć **zasoby** bloku.
2. Kliknij przycisk **połączeń** , aby otworzyć **połączeń** bloku.
3. Kliknij przycisk **Dodaj połączenie** w górnej części bloku.
4. W **typu** listy rozwijanej wybierz typ połączenia, w której chcesz utworzyć. Formularz spowoduje wyświetlenie właściwości dla tego określonego typu.
5. Wypełnij formularz, a następnie kliknij przycisk **Utwórz** można zapisać nowego połączenia.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Aby utworzyć nowe połączenie przy użyciu programu Windows PowerShell

Utwórz nowe połączenie za pomocą środowiska Windows PowerShell przy użyciu [New AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) polecenia cmdlet. To polecenie cmdlet ma parametr o nazwie **ConnectionFieldValues** który oczekuje, że [tabeli mieszania](https://technet.microsoft.com/library/hh847780.aspx) definiowanie wartości dla każdej właściwości zdefiniowane przez typ połączenia.

Jeśli znasz automatyzację [konta Uruchom jako](automation-sec-configure-azure-runas-account.md) do uwierzytelniania elementów runbook przy użyciu jednostki usługi, skrypt programu PowerShell, podana jako alternatywę do tworzenia konta Uruchom jako z portalu, tworzy nowe połączenie zasób za pomocą następujących przykładowych poleceniach.  

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Można użyć skryptu, aby utworzyć zasób połączenia, ponieważ podczas tworzenia konta usługi Automation automatycznie obejmuje kilka modułów globalnych domyślnie wraz z typu połączenia **AzureServicePrincipal** do Tworzenie **AzureRunAsConnection** zasób połączenia.  Może to być ważne, aby pamiętać, dlatego Jeśli spróbujesz utworzyć nowy zasób połączenia, połączyć się z usługą lub aplikacją przy użyciu innej metody uwierzytelniania zakończy się niepowodzeniem, ponieważ typ połączenia nie jest już zdefiniowany w ramach konta usługi Automation.  Aby uzyskać więcej informacji na temat tworzenia swój własny typ połączenia, niestandardowe lub modułu na podstawie [galerii programu PowerShell](https://www.powershellgallery.com), zobacz [moduły integracji](automation-integration-modules.md)
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Połączenie runbook lub konfiguracji DSC

Możesz pobrać połączenie runbook lub konfiguracji DSC przy użyciu **Get AutomationConnection** polecenia cmdlet.  Nie można użyć [Get AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) działania.  To działanie pobiera wartości różnych pól w połączeniu i zwraca je jako [tabeli mieszania](https://go.microsoft.com/fwlink/?LinkID=324844) której następnie można za pomocą odpowiednich poleceń w elemencie runbook lub konfiguracji DSC.

### <a name="textual-runbook-sample"></a>Przykład tekstowy element runbook

Następujące przykładowe polecenia pokazują, jak używać konta Uruchom jako wspomniano wcześniej, do uwierzytelniania przy użyciu zasobów usługi Azure Resource Manager w elemencie runbook.  Używa ona zasób połączenia reprezentujący konto Uruchom jako, który odwołuje się do nazwy głównej usługi opartej na certyfikacie, nie poświadczeń.  

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection 
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** teraz jest aliasem dla **Connect-AzureRMAccount**. Podczas wyszukiwania biblioteki elementów, jeśli nie widzisz **Connect-AzureRMAccount**, możesz użyć **Add-AzureRmAccount**, lub na koncie usługi Automation można zaktualizować moduły.

### <a name="graphical-runbook-samples"></a>Przykłady graficznego elementu runbook

Możesz dodać **Get AutomationConnection** działanie graficzny element runbook, klikając prawym przyciskiem myszy w ramach połączenia w okienku Biblioteka edytor graficzny i wybierając polecenie **Dodaj do kanwy**.

![Dodaj do kanwy](media/automation-connections/connection-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład korzystać z połączenia w graficznego elementu runbook.  Jest to ten sam powyższym przykładzie do uwierzytelniania przy użyciu konta Uruchom jako z tekstowej elementu runbook.  W tym przykładzie użyto **wartości stałej** zestaw danych na potrzeby **Uzyskaj połączenie Uruchom jako** działanie, które używa obiektu połączenia do uwierzytelniania.  A [linku potoku](automation-graphical-authoring-intro.md#links-and-workflow) jest używany w tym miejscu, ponieważ zestaw parametrów ServicePrincipalCertificate oczekuje pojedynczego obiektu.

![Nawiązuj połączenia](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Przykładowy element runbook Python2
Poniższy przykład pokazuje sposób uwierzytelniania przy użyciu połączenie Uruchom jako w elemencie runbook Python2.

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
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

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
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>Kolejne kroki

- Przegląd [łącza w tworzenie graficzne](automation-graphical-authoring-intro.md#links-and-workflow) zrozumienie, jak kierować do sterowania przepływem logiki w elementach runbook.  

- Aby dowiedzieć się więcej na temat używania usługi Azure Automation modułów programu PowerShell i najlepsze rozwiązania do tworzenia własnych modułów programu PowerShell, aby działać jako moduły integracji w usłudze Azure Automation, zobacz [moduły integracji](automation-integration-modules.md).  

