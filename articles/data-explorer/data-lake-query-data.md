---
title: Wykonywanie zapytań dotyczących danych w Azure Data Lake przy użyciu usługi Azure Eksplorator danych
description: Dowiedz się, jak wykonywać zapytania dotyczące danych w Azure Data Lake przy użyciu usługi Azure Eksplorator danych.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: ef4dfc4370c71eac1978a6f3535b571a5e6009b5
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950134"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Wykonywanie zapytań dotyczących danych w Azure Data Lake przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)

Azure Data Lake Storage to wysoce skalowalne i ekonomiczne rozwiązanie usługi Data Lake do analizy danych Big Data. Połączono w niej wysoce wydajny system plików z ogromną skalą i przystępnością cenową, aby przyspieszyć uzyskiwanie szczegółowych informacji. Usługa Data Lake Storage 2. generacji rozszerza możliwości usługi Azure Blob Storage i jest zoptymalizowana pod kątem obciążeń analitycznych.
 
Platforma Azure Eksplorator danych integruje się z usługą Azure Blob Storage i Azure Data Lake Storage Gen2, zapewniając szybki, buforowany i indeksowany dostęp do danych w usłudze Lake. Można analizować i wysyłać zapytania dotyczące danych w usłudze Lake bez wcześniejszego pozyskiwania do Eksplorator danych platformy Azure. Możesz również wykonywać zapytania na wyzyskanych i nieprzezyskanych natywnych danych Lake.  

> [!TIP]
> Najlepsza wydajność zapytań wymaga pozyskiwania danych na platformie Azure Eksplorator danych. Możliwość wykonywania zapytań dotyczących danych w Azure Data Lake Storage Gen2 bez wcześniejszego pozyskiwania powinna być używana tylko w przypadku danych historycznych lub danych, które są rzadko wysyłane zapytania.
 
## <a name="optimize-query-performance-in-the-lake"></a>Optymalizowanie wydajności zapytań w Lake 

* Podziel dane na partycje, aby zwiększyć wydajność i zoptymalizowany czas zapytania.
* Kompresuj dane, aby zwiększyć wydajność (GZIP dla najlepszej kompresji, lz4 dla najlepszej wydajności).
* Użyj usługi Azure Blob Storage lub Azure Data Lake Storage Gen2 z tym samym regionem co klaster Eksplorator danych platformy Azure. 

## <a name="create-an-external-table"></a>Tworzenie tabeli zewnętrznej

 > [!NOTE]
 > Obecnie obsługiwane konta magazynu to Azure Blob Storage lub Azure Data Lake Storage Gen2. Obecnie obsługiwane są formaty danych JSON, CSV, TSV i txt.

1. Użyj polecenia `.create external table` , aby utworzyć tabelę zewnętrzną w usłudze Azure Eksplorator danych. Dodatkowe polecenia tabeli zewnętrznej, takie `.show`jak `.drop`, i `.alter` są udokumentowane w [poleceniach tabeli zewnętrznej](/azure/kusto/management/externaltables).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * Oczekiwana jest zwiększona wydajność z bardziej szczegółowym partycjonowaniem. Na przykład zapytania dotyczące tabel zewnętrznych z partycjami dziennymi będą miały lepszą wydajność niż te zapytania z tabelami z podziałem na partycje.
    > * Podczas definiowania tabeli zewnętrznej z partycjami, struktura magazynu powinna być taka sama.
Na przykład jeśli tabela jest zdefiniowana z partycją DateTime w formacie RRRR/MM/DD (domyślnie), ścieżka pliku magazynu URI powinna mieć wartość *container1/rrrr/mm/dd/all_exported_blobs*. 

1. Tabela zewnętrzna jest widoczna w lewym okienku interfejsu użytkownika sieci Web

    ![tabela zewnętrzna w interfejsie użytkownika sieci Web](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Tworzenie tabeli zewnętrznej z formatem JSON

Tabelę zewnętrzną można utworzyć przy użyciu formatu JSON. Aby uzyskać więcej informacji, zobacz [zewnętrzne polecenia tabel](/azure/kusto/management/externaltables)

1. Użyj polecenia `.create external table` , aby utworzyć tabelę o nazwie *ExternalTableJson*:

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. Format JSON wymaga drugiego kroku tworzenia mapowania do kolumn, jak pokazano poniżej. W poniższym zapytaniu Utwórz określone mapowanie JSON o nazwie *MappingName*:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Uprawnienia tabeli zewnętrznej
 
* Użytkownik bazy danych może utworzyć tabelę zewnętrzną. Twórcy tabeli automatycznie staną się administratorem tabeli.
* Klaster, baza danych lub administrator tabeli może edytować istniejącą tabelę.
* Każdy użytkownik lub czytelnik bazy danych może wysyłać zapytania do tabeli zewnętrznej.
 
## <a name="query-an-external-table"></a>Kwerenda tabeli zewnętrznej
 
Aby zbadać tabelę zewnętrzną, użyj `external_table()` funkcji i podaj nazwę tabeli jako argument funkcji. Pozostała część zapytania to standardowy język zapytań Kusto.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Funkcja IntelliSense nie jest obecnie obsługiwana w zapytaniach tabeli zewnętrznej.

### <a name="query-an-external-table-with-json-format"></a>Zapytanie tabeli zewnętrznej z formatem JSON

Aby zbadać tabelę zewnętrzną z formatem JSON, należy użyć `external_table()` funkcji i podać nazwę tabeli i nazwę mapowania jako argumenty funkcji. W zapytaniu poniżej, jeśli nie określono mapowanianame, zostanie użyte mapowanie, które zostało wcześniej utworzone.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Wykonywanie zapytań dotyczących danych zewnętrznych i pozyskanych

W ramach tego samego zapytania można wykonywać zapytania dotyczące tabel zewnętrznych i tabel pozyskanych danych. Ty [`join`](/azure/kusto/query/joinoperator) [lub`union`](/azure/kusto/query/unionoperator) tabela zewnętrzna z dodatkowymi danymi z usług Azure Eksplorator danych, SQL Server lub innych źródeł. Użyj, [`let( ) statement`](/azure/kusto/query/letstatement) aby przypisać nazwę skróconą do odwołania do tabeli zewnętrznej.

W poniższym przykładzie *produkty* są tabelą danych pozyskiwanych, a *ArchivedProducts* jest tabelą zewnętrzną, która zawiera dane w Azure Data Lake Storage Gen2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Kwerenda *TaxiRides* tabeli zewnętrznej w klastrze pomocy

Zestaw danych przykładowych *TaxiRides* zawiera dane o pozostałej stolicy w Nowym Jorku od [NYC taksówki i Limousinee](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Utwórz zewnętrzną tabelę *TaxiRides* 

> [!NOTE]
> W tej sekcji przedstawiono zapytanie używane do tworzenia tabeli zewnętrznej *TaxiRides* w klastrze *pomocy* . Ponieważ ta tabela została już utworzona, można pominąć tę sekcję i wykonać [zapytanie *TaxiRides* zewnętrzne dane tabeli](#query-taxirides-external-table-data). 

1. Poniższe zapytanie zostało użyte do utworzenia tabeli zewnętrznej *TaxiRides* w klastrze pomocy. 

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
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. Powstała tabela została utworzona w klastrze *pomocy* :

    ![Tabela zewnętrzna TaxiRides](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Zapytanie *TaxiRides* zewnętrzne dane tabeli 

Zaloguj się, [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) aby wysłać zapytanie do tabeli zewnętrznej *TaxiRides* . 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Zapytanie *TaxiRides* tabelę zewnętrzną bez partycjonowania

[Uruchom to zapytanie](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) w tabeli zewnętrznej *TaxiRides* , aby przedstawić kolarstwu dla każdego dnia tygodnia, w całym zestawie danych. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

To zapytanie pokazuje dzień najgorętszym tygodnia. Ponieważ dane nie są partycjonowane, to zapytanie może potrwać długo, aby można było zwracać wyniki (do kilku minut).

![Renderowanie zapytania bez partycjonowania](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Kwerenda TaxiRides tabela zewnętrzna z partycjonowaniem 

[Uruchom to zapytanie](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) w tabeli zewnętrznej *TaxiRides* przedstawiające typy kabin (żółte lub zielone), które są używane w styczniu 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

To zapytanie używa partycjonowania, które optymalizuje czas zapytania i wydajność. Zapytanie jest filtrowane na partycjonowanej kolumnie (pickup_datetime) i zwraca wyniki w ciągu kilku sekund.

![Renderuj zapytanie partycjonowane](media/data-lake-query-data/taxirides-with-partition.png)
  
Można napisać dodatkowe zapytania do uruchamiania w tabeli zewnętrznej *TaxiRides* i dowiedzieć się więcej na temat danych. 

## <a name="next-steps"></a>Następne kroki

Wykonaj zapytanie dotyczące danych w Azure Data Lake przy użyciu usługi Azure Eksplorator danych. Dowiedz się, jak [pisać zapytania](write-queries.md) i uzyskać dodatkowe szczegółowe informacje na podstawie danych.
