---
title: 'Samouczek: Pozyskiwanie danych dzienników diagnostycznych i danych dzienników aktywności w usłudze Azure Data Explorer bez pisania ani jednego wiersza kodu'
description: Z tego samouczka dowiesz się, jak pozyskiwać dane do usługi Azure Data Explorer bez użycia ani jednego wiersza kodu oraz jak tworzyć zapytania dotyczące tych danych.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 2/5/2019
ms.openlocfilehash: 39019c4b11d055aa8f550928bd677e4ce33d6252
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885347"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Samouczek: Pozyskiwanie danych w usłudze Azure Data Explorer bez pisania ani jednego wiersza kodu

W tym samouczku nauczymy Cię, jak pozyskiwać dane diagnostyczne i dane dzienników aktywności do klastra usługi Azure Data Explorer bez pisania ani jednego wiersza kodu. Ta prosta metoda pozyskiwania pozwala na szybkie rozpoczęcie tworzenia zapytań w usłudze Azure Data Explorer na potrzeby analizy danych.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie tabel i mapowania pozyskiwania w bazie danych usługi Azure Data Explorer.
> * Formatowanie pozyskanych danych za pomocą zasad aktualizacji.
> * Tworzenie centrum zdarzeń [Event Hubs](/azure/event-hubs/event-hubs-about) i łączenie go z usługą Azure Data Explorer.
> * Przesyłanie strumieniowe danych do centrum zdarzeń Event Hubs z [dzienników diagnostycznych usługi Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) i [dzienników aktywności usługi Azure Monitor](/azure/azure-monitor/platform/activity-logs-overview).
> * Tworzenie zapytań dotyczących pozyskiwanych danych za pomocą usługi Azure Data Explorer.

> [!NOTE]
> Tworzenie wszystkich zasobów w tej samej lokalizacji/regionie platformy Azure. Jest to wymagane w przypadku dzienników diagnostycznych usługi Azure Monitor.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Baza danych i klaster usługi Azure Data Explorer](create-cluster-database-portal.md). Nazwa bazy danych używanej w tym samouczku to *AzureMonitoring*.

## <a name="azure-monitoring-data-provider---diagnostic-and-activity-logs"></a>Dostawca danych monitorowania platformy Azure — dzienniki diagnostyczne i dzienniki aktywności

Wyświetlaj i interpretuj dane dostarczane przez dzienniki diagnostyczne i dzienniki aktywności funkcji monitorowania platformy Azure. Utworzymy potok pozyskiwania w oparciu o te schematy danych.

### <a name="diagnostic-logs-example"></a>Przykład dzienników diagnostycznych

Dzienniki diagnostyczne platformy Azure to metryki emitowane przez usługę platformy Azure, która dostarcza dane o działaniu tej usługi. Dane są agregowane z ziarnem czasu o wartości 1 minuty. Każde zdarzenie dziennika diagnostycznego zawiera jeden rekord. Oto przykład schematu zdarzeń metryki usługi Azure Data Explorer dotyczący czasu trwania zapytania:

```json
{
    "count": 14,
    "total": 0,
    "minimum": 0,
    "maximum": 0,
    "average": 0,
    "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
    "time": "2018-12-20T17:00:00.0000000Z",
    "metricName": "QueryDuration",
    "timeGrain": "PT1M"
}
```

### <a name="activity-logs-example"></a>Przykład dzienników aktywności

Dzienniki aktywności platformy Azure to dzienniki na poziomie subskrypcji, które zawierają kolekcję rekordów. Dzienniki umożliwiają wgląd w szczegółowe dane operacji wykonywanych na zasobach w ramach subskrypcji. W przeciwieństwie do dzienników diagnostycznych zdarzenie dzienników aktywności ma tablicę rekordów. W dalszej części samouczka trzeba będzie podzielić tę tablicę rekordów. Oto przykład zdarzenia dziennika aktywności na potrzeby sprawdzania dostępu:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```

## <a name="set-up-ingestion-pipeline-in-azure-data-explorer"></a>Konfigurowanie potoku pozyskiwania w usłudze Azure Data Explorer 

Konfiguracja potoku usługi Azure Data Explorer zawiera różne kroki, które obejmują [tworzenie tabeli i pozyskiwanie danych](/azure/data-explorer/ingest-sample-data#ingest-data). Można również modyfikować, mapować i aktualizować dane.

### <a name="connect-to-azure-data-explorer-web-ui"></a>Łączenie z internetowym interfejsem użytkownika usługi Azure Data Explorer

1. W bazie danych *AzureMonitoring* usługi Azure Data Explorer wybierz pozycję **Zapytanie**, co spowoduje otwarcie internetowego interfejsu użytkownika usługi Azure Data Explorer.

    ![Zapytanie](media/ingest-data-no-code/query-database.png)

### <a name="create-target-tables"></a>Tworzenie tabel docelowych

Do utworzenia tabel docelowych w bazie danych usługi Azure Data Explorer użyj internetowego interfejsu użytkownika usługi Azure Data Explorer.

#### <a name="diagnostic-logs-table"></a>Tabela dzienników diagnostycznych

1. Utwórz tabelę *DiagnosticLogsRecords* w bazie danych *AzureMonitoring*, która będzie odbierać rekordy dziennika diagnostycznego przy użyciu polecenia kontroli `.create table`:

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Wybierz pozycję **Uruchom**, aby utworzyć tabelę.

    ![Uruchamianie zapytania](media/ingest-data-no-code/run-query.png)

#### <a name="activity-logs-tables"></a>Tabele dzienników aktywności

Ponieważ struktura dzienników aktywności nie jest tabelaryczna, trzeba będzie zmodyfikować dane i rozwinąć każde zdarzenie do co najmniej jednego rekordu. Dane pierwotne zostaną pozyskane do pośredniej tabeli *ActivityLogsRawRecords*. W tym momencie dane będą modyfikowane i rozwijane. Rozwinięte dane zostaną następnie pozyskane do tabeli *ActivityLogsRecords* za pomocą zasad aktualizacji. W związku z tym musisz utworzyć dwie oddzielne tabele na potrzeby pozyskiwania dzienników aktywności.

1. Utwórz tabelę *ActivityLogsRecords* w bazie danych *AzureMonitoring*, która będzie odbierać rekordy dziennika aktywności. Uruchom następujące zapytanie usługi Azure Data Explorer w celu utworzenia tabeli:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Utwórz tabelę danych pośrednich *ActivityLogsRawRecords* w bazie danych *AzureMonitoring* na potrzeby modyfikowania danych:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>Tworzenie mapowań tabel

 Format danych to `json`, dlatego wymagane jest mapowanie danych. Mapowanie `json` mapuje każdą ścieżkę JSON na nazwę kolumny tabeli.

#### <a name="diagnostic-logs-table-mapping"></a>Mapowanie tabeli dzienników diagnostycznych

Użyj następującego zapytania, aby mapować dane do tabeli:

```kusto
.create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[
{"column":"Timestamp","path":"$.time"},
{"column":"ResourceId","path":"$.resourceId"},
{"column":"MetricName","path":"$.metricName"},
{"column":"Count","path":"$.count"},
{"column":"Total","path":"$.total"},
{"column":"Minimum","path":"$.minimum"},
{"column":"Maximum","path":"$.maximum"},
{"column":"Average","path":"$.average"},
{"column":"TimeGrain","path":"$.timeGrain"}]'
```

#### <a name="activity-logs-table-mapping"></a>Mapowanie tabeli dzienników aktywności

Użyj następującego zapytania, aby mapować dane do tabeli:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[
{"column":"Records","path":"$.records"}]'
```

### <a name="create-update-policy"></a>Tworzenie zasad aktualizacji

1. Utwórz [funkcję](/azure/kusto/management/functions), która rozwija kolekcję rekordów, tak aby każda wartość w kolekcji odbierała oddzielny wiersz. Użyj operatora [`mvexpand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mvexpand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events["identity.authorization"],
            IdentityClaims = events["identity.claims"],
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. Dodaj [zasady aktualizacji](/azure/kusto/concepts/updatepolicy) do tabeli docelowej. Spowoduje to automatyczne uruchomienie zapytania dla wszystkich nowo pozyskanych danych w tabeli danych pośrednich *ActivityLogsRawRecords* i pozyskanie wyników do tabeli *ActivityLogsRecords*:

    ```kusto
    .alter table ActivityLogRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-event-hub-namespace"></a>Tworzenie przestrzeni nazw centrum zdarzeń

Dzienniki diagnostyczne platformy Azure umożliwiają eksportowanie metryk na konto usługi Storage lub centrum zdarzeń. W tym samouczku kierujemy metryki za pośrednictwem centrum zdarzeń. W kolejnych krokach utworzymy centrum zdarzeń i przestrzeń nazw usługi Event Hubs na potrzeby dzienników diagnostycznych. Funkcja monitorowania platformy Azure spowoduje utworzenie elementu *insights-operational-logs* centrum zdarzeń na potrzeby dzienników aktywności.

1. Utwórz centrum zdarzeń przy użyciu szablonu usługi Azure Resource Manager w witrynie Azure Portal. Użyj poniższego przycisku, aby rozpocząć wdrażanie. Kliknij prawym przyciskiem myszy i wybierz pozycję **Utwórz w nowym oknie**, aby wykonać pozostałe kroki w tym artykule. Przycisk **Wdróż na platformie Azure** powoduje przejście do witryny Azure Portal.

    [![Wdrażanie na platformie Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Utwórz centrum zdarzeń i przestrzeń nazw usługi Event Hubs na potrzeby dzienników diagnostycznych.

    ![Tworzenie centrum zdarzeń usługi Event Hubs](media/ingest-data-no-code/event-hub.png)

    Wypełnij formularz, używając poniższych informacji. W przypadku wszystkich ustawień, które nie są wymienione w poniższej tabeli, użyj ustawień domyślnych.

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla centrum zdarzeń.|
    | Grupa zasobów | *test-resource-group* | Utwórz nową grupę zasobów. |
    | Lokalizacja | Wybierz region, który najlepiej odpowiada Twoim potrzebom. | Utwórz przestrzeń nazw usługi Event Hubs w tej samej lokalizacji, co inne zasoby.
    | Nazwa przestrzeni nazw | *AzureMonitoringData* | Wybierz unikatową nazwę, która identyfikuje Twoją przestrzeń nazw.
    | Nazwa centrum zdarzeń | *DiagnosticLogsData* | Centrum zdarzeń znajduje się w przestrzeni nazw, która zapewnia unikatowy kontener określania zakresu. |
    | Nazwa grupy konsumentów | *adxpipeline* | Utwórz nazwę grupy użytkowników. Dzięki niej każda z wielu aplikacji korzystających z danych może mieć osobny widok strumienia zdarzeń. |
    | | |

## <a name="connect-azure-monitoring-logs-to-event-hub"></a>Łączenie dzienników monitorowania platformy Azure z centrum zdarzeń Event Hubs

### <a name="diagnostic-logs-connection-to-event-hub"></a>Połączenie dzienników diagnostycznych z centrum zdarzeń Event Hubs

Wybierz zasób, z którego chcesz eksportować metryki. Istnieje kilka typów zasobów, które umożliwiają eksportowanie dzienników diagnostycznych, w tym przestrzeni nazw usługi Event Hubs, usług KeyVault i IoT Hub oraz klastra usługi Azure Data Explorer. W tym samouczku używamy klastra usługi Azure Data Explorer jako naszego zasobu.

1. Wybierz klaster usługi Kusto w witrynie Azure Portal.

    ![Ustawienia diagnostyczne](media/ingest-data-no-code/diagnostic-settings.png)

1. Wybierz pozycję **Ustawienia diagnostyczne** w menu po lewej stronie.
1. Kliknij link **Włącz diagnostykę**. Zostanie otwarte okno **Ustawienia diagnostyczne**.

    ![Okno Ustawienia diagnostyczne](media/ingest-data-no-code/diagnostic-settings-window.png)

1. W okienku **Ustawienia diagnostyczne**:
    1. Nadaj nazwę danym dziennika diagnostycznego: *ADXExportedData*
    1. Zaznacz pole wyboru **AllMetrics** (opcjonalnie).
    1. Zaznacz pole wyboru **Prześlij strumieniowo do centrum zdarzeń**.
    1. Kliknij pozycję **Konfiguruj**.

1. W okienku **Wybieranie centrum zdarzeń** skonfiguruj eksportowanie do utworzonego centrum zdarzeń:
    1. **Wybierz przestrzeń nazw usługi Event Hubs** *AzureMonitoringData* z listy rozwijanej.
    1. **Wybierz nazwę centrum zdarzeń Event Hubs** *AzureMonitoringData* z listy rozwijanej.
    1. **Wybierz nazwę zasad centrum zdarzeń Event Hubs** z listy rozwijanej.
    1. Kliknij przycisk **OK**.

1. Kliknij pozycję **Zapisz**. Przestrzeń nazw, nazwa i nazwa zasad centrum zdarzeń Event Hubs zostaną wyświetlone w oknie.

    ![Zapisywanie ustawień diagnostycznych](media/ingest-data-no-code/save-diagnostic-settings.png)

### <a name="activity-logs-connection-to-event-hub"></a>Połączenie dzienników aktywności z centrum zdarzeń Event Hubs

1. W menu po lewej stronie w witrynie Azure Portal wybierz pozycję **Dziennik aktywności**.
1. Zostanie otwarte okno **Dziennik aktywności**. Kliknij pozycję **Eksportuj do centrum zdarzeń**.

    ![Dziennik aktywności](media/ingest-data-no-code/activity-log.png)

1. W oknie **Eksportowanie dziennika aktywności**:
 
    ![Eksportowanie dziennika aktywności](media/ingest-data-no-code/export-activity-log.png)

    1. Wybierz subskrypcję.
    1. Na liście rozwijanej **Regiony** wybierz pozycję **Zaznacz wszystko**.
    1. Zaznacz pole wyboru **Eksportuj do centrum zdarzeń**.
    1. Kliknij pozycję **Wybierz przestrzeń nazw usługi Service Bus**, aby otworzyć okienko **Wybierz centrum zdarzeń**.
    1. W okienku **wybierania centrum zdarzeń** wybierz z menu rozwijanych subskrypcję, przestrzeń nazw zdarzeń *AzureMonitoringData* i domyślną nazwę zasad centrum zdarzeń.
    1. Kliknij przycisk **OK**.
    1. Kliknij pozycję **Zapisz** w prawym górnym rogu okna. Zostanie utworzone centrum zdarzeń o nazwie *insights-operational-logs*.

### <a name="see-data-flowing-to-your-event-hubs"></a>Wyświetlanie danych przepływających do usługi Event Hubs

1. Poczekaj kilka minut, aż połączenie zostanie zdefiniowane, a eksport dziennika aktywności do centrum zdarzeń zakończy się. Przejdź do przestrzeni nazw usługi Event Hubs, aby wyświetlić utworzone centra zdarzeń.

    ![Utworzone centra zdarzeń Event Hubs](media/ingest-data-no-code/event-hubs-created.png)

1. Zapoznaj się z danymi przepływającymi do centrum zdarzeń:

    ![Dane centrów zdarzeń Event Hubs](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-event-hub-to-azure-data-explorer"></a>Łączenie centrum zdarzeń z usługą Azure Data Explorer

### <a name="diagnostic-logs-data-connection"></a>Połączenie danych dzienników diagnostycznych

1. W klastrze usługi Azure Data Explorer *kustodocs* wybierz pozycję **Bazy danych** w menu po lewej stronie.
1. W oknie **Bazy danych** wybierz nazwę bazy danych *AzureMonitoring*.
1. W menu po lewej stronie wybierz pozycję **Pozyskiwanie danych**.
1. W oknie **Pozyskiwanie danych** kliknij pozycję **+ Dodaj połączenie danych**.
1. W oknie **Połączenie danych** wprowadź następujące informacje:

    ![Połączenie centrum zdarzeń](media/ingest-data-no-code/event-hub-data-connection.png)

    Źródło danych:

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Nazwa połączenia danych | *DiagnosticsLogsConnection* | Nazwa połączenia, które chcesz utworzyć w usłudze Azure Data Explorer.|
    | Przestrzeń nazw centrum zdarzeń | *AzureMonitoringData* | Wybrana wcześniej nazwa, która identyfikuje Twoją przestrzeń nazw. |
    | Centrum zdarzeń | *diagnosticlogsdata* | Utworzone przez Ciebie centrum zdarzeń. |
    | Grupa konsumentów | *adxpipeline* | Grupa konsumentów zdefiniowana w utworzonym przez Ciebie centrum zdarzeń. |
    | | |

    Tabela docelowa:

    Dostępne są dwie opcje routingu: *statyczny* i *dynamiczny*. W tym samouczku będziesz używać routingu statycznego (opcja domyślna), w którym określisz nazwę tabeli, format pliku i mapowanie. W związku z tym pozostaw pole **Moje dane zawierają informacje o routingu** niezaznaczone.

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Tabela | *DiagnosticLogsRecords* | Tabela utworzona w bazie danych *AzureMonitoring*. |
    | Format danych | *JSON* | Format w tabeli. |
    | Mapowanie kolumn | *DiagnosticLogsRecordsMapping* | Mapowanie utworzone w bazie danych *AzureMonitoring*, które mapuje przychodzące dane JSON na nazwy kolumn i typy danych tabeli *DiagnosticLogsRecords*.|
    | | |

1. Kliknij przycisk **Utwórz**  

### <a name="activity-logs-data-connection"></a>Połączenie danych dzienników aktywności

Powtórz kroki z sekcji [Połączenie danych dzienników diagnostycznych](#diagnostic-logs-data-connection), aby utworzyć połączenie danych dzienników aktywności.

1. Wstaw następujące ustawienia w oknie **Połączenie danych**:

    Źródło danych:

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Nazwa połączenia danych | *ActivityLogsConnection* | Nazwa połączenia, które chcesz utworzyć w usłudze Azure Data Explorer.|
    | Przestrzeń nazw centrum zdarzeń | *AzureMonitoringData* | Wybrana wcześniej nazwa, która identyfikuje Twoją przestrzeń nazw. |
    | Centrum zdarzeń | *insights-operational-logs* | Utworzone przez Ciebie centrum zdarzeń. |
    | Grupa konsumentów | *$Default* | Domyślna grupa użytkowników. W razie potrzeby możesz utworzyć inną grupę użytkowników. |
    | | |

    Tabela docelowa:

    Dostępne są dwie opcje routingu: *statyczny* i *dynamiczny*. W tym samouczku będziesz używać routingu statycznego (opcja domyślna), w którym określisz nazwę tabeli, format pliku i mapowanie. W związku z tym pozostaw pole **Moje dane zawierają informacje o routingu** niezaznaczone.

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Tabela | *ActivityLogsRawRecords* | Tabela utworzona w bazie danych *AzureMonitoring*. |
    | Format danych | *JSON* | Format w tabeli. |
    | Mapowanie kolumn | *ActivityLogsRawRecordsMapping* | Mapowanie utworzone w bazie danych *AzureMonitoring*, które mapuje przychodzące dane JSON na nazwy kolumn i typy danych tabeli *ActivityLogsRawRecords*.|
    | | |

1. Kliknij przycisk **Utwórz**  

## <a name="query-the-new-tables"></a>Tworzenie zapytań dotyczących nowych tabel

Masz potok z przepływającymi danymi. Pozyskiwanie za pośrednictwem klastra trwa domyślnie 5 minut, co pozwala na przepływ danych przez kilka minut przed rozpoczęciem tworzenia zapytania.

### <a name="diagnostic-logs-table-query-example"></a>Przykład zapytania dotyczącego tabeli dzienników diagnostycznych

Następujące zapytanie analizuje dane czasu trwania zapytania z rekordów dziennika diagnostycznego usługi Azure Data Explorer:

```kusto
DiagnosticLogsRecords
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Wyniki zapytania:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06,156 |
| | |

### <a name="activity-logs-table-query-example"></a>Przykład zapytania dotyczącego tabeli dzienników aktywności

Następujące zapytanie analizuje dane z rekordów dziennika aktywności usługi Azure Data Explorer:

```kusto
ActivityLogsRecords
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Wyniki zapytania:
|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768,333 |
| | |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak pisać wiele innych zapytań dotyczących danych wyodrębnionych z usługi Azure Data Explorer, zapoznaj się z następującym artykułem:

> [!div class="nextstepaction"]
> [Pisanie zapytań dla usługi Azure Data Explorer](write-queries.md)
