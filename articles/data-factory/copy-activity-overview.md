---
title: Działanie kopiowania w Azure Data Factory
description: Dowiedz się więcej o działaniu kopiowania w Azure Data Factory. Za jego pomocą można skopiować dane z obsługiwanego źródłowego magazynu danych do obsługiwanego magazynu danych ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: jingwang
ms.openlocfilehash: fa2876b88a520480813ebfb8af8219d53c32057a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075559"
---
# <a name="copy-activity-in-azure-data-factory"></a>Działanie kopiowania w Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję Data Factory:"]
> * [Wersja 1](v1/data-factory-data-movement-activities.md)
> * [Bieżąca wersja](copy-activity-overview.md)

W Azure Data Factory można użyć działania kopiowania do kopiowania danych między magazynami danych znajdującymi się w środowisku lokalnym i w chmurze. Po skopiowaniu danych można użyć innych działań do dalszej transformacji i przeanalizowania. Możesz również użyć działania kopiowania, aby opublikować wyniki transformacji i analizy dla analizy biznesowej i użycia aplikacji.

![Rola działania kopiowania](media/copy-activity-overview/copy-activity.png)

Działanie kopiowania jest wykonywane w [środowisku Integration Runtime](concepts-integration-runtime.md). Możesz użyć różnych typów środowiska Integration Runtime dla różnych scenariuszy kopiowania danych:

* W przypadku kopiowania danych między dwoma magazynami danych, które są publicznie dostępne za pośrednictwem Internetu z dowolnego adresu IP, możesz użyć środowiska Azure Integration Runtime dla działania kopiowania. To środowisko Integration Runtime jest bezpieczne, niezawodne, skalowalne i [dostępne globalnie](concepts-integration-runtime.md#integration-runtime-location).
* Podczas kopiowania danych do i z magazynów danych, które znajdują się lokalnie lub w sieci z kontrolą dostępu (na przykład w sieci wirtualnej platformy Azure), należy skonfigurować własne środowisko Integration Runtime.

Środowisko Integration Runtime musi być skojarzone z poszczególnymi magazynami danych źródła i ujścia. Aby uzyskać informacje o tym, jak działanie kopiowania określa, które środowisko Integration Runtime ma być używane, zobacz [Określanie, który z nich ma być używany](concepts-integration-runtime.md#determining-which-ir-to-use).

Aby skopiować dane ze źródła do ujścia, usługa, która uruchamia działanie kopiowania, wykonuje następujące czynności:

1. Odczytuje dane z magazynu danych źródłowych.
2. Wykonuje serializacji/deserializacji, kompresję/dekompresowanie, Mapowanie kolumn i tak dalej. Wykonuje te operacje na podstawie konfiguracji wejściowego zestawu danych, wyjściowego zestawu danych i działania kopiowania.
3. Zapisuje dane w magazynie danych będących ujściem/docelowym.

![Omówienie działania kopiowania](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Formaty i obsługiwane magazyny danych

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Obsługiwane formaty plików

Możesz użyć działania kopiowania, aby skopiować pliki w postaci między dwoma magazynami danych opartymi na plikach. W takim przypadku dane są kopiowane efektywnie bez serializacji ani deserializacji.

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Można na przykład wykonać następujące działania kopiowania:

* Skopiuj dane z lokalnej bazy danych SQL Server i Zapisz dane do Azure Data Lake Storage Gen2 w formacie Parquet.
* Skopiuj pliki w formacie tekstu (CSV) z lokalnego systemu plików i Zapisz w usłudze Azure Blob Storage w formacie Avro.
* Skopiuj pliki spakowane z lokalnego systemu plików, zdekompresuj je i Zapisz do Azure Data Lake Storage Gen2.
* Skopiuj dane w formacie skompresowanego tekstu (CSV) w usłudze Azure Blob Storage i Zapisz je w Azure SQL Database.
* Wiele innych działań, które wymagają serializacji/deserializacji lub kompresji/dekompresji.

## <a name="supported-regions"></a>Obsługiwane regiony

Usługa, która umożliwia działanie kopiowania, jest dostępna globalnie w regionach i lokalizacje geograficzne na liście [lokalizacji w usłudze Azure Integration Runtime](concepts-integration-runtime.md#integration-runtime-location). Dostępnie topologii zapewnia efektywne przenoszenia, zazwyczaj pozwala uniknąć przeskoków między regionami. Zobacz [produkty według regionów](https://azure.microsoft.com/regions/#services) , aby sprawdzić dostępność Data Factory i przenoszenia danych w określonym regionie.

## <a name="configuration"></a>Konfiguracja

Aby użyć działania kopiowania w Azure Data Factory, należy wykonać następujące czynności:

1. **Utwórz połączone usługi dla źródłowego magazynu danych i magazynu danych ujścia.** Informacje o konfiguracji i obsługiwane właściwości można znaleźć w sekcji "właściwości połączonej usługi" w artykule. Listę obsługiwanych łączników można znaleźć w sekcji [obsługiwane magazyny i formaty danych](#supported-data-stores-and-formats) w tym artykule.
2. **Utwórz zestawy danych dla źródła i ujścia.** Zapoznaj się z sekcjami "właściwości zestawu danych" w artykule dotyczącym łącznika źródła i ujścia, aby uzyskać informacje o konfiguracji i obsługiwane właściwości.
3. **Utwórz potok za pomocą działania kopiowania.** Następna sekcja zawiera przykład.

### <a name="syntax"></a>Składnia

Następujący szablon działania kopiowania zawiera pełną listę obsługiwanych właściwości. Określ te, które dopasowania ich do scenariusza.

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
            "dataIntegrationUnits": <number>,
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

#### <a name="syntax-details"></a>Szczegóły składni

| Właściwość | Opis | Wymagana? |
|:--- |:--- |:--- |
| type | Dla działania kopiowania ustaw wartość `Copy` | Tak |
| inputs | Określ utworzony zestaw danych, który wskazuje na dane źródłowe. Działanie kopiowania obsługuje tylko pojedyncze dane wejściowe. | Tak |
| outputs | Określ utworzony zestaw danych, który wskazuje na dane ujścia. Działanie kopiowania obsługuje tylko pojedyncze dane wyjściowe. | Tak |
| typeProperties | Określ właściwości, aby skonfigurować działanie kopiowania. | Tak |
| source | Określ typ źródła kopiowania i odpowiednie właściwości do pobierania danych.<br/><br/>Aby uzyskać więcej informacji, zobacz sekcję "właściwości działania kopiowania" w artykule łącznika wymienionym w temacie [obsługiwane magazyny i formaty danych](#supported-data-stores-and-formats). | Tak |
| sink | Określ typ ujścia kopiowania i odpowiadające im właściwości zapisywania danych.<br/><br/>Aby uzyskać więcej informacji, zobacz sekcję "właściwości działania kopiowania" w artykule łącznika wymienionym w temacie [obsługiwane magazyny i formaty danych](#supported-data-stores-and-formats). | Tak |
| translator | Określ mapowania kolumn jawne ze źródła do ujścia. Ta właściwość ma zastosowanie, gdy domyślne zachowanie kopiowania nie spełnia Twoich potrzeb.<br/><br/>Aby uzyskać więcej informacji, zobacz [Mapowanie schematu w działaniu kopiowania](copy-activity-schema-and-type-mapping.md). | Nie |
| dataIntegrationUnits | Określ miarę, która przedstawia ilość mocy używanej przez [środowisko Azure Integration Runtime](concepts-integration-runtime.md) do kopiowania danych. Te jednostki były wcześniej znane jako jednostki przenoszenia danych w chmurze (DMU). <br/><br/>Aby uzyskać więcej informacji, zobacz [jednostki integracji danych](copy-activity-performance.md#data-integration-units). | Nie |
| parallelCopies | Określ równoległość, która ma być używana przez działanie kopiowania podczas odczytywania danych ze źródła i zapisywania danych do ujścia.<br/><br/>Aby uzyskać więcej informacji, zobacz [Kopiowanie równoległe](copy-activity-performance.md#parallel-copy). | Nie |
| enableStaging<br/>stagingSettings | Określ, czy przemieścić dane tymczasowe w magazynie obiektów blob, zamiast bezpośrednio kopiować dane ze źródła do ujścia.<br/><br/>Aby uzyskać informacje na temat przydatnych scenariuszy i szczegółów konfiguracji, zobacz [przygotowane kopie](copy-activity-performance.md#staged-copy). | Nie |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Wybierz sposób obsługi niezgodnych wierszy podczas kopiowania danych ze źródła do ujścia.<br/><br/>Aby uzyskać więcej informacji, zobacz [odporność na uszkodzenia](copy-activity-fault-tolerance.md). | Nie |

## <a name="monitoring"></a>Monitorowanie

Działanie kopiowania można monitorować w interfejsie użytkownika Azure Data Factory **autor & monitorowanie** lub programowo.

### <a name="monitor-visually"></a>Monitorowanie wizualne

Aby wizualnie monitorować przebieg działania kopiowania, przejdź do fabryki danych, a następnie przejdź do pozycji **autor & monitor**. Na karcie **monitor** zostanie wyświetlona lista uruchomień potoków z przyciskiem **Wyświetl uruchomienie działania** w kolumnie **Akcje** :

![Monitorowanie uruchomień potoku](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Wybierz pozycję **Wyświetl uruchomienia działania** , aby wyświetlić listę działań w ramach uruchomienia potoku. W kolumnie **Akcje** widoczne są linki do danych wejściowych działania kopiowania, danych wyjściowych, błędów (Jeśli uruchomienie działania kopiowania nie powiedzie się) i szczegółów:

![Monitorowanie uruchomień działania](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Wybierz przycisk **szczegóły** w kolumnie **Akcje** , aby wyświetlić szczegóły wykonania działania kopiowania i charakterystyki wydajności. Zobaczysz informacje na przykład o ilości/liczbie wierszy/liczbie danych skopiowanych ze źródła do ujścia, przepływności, czynności kopiowania odbywają się za pomocą odpowiednich czasów trwania i konfiguracji używanych dla scenariusza kopiowania.

>[!TIP]
>W niektórych scenariuszach zobaczysz również **porady dotyczące dostrajania wydajności** w górnej części strony monitorowanie kopiowania. Te porady informują o zidentyfikowanych wąskich gardeł i zawierają informacje o tym, co należy zmienić w celu zwiększenia przepływności kopiowania. Aby zapoznać się z przykładem, zobacz sekcję dotyczącą [wydajności i dostrajania](#performance-and-tuning) w tym artykule.

**Przykład: Kopiuj z usługi Amazon S3 do Azure Data Lake Store**
![szczegóły uruchomienia działania monitorowania](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Przykład: Kopiuj z Azure SQL Database do Azure SQL Data Warehouse za pomocą kopii etapowej**
szczegóły uruchomienia działania monitorowania ![](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Programowe monitorowanie

Szczegóły wykonania działania kopiowania i charakterystyki wydajności są również zwracane w sekcji **wynik uruchomienia działania kopiowania** > **danych wyjściowych** . Poniżej znajduje się kompletna lista właściwości, które mogą zostać zwrócone. Zobaczysz tylko właściwości, które mają zastosowanie do Twojego scenariusza kopiowania. Aby uzyskać informacje o sposobie monitorowania przebiegów działań, zobacz [monitorowanie uruchomienia potoku](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Nazwa właściwości  | Opis | Jednostka |
|:--- |:--- |:--- |
| dataRead | Ilość danych odczytanych ze źródła. | Wartość Int64, w bajtach |
| dataWritten | Ilość danych zapisywana w zlewie. | Wartość Int64, w bajtach |
| filesRead | Liczba plików skopiowanych podczas kopiowania z magazynu plików. | Wartość Int64 (Brak jednostki) |
| filesWritten | Liczba plików skopiowanych podczas kopiowania do magazynu plików. | Wartość Int64 (Brak jednostki) |
| sourcePeakConnections | Szczytowa liczba jednoczesnych połączeń ustanowionych w źródłowym magazynie danych podczas uruchomienia działania kopiowania. | Wartość Int64 (Brak jednostki) |
| sinkPeakConnections | Szczytowa liczba jednoczesnych połączeń ustanowionych do magazynu danych ujścia podczas uruchomienia działania kopiowania. | Wartość Int64 (Brak jednostki) |
| rowsRead | Liczba wierszy odczytanych ze źródła (nie dotyczy kopiowania binarnego). | Wartość Int64 (Brak jednostki) |
| rowsCopied | Liczba wierszy skopiowanych do ujścia (nie dotyczy kopiowania binarnego). | Wartość Int64 (Brak jednostki) |
| rowsSkipped | Liczba niezgodnych wierszy, które zostały pominięte. Aby można było pominąć niezgodne wiersze, należy ustawić `enableSkipIncompatibleRow` na true. | Wartość Int64 (Brak jednostki) |
| copyDuration | Czas trwania kopiowania. | Wartość Int32 (w sekundach) |
| Przepływność | Szybkość transferu danych. | Liczba zmiennoprzecinkowa w KB/s |
| sourcePeakConnections | Szczytowa liczba jednoczesnych połączeń ustanowionych w źródłowym magazynie danych podczas uruchomienia działania kopiowania. | Wartość Int32 (brak jednostki) |
| sinkPeakConnections| Szczytowa liczba jednoczesnych połączeń ustanowionych do magazynu danych ujścia podczas uruchomienia działania kopiowania.| Wartość Int32 (brak jednostki) |
| sqlDwPolyBase | Określa, czy baza danych jest używana, gdy dane są kopiowane do SQL Data Warehouse. | Wartość logiczna |
| redshiftUnload | Czy ZWALNIAnie jest używane, gdy dane są kopiowane z RedShift. | Wartość logiczna |
| hdfsDistcp | Określa, czy pomocą distcp jest używany, gdy dane są kopiowane z systemu plików HDFS. | Wartość logiczna |
| effectiveIntegrationRuntime | Środowisko Integration Runtime (IR) lub środowisko uruchomieniowe służące do włączania uruchomienia działania w formacie `<IR name> (<region if it's Azure IR>)`. | Tekst (ciąg) |
| usedDataIntegrationUnits | Skuteczne jednostek integracji danych podczas kopiowania. | Wartość Int32 |
| usedParallelCopies | Skuteczne parallelCopies podczas kopiowania. | Wartość Int32 |
| redirectRowPath | Ścieżka do dziennika pominiętych niezgodnych wierszy w magazynie obiektów BLOB skonfigurowanym we właściwości `redirectIncompatibleRowSettings`. Zobacz [odporność na uszkodzenia](#fault-tolerance) w dalszej części tego artykułu. | Tekst (ciąg) |
| executionDetails | Więcej szczegółów na temat etapów działania kopiowania oraz odpowiednich czynności, czasów trwania, konfiguracji itd. Nie zalecamy przeanalizowania tej sekcji, ponieważ może ona ulec zmianie.<br/><br/>Data Factory również zgłasza szczegółowe czasy trwania (w sekundach) poświęcane na różne etapy w obszarze `detailedDurations`. Czas trwania tych kroków ma charakter wyłączny. Wyświetlane są tylko czasy trwania mające zastosowanie do danego uruchomienia działania kopiowania:<br/>**Czas trwania kolejkowania** (`queuingDuration`): ilość czasu przed rozpoczęciem działania kopiowania w programie Integration Runtime. Jeśli używasz własnego środowiska IR i ta wartość jest duża, sprawdź pojemność i użycie IR oraz Skaluj w górę lub w dół zgodnie z obciążeniem. <br/>**Czas trwania skryptu przed kopiowaniem** (`preCopyScriptDuration`): czas, który upłynął od momentu uruchomienia działania kopiowania w środowisku IR i kiedy działanie kopiowania zakończy uruchamianie skryptu sprzed kopiowania w magazynie danych ujścia. Ma zastosowanie podczas konfigurowania skryptu poprzedzającego kopiowanie. <br/>**Czas do pierwszego bajtu** (`timeToFirstByte`): czas, który upłynął między końcem poprzedniego kroku a czasem, gdy środowisko IR odbierze pierwszy bajt ze źródłowego magazynu danych. Dotyczy źródeł nieopartych na plikach. Jeśli ta wartość jest duża, należy sprawdzić i zoptymalizować zapytanie lub serwer.<br/>**Czas trwania transferu** (`transferDuration`): czas, który upłynął między końcem poprzedniego kroku a czasem, gdy środowisko IR przeniesie wszystkie dane ze źródła do ujścia. | Tablica |
| perfRecommendation | Kopiuj wskazówki dotyczące dostrajania wydajności. Aby uzyskać szczegółowe informacje [, zobacz wydajność i dostrajanie](#performance-and-tuning) . | Tablica |

```json
"output": {
    "dataRead": 6198358,
    "dataWritten": 19169324,
    "filesRead": 1,
    "sourcePeakConnections": 1,
    "sinkPeakConnections": 2,
    "rowsRead": 39614,
    "rowsCopied": 39614,
    "copyDuration": 1325,
    "throughput": 4.568,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 4,
    "usedParallelCopies": 1,
    "executionDetails": [
        {
            "source": {
                "type": "AzureBlobStorage"
            },
            "sink": {
                "type": "AzureSqlDatabase"
            },
            "status": "Succeeded",
            "start": "2019-08-06T01:01:36.7778286Z",
            "duration": 1325,
            "usedDataIntegrationUnits": 4,
            "usedParallelCopies": 1,
            "detailedDurations": {
                "queuingDuration": 2,
                "preCopyScriptDuration": 12,
                "transferDuration": 1311
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "Sink Azure SQL Database: The DTU utilization was high during the copy activity run. To achieve better performance, you are suggested to scale the database to a higher tier than the current 1600 DTUs.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2043368",
            "RuleName": "AzureDBTierUpgradePerfRecommendRule"
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Mapowanie typu danych i schematu

Zobacz [Mapowanie schematu i typu danych,](copy-activity-schema-and-type-mapping.md) Aby uzyskać informacje o tym, jak działanie kopiowania mapuje dane źródłowe do ujścia.

## <a name="fault-tolerance"></a>Odporność na uszkodzenia

Domyślnie działanie kopiowania kończy kopiowanie danych i zwraca błąd, gdy wiersze danych źródłowych są niezgodne z wierszami danych ujścia. Aby pomyślnie wykonać kopię, można skonfigurować działanie kopiowania, aby pominąć i zarejestrować niezgodne wiersze i skopiować tylko zgodne dane. Aby uzyskać szczegółowe informacje, zobacz [odporność na błędy działania kopiowania](copy-activity-fault-tolerance.md) .

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie

[Przewodnik dotyczący wydajności i skalowalności działania kopiowania](copy-activity-performance.md) opisuje kluczowe czynniki wpływające na wydajność przenoszenia danych za pośrednictwem działania kopiowania w Azure Data Factory. Wyświetla również wartości wydajności zaobserwowane podczas testowania i omawia sposób optymalizowania wydajności działania kopiowania.

W niektórych scenariuszach po uruchomieniu działania kopiowania w Data Factory w górnej części [strony monitorowania działania kopiowania](#monitor-visually)zostaną wyświetlone **porady dotyczące dostrajania wydajności** , jak pokazano w poniższym przykładzie. Porady poinformują o wąskim Gardee zidentyfikowanym dla danego przebiegu kopiowania. Zawierają także informacje o tym, co należy zmienić w celu zwiększenia przepływności kopiowania. Porady dotyczące dostrajania wydajności obecnie udostępniają sugestie dotyczące korzystania z bazy danych w przypadku kopiowania do Azure SQL Data Warehouse, zwiększając Azure Cosmos DB jednostek ru lub Azure SQL Database DTU, gdy zasób po stronie magazynu danych to wąskie gardło i usunięcie niepotrzebne kopie przygotowane.

**Przykład: Kopiuj do Azure SQL Database przy użyciu porady dostrajania wydajności**

W tym przykładzie podczas wykonywania kopii Data Factory śledzi wysokie wykorzystanie jednostek DTU w Azure SQL Database ujścia. Ten stan spowalnia operacje zapisu. Sugestia polega na zwiększeniu DTU w warstwie Azure SQL Database:

![Kopiuj monitorowanie ze wskazówkami dotyczącymi dostrajania wydajności](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Przyrostowa kopia
Data Factory umożliwia przyrostowe kopiowanie danych różnicowych z magazynu danych źródłowych do magazynu danych ujścia. Aby uzyskać szczegółowe informacje, zobacz [Samouczek: przyrostowo Kopiuj dane](tutorial-incremental-copy-overview.md).

## <a name="next-steps"></a>Następne kroki
Zobacz następujące Przewodniki Szybki Start, samouczków i przykładów:

- [Skopiuj dane z jednej lokalizacji do innej lokalizacji na tym samym koncie usługi Azure Blob Storage](quickstart-create-data-factory-dot-net.md)
- [Kopiowanie danych z usługi Azure Blob Storage do Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Kopiowanie danych z lokalnej bazy danych SQL Server na platformę Azure](tutorial-hybrid-copy-powershell.md)
