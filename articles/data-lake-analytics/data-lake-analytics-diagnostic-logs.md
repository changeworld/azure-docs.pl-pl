---
title: Wyświetlanie dzienników diagnostycznych dla usługi Azure Data Lake Analytics | Dokumentacja firmy Microsoft
description: 'Zrozumienie, jak skonfigurować i dostępu do dzienników diagnostycznych dla usługi Azure Data Lake analytics '
services: data-lake-analytics
documentationcenter: ''
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/12/2018
ms.openlocfilehash: efec1e00e9c2da519028f7e6ff094a0f0876df79
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Uzyskiwanie dostępu do dzienników diagnostycznych dla usługi Azure Data Lake Analytics

Rejestrowanie diagnostyczne umożliwia zbieranie zapisy inspekcji dostępu do danych. Te dzienniki zawierają informacje, takie jak:

* Lista użytkowników, które uzyskiwały dostęp do danych.
* Jak często jest uzyskiwany dostęp do danych.
* Ile dane są przechowywane w ramach konta.

## <a name="enable-logging"></a>Włącz rejestrowanie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Otwórz konto usługi Data Lake Analytics i wybierz **dzienniki diagnostyczne** z __Monitor__ sekcji. Następnie wybierz pozycję __Włącz diagnostykę__.

    ![Włącz diagnostykę do zbierania danych inspekcji i Dzienniki żądań](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. Z __ustawień diagnostycznych__, wprowadź __nazwa__ dla tej konfiguracji rejestrowania i rejestrowanie a następnie wybierz pozycję Opcje.

    ![Włącz diagnostykę do zbierania danych inspekcji i Dzienniki żądań](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Włączanie dzienników diagnostycznych")

   * Istnieje możliwość magazynu/procesu dane na trzy różne sposoby.

     * Wybierz __archiwum na konto magazynu__ do przechowywania dzienników dla konta magazynu platformy Azure. Użyj tej opcji, jeśli chcesz zarchiwizować dane. Jeśli wybierzesz tę opcję, należy podać konto magazynu Azure dzienniki, aby zapisać.

     * Wybierz **strumienia do Centrum zdarzeń** strumienia danych dziennika do usługi Azure Event Hub. Użyj tej opcji, jeśli masz potoku przetwarzania podrzędnego, która analizuje przychodzące dzienniki w czasie rzeczywistym. Jeśli wybierzesz tę opcję, podaj szczegóły w Centrum zdarzeń platformy Azure, którego chcesz użyć.

     * Wybierz __wysyłać do analizy dzienników__ do wysyłania danych do usługi analizy dzienników. Użyj tej opcji, jeśli chcesz użyć do zbierania i analizowania dzienników analizy dzienników.
   * Określ, czy chcesz pobrać dzienniki inspekcji Dzienniki żądań i/lub.  Dziennik żądań przechwytuje każde żądanie interfejsu API. Dziennik inspekcji rejestruje wszystkie operacje, które są uruchamiane w tym żądania interfejsu API.

   * Aby uzyskać __archiwum na konto magazynu__, określ liczbę dni, aby zachować dane.

   * Kliknij przycisk __Save__ (Zapisz).

        > [!NOTE]
        > Musisz wybrać __archiwum na konto magazynu__, __strumienia do Centrum zdarzeń__ lub __wysyłać do analizy dzienników__ przed kliknięciem przycisku __zapisać__ przycisk.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Użyj konta magazynu Azure, która zawiera dane dziennika

1. Aby wyświetlić kontenerów obiektów blob, które zawierają dane rejestrowania, otwieranie konta usługi Azure Storage używane na potrzeby rejestrowania dla usługi Data Lake Analytics, a następnie kliknij __obiekty BLOB__.

   * Kontener **insights dzienniki inspekcji** zawiera dzienników inspekcji.
   * Kontener **insights dzienniki żądania** zawiera dzienniki żądania.

2. W kontenerach dzienniki są przechowywane w następującej struktury plików:

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
   > `##` Wpisy w ścieżce zawierają rok, miesiąc, dzień i godzinę, w którym utworzono dziennik. Data Lake Analytics tworzą jeden plik co godzinę, więc `m=` zawsze zawiera wartość `00`.

    Na przykład może być pełną ścieżką do dziennika inspekcji:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Podobnie może być pełną ścieżką do dziennika żądania:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Struktura dziennika

Dzienniki inspekcji i żądania są w formacie JSON strukturalnych.

### <a name="request-logs"></a>Dzienniki żądań

Oto przykładowy wpis w dzienniku żądania w formacie JSON. Każdy obiekt blob ma jeden obiekt głównego o nazwie **rekordów** zawiera tablicę obiektów dziennika.

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
| resourceId |Ciąg |Identyfikator zasobu, w którym została wykonana operacja umieść na |
| category |Ciąg |Kategoria dziennika. Na przykład **żądań**. |
| operationName |Ciąg |Nazwa operacji, które są rejestrowane. Na przykład GetAggregatedJobHistory. |
| resultType |Ciąg |Stan operacji, na przykład 200. |
| callerIpAddress |Ciąg |Adres IP klienta zgłoszenia żądania |
| correlationId |Ciąg |Identyfikator dziennika. Ta wartość może być używana do grupowania zestawu wpisów dziennika powiązane. |
| identity |Obiekt |Tożsamości, który wygenerował dziennika |
| properties |JSON |W następnej sekcji (schemat właściwości dziennika żądania) Aby uzyskać więcej informacji |

#### <a name="request-log-properties-schema"></a>Schemat właściwości dziennika żądania

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| HttpMethod |Ciąg |Metoda HTTP używana dla tej operacji. Na przykład GET. |
| Ścieżka |Ciąg |Ścieżka operacja została wykonana na |
| RequestContentLength |int |Długość treści żądania HTTP |
| ClientRequestId |Ciąg |Identyfikator, który unikatowo identyfikuje tego żądania |
| Godzina rozpoczęcia |Ciąg |Czas, w którym serwer odebrał żądanie |
| Godzina zakończenia |Ciąg |Czas wysłanego przez serwer odpowiedzi |

### <a name="audit-logs"></a>Dzienniki inspekcji

Oto przykładowy wpis w dzienniku inspekcji w formacie JSON. Każdy obiekt blob ma jeden obiekt głównego o nazwie **rekordów** zawiera tablicę obiektów dziennika.

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
| resourceId |Ciąg |Identyfikator zasobu, w którym została wykonana operacja umieść na |
| category |Ciąg |Kategoria dziennika. Na przykład **inspekcji**. |
| operationName |Ciąg |Nazwa operacji, które są rejestrowane. Na przykład JobSubmitted. |
| resultType |Ciąg |Podstan stanu zadania (operationName). |
| resultSignature |Ciąg |Więcej informacji na temat stanu zadania (operationName). |
| identity |Ciąg |Użytkownik, który żądanej operacji. Na przykład susan@contoso.com. |
| properties |JSON |W następnej sekcji (schemat właściwości dziennika inspekcji) Aby uzyskać więcej informacji |

> [!NOTE]
> **Typ resultType** i **resultSignature** udostępniają informacje dotyczące wynik operacji i zawierać tylko wartości, jeśli operacja została ukończona. Na przykład tylko z wartościami, gdy **operationName** zawiera wartość **JobStarted** lub **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Schemat właściwości dziennika inspekcji

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| JobId |Ciąg |Identyfikator przypisany do zadania |
| JobName |Ciąg |Nazwa podany dla zadania |
| JobRunTime |Ciąg |Środowisko uruchomieniowe używane do przetwarzania zadania |
| Godzina przesłania |Ciąg |Czas (w formacie UTC), że zadanie zostało przesłane |
| Godzina rozpoczęcia |Ciąg |Przy uruchomieniu zadania uruchomione po przesłaniu (w formacie UTC) |
| Godzina zakończenia |Ciąg |Godzina zakończenia zadania |
| Równoległość |Ciąg |Liczba wymagane dla tego zadania podczas przesyłania jednostki usługi Data Lake Analytics |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime**, i **równoległości** udostępniają informacje dotyczące operacji. Te wpisy tylko zawierać wartość, jeśli operacja ma uruchomione lub ukończone. Na przykład **SubmitTime** zawiera tylko wartości po **operationName** ma wartość **JobSubmitted**.

## <a name="process-the-log-data"></a>Przetwarzaj dane dziennika

Azure Data Lake Analytics zawiera przykładowe przetwarzanie i analizowanie danych dziennika. Na przykład można znaleźć [ https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample ](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
