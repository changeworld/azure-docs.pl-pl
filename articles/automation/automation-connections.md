---
title: Zasoby połączenia w Azure Automation
description: Zasoby połączeń w Azure Automation zawierają informacje wymagane do nawiązania połączenia z zewnętrzną usługą lub aplikacją z poziomu elementu Runbook lub konfiguracji DSC. W tym artykule opisano szczegółowe informacje o połączeniach i sposobach pracy z nimi zarówno w tworzeniu tekstu, jak i w formie graficznej.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 01/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 90b2234607ad120c43e241fe4ae5222fe285803e
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001650"
---
# <a name="connection-assets-in-azure-automation"></a>Zasoby połączenia w Azure Automation

Zasób połączenia usługi Automation zawiera informacje wymagane do nawiązania połączenia z zewnętrzną usługą lub aplikacją z poziomu elementu Runbook lub konfiguracji DSC. Może to obejmować informacje wymagane do uwierzytelniania, takie jak nazwa użytkownika i hasło, a także informacje dotyczące połączenia, takie jak adres URL lub port. Wartość połączenia zachowuje wszystkie właściwości łączenia się z konkretną aplikacją w jednym z zasobów, zamiast tworzyć wiele zmiennych. Użytkownik może edytować wartości połączenia w jednym miejscu i można przekazać nazwę połączenia do elementu Runbook lub konfiguracji DSC w jednym parametrze. Dostęp do właściwości połączenia można uzyskać, korzystając z działania **Get-AutomationConnection** w elemencie Runbook lub konfiguracji DSC.

Podczas tworzenia połączenia należy określić *Typ połączenia*. Typ połączenia to szablon, który definiuje zestaw właściwości. Połączenie definiuje wartości dla każdej właściwości zdefiniowanej w jej typie połączenia. Typy połączeń są dodawane do Azure Automation w modułach integracji lub tworzone za pomocą [interfejsu API Azure Automation](/previous-versions/azure/reference/mt163818(v=azure.100)) , jeśli moduł integracji zawiera typ połączenia i zostanie zaimportowany do konta usługi Automation. W przeciwnym razie konieczne będzie utworzenie pliku metadanych w celu określenia typu połączenia automatyzacji.  Aby uzyskać więcej informacji na ten temat, zobacz [moduły integracji](automation-integration-modules.md).

>[!NOTE]
>Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zmienne zaszyfrowane. Te zasoby są szyfrowane i przechowywane w Azure Automation przy użyciu unikatowego klucza wygenerowanego dla każdego konta usługi Automation. Ten klucz jest przechowywany w Key Vault zarządzanym przez system. Przed zapisaniem bezpiecznego elementu zawartości klucz jest ładowany z Key Vault a następnie używany do szyfrowania elementu zawartości. Ten proces jest zarządzany przez Azure Automation.

## <a name="connection-types"></a>Typy połączeń

Istnieją trzy typy wbudowanych połączeń dostępnych w Azure Automation:

* **Azure** — to połączenie może służyć do zarządzania klasycznymi zasobami.
* **AzureClassicCertificate** — to połączenie jest używane przez konto **AzureClassicRunAs** .
* **AzureServicePrincipal** — to połączenie jest używane przez konto **AzureRunAs** .

W większości przypadków nie trzeba tworzyć zasobu połączenia, ponieważ jest tworzony podczas tworzenia [konta Uruchom](manage-runas-account.md)jako.

## <a name="windows-powershell-cmdlets"></a>Polecenia cmdlet programu Windows PowerShell

Polecenia cmdlet w poniższej tabeli służą do tworzenia połączeń automatyzacji i zarządzania nimi za pomocą programu Windows PowerShell. Są one dostarczane jako część [modułu Azure PowerShell](/powershell/azure/overview) , który jest dostępny do użycia w elementach Runbook usługi Automation i konfiguracjach DSC.

|Parametr|Opis|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Pobiera połączenie. Zawiera tabelę skrótów z wartościami pól połączenia.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Tworzy nowe połączenie.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Usuń istniejące połączenie.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Ustawia wartość określonego pola dla istniejącego połączenia.|

## <a name="activities"></a>Kategoria Activities

Działania w poniższej tabeli służą do uzyskiwania dostępu do połączeń w elemencie Runbook lub konfiguracji DSC.

|Kategoria Activities|Opis|
|---|---|
|[Get-AutomationConnection](/powershell/module/servicemanagement/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Pobiera połączenie do użycia. Zwraca tablicę skrótów z właściwościami połączenia.|

>[!NOTE]
>Należy unikać używania zmiennych z parametrem – name polecenia **Get-AutomationConnection** , ponieważ może to spowodować skomplikowanie zależności między elementami Runbook lub konfiguracjami DSC oraz zasobami połączeń w czasie projektowania.


## <a name="python2-functions"></a>Funkcje python2
Funkcja w poniższej tabeli służy do uzyskiwania dostępu do połączeń w elemencie Runbook python2.

| Funkcja | Opis |
|:---|:---|
| automationassets. Get _automation_connection | Pobiera połączenie. Zwraca słownik z właściwościami połączenia. |

> [!NOTE]
> Aby uzyskać dostęp do funkcji zasobów, należy zaimportować moduł "automationassets" w górnej części elementu Runbook języka Python.

## <a name="creating-a-new-connection"></a>Tworzenie nowego połączenia

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Aby utworzyć nowe połączenie z Azure Portal

1. Na koncie usługi Automation kliknij składnik **zasoby** , aby otworzyć blok **zasoby** .
2. Kliknij część **połączenia** , aby otworzyć blok **połączenia** .
3. Kliknij pozycję **Dodaj połączenie** w górnej części bloku.
4. Z listy rozwijanej **Typ** wybierz typ połączenia, które chcesz utworzyć. Formularz będzie widział właściwości tego określonego typu.
5. Wypełnij formularz i kliknij przycisk **Utwórz** , aby zapisać nowe połączenie.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Aby utworzyć nowe połączenie za pomocą programu Windows PowerShell

Utwórz nowe połączenie za pomocą programu Windows PowerShell za pomocą polecenia cmdlet [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) . To polecenie cmdlet ma parametr o nazwie **ConnectionFieldValues** , który oczekuje [tabeli skrótów](https://technet.microsoft.com/library/hh847780.aspx) definiującej wartości dla każdej właściwości zdefiniowanej przez typ połączenia.

Jeśli znasz [konto Uruchom jako](automation-sec-configure-azure-runas-account.md) usługi Automation do uwierzytelniania elementów Runbook przy użyciu nazwy głównej usługi, skrypt programu PowerShell, dostarczony jako alternatywę do tworzenia konta Uruchom jako w portalu, utworzy nowy zasób połączenia przy użyciu następujące przykładowe polecenia.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Możesz użyć skryptu, aby utworzyć zasób połączenia, ponieważ podczas tworzenia konta usługi Automation automatycznie zawiera domyślnie kilka modułów globalnych wraz z typem połączenia **AzureServicePrincipal** do utworzeniaZasób połączenia AzureRunAsConnection.  Jest to ważne, ponieważ w przypadku próby utworzenia nowego zasobu połączenia w celu nawiązania połączenia z usługą lub aplikacją z inną metodą uwierzytelniania nie powiedzie się, ponieważ typ połączenia nie jest już zdefiniowany na koncie usługi Automation.  Aby uzyskać więcej informacji na temat tworzenia własnego typu połączenia dla niestandardowego lub modułu z [Galeria programu PowerShell](https://www.powershellgallery.com), zobacz [moduły integracji](automation-integration-modules.md)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Korzystanie z połączenia w elemencie Runbook lub konfiguracji DSC

Połączenie z elementem Runbook lub konfiguracją DSC jest pobierane za pomocą polecenia cmdlet **Get-AutomationConnection** .  Nie można użyć działania [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) .  To działanie pobiera wartości różnych pól w połączeniu i zwraca je jako [tablicę skrótów](https://go.microsoft.com/fwlink/?LinkID=324844) , której można następnie użyć z odpowiednimi poleceniami w elemencie Runbook lub konfiguracji DSC.

### <a name="textual-runbook-sample"></a>Przykład tekstu elementu Runbook

Następujące przykładowe polecenia pokazują, jak używać konta Uruchom jako wymienionego wcześniej, aby uwierzytelniać się przy użyciu zasobów Azure Resource Manager w elemencie Runbook.  Używa ona zasobu Connection reprezentującego konto Uruchom jako, które odwołuje się do nazwy głównej usługi opartej na certyfikatach, a nie poświadczeń.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** jest teraz aliasem dla polecenia **Connect-AzureRmAccount**. Podczas wyszukiwania elementów biblioteki, jeśli nie widzisz pozycji **Connect-AzureRMAccount**, możesz użyć polecenie **Add-AzureRMAccount**lub zaktualizować moduły na koncie usługi Automation.

### <a name="graphical-runbook-samples"></a>Graficzne przykłady elementów Runbook

Aby dodać działanie **Get-AutomationConnection** do graficznego elementu Runbook, kliknij prawym przyciskiem myszy połączenie w okienku Biblioteka w edytorze graficznym i wybierz polecenie **Dodaj do kanwy**.

![Dodaj do kanwy](media/automation-connections/connection-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia połączenia w graficznym elemencie Runbook.  Jest to ten sam przykład, który jest wyświetlany powyżej w celu uwierzytelniania przy użyciu konta Uruchom jako z tekstem elementu Runbook.  W tym przykładzie zastosowano zestaw danych **wartość stała** dla działania **Get runas** , które używa obiektu połączenia do uwierzytelniania.  [Link potoku](automation-graphical-authoring-intro.md#links-and-workflow) jest używany w tym miejscu, ponieważ zestaw parametrów ServicePrincipalCertificate oczekuje pojedynczego obiektu.

![Pobierz połączenia](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Przykład elementu Runbook python2
Poniższy przykład pokazuje sposób uwierzytelniania przy użyciu połączenia Uruchom jako w elemencie Runbook python2.

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

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [linki w temacie Tworzenie graficzne](automation-graphical-authoring-intro.md#links-and-workflow) , aby zrozumieć, jak skierować i kontrolować przepływ logiki w elementach Runbook.

- Aby dowiedzieć się więcej Azure Automation o używaniu modułów programu PowerShell i najlepszych rozwiązaniach w zakresie tworzenia własnych modułów programu PowerShell do pracy jako modułów integracji w programie Azure Automation, zobacz [moduły integracji](automation-integration-modules.md).

