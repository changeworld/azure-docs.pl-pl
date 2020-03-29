---
title: Wyświetlanie dzienników diagnostycznych dla usługi Azure Data Lake Storage Gen1 | Dokumenty firmy Microsoft
description: 'Dowiedz się, jak skonfigurować dzienniki diagnostyczne usługi Azure Data Storage Gen1 i uzyskiwać do nich dostęp '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: d200f72b3c0e5634c3dca8f60a4754a14351110a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878758"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Uzyskiwanie dostępu do dzienników diagnostycznych dla usługi Azure Data Lake Storage Gen1
Dowiedz się, jak włączyć rejestrowanie diagnostyczne konta usługi Azure Data Lake Storage Gen1 i jak wyświetlać dzienniki zebrane dla konta.

Organizacje mogą włączyć rejestrowanie diagnostyczne dla swojego konta usługi Azure Data Lake Storage Gen1 w celu zbierania dzienników inspekcji dostępu do danych, które zawierają informacje, takie jak lista użytkowników uzyskujących dostęp do danych, jak często dane są uzyskiwany, ile danych jest przechowywanych na koncie itp. Po włączeniu diagnostyki i/lub żądania są rejestrowane na podstawie najlepszych starań. Wpisy dziennika żądania i diagnostyka są tworzone tylko wtedy, gdy istnieją żądania względem punktu końcowego usługi.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami w [usłudze Wprowadzenie do usługi Azure Data Lake Storage Gen1 przy użyciu witryny Azure Portal.](data-lake-store-get-started-portal.md)

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Włączanie rejestrowania diagnostycznego dla konta Data Lake Storage Gen1
1. Zaloguj się do nowego [portalu Azure](https://portal.azure.com).
2. Otwórz konto Data Lake Storage Gen1, a w bloku konta Data Lake Storage Gen1 kliknij pozycję **Ustawienia diagnostyczne**.
3. W bloku **Ustawienia diagnostyki** kliknij pozycję **Włącz diagnostykę**.

    ![Włączanie rejestrowania diagnostycznego](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Włączanie dzienników diagnostycznych")

3. W **bloku Ustawienia diagnostyki** należy wprowadzić następujące zmiany, aby skonfigurować rejestrowanie diagnostyczne.
   
    ![Włączanie rejestrowania diagnostycznego](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Włączanie dzienników diagnostycznych")
   
   * W polu **Nazwa**wprowadź wartość konfiguracji dziennika diagnostycznego.
   * Można zapisać/przetworzyć dane na różne sposoby.
     
        * Wybierz opcję **Archiwum do konta magazynu** do przechowywania dzienników do konta usługi Azure Storage. Ta opcja jest używana, jeśli chcesz zarchiwizować dane, które będą przetwarzane w partii w późniejszym terminie. Jeśli wybierzesz tę opcję, musisz podać konto usługi Azure Storage, aby zapisać dzienniki.
        
        * Wybierz opcję **Strumień do centrum zdarzeń,** aby przesyłać strumieniowo dane dziennika do usługi Azure Event Hub. Najprawdopodobniej użyjesz tej opcji, jeśli masz potok przetwarzania podrzędnego do analizowania dzienników przychodzących w czasie rzeczywistym. Jeśli wybierzesz tę opcję, musisz podać szczegóły dotyczące usługi Azure Event Hub, którego chcesz użyć.

        * Wybierz opcję **Wyślij do usługi Log Analytics,** aby użyć usługi Azure Monitor do analizowania wygenerowanych danych dziennika. Jeśli wybierzesz tę opcję, należy podać szczegóły dla obszaru roboczego usługi Log Analytics, który będzie używany do wykonywania analizy dziennika. Zobacz [Wyświetlanie lub analizowanie danych zebranych za pomocą dzienników usługi Azure Monitor wyszukuje](../azure-monitor/learn/tutorial-viewdata.md) szczegółowe informacje na temat korzystania z dzienników usługi Azure Monitor.
     
   * Określ, czy chcesz uzyskać dzienniki inspekcji lub dzienniki żądań, czy oba te dzienniki.
   * Określ liczbę dni, dla których dane muszą być przechowywane. Przechowywanie ma zastosowanie tylko wtedy, gdy używasz konta magazynu platformy Azure do archiwizowania danych dziennika.
   * Kliknij przycisk **Zapisz**.

Po włączeniu ustawień diagnostycznych można oglądać dzienniki na karcie **Dzienniki diagnostyczne.**

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Wyświetlanie dzienników diagnostycznych dla konta Data Lake Storage Gen1
Istnieją dwa sposoby wyświetlania danych dziennika dla konta Usługi Data Lake Storage Gen1.

* W widoku ustawienia konta Usługi Data Lake Storage Gen1
* Z konta usługi Azure Storage, na którym przechowywane są dane

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Korzystanie z widoku Ustawienia gen1 magazynu usługi Data Lake
1. W bloku **Ustawienia** konta Data Lake Storage Gen1 kliknij pozycję **Dzienniki diagnostyczne**.
   
    ![Wyświetlanie dzienników diagnostycznych](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Wyświetlanie dzienników diagnostycznych") 
2. W **bloku Dzienniki diagnostyki** powinny zostać wyświetlone dzienniki podzielone według **dzienników inspekcji** i **dzienników żądań.**
   
   * Dzienniki żądań przechwytują każde żądanie interfejsu API złożone na koncie Data Lake Storage Gen1.
   * Dzienniki inspekcji są podobne do dzienników żądań, ale zawierają znacznie bardziej szczegółowy podział operacji wykonywanych na koncie Usługi Data Lake Storage Gen1. Na przykład pojedyncze wywołanie interfejsu API przekazywania w dziennikach żądań może spowodować wiele operacji "Dołącz" w dziennikach inspekcji.
3. Aby pobrać dzienniki, kliknij link **Pobierz** dla każdego wpisu dziennika.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Z konta usługi Azure Storage zawierającego dane dziennika
1. Otwórz blok konta usługi Azure Storage skojarzony z usługą Data Lake Storage Gen1 do rejestrowania, a następnie kliknij pozycję Obiekty blob. Blok **usługi obiektów blob** zawiera listę dwóch kontenerów.
   
    ![Wyświetlanie rejestrowania diagnostycznego](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Wyświetlanie dzienników diagnostycznych")
   
   * Kontener **insights-logs-audit** zawiera dzienniki inspekcji.
   * Kontener **insights-logs-requests** zawiera dzienniki żądań.
2. W tych kontenerach dzienniki są przechowywane w następującej strukturze.
   
    ![Wyświetlanie rejestrowania diagnostycznego](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Wyświetlanie dzienników diagnostycznych")
   
    Na przykład pełną ścieżkę do dziennika inspekcji można`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Podobnie pełną ścieżkę do dziennika żądań można`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Zrozumienie struktury danych dziennika
Dzienniki inspekcji i żądań są w formacie JSON. W tej sekcji przyjrzymy się strukturze JSON dla dzienników żądań i inspekcji.

### <a name="request-logs"></a>Dzienniki żądań
Oto przykładowy wpis w dzienniku żądań w formacie JSON. Każdy obiekt blob ma jeden obiekt główny o nazwie **rekordy,** który zawiera tablicę obiektów dziennika.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
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

#### <a name="request-log-schema"></a>Schemat dziennika żądań
| Nazwa | Typ | Opis |
| --- | --- | --- |
| time |Ciąg |Sygnatura czasowa (w czasie UTC) dziennika |
| resourceId |Ciąg |Identyfikator zasobu, który został wprowadzony na |
| category |Ciąg |Kategoria dziennika. Na przykład **żądania**. |
| operationName |Ciąg |Nazwa operacji, która jest rejestrowana. Na przykład getfilestatus. |
| resultType |Ciąg |Stan operacji, Na przykład 200. |
| callerIpAddress |Ciąg |Adres IP klienta składającego wniosek |
| correlationId |Ciąg |Identyfikator dziennika, który służy do grupowania zestawu powiązanych wpisów dziennika |
| identity |Obiekt |Tożsamość, która wygenerowała dziennik |
| properties |JSON |Szczegóły poniżej |

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
Oto przykładowy wpis w dzienniku inspekcji w formacie JSON. Każdy obiekt blob ma jeden obiekt główny o nazwie **rekordy,** który zawiera tablicę obiektów dziennika

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "resultSignature": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Schemat dziennika inspekcji
| Nazwa | Typ | Opis |
| --- | --- | --- |
| time |Ciąg |Sygnatura czasowa (w czasie UTC) dziennika |
| resourceId |Ciąg |Identyfikator zasobu, który został wprowadzony na |
| category |Ciąg |Kategoria dziennika. Na przykład **Inspekcja**. |
| operationName |Ciąg |Nazwa operacji, która jest rejestrowana. Na przykład getfilestatus. |
| resultType |Ciąg |Stan operacji, Na przykład 200. |
| resultSignature |Ciąg |Dodatkowe szczegóły dotyczące operacji. |
| correlationId |Ciąg |Identyfikator dziennika, który służy do grupowania zestawu powiązanych wpisów dziennika |
| identity |Obiekt |Tożsamość, która wygenerowała dziennik |
| properties |JSON |Szczegóły poniżej |

#### <a name="audit-log-properties-schema"></a>Schemat właściwości dziennika inspekcji
| Nazwa | Typ | Opis |
| --- | --- | --- |
| Nazwa potoku |Ciąg |Ścieżka, na którą została wykonana operacja |

## <a name="samples-to-process-the-log-data"></a>Przykłady do przetwarzania danych dziennika
Podczas wysyłania dzienników z usługi Azure Data Lake Storage Gen1 do dzienników usługi Azure Monitor (zobacz [Wyświetlanie lub analizowanie danych zebranych za pomocą dzienników usługi Azure Monitor wyszukuje](../azure-monitor/learn/tutorial-viewdata.md) szczegóły dotyczące korzystania z dzienników usługi Azure Monitor), następująca kwerenda zwróci tabelę zawierającą listę nazw wyświetlanych użytkowników, czas zdarzeń i liczbę zdarzeń dla czasu zdarzenia wraz z wykresem wizualnym. Można go łatwo zmodyfikować, aby pokazać identyfikator GUID użytkownika lub inne atrybuty:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Usługa Azure Data Lake Storage Gen1 zawiera przykład sposobu przetwarzania i analizowania danych dziennika. Przykład można znaleźć [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)w pliku . 

## <a name="see-also"></a>Zobacz też
* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)

