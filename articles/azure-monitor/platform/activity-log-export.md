---
title: Eksportuj Dziennik aktywności platformy Azure
description: Eksportuj Dziennik aktywności platformy Azure do magazynu dla usługi Event Hubs archiwizacji lub na platformie Azure eksportowania spoza platformy Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: acf2526e79519e610614dc5217efbfe5e327b90f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248147"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Eksportuj Dziennik aktywności usługi Azure storage lub Azure Event Hubs
[Dziennika aktywności platformy Azure](activity-logs-overview.md) zapewnia wgląd w zdarzenia na poziomie subskrypcji, które miały miejsce w ramach subskrypcji platformy Azure. Oprócz wyświetlania dziennika aktywności w witrynie Azure portal lub skopiować go do obszaru roboczego usługi Log Analytics, gdzie mogą być analizowane za pomocą pozostałymi danymi zebranymi przez usługi Azure Monitor, można utworzyć profil dziennika Archiwizowanie dziennika aktywności na koncie usługi Azure storage lub go do usługi stream  Centrum zdarzeń.

## <a name="archive-activity-log"></a>Archiwizowanie dziennika aktywności
Archiwizowanie dziennika aktywności na koncie magazynu jest przydatne, jeśli chcesz przechowywać dane dłużej niż 90 dni (z pełną kontrolę nad zasady przechowywania) inspekcji, analizę statyczną lub kopii zapasowej dziennika. Jeśli musisz zachować zdarzenia przez 90 dni lub mniej nie trzeba skonfigurować archiwizowanie na koncie magazynu, ponieważ zdarzenia dziennika aktywności są przechowywane na platformie Azure przez 90 dni.

## <a name="stream-activity-log-to-event-hub"></a>Stream dzienników aktywności do Centrum zdarzeń
[Usługa Azure Event Hubs](/azure/event-hubs/) danych przesyłanych strumieniowo platformy i zdarzeń Usługa pozyskiwania, która może odbierać i przetwarzać miliony zdarzeń na sekundę. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Można na przykład możliwość przesyłania strumieniowego dla dziennika aktywności na dwa sposoby są następujące:
* **Stream z systemami innych firm rejestrowania i dane telemetryczne**: Wraz z upływem czasu przesyłania strumieniowego usługi Azure Event Hubs staną się mechanizm dziennika rozwiązań do analizy i przekazać dziennik aktywności do rozwiązania Siem innych firm.
* **Tworzenie niestandardowej telemetrii i rejestrowania platformy**: Jeśli już masz platformy niestandardowej telemetrii lub są myśleć o zbudowania jej, wysoce skalowalna publikowania/subskrybowania rodzaj usługi Event Hubs umożliwia elastyczne pozyskiwania dziennika aktywności. 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="storage-account"></a>Konto magazynu
Jeśli masz Archiwizowanie dziennika aktywności, musisz [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md) Jeśli nie masz jeszcze jeden. Nie należy używać istniejącego konta magazynu, który ma inne — monitorowanie danych przechowywanych w nim, dzięki czemu można lepiej kontrolować dostęp do danych monitorowania. Jeśli jednak można zachować wszystkie dane monitorowania w centralnej lokalizacji za pomocą tego samego konta magazynu, są archiwizacji dzienniki diagnostyczne i metryki na koncie magazynu.

Konto magazynu nie musi znajdować się w tej samej subskrypcji co emitowane dzienniki, tak długo, jak użytkownik, który konfiguruje ustawienie ma odpowiedni dostęp RBAC do obu subskrypcji subskrypcji.
> [!NOTE]
>  Obecnie nie można zarchiwizować dane na koncie magazynu, który znajduje się za zabezpieczonej sieci wirtualnej.

### <a name="event-hubs"></a>Event Hubs
Jeśli dziennik aktywności jest wysyłana do Centrum zdarzeń, a następnie należy [Tworzenie Centrum zdarzeń](../../event-hubs/event-hubs-create.md) Jeśli nie masz jeszcze jeden. Jeśli wcześniej przesyłane strumieniowo zdarzenia dziennika aktywności do tej przestrzeni nazw usługi Event Hubs tego Centrum zdarzeń będzie ponownie.

Zasady dostępu współdzielonego definiuje uprawnienia, które ma mechanizm przesyłania strumieniowego. Przesyłanie strumieniowe do usługi Event Hubs musi mieć uprawnienia do zarządzania, wysyłania i nasłuchiwania. Można utworzyć lub zmodyfikować zasady dostępu współdzielonego dla przestrzeni nazw usługi Event Hubs w witrynie Azure portal, na karcie Konfigurowanie dla przestrzeni nazw usługi Event Hubs.

Aby zaktualizować profil dziennika dziennika aktywności do uwzględnienia, przesyłanie strumieniowe, musi mieć uprawnienie ListKey, w tym reguły autoryzacji usługi Event Hubs. Przestrzeń nazw usługi Event Hubs nie musi znajdować się w tej samej subskrypcji co subskrypcję, która jest emitowane dzienniki, tak długo, jak użytkownik, który konfiguruje ustawienie ma odpowiednią rolę RBAC dostęp do obu subskrypcji i obu subskrypcji znajdują się w tej samej dzierżawie usługi AAD.

Stream dziennika aktywności do Centrum zdarzeń przez [tworzenia profilu dziennika](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Tworzenie profilu dziennika
Zdefiniuj, jak dziennik aktywności platformy Azure jest wyeksportowane przy użyciu **profil dziennika**. Każda subskrypcja platformy Azure może mieć tylko jeden profil dziennika. Te ustawienia mogą być konfigurowane przez **wyeksportować** opcji w bloku dziennika aktywności w portalu. Również mogą być konfigurowane programowo [przy użyciu interfejsu API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), w przypadku poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia.

Profil dziennika definiuje następujące czynności.

**Gdzie mają być wysyłane w dzienniku aktywności.** Obecnie dostępne opcje to konto magazynu lub centrów zdarzeń.

**Kategorie zdarzeń, które mają być wysyłane.** Znaczenie *kategorii* w profile dziennika i dziennika działań różni się zdarzenia. W profilu dziennika *kategorii* reprezentuje typ operacji (zapis, usuwanie i akcji). W przypadku dziennika aktywności *kategorii*"* właściwość reprezentuje źródło lub typ zdarzenia (na przykład administracji ServiceHealth i alertu).

**Które regiony (lokalizacje) powinny być wyeksportowane.** Należy uwzględnić wszystkie lokalizacje, ponieważ wiele zdarzeń w dzienniku aktywności są zdarzenia globalne.

**Jak długo dziennika aktywności powinny być przechowywane na koncie magazynu.** Wpisanie wartości zero oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbę dni z zakresu od 1 do 2147483647.

Jeśli ustawiono zasady przechowywania, ale przechowywania dzienników na koncie magazynu jest wyłączona, zasady przechowywania miało żadnego efektu. Zasady przechowywania są stosowane dziennie, aby na koniec dnia (UTC), dzienniki w dzień, w którym jest teraz, po przekroczeniu przechowywania zasady zostaną usunięte. Na przykład jeśli masz zasady przechowywania w jeden dzień, na początku dnia już dziś dzienników z wczoraj zanim dnia zostaną usunięte. Proces usuwania rozpoczyna się od północy czasu UTC, ale należy pamiętać, że może upłynąć do 24 godzin dla dzienników są usuwane z konta magazynu.



> [!WARNING]
> Format danych dziennika w ramach konta magazynu zmieniony na JSON wierszy od 1 listopada 2018 r. [W tym artykule znajdziesz opis skutków tej zmiany oraz instrukcje aktualizacji narzędzi w celu zapewnienia obsługi nowego formatu.](diagnostic-logs-append-blobs.md)
>
>

### <a name="create-log-profile-using-the-azure-portal"></a>Utwórz profil dziennika przy użyciu witryny Azure portal

Tworzenie lub edytowanie profilu dziennika z **Eksportuj do Centrum zdarzeń** opcji w witrynie Azure portal.

1. Z **Monitor** w witrynie Azure portal, wybierz opcję menu **Eksportuj do Centrum zdarzeń**.

    ![Przycisk Eksportuj w portalu](media/activity-log-export/portal-export.png)

3. W wyświetlonym bloku określ następujące ustawienia:
   * Regiony ze zdarzeniami do wyeksportowania. Należy wybrać we wszystkich regionach, aby upewnić się, nie przegap kluczowych zdarzeń dziennika aktywności jest globalne dziennika (inne niż regionalne), a więc większość zdarzeń ma region skojarzonych z nimi. 
   * Jeśli chcesz zapisać do konta magazynu:
       * Konto magazynu, do której chcesz zapisać zdarzenia.
       * Liczba dni, aby zachować te zdarzenia w magazynie. Ustawienie wartości 0 dni, które zawsze zachowuje dzienniki.
   * Jeśli chcesz zapisać do Centrum zdarzeń:
       * Namespace usługi Service Bus, w którym chcesz Centrum zdarzeń do utworzenia tych zdarzeń do przesyłania strumieniowego.

     ![Eksportuj Dziennik aktywności bloku](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Kliknij przycisk **Zapisz** można zapisać tych ustawień. Ustawienia są natychmiastowo stosowane do subskrypcji.


### <a name="configure-log-profile-using-powershell"></a>Konfigurowanie profilu dziennika przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Jeśli istnieje już profil dziennika, należy najpierw usunąć istniejący profil dziennika, a następnie utwórz nową.

1. Użyj `Get-AzLogProfile` Aby ustalić, czy istnieje już profil dziennika.  Jeśli istnieje profil dziennika, weź pod uwagę *nazwa* właściwości.

1. Użyj `Remove-AzLogProfile` do usunięcia profilu dziennika, używając wartości z *nazwa* właściwości.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Użyj `Add-AzLogProfile` Aby utworzyć nowy profil dziennika:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Właściwość | Wymagane | Opis |
    | --- | --- | --- |
    | Name |Yes |Nazwa profilu dziennika. |
    | StorageAccountId |Nie |Identyfikator zasobu konta magazynu, w którym ma zostać zapisany w dzienniku aktywności. |
    | serviceBusRuleId |Nie |Identyfikator reguły usługi Service Bus dla przestrzeni nazw usługi Service Bus chcesz mieć centra zdarzeń utworzonych w. Jest to ciąg w formacie: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Lokalizacja |Yes |Rozdzielana przecinkami lista regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. |
    | RetentionInDays |Yes |Liczba dni, dla których zdarzenia ma być przechowywana na koncie magazynu, od 1 do 2147483647. Dzienniki na wartość zero są przechowywane w nieskończoność. |
    | Category |Nie |Rozdzielana przecinkami lista kategorie zdarzeń, które powinny być zbierane. Możliwe wartości to _zapisu_, _Usuń_, i _akcji_. |

### <a name="example-script"></a>Przykładowy skrypt
Poniżej przedstawiono przykładowy skrypt programu PowerShell, aby utworzyć profil dziennika, który zapisuje dziennik aktywności do obu magazynu konta i Centrum zdarzeń.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Konfigurowanie profilu dziennika przy użyciu wiersza polecenia platformy Azure

Jeśli istnieje już profil dziennika, należy najpierw usunąć istniejący profil dziennika, a następnie utwórz nowy profil dziennika.

1. Użyj `az monitor log-profiles list` Aby ustalić, czy istnieje już profil dziennika.
2. Użyj `az monitor log-profiles delete --name "<log profile name>` do usunięcia profilu dziennika, używając wartości z *nazwa* właściwości.
3. Użyj `az monitor log-profiles create` Aby utworzyć nowy profil dziennika:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Właściwość | Wymagane | Opis |
    | --- | --- | --- |
    | name |Tak |Nazwa profilu dziennika. |
    | storage-account-id |Tak |Identyfikator zasobu konta magazynu, do którego powinny być zapisywane dzienniki aktywności. |
    | locations |Tak |Rozdzielonych spacjami listę regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. Można wyświetlić listę wszystkich regionów dla subskrypcji przy użyciu `az account list-locations --query [].name`. |
    | days |Tak |Liczba dni dla zdarzenia, które powinny zostać zachowane, od 1 do 365. Wartość zero będzie przechowywać dzienniki na czas nieokreślony (nieskończoność).  Jeśli zero, następnie włączone parametru powinna być ustawiona na wartość true. |
    |enabled | Tak |Wartość TRUE lub False.  Używane, aby włączyć lub wyłączyć zasady przechowywania.  W przypadku opcji True parametr liczby dni musi być wartością większą niż 0.
    | categories |Yes |Rozdzielonej spacjami listy kategorie zdarzeń, które powinny być zbierane. Możliwe wartości to zapis, usuwanie i akcji. |



## <a name="activity-log-schema"></a>Schemat dziennika aktywności
Czy wysyłane do usługi Azure storage lub Centrum zdarzeń, danych dziennika aktywności będą zapisywane do formatu JSON o następującym formacie.

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```
W poniższej tabeli opisano elementy w tym pliku JSON.

| Nazwa elementu | Opis |
| --- | --- |
| time |Sygnatura czasowa podczas generowania zdarzenia według usługi platformy Azure, przetwarzanie żądania odpowiadające zdarzenie. |
| resourceId |Identyfikator zasobu zasób objęty wpływem. |
| operationName |Nazwa operacji. |
| category |Kategoria działania, np. Zapisu, odczytu, akcja. |
| resultType |Typ wyniku, np. Sukces, Niepowodzenie, rozpoczęcie |
| resultSignature |Zależy od typu zasobu. |
| durationMs |Czas trwania działania (w milisekundach) |
| callerIpAddress |Adres IP użytkownika, który wykonał operację, oświadczenia nazwy UPN lub nazwy SPN oświadczenia na podstawie dostępności. |
| correlationId |Zazwyczaj identyfikator GUID w formacie ciągu. Zdarzenia, które mają identyfikator korelacji należą do tej samej akcji uber. |
| identity |Obiekt blob JSON opisujące autoryzację i oświadczenia. |
| authorization |Obiekt blob RBAC właściwości zdarzenia. Zazwyczaj zawiera właściwości "action", "roli" i "scope". |
| poziom |Poziom zdarzenia. Jeden z następujących wartości: _Krytyczne_, _błąd_, _ostrzeżenie_, _informacyjny_, i _pełne_ |
| location |Region, w którym wystąpił lokalizacji (lub globalnego). |
| properties |Zestaw `<Key, Value>` pary (tj. Słownik), opisujący szczegóły zdarzenia. |

> [!NOTE]
> Właściwości i użycia tych właściwości może się różnić zależnie od zasobu.



## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o dzienniku aktywności](../../azure-resource-manager/resource-group-audit.md)
* [Zbieranie dzienników aktywności do dzienników usługi Azure Monitor](activity-log-collect.md)
