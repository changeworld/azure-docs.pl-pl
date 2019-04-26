---
title: Archiwizowanie dziennika aktywności platformy Azure
description: Archiwizuj dziennik aktywności platformy Azure do długoterminowego przechowywania danych na koncie magazynu.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: b6009471048232b52020e4bef6272ed8cb1bd35b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60345857"
---
# <a name="archive-the-azure-activity-log"></a>Archiwizowanie dziennika aktywności platformy Azure
W tym artykule pokazano, jak można użyć witryny Azure portal, poleceń cmdlet programu PowerShell lub Wieloplatformowego interfejsu wiersza polecenia do archiwizacji swoje [ **dziennika aktywności platformy Azure** ](../../azure-monitor/platform/activity-logs-overview.md) na koncie magazynu. Ta opcja jest przydatna, jeśli chcesz przechowywać więcej niż 90 dni (z pełną kontrolę nad zasady przechowywania) inspekcji, analizę statyczną lub kopii zapasowej dziennika aktywności. Jeśli musisz zachować zdarzenia przez 90 dni lub mniej nie trzeba skonfigurować archiwizowanie na koncie magazynu, ponieważ zdarzenia dziennika aktywności są przechowywane na platformie Azure przez 90 dni bez włączania archiwizacji.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem należy [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md) do której można Archiwizuj dziennik aktywności. Zdecydowanie zaleca się, że nie używasz istniejącego konta magazynu, który ma inne — monitorowanie danych przechowywanych w nim, dzięki czemu można lepiej kontrolować dostęp do danych monitorowania. Jednakże jeśli są również archiwizowanie dzienniki diagnostyczne i metryki na konto magazynu, rozsądne może okazać się zachować wszystkie dane monitorowania w centralnej lokalizacji za pomocą tego konta magazynu dla także dziennik aktywności. Konto magazynu nie musi znajdować się w tej samej subskrypcji co emitowane dzienniki, tak długo, jak użytkownik, który konfiguruje ustawienie ma odpowiedni dostęp RBAC do obu subskrypcji subskrypcji.

## <a name="log-profile"></a>Profilu dziennika
Aby Archiwizowanie dziennika aktywności przy użyciu dowolnej z poniższych metod, należy ustawić **profilu dziennika** dla subskrypcji. Profil dziennika określa typ zdarzenia, które są przechowywane lub przesyłane strumieniowo i dane wyjściowe — magazynu konta i/lub zdarzenia koncentratora. Definiuje również zasady przechowywania (liczba dni przechowywania) dla zdarzeń przechowywane na koncie magazynu. Jeśli zasady przechowywania są ustawione na wartość zero, zdarzenia są przechowywane przez czas nieokreślony. W przeciwnym razie to można ustawić dowolną wartość z zakresu od 1 do 365. Zasady przechowywania są zastosowane dziennie, dzięki czemu na koniec dnia (UTC), dzienniki w dzień, w którym jest teraz, po przekroczeniu przechowywania zasady zostaną usunięte. Na przykład jeśli masz zasady przechowywania w jeden dzień, na początku dnia już dziś dzienników z wczoraj zanim dnia zostaną usunięte. Proces usuwania rozpoczyna się od północy czasu UTC, ale należy pamiętać, że może upłynąć do 24 godzin dla dzienników są usuwane z konta magazynu. [Możesz dowiedzieć się więcej o dzienniku tutaj profile](../../azure-monitor/platform/activity-logs-overview.md#export-the-activity-log-with-a-log-profile). 

## <a name="archive-the-activity-log-using-the-portal"></a>Archiwizowanie dziennika aktywności przy użyciu portalu
1. W portalu, kliknij przycisk **dziennika aktywności** łącze w okienku nawigacji po lewej stronie. Jeśli nie widzisz łącza dla dziennika aktywności kliknij **wszystkich usług** najpierw połączyć.
   
    ![Przejdź do bloku dziennika aktywności](media/archive-activity-log/activity-logs-portal-navigate-v2.png)
2. W górnej części bloku kliknij **Eksportuj do Centrum zdarzeń**.
   
    ![Przycisk Eksportuj](media/archive-activity-log/activity-logs-portal-export-v2.png)
3. W bloku, który pojawia się pole wyboru dla **Eksportuj na konto magazynu** i wybierz konto magazynu.
   
    ![Ustaw konto magazynu](media/archive-activity-log/act-log-portal-export-blade.png)
4. Za pomocą suwaka lub pola tekstowego, zdefiniuj liczbę dni (od 0 do 365), dla których zdarzenia dziennika aktywności powinna być przechowywana w koncie magazynu. Jeśli chcesz mieć swoje dane utrwalone w ramach konta magazynu przez czas nieokreślony, należy wybrać wartość zero. Jeśli musisz wprowadzić liczbę dni, więcej niż 365, należy użyć metod programu PowerShell lub interfejsu wiersza polecenia, opisane poniżej.
5. Kliknij pozycję **Zapisz**.

## <a name="archive-the-activity-log-via-powershell"></a>Archiwizowanie dziennika aktywności przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your storage account belongs to>"
   $storageAccountName = "<your storage account name>"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -StorageAccountId $storageAccountId
   ```

| Właściwość | Wymagane | Opis |
| --- | --- | --- |
| StorageAccountId |Yes |Identyfikator zasobu konta magazynu, do którego powinny być zapisywane dzienniki aktywności. |
| Lokalizacje |Yes |Rozdzielana przecinkami lista regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. Można wyświetlić listę wszystkich regionów dla subskrypcji przy użyciu `(Get-AzLocation).Location`. |
| RetentionInDays |Nie |Liczba dni dla zdarzenia, które powinny zostać zachowane, od 1 do 365. Wartość zero zapisuje dzienniki na czas nieokreślony (nieskończoność). |
| Categories |Nie |Rozdzielana przecinkami lista kategorie zdarzeń, które powinny być zbierane. Możliwe wartości to zapis, usuwanie i akcji.  Jeśli nie zostanie podana, następnie wszystkie możliwe wartości są uznawane za |

## <a name="archive-the-activity-log-via-cli"></a>Archiwizowanie dziennika aktywności przy użyciu interfejsu wiersza polecenia

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --storage-account-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>"
   ```

| Właściwość | Wymagane | Opis |
| --- | --- | --- |
| name |Yes |Nazwa profilu dziennika. |
| storage-account-id |Yes |Identyfikator zasobu konta magazynu, do którego powinny być zapisywane dzienniki aktywności. |
| locations |Yes |Rozdzielonych spacjami listę regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. Można wyświetlić listę wszystkich regionów dla subskrypcji przy użyciu `az account list-locations --query [].name`. |
| dni |Yes |Liczba dni dla zdarzenia, które powinny zostać zachowane, od 1 do 365. Wartość zero będzie przechowywać dzienniki na czas nieokreślony (nieskończoność).  Jeśli zero, następnie włączone parametru powinna być ustawiona na wartość true. |
|enabled | Yes |Wartość TRUE lub False.  Używane, aby włączyć lub wyłączyć zasady przechowywania.  W przypadku opcji True parametr liczby dni musi być wartością większą niż 0.
| categories |Yes |Rozdzielonej spacjami listy kategorie zdarzeń, które powinny być zbierane. Możliwe wartości to zapis, usuwanie i akcji. |

## <a name="storage-schema-of-the-activity-log"></a>Schemat magazynu dziennika aktywności
Po skonfigurowaniu archiwizacji, zaraz po wystąpieniu zdarzenia dziennika aktywności na koncie magazynu zostanie utworzony kontener magazynu. Obiekty BLOB w kontenerze postępuj zgodnie z tej samej konwencji nazewnictwa w dziennikach aktywności i dzienników diagnostycznych, jak pokazano poniżej:

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Na przykład może być nazwa obiektu blob:

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Każdy obiekt blob PT1H.json zawiera obiekt blob JSON, zdarzenia, które wystąpiły w ciągu godziny określonej w adresie URL obiektu blob (np. h = 12). Zdarzenia występujące w danej chwili są na bieżąco dołączane do pliku PT1H.json. Wartość minut (m = 00) jest zawsze 00, ponieważ zdarzenia dziennika aktywności są dzielone na poszczególne obiekty BLOB na godzinę.

W pliku PT1H.json każde zdarzenie jest przechowywane w tablicy "rekordy", zgodnie z następującym formacie:

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
| Autoryzacja |Obiekt blob RBAC właściwości zdarzenia. Zazwyczaj zawiera właściwości "action", "roli" i "scope". |
| poziom |Poziom zdarzenia. Jeden z następujących wartości: "Krytyczne", "Error", "Ostrzeżenie", "Informacyjne" i "Pełne" |
| location |Region, w którym wystąpił lokalizacji (lub globalnego). |
| properties |Zestaw `<Key, Value>` pary (tj. Słownik), opisujący szczegóły zdarzenia. |

> [!NOTE]
> Właściwości i użycia tych właściwości może się różnić zależnie od zasobu.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* [Pobierz obiekty BLOB na potrzeby analizy](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Stream dziennika aktywności do usługi Event Hubs](../../azure-monitor/platform/activity-logs-stream-event-hubs.md)
* [Dowiedz się więcej o dzienniku aktywności](../../azure-monitor/platform/activity-logs-overview.md)


