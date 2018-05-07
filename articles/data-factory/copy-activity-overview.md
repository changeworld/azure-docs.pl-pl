---
title: Aktywność kopiowania w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o działanie kopiowania w fabryce danych Azure, której można skopiować dane z magazynu danych obsługiwanych źródłowych w magazynie danych obsługiwanych ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jingwang
ms.openlocfilehash: 8ae1402b6821d1b42fa8f2bf9c2f6453a5ce7109
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="copy-activity-in-azure-data-factory"></a>Działanie kopiowania w fabryce danych Azure

## <a name="overview"></a>Przegląd

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-data-movement-activities.md)
> * [Wersja 2 — wersja zapoznawcza](copy-activity-overview.md)

W fabryce danych Azure działanie kopiowania służy do kopiowania danych między danych magazynów znajduje się w sieci lokalnej i w chmurze. Po skopiowaniu danych można go dalej transformacji i analizy. Działanie kopiowania umożliwia także publikowanie transformacji i wyniki analizy do analizy biznesowej (BI) i użycie aplikacji.

![Rola działanie kopiowania](media/copy-activity-overview/copy-activity.png)

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [działanie kopiowania w wersji 1](v1/data-factory-data-movement-activities.md).

Działanie kopiowania jest wykonywana na [integrację środowiska uruchomieniowego](concepts-integration-runtime.md). W scenariuszu kopii dla innych danych można użyć różnych wersji środowiska uruchomieniowego integracji:

* Jeśli kopiowanie danych między danymi przechowuje oba są publicznie, działanie kopiowania może upoważnionego przez **środowiska uruchomieniowego integracji Azure**, która jest bezpieczne, niezawodne i skalowalne, i [globalnie dostępną](concepts-integration-runtime.md#integration-runtime-location).
* Podczas kopiowania danych z/do magazynów danych znajdujących się na lokalnych lub w sieci przy użyciu kontroli dostępu (na przykład sieci wirtualnej Azure), należy skonfigurować **hosta samodzielnego zintegrowanego środowiska uruchomieniowego** upoważnienie kopię danych.

Integracja środowiska uruchomieniowego musi być skojarzone z każdym źródłowy i odbiorczy magazynu danych. Dowiedz się więcej informacji na temat aktywności kopiowania [Określa, które IR, aby użyć](concepts-integration-runtime.md#determining-which-ir-to-use).

Działanie kopiowania przechodzi przez następujące etapy, aby skopiować dane ze źródła do ujścia. Usługa obsługującego działanie kopiowania:

1. Odczytuje dane z magazynu danych źródłowych.
2. Wykonuje serializacji/deserializacji, kompresji i dekompresji, mapowanie kolumn itp. Tak, aby te operacje konfiguracji wejściowy zestaw danych, wyjściowy zestaw danych i działanie kopiowania.
3. Zapisuje dane w magazynie danych zbiornika i docelowego.

![Omówienie działania kopiowania](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Magazyny danych obsługiwane i formaty

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Obsługiwane formaty plików

Można użyć do działania kopiowania **skopiuj pliki jako — jest** między dwoma magazynów danych opartych na plikach, w których przypadku danych jest kopiowana wydajnie bez żadnych serializacji/deserializacji.

Działanie kopiowania obsługuje również odczytywanie z oraz zapisywanie do plików w określonym formacie: **tekst JSON, Avro, ORC i Parquet**i koder-dekoder kompresji **GZip, Deflate, BZip2 i ZipDeflate** są obsługiwane. Zobacz [obsługiwane formaty plików i kompresji](supported-file-formats-and-compression-codecs.md) ze szczegółami.

Na przykład można wykonywać następujące działania kopiowania:

* Kopiowanie danych w lokalnym programie SQL Server i zapisać w formacie ORC Azure Data Lake Store.
* Skopiuj pliki w formacie tekstowym (CSV) z lokalnego systemu plików i zapisać w formacie Avro obiektów Blob platformy Azure.
* Skopiuj pliki zip z lokalnego systemu plików, a następnie dekompresja ziemi do usługi Azure Data Lake Store.
* Kopiowanie danych w formacie tekstowym skompresowane (CSV) GZip z obiektów Blob platformy Azure i zapisu do bazy danych SQL Azure.

## <a name="supported-regions"></a>Obsługiwane regiony

Usługa obsługującego działanie kopiowania jest dostępna globalnie w regionach i lokalizacji geograficznych wymienionych w [lokalizacji środowiska uruchomieniowego integracji Azure](concepts-integration-runtime.md#integration-runtime-location). Ogólnie dostępna topologia zapewnia przepływ danych wydajne, pozwalający na uniknięcie zwykle przeskoków między regionu. Zobacz [usług według regionu](https://azure.microsoft.com/regions/#services) dostępność fabryki danych i przenoszenie danych w regionie.

## <a name="configuration"></a>Konfigurowanie

Aby użyć działania kopiowania w fabryce danych Azure, musisz:

1. **Tworzenie połączonej usługi magazynu danych źródła i ujścia magazynu danych.** Zobacz sekcję "Połączone usługi właściwości" artykułu łącznika na sposób konfigurowania i obsługiwanych właściwości. Można znaleźć na liście obsługiwanych łącznika w [obsługiwane formaty i magazyny danych](#supported-data-stores-and-formats) sekcji.
2. **Tworzenie zestawów danych dla źródłowy i odbiorczy.** Zapoznaj się ze źródłem i sink sekcji "Zestawu danych właściwości" artykuły łącznika na sposób konfigurowania i obsługiwanych właściwości.
3. **Utworzyć potok z działania kopiowania.** Następna sekcja zawiera przykład.  

### <a name="syntax"></a>Składnia

Następujący szablon działania kopiowania zawiera stanowi wyczerpującej listy obsługiwanych właściwości. Określ te, które pasują do danego scenariusza.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "cloudDataMovementUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

### <a name="syntax-details"></a>Szczegóły składni

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type działania kopiowania: **kopiowania** | Yes |
| Dane wejściowe | Określ zestaw danych został utworzony wskazującą na źródło danych. Działanie kopiowania obsługuje tylko jeden danych wejściowych. | Yes |
| wyjścia | Określ zestaw danych został utworzony wskazującą na obiekt sink danych. Działanie kopiowania obsługuje tylko pojedynczego wyjścia. | Yes |
| typeProperties | Grupa właściwości, aby skonfigurować działanie Kopiuj. | Yes |
| source | Określ typ źródła kopiowania i odpowiednie właściwości na temat pobierania danych.<br/><br/>Dowiedz się więcej szczegółów w sekcji "Kopiuj właściwości działania" w artykule łącznika na liście [obsługiwane formaty i magazyny danych](#supported-data-stores-and-formats). | Yes |
| obiekt sink | Określ typ ujścia kopiowania i odpowiednie właściwości na temat zapisywania danych.<br/><br/>Dowiedz się więcej szczegółów w sekcji "Kopiuj właściwości działania" w artykule łącznika na liście [obsługiwane formaty i magazyny danych](#supported-data-stores-and-formats). | Yes |
| translator | Określ mapowania kolumn jawne ze źródła do zbiornika. Ma zastosowanie, gdy domyślne zachowanie kopiowania nie może spełnić potrzeby.<br/><br/>Dowiedz się więcej szczegółów z [schemat i dane mapowania typu](copy-activity-schema-and-type-mapping.md). | Nie |
| cloudDataMovementUnits | Określ powerfulness z [środowiska uruchomieniowego integracji Azure](concepts-integration-runtime.md) upoważnienie kopię danych.<br/><br/>Dowiedz się więcej szczegółów z [jednostki przepływu danych w chmurze](copy-activity-performance.md). | Nie |
| parallelCopies | Określanie równoległości interesujące Kopiuj działania do użycia podczas odczytu danych z źródła i zapisywania danych do zbiornika.<br/><br/>Dowiedz się więcej szczegółów z [równoległych kopii](copy-activity-performance.md#parallel-copy). | Nie |
| enableStaging<br/>stagingSettings | Wybierz etap przejściowy danych w magazynie obiektów blob aa zamiast bezpośrednio kopii danych ze źródła do zbiornika.<br/><br/>Poznaj przydatne w scenariuszach i szczegóły konfiguracji z [przemieszczane kopiowania](copy-activity-performance.md#staged-copy). | Nie |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Wybierz sposób obsługi niezgodne wierszy podczas kopiowania danych ze źródła do zbiornika.<br/><br/>Dowiedz się więcej szczegółów z [odporność na uszkodzenia](copy-activity-fault-tolerance.md). | Nie |

## <a name="monitoring"></a>Monitorowanie

Można monitorować działanie kopiowania Uruchom fabryki danych Azure "Autor & Monitor" w interfejsie użytkownika lub programowo. Możesz następnie porównać wydajności i konfiguracji scenariusz działania kopiowania [dotyczące wydajności](copy-activity-performance.md#performance-reference) z testów wewnętrznych.

### <a name="monitor-visually"></a>Monitorowanie wizualne

Aby wizualnie monitorować uruchamiania działania kopiowania, przejdź do fabrykę danych -> **autora & Monitor** -> **kartę Monitor**, zobaczysz listę potoku jest uruchamiany w łącze"Uruchamiadziałaniewidoku" **Akcje** kolumny. 

![Monitorowanie uruchomień potoku](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Kliknij, aby wyświetlić listę działań w tym przebiegu potoku. W **akcje** kolumny, masz łącza do danych wejściowych działania kopiowania, dane wyjściowe, błędy (w przypadku uruchamiania działania kopiowania nie powiedzie się) i szczegóły.

![Monitorowanie uruchomień działania](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Kliknij przycisk "**szczegóły**" łącze w obszarze **akcje** aby zobaczyć szczegóły wykonywania działania kopiowania i charakterystyki wydajności. Przedstawia on informacje o tym wolumin/wiersze/pliki danych skopiowane ze źródła do zbiornika, przepływności, kroki przechodzi przez o odpowiedni czas trwania i używany dla danego scenariusza kopii konfiguracji.

**Przykład: kopiowanie z Amazon S3 do usługi Azure Data Lake Store**
![szczegóły uruchomienia działania monitorowania](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Przykład: kopiowanie z bazy danych SQL Azure przy użyciu usługi Azure SQL Data Warehouse przemieszczane kopiowania**
![szczegóły uruchomienia działania monitorowania](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Monitorowanie programowo

Szczegóły wykonywania działania kopiowania i charakterystyki wydajności także zwracany w przypadku działania kopiowania wynik uruchomienia -> sekcji danych wyjściowych. Poniżej znajduje się kompletnej; zostaną wyświetlone tylko te odpowiednie do realizowanego scenariusza kopiowania. Dowiedz się, jak monitorować działania Uruchom z [szybkiego startu w sekcji monitorowanie](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Nazwa właściwości  | Opis | Jednostka |
|:--- |:--- |:--- |
| DataRead | Rozmiar danych ze źródła do odczytu | Wartość Int64 w **bajtów** |
| DataWritten | Rozmiar danych zapisywane do zbiornika | Wartość Int64 w **bajtów** |
| filesRead | Liczba plików kopiowanych podczas kopiowania danych z magazynu plików. | Wartości Int64 (Brak jednostki) |
| filesWritten | Liczba plików kopiowanych podczas kopiowania danych do magazynu plików. | Wartości Int64 (Brak jednostki) |
| rowsCopied | Liczba wierszy kopiowane (nie dotyczy binarne kopii). | Wartości Int64 (Brak jednostki) |
| rowsSkipped | Liczba wierszy niezgodne pomijany. Można włączyć funkcję przez zestaw "enableSkipIncompatibleRow" na wartość true. | Wartości Int64 (Brak jednostki) |
| Przepływność | Współczynnik szybkości transferu danych | Liczba w zmiennoprzecinkowa **KB/s** |
| copyDuration | Czas trwania kopii | Wartość Int32 w sekundach |
| sqlDwPolyBase | Jeśli program PolyBase jest używany podczas kopiowania danych do usługi SQL Data Warehouse. | Wartość logiczna |
| redshiftUnload | Jeśli zwolnienie jest używany podczas kopiowania danych z Redshift. | Wartość logiczna |
| hdfsDistcp | Jeśli narzędzia DistCp jest używany podczas kopiowania danych z systemu plików HDFS. | Wartość logiczna |
| effectiveIntegrationRuntime | Pokaż Runtime(s) integracji używanego do uruchamiania w formacie działania pozwalają `<IR name> (<region if it's Azure IR>)`. | Tekst (ciąg) |
| usedCloudDataMovementUnits | Skuteczne chmury jednostki przepływu danych podczas kopiowania. | Wartość Int32 |
| usedParallelCopies | Skuteczne parallelCopies podczas kopiowania. | Wartość Int32|
| redirectRowPath | Ścieżka do dziennika pominiętych wierszy niezgodne w magazynie obiektów blob, należy skonfigurować w obszarze "redirectIncompatibleRowSettings". Zobacz w poniższym przykładzie. | Tekst (ciąg) |
| executionDetails | Więcej informacji na temat etapów działania kopiowania przechodzi przez, i odpowiadające jej kroki, czas trwania, konfiguracje używane,... itd. Nie zaleca się przeanalizować w tej sekcji, ponieważ mogą ulec zmianie. | Tablica |

```json
"output": {
    "dataRead": 107280845500,
    "dataWritten": 107280845500,
    "filesRead": 10,
    "filesWritten": 10,
    "copyDuration": 224,
    "throughput": 467707.344,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US 2)",
    "usedCloudDataMovementUnits": 32,
    "usedParallelCopies": 8,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureDataLakeStore"
            },
            "status": "Succeeded",
            "start": "2018-01-17T15:13:00.3515165Z",
            "duration": 221,
            "usedCloudDataMovementUnits": 32,
            "usedParallelCopies": 8,
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 219
            }
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Mapowanie typu danych i schematu

Zobacz [schemat i dane mapowania typu](copy-activity-schema-and-type-mapping.md), która opisuje sposób działania kopiowania mapowania danych źródłowych do zbiornika.

## <a name="fault-tolerance"></a>Odporność na uszkodzenia

Domyślnie działanie kopiowania zatrzymuje kopiowanie danych i zwraca błąd, po napotkaniu niezgodne dane między źródłowy i odbiorczy. Można jawnie skonfigurować Aby pominąć i zalogować się niezgodne wiersze i kopiować tylko tych zgodne danych do utworzenia kopii zakończyło się pomyślnie. Zobacz [działanie kopiowania odporność na uszkodzenia](copy-activity-fault-tolerance.md) na więcej szczegółów.

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie

Zobacz [wydajności działania kopiowania i dostrajania przewodnik](copy-activity-performance.md), która opisuje kluczowych czynników wpływających na wydajność przepływu danych (działanie kopiowania) w fabryce danych Azure. Również zawiera listę obserwowana wydajność podczas testowania wewnętrznego oraz opisano różne sposoby optymalizacji wydajności działania kopiowania.

## <a name="incremental-copy"></a>Kopia przyrostowa 
Fabryka danych w wersji 2 obsługuje scenariusze przyrostowo kopiowania danych różnicowych z magazynu danych źródłowych w magazynie danych docelowego. Zobacz [samouczek: przyrostowo kopiowanie danych](tutorial-incremental-copy-overview.md). 

## <a name="read-and-write-partitioned-data"></a>Odczytywanie i zapisywanie danych podzielonej na partycje
W wersji 1 usługi fabryka danych Azure obsługiwane odczytu lub zapisu danych podzielonej na partycje przy użyciu SliceStart/SliceEnd/WindowStart/WindowEnd zmienne systemowe. W wersji 2 to zachowanie można osiągnąć za pomocą parametru potoku i czas/zaplanowana godzina rozpoczęcia tego wyzwalacza jako wartość parametru. Aby uzyskać więcej informacji, zobacz [jak do odczytu lub zapisu na partycje danych](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące Przewodniki Szybki Start, samouczki i przykłady:

- [Kopiowanie danych z jednej lokalizacji do innej lokalizacji, w tym samym magazynie obiektów Blob Azure](quickstart-create-data-factory-dot-net.md)
- [Kopiowanie danych z magazynu obiektów Blob Azure do bazy danych SQL Azure](tutorial-copy-data-dot-net.md)
- [Kopiowanie danych z lokalnego programu SQL Server na platformie Azure](tutorial-hybrid-copy-powershell.md)
