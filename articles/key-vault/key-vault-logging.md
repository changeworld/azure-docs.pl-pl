---
title: Rejestrowanie usługi Azure Key Vault — usługa Azure Key Vault | Dokumentacja firmy Microsoft
description: Użyj tego samouczka, aby rozpocząć pracę z funkcją rejestrowania usługi Azure Key Vault.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 7229cedf2ad5e211847054b53c34e54f633f57e0
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434771"
---
# <a name="azure-key-vault-logging"></a>Funkcja rejestrowania usługi Azure Key Vault

Usługa Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Wprowadzenie

Po utworzeniu co najmniej jednego magazynu kluczy można monitorować kto, w jaki sposób i kiedy uzyskiwał do niego dostęp. W tym celu możesz włączyć funkcję rejestrowania dla usługi Key Vault, która zapisuje informacje na podanym przez Ciebie koncie magazynu platformy Azure. Nowy kontener o nazwie **insights-logs-auditevent** jest tworzony automatycznie dla określonego konta magazynu. Tego samego konta magazynu możesz używać do zbierania dzienników dla wielu magazynów kluczy.

Dostęp do informacji rejestrowania będzie możliwy maksymalnie po 10 minutach od wykonanej operacji magazynu kluczy. W większości przypadków czas będzie krótszy.  To Ty zarządzasz dziennikami na swoim koncie magazynu:

* Użyj standardowych metod kontroli dostępu platformy Azure w celu zabezpieczenia dzienników, wprowadzając ograniczenia co do tego, kto może uzyskiwać do nich dostęp.
* Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.

Ten samouczek ułatwi rozpoczęcie pracy z funkcją rejestrowania usługi Azure Key Vault. Pomoże utworzyć konto magazynu, włączyć funkcję rejestrowania i zinterpretować zebrane informacje rejestrowania.  

> [!NOTE]
> Ten samouczek nie zawiera instrukcji dotyczących sposobu tworzenia magazynów kluczy, kluczy ani kluczy tajnych. Te informacje można znaleźć w temacie [Rozpoczynanie pracy z usługą Azure Key Vault](key-vault-get-started.md). Instrukcje dotyczące wieloplatformowego interfejsu wiersza polecenia znajdują się w [tym równoważnym samouczku](key-vault-manage-with-cli2.md).
>
> Ten artykuł zawiera instrukcje programu Azure PowerShell w celu zaktualizowania rejestrowania diagnostycznego. Jednak takie same włączona przy użyciu usługi Azure Monitor w witrynie Azure portal w **dzienniki diagnostyczne** sekcji. 
>
>

Aby uzyskać ogólne informacje na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Istniejący magazyn kluczy, który był przez Ciebie używany.  
* Usługa Azure PowerShell w **minimalnej wersji 1.0.1**. Aby zainstalować program Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [Sposób instalowania i konfigurowania programu Azure PowerShell](/powershell/azure/overview). Jeśli masz już zainstalowany program Azure PowerShell, ale nie wiesz, z jakiej wersji korzystasz, w konsoli programu Azure PowerShell wpisz polecenie `(Get-Module azure -ListAvailable).Version`.  
* Wystarczająca ilość miejsca w magazynie platformy Azure dla dzienników usługi Key Vault.

## <a id="connect"></a>Nawiązywanie połączenia z subskrypcjami

Uruchom sesję programu PowerShell Azure i zaloguj się na konto platformy Azure przy użyciu następującego polecenia:  

```PowerShell
Connect-AzureRmAccount
```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Program Azure PowerShell pobierze wszystkie subskrypcje, które są skojarzone z tym kontem, i domyślnie użyje pierwszej.

Jeśli masz wiele subskrypcji, określ konkretne konto, które zostało użyte do utworzenia usługi Azure Key Vault. Wpisz następujące polecenie, aby zobaczyć subskrypcje dla swojego konta:

```PowerShell
    Get-AzureRmSubscription
```

Następnie, aby określić subskrypcję skojarzoną z Twoim magazynem kluczy, który będziesz rejestrować, wpisz polecenie:

```PowerShell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> To jest ważny krok, szczególnie przydatny, jeśli masz wiele subskrypcji skojarzonych z Twoim kontem. Może wystąpić błąd rejestrowania usługi Microsoft.Insights, jeśli ten krok zostanie pominięty.
>   
>

Aby uzyskać więcej informacji na temat konfigurowania programu Azure PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

## <a id="storage"></a>Tworzenie nowego konta magazynu dla dzienników

Chociaż można użyć istniejącego konta magazynu dla dzienników, utworzymy nowe konto magazynu, które będzie przeznaczone dla dzienników usługi Key Vault. Aby później można było wygodnie określić wszystkie szczegóły, będą one przechowywane w zmiennej o nazwie **sa**.

W celu ułatwienia zarządzania użyjemy tej grupy zasobów, która zawiera nasz magazyn kluczy. Tak samo jak w [samouczku wprowadzającym](key-vault-get-started.md) ta grupa zasobów ma nazwę **ContosoResourceGroup**. W dalszym ciągu będziemy też używać lokalizacji Azja Wschodnia. Zastąp te wartości własnymi, w razie potrzeby:

```PowerShell
 $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Jeśli zdecydujesz się używać istniejącego konta magazynu, należy użyć tej samej subskrypcji, której użyto dla magazynu kluczy. Musi ona korzystać z modelu wdrażania przy użyciu usługi Resource Manager, a nie klasycznego modelu wdrażania.
>
>

## <a id="identify"></a>Identyfikowanie magazynu kluczy dla dzienników

W naszym samouczku wprowadzającym magazyn kluczy został nazwany **ContosoKeyVault**, dlatego dalej będziemy używać tej nazwy, a szczegóły będziemy przechowywać w zmiennej o nazwie **kv**:

```PowerShell
$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Włączanie rejestrowania

Aby włączyć rejestrowanie dla usługi Key Vault użyjemy polecenia cmdlet Set-AzureRmDiagnosticSetting wraz ze zmiennymi utworzonymi dla naszego nowego konta magazynu i magazynu kluczy. Ustawimy też **— włączone** flaga **$true** i Ustaw kategorię na AuditEvent (jedyna kategoria rejestrowania usługi Key Vault):

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Dane wyjściowe będą wyglądać następująco:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Stanowi to potwierdzenie, że włączono rejestrowanie dla magazynu kluczy i informacje są zapisywane na koncie magazynu.

Opcjonalnie można także ustawić zasady przechowywania dla dzienników tak, aby starsze dzienniki były automatycznie usuwane. Na przykład ustaw zasady przechowywania, ustawiając wartość flagi **-RetentionEnable** na **$true** i wartość parametru **-RetentionInDays** na **90**, aby dzienniki starsze niż 90 dni były automatycznie usuwane.

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Co jest rejestrowane:

* Wszystkie uwierzytelnione żądania interfejsu API REST są rejestrowane, w tym żądania zakończone niepowodzeniem z powodu uprawnień dostępu, błędów systemu lub błędów w żądaniach.
* Operacje na samym magazynie kluczy, łącznie z tworzeniem, usuwaniem, ustawianiem zasad dostępu do magazynu kluczy i aktualizowaniem atrybutów magazynu kluczy, takich jak tagi.
* Operacje na kluczach i kluczach tajnych w magazynie kluczy (łącznie z tworzeniem, modyfikowaniem lub usuwaniem tych kluczy lub kluczy tajnych) oraz operacje, takie jak podpisywanie, weryfikowanie, szyfrowanie, odszyfrowywanie, opakowywanie i odpakowywanie kluczy, pobieranie kluczy tajnych, tworzenie listy kluczy i kluczy tajnych oraz ich wersji.
* Nieuwierzytelnione żądania, które powodują uzyskanie odpowiedzi 401. Na przykład żądania, które nie mają tokenu elementu nośnego, są nieprawidłowo sformułowane, wygasły lub mają nieprawidłowy token.  

## <a id="access"></a>Uzyskiwanie dostępu do dzienników

Dzienniki magazynu kluczy są przechowywane w kontenerze **insights-logs-auditevent** na podanym przez Ciebie koncie magazynu. Aby wyświetlić listę wszystkich obiektów blob w tym kontenerze, wpisz polecenie:

Najpierw utwórz zmienną dla nazwy kontenera. To posłuży w pozostałej części przewodnika.

```PowerShell
$container = 'insights-logs-auditevent'
```

Aby wyświetlić listę wszystkich obiektów blob w tym kontenerze, wpisz polecenie:

```PowerShell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Dane wyjściowe będą wyglądać następująco:

**Identyfikator URI kontenera: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**

**Nazwa**

- - -
**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json**\*\*

Jak widać w przedstawionych danych wyjściowych, obiekty blob są zgodne z następującą konwencją nazewnictwa: **resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json**

Wartości daty i godziny używają czasu UTC.

Ponieważ tego samego konta magazynu może służyć do zbierania dzienników dla wielu zasobów, pełny identyfikator zasobu w nazwie obiektu blob jest uzyskanie dostępu i pobranie tylko tych obiektów blob, które są potrzebne. Jednak zanim do tego przejdziemy, najpierw zostanie omówiony sposób pobierania wszystkich obiektów blob.

Najpierw utwórz folder w celu pobrania obiektów blob. Na przykład:

```PowerShell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Następnie uzyskaj listę wszystkich obiektów blob:  

```PowerShell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Prześlij tę listę potokiem do polecenia „Get-AzureStorageBlobContent”, aby pobrać obiekty blob do folderu docelowego:

```PowerShell
$blobs | Get-AzureStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Po uruchomieniu tego drugiego polecenia ogranicznik **/** w nazwach obiektów blob utworzy pełną strukturę folderów w folderze docelowym, a ta struktura będzie służyć do pobierania i przechowywania obiektów blob jako plików.

Aby selektywnie pobierać obiekty blob, użyj symboli wieloznacznych. Na przykład:

* Jeśli masz wiele magazynów kluczy i chcesz pobrać dzienniki dla tylko jednego magazynu kluczy o nazwie CONTOSOKEYVAULT3:

```PowerShell
Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
```

* Jeśli masz wiele grup zasobów i chcesz pobrać dzienniki dla tylko jednej grupy zasobów, użyj parametru `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

```PowerShell
Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
```

* Jeśli chcesz pobrać wszystkie dzienniki ze stycznia 2016 roku, użyj parametru `-Blob '*/year=2016/m=01/*'`:

```PowerShell
Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
```

Teraz możesz rozpocząć wyszukiwanie informacji zawartych w dziennikach. Jednak zanim do tego przystąpimy, warto zapoznać się jeszcze z dwoma parametrami polecenia Get-AzureRmDiagnosticSetting:

* Aby wykonać zapytanie o stan ustawień diagnostycznych dla zasobu magazynu kluczy: `Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`
* Aby wyłączyć rejestrowanie dla zasobu magazynu kluczy: `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`

## <a id="interpret"></a>Interpretowanie dzienników usługi Key Vault

Poszczególne obiekty blob są przechowywane jako tekst w formacie obiektu blob JSON. Działanie

```PowerShell
Get-AzureRmKeyVault -VaultName 'contosokeyvault'`
```

zwróci wpis dziennika podobny do przedstawionego poniżej:

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

W poniższej tabeli wymieniono nazwy pól i ich opisy.

| Nazwa pola | Opis |
| --- | --- |
| time |Data i godzina (UTC). |
| resourceId |Identyfikator zasobu usługi Azure Resource Manager W przypadku dzienników usługi Key Vault jest nim zawsze identyfikator zasobu usługi Key Vault. |
| operationName |Nazwa operacji zgodnie z opisem w następnej tabeli. |
| operationVersion |To jest wersja interfejsu API REST zażądana przez klienta. |
| category |W przypadku dzienników usługi Key Vault kategoria AuditEvent jest jedyną dostępną wartością. |
| resultType |Wynik żądania interfejsu API REST. |
| resultSignature |Stan HTTP. |
| resultDescription |Dodatkowy opis wyniku, jeśli jest dostępny. |
| durationMs |Czas potrzebny do obsłużenia żądania interfejsu API REST podany w milisekundach. Nie obejmuje opóźnienia sieci, więc czas zmierzony po stronie klienta może być niezgodny z tym czasem. |
| callerIpAddress |Adres IP klienta, który wysłał żądanie. |
| correlationId |Opcjonalny identyfikator GUID, który klient może przekazać w celu skorelowania dzienników po stronie klienta z dziennikami po stronie usługi (Key Vault). |
| identity |Tożsamość z tokenu, który został przedstawiony podczas przesyłania żądania interfejsu API REST. Jest to zazwyczaj „użytkownik”, „główna nazwa usługi” lub kombinacja „użytkownik+identyfikator appId”, jak w przypadku żądania wynikającego z polecenia cmdlet usługi Azure PowerShell. |
| properties |To pole zawiera różne informacje w zależności od operacji (operationName). W większości przypadków zawiera informacje o kliencie (ciąg agenta użytkownika przekazany przez klienta), dokładny identyfikator URI żądania interfejsu API REST i kod stanu HTTP. Ponadto, gdy w wyniku żądania jest zwracany obiekt (na przykład KeyCreate lub VaultGet), będzie również zawierać identyfikator URI klucza (jako „id”), identyfikator URI magazynu lub identyfikator URI klucza tajnego. |

Wartości pola **operationName** są w formacie ObjectVerb. Na przykład:

* Wszystkie operacje magazynu kluczy mają format „Vault`<action>`”, np. `VaultGet` i `VaultCreate`.
* Wszystkie operacje kluczy mają format „Key`<action>`”, np. `KeySign` i `KeyList`.
* Wszystkie operacje kluczy tajnych mają format „Secret`<action>`”, np. `SecretGet` i `SecretListVersions`.

W poniższej tabeli wymieniono nazwy operacji operationName i odpowiadające im polecenie interfejsu API REST.

| operationName | Polecenie interfejsu API REST |
| --- | --- |
| Authentication |Za pośrednictwem punktu końcowego usługi Azure Active Directory |
| VaultGet |[Pobierz informacje o magazynie kluczy](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| VaultPut |[Utwórz lub zaktualizuj magazyn kluczy](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultDelete |[Usuń magazyn kluczy](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| VaultPatch |[Zaktualizuj magazyn kluczy](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultList |[Utwórz listę wszystkich magazynów kluczy w grupie zasobów](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| KeyCreate |[Utwórz klucz](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| KeyGet |[Pobierz informacje o kluczu](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| KeyImport |[Importuj klucz do magazynu](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| KeyBackup |[Wykonaj kopię zapasową klucza](https://msdn.microsoft.com/library/azure/dn878058.aspx). |
| KeyDelete |[Usuń klucz](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| KeyRestore |[Przywróć klucz](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| KeySign |[Podpisz przy użyciu klucza](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| KeyVerify |[Weryfikuj za pomocą klucza](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| KeyWrap |[Opakuj klucz](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| KeyUnwrap |[Odpakuj klucz](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| KeyEncrypt |[Szyfruj za pomocą klucza](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| KeyDecrypt |[Odszyfruj za pomocą klucza](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| KeyUpdate |[Zaktualizuj klucz](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| KeyList |[Utwórz listę kluczy w magazynie](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| KeyListVersions |[Utwórz listę wersji klucza](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| SecretSet |[Utwórz klucz tajny](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| SecretGet |[Pobierz klucz tajny](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| SecretUpdate |[Zaktualizuj klucz tajny](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| SecretDelete |[Usuń klucz tajny](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| SecretList |[Utwórz listę kluczy tajnych w magazynie](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| SecretListVersions |[Utwórz listę wersji klucza tajnego](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Korzystanie z usługi Log Analytics

Za pomocą rozwiązania Azure Key Vault możesz w usłudze Log Analytics przeglądać dzienniki rozwiązania Azure Key Vault z kategorii AuditEvent. Aby uzyskać więcej informacji, w tym na temat konfigurowania tego rozwiązania, zobacz [Rozwiązanie Azure Key Vault w usłudze Log Analytics](../azure-monitor/insights/azure-key-vault.md). Artykuł ten zawiera również instrukcje w razie potrzeby migracji ze starego rozwiązania Key Vault, które było oferowane w trakcie okresu zapoznawczego usługi Log Analytics, w której najpierw należało skierować dzienniki do konta usługi Azure Storage, a następnie skonfigurować usługę Log Analytics do odczytu z tego konta.

## <a id="next"></a>Następne kroki

Aby zapoznać się z samouczkiem, w którym użyto usługi Azure Key Vault w aplikacji internetowej, zobacz [Use Azure Key Vault from a Web Application](key-vault-use-from-web-application.md) (Używanie usługi Azure Key Vault za pośrednictwem aplikacji internetowej).

Odwołania dotyczące programowania znajdują się w [przewodniku dewelopera usługi Azure Key Vault](key-vault-developers-guide.md).

Aby zapoznać się z listą poleceń cmdlet usługi Azure PowerShell 1.0 dla usługi Azure Key Vault, zobacz artykuł [Azure Key Vault Cmdlets](/powershell/module/azurerm.keyvault/#key_vault) (Polecenia cmdlet w usłudze Azure Key Vault).

Aby znaleźć samouczek dotyczący rotacji kluczy i inspekcji dzienników w usłudze Azure Key Vault, zobacz [Jak skonfigurować usługę Key Vault na potrzeby rotacji i inspekcji typu end-to-end](key-vault-key-rotation-log-monitoring.md).
