---
title: Eksportowanie dziennika aktywności platformy Azure
description: Eksportuj dziennik aktywności platformy Azure do magazynu w celu archiwizacji lub Event Hubs platformy Azure do eksportowania poza platformę Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 33302d7252c56badfed1dc7adea6a4f7cbf961b6
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048252"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Eksportowanie dziennika aktywności platformy Azure do magazynu lub Event Hubs platformy Azure

> [!NOTE]
> Teraz można zbierać dane dziennika aktywności w obszarze roboczym Log Analytics przy użyciu ustawienia diagnostycznego podobnego do sposobu zbierania dzienników zasobów. Zobacz [zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym log Analytics w Azure monitor](activity-log-collect.md).

[Dziennik aktywności platformy Azure](activity-logs-overview.md) zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły w ramach subskrypcji platformy Azure. Oprócz wyświetlania dziennika aktywności w Azure Portal lub kopiowania go do Log Analytics obszaru roboczego, gdzie można analizować z innymi danymi zebranymi przez Azure Monitor, można utworzyć profil dziennika w celu zarchiwizowania dziennika aktywności na koncie usługi Azure Storage lub przesyłania strumienia do niego  Centrum zdarzeń.

## <a name="archive-activity-log"></a>Archiwizowanie dziennika aktywności
Archiwizowanie dziennika aktywności na koncie magazynu jest przydatne, jeśli chcesz przechowywać dane dziennika dłużej niż 90 dni (z pełną kontrolą nad zasadami przechowywania) na potrzeby inspekcji, statycznej analizy lub tworzenia kopii zapasowych. Jeśli musisz tylko zachować zdarzenia przez 90 dni lub mniej, nie musisz konfigurować archiwizowania do konta magazynu, ponieważ zdarzenia dziennika aktywności są przechowywane na platformie Azure przez 90 dni.

## <a name="stream-activity-log-to-event-hub"></a>Przesyłanie strumieniowe dziennika aktywności do centrum zdarzeń
[Azure Event Hubs](/azure/event-hubs/) to usługa przesyłania strumieniowego danych i usługi pozyskiwania zdarzeń, które mogą odbierać i przetwarzać miliony zdarzeń na sekundę. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. W przypadku dziennika aktywności można użyć funkcji przesyłania strumieniowego na dwa sposoby:
* **Przesyłaj strumieniowo systemy rejestrowania i telemetrii innych firm**: w czasie usługa Azure Event Hubs Streaming stanie się mechanizmem potokowym dziennika aktywności w rozwiązaniach rozwiązań Siem i log Analytics innych firm.
* **Utwórz niestandardową platformę telemetrii i rejestrowania**: Jeśli masz już wbudowaną niestandardową platformę telemetrii lub zastanawiasz się, że tworzysz ten, wysoce skalowalny charakter publikowania/subskrybowania Event Hubs umożliwia elastyczne pozyskiwanie dziennika aktywności. 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="storage-account"></a>Konto magazynu
W przypadku archiwizowania dziennika aktywności należy [utworzyć konto magazynu](../../storage/common/storage-quickstart-create-account.md) , jeśli jeszcze go nie masz. Nie należy używać istniejącego konta magazynu, które ma inne niemonitorowane dane, które są w nim przechowywane, dzięki czemu można lepiej kontrolować dostęp do danych monitorowania. Jeśli archiwizowanie dzienników diagnostycznych i metryk na koncie magazynu jest również możliwe, możesz użyć tego samego konta magazynu, aby zachować wszystkie dane monitorowania w centralnej lokalizacji.

Konto magazynu nie musi znajdować się w tej samej subskrypcji co subskrypcja emitująca dzienniki, dopóki użytkownik, który konfiguruje ustawienie, ma dostęp do obu subskrypcji.
> [!NOTE]
>  Obecnie nie można archiwizować danych na koncie magazynu, które znajduje się za bezpieczną siecią wirtualną.

### <a name="event-hubs"></a>Event Hubs
Jeśli wysyłasz dziennik aktywności do centrum zdarzeń, musisz [utworzyć centrum zdarzeń](../../event-hubs/event-hubs-create.md) , jeśli jeszcze go nie masz. Jeśli wcześniej przesyłane strumieniowo zdarzenia dziennika aktywności do tej przestrzeni nazw Event Hubs, centrum zdarzeń zostanie ponownie użyte.

Zasady dostępu współdzielonego definiują uprawnienia, które ma mechanizm przesyłania strumieniowego. Przesyłanie strumieniowe do Event Hubs wymaga uprawnień do zarządzania, wysyłania i nasłuchiwania. Można utworzyć lub zmodyfikować zasady dostępu współdzielonego dla przestrzeni nazw Event Hubs w Azure Portal na karcie Konfiguracja dla przestrzeni nazw Event Hubs.

Aby zaktualizować profil dziennika aktywności w celu uwzględnienia przesyłania strumieniowego, musisz mieć uprawnienie ListKey dla tej reguły autoryzacji Event Hubs. Przestrzeń nazw Event Hubs nie musi znajdować się w tej samej subskrypcji co subskrypcja, która emituje dzienniki, pod warunkiem, że użytkownik, który konfiguruje ustawienie, ma dostęp do obu subskrypcji i obie subskrypcje są w tej samej dzierżawie usługi AAD.

Przesyłanie strumieniowe dziennika aktywności do centrum zdarzeń przez [utworzenie profilu dziennika](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Tworzenie profilu dziennika
Użytkownik definiuje sposób eksportowania dziennika aktywności platformy Azure przy użyciu **profilu dziennika**. Każda subskrypcja platformy Azure może mieć tylko jeden profil dziennika. Te ustawienia można skonfigurować za pomocą opcji **Eksportuj** w bloku dziennika aktywności w portalu. Można je również skonfigurować programowo [przy użyciu interfejsu API REST Azure monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia.

Profil dziennika definiuje następujące elementy.

**Miejsce, w którym ma być wysyłany dziennik aktywności.** Obecnie dostępne opcje to konto magazynu lub Event Hubs.

**Które kategorie zdarzeń powinny być wysyłane.** Znaczenie *kategorii* w profilach dzienników i zdarzeniach dziennika aktywności jest inne. W profilu dziennika *Kategoria* reprezentuje typ operacji (zapis, usuwanie, Akcja). W zdarzeniu dziennika aktywności *Kategoria*"* Właściwość reprezentuje źródło lub typ zdarzenia (na przykład administracja, usługa servicehealth i alert).

**Które regiony (lokalizacje) powinny zostać wyeksportowane.** Należy uwzględnić wszystkie lokalizacje, ponieważ wiele zdarzeń w dzienniku aktywności jest zdarzeniami globalnymi.

**Czas zachowywania dziennika aktywności na koncie magazynu.** Wpisanie wartości zero oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbą dni z zakresu od 1 do 365.

Jeśli zasady przechowywania są ustawione, ale przechowywanie dzienników na koncie magazynu jest wyłączone, zasady przechowywania nie będą miały żadnego efektu. Zasady przechowywania są stosowane dziennie, aby na koniec dnia (UTC), dzienniki w dzień, w którym jest teraz, po przekroczeniu przechowywania zasady zostaną usunięte. Na przykład jeśli masz zasady przechowywania w jeden dzień, na początku dnia już dziś dzienników z wczoraj zanim dnia zostaną usunięte. Proces usuwania rozpoczyna się od północy czasu UTC, ale należy pamiętać, że może upłynąć do 24 godzin dla dzienników są usuwane z konta magazynu.


> [!IMPORTANT]
> Jeśli nie zarejestrowano dostawcy zasobów Microsoft. Insights, podczas tworzenia profilu dziennika może zostać wyświetlony komunikat o błędzie. Zobacz [dostawcy zasobów platformy Azure i typy](../../azure-resource-manager/resource-manager-supported-services.md) , aby zarejestrować tego dostawcę.


### <a name="create-log-profile-using-the-azure-portal"></a>Utwórz profil dziennika przy użyciu Azure Portal

Utwórz lub Edytuj profil dziennika za pomocą opcji **Eksportuj do centrum zdarzeń** w Azure Portal.

1. Z menu **monitor** w Azure Portal wybierz pozycję **Eksportuj do centrum zdarzeń**.

    ![Przycisk Eksportuj w portalu](media/activity-log-export/portal-export.png)

3. W wyświetlonym bloku określ następujące elementy:
   * Regiony ze zdarzeniami do eksportowania. Należy zaznaczyć wszystkie regiony, aby upewnić się, że nie zostaną pominięte najważniejsze zdarzenia, ponieważ dziennik aktywności jest dziennikiem globalnym (nieregionalnym), dlatego większość zdarzeń nie ma skojarzonego regionu. 
   * Jeśli chcesz zapisać na koncie magazynu:
       * Konto magazynu, do którego chcesz zapisać zdarzenia.
       * Liczba dni, przez jaką te zdarzenia mają być przechowywane w magazynie. Ustawienie 0 dni zachowuje dzienniki w nieskończoność.
   * Jeśli chcesz pisać do centrum zdarzeń:
       * Przestrzeń nazw Service Bus, w której chcesz utworzyć centrum zdarzeń na potrzeby przesyłania strumieniowego tych zdarzeń.

     ![Eksportuj blok dziennika aktywności](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Kliknij przycisk **Zapisz** , aby zapisać te ustawienia. Ustawienia są natychmiastowo stosowane do subskrypcji.


### <a name="configure-log-profile-using-powershell"></a>Konfigurowanie profilu dziennika przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Jeśli profil dziennika już istnieje, najpierw musisz usunąć istniejący profil dziennika, a następnie utworzyć nowy.

1. Użyj `Get-AzLogProfile`, aby określić, czy profil dziennika istnieje.  W przypadku istnienia profilu dziennika należy zwrócić uwagę na Właściwość *name* .

1. Użyj `Remove-AzLogProfile`, aby usunąć profil dziennika przy użyciu wartości z właściwości *Nazwa* .

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Użyj `Add-AzLogProfile`, aby utworzyć nowy profil dziennika:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Właściwość | Wymagany | Opis |
    | --- | --- | --- |
    | Nazwa |Yes |Nazwa profilu dziennika. |
    | StorageAccountId |Nie |Identyfikator zasobu konta magazynu, w którym ma zostać zapisany dziennik aktywności. |
    | serviceBusRuleId |Nie |Service Bus Identyfikator reguły dla przestrzeni nazw Service Bus, w której chcesz utworzyć Centra zdarzeń. Jest to ciąg o formacie: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Lokalizacja |Yes |Rozdzielana przecinkami lista regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. |
    | RetentionInDays |Yes |Liczba dni, przez jaką zdarzenia mają być przechowywane na koncie magazynu, z zakresu od 1 do 365. Wartość zero przechowuje dzienniki w nieskończoność. |
    | Kategoria |Nie |Rozdzielana przecinkami lista kategorii zdarzeń, które mają być zbierane. Możliwe wartości to _Write_, _delete_i _Action_. |

### <a name="example-script"></a>Przykładowy skrypt
Poniżej znajduje się przykładowy skrypt programu PowerShell służący do tworzenia profilu dziennika, który zapisuje dziennik aktywności zarówno do konta magazynu, jak i do centrum zdarzeń.

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


### <a name="configure-log-profile-using-azure-cli"></a>Konfigurowanie profilu dziennika przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli profil dziennika już istnieje, należy najpierw usunąć istniejący profil dziennika, a następnie utworzyć nowy profil dziennika.

1. Użyj `az monitor log-profiles list`, aby określić, czy profil dziennika istnieje.
2. Użyj `az monitor log-profiles delete --name "<log profile name>`, aby usunąć profil dziennika przy użyciu wartości z właściwości *Nazwa* .
3. Użyj `az monitor log-profiles create`, aby utworzyć nowy profil dziennika:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Właściwość | Wymagany | Opis |
    | --- | --- | --- |
    | name |Yes |Nazwa profilu dziennika. |
    | storage-account-id |Yes |Identyfikator zasobu konta magazynu, do którego mają zostać zapisane dzienniki aktywności. |
    | locations |Yes |Rozdzielana spacjami lista regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. Listę wszystkich regionów dla subskrypcji można wyświetlić przy użyciu `az account list-locations --query [].name`. |
    | days |Yes |Liczba dni przechowywania zdarzeń między 1 a 365. Wartość zerowa spowoduje przechowywanie dzienników w nieskończoność (w nieskończoność).  Jeśli wartość jest równa zero, wartość parametru enabled powinna być równa false. |
    |dostępny | Yes |Wartość TRUE lub False.  Służy do włączania lub wyłączania zasad przechowywania.  W przypadku wartości true wartość parametru Days musi być większa niż 0.
    | categories |Yes |Rozdzielana spacjami lista kategorii zdarzeń, które powinny być zbierane. Możliwe wartości to Write, DELETE i Action. |



## <a name="activity-log-schema"></a>Schemat dziennika aktywności
Niezależnie od tego, czy jest wysyłany do usługi Azure Storage, czy centrum zdarzeń, dane dziennika aktywności będą zapisywane w formacie JSON przy użyciu następującego formatu.


> Format danych dziennika aktywności zapisany na koncie magazynu został zmieniony na wiersze JSON na lis. 1, 2018. Aby uzyskać szczegółowe informacje na temat tego formatu, zobacz [Przygotowywanie do zmiany formatu do Azure monitor dzienników diagnostycznych archiwizowanych na koncie magazynu](diagnostic-logs-append-blobs.md) .

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
Elementy w tym formacie JSON są opisane w poniższej tabeli.

| Nazwa elementu | Opis |
| --- | --- |
| time |Sygnatura czasowa, gdy zdarzenie zostało wygenerowane przez usługę platformy Azure przetwarzające żądanie odpowiadające zdarzeniu. |
| resourceId |Identyfikator zasobu zasobu, którego dotyczy problem. |
| operationName |Nazwa operacji. |
| category |Kategoria akcji, np. Zapis, Odczyt, akcja. |
| resultType |Typ wyniku, np. Sukces, Niepowodzenie, rozpoczęcie |
| resultSignature |Zależy od typu zasobu. |
| durationMs |Czas trwania działania (w milisekundach) |
| callerIpAddress |Adres IP użytkownika, który wykonał operację, oświadczenie nazwy UPN lub oświadczenie SPN na podstawie dostępności. |
| correlationId |Zazwyczaj identyfikator GUID w formacie ciągu. Zdarzenia, które współużytkują identyfikator korelacji, należy do tej samej akcji Uber. |
| identity |Obiekt BLOB JSON opisujący autoryzację i oświadczenia. |
| authorization |Obiekt BLOB właściwości RBAC zdarzenia. Zazwyczaj obejmuje właściwości "Action", "role" i "Scope". |
| poziom |Poziom zdarzenia. Jedna z następujących wartości: _krytyczny_, _błąd_, _Ostrzeżenie_, _informacyjny_i _pełny_ |
| location |Region, w którym wystąpiła lokalizacja (lub globalna). |
| properties |Zestaw par `<Key, Value>` (tj. Dictionary) opisujących szczegóły zdarzenia. |

> [!NOTE]
> Właściwości i użycie tych właściwości mogą się różnić w zależności od zasobu.



## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dzienniku aktywności](../../azure-resource-manager/resource-group-audit.md)
* [Zbierz dziennik aktywności do dzienników Azure Monitor](activity-log-collect.md)
