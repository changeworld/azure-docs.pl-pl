---
title: Włączanie i wyświetlanie dzienników diagnostycznych usługi Azure Data Lake Analytics
description: Dowiedz się, jak skonfigurować i dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.topic: conceptual
ms.date: 02/12/2018
ms.openlocfilehash: 0bade9f393d879123b7b1485052f70924d9c9b9c
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045485"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Uzyskiwanie dostępu do dzienników diagnostycznych usługi Azure Data Lake Analytics

Rejestrowanie diagnostyczne można zbierać ślady inspekcji dostępu do danych. Te dzienniki zawierają informacje, takie jak:

* Lista użytkowników, którzy dostęp do danych.
* Jak często uzyskać dostępu do danych.
* Jak dużo danych jest przechowywany na koncie.

## <a name="enable-logging"></a>Włącz rejestrowanie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Otwórz konto usługi Data Lake Analytics i wybierz **dzienniki diagnostyczne** z __Monitor__ sekcji. Następnie wybierz pozycję __Włącz diagnostykę__.

    ![Włącz diagnostykę do zbierania danych inspekcji i Dziennik żądań](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. Z __ustawień diagnostycznych__, wprowadź __nazwa__ dla tej konfiguracji rejestrowania i rejestrowania a następnie wybierz pozycję Opcje.

    ![Włącz diagnostykę do zbierania danych inspekcji i Dzienniki żądań](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Włączanie dzienników diagnostycznych")

   * Istnieje możliwość magazyn i przetwarzanie tych danych na trzy różne sposoby.

     * Wybierz __Zarchiwizuj na koncie magazynu__ do przechowywania dzienników na koncie usługi Azure storage. Użyj tej opcji, jeśli chcesz zarchiwizować dane. Jeśli wybierzesz tę opcję, musisz podać konto magazynu platformy Azure, aby zapisać dzienniki.

     * Wybierz **Stream do usługi Event Hub** przesyłanie strumieniowe dzienników danych do usługi Azure Event Hub. Użyj tej opcji, jeśli masz potok przetwarzania transmisji dla klientów, który analizuje przychodzące dzienników w czasie rzeczywistym. Jeśli wybierzesz tę opcję, musisz podać szczegółowe informacje dla usługi Azure Event Hub, którego chcesz użyć.

     * Wybierz __wysyłanie do usługi Log Analytics__ do wysyłania danych do usługi Log Analytics. Użyj tej opcji, jeśli chcesz użyć usługi Log Analytics można zbierać i analizować dzienniki.
   * Określ, czy chcesz pobrać dzienniki inspekcji, dzienniki żądania lub obu.  Dziennik żądań przechwytuje każde żądanie interfejsu API. Dziennik inspekcji rejestruje wszystkie operacje, które są wyzwalane przez żądanie tego interfejsu API.

   * Aby uzyskać __Zarchiwizuj na koncie magazynu__, określ liczbę dni przechowywania danych.

   * Kliknij pozycję __Zapisz__.

        > [!NOTE]
        > Należy wybrać __Zarchiwizuj na koncie magazynu__, __Stream do usługi Event Hub__ lub __wysyłanie do usługi Log Analytics__ przed kliknięciem przycisku __Zapisz__ przycisk.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Użyj konta usługi Azure Storage, która zawiera dane dziennika

1. Aby wyświetlić kontenery obiektów blob, w których przechowywane są dane rejestrowania, otwórz konto usługi Azure Storage używane usługi Data Lake Analytics do rejestrowania, a następnie kliknij przycisk __obiektów blob__.

   * Kontener **insights — dzienniki inspekcji** zawiera dzienniki inspekcji.
   * Kontener **insights Dzienniki żądań** zawiera dzienniki żądania.

2. W kontenerach dzienniki są przechowywane w obszarze następującej struktury plików:

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
   > `##` Wpisy w ścieżce zawiera rok, miesiąc, dzień i godzinę, w którym utworzono dziennik. Usługa Data Lake Analytics tworzy jeden plik co godzinę, więc `m=` zawsze zawiera wartość `00`.

    Na przykład może być pełną ścieżką do dziennika inspekcji:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Podobnie Pełna ścieżka do dziennika żądań może być:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Struktura dziennika

Dzienniki inspekcji i żądania są w formacie JSON ze strukturą.

### <a name="request-logs"></a>Dziennik żądań

Poniżej przedstawiono przykładowy wpis w dzienniku żądania w formacie JSON. Każdy obiekt blob ma jeden główny obiekt o nazwie **rekordów** zawierający tablicę obiektów dziennika.

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

#### <a name="request-log-schema"></a>Schemat dziennika żądania

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| time |Ciąg |Znacznik czasu (w formacie UTC) dziennika |
| resourceId |Ciąg |Identyfikator zasobu, która trwała operacja umieść na |
| category |Ciąg |Kategoria dziennika. Na przykład **żądań**. |
| operationName |Ciąg |Nazwa operacji, który jest zalogowany. Na przykład GetAggregatedJobHistory. |
| resultType |Ciąg |Stan operacji, na przykład 200. |
| callerIpAddress |Ciąg |Adres IP klienta wysyłającego żądanie |
| correlationId |Ciąg |Identyfikator dziennika. Ta wartość może służyć do grupowania zbiór wpisów dziennika powiązane. |
| identity |Obiekt |Tożsamość, która wygenerowała dziennika |
| properties |JSON |W następnej sekcji (schemat właściwości dziennika żądania) Aby uzyskać szczegółowe informacje |

#### <a name="request-log-properties-schema"></a>Schemat właściwości dziennika żądania

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| HttpMethod |Ciąg |Metoda HTTP używana dla tej operacji. Na przykład UZYSKAĆ. |
| Ścieżka |Ciąg |Ścieżka operacja została wykonana w |
| RequestContentLength |Int |Długość zawartości żądania HTTP |
| ClientRequestId |Ciąg |Identyfikator, który unikatowo identyfikuje tego żądania |
| Godzina rozpoczęcia |Ciąg |Czas, w którym serwer odebrał żądanie |
| Godzina zakończenia |Ciąg |Czas wysłanego przez serwer odpowiedzi |

### <a name="audit-logs"></a>Dzienniki inspekcji

Poniżej przedstawiono przykładowy wpis w dzienniku inspekcji w formacie JSON. Każdy obiekt blob ma jeden główny obiekt o nazwie **rekordów** zawierający tablicę obiektów dziennika.

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

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| time |Ciąg |Znacznik czasu (w formacie UTC) dziennika |
| resourceId |Ciąg |Identyfikator zasobu, która trwała operacja umieść na |
| category |Ciąg |Kategoria dziennika. Na przykład **inspekcji**. |
| operationName |Ciąg |Nazwa operacji, który jest zalogowany. Na przykład JobSubmitted. |
| resultType |Ciąg |Podstan stan zadania (operationName). |
| resultSignature |Ciąg |Więcej informacji na temat stanu zadania (operationName). |
| identity |Ciąg |Użytkownik, który zażądał operacji. Na przykład susan@contoso.com. |
| properties |JSON |W następnej sekcji (schemat właściwości dziennika inspekcji) Aby uzyskać szczegółowe informacje |

> [!NOTE]
> **Typ resultType** i **resultSignature** zawierają informacje na temat wynik operacji i zawierać tylko wartości, jeśli operacja została ukończona. Na przykład tylko z wartościami, gdy **operationName** znajduje się wartość **JobStarted** lub **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Schemat właściwości dziennika inspekcji

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| JobId |Ciąg |Identyfikator przypisany do zlecenia |
| JobName |Ciąg |Nazwa która została podana dla zadania |
| JobRunTime |Ciąg |Środowiska uruchomieniowego użytego do przetworzenia zadania |
| Godzina przesłania |Ciąg |Czas (w formacie UTC), które przesłano zadanie |
| Godzina rozpoczęcia |Ciąg |Uruchomienia zadania uruchamiania po przesłaniu wizualizacji (w formacie UTC) |
| Godzina zakończenia |Ciąg |Czas zakończenia zadania |
| Równoległość |Ciąg |Liczba wymagane dla tego zadania podczas przesyłania jednostki usługi Data Lake Analytics |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime**, i **równoległości** zawierają informacje na temat operacji. Te wpisy tylko zawierać wartości, jeśli operacja ma rozpoczęciu lub zakończeniu, które. Na przykład **SubmitTime** zawiera tylko wartości po **operationName** ma wartość **JobSubmitted**.

## <a name="process-the-log-data"></a>Przetwarzanie danych dziennika

Usługa Azure Data Lake Analytics zawiera przykładowy na temat sposobu przetwarzania i analizowania danych dziennika. Można znaleźć przykład w [ https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample ](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
