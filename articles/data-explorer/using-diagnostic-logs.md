---
title: Monitorowanie operacji pozyskiwania Eksplorator danych platformy Azure przy użyciu dzienników diagnostycznych
description: Dowiedz się, jak skonfigurować dzienniki diagnostyczne dla Eksplorator danych platformy Azure, aby monitorować operacje pozyskiwania.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 7d0fec56791c0d3e7ae60d78da83cf286532b9ab
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71124008"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Monitorowanie operacji pozyskiwania Eksplorator danych platformy Azure przy użyciu dzienników diagnostycznych (wersja zapoznawcza)

Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa do analizy danych, która pozwala w czasie rzeczywistym analizować duże woluminy danych przesyłanych strumieniowo z aplikacji, witryn internetowych, urządzeń IoT i nie tylko. Aby używać usługi Azure Data Explorer, najpierw utwórz klaster, a następnie utwórz w tym klastrze co najmniej jedną bazę danych. Następnie Pozyskaj (Załaduj) dane do tabeli w bazie danych, aby można było uruchamiać zapytania względem tego programu. [Azure monitor dzienników diagnostycznych](/azure/azure-monitor/platform/diagnostic-logs-overview) zapewniają dane dotyczące operacji zasobów platformy Azure. Usługa Azure Eksplorator danych używa dzienników diagnostycznych do uzyskiwania szczegółowych informacji na temat sukcesów i niepowodzeń pozyskiwania. Dzienniki operacji można wyeksportować do usługi Azure Storage, centrum zdarzeń lub Log Analytics, aby monitorować stan pozyskiwania. Dzienniki z usługi Azure Storage i usługi Azure Event Hub można kierować do tabeli w klastrze Eksplorator danych platformy Azure w celu przeprowadzenia dalszej analizy.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* Utwórz [klaster i bazę danych](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Konfigurowanie dzienników diagnostycznych dla klastra usługi Azure Eksplorator danych

Dzienników diagnostycznych można użyć do skonfigurowania kolekcji następujących danych dziennika:
* Pomyślne operacje pozyskiwania: Te dzienniki zawierają informacje o pomyślnym zakończeniu operacji pozyskiwania.
* Nieudane operacje pozyskiwania: Te dzienniki zawierają szczegółowe informacje o nieudanych operacjach pozyskiwania, w tym szczegóły błędu. 

Dane są następnie archiwizowane na koncie magazynu, przesyłane strumieniowo do centrum zdarzeń lub wysyłane do Log Analytics zgodnie ze specyfikacją.

### <a name="enable-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

Dzienniki diagnostyczne są domyślnie wyłączone. Aby włączyć dzienniki diagnostyczne, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)wybierz zasób klastra usługi Azure Eksplorator danych, który ma być monitorowany.
1. W obszarze **monitorowanie**, wybierz opcję **ustawień diagnostycznych**.
  
    ![Dodawanie dzienników diagnostycznych](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Wybierz pozycję **Dodaj ustawienie diagnostyczne**.
1. W oknie **Ustawienia diagnostyczne** :
 
    ![Konfiguracja ustawień diagnostycznych](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Wybierz **nazwę** dla ustawienia diagnostycznego.
    1. Wybierz co najmniej jeden obiekt docelowy: konto magazynu, centrum zdarzeń lub Log Analytics.
    1. Wybierz dzienniki do zebrania: `SucceededIngestion` lub `FailedIngestion`.
    1. Wybierz [metryki](using-metrics.md) do zebrania (opcjonalnie).   
    1. Wybierz pozycję **Zapisz** , aby zapisać nowe ustawienia dzienników diagnostycznych i metryki.
    1. Utwórz **nowe żądanie obsługi** w Azure Portal, aby zażądać aktywacji dzienników diagnostycznych.

Nowe ustawienia zostaną ustawione w ciągu kilku minut. Dzienniki są następnie wyświetlane w skonfigurowanym celu archiwizacji (konto magazynu, centrum zdarzeń lub Log Analytics). 

## <a name="diagnostic-logs-schema"></a>Dzienniki diagnostyczne schematu

Wszystkie [dzienniki diagnostyczne Azure monitor korzystają ze wspólnego schematu najwyższego poziomu](/azure/azure-monitor/platform/diagnostic-logs-schema). Usługa Azure Eksplorator danych ma unikatowe właściwości dla swoich własnych zdarzeń. Wszystkie dzienniki są przechowywane w formacie JSON.

### <a name="ingestion-logs-schema"></a>Schemat dzienników pozyskiwania

Ciągi JSON dziennika zawierają elementy wymienione w poniższej tabeli:

|Name               |Opis
|---                |---
|time               |Godzina raportu
|resourceId         |Identyfikator zasobu Azure Resource Manager
|operationName      |Nazwa operacji: PROGRAMU. KUSTO/KLASTRY/POZYSKIWANIE/AKCJA "
|operationVersion   |Wersja schematu: "1,0" 
|category           |Kategoria operacji. `SucceededIngestion`lub `FailedIngestion`. Właściwości różnią się w zależności od [operacji zakończonej powodzeniem](#successful-ingestion-operation-log) lub [nieudanej operacji](#failed-ingestion-operation-log).
|properties         |Szczegółowe informacje o operacji.

#### <a name="successful-ingestion-operation-log"></a>Dziennik operacji pozyskiwania zakończonych powodzeniem

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

|Name               |Opis
|---                |---
|succeededOn        |Czas ukończenia pozyskiwania
|operationId        |Identyfikator operacji pozyskiwania Eksplorator danych platformy Azure
|database           |Nazwa docelowej bazy danych
|table              |Nazwa tabeli docelowej
|ingestionSourceId  |Identyfikator źródła danych pozyskiwania
|ingestionSourcePath|Ścieżka do źródła danych pozyskiwania lub identyfikatora URI obiektu BLOB
|rootActivityId     |Identyfikator działania

#### <a name="failed-ingestion-operation-log"></a>Dziennik operacji pozyskiwania zakończonych niepowodzeniem

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

**Właściwości dziennika diagnostyki operacji zakończonej niepowodzeniem**

|Name               |Opis
|---                |---
|failedOn           |Czas ukończenia pozyskiwania
|operationId        |Identyfikator operacji pozyskiwania Eksplorator danych platformy Azure
|database           |Nazwa docelowej bazy danych
|table              |Nazwa tabeli docelowej
|ingestionSourceId  |Identyfikator źródła danych pozyskiwania
|ingestionSourcePath|Ścieżka do źródła danych pozyskiwania lub identyfikatora URI obiektu BLOB
|rootActivityId     |Identyfikator działania
|details informacje            |Szczegółowy opis błędu i komunikatu o błędzie
|Kodzie          |Kod błędu 
|failureStatus      |`Permanent`lub `Transient`. Ponowna próba błędu przejściowego może zakończyć się pomyślnie.
|originatesFromUpdatePolicy|Prawda, jeśli niepowodzenie pochodzi z zasad aktualizacji
|shouldRetry        |Prawda, jeśli próba powiodła się

## <a name="next-steps"></a>Następne kroki

[Monitorowanie kondycji klastra przy użyciu metryk](using-metrics.md)
