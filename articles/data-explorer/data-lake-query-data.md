---
title: Wykonywanie zapytań dotyczących danych w Azure Data Lake przy użyciu usługi Azure Eksplorator danych
description: Dowiedz się, jak wykonywać zapytania dotyczące danych w Azure Data Lake przy użyciu usługi Azure Eksplorator danych.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 8240b1a01aa39e53b9ae41f73543ccf9774290b2
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161753"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Wykonywanie zapytań dotyczących danych w Azure Data Lake przy użyciu usługi Azure Eksplorator danych

Azure Data Lake Storage to wysoce skalowalne i ekonomiczne rozwiązanie usługi Data Lake do analizy danych Big Data. Łączy on moc systemu plików o wysokiej wydajności z ogromną skalą i oszczędnością, aby pomóc w skróceniu czasu do wglądu w szczegółowe dane. Data Lake Storage Gen2 rozszerza możliwości platformy Azure Blob Storage i jest zoptymalizowany pod kątem obciążeń analitycznych.
 
Platforma Azure Eksplorator danych integruje się z platformą Azure Blob Storage i Azure Data Lake Storage (Gen1 i Gen2), zapewniając szybki, buforowany i indeksowany dostęp do danych w usłudze Lake. Można analizować i wysyłać zapytania dotyczące danych w usłudze Lake bez wcześniejszego pozyskiwania do Eksplorator danych platformy Azure. Możesz również wykonywać zapytania na wyzyskanych i nieprzezyskanych natywnych danych Lake.  

> [!TIP]
> Najlepsza wydajność zapytań wymaga pozyskiwania danych na platformie Azure Eksplorator danych. Możliwość wykonywania zapytań o dane zewnętrzne bez wcześniejszego pozyskiwania powinna być używana tylko w przypadku danych historycznych lub danych, które są rzadko wysyłane zapytania. [Zoptymalizuj wydajność zapytań na Lake,](#optimize-your-query-performance) Aby uzyskać najlepsze wyniki.
 

## <a name="create-an-external-table"></a>Tworzenie tabeli zewnętrznej

 > [!NOTE]
 > Obecnie obsługiwane konta magazynu to Azure Blob Storage lub Azure Data Lake Storage (Gen1 i Gen2).

1. Użyj `.create external table` polecenie, aby utworzyć tabelę zewnętrzną w usłudze Azure Eksplorator danych. Dodatkowe polecenia tabeli zewnętrznej, takie jak `.show`, `.drop`i `.alter` są udokumentowane w [poleceniach tabeli zewnętrznej](/azure/kusto/management/externaltables).

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
    > * Jeśli tabela zewnętrzna jest podzielona na partycje przez kolumnę datetime, zawsze Uwzględnij filtr czasu dla zamkniętego zakresu w zapytaniu (na przykład zapytanie-`ArchivedProducts | where Timestamp between (ago(1h) .. 10m)`-powinno działać lepiej niż ten (otwarty zakres) jeden `ArchivedProducts | where Timestamp > ago(1h)`). 
    > * Wszystkie [obsługiwane formaty](ingest-data-overview.md#supported-data-formats) pozyskiwania można zbadać przy użyciu tabel zewnętrznych.

1. Tabela zewnętrzna jest widoczna w lewym okienku interfejsu użytkownika sieci Web

    ![tabela zewnętrzna w interfejsie użytkownika sieci Web](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Tworzenie tabeli zewnętrznej z formatem JSON

Tabelę zewnętrzną można utworzyć przy użyciu formatu JSON. Aby uzyskać więcej informacji, zobacz [zewnętrzne polecenia tabel](/azure/kusto/management/externaltables)

1. Użyj `.create external table` polecenie, aby utworzyć tabelę o nazwie *ExternalTableJson*:

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
 
Aby zbadać tabelę zewnętrzną, użyj funkcji `external_table()` i podaj nazwę tabeli jako argument funkcji. Pozostała część zapytania to standardowy język zapytań Kusto.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Funkcja IntelliSense nie jest obecnie obsługiwana w zapytaniach tabeli zewnętrznej.

### <a name="query-an-external-table-with-json-format"></a>Zapytanie tabeli zewnętrznej z formatem JSON

Aby zbadać tabelę zewnętrzną z formatem JSON, użyj funkcji `external_table()` i podaj nazwę tabeli i nazwę mapowania jako argumenty funkcji. W zapytaniu poniżej, jeśli nie określono *mapowanianame* , zostanie użyte mapowanie, które zostało wcześniej utworzone.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Wykonywanie zapytań dotyczących danych zewnętrznych i pozyskanych

W ramach tego samego zapytania można wykonywać zapytania dotyczące tabel zewnętrznych i tabel pozyskanych danych. Możesz [`join`](/azure/kusto/query/joinoperator) lub [`union`](/azure/kusto/query/unionoperator) tabelę zewnętrzną z dodatkowymi danymi z usług Azure Eksplorator danych, SQL Server lub innych źródeł. Użyj [`let( ) statement`](/azure/kusto/query/letstatement) , aby przypisać nazwę skróconą do odwołania do tabeli zewnętrznej.

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

Zaloguj się do [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) , aby wykonać zapytanie dotyczące zewnętrznej tabeli *TaxiRides* . 

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

## <a name="optimize-your-query-performance"></a>Optymalizowanie wydajności zapytań

Zoptymalizuj wydajność zapytań w Lake, wykonując następujące najlepsze rozwiązania dotyczące wykonywania zapytań dotyczących danych zewnętrznych. 
 
### <a name="data-format"></a>Format danych
 
Użyj formatu kolumn dla zapytań analitycznych od:
* Można odczytać tylko kolumny istotne dla zapytania. 
* Techniki kodowania kolumn znacznie zmniejszają rozmiar danych.  
Usługa Azure Eksplorator danych obsługuje formaty kolumn Parquet i ORC. Format Parquet jest sugerowany ze względu na zoptymalizowaną implementację. 
 
### <a name="azure-region"></a>Region platformy Azure 
 
Upewnienie się, że dane zewnętrzne znajdują się w tym samym regionie świadczenia usługi Azure co klaster Eksplorator danych platformy Azure. Zmniejsza to koszty i czas pobierania danych.
 
### <a name="file-size"></a>Rozmiar pliku
 
Optymalny rozmiar pliku to setki MB (do 1 GB) dla każdego pliku. Unikaj wielu małych plików, które wymagają niepotrzebnych obciążeń, takich jak wolniejszy proces wyliczania plików i ograniczone użycie formatu kolumn. Należy pamiętać, że liczba plików powinna być większa niż liczba rdzeni procesora CPU w klastrze usługi Azure Eksplorator danych. 
 
### <a name="compression"></a>Kompresja
 
Użyj kompresji, aby zmniejszyć ilość danych pobieranych z magazynu zdalnego. W przypadku formatu Parquet należy użyć wewnętrznego mechanizmu kompresji Parquet, który kompresuje grupy kolumn oddzielnie, dzięki czemu można je oddzielnie odczytać. Aby zweryfikować użycie mechanizmu kompresji, sprawdź, czy pliki są nazwane w następujący sposób: "<filename>. gz. Parquet" lub "<filename>. przyciąganie. Parquet" zamiast "<filename>. Parquet. gz"). 
 
### <a name="partitioning"></a>Partycjonowanie
 
Organizuj dane przy użyciu partycji "folder", które umożliwiają zapytanie pomijanie nieistotnych ścieżek. Podczas planowania partycjonowanie należy wziąć pod uwagę rozmiar pliku i typowe filtry w zapytaniach, takie jak sygnatura czasowa lub identyfikator dzierżawy.
 
### <a name="vm-size"></a>Rozmiar maszyny wirtualnej
 
Wybierz jednostki SKU maszyny wirtualnej z większą liczbą rdzeni i wyższą przepustowość sieci (pamięć jest mniej ważna). Aby uzyskać więcej informacji [, zobacz temat Wybieranie odpowiedniej jednostki SKU maszyny wirtualnej dla klastra Eksplorator danych platformy Azure](manage-cluster-choose-sku.md).

## <a name="next-steps"></a>Następne kroki

Wykonaj zapytanie dotyczące danych w Azure Data Lake przy użyciu usługi Azure Eksplorator danych. Dowiedz się, jak [pisać zapytania](write-queries.md) i uzyskać dodatkowe szczegółowe informacje na podstawie danych.
