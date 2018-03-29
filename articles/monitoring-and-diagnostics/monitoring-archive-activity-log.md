---
title: Archiwizowanie dziennika aktywności platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak archiwum dziennik aktywności platformy Azure w celu przechowywania długoterminowego na koncie magazynu.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: johnkem
ms.openlocfilehash: 1ee634b3acf0fa8815b69aef21e6213aee636ce1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="archive-the-azure-activity-log"></a>Archiwum dziennik aktywności platformy Azure
W tym artykule zostanie przedstawiony sposób można użyć portalu Azure, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia i Platform do archiwizacji Twojej [ **dziennika aktywności platformy Azure** ](monitoring-overview-activity-logs.md) na koncie magazynu. Ta opcja jest przydatna, jeśli chcesz zachować dłużej niż 90 dni (z pełną kontrolę nad zasady przechowywania) inspekcji, analizę statyczną lub kopii zapasowej dziennika aktywności. Jeśli musisz zachować zdarzeń przez 90 dni lub mniej nie trzeba skonfigurować archiwizacji na konto magazynu, ponieważ zdarzenia dziennika aktywności są przechowywane na platformie Azure przez 90 dni bez włączania archiwizacji.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem należy [Utwórz konto magazynu](../storage/common/storage-create-storage-account.md#create-a-storage-account) do której można archiwizować dziennik aktywności. Zdecydowanie zaleca się, że nie należy używać istniejącego konta magazynu ma inne — monitorowanie dane w niej przechowywane, dzięki czemu można lepiej kontrolować dostęp do danych monitorowania. Jednak jeśli są również archiwizowanie dzienników diagnostycznych i metryki na konto magazynu, rozsądne może okazać na potrzeby tego konta magazynu dziennika działań oraz wszystkie dane monitorowania są przechowywane w centralnej lokalizacji. Konto magazynu, którego używasz, musi być konto magazynu ogólnego przeznaczenia, a nie konta magazynu obiektów blob. Konto magazynu nie ma w tej samej subskrypcji co subskrypcji emitowanie dzienniki, dopóki użytkownik, który konfiguruje ustawienia ma odpowiedni dostęp RBAC do obu subskrypcji.

## <a name="log-profile"></a>Profil dziennika
Archiwizowanie dziennika aktywności przy użyciu dowolnej z poniższych metod, możesz ustawić **profilu dziennika** dla subskrypcji. Profil dziennika definiuje typ zdarzenia, które są zapisywane lub przesyłane strumieniowo i dane wyjściowe — magazynu konta i/lub zdarzenia koncentratora. Definiuje również zasady przechowywania (liczba dni przechowywania) dla zdarzenia zapisane na koncie magazynu. Jeśli zasady przechowywania są ustawione na zero, zdarzenia są przechowywane w nieskończoność. W przeciwnym razie to można ustawić dowolną wartość z zakresu od 1 do 2147483647. Zasady przechowywania są zastosowane na dni, więc pod koniec dnia (UTC), dzienniki od dnia, która jest teraz poza przechowywania zasady zostaną usunięte. Na przykład jeśli masz zasady przechowywania jeden dzień na początku dnia dzisiaj dzienniki na wczoraj zanim dzień zostaną usunięte. [Więcej informacje dziennika tutaj profile](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). 

## <a name="archive-the-activity-log-using-the-portal"></a>Archiwizowanie dziennika aktywności przy użyciu portalu
1. W portalu kliknij **dziennik aktywności** łącze nawigacji po lewej stronie. Jeśli nie widzisz łącze dla dziennika aktywności, kliknij przycisk **wszystkie usługi** najpierw łącza.
   
    ![Przejdź do bloku dziennika aktywności](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. W górnej części bloku, kliknij **wyeksportować**.
   
    ![Przycisk Eksportuj](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. W wyświetlonym bloku pole wyboru dla **wyeksportować do konta magazynu** i wybierz konto magazynu.
   
    ![Ustaw konto magazynu](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. Za pomocą suwaka lub pola tekstowego, określić liczbę dni, dla których zdarzenia dziennika aktywności powinna być przechowywana w koncie magazynu. Jeśli preferujesz dane utrwalone w ramach konta magazynu przez czas nieokreślony, ustaw ten numer od zera.
5. Kliknij pozycję **Zapisz**.

## <a name="archive-the-activity-log-via-powershell"></a>Archiwizowanie dziennika aktywności za pomocą programu PowerShell
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| Właściwość | Wymagane | Opis |
| --- | --- | --- |
| StorageAccountId |Nie |Identyfikator zasobu konta magazynu, do której ma zostać zapisany Dzienniki aktywności. |
| Lokalizacje |Yes |Rozdzielana przecinkami lista regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. Można wyświetlić listę wszystkich regionach [, przechodząc na stronę tej strony](https://azure.microsoft.com/en-us/regions) lub za pomocą [interfejsu API REST zarządzania Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| retentionInDays |Yes |Liczba dni dla zdarzenia, które mają być przechowywane, od 1 do 2147483647. Wartość zero przechowuje dzienniki w nieskończoność (zawsze). |
| Kategorie |Yes |Rozdzielana przecinkami lista kategorii zdarzeń, które powinny być zbierane. Możliwe wartości to zapisu, usuwania i akcji. |

## <a name="archive-the-activity-log-via-cli"></a>Archiwizowanie dziennika aktywności za pomocą interfejsu wiersza polecenia
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| Właściwość | Wymagane | Opis |
| --- | --- | --- |
| name |Yes |Nazwa profilu dziennika. |
| storageId |Nie |Identyfikator zasobu konta magazynu, do której ma zostać zapisany Dzienniki aktywności. |
| Lokalizacje |Yes |Rozdzielana przecinkami lista regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. Można wyświetlić listę wszystkich regionach [, przechodząc na stronę tej strony](https://azure.microsoft.com/en-us/regions) lub za pomocą [interfejsu API REST zarządzania Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| retentionInDays |Yes |Liczba dni dla zdarzenia, które mają być przechowywane, od 1 do 2147483647. Wartość zero będą przechowywane dzienniki nieskończoność (zawsze). |
| kategorie |Yes |Rozdzielana przecinkami lista kategorii zdarzeń, które powinny być zbierane. Możliwe wartości to zapisu, usuwania i akcji. |

## <a name="storage-schema-of-the-activity-log"></a>Schemat magazynu dziennika aktywności
Po skonfigurowaniu archiwizacji, kontenera magazynu zostanie utworzony na koncie magazynu zaraz po wystąpieniu zdarzenia dziennika aktywności. Obiekty BLOB w kontenerze należy wykonać ten sam format dziennika aktywności i dzienników diagnostycznych. Struktura tych obiektów blob jest:

> szczegółowe informacje operacyjne dzienniki/nazwa-= domyślne/resourceId = / SUBSKRYPCJI / {identyfikator subskrypcji} / y = {czterocyfrowy rok liczbowych} / m = {dwucyfrowe liczbowych month} / d = {dwucyfrowe liczbą dzień} / h = {dwucyfrowe 24-godzinnym hour}/m=00/PT1H.json
> 
> 

Na przykład może być nazwa obiektu blob:

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
> 
> 

Każdy obiekt blob PT1H.json zawiera obiektu blob JSON zdarzeń, które wystąpiły w ciągu godziny określonego w adresie URL obiektu blob (np. h = 12). Podczas obecny godzinę zdarzenia są dołączane do pliku PT1H.json miarę ich występowania. Wartości minutowe (m = 00) jest zawsze 00, ponieważ dziennik zdarzeń są podzielone na poszczególne obiekty BLOB na godzinę.

W pliku PT1H.json każdego zdarzenia są przechowywane w tablicy "rekordy", po tym formacie:

```
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
| time |Sygnatura czasowa po zdarzeniu został wygenerowany przez usługę Azure przetwarzania żądania odpowiednie zdarzenie. |
| resourceId |Identyfikator zasobu ryzyko zasobu. |
| operationName |Nazwa operacji. |
| category |Kategoria akcji, np. Zapis, Odczyt, akcji. |
| resultType |Typ wyniku, np. Sukces, Niepowodzenie, Start |
| resultSignature |Zależy od typu zasobu. |
| durationMs |Czas trwania działania w milisekundach |
| callerIpAddress |Adres IP użytkownika, który wykonał operację, oświadczenia UPN lub nazwy SPN oświadczenia na podstawie dostępności. |
| correlationId |Zazwyczaj identyfikator GUID w postaci ciągu. Zdarzenia, które mają correlationId należą do tego samego działania pełny. |
| identity |Obiekt blob JSON opisujące autoryzacji i oświadczenia. |
| Autoryzacji |Obiekt typu blob RBAC właściwości zdarzenia. Zwykle zawiera właściwości "Akcja" i "rola" 'scope'. |
| poziom |Poziom zdarzenia. Jedną z następujących wartości: "Krytyczne", "Błąd", "Ostrzeżenie", "Informacyjny" i "Pełne" |
| location |Region, w którym wystąpił położenie (lub globalne). |
| properties |Zestaw `<Key, Value>` pary (tj. Słownik) opisujący szczegóły zdarzenia. |

> [!NOTE]
> Właściwości i użycia tych właściwości zależy od zasobu.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* [Pobierać obiekty BLOB do analizy](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Strumień dziennik aktywności do usługi Event Hubs](monitoring-stream-activity-logs-event-hubs.md)
* [Więcej informacji na temat dziennika aktywności](monitoring-overview-activity-logs.md)

