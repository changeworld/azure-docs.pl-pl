---
title: LightIngest to narzędzie wiersza polecenia do pozyskiwania do Usługi Azure Data Explorer.
description: Dowiedz się więcej o LightIngest, narzędziu wiersza polecenia do pozyskiwania danych ad hoc do Usługi Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 95d943685cf511acb88f9e48d36a9dd43b0a27d2
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548015"
---
# <a name="install-and-use-lightingest"></a>Instalowanie i używanie LightIngest

LightIngest to narzędzie wiersza polecenia do pozyskiwania danych ad hoc do Eksploratora danych platformy Azure.
Narzędzie może pobierać dane źródłowe z folderu lokalnego lub z kontenera magazynu obiektów blob platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* LightIngest - pobierz go jako część [pakietu Microsoft.Azure.Kusto.Tools NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)

    ![Oświetlenienajednak](media/lightingest/lightingest-download-area.png)

* WinRAR - pobierz go z [www.win-rar.com/download.html](http://www.win-rar.com/download.html)

## <a name="install-lightingest"></a>Zainstaluj LightIngest

1. Przejdź do lokalizacji na komputerze, w której pobrano lightingest. 
1. Korzystając z usługi WinRAR, wyodrębnij katalog *narzędzi* do komputera.

## <a name="run-lightingest"></a>Uruchom LightIngest

1. Przejdź do katalogu wyodrębnionych *narzędzi* na komputerze.
1. Usuń istniejące informacje o lokalizacji z paska lokalizacji.
    
      ![Usuwanie informacji o lokalizacji](media/lightingest/lightingest-location-bar.png)

1. Wprowadź `cmd` i naciśnij **klawisz Enter**.
1. W wierszu polecenia `LightIngest.exe` wprowadź, po którym następuje odpowiedni argument wiersza polecenia.

    > [!Tip]
    > Aby wyświetlić listę obsługiwanych argumentów `LightIngest.exe /help`wiersza polecenia, należy wprowadzić plik .
    >
    >![Pomoc wiersza polecenia](media/lightingest/lightingest-cmd-line-help.png)

1. Wprowadź, `ingest-` po którym następuje parametry połączenia z klastrem usługi Azure Data Explorer, który będzie zarządzał pozyskiwania.
    Ujednij parametry połączenia w cudzysłowie i postępuj zgodnie [ze specyfikacją ciągów połączeń Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto).

    Przykład:
    ```
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* Zalecaną metodą jest, aby LightIngest działał z `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net`punktem końcowym spożycia w punkcie końcowym spożycia w . W ten sposób usługa Azure Data Explorer może zarządzać obciążeniem pozyskiwania i można łatwo odzyskać po błędach przejściowych. Można jednak również skonfigurować LightIngest do pracy bezpośrednio z`https://{yourClusterNameAndRegion}.kusto.windows.net`punktem końcowym silnika ( ).

> [!Note]
> Jeśli połkniesz bezpośrednio z punktem końcowym silnika, `ingest-`nie musisz uwzględniać , ale nie będzie funkcji DM, aby chronić silnik i poprawić wskaźnik powodzenia spożycia.

* Aby uzyskać optymalną wydajność pozyskiwania, ważne jest, aby LightIngest znać rozmiar surowych danych i tak LightIngest oszacować nieskompresowany rozmiar plików lokalnych. Jednak LightIngest może nie być w stanie poprawnie oszacować nieprzetworzonego rozmiaru skompresowanych obiektów blob bez uprzedniego ich pobrania. W związku z tym podczas pozyskiwania skompresowanych obiektów blob, ustaw `rawSizeBytes` właściwość na metadanych obiektu blob do nieskompresowanego rozmiaru danych w bajtach.

## <a name="general-command-line-arguments"></a>Ogólne argumenty wiersza polecenia

|Nazwa argumentu         |Krótka nazwa   |Typ    |Obowiązkowy |Opis                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |ciąg  |Obowiązkowy |[Ciąg połączenia usługi Azure Data Explorer](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto) określający punkt końcowy Kusto, który będzie obsługiwał pozyskiwania. Powinny być ujęte w cudzysłowie |
|-baza danych             |-db          |ciąg  |Optional (Opcjonalność)  |Docelowa nazwa bazy danych Usługi Azure Data Explorer |
|-tabela                |             |ciąg  |Obowiązkowy |Docelowa nazwa tabeli Eksploratora danych platformy Azure |
|-sourcePath           |-źródło      |ciąg  |Obowiązkowy |Ścieżka do plików źródłowych lub głównego identyfikatora URI kontenera obiektów blob. Jeśli dane znajdują się w obiektach blob, musi zawierać klucz konta magazynu lub sygnatury dostępu Współdzielonego. Zaleca się ujęte w cudzysłowie |
|-prefiks               |             |ciąg  |Optional (Opcjonalność)  |Gdy dane źródłowe do pozyskiwania znajduje się w magazynie obiektów blob, ten prefiks adresu URL jest współużytkowana przez wszystkie obiekty blob, z wyłączeniem nazwy kontenera. <br>Na przykład, jeśli dane `MyContainer/Dir1/Dir2`są w , `Dir1/Dir2`to prefiks powinien być . Zaleca się załączanie w cudzysłowie |
|-wzór              |             |ciąg  |Optional (Opcjonalność)  |Wzorzec, za pomocą którego są zbierane pliki źródłowe/obiekty blob. Obsługuje symbole wieloznaczne. Na przykład `"*.csv"`. Zaleca się ujęte w cudzysłowie |
|-zipPattern           |             |ciąg  |Optional (Opcjonalność)  |Wyrażenie regularne do użycia podczas wybierania plików w archiwum ZIP do pozyskiwania.<br>Wszystkie inne pliki w archiwum zostaną zignorowane. Na przykład `"*.csv"`. Zaleca się otoczyć go w cudzysłowie |
|-format               |-f           |ciąg  |Optional (Opcjonalność)  |Format danych źródłowych. Musi być jednym z [obsługiwanych formatów](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) |
|-ingestionMappingPath |-mappingPath |ciąg  |Optional (Opcjonalność)  |Ścieżka do pliku mapowania kolumn pozyskiwania (obowiązkowe dla formatów Json i Avro). Zobacz [mapowania danych](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-ingestionMappingRef  |-mappingRef  |ciąg  |Optional (Opcjonalność)  |Nazwa wstępnie utworzonego mapowania kolumn pozyskiwania (obowiązkowe dla formatów Json i Avro). Zobacz [mapowania danych](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-creationTimePattern  |             |ciąg  |Optional (Opcjonalność)  |Gdy zestaw, jest używany do wyodrębniania CreationTime właściwości ze ścieżki pliku lub obiektu blob. Zobacz [Używanie argumentu CreationTimePattern](#using-creationtimepattern-argument) |
|-ignoreFirstRow       |-ignoreFirst |bool    |Optional (Opcjonalność)  |Jeśli jest ustawiona, pierwszy rekord każdego pliku/obiektu blob jest ignorowany (na przykład, jeśli dane źródłowe mają nagłówki) |
|-tag                  |             |ciąg  |Optional (Opcjonalność)  |[Tagi](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) do skojarzenia z pochłoniętymi danymi. Dozwolone jest wiele wystąpień |
|-dontWait             |             |bool    |Optional (Opcjonalność)  |Jeśli ustawiona na "true", nie czeka na zakończenie pozyskiwania. Przydatne przy ciuciu dużych ilości plików/obiektów blob |

### <a name="using-creationtimepattern-argument"></a>Używanie argumentu CreationTimePattern

Argument `-creationTimePattern` wyodrębnia CreationTime właściwość ze ścieżki pliku lub obiektu blob. Deseń nie musi odzwierciedlać całej ścieżki elementu, tylko sekcję zawierającą sygnaturę czasową, której chcesz użyć.

Wartości argumentów muszą zawierać:
* Stały test bezpośrednio poprzedzający sygnaturę czasowa, ujęty w pojedyncze cudzysłowy
* Format sygnatury czasowej w standardowym [notacji .NET DateTime](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)
* Stały tekst bezpośrednio po sygnatury czasowej. Na przykład jeśli nazwy obiektów `historicalvalues19840101.parquet` blob kończą się (sygnatura czasowa to cztery cyfry dla roku, dwie cyfry `-creationTimePattern` dla miesiąca i dwie cyfry dla dnia miesiąca), odpowiednią wartością argumentu jest:

```
ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'historicalvalues'yyyyMMdd'.parquet'"
 -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
```

### <a name="command-line-arguments-for-advanced-scenarios"></a>Argumenty wiersza polecenia dla zaawansowanych scenariuszy

|Nazwa argumentu         |Krótka nazwa   |Typ    |Obowiązkowy |Opis                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|-kompresja          |-cr          |double  |Optional (Opcjonalność)  |Wskazówka współczynnika kompresji. Przydatne podczas pozyskiwania skompresowanych plików/obiektów blob, aby pomóc Eksploratorowi danych platformy Azure ocenić rozmiar nieprzetworzonych danych. Obliczony jako oryginalny rozmiar podzielony przez skompresowany rozmiar |
|-limit                |-l           |liczba całkowita |Optional (Opcjonalność)  |Jeśli jest ustawiona, ogranicza połkowanie do pierwszych plików N |
|-listOnly             |-lista        |bool    |Optional (Opcjonalność)  |Jeśli jest ustawiony, wyświetla tylko elementy, które zostałyby wybrane do spożycia| 
|-ingestCzas        |             |liczba całkowita |Optional (Opcjonalność)  |Limit czasu w minutach dla wszystkich operacji pozyskiwania zakończenia. Domyślnie wartość`60`|
|-forceSync            |             |bool    |Optional (Opcjonalność)  |Jeśli jest ustawiona, wymusza synchroniczne połknienie. Domyślnie wartość`false` |
|-dataBatchSize        |             |liczba całkowita |Optional (Opcjonalność)  |Ustawia całkowity limit rozmiaru (MB, nieskompresowany) każdej operacji pozyskiwania |
|-filesInBatch         |             |liczba całkowita |Optional (Opcjonalność)  |Ustawia limit liczby plików/obiektów blob każdej operacji pozyskiwania |
|-devTracing           |-ślad       |ciąg  |Optional (Opcjonalność)  |Jeśli jest ustawiona, dzienniki diagnostyczne są zapisywane `RollingLogs` w katalogu lokalnym (domyślnie w bieżącym katalogu lub mogą być modyfikowane przez ustawienie wartości przełącznika) |

## <a name="blob-metadata-properties"></a>Właściwości metadanych obiektu Blob
W przypadku użycia z obiektami blob platformy Azure LightIngest użyje niektórych właściwości metadanych obiektu blob, aby rozszerzyć proces pozyskiwania.

|Właściwość metadanych                            | Sposób użycia                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | Jeśli jest ustawiona, będzie interpretowana jako nieskompresowany rozmiar danych                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | Interpretowany jako sygnatura czasowa UTC. Jeśli jest ustawiona, będzie używana do zastąpienia czasu tworzenia w Kusto. Przydatne w scenariuszach wypełniania zasypywania |

## <a name="usage-examples"></a>Przykłady użycia

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Injestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>Pobędzenie obiektów blob przy użyciu klucza konta magazynu lub tokenu Sygnatury dostępu Współdzielonego

* Połknienie 10 obiektów `ACCOUNT`blob `DIR`w obszarze `CONT`określonego konta magazynu , w folderze , w kontenerze i dopasowanie wzorca`*.csv.gz`
* Miejsce docelowe to baza danych, `DB`tabela `TABLE`, a mapowanie `MAPPING` pozyskiwania jest wstępnie
* Narzędzie będzie czekać do zakończenia operacji pozyskiwania
* Zanotuj różne opcje określania docelowej bazy danych i klucza konta magazynu w porównaniu z tokenem sygnatury dostępu Współdzielonego

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>Połknienie wszystkich obiektów blob w kontenerze, z wyłączeniem wierszy nagłówka

* Ponajmowanie wszystkich obiektów `ACCOUNT`blob `DIR1/DIR2`w obszarze `CONT`określonego konta magazynu , w folderze , w kontenerze i dopasowywanie wzorca`*.csv.gz`
* Miejsce docelowe to baza danych, `DB`tabela `TABLE`, a mapowanie `MAPPING` pozyskiwania jest wstępnie
* Źródłowe obiekty blob zawierają wiersz nagłówka, więc narzędzie jest instruowane o upuszczaniu pierwszego rekordu każdego obiektu blob
* Narzędzie opublikuje dane do spożycia i nie będzie czekać na zakończenie operacji pozyskiwania

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>Połknienie wszystkich plików JSON ze ścieżki

* Połknienie wszystkich `PATH`plików pod ścieżką, pasujące do wzorca`*.json`
* Miejsce docelowe to baza danych `DB`, tabela `TABLE`, a mapowanie pozyskiwania jest zdefiniowane w pliku lokalnym`MAPPING_FILE_PATH`
* Narzędzie opublikuje dane do spożycia i nie będzie czekać na zakończenie operacji pozyskiwania

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>Łowianie plików i zapisywanie diagnostycznych plików śledzenia

* Połknienie wszystkich `PATH`plików pod ścieżką, pasujące do wzorca`*.json`
* Miejsce docelowe to baza danych `DB`, tabela `TABLE`, a mapowanie pozyskiwania jest zdefiniowane w pliku lokalnym`MAPPING_FILE_PATH`
* Narzędzie opublikuje dane do spożycia i nie będzie czekać na zakończenie operacji pozyskiwania
* Diagnostyka pliki śledzenia zostaną zapisane lokalnie w folderze`LOGS_PATH`

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
## <a name="changelog"></a>Changelog
|Wersja        |Zmiany                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>Dodano `-zipPattern` argument</li><li>Dodano `-listOnly` argument</li><li>Podsumowanie argumentów jest wyświetlane przed rozpoczęciem uruchamiania</li><li>CreationTime jest odczytywany z właściwości metadanych obiektu blob `-creationTimePattern` lub z obiektu blob lub nazwy pliku, zgodnie z argumentem</li></ul>|
