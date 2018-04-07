---
title: Archiwum dzienników diagnostycznych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak archiwum dzienników diagnostycznych platformy Azure w celu przechowywania długoterminowego na koncie magazynu.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 3a55c73f-2ef3-45f3-8956-bcf9c0cb7e05
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2018
ms.author: johnkem
ms.openlocfilehash: bf776ba8aaeca361250f39fb2c62233ee1dfbd5b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="archive-azure-diagnostic-logs"></a>Archiwum dzienników diagnostycznych platformy Azure

W tym artykule zostanie przedstawiony sposób może użyć portalu Azure, poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST do archiwizacji Twojej [Azure dzienników diagnostycznych](monitoring-overview-of-diagnostic-logs.md) na koncie magazynu. Ta opcja jest przydatna, jeśli chcesz zachować dzienników diagnostycznych przy użyciu zasad przechowywania opcjonalne inspekcji, analizę statyczną lub kopii zapasowej. Konto magazynu nie ma w tej samej subskrypcji co zasób emitowanie dzienniki, dopóki użytkownik, który konfiguruje ustawienia ma odpowiedni dostęp RBAC do obu subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy [Utwórz konto magazynu](../storage/storage-create-storage-account.md) do której można archiwizować dzienników diagnostycznych. Zdecydowanie zaleca się, że nie należy używać istniejącego konta magazynu ma inne — monitorowanie dane w niej przechowywane, dzięki czemu można lepiej kontrolować dostęp do danych monitorowania. Jednak jeśli są również Archiwizowanie dziennika aktywności, a metryki diagnostycznych na konto magazynu, rozsądne może okazać na potrzeby tego konta magazynu dla dzienników diagnostycznych oraz wszystkie dane monitorowania są przechowywane w centralnej lokalizacji. Konto magazynu, którego używasz, musi być konto magazynu ogólnego przeznaczenia, a nie konta magazynu obiektów blob.

## <a name="diagnostic-settings"></a>Ustawienia diagnostyczne

Aby zarchiwizować dzienników diagnostycznych przy użyciu dowolnej z poniższych metod, należy ustawić **ustawienie diagnostyczne** dla określonego zasobu. Ustawienie diagnostyczne dla zasobu definiuje kategorii dzienników i dane wysyłane do miejsca docelowego (konto magazynu, centra zdarzeń w przestrzeni nazw lub Log Analytics). Definiuje również zasady przechowywania (liczba dni przechowywania) dla zdarzenia każdej kategorii dziennika i dane przechowywane na koncie magazynu. Jeśli zasady przechowywania wynosi zero, zdarzenia dla tej kategorii dziennika są przechowywane przez nieograniczony czas (to znaczy oznacza nieskończoność). Zasady przechowywania, w przeciwnym razie może być dowolną liczbę dni od 1 do 2147483647. [Możesz przeczytać dodatkowe informacje w tym miejscu ustawień diagnostycznych](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings). Zasady przechowywania są zastosowane na dni, więc pod koniec dnia (UTC), dzienniki od dnia, która jest teraz poza przechowywania zasady zostaną usunięte. Na przykład jeśli masz zasady przechowywania jeden dzień na początku dnia, w obecnie dzienniki na wczoraj zanim dzień zostaną usunięte

> [!NOTE]
> Wysyłanie metryki wielowymiarowej za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryka z wymiarów są eksportowane jako spłaszczone pojedynczego metryki wymiarów, zagregowane WE wartości wymiaru.
>
> *Na przykład*: metryka "Komunikatów przychodzących" w Centrum zdarzeń można przedstawione i wykresie na na poziomie kolejki. Jednak podczas eksportowania za pomocą ustawień diagnostycznych, które Metryka będą reprezentowane jako komunikaty przychodzące we wszystkich kolejek zdarzeń koncentratora.
>
>

## <a name="archive-diagnostic-logs-using-the-portal"></a>Dzienniki diagnostyczne archiwum przy użyciu portalu

1. W portalu przejdź do monitora Azure i kliknij na **ustawień diagnostycznych**

    ![Monitorowanie sekcji Azure Monitor](media/monitoring-archive-diagnostic-logs/diagnostic-settings-blade.png)

2. Opcjonalnie Filtruj listę według grupy zasobów lub typ zasobu, a następnie kliknij na zasobie, dla której chcesz skonfigurować ustawienie diagnostyczne.

3. Jeśli nie istnieją żadne ustawienia zasobu wybrany, zostanie wyświetlony monit, aby utworzyć ustawienie. Kliknij pozycję "Włącz diagnostykę."

   ![Dodaj ustawienie diagnostyczne - żadnych istniejących ustawień](media/monitoring-archive-diagnostic-logs/diagnostic-settings-none.png)

   W przypadku istniejących ustawień na zasobie, zostanie wyświetlona lista ustawień już skonfigurowana dla tego zasobu. Kliknij przycisk "Dodaj ustawienie diagnostyczne".

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/monitoring-archive-diagnostic-logs/diagnostic-settings-multiple.png)

3. Nadaj nazwę ustawienia i pole wyboru dla **wyeksportować do konta magazynu**, wybierz konto magazynu. Opcjonalnie można ustawić liczbę dni, aby zachować te dzienniki przy użyciu **przechowywania (dni)** suwaki. Przechowywanie zero dni są przechowywane dzienniki nieskończoność.

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/monitoring-archive-diagnostic-logs/diagnostic-settings-configure.png)

4. Kliknij pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie jest wyświetlane na liście ustawień dla tego zasobu, a dzienników diagnostycznych są archiwizowane do tego konta magazynu, natychmiast po wygenerowaniu nowych danych zdarzenia.

## <a name="archive-diagnostic-logs-via-azure-powershell"></a>Dzienniki diagnostyczne archiwum za pomocą programu Azure PowerShell

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Właściwość | Wymagane | Opis |
| --- | --- | --- |
| ResourceId |Yes |Identyfikator zasobu zasobu, na którym można skonfigurować ustawienie diagnostyczne. |
| StorageAccountId |Nie |Identyfikator zasobu konta magazynu, do której ma zostać zapisany dzienników diagnostycznych. |
| Kategorie |Nie |Rozdzielana przecinkami lista kategorii dziennika, aby włączyć. |
| Enabled (Włączony) |Yes |Wartość logiczna wskazująca, czy diagnostycznych są włączone lub wyłączone w przypadku tego zasobu. |
| RetentionEnabled |Nie |Wartość logiczna wskazująca, czy zasady przechowywania są włączone dla tego zasobu. |
| retentionInDays |Nie |Liczba dni, dla których mają być przechowywane zdarzeń od 1 do 2147483647. Wartość zero przechowuje dzienniki nieskończoność. |

## <a name="archive-diagnostic-logs-via-the-azure-cli-20"></a>Dzienniki diagnostyczne archiwum za pośrednictwem 2.0 interfejsu wiersza polecenia platformy Azure

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Dodatkowe kategorie można dodać do dzienników diagnostycznych, dodając słowniki do tablicy JSON przekazany jako `--logs` parametru.

`--resource-group` Argument jest tylko w przypadku wymaganych `--storage-account` nie jest identyfikatorem obiektu. Aby wyświetlić pełną dokumentację archiwizowania dzienników diagnostycznych do magazynu, zobacz [Dokumentacja poleceń interfejsu wiersza polecenia](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Dzienniki diagnostyczne archiwum za pośrednictwem interfejsu API REST

[Zobacz ten dokument](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings) informacji w sposób konfigurowania ustawienie diagnostyczne przy użyciu interfejsu API REST Azure monitora.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Schemat dzienników diagnostycznych na koncie magazynu

Po skonfigurowaniu archiwizacji, natychmiast po wystąpieniu zdarzenia w jednym z kategorii dziennika, które aktywowano kontenera magazynu jest tworzony na koncie magazynu. Obiekty BLOB w kontenerze należy wykonać ten sam format dzienników diagnostycznych i dziennik aktywności. Struktura tych obiektów blob jest:

> insights — dzienniki — {nazwa kategorii dziennika} / resourceId = / SUBSKRYPCJI / {identyfikator subskrypcji} /RESOURCEGROUPS/ {Nazwa grupy zasobów} /PROVIDERS/ {Nazwa dostawcy zasobów} / {typu zasobu} / {Nazwa zasobu} / y = {czterocyfrowy rok liczbowych} / m = {dwucyfrowe liczbowych month} / d = {dwucyfrowe liczbą dzień} / h = {dwucyfrowe 24-godzinnym hour}/m=00/PT1H.json

Lub po prostu,

> insights — dzienniki — {nazwa kategorii dziennika} / resourceId = / {identyfikator zasobu} / y = {czterocyfrowy rok liczbowych} / m = {dwucyfrowe liczbowych month} / d = {dwucyfrowe liczbą dzień} / h = {dwucyfrowe 24-godzinnym hour}/m=00/PT1H.json

Na przykład może być nazwa obiektu blob:

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json

Każdy obiekt blob PT1H.json zawiera obiektu blob JSON zdarzeń, które wystąpiły w ciągu godziny określonego w adresie URL obiektu blob (na przykład h = 12). Podczas obecny godzinę zdarzenia są dołączane do pliku PT1H.json miarę ich występowania. Wartości minutowe (m = 00) jest zawsze 00, ponieważ dziennik diagnostyczny zdarzenia są podzielone na poszczególne obiekty BLOB na godzinę.

W pliku PT1H.json każdego zdarzenia są przechowywane w tablicy "rekordy", po tym formacie:

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
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
| category |Kategoria zdarzenia dziennika. |
| properties |Zestaw `<Key, Value>` pary (tj. Słownik) opisujący szczegóły zdarzenia. |

> [!NOTE]
> Właściwości i użycia tych właściwości zależy od zasobu.

## <a name="next-steps"></a>Kolejne kroki

* [Pobierać obiekty BLOB do analizy](../storage/storage-dotnet-how-to-use-blobs.md)
* [Strumieniowe przesyłanie dzienników diagnostycznych do przestrzeni nazw usługi Event Hubs](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Więcej informacji na temat dzienników diagnostycznych](monitoring-overview-of-diagnostic-logs.md)
