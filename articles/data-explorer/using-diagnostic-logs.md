---
title: Monitorowanie operacji pozyskiwania usługi Azure Data Explorer przy użyciu dzienników diagnostycznych
description: Dowiedz się, jak skonfigurować dzienniki diagnostyczne dla Eksploratora danych platformy Azure do monitorowania operacji pozyskiwania.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 3e10979e26cacdc0c2071a6030c945adad21a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277424"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Monitorowanie operacji pozyskiwania usługi Azure Data Explorer przy użyciu dzienników diagnostycznych (Wersja zapoznawcza)

Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa do analizy danych, która pozwala w czasie rzeczywistym analizować duże woluminy danych przesyłanych strumieniowo z aplikacji, witryn internetowych, urządzeń IoT i nie tylko. Aby używać usługi Azure Data Explorer, najpierw utwórz klaster, a następnie utwórz w tym klastrze co najmniej jedną bazę danych. Następnie można pozyskiwania (ładowania) danych do tabeli w bazie danych, dzięki czemu można uruchamiać kwerendy przeciwko niemu. [Dzienniki diagnostyczne usługi Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) zawierają dane dotyczące działania zasobów platformy Azure. Usługa Azure Data Explorer używa dzienników diagnostycznych do szczegółowych informacji na temat sukcesów i błędów pozyskiwania. Dzienniki operacji można eksportować do usługi Azure Storage, Event Hub lub Log Analytics w celu monitorowania stanu pozyskiwania. Dzienniki z usługi Azure Storage i usługi Azure Event Hub można przekierować do tabeli w klastrze usługi Azure Data Explorer w celu dalszej analizy.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/)
* Tworzenie [klastra i bazy danych](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Konfigurowanie dzienników diagnostycznych dla klastra eksploratora danych platformy Azure

Dzienniki diagnostyczne mogą służyć do konfigurowania zbierania następujących danych dziennika:
* Pomyślne operacje pozyskiwania: Te dzienniki mają informacje o pomyślnie zakończonych operacjach pozyskiwania.
* Operacje pozyskiwania nie powiodło się: Te dzienniki mają szczegółowe informacje o nieudanych operacjach pozyskiwania, w tym szczegóły błędu. 

Dane są następnie archiwizowane na koncie magazynu, przesyłane strumieniowo do Centrum zdarzeń lub wysyłane do usługi Log Analytics zgodnie ze specyfikacjami.

### <a name="enable-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

Dzienniki diagnostyczne są domyślnie wyłączone. Aby włączyć dzienniki diagnostyczne, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com)wybierz zasób klastra usługi Azure Data Explorer, który chcesz monitorować.
1. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.
  
    ![Dodawanie dzienników diagnostycznych](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Wybierz **pozycję Dodaj ustawienie diagnostyczne**.
1. W oknie **Ustawienia diagnostyki:**
 
    ![Konfiguracja ustawień diagnostyki](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Wybierz **nazwę** dla ustawienia diagnostycznego.
    1. Wybierz jeden lub więcej obiektów docelowych: konto magazynu, Centrum zdarzeń lub usługa Log Analytics.
    1. Wybierz dzienniki do `SucceededIngestion` zebrania: lub `FailedIngestion`.
    1. Wybierz [metryki](using-metrics.md#supported-azure-data-explorer-metrics) do zebrania (opcjonalnie).  
    1. Wybierz **pozycję Zapisz,** aby zapisać nowe ustawienia i metryki dzienników diagnostycznych.
    1. Utwórz **nowe żądanie pomocy technicznej** w witrynie Azure portal, aby zażądać aktywacji dzienników diagnostycznych.

Nowe ustawienia zostaną ustawione w ciągu kilku minut. Dzienniki są następnie wyświetlane w skonfigurowanym celu archiwizacji (konto magazynu, Centrum zdarzeń lub usługa Log Analytics). 

## <a name="diagnostic-logs-schema"></a>Schemat dzienników diagnostycznych

Wszystkie [dzienniki diagnostyczne usługi Azure Monitor mają wspólny schemat najwyższego poziomu.](/azure/azure-monitor/platform/diagnostic-logs-schema) Usługa Azure Data Explorer ma unikatowe właściwości dla własnych zdarzeń. Wszystkie dzienniki są przechowywane w formacie JSON.

### <a name="ingestion-logs-schema"></a>Schemat dzienników pozyskiwania

Ciągi JSON dziennika zawierają elementy wymienione w poniższej tabeli:

|Nazwa               |Opis
|---                |---
|time               |Czas raportu
|resourceId         |Identyfikator zasobu usługi Azure Resource Manager
|operationName      |Nazwa operacji: 'MICROSOFT. KUSTO/KLASTRY/POŁKNIENIE/DZIAŁANIE"
|operationVersion   |Wersja schematu: '1.0' 
|category           |Kategoria operacji. `SucceededIngestion` lub `FailedIngestion`. Właściwości różnią się dla [pomyślnej operacji](#successful-ingestion-operation-log) lub [nieudanej operacji](#failed-ingestion-operation-log).
|properties         |Szczegółowe informacje o operacji.

#### <a name="successful-ingestion-operation-log"></a>Dziennik operacji pośmiania pomyślnego

**Przykład:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**Właściwości dziennika diagnostycznego pomyślnej operacji**

|Nazwa               |Opis
|---                |---
|succeededOn        |Czas połkania
|operationId        |Identyfikator operacji pozyskiwania usługi Azure Data Explorer
|database           |Nazwa docelowej bazy danych
|tabela              |Nazwa tabeli docelowej
|ingestionSourceId  |Identyfikator źródła danych pozyskiwania
|ingestionSourcePath|Ścieżka źródła danych pozyskiwania lub identyfikatora URI obiektu blob
|rootActivityId (AktywnyId)     |Identyfikator działania

#### <a name="failed-ingestion-operation-log"></a>Dziennik operacji pozyskiwania nie powiodło się

**Przykład:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**Właściwości dziennika diagnostycznego operacji nie powiodło się**

|Nazwa               |Opis
|---                |---
|failedOn           |Czas połkania
|operationId        |Identyfikator operacji pozyskiwania usługi Azure Data Explorer
|database           |Nazwa docelowej bazy danych
|tabela              |Nazwa tabeli docelowej
|ingestionSourceId  |Identyfikator źródła danych pozyskiwania
|ingestionSourcePath|Ścieżka źródła danych pozyskiwania lub identyfikatora URI obiektu blob
|rootActivityId (AktywnyId)     |Identyfikator działania
|Szczegóły            |Szczegółowy opis komunikatu o błędzie i awarii
|Errorcode          |Kod błędu 
|status failure      |`Permanent` lub `Transient`. Ponów próbę niepowodzenia przejściowego może zakończyć się pomyślnie.
|pochodziFromUpdatePolicy|Prawda, jeśli błąd pochodzi z zasad aktualizacji
|powinienRetry        |Prawda, jeśli ponowna próby może zakończyć się pomyślnie

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Pozyskiwania i wykonywania zapytań danych monitorowania danych w Eksploratorze danych platformy Azure](ingest-data-no-code.md)
* [Monitorowanie kondycji klastra przy użyciu metryk](using-metrics.md)

