---
title: Wykonywanie zapytań dotyczących danych w usłudze Azure Data Lake za pomocą Eksploratora danych platformy Azure
description: Dowiedz się, jak wykonywać zapytania o dane w usłudze Azure Data Lake za pomocą Eksploratora danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: d6a58d144482e17f7e4b615134115d1da46af6f0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453179"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Wykonywanie zapytań dotyczących danych w usłudze Azure Data Lake za pomocą Eksploratora danych platformy Azure (wersja zapoznawcza)

Usługi Azure Data Lake Storage to rozwiązanie lake wysoce skalowalna i ekonomiczna danych do analizy danych big data. Połączono w niej wysoce wydajny system plików z ogromną skalą i przystępnością cenową, aby przyspieszyć uzyskiwanie szczegółowych informacji. Usługa Data Lake Storage 2. generacji rozszerza możliwości usługi Azure Blob Storage i jest zoptymalizowana pod kątem obciążeń analitycznych.
 
Eksplorator danych usługi Azure integruje się z usługą Azure Blob Storage i Azure Data Lake magazynu Gen2, zapewniając szybką, pamięci podręcznej i indeksowane dostępu do danych z usługi Data lake. Można analizować i wykonywanie zapytań dotyczących danych w usługi Data lake bez uprzedniego przyjęciu Eksploratora danych usługi Azure. Możesz także zbadać pozyskiwane i uningested natywnych jeziora danych jednocześnie.  

> [!TIP]
> Najlepszą wydajność zapytań powoduje konieczność wprowadzania danych do Eksploratora danych usługi Azure. Możliwość zapytania o dane w usłudze Azure Data Lake magazynu Gen2 bez uprzedniego pozyskiwania powinna służyć wyłącznie dane historyczne lub dane, które rzadko jest wykonywane zapytanie.
 
## <a name="optimize-query-performance-in-the-lake"></a>Optymalizowanie wydajności zapytań w usługi Data lake 

* Partycjonowanie danych większą wydajność i zoptymalizowane przeszukiwania.
* Kompresuj dane w celu zwiększenia wydajności (gzip, aby uzyskać najlepszą kompresję, lz4 w celu uzyskania najlepszej wydajności).
* Za pomocą usługi Azure Blob Storage lub Azure Data Lake Storage Gen2 tym samym regionie co klaster Eksploratora danych usługi Azure. 

## <a name="create-an-external-table"></a>Tworzenie zewnętrznej tabeli

1. Użyj `.create external table` polecenie, aby utworzyć tabelę zewnętrzną w Eksploratorze danych platformy Azure. Tabela zewnętrzna dodatkowe polecenia, takie jak `.show`, `.drop`, i `.alter` są udokumentowane w artykule [polecenia tabeli zewnętrznej](/azure/kusto/management/externaltables).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```

    To zapytanie tworzy partycji codziennych *container1/yyyy/MM/dd/all_exported_blobs.csv*. Zwiększenie wydajności jest oczekiwana z bardziej szczegółowym partycjonowania. Na przykład zapytania za pośrednictwem tabel zewnętrznych z partycji codziennych, takiego jak pokazano powyżej, mają lepszą wydajność niż te zapytania przy użyciu miesięcznych podzielonych tabel.

    > [!NOTE]
    > Konta magazynu obecnie obsługiwane są usługi Azure Blob Storage lub Azure Data Lake Storage Gen2. Formaty danych obecnie obsługiwane są formatu csv, tsv i txt.

1. Tabela zewnętrzna jest widoczny w okienku po lewej stronie interfejsu użytkownika sieci Web

    ![Tabela zewnętrzna w internetowym interfejsie użytkownika](media/data-lake-query-data/external-tables-web-ui.png)
 
### <a name="external-table-permissions"></a>Uprawnienia tabeli zewnętrznej
 
* Użytkownik bazy danych można utworzyć tabeli zewnętrznej. Twórca tabeli automatycznie staje się administratorem tabeli.
* Klaster, bazy danych lub tabeli administratora można edytować istniejącej tabeli.
* Dowolny użytkownik bazy danych lub Czytelnik mogą wysyłać zapytania tabeli zewnętrznej.
 
## <a name="query-an-external-table"></a>Zapytanie tabeli zewnętrznej
 
Aby wysłać zapytanie tabeli zewnętrznej, użyj `external_table()` funkcji, a następnie podaj nazwę tabeli jako argument funkcji. Pozostała część zapytania jest standardowy język zapytania Kusto.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Funkcja IntelliSense nie jest obecnie obsługiwane dla zapytań tabeli zewnętrznej.

## <a name="query-external-and-ingested-data-together"></a>Wykonywanie zapytań dotyczących danych zewnętrznych i pozyskiwane razem

Można tworzyć zapytania tabel zewnętrznych i tabele pozyskiwanych danych w ramach tego samego zapytania. Możesz [ `join` ](/azure/kusto/query/joinoperator) lub [ `union` ](/azure/kusto/query/unionoperator) tabeli zewnętrznej wraz z dodatkowymi danymi z Eksploratora danych usługi Azure, serwerów SQL lub innych źródeł. Użyj [ `let( ) statement` ](/azure/kusto/query/letstatement) Aby przypisać nazwę skrót do odwołań tabeli zewnętrznej.

W poniższym przykładzie *produktów* to tabela pozyskiwanych danych i *ArchivedProducts* jest tabeli zewnętrznej, która zawiera dane w Gen2 magazynu jezioro danych Azure:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Zapytanie *TaxiRides* tabeli zewnętrznej w klastrze help

*TaxiRides* Przykładowy zestaw danych zawiera danych taksówek w Nowym Jorku z [taksówek NYC i Komisji Limousine](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Tworzenie zewnętrznej tabeli *TaxiRides* 

> [!NOTE]
> W tej sekcji przedstawiono zapytanie użyte do utworzenia *TaxiRides* tabeli zewnętrznej w *pomocy* klastra. Ponieważ ta tabela została już utworzona możesz pominąć tę sekcję i wykonywać [zapytania *TaxiRides* danych tabeli zewnętrznej](#query-taxirides-external-table-data). 

1. Następujące zapytanie został użyty do utworzenia tabeli zewnętrznej *TaxiRides* w klastrze help. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
    h@'https://externalkustosamples.blob.core.windows.net/taxiridesbyday?st=2019-06-18T14%3A59%3A00Z&se=2029-06-19T14%3A59%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=yEaO%2BrzFHzAq7lvd4d9PeQ%2BTi3AWnho8Rn8hGU0X30M%3D'
    )
    ```
1. Tabela wynikowa został utworzony w *pomocy* klastra:

    ![Tabela zewnętrzna TaxiRides](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Zapytanie *TaxiRides* danych tabeli zewnętrznej 

Zaloguj się do [ https://dataexplorer.azure.com/clusters/help/databases/Samples ](https://dataexplorer.azure.com/clusters/help/databases/Samples) zapytania *TaxiRides* tabeli zewnętrznej. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Zapytanie *TaxiRides* tabeli zewnętrznej bez podziału na partycje

[Uruchom to zapytanie](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) w tabeli zewnętrznej *TaxiRides* opisujący było dla poszczególnych dni tygodnia, w obrębie całego zestawu danych. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

To zapytanie Wyświetla najczęściej odwiedzane dzień tygodnia. Ponieważ dane nie jest podzielona na partycje, to zapytanie może potrwać dłuższy czas zwracania wyników (maksymalnie kilka minut).

![Renderowanie niepartycjonowana zapytania](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Tabela zewnętrzna TaxiRides zapytania z podziałem na partycje 

[Uruchom to zapytanie](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) w tabeli zewnętrznej *TaxiRides* przedstawiający typy plików cab taksówek (żółto lub zielono) używane w styczniu 2017 roku. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

To zapytanie używa partycjonowania, który optymalizuje czas wykonywania zapytania i wydajności. Zapytanie filtrów partycjonowanego kolumny (pickup_datetime) i zwraca wyniki w ciągu kilku sekund.

![Renderowanie podzielonym na partycje zapytanie](media/data-lake-query-data/taxirides-with-partition.png)
  
Można napisać dodatkowych zapytań do uruchamiania w tabeli zewnętrznej *TaxiRides* i Dowiedz się więcej o danych. 

## <a name="next-steps"></a>Kolejne kroki

Wykonuj zapytania na danych w usłudze Azure Data Lake za pomocą Eksploratora danych platformy Azure. Dowiedz się, jak [pisać zapytania](write-queries.md) i pobieraj dodatkowe szczegółowe informacje z danych.