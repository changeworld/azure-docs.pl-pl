---
title: Włączanie i wyświetlanie dzienników diagnostycznych dla usługi Azure Data Lake Analytics
description: Dowiedz się, jak skonfigurować dzienniki diagnostyczne usługi Azure Data Lake Analytics i uzyskiwać do nich dostęp
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.topic: conceptual
ms.date: 02/12/2018
ms.openlocfilehash: 7fd88383e909ebd6be64c22721b813946e37179e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60616511"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Accessing diagnostic logs for Azure Data Lake Analytics (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics)

Rejestrowanie diagnostyczne umożliwia zbieranie śladów inspekcji dostępu do danych. Te dzienniki zawierają informacje, takie jak:

* Lista użytkowników, którzy uzyskili dostęp do danych.
* Jak często dane są dostępne.
* Ile danych jest przechowywanych na koncie.

## <a name="enable-logging"></a>Włącz rejestrowanie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Otwórz konto usługi Data Lake Analytics i wybierz **dzienniki diagnostyczne** w sekcji __Monitor.__ Następnie wybierz pozycję __Włącz diagnostykę__.

    ![Włączanie diagnostyki w celu zbierania dzienników inspekcji i żądań](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. W __obszarze Ustawienia diagnostyki__wprowadź __nazwę__ dla tej konfiguracji rejestrowania, a następnie wybierz opcję rejestrowania.

    ![Włączanie diagnostyki w celu zbierania dzienników inspekcji i żądań](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Włączanie dzienników diagnostycznych")

   * Można zapisać/przetworzyć dane na trzy różne sposoby.

     * Wybierz __opcję Archiwizuj konto magazynu,__ aby przechowywać dzienniki na koncie magazynu platformy Azure. Użyj tej opcji, jeśli chcesz zarchiwizować dane. Jeśli wybierzesz tę opcję, musisz podać konto magazynu platformy Azure, aby zapisać dzienniki.

     * Wybierz **opcję Strumień do Centrum zdarzeń,** aby przesyłać strumieniowo dane dziennika do usługi Azure Event Hub. Użyj tej opcji, jeśli masz potok przetwarzania podrzędnego, który analizuje przychodzące dzienniki w czasie rzeczywistym. Jeśli wybierzesz tę opcję, musisz podać szczegóły dotyczące usługi Azure Event Hub, którego chcesz użyć.

     * Wybierz __pozycję Wyślij do usługi Log Analytics,__ aby wysłać dane do usługi Azure Monitor. Użyj tej opcji, jeśli chcesz używać dzienników usługi Azure Monitor do zbierania i analizowania dzienników.
   * Określ, czy chcesz uzyskać dzienniki inspekcji lub dzienniki żądań, czy oba te dzienniki.  Dziennik żądań przechwytuje każde żądanie interfejsu API. Dziennik inspekcji rejestruje wszystkie operacje, które są wyzwalane przez to żądanie interfejsu API.

   * W przypadku __konta magazynu Archiwum__należy określić liczbę dni przechowywania danych.

   * Kliknij przycisk __Zapisz__.

        > [!NOTE]
        > Przed kliknięciem przycisku __Zapisz__ należy wybrać __opcję Archiwizuj do konta magazynu,__ __Strumień do Centrum zdarzeń__ lub Wyślij do usługi Log __Analytics.__

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Korzystanie z konta usługi Azure Storage zawierającego dane dziennika

1. Aby wyświetlić kontenery obiektów blob, w których przechowywane są dane rejestrowania, otwórz konto usługi Azure Storage używane do rejestrowania usługi Data Lake Analytics, a następnie kliknij przycisk __Obiekty Blobs__.

   * Kontener **insights-logs-audit** zawiera dzienniki inspekcji.
   * Kontener **insights-logs-requests** zawiera dzienniki żądań.

2. W kontenerach dzienniki są przechowywane w następującej strukturze plików:

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json

   > [!NOTE]
   > `##` Wpisy w ścieżce zawierają rok, miesiąc, dzień i godzinę, w którym został utworzony dziennik. Data Lake Analytics tworzy jeden `m=` plik co godzinę, więc zawsze zawiera wartość `00`.

    Na przykład pełna ścieżka do dziennika inspekcji może być:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Podobnie pełna ścieżka do dziennika żądań może być:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Struktura dziennika

Dzienniki inspekcji i żądań są w ustrukturyzowanym formacie JSON.

### <a name="request-logs"></a>Dzienniki żądań

Oto przykładowy wpis w dzienniku żądań w formacie JSON. Każdy obiekt blob ma jeden obiekt główny o nazwie **rekordy,** który zawiera tablicę obiektów dziennika.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Schemat dziennika żądań

| Nazwa | Typ | Opis |
| --- | --- | --- |
| time |Ciąg |Sygnatura czasowa (w czasie UTC) dziennika |
| resourceId |Ciąg |Identyfikator zasobu, który operacja miała miejsce na |
| category |Ciąg |Kategoria dziennika. Na przykład **żądania**. |
| operationName |Ciąg |Nazwa operacji, która jest rejestrowana. Na przykład GetAggregatedJobHistory. |
| resultType |Ciąg |Stan operacji, Na przykład 200. |
| callerIpAddress |Ciąg |Adres IP klienta składającego wniosek |
| correlationId |Ciąg |Identyfikator dziennika. Ta wartość może służyć do grupowania zestawu powiązanych wpisów dziennika. |
| identity |Obiekt |Tożsamość, która wygenerowała dziennik |
| properties |JSON |Szczegółowe informacje można znaleźć w następnej sekcji (Schemat właściwości dziennika żądania) |

#### <a name="request-log-properties-schema"></a>Schemat właściwości dziennika żądania

| Nazwa | Typ | Opis |
| --- | --- | --- |
| HttpMethod (httpmethod) |Ciąg |Metoda HTTP używana do operacji. Na przykład GET. |
| Ścieżka |Ciąg |Ścieżka, na którą została wykonana operacja |
| ProśbaContentLength |int |Długość zawartości żądania HTTP |
| Identyfikator zapisu klienta |Ciąg |Identyfikator, który jednoznacznie identyfikuje to żądanie |
| StartTime |Ciąg |Czas, w którym serwer odebrał żądanie |
| EndTime |Ciąg |Czas, w którym serwer wysłał odpowiedź |

### <a name="audit-logs"></a>Dzienniki inspekcji

Oto przykładowy wpis w dzienniku inspekcji w formacie JSON. Każdy obiekt blob ma jeden obiekt główny o nazwie **rekordy,** który zawiera tablicę obiektów dziennika.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job",
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Schemat dziennika inspekcji

| Nazwa | Typ | Opis |
| --- | --- | --- |
| time |Ciąg |Sygnatura czasowa (w czasie UTC) dziennika |
| resourceId |Ciąg |Identyfikator zasobu, który operacja miała miejsce na |
| category |Ciąg |Kategoria dziennika. Na przykład **Inspekcja**. |
| operationName |Ciąg |Nazwa operacji, która jest rejestrowana. Na przykład JobSubmitted. |
| resultType |Ciąg |Podstatus stanu zadania (operationName). |
| resultSignature |Ciąg |Dodatkowe szczegóły dotyczące stanu zadania (operationName). |
| identity |Ciąg |Użytkownik, który zażądał operacji. Na przykład susan@contoso.com. |
| properties |JSON |Szczegółowe informacje można znaleźć w następnej sekcji (Schemat właściwości dziennika inspekcji) |

> [!NOTE]
> **resultType** i **resultSygnatura** podają informacje o wyniku operacji i zawierają wartość tylko wtedy, gdy operacja została zakończona. Na przykład zawierają one tylko wartość, gdy **operationName** zawiera wartość **JobStarted** lub **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Schemat właściwości dziennika inspekcji

| Nazwa | Typ | Opis |
| --- | --- | --- |
| JobId |Ciąg |Identyfikator przypisany do zadania |
| Jobname |Ciąg |Nazwa, która została podana dla zadania |
| Czas pracy |Ciąg |Środowisko wykonawcze używane do przetwarzania zadania |
| Czas przesyłania |Ciąg |Czas (w czasie UTC), w której zadanie zostało przesłane |
| StartTime |Ciąg |Czas rozpoczęcia pracy po przesłaniu (w czasie UTC) |
| EndTime |Ciąg |Czas zakończenia zadania |
| Równoległości prostych |Ciąg |Liczba jednostek Data Lake Analytics wymaganych dla tego zadania podczas przesyłania |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime**i **Równoległość** zawierają informacje o operacji. Te wpisy zawierają wartość tylko wtedy, gdy ta operacja została uruchomiona lub ukończona. Na przykład **SubmitTime** zawiera tylko wartość po **operationName** ma wartość **JobSubmitted**.

## <a name="process-the-log-data"></a>Przetwarzanie danych dziennika

Usługa Azure Data Lake Analytics zawiera przykład sposobu przetwarzania i analizowania danych dziennika. Przykład można znaleźć [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)w pliku .

## <a name="next-steps"></a>Następne kroki
* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
