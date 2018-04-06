---
title: Wyświetlanie dzienników diagnostycznych dla usługi Azure Data Lake Store | Dokumentacja firmy Microsoft
description: 'Zrozumienie, jak skonfigurować i dostępu do dzienników diagnostycznych dla usługi Azure Data Lake Store '
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: a5cdc67a138e2316c2e87a72371a6df527cc36ac
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Uzyskiwanie dostępu do dzienników diagnostycznych dla usługi Azure Data Lake Store
Dowiedz się włączyć diagnostyczne dla konta usługi Data Lake Store i sposób wyświetlania dzienników zbierane dla Twojego konta.

Organizacje mogą włączyć rejestrowanie diagnostyczne dla swojego konta usługi Azure Data Lake Store zbierać zapisy inspekcji dostępu do danych, która udostępnia informacje, takie jak listy użytkowników uzyskujących dostęp do danych, jak często jest uzyskiwany dostęp do danych, jak dużo danych jest przechowywany w konta itp. Po włączeniu diagnostyki i/lub żądania są rejestrowane w sposób optymalny. Zarówno żądań i Diagnostyka wpisy dziennika są tworzone tylko w przypadku żądań wysyłanych do punktu końcowego usługi.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Store**. Postępuj zgodnie z instrukcjami w temacie [Rozpoczynanie pracy z usługą Azure Data Lake Store za pomocą witryny Azure Portal](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Włączanie rejestrowania diagnostyki dla konta usługi Data Lake Store
1. Zaloguj się w nowej witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz konto usługi Data Lake Store, a w bloku konta usługi z usługą Data Lake Store, kliknij przycisk **dzienniki diagnostyczne**.
3. W **dzienników diagnostycznych** bloku, kliknij przycisk **Włącz diagnostykę**.

    ![Włączanie rejestrowania diagnostyki](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Włączanie dzienników diagnostycznych")

3. W **diagnostycznych** bloku, wprowadź następujące zmiany, aby skonfigurować rejestrowanie diagnostyczne.
   
    ![Włączanie rejestrowania diagnostyki](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Włączanie dzienników diagnostycznych")
   
   * Aby uzyskać **nazwa**, wprowadź wartość dla konfiguracji dziennika diagnostycznego.
   * Istnieje możliwość magazynu/procesu dane w różny sposób.
     
        * Wybierz opcję **archiwum na konto magazynu** do przechowywania dzienników do konta usługi Azure Storage. Użyj tej opcji, jeśli chcesz zarchiwizować dane, które będą przetwarzane wsadowe w późniejszym terminie. Po wybraniu tej opcji należy podać konto magazynu Azure dzienniki, aby zapisać.
        
        * Wybierz opcję **strumienia do Centrum zdarzeń** strumienia danych dziennika do usługi Azure Event Hub. Prawdopodobnie użyjesz tę opcję, jeśli masz potoku przetwarzania podrzędnego do analizowania przychodzących dzienniki w czasie rzeczywistym. Jeśli wybierzesz tę opcję, podaj szczegóły w Centrum zdarzeń platformy Azure, którego chcesz użyć.

        * Wybierz opcję **wysyłać do analizy dzienników** do analizowania danych dziennika wygenerowanych przy użyciu usługi Analiza dzienników Azure. Jeśli wybierzesz tę opcję, podaj szczegóły dla obszaru roboczego analizy dzienników użyje przeprowadzanie analizy dziennika. Zobacz [widoku lub analizowania danych zebranych przez wyszukiwania dziennika analizy dzienników](../log-analytics/log-analytics-tutorial-viewdata.md) szczegółowe informacje na temat używania analizy dzienników.
     
   * Określ, czy chcesz pobrać dzienniki inspekcji Dzienniki żądań i/lub.
   * Określ liczbę dni, dla których dane muszą zostać zachowane. Przechowywania dotyczy tylko jeśli używasz konta magazynu Azure do archiwum danych dziennika.
   * Kliknij pozycję **Zapisz**.

Po włączeniu ustawienia diagnostyki można obejrzeć dzienniki w **dzienników diagnostycznych** kartę.

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Wyświetlanie dzienników diagnostycznych dla Twojego konta usługi Data Lake Store
Istnieją dwa sposoby, aby wyświetlić dane dziennika dla konta usługi Data Lake Store.

* W widoku ustawienia konta usługi Data Lake Store
* Z konta magazynu Azure, gdzie są przechowywane dane

### <a name="using-the-data-lake-store-settings-view"></a>Używanie widoku ustawień magazynu Data Lake
1. Z konta usługi Data Lake Store **ustawienia** bloku, kliknij przycisk **dzienników diagnostycznych**.
   
    ![Widok rejestrowania diagnostycznego](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "wyświetlania dziennika diagnostycznego") 
2. W **dzienników diagnostycznych** bloku, powinien zostać wyświetlony dziennik skategoryzowany przez **dzienników inspekcji** i **żądania dzienniki**.
   
   * Dzienniki żądań przechwytywania każdego żądania interfejsu API na koncie usługi Data Lake Store.
   * Dzienniki inspekcji są podobne do żądania dzienniki, ale zapewnia bardziej szczegółowego podziału wykonywania operacji na koncie usługi Data Lake Store. Na przykład wywołanie interfejsu API przekazywanych w dziennikach żądanie może spowodować wiele operacji "Dołącz" w dziennikach inspekcji.
3. Aby pobrać dzienniki, kliknij przycisk **Pobierz** łącze dla każdego wpisu dziennika.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Z konta magazynu Azure, która zawiera dane dziennika
1. Otwarcie bloku konto magazynu Azure skojarzony z usługą Data Lake Store dla rejestrowania, a następnie kliknij przycisk obiektów blob. **Usługa Blob** blok zawiera dwa kontenery.
   
    ![Widok rejestrowania diagnostycznego](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "wyświetlania dziennika diagnostycznego")
   
   * Kontener **insights dzienniki inspekcji** zawiera dzienników inspekcji.
   * Kontener **insights dzienniki żądania** zawiera dzienniki żądania.
2. W tych kontenerach dzienniki są przechowywane w następującej struktury.
   
    ![Widok rejestrowania diagnostycznego](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "wyświetlania dziennika diagnostycznego")
   
    Na przykład może być pełną ścieżką do dziennika inspekcji `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Podobnie może być pełną ścieżką do dziennika żądań `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Należy poznać strukturę dane dziennika
Dzienniki inspekcji i żądania są w formacie JSON. W tej sekcji możemy Spójrz na strukturze JSON dla żądania i dzienniki inspekcji.

### <a name="request-logs"></a>Dzienniki żądań
Oto przykładowy wpis w dzienniku żądania w formacie JSON. Każdy obiekt blob ma jeden obiekt głównego o nazwie **rekordów** zawiera tablicę obiektów dziennika.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Schemat dziennika żądania
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| time |Ciąg |Znacznik czasu (w formacie UTC) dziennika |
| resourceId |Ciąg |Identyfikator zasobu, na którym została wykonana operacja umieść na |
| category |Ciąg |Kategoria dziennika. Na przykład **żądań**. |
| operationName |Ciąg |Nazwa operacji, które są rejestrowane. Na przykład getfilestatus. |
| resultType |Ciąg |Stan operacji, na przykład 200. |
| callerIpAddress |Ciąg |Adres IP klienta zgłoszenia żądania |
| correlationId |Ciąg |Identyfikator dziennika, który można używane do grupowania zbiór wpisów dziennika pokrewne |
| identity |Obiekt |Tożsamości, który wygenerował dziennika |
| properties |JSON |Wymienione poniżej, aby uzyskać więcej informacji |

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
Oto przykładowy wpis w dzienniku inspekcji w formacie JSON. Każdy obiekt blob ma jeden obiekt głównego o nazwie **rekordów** zawiera tablicę obiektów dziennika

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "resultSignature": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Schemat dziennika inspekcji
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| time |Ciąg |Znacznik czasu (w formacie UTC) dziennika |
| resourceId |Ciąg |Identyfikator zasobu, na którym została wykonana operacja umieść na |
| category |Ciąg |Kategoria dziennika. Na przykład **inspekcji**. |
| operationName |Ciąg |Nazwa operacji, które są rejestrowane. Na przykład getfilestatus. |
| resultType |Ciąg |Stan operacji, na przykład 200. |
| resultSignature |Ciąg |Więcej informacji na temat operacji. |
| correlationId |Ciąg |Identyfikator dziennika, który można używane do grupowania zbiór wpisów dziennika pokrewne |
| identity |Obiekt |Tożsamości, który wygenerował dziennika |
| properties |JSON |Wymienione poniżej, aby uzyskać więcej informacji |

#### <a name="audit-log-properties-schema"></a>Schemat właściwości dziennika inspekcji
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| StreamName |Ciąg |Ścieżka operacja została wykonana na |

## <a name="samples-to-process-the-log-data"></a>Przykłady do przetwarzania danych dziennika
Podczas wysyłania dzienników z usługi Azure Data Lake Store Analiza dzienników Azure (zobacz [widoku lub analizowania danych zebranych przez wyszukiwania dziennika analizy dzienników](../log-analytics/log-analytics-tutorial-viewdata.md) szczegółowe informacje na temat używania analizy dzienników), następujące zapytanie zwraca tabelę zawierającą listę użytkowników Wyświetlanie nazw, czas zdarzenia, a liczba zdarzeń czasu dla zdarzeń oraz visual wykresu. Łatwo możesz to modyfikować, aby wyświetlić identyfikator GUID użytkownika lub innych atrybutów:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Store zapewnia próbkę na temat przetwarzanie i analizowanie danych dziennika. Na przykład można znaleźć [ https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample ](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure Data Lake Store](data-lake-store-overview.md)
* [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md)

