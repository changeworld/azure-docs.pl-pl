---
title: Rejestrowania platformy Azure Key Vault | Dokumentacja firmy Microsoft
description: Użyj tego samouczka, aby rozpocząć pracę z funkcją rejestrowania usługi Azure Key Vault.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 89f9ef37ed7c53817854442b3a32b32b7d11ae27
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64706026"
---
# <a name="azure-key-vault-logging"></a>Funkcja rejestrowania usługi Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Po utworzeniu co najmniej jednego magazynu kluczy, prawdopodobnie należy do monitorowania sposobu i czasu Twoich magazynów kluczy są używane i przez kogo. Można to zrobić, należy włączyć rejestrowanie dla usługi Azure Key Vault, który zapisuje informacje o koncie magazynu platformy Azure, które należy podać. Nowy kontener o nazwie **insights-logs-auditevent** jest tworzona automatycznie dla określonego konta magazynu. Do zbierania dzienników dla wielu magazynów kluczy, można użyć tego samego konta magazynu.

Można się do informacji rejestrowania będzie 10 minut (maksymalnie) po wykonaniu operacji magazynu kluczy. W większości przypadków czas będzie krótszy.  To Ty zarządzasz dziennikami na swoim koncie magazynu:

* Użyj standardowych metod kontroli dostępu platformy Azure w celu zabezpieczenia dzienników, wprowadzając ograniczenia co do tego, kto może uzyskiwać do nich dostęp.
* Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.

Użyj tego samouczka, aby rozpocząć pracę z funkcją rejestrowania usługi Azure Key Vault. Będzie Tworzenie konta magazynu, Włącz rejestrowanie i interpretować informacje zebrany dziennik.  

> [!NOTE]
> Ten samouczek nie zawiera instrukcji dotyczących sposobu tworzenia magazynów kluczy, kluczy ani kluczy tajnych. Aby uzyskać te informacje, zobacz [co to jest usługa Azure Key Vault?](key-vault-overview.md). Lub, aby uzyskać instrukcje wiersza polecenia platformy Azure dla wielu platform, zobacz [tym równoważnym samouczku](key-vault-manage-with-cli2.md).
>
> Ten artykuł zawiera instrukcje aktualizowania rejestrowania diagnostycznego programu Azure PowerShell. Rejestrowanie diagnostyczne można również aktualizować za pomocą usługi Azure Monitor w **dzienniki diagnostyczne** części witryny Azure portal. 
>

Aby uzyskać informacje ogólne o usłudze Key Vault, zobacz [co to jest usługa Azure Key Vault?](key-vault-whatis.md). Aby dowiedzieć się, jak gdzie dostępna jest usługa Key Vault, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Istniejący magazyn kluczy, który był przez Ciebie używany.  
* Program Azure PowerShell w wersji 1.0.0. Aby zainstalować program Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [Sposób instalowania i konfigurowania programu Azure PowerShell](/powershell/azure/overview). Jeśli masz już zainstalowany program Azure PowerShell, nie wiadomo, wersja, z poziomu konsoli programu Azure PowerShell wprowadź `$PSVersionTable.PSVersion`.  
* Wystarczająca ilość miejsca w magazynie platformy Azure dla dzienników usługi Key Vault.

## <a id="connect"></a>Połącz z subskrypcją usługi key vault

Pierwszym krokiem w procesie konfigurowania klucza rejestracji jest punkt Azure PowerShell do magazynu kluczy, które mają być rejestrowane.

Uruchom sesję programu Azure PowerShell, a następnie zaloguj się do konta platformy Azure przy użyciu następującego polecenia:  

```powershell
Connect-AzAccount
```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Program Azure PowerShell pobierze wszystkie subskrypcje, które są skojarzone z tym kontem. Domyślnie program PowerShell użyje pierwszej.

Może być określić subskrypcję, który został użyty do utworzenia magazynu kluczy. Wprowadź następujące polecenie, aby zobaczyć subskrypcje dla konta:

```powershell
Get-AzSubscription
```

Następnie aby określić subskrypcję, która jest skojarzona z magazynem kluczy, który będzie można się zalogować, wprowadź:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Wskazuje odpowiednią subskrypcję programu PowerShell jest to ważny krok, zwłaszcza, jeśli masz wiele subskrypcji skojarzonych z Twoim kontem. Aby uzyskać więcej informacji na temat konfigurowania programu Azure PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

## <a id="storage"></a>Tworzenie konta magazynu dla dzienników

Chociaż można użyć istniejącego konta magazynu dla dzienników, utworzymy konto magazynu, które będzie przeznaczone dla dzienników usługi Key Vault. Jako udogodnienie dotyczące gdy mamy później określić, szczegółowe informacje będą przechowywane w zmiennej o nazwie **sa**.

W celu ułatwienia zarządzania użyjemy tej samej grupie zasobów, która zawiera usługi key vault. Z [samouczek ułatwiający rozpoczęcie](key-vault-get-started.md), ta grupa zasobów nosi nazwę **ContosoResourceGroup**, i będziemy używać lokalizacji Azja Wschodnia. Zastąp te wartości własnymi, zgodnie z wymaganiami:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Jeśli zdecydujesz się użyć istniejącego konta magazynu, ona używać tej samej subskrypcji co magazyn kluczy. I musi używać modelu wdrażania usługi Azure Resource Manager, a nie klasycznego modelu wdrażania.
>
>

## <a id="identify"></a>Identyfikowanie magazynu kluczy dla dzienników

W [samouczek ułatwiający rozpoczęcie](key-vault-get-started.md), nazwa usługi key vault **ContosoKeyVault**. Będziemy używać tej nazwy i przechowywać dane w zmiennej o nazwie **kv**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Włączanie rejestrowania

Aby włączyć rejestrowanie dla usługi Key Vault, użyjemy **AzDiagnosticSetting zestaw** polecenia cmdlet, wraz ze zmiennymi utworzone dla nowego konta magazynu i magazynu kluczy. Ustawimy też **— włączone** flaga **$true** i Ustaw kategorię **AuditEvent** (jedyna kategoria rejestrowania usługi Key Vault):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Dane wyjściowe wyglądają następująco:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Te dane wyjściowe potwierdzają, że włączono rejestrowanie dla magazynu kluczy i zapisze informacje do swojego konta magazynu.

Opcjonalnie można ustawić zasady przechowywania dla dzienników tak, aby starsze dzienniki są automatycznie usuwane. Na przykład ustawić zasady przechowywania, ustawiając **- Retentionenable** flaga **$true**i ustaw **- RetentionInDays** parametr **90**tak, aby dzienniki starsze niż 90 dni są automatycznie usuwane.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Co jest rejestrowane:

* Wszystkie uwierzytelnione żądania interfejsu API REST, w tym żądań zakończonych niepowodzeniem w wyniku uprawnienia dostępu, błędów systemowych lub żądaniach.
* Operacje na klucz magazynu siebie, łącznie z tworzeniem, usuwaniem, ustawianie zasad dostępu magazynu kluczy i aktualizowaniem atrybutów magazynu kluczy, takich jak tagi.
* Operacje na kluczach i wpisach tajnych w magazynie kluczy, w tym:
  * Tworzenie, modyfikowanie lub usuwaniem tych kluczy lub kluczy tajnych.
  * Podpisywanie, weryfikowanie, szyfrowanie, odszyfrowywanie, zawijania i Odkodowywanie klucze pobierania wpisów tajnych oraz listę kluczy i wpisów tajnych (i ich wersje).
* Nieuwierzytelnione żądania, które powodują uzyskanie odpowiedzi 401. Przykładami są żądań, które nie mają tokenu elementu nośnego, są źle sformułowane lub wygasły lub mają nieprawidłowy token.  

## <a id="access"></a>Uzyskiwanie dostępu do dzienników

Dzienniki magazynu kluczy są przechowywane w **insights-logs-auditevent** kontenera na koncie magazynu, podane. Aby wyświetlić dzienniki, musisz pobierać obiekty BLOB.

Najpierw utwórz zmienną dla nazwy kontenera. Użyjesz tej zmiennej w pozostałej części przewodnika.

```powershell
$container = 'insights-logs-auditevent'
```

Aby wyświetlić listę wszystkich obiektów blob w tym kontenerze, wpisz:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Dane wyjściowe wyglądają podobnie do następujących:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

- - -
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Jak widać na tych danych wyjściowych, obiekty BLOB konwencją nazewnictwa: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Wartości daty i godziny używają czasu UTC.

Ponieważ korzystają z tego samego konta magazynu, do zbierania dzienników dla wielu zasobów, pełny identyfikator zasobu w nazwie obiektu blob jest uzyskanie dostępu i pobranie tylko tych obiektów blob, które są potrzebne. Jednak zanim do tego przejdziemy, najpierw zostanie omówiony sposób pobierania wszystkich obiektów blob.

Utwórz folder w celu pobrania obiektów blob. Na przykład:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Następnie uzyskaj listę wszystkich obiektów blob:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Prześlij tę listę, za pośrednictwem **Get AzStorageBlobContent** pobierania obiektów blob do folderu docelowego:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Po uruchomieniu tego drugiego polecenia **/** ogranicznika w nazwach obiektów blob utworzy pełną strukturę folderów w folderze docelowym. Ta struktura będzie używana do pobierania i przechowywania obiektów blob jako plików.

Aby selektywnie pobierać obiekty blob, użyj symboli wieloznacznych. Na przykład:

* Jeśli masz wiele magazynów kluczy i chcesz pobrać dzienniki dla tylko jednego magazynu kluczy o nazwie CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Jeśli masz wiele grup zasobów i chcesz pobrać dzienniki dla tylko jednej grupy zasobów, użyj parametru `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Aby pobrać wszystkie dzienniki w miesiącu 2019 stycznia, należy użyć `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Teraz możesz rozpocząć wyszukiwanie informacji zawartych w dziennikach. Jednak zanim przejdziemy dalej, należy znać dwie kolejne polecenia:

* Aby wykonać zapytanie o stan ustawień diagnostycznych dla zasobu magazynu kluczy: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Aby wyłączyć rejestrowanie dla zasobu magazynu kluczy: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Interpretowanie dzienników usługi Key Vault

Poszczególne obiekty blob są przechowywane jako tekst w formacie obiektu blob JSON. Przyjrzyjmy się przykładowy wpis dziennika. Uruchom następujące polecenie:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Zwraca wpis dziennika podobny do następującego:

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

W poniższej tabeli wymieniono nazwy pól i opisy:

| Nazwa pola | Opis |
| --- | --- |
| **czas** |Data i godzina w formacie UTC. |
| **resourceId** |Identyfikator usługi Azure Resource Manager zasobu. W przypadku dzienników usługi Key Vault jest zawsze identyfikator zasobu usługi Key Vault |
| **OperationName** |Nazwa operacji zgodnie z opisem w następnej tabeli. |
| **operationVersion** |Wersja interfejsu API REST zażądane przez klienta. |
| **category** |Typ wyniku. W przypadku dzienników usługi Key Vault **AuditEvent** jest dostępną wartością. |
| **resultType** |Wynik żądania interfejsu API REST. |
| **resultSignature** |Stan HTTP. |
| **resultDescription** |Dodatkowy opis wyniku, jeśli jest dostępny. |
| **durationMs** |Czas potrzebny do obsłużenia żądania interfejsu API REST podany w milisekundach. Nie obejmuje opóźnienia sieci, więc czas zmierzony po stronie klienta może być niezgodny z tym czasem. |
| **callerIpAddress** |Adres IP klienta, który zgłosił żądanie. |
| **correlationId** |Opcjonalny identyfikator GUID, który klient może przekazać w celu skorelowania dzienników po stronie klienta z dziennikami po stronie usługi (Key Vault). |
| **Tożsamość** |Tożsamość z tokenu, który został przedstawiony w żądania interfejsu API REST. Jest to zazwyczaj "użytkownik", "główna nazwa usługi" lub kombinacja "użytkownik + identyfikator appId,", tak jak w przypadku żądania, która wynika z polecenia cmdlet programu Azure PowerShell. |
| **Właściwości** |Informacje, które różni się w zależności od operacji (**operationName**). W większości przypadków to pole zawiera informacje o kliencie (ciąg agenta użytkownika przekazany przez klienta), dokładny identyfikator URI żądania interfejsu API REST i kod stanu HTTP. Ponadto, gdy obiekt jest zwracany w wyniku żądania (na przykład **KeyCreate** lub **VaultGet**), zawiera ona także klucz identyfikatora URI (jako "id"), Magazyn, identyfikator URI lub identyfikator URI klucza tajnego. |

**OperationName** wartości pól są w *ObjectVerb* formatu. Na przykład:

* Wszystkie operacje kluczy mają `Vault<action>` formatowania, takich jak `VaultGet` i `VaultCreate`.
* Wszystkie operacje kluczy mają `Key<action>` formatowania, takich jak `KeySign` i `KeyList`.
* Wszystkie operacje kluczy tajnych mają `Secret<action>` formatowania, takich jak `SecretGet` i `SecretListVersions`.

W poniższej tabeli wymieniono **operationName** odpowiednich poleceń interfejsu API REST i wartości:

| operationName | Polecenie interfejsu API REST |
| --- | --- |
| **Uwierzytelnianie** |Uwierzytelnianie za pośrednictwem punktu końcowego usługi Azure Active Directory |
| **VaultGet** |[Pobierz informacje o magazynie kluczy](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Utwórz lub zaktualizuj magazyn kluczy](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Usuń magazyn kluczy](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Zaktualizuj magazyn kluczy](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Utwórz listę wszystkich magazynów kluczy w grupie zasobów](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Utwórz klucz](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Pobierz informacje o kluczu](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importuj klucz do magazynu](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Utwórz kopię zapasową klucza](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Usuń klucz](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Przywróć klucz](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Podpisz przy użyciu klucza](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Weryfikuj za pomocą klucza](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Opakuj klucz](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Odpakuj klucz](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Szyfruj za pomocą klucza](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Odszyfruj za pomocą klucza](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Zaktualizuj klucz](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Utwórz listę kluczy w magazynie](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Utwórz listę wersji klucza](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Utwórz klucz tajny](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Pobierz klucz tajny](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Zaktualizuj klucz tajny](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Usuń klucz tajny](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Utwórz listę kluczy tajnych w magazynie](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Utwórz listę wersji klucza tajnego](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Przy użyciu dzienników usługi Azure Monitor

Rozwiązanie Key Vault można użyć w dziennikach w usłudze Azure Monitor, aby zapoznać się z usługi Key Vault **AuditEvent** dzienniki. W dziennikach usługi Azure Monitor umożliwia dziennika zapytań analizy danych i uzyskać potrzebne informacje. 

Aby uzyskać więcej informacji, w tym jak to skonfigurować, zobacz [rozwiązanie Azure Key Vault w dziennikach w usłudze Azure Monitor](../azure-monitor/insights/azure-key-vault.md). Ten artykuł zawiera również instrukcje, jeśli chcesz przeprowadzić migrację ze starego rozwiązania Key Vault, które było oferowane w trakcie dzienniki usługi Azure Monitor w wersji zapoznawczej, gdzie możesz najpierw należało skierować dzienniki do konta usługi Azure storage i skonfigurowane usługi Azure Monitor loguje się do odczytu z.

## <a id="next"></a>Następne kroki

Aby uzyskać samouczek, który używa usługi Azure Key Vault w aplikacji sieci web platformy .NET, zobacz [użycia usługi Azure Key Vault z aplikacji sieci web](tutorial-net-create-vault-azure-web-app.md).

Odwołania dotyczące programowania znajdują się w [przewodniku dewelopera usługi Azure Key Vault](key-vault-developers-guide.md).

Aby uzyskać listę poleceń cmdlet programu Azure PowerShell w wersji 1.0, usługi Azure Key Vault, zobacz [poleceń cmdlet usługi Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

Samouczek dotyczący rotacji kluczy i inspekcji dzienników w usłudze Azure Key Vault, zobacz [Konfigurowanie usługi Key Vault przy użyciu end-to-end rotacji i inspekcji kluczy](key-vault-key-rotation-log-monitoring.md).
