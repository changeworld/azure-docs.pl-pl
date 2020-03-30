---
title: Zasoby połączeń w usłudze Azure Automation
description: Zasoby połączeń w usłudze Azure Automation zawierają informacje wymagane do nawiązania połączenia z usługą lub aplikacją z konfiguracji systemu runbook lub DSC. W tym artykule wyjaśniono szczegóły połączeń i sposób pracy z nimi zarówno w tekście, jak i w grafice.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: b6276153921feb0e6f27194d36d1c32c1d0ffb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75940829"
---
# <a name="connection-assets-in-azure-automation"></a>Zasoby połączeń w usłudze Azure Automation

Zasób połączenia automatyzacji zawiera informacje wymagane do nawiązania połączenia z usługą lub aplikacją z konfiguracji systemu runbook lub DSC. Może to obejmować informacje wymagane do uwierzytelniania, takie jak nazwa użytkownika i hasło oprócz informacji o połączeniu, takich jak adres URL lub port. Wartość połączenia jest utrzymanie wszystkich właściwości do łączenia się z określoną aplikacją w jednym zasobie, w przeciwieństwie do tworzenia wielu zmiennych. Użytkownik może edytować wartości połączenia w jednym miejscu i można przekazać nazwę połączenia do konfiguracji wiązki uruchomieniu lub DSC w jednym parametrze. Właściwości połączenia są dostępne w konfiguracji elementu runbook lub DSC za pomocą działania **Get-AutomationConnection.**

Podczas tworzenia połączenia należy określić *typ połączenia*. Typ połączenia jest szablonem definiujący zestaw właściwości. Połączenie definiuje wartości dla każdej właściwości zdefiniowanej w jego typie połączenia. Typy połączeń są dodawane do usługi Azure Automation w modułach integracji lub tworzone za pomocą [interfejsu API automatyzacji platformy Azure,](/previous-versions/azure/reference/mt163818(v=azure.100)) jeśli moduł integracji zawiera typ połączenia i jest importowany do konta automatyzacji. W przeciwnym razie należy utworzyć plik metadanych, aby określić typ połączenia automatyzacji. Aby uzyskać więcej informacji na ten temat, zobacz [Moduły integracji](automation-integration-modules.md).

>[!NOTE]
>Bezpieczne zasoby w usłudze Azure Automation obejmują poświadczenia, certyfikaty, połączenia i zaszyfrowane zmienne. Te zasoby są szyfrowane i przechowywane w usłudze Azure Automation przy użyciu unikatowego klucza, który jest generowany dla każdego konta automatyzacji. Ten klucz jest przechowywany w zarządzanym przez system magazynie kluczy. Przed zapisaniem bezpiecznego zasobu klucz jest ładowany z usługi Key Vault, a następnie używany do szyfrowania zasobu. Ten proces jest zarządzany przez usługę Azure Automation.

## <a name="connection-types"></a>Typy połączeń

Istnieją trzy typy wbudowanych połączeń dostępnych w usłudze Azure Automation:

* **Platforma Azure** — to połączenie może służyć do zarządzania zasobami klasycznymi.
* **AzureClassicCertificate** — to połączenie jest używane przez konto **AzureClassicRunAs.**
* **AzureServicePrincipal** — to połączenie jest używane przez konto **AzureRunAs.**

W większości przypadków nie trzeba tworzyć zasobu połączenia, ponieważ jest on tworzony podczas tworzenia [konta RunAs](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Polecenia cmdlet środowiska Windows PowerShell

Polecenia cmdlet w poniższej tabeli są używane do tworzenia połączeń automatyzacji i zarządzania nimi za pomocą programu Windows PowerShell. Są one dostarczane jako część [modułu Azure PowerShell](/powershell/azure/overview), który jest dostępny do użycia w systemach runbook automatyzacji i konfiguracji DSC.

|Polecenie cmdlet|Opis|
|:---|:---|
|[Połączenie Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Pobiera połączenie. Zawiera tabelę mieszania z wartościami pól połączenia.|
|[Połączenie nowej usługi AzureRmAutomation](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Tworzy nowe połączenie.|
|[Usuń-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Usuwa istniejące połączenie.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Ustawia wartość określonego pola dla istniejącego połączenia.|

## <a name="activities"></a>Działania

Działania w poniższej tabeli są używane do uzyskiwania dostępu do połączeń w konfiguracji elementów runbook lub DSC.

|Działania|Opis|
|---|---|
|Get-AutomationConnection | Pobiera połączenie do użycia. Zwraca tabelę mieszania z właściwościami połączenia.|

>[!NOTE]
>Należy unikać używania zmiennych z parametrem –Name **get-AutomationConnection,** ponieważ może to skomplikować odnajdowanie zależności między elementami runbook lub konfiguracjami DSC i zasobami połączenia w czasie projektowania.


## <a name="python2-functions"></a>Funkcje Python2

Funkcja w poniższej tabeli służy do uzyskiwania dostępu do połączeń w uruchomieniu python2.

| Funkcja | Opis |
|:---|:---|
| automationassets.get_automation_connection | Pobiera połączenie. Zwraca słownik z właściwościami połączenia. |

> [!NOTE]
> Aby uzyskać dostęp do funkcji zasobu, należy zaimportować moduł "automationassets" w górnej części systemu runbook języka Python.

## <a name="creating-a-new-connection"></a>Tworzenie nowego połączenia

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Aby utworzyć nowe połączenie z witryną Azure portal

1. Na koncie automatyzacji kliknij część **Zasoby,** aby otworzyć blok **Zasoby.**
2. Kliknij część **Połączenia,** aby otworzyć blok **Połączenia.**
3. Kliknij **pozycję Dodaj połączenie** w górnej części bloku.
4. Z listy rozwijanej **Typ** wybierz typ połączenia, które chcesz utworzyć. Formularz przedstawi właściwości dla tego określonego typu.
5. Wypełnij formularz i kliknij przycisk **Utwórz,** aby zapisać nowe połączenie.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Aby utworzyć nowe połączenie z programem Windows PowerShell

Utwórz nowe połączenie z programem Windows PowerShell przy użyciu polecenia cmdlet [New-AzureRmAutomationConnection.](/powershell/module/azurerm.automation/new-azurermautomationconnection) To polecenie cmdlet ma parametr o nazwie **ConnectionFieldValues,** który oczekuje [tabeli mieszania](https://technet.microsoft.com/library/hh847780.aspx) definiujące wartości dla każdej właściwości zdefiniowanych przez typ połączenia.

Jeśli znasz konto Uruchamianie automatyzacji jako do uwierzytelniania [elementów](automation-sec-configure-azure-runas-account.md) runbook przy użyciu jednostki usługi, skrypt programu PowerShell, pod warunkiem, jako alternatywa dla tworzenia konta Uruchom jako z portalu, tworzy nowy zasób połączenia przy użyciu następujących poleceń przykładowych.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Skrypt można użyć do utworzenia zasobu połączenia, ponieważ podczas tworzenia konta automatyzacji automatycznie zawiera kilka modułów globalnych domyślnie wraz z typem połączenia **AzureServicePrincipal** do utworzenia zasobu połączenia **AzureRunAsConnection.** Jest to ważne, aby pamiętać, ponieważ w przypadku próby utworzenia nowego zasobu połączenia, aby połączyć się z usługą lub aplikacją przy użyciu innej metody uwierzytelniania, zakończy się niepowodzeniem, ponieważ typ połączenia nie jest jeszcze zdefiniowany na koncie automatyzacji. Aby uzyskać więcej informacji na temat tworzenia własnego typu połączenia dla niestandardowego lub modułu z [Galerii programu PowerShell,](https://www.powershellgallery.com)zobacz [Moduły integracji](automation-integration-modules.md)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Korzystanie z połączenia w konfiguracji systemu runbook lub DSC

Połączenie jest pobierane w konfiguracji wiązany lub dsc za pomocą polecenia cmdlet **Get-AutomationConnection.** Nie można użyć [get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) działania. To działanie pobiera wartości różnych pól w połączeniu i zwraca je jako [tabelę mieszania](https://go.microsoft.com/fwlink/?LinkID=324844), która może być następnie używana z odpowiednimi poleceniami w konfiguracji runbook lub DSC.

### <a name="textual-runbook-sample"></a>Przykładowy tekstowy podręcznik

Poniższe przykładowe polecenia pokazują, jak używać konta Uruchom jako wymienione wcześniej, aby uwierzytelnić się przy użyciu zasobów usługi Azure Resource Manager w bieślicie. Używa zasobu połączenia reprezentującego konto Uruchom jako, które odwołuje się do jednostki usługi opartej na certyfikatach, a nie poświadczeń.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** jest teraz aliasem **connect-azurermaccount**. Podczas przeszukiwania elementów biblioteki, jeśli nie widzisz **Connect-AzureRMAccount**, można użyć **Add-AzureRmAccount**lub można zaktualizować moduły na koncie automatyzacji.

### <a name="graphical-runbook-samples"></a>Przykłady graficznego ekscesu

Działanie **Get-AutomationConnection** można dodać do graficznego systemu runbook, klikając prawym przyciskiem myszy połączenie w okienku **Biblioteka edytora** graficznego i wybierając pozycję **Dodaj do obszaru roboczego**.

![dodaj do kanwy](media/automation-connections/connection-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia połączenia w graficznym uruchomieniu wiązku. Jest to ten sam przykład pokazany powyżej do uwierzytelniania przy użyciu konta Uruchom jako z tekstowym plikiem runbook. W tym przykładzie użyto zestawu danych **wartości stałej** dla działania **Get RunAs Connection,** który używa obiektu połączenia do uwierzytelniania. [Łącze potoku](automation-graphical-authoring-intro.md#links-and-workflow) jest używane w tym miejscu, ponieważ ServicePrincipalCertificate parametr set is expecting a single object.

![uzyskać połączenia](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Przykład owy podręcznik Python2

W poniższym przykładzie pokazano, jak uwierzytelnić przy użyciu połączenia Uruchom jako w uruchomieniu 2.

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

- Przejrzyj [łącza w tworzenia graficznym,](automation-graphical-authoring-intro.md#links-and-workflow) aby zrozumieć, jak kierować i kontrolować przepływ logiki w elementach runbook.

- Aby dowiedzieć się więcej na temat korzystania przez usługę Azure Automation z modułów programu PowerShell i najlepszych rozwiązań dotyczących tworzenia własnych modułów programu PowerShell do pracy jako moduły integracji w ramach automatyzacji platformy Azure, zobacz [Moduły integracji.](automation-integration-modules.md)