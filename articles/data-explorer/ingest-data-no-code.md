---
title: 'Samouczek: Pozyskiwanie danych dzienników diagnostycznych i danych dzienników aktywności w usłudze Azure Data Explorer bez pisania ani jednego wiersza kodu'
description: Z tego samouczka dowiesz się, jak pozyskiwać dane do usługi Azure Data Explorer bez użycia ani jednego wiersza kodu oraz jak tworzyć zapytania dotyczące tych danych.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 04/07/2019
ms.openlocfilehash: 9f4b7ee0dcc87ca03fd051be0dacedf0912b5320
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759865"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Samouczek: Pozyskiwanie danych w usłudze Azure Data Explorer bez pisania ani jednego wiersza kodu

W tym samouczku nauczymy Cię, jak pozyskiwać dane z dzienników diagnostycznych i dzienników aktywności do klastra usługi Azure Data Explorer bez pisania kodu. Ta prosta metoda pozyskiwania pozwala na szybkie rozpoczęcie tworzenia zapytań w usłudze Azure Data Explorer na potrzeby analizy danych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie tabel i mapowania pozyskiwania w bazie danych usługi Azure Data Explorer.
> * Formatowanie pozyskanych danych za pomocą zasad aktualizacji.
> * Tworzenie [centrum zdarzeń](/azure/event-hubs/event-hubs-about) i łączenie go z usługą Azure Data Explorer.
> * Przesyłanie strumieniowe danych do centrum zdarzeń z [dzienników diagnostycznych usługi Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) i [dzienników aktywności usługi Azure Monitor](/azure/azure-monitor/platform/activity-logs-overview).
> * Tworzenie zapytań dotyczących pozyskiwanych danych za pomocą usługi Azure Data Explorer.

> [!NOTE]
> Tworzenie wszystkich zasobów w tej samej lokalizacji lub regionie platformy Azure. Jest to wymagane w przypadku dzienników diagnostycznych usługi Azure Monitor.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Baza danych i klaster usługi Azure Data Explorer](create-cluster-database-portal.md). Nazwa bazy danych używanej w tym samouczku to *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-and-activity-logs"></a>Dostawca danych usługi Azure Monitor: dzienniki diagnostyczne i dzienniki aktywności

Wyświetl i zrozumieć dane udostępniane przez usługi Azure Monitor Diagnostyka i Dzienniki aktywności poniżej. Utworzymy potok pozyskiwania w oparciu o te schematy danych. Należy pamiętać, że każde zdarzenie w dzienniku ma tablicą rekordów. Ta tablica rekordów, zostanie ona podzielona w dalszej części tego samouczka.

### <a name="diagnostic-logs-example"></a>Przykład dzienników diagnostycznych

Dzienniki diagnostyczne platformy Azure to metryki emitowane przez usługę platformy Azure, która dostarcza dane o działaniu tej usługi. Dane są agregowane z ziarnem czasu o wartości 1 minuty. Oto przykład schematu zdarzeń metryki usługi Azure Data Explorer dotyczący czasu trwania zapytania:

```json
{
    "records": [
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
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

### <a name="activity-logs-example"></a>Przykład dzienników aktywności

Dzienniki aktywności platformy Azure są dzienniki poziom subskrypcji, które udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji. Oto przykład zdarzenia dziennika aktywności na potrzeby sprawdzania dostępu:

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

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Konfigurowanie potoku pozyskiwania w usłudze Azure Data Explorer

Konfigurowanie potoku w usłudze Azure Data Explorer obejmuje kilka kroków, takich jak [tworzenie tabeli i pozyskiwanie danych](/azure/data-explorer/ingest-sample-data#ingest-data). Można również modyfikować, mapować i aktualizować dane.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Łączenie z internetowym interfejsem użytkownika usługi Azure Data Explorer

W bazie danych *TestDatabase* usługi Azure Data Explorer wybierz pozycję **Zapytanie**, aby otworzyć internetowy interfejs użytkownika usługi Azure Data Explorer.

![Strona zapytań](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Tworzenie tabel docelowych

Struktura dzienniki usługi Azure Monitor nie jest tabelarycznym. Będziesz manipulowania danymi i rozwinąć każde zdarzenie do co najmniej jeden rekord. Będzie można pozyskać danych pierwotnych do pośredniego tabeli o nazwie *ActivityLogsRawRecords* o Dzienniki aktywności i *DiagnosticLogsRawRecords* dla dzienników diagnostycznych. W tym momencie dane będą modyfikowane i rozwijane. Za pomocą zasad aktualizacji, rozwinięty danych zostanie następnie być pozyskiwane do *ActivityLogsRecords* tabeli o Dzienniki aktywności i *DiagnosticLogsRecords* dla dzienników diagnostycznych. Oznacza to, że musisz utworzyć oddzielne tabel, aby zbierać Dzienniki aktywności i oddzielnych tabel, aby zbierać dzienniki diagnostyczne.

Do utworzenia tabel docelowych w bazie danych usługi Azure Data Explorer użyj internetowego interfejsu użytkownika usługi Azure Data Explorer.

#### <a name="the-diagnostic-logs-table"></a>Tabela dzienników diagnostycznych

1. W bazie danych *TestDatabase* utwórz tabelę o nazwie *DiagnosticLogsRecords* do przechowywania rekordów dziennika diagnostycznego. Użyj następującego polecenia kontroli `.create table`:

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Wybierz pozycję **Uruchom**, aby utworzyć tabelę.

    ![Uruchamianie zapytania](media/ingest-data-no-code/run-query.png)

1. Tworzenie tabeli danych pośrednich o nazwie *DiagnosticLogsRawRecords* w *TestDatabase* bazę danych do manipulowania danymi przy użyciu następującej kwerendy. Wybierz pozycję **Uruchom**, aby utworzyć tabelę.

    ```kusto
    .create table DiagnosticLogsRawRecords (Records:dynamic)
    ```

#### <a name="the-activity-logs-tables"></a>Tabele dzienników aktywności

1. Utwórz tabelę o nazwie *ActivityLogsRecords* w bazie danych *TestDatabase*, która będzie odbierać rekordy dziennika aktywności. Uruchom następujące zapytanie usługi Azure Data Explorer w celu utworzenia tabeli:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Utwórz tabelę danych pośrednich o nazwie *ActivityLogsRawRecords* w bazie danych *TestDatabase* na potrzeby modyfikowania danych:

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

#### <a name="table-mapping-for-diagnostic-logs"></a>Mapowanie tabeli na potrzeby dzienników diagnostycznych

Użyj następującego zapytania, aby zamapować dane dzienników diagnostycznych na tabelę:

```kusto
.create table DiagnosticLogsRawRecords ingestion json mapping 'DiagnosticLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

#### <a name="table-mapping-for-activity-logs"></a>Mapowanie tabeli na potrzeby dzienników aktywności

Użyj następującego zapytania, aby zamapować dane dzienników aktywności na tabelę:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-the-update-policy-for-log-data"></a>Utwórz zasady aktualizacji za dane dziennika

#### <a name="activity-log-data-update-policy"></a>Zasady aktualizacji danych dziennika aktywności

1. Tworzenie [funkcja](/azure/kusto/management/functions) , rozszerza kolekcji rekordów dziennika aktywności, aby każda wartość w kolekcji odbiera oddzielnym wierszu. Użyj operatora [`mv-expand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. Dodaj [zasady aktualizacji](/azure/kusto/concepts/updatepolicy) do tabeli docelowej. Te zasady powodują automatyczne uruchomienie zapytania dla wszystkich nowo pozyskanych danych w tabeli danych pośrednich *ActivityLogsRawRecords* i pozyskanie wyników do tabeli *ActivityLogsRecords*:

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

#### <a name="diagnostic-log-data-update-policy"></a>Dane dzienników diagnostycznych aktualizowanie zasad

1. Tworzenie [funkcja](/azure/kusto/management/functions) kolekcji rekordów dziennika diagnostycznego, rozszerza tak, aby każda wartość w kolekcji odbiera oddzielnym wierszu. Użyj operatora [`mv-expand`](/azure/kusto/query/mvexpandoperator):
     ```kusto
    .create function DiagnosticLogRecordsExpand() {
        DiagnosticLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            MetricName = tostring(events["metricName"]),
            Count = toint(events["count"]),
            Total = todouble(events["total"]),
            Minimum = todouble(events["minimum"]),
            Maximum = todouble(events["maximum"]),
            Average = todouble(events["average"]),
            TimeGrain = tostring(events["timeGrain"])
    }
    ```

2. Dodaj [zasady aktualizacji](/azure/kusto/concepts/updatepolicy) do tabeli docelowej. Ta zasada zostanie automatycznie uruchomiona zapytanie na wszystkie nowo odebrane dane w *DiagnosticLogsRawRecords* danych pośrednich tabeli i pozyskiwanie ich wyników do *DiagnosticLogsRecords* tabeli:

    ```kusto
    .alter table DiagnosticLogsRecords policy update @'[{"Source": "DiagnosticLogsRawRecords", "Query": "DiagnosticLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-azure-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Azure Event Hubs

Dzienniki diagnostyczne platformy Azure umożliwiają eksportowanie metryk na konto magazynu lub do centrum zdarzeń. W tym samouczku skierujemy metryki za pośrednictwem centrum zdarzeń. W kolejnych krokach utworzysz centrum zdarzeń i przestrzeń nazw usługi Event Hubs na potrzeby dzienników diagnostycznych. Usługa Azure Monitor utworzy centrum zdarzeń *insights-operational-logs* na potrzeby dzienników aktywności.

1. Utwórz centrum zdarzeń przy użyciu szablonu usługi Azure Resource Manager w witrynie Azure Portal. Aby wykonać pozostałe kroki w tym artykule, kliknij prawym przyciskiem myszy przycisk **Wdróż na platformie Azure** i wybierz pozycję **Otwórz w nowym oknie**. Przycisk **Wdróż na platformie Azure** powoduje przejście do witryny Azure Portal.

    [![Przycisk Wdróż na platformie Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Utwórz centrum zdarzeń i przestrzeń nazw usługi Event Hubs na potrzeby dzienników diagnostycznych.

    ![Tworzenie centrum zdarzeń](media/ingest-data-no-code/event-hub.png)

1. Wypełnij formularz, używając poniższych informacji. W przypadku wszystkich ustawień, które nie są wymienione w poniższej tabeli, użyj wartości domyślnych.

    **Ustawienie** | **Sugerowana wartość** | **Opis**
    |---|---|---|
    | **Subskrypcja** | *Twoja subskrypcja* | Wybierz subskrypcję platformy Azure, która ma być używana dla centrum zdarzeń.|
    | **Grupa zasobów** | *test-resource-group* | Utwórz nową grupę zasobów. |
    | **Lokalizacja** | Wybierz region, który najlepiej odpowiada Twoim potrzebom. | Utwórz przestrzeń nazw usługi Event Hubs w tej samej lokalizacji co inne zasoby.
    | **Nazwa przestrzeni nazw** | *AzureMonitoringData* | Wybierz unikatową nazwę, która identyfikuje Twoją przestrzeń nazw.
    | **Nazwa centrum zdarzeń** | *DiagnosticLogsData* | Centrum zdarzeń znajduje się w przestrzeni nazw, która zapewnia unikatowy kontener określania zakresu. |
    | **Nazwa grupy konsumentów** | *adxpipeline* | Utwórz nazwę grupy użytkowników. Dzięki grupom konsumentów każda z wielu aplikacji korzystających z danych może mieć osobny widok strumienia zdarzeń. |
    | | |

## <a name="connect-azure-monitor-logs-to-your-event-hub"></a>Łączenie dzienników usługi Azure Monitor z centrum zdarzeń

Teraz należy połączyć dzienniki diagnostyczne i dzienniki aktywności z centrum zdarzeń.

### <a name="connect-diagnostic-logs-to-your-event-hub"></a>Łączenie dzienników diagnostycznych z centrum zdarzeń

Wybierz zasób, z którego chcesz eksportować metryki. Istnieje kilka typów zasobów, które obsługują eksportowanie dzienników diagnostycznych, w tym przestrzeń nazw usługi Event Hubs, usługa Azure Key Vault, usługa Azure IoT Hub i klastry usługi Azure Data Explorer. W tym samouczku jako zasobu użyjemy klastra usługi Azure Data Explorer.

1. Wybierz klaster usługi Kusto w witrynie Azure Portal.
1. Wybierz pozycję **Ustawienia diagnostyczne**, a następnie wybierz link **Włącz diagnostykę**. 

    ![Ustawienia diagnostyczne](media/ingest-data-no-code/diagnostic-settings.png)

1. Zostanie otwarte okienko **Ustawienia diagnostyczne**. Wykonaj następujące kroki:
   1. Nadaj danym z dziennika diagnostycznego nazwę *ADXExportedData*.
   1. W obszarze **METRYKA** zaznacz pole wyboru **AllMetrics** (opcjonalnie).
   1. Zaznacz pole wyboru **Przesyłaj strumieniowo do centrum zdarzeń**.
   1. Wybierz pozycję **Konfiguruj**.

      ![Okienko ustawień diagnostycznych](media/ingest-data-no-code/diagnostic-settings-window.png)

1. W okienku **Wybieranie centrum zdarzeń** skonfiguruj sposób eksportowania danych z dzienników diagnostycznych do utworzonego centrum zdarzeń:
    1. Z listy **Wybierz przestrzeń nazw centrum zdarzeń** wybierz pozycję *AzureMonitoringData*.
    1. Z listy **Wybierz nazwę centrum zdarzeń** wybierz pozycję *diagnosticlogsdata*.
    1. Z listy **Wybierz nazwę zasad centrum zdarzeń** wybierz pozycję **RootManagerSharedAccessKey**.
    1. Kliknij przycisk **OK**.

1. Wybierz pozycję **Zapisz**.

### <a name="connect-activity-logs-to-your-event-hub"></a>Łączenie dzienników aktywności z centrum zdarzeń

1. W menu po lewej stronie w witrynie Azure Portal wybierz pozycję **Dziennik aktywności**.
1. Zostanie otwarte okno **Dziennik aktywności**. Wybierz pozycję **Eksportuj do centrum zdarzeń**.

    ![Okno Dziennik aktywności](media/ingest-data-no-code/activity-log.png)

1. Zostanie otwarte okno **Eksportuj dziennik aktywności**:
 
    ![Okno Eksportuj dziennik aktywności](media/ingest-data-no-code/export-activity-log.png)

1. W oknie **Eksportuj dziennik aktywności** wykonaj następujące czynności:
      1. Wybierz subskrypcję.
      1. Z listy **Regiony** wybierz pozycję **Zaznacz wszystko**.
      1. Zaznacz pole wyboru **Eksportuj do centrum zdarzeń**.
      1. Wybierz pozycję **Wybierz przestrzeń nazw usługi Service Bus**, aby otworzyć okienko **Wybierz centrum zdarzeń**.
      1. W okienku **Wybierz centrum zdarzeń** wybierz swoją subskrypcję.
      1. Z listy **Wybierz przestrzeń nazw centrum zdarzeń** wybierz pozycję *AzureMonitoringData*.
      1. Z listy **Wybierz nazwę zasad centrum zdarzeń** wybierz nazwę domyślnych zasad centrum zdarzeń.
      1. Kliknij przycisk **OK**.
      1. W lewym górnym rogu okna wybierz pozycję **Zapisz**.
   Zostanie utworzone centrum zdarzeń o nazwie *insights-operational-logs*.

### <a name="see-data-flowing-to-your-event-hubs"></a>Wyświetlanie danych przepływających do centrum zdarzeń

1. Poczekaj kilka minut, aż połączenie zostanie zdefiniowane i zakończy się eksport dziennika aktywności do centrum zdarzeń. Przejdź do przestrzeni nazw usługi Event Hubs, aby wyświetlić utworzone centra zdarzeń.

    ![Utworzone centra zdarzeń](media/ingest-data-no-code/event-hubs-created.png)

1. Zapoznaj się z danymi przepływającymi do centrum zdarzeń:

    ![Dane centrum zdarzeń](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Łączenie centrum zdarzeń z usługą Azure Data Explorer

Teraz należy utworzyć połączenia danych dla dzienników aktywności i dzienników diagnostycznych.

### <a name="create-the-data-connection-for-diagnostic-logs"></a>Tworzenie połączenia danych dla dzienników diagnostycznych

1. W klastrze usługi Azure Data Explorer o nazwie *kustodocs* wybierz pozycję **Bazy danych** w menu po lewej stronie.
1. W oknie **Bazy danych** wybierz nazwę bazy danych *TestDatabase*.
1. W menu po lewej stronie wybierz pozycję **Pozyskiwanie danych**.
1. W oknie **Pozyskiwanie danych** kliknij pozycję **+ Dodaj połączenie danych**.
1. W oknie **Połączenie danych** wprowadź następujące informacje:

    ![Połączenie danych centrum zdarzeń](media/ingest-data-no-code/event-hub-data-connection.png)

    Źródło danych:

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | **Nazwa połączenia danych** | *DiagnosticsLogsConnection* | Nazwa połączenia, które chcesz utworzyć w usłudze Azure Data Explorer.|
    | **Przestrzeń nazw centrum zdarzeń** | *AzureMonitoringData* | Wybrana wcześniej nazwa, która identyfikuje Twoją przestrzeń nazw. |
    | **Centrum zdarzeń** | *diagnosticlogsdata* | Utworzone przez Ciebie centrum zdarzeń. |
    | **Grupa konsumentów** | *adxpipeline* | Grupa konsumentów zdefiniowana w utworzonym przez Ciebie centrum zdarzeń. |
    | | |

    Tabela docelowa:

    Dostępne są dwie opcje routingu: *statyczny* i *dynamiczny*. W tym samouczku będziesz używać routingu statycznego (opcja domyślna), w którym określisz nazwę tabeli, format danych i mapowanie. Pozostaw pole **Moje dane zawierają informacje o routingu** niezaznaczone.

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | **Tabela** | *DiagnosticLogsRawRecords* | Tabela utworzona w bazie danych *TestDatabase*. |
    | **Format danych** | *JSON* | Format używany w tabeli. |
    | **Mapowanie kolumn** | *DiagnosticLogsRecordsMapping* | Mapowanie utworzone w bazie danych *TestDatabase*, które mapuje przychodzące dane JSON na nazwy kolumn i typy danych tabeli *DiagnosticLogsRecords*.|
    | | |

1. Wybierz pozycję **Utwórz**.  

### <a name="create-the-data-connection-for-activity-logs"></a>Tworzenie połączenia danych dla dzienników aktywności

Powtórz kroki z sekcji „Tworzenie połączenia danych dla dzienników diagnostycznych”, aby utworzyć połączenie danych dla dzienników aktywności.

1. Użyj następujących ustawień w oknie **Połączenie danych**:

    Źródło danych:

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | **Nazwa połączenia danych** | *ActivityLogsConnection* | Nazwa połączenia, które chcesz utworzyć w usłudze Azure Data Explorer.|
    | **Przestrzeń nazw centrum zdarzeń** | *AzureMonitoringData* | Wybrana wcześniej nazwa, która identyfikuje Twoją przestrzeń nazw. |
    | **Centrum zdarzeń** | *insights-operational-logs* | Utworzone przez Ciebie centrum zdarzeń. |
    | **Grupa konsumentów** | *$Default* | Domyślna grupa użytkowników. W razie potrzeby możesz utworzyć inną grupę użytkowników. |
    | | |

    Tabela docelowa:

    Dostępne są dwie opcje routingu: *statyczny* i *dynamiczny*. W tym samouczku będziesz używać routingu statycznego (opcja domyślna), w którym określisz nazwę tabeli, format danych i mapowanie. Pozostaw pole **Moje dane zawierają informacje o routingu** niezaznaczone.

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | **Tabela** | *ActivityLogsRawRecords* | Tabela utworzona w bazie danych *TestDatabase*. |
    | **Format danych** | *JSON* | Format używany w tabeli. |
    | **Mapowanie kolumn** | *ActivityLogsRawRecordsMapping* | Mapowanie utworzone w bazie danych *TestDatabase*, które mapuje przychodzące dane JSON na nazwy kolumn i typy danych tabeli *ActivityLogsRawRecords*.|
    | | |

1. Wybierz pozycję **Utwórz**.  

## <a name="query-the-new-tables"></a>Tworzenie zapytań dotyczących nowych tabel

Masz teraz potok z przepływającymi danymi. Pozyskiwanie za pośrednictwem klastra trwa domyślnie 5 minut, co pozwala na przepływ danych przez kilka minut przed rozpoczęciem tworzenia zapytania.

### <a name="an-example-of-querying-the-diagnostic-logs-table"></a>Przykład wykonywania zapytania dotyczącego tabeli dzienników diagnostycznych

Następujące zapytanie analizuje dane czasu trwania zapytania z rekordów dziennika diagnostycznego w usłudze Azure Data Explorer:

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

### <a name="an-example-of-querying-the-activity-logs-table"></a>Przykład wykonywania zapytania dotyczącego tabeli dzienników aktywności

Następujące zapytanie analizuje dane z rekordów dziennika aktywności w usłudze Azure Data Explorer:

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

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak pisać wiele innych zapytań dotyczących danych wyodrębnionych z usługi Azure Data Explorer, zapoznaj się z następującym artykułem:

> [!div class="nextstepaction"]
> [Pisanie zapytań dla usługi Azure Data Explorer](write-queries.md)
