---
title: Zapytanie o dane w usłudze Azure Data Lake przy użyciu Eksploratora danych platformy Azure
description: Dowiedz się, jak badać dane w usłudze Azure Data Lake przy użyciu Eksploratora danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 8240b1a01aa39e53b9ae41f73543ccf9774290b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161753"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Zapytanie o dane w usłudze Azure Data Lake przy użyciu Eksploratora danych platformy Azure

Usługa Azure Data Lake Storage to wysoce skalowalne i ekonomiczne rozwiązanie do analizy dużych zbiorów danych. Łączy w sobie moc wydajnego systemu plików z ogromną skalą i ekonomią, aby przyspieszyć czas na wgląd. Usługa Data Lake Storage Gen2 rozszerza możliwości usługi Azure Blob Storage i jest zoptymalizowana pod kątem obciążeń analitycznych.
 
Usługa Azure Data Explorer integruje się z usługą Azure Blob Storage i Usługi Azure Data Lake Storage (Gen1 i Gen2), zapewniając szybki, buforowany i indeksowany dostęp do danych w sieci. Można analizować i wysyłać zapytania do danych w jeziorze bez wcześniejszego pozyskiwania do Eksploratora danych platformy Azure. Można również kwerendy przez pochłoniętych i nienastuowanych natywnych danych jeziora jednocześnie.  

> [!TIP]
> Najlepsza wydajność kwerendy wymaga pozyskiwania danych do Eksploratora danych platformy Azure. Możliwość wykonywania zapytań o dane zewnętrzne bez wcześniejszego pozyskiwania powinna być używana tylko w przypadku danych historycznych lub danych rzadko używanych. [Zoptymalizuj wydajność zapytań w jeziorze,](#optimize-your-query-performance) aby uzyskać najlepsze wyniki.
 

## <a name="create-an-external-table"></a>Tworzenie tabeli zewnętrznej

 > [!NOTE]
 > Obecnie obsługiwane konta magazynu to Usługa Azure Blob Storage lub Usługa Azure Data Lake Storage (Gen1 i Gen2).

1. Użyj `.create external table` polecenia, aby utworzyć tabelę zewnętrzną w Eksploratorze danych platformy Azure. Dodatkowe polecenia tabeli `.show`zewnętrznej, takie jak , `.drop`i `.alter` są udokumentowane w [poleceniach tabeli zewnętrznej](/azure/kusto/management/externaltables).

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
    > * Oczekuje się zwiększonej wydajności dzięki bardziej szczegółowej partycjonowania. Na przykład zapytania za pomocą tabel zewnętrznych z codziennymi partycjami będą miały lepszą wydajność niż te kwerendy z tabelami podzielonymi na partycje miesięczne.
    > * Podczas definiowania tabeli zewnętrznej z partycjami, struktura magazynu ma być identyczna.
Jeśli na przykład tabela jest zdefiniowana za pomocą partycji DateTime w formacie yyyy/MM/dd (domyślnie), ścieżka pliku magazynu URI powinna być *kontener1/yyyy/MM/dd/all_exported_blobs*. 
    > * Jeśli tabela zewnętrzna jest podzielona na partycje według kolumny datetime, zawsze zawiera filtr `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` czasu dla zamkniętego zakresu w zapytaniu (na przykład kwerenda - - powinna działać lepiej niż ten (otwarty zakres) jeden - `ArchivedProducts | where Timestamp > ago(1h)` ). 
    > * Wszystkie [obsługiwane formaty pozyskiwania](ingest-data-overview.md#supported-data-formats) można wyszukiwać za pomocą tabel zewnętrznych.

1. Tabela zewnętrzna jest widoczna w lewym okienku interfejsu użytkownika sieci Web

    ![tabela zewnętrzna w interfejsie użytkownika sieci Web](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Tworzenie tabeli zewnętrznej w formacie json

Można utworzyć tabelę zewnętrzną w formacie json. Aby uzyskać więcej informacji, zobacz [Polecenia tabeli zewnętrznej](/azure/kusto/management/externaltables)

1. Użyj `.create external table` polecenia, aby utworzyć tabelę o nazwie *ExternalTableJson*:

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
 
1. Format Json wymaga drugiego kroku tworzenia mapowania do kolumn, jak pokazano poniżej. W następującej kwerendzie utwórz określone mapowanie json o nazwie *mappingName:*

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Uprawnienia do tabeli zewnętrznej
 
* Użytkownik bazy danych może utworzyć tabelę zewnętrzną. Twórca tabeli automatycznie staje się administratorem tabeli.
* Administrator klastra, bazy danych lub tabeli może edytować istniejącą tabelę.
* Każdy użytkownik bazy danych lub czytelnik może wysyłać zapytania do tabeli zewnętrznej.
 
## <a name="query-an-external-table"></a>Zapytanie o tabelę zewnętrzną
 
Aby zbadać tabelę zewnętrzną, użyj `external_table()` funkcji i podaj nazwę tabeli jako argument funkcji. Reszta zapytania jest standardowym językiem zapytania Kusto.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Intellisense nie jest obecnie obsługiwany w kwerendach tabel zewnętrznych.

### <a name="query-an-external-table-with-json-format"></a>Zapytanie o tabelę zewnętrzną w formacie json

Aby zbadać tabelę zewnętrzną w `external_table()` formacie json, użyj tej funkcji i podaj zarówno nazwę tabeli, jak i nazwę mapowania jako argumenty funkcji. W poniższej kwerendzie, jeśli *nazwa mapowania* nie jest określona, zostanie użyte mapowanie, które zostało wcześniej utworzone.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Zbieranie zapytań o dane zewnętrzne i przynajmowane razem

W ramach tej samej kwerendy można wysyłać kwerendy zarówno do tabel zewnętrznych, jak i do tabel danych. Użytkownik [`join`](/azure/kusto/query/joinoperator) [`union`](/azure/kusto/query/unionoperator) lub tabela zewnętrzna z dodatkowymi danymi z usługi Azure Data Explorer, serwerów SQL lub innych źródeł. Użyj [`let( ) statement`](/azure/kusto/query/letstatement) a, aby przypisać skróconą nazwę do odwołania do tabeli zewnętrznej.

W poniższym przykładzie *Produkty* to pochłonięta tabela danych, a *ArchivedProducts* to zewnętrzna tabela zawierająca dane w usłudze Azure Data Lake Storage Gen2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Zapytanie *TaxiRides* tabeli zewnętrznej w klastrze pomocy

Zestaw danych *taxirides* próbki zawiera dane taksówki New York City z [NYC Taxi i Limousine Komisji](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Tworzenie tabeli zewnętrznej *TaxiRides* 

> [!NOTE]
> Ta sekcja przedstawia kwerendę używaną do tworzenia tabeli zewnętrznej *TaxiRides* w klastrze *pomocy.* Ponieważ ta tabela została już utworzona, można pominąć tę sekcję i wykonać [kwerendę *TaxiRides* dane tabeli zewnętrznej](#query-taxirides-external-table-data). 

1. Następująca kwerenda została użyta do utworzenia tabeli zewnętrznej *TaxiRides* w klastrze pomocy. 

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
1. Wynikowa tabela została utworzona w klastrze *pomocy:*

    ![Tabela zewnętrzna TaxiRides](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Zapytanie *TaxiRides* dane tabeli zewnętrznej 

Zaloguj się, aby [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) zbadać tabelę zewnętrzną *TaxiRides.* 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Zapytanie *TaxiRides* tabela zewnętrzna bez partycjonowania

[Uruchom tę kwerendę](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) w tabeli zewnętrznej *TaxiRides,* aby przedstawić przejazdy dla każdego dnia tygodnia, w całym zestawie danych. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Ta kwerenda pokazuje najbardziej pracowity dzień tygodnia. Ponieważ dane nie są podzielone na partycje, ta kwerenda może zająć dużo czasu, aby zwrócić wyniki (do kilku minut).

![renderowanie kwerendy nieobjętej partycją](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Zapytanie TaxiRides tabela zewnętrzna z partycjonowania 

[Uruchom tę kwerendę](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) w tabeli zewnętrznej *TaxiRides* pokazując typy taksówek (żółty lub zielony) używane w styczniu 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Ta kwerenda używa partycjonowania, który optymalizuje czas i wydajność kwerendy. Kwerenda filtruje kolumnę podzieloną na partycje (pickup_datetime) i zwraca wyniki w ciągu kilku sekund.

![renderowanie kwerendy podzielonej na partycje](media/data-lake-query-data/taxirides-with-partition.png)
  
Można napisać dodatkowe zapytania do uruchomienia w tabeli zewnętrznej *TaxiRides* i dowiedzieć się więcej o danych. 

## <a name="optimize-your-query-performance"></a>Optymalizacja wydajności zapytania

Optymalizuj wydajność kwerendy w sieci, korzystając z następujących najlepszych rozwiązań dotyczących wykonywania zapytań dotyczących danych zewnętrznych. 
 
### <a name="data-format"></a>Format danych
 
Użyj formatu kolumnowego dla zapytań analitycznych, ponieważ:
* Można odczytać tylko kolumny istotne dla kwerendy. 
* Techniki kodowania kolumn mogą znacznie zmniejszyć rozmiar danych.  
Usługa Azure Data Explorer obsługuje formaty kolumnowe Parquet i ORC. Format parkietu jest sugerowany ze względu na zoptymalizowaną implementację. 
 
### <a name="azure-region"></a>Region platformy Azure 
 
Sprawdź, czy dane zewnętrzne znajdują się w tym samym regionie platformy Azure co klaster usługi Azure Data Explorer. Zmniejsza to koszt i czas pobierania danych.
 
### <a name="file-size"></a>Rozmiar pliku
 
Optymalny rozmiar pliku to setki Mb (do 1 Gb) na plik. Unikaj wielu małych plików, które wymagają niepotrzebnego obciążenia, takich jak wolniejszy proces wyliczenia plików i ograniczone użycie formatu kolumnowego. Należy zauważyć, że liczba plików powinna być większa niż liczba rdzeni procesora CPU w klastrze usługi Azure Data Explorer. 
 
### <a name="compression"></a>Kompresja
 
Użyj kompresji, aby zmniejszyć ilość pobieranych danych z magazynu zdalnego. W przypadku formatu Parkietu należy użyć wewnętrznego mechanizmu kompresji Parkietu, który kompresuje grupy kolumn oddzielnie, umożliwiając w ten sposób ich odczytywanie oddzielnie. Aby sprawdzić poprawność użycia mechanizmu kompresji, sprawdź,<filename>czy pliki są nazwane<filename>w następujący sposób: " .gz.parquet" lub " .snappy.parquet" w przeciwieństwie do "<filename>.parquet.gz"). 
 
### <a name="partitioning"></a>Partycjonowanie
 
Zorganizuj dane przy użyciu partycji "folder", która umożliwia kwerendzie pomijanie nieistotnych ścieżek. Podczas planowania partycjonowania należy wziąć pod uwagę rozmiar pliku i typowe filtry w kwerendach, takich jak sygnatura czasowa lub identyfikator dzierżawy.
 
### <a name="vm-size"></a>Rozmiar maszyny wirtualnej
 
Wybierz jednostki SKU maszyn wirtualnych z większą liczą rdzeniami i wyższą przepustowością sieci (pamięć jest mniej ważna). Aby uzyskać więcej informacji, zobacz [Wybieranie poprawnej jednostki SKU maszyny Wirtualnej dla klastra usługi Azure Data Explorer](manage-cluster-choose-sku.md).

## <a name="next-steps"></a>Następne kroki

Wysyłaj zapytania do danych w usłudze Azure Data Lake przy użyciu Eksploratora danych platformy Azure. Dowiedz [się,](write-queries.md) jak pisać zapytania i czerpać dodatkowe informacje z danych.
