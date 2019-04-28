---
title: Wyświetlanie dzienników diagnostycznych usługi Azure Data Lake Storage Gen1 | Dokumentacja firmy Microsoft
description: 'Dowiedz się, jak skonfigurować, a dostęp do dzienników diagnostycznych do usługi Azure Data Lake Storage Gen1 '
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878758"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Uzyskiwanie dostępu do dzienników diagnostycznych usługi Azure Data Lake Storage Gen1
Dowiedz się włączyć diagnostyczne dla Twojego konta usługi Azure Data Lake Storage Gen1 i jak wyświetlić dzienniki zebrane dla swojego konta.

Organizacje mogą włączyć rejestrowania diagnostycznego dla swojego konta usługi Azure Data Lake Storage Gen1 zbierać ślady inspekcji dostępu do danych, który zawiera informacje, takie jak listy użytkowników uzyskujących dostęp do danych, jak często uzyskać dostępu do danych, jak dużo danych znajduje się w konta itp. Po włączeniu diagnostyki i/lub żądania są rejestrowane na zasadzie największej staranności. Maksymalny rozmiar żądań i Diagnostyka wpisy dziennika są tworzone tylko wtedy, gdy żądania skierowanego do punktu końcowego usługi.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami w artykule [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 przy użyciu witryny Azure Portal](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Włączanie rejestrowania diagnostycznego dla konta usługi Data Lake Storage Gen1
1. Zaloguj się w nowej witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz konto usługi Data Lake Storage Gen1, a następnie w bloku konta usługi Data Lake Storage Gen1 kliknij **ustawień diagnostycznych**.
3. W **ustawień diagnostycznych** bloku kliknij **Włącz diagnostykę**.

    ![Włączanie rejestrowania diagnostycznego](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Włączanie dzienników diagnostycznych")

3. W **ustawień diagnostycznych** bloku, należy wprowadzić następujące zmiany, aby skonfigurować rejestrowanie diagnostyczne.
   
    ![Włączanie rejestrowania diagnostycznego](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Włączanie dzienników diagnostycznych")
   
   * Aby uzyskać **nazwa**, wprowadź wartość dla konfiguracji dzienniki diagnostyczne.
   * Istnieje możliwość magazyn i przetwarzanie tych danych na różne sposoby.
     
        * Wybierz opcję, aby **Zarchiwizuj na koncie magazynu** do przechowywania dzienników do konta usługi Azure Storage. Użyj tej opcji, jeśli chcesz zarchiwizować dane, które będą przetwarzane wsadowe w późniejszym terminie. Jeśli wybierzesz tę opcję, musisz podać konto usługi Azure Storage, aby zapisać dzienniki.
        
        * Wybierz opcję, aby **Stream do usługi event hub** przesyłanie strumieniowe dzienników danych do usługi Azure Event Hub. Prawdopodobnie użyjesz tej opcji w przypadku potoku przetwarzania transmisji dla klientów w celu analizowania przychodzącej dzienników w czasie rzeczywistym. Jeśli wybierzesz tę opcję, musisz podać szczegółowe informacje dla usługi Azure Event Hub, którego chcesz użyć.

        * Wybierz opcję, aby **wysyłanie do usługi Log Analytics** analizować dane dzienników wygenerowane za pomocą usługi Azure Monitor. Jeśli wybierzesz tę opcję, musisz podać szczegółowe informacje dla obszaru roboczego usługi Log Analytics zostanie wykorzystany wykonaj analizę dziennika. Zobacz [wyświetlanie i analizowanie zebranych za pomocą usługi Azure Monitor dzienniki wyszukiwania danych](../azure-monitor/learn/tutorial-viewdata.md) Aby uzyskać szczegółowe informacje na temat korzystania z usługi Azure Monitor dzienniki.
     
   * Określ, czy chcesz pobrać dzienniki inspekcji, dzienniki żądania lub obu.
   * Określ liczbę dni, dla których dane muszą zostać zachowane. Przechowywanie ma zastosowanie tylko jeśli używasz konta magazynu platformy Azure pod kątem archiwizowania danych dziennika.
   * Kliknij pozycję **Zapisz**.

Po włączeniu ustawienia diagnostyczne, możesz obejrzeć dzienniki w **dzienniki diagnostyczne** kartę.

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Wyświetlanie dzienników diagnostycznych dla konta usługi Data Lake Storage Gen1
Istnieją dwa sposoby, aby wyświetlić dane dziennika dla konta usługi Data Lake Storage Gen1.

* Na Data Lake Storage Gen1 wyświetlić ustawienia konta
* Z kontem usługi Azure Storage, w którym przechowywane są dane

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Korzystając z widoku Data Lake Gen1 miejsca do magazynowania
1. Z konta usługi Data Lake Storage Gen1 **ustawienia** bloku kliknij **dzienniki diagnostyczne**.
   
    ![Wyświetlanie dzienników diagnostycznych](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Przeglądaj dzienniki diagnostyki") 
2. W **dzienniki diagnostyczne** bloku, powinien zostać wyświetlony dzienników, pogrupowane według **dzienników inspekcji** i **Dzienniki żądań**.
   
   * Dzienniki na żądanie przechwytywać każde żądanie interfejsu API dla konta Data Lake Storage Gen1.
   * Dzienniki inspekcji są podobne do żądania dzienników, ale zapewnia bardziej szczegółowy podział operacje wykonywane na konta Data Lake Storage Gen1. Na przykład wywołanie interfejsu API przekazywanych w dziennikach żądanie może spowodować wiele operacji "Dołącz" w dziennikach inspekcji.
3. Aby pobrać dzienniki, kliknij **Pobierz** łącza względem każdego wpisu dziennika.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Z kontem usługi Azure Storage, która zawiera dane dziennika
1. Otwórz blok konta usługi Azure Storage skojarzonych z Data Lake Storage Gen1 do rejestrowania, a następnie kliknij przycisk obiektów blob. **Usługi Blob service** blok zawiera listę dwóch kontenerów.
   
    ![Rejestrowanie diagnostyczne widoku](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Przeglądaj dzienniki diagnostyki")
   
   * Kontener **insights — dzienniki inspekcji** zawiera dzienniki inspekcji.
   * Kontener **insights Dzienniki żądań** zawiera dzienniki żądania.
2. W tych kontenerach dzienniki są przechowywane w obszarze następującej strukturze.
   
    ![Rejestrowanie diagnostyczne widoku](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Przeglądaj dzienniki diagnostyki")
   
    Na przykład może być pełną ścieżką do dziennika inspekcji `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Podobnie może być pełną ścieżką do Dziennik żądań `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Zrozumienie struktury danych dziennika
Dzienniki inspekcji i żądania są w formacie JSON. W tej sekcji możemy przyjrzeć się strukturze JSON dla żądania i dzienników inspekcji.

### <a name="request-logs"></a>Dziennik żądań
Poniżej przedstawiono przykładowy wpis w dzienniku żądania w formacie JSON. Każdy obiekt blob ma jeden główny obiekt o nazwie **rekordów** zawierający tablicę obiektów dziennika.

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

#### <a name="request-log-schema"></a>Schemat dziennika żądania
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| time |String |Znacznik czasu (w formacie UTC) dziennika |
| resourceId |String |Identyfikator zasobu, która trwała operacja umieść na |
| category |String |Kategoria dziennika. Na przykład **żądań**. |
| operationName |String |Nazwa operacji, który jest zalogowany. Na przykład getfilestatus. |
| resultType |String |Stan operacji, na przykład 200. |
| callerIpAddress |String |Adres IP klienta wysyłającego żądanie |
| correlationId |String |Identyfikator dziennika, która może być używane do grupowania zbiór wpisów dziennika powiązane |
| identity |Object |Tożsamość, która wygenerowała dziennika |
| properties |JSON |Poniżej znajdują się szczegółowe informacje |

#### <a name="request-log-properties-schema"></a>Schemat właściwości dziennika żądania
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| HttpMethod |String |Metoda HTTP używana dla tej operacji. Na przykład UZYSKAĆ. |
| Ścieżka |String |Ścieżka operacja została wykonana w |
| RequestContentLength |int |Długość zawartości żądania HTTP |
| ClientRequestId |String |Identyfikator, który unikatowo identyfikuje tego żądania |
| StartTime |String |Czas, w którym serwer odebrał żądanie |
| EndTime |String |Czas wysłanego przez serwer odpowiedzi |

### <a name="audit-logs"></a>Dzienniki inspekcji
Poniżej przedstawiono przykładowy wpis w dzienniku inspekcji w formacie JSON. Każdy obiekt blob ma jeden główny obiekt o nazwie **rekordów** zawierający tablicę obiektów dziennika

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
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| time |String |Znacznik czasu (w formacie UTC) dziennika |
| resourceId |String |Identyfikator zasobu, która trwała operacja umieść na |
| category |String |Kategoria dziennika. Na przykład **inspekcji**. |
| operationName |String |Nazwa operacji, który jest zalogowany. Na przykład getfilestatus. |
| resultType |String |Stan operacji, na przykład 200. |
| resultSignature |String |Więcej informacji na temat operacji. |
| correlationId |String |Identyfikator dziennika, która może być używane do grupowania zbiór wpisów dziennika powiązane |
| identity |Object |Tożsamość, która wygenerowała dziennika |
| properties |JSON |Poniżej znajdują się szczegółowe informacje |

#### <a name="audit-log-properties-schema"></a>Schemat właściwości dziennika inspekcji
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| StreamName |String |Ścieżka operacja została wykonana w |

## <a name="samples-to-process-the-log-data"></a>Przykłady, aby przetwarzać dane dziennika
Podczas wysyłania dzienników z usługi Azure Data Lake Storage Gen1 dzienniki usługi Azure Monitor (zobacz [wyświetlanie i analizowanie zebranych za pomocą usługi Azure Monitor dzienniki wyszukiwania danych](../azure-monitor/learn/tutorial-viewdata.md) Aby uzyskać szczegółowe informacje na temat korzystania z usługi Azure Monitor dzienników), następujące zapytanie zwraca tabelę zawierającą listę użytkowników nazw wyświetlanych, czas zdarzenia, a liczba zdarzeń czasu dla zdarzenia wraz z visual wykresu. Łatwo możesz to modyfikować, aby wyświetlić identyfikator GUID użytkownika lub inne atrybuty:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 zawiera przykładowy na temat sposobu przetwarzania i analizowania danych dziennika. Można znaleźć przykład w [ https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample ](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)

