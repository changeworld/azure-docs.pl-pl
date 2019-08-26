---
title: Działanie kopiowania w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o działania kopiowania w usłudze Azure Data Factory, umożliwia kopiowanie danych z obsługiwanego źródłowego magazynu danych do obsługiwanego magazynu danych ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 59ac4b36a4bc2b3ff454b3a2ae98ce60f6bfcb5f
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996610"
---
# <a name="copy-activity-in-azure-data-factory"></a>Działanie kopiowania w usłudze Azure Data Factory

## <a name="overview"></a>Omówienie

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-data-movement-activities.md)
> * [Bieżąca wersja](copy-activity-overview.md)

W usłudze Azure Data Factory działanie kopiowania służy do kopiowania danych między danych magazynach znajdujących się lokalnie i w chmurze. Po skopiowaniu danych można je przeanalizować i analizować przy użyciu innych działań. Działanie kopiowania umożliwia również publikować wyniki analizy do analizy biznesowej (BI) i użycie aplikacji i przekształcania.

![Rola działania kopiowania](media/copy-activity-overview/copy-activity.png)

Działanie kopiowania jest wykonywane na [środowiska Integration Runtime](concepts-integration-runtime.md). Dla różnych scenariuszy kopiowania danych można wykorzystać różne rodzaje Integration Runtime:

* W przypadku kopiowania danych między magazynami danych, które są publicznie dostępne za pośrednictwem Internetu z dowolnych adresów IP, działanie Copy może być upoważnione przez **Azure Integration Runtime**, które jest bezpieczne, niezawodne, skalowalne i [globalnie dostępne](concepts-integration-runtime.md#integration-runtime-location).
* Podczas kopiowania danych z/do magazynów danych działającego lokalnie lub w sieci przy użyciu kontroli dostępu (na przykład, Azure Virtual Network), musisz skonfigurować **może być samodzielnie hostowane zintegrowanego środowiska uruchomieniowego** umożliwiające kopiowanie danych.

Środowisko Integration Runtime musi być skojarzone z każdego źródła i ujścia magazynu danych. Dowiedz się więcej informacji na temat działania kopiowania [Określa, które środowisko IR wybrać](concepts-integration-runtime.md#determining-which-ir-to-use).

Działanie kopiowania przechodzi przez następujące etapy do kopiowania danych ze źródła do ujścia. Usługa, która obsługuje działania kopiowania:

1. Odczytuje dane z magazynu danych źródłowych.
2. Wykonuje serializacji/deserializacji, kompresji i dekompresji, mapowania kolumn itd. Robi te operacje na podstawie konfiguracji zestaw wejściowy, wyjściowy zestaw danych i działania kopiowania.
3. Zapisuje dane w magazynie danych będących ujściem/docelowym.

![Omówienie działania kopiowania](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Formaty i obsługiwane magazyny danych

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Obsługiwane formaty plików

Można użyć działania kopiowania do **skopiuj pliki — jest** między dwoma magazynami danych opartych na plikach, w których przypadku dane są kopiowane efektywnie bez żadnych serializacji/deserializacji.

Działanie Copy umożliwia również odczytywanie i zapisywanie plików w określonych formatach: **Text, JSON, Avro, Orc i Parquet**oraz kompresowanie i dekompresowanie plików z następującymi kodekami: **Gzip, Wklęśnięcie, BZip2 i ZipDeflate**. Zobacz [obsługiwane formaty plików i kompresji](supported-file-formats-and-compression-codecs.md) ze szczegółowymi informacjami.

Na przykład należy wykonać następujące działania kopiowania:

* Skopiuj dane do SQL Server lokalnego i Zapisz je w Azure Data Lake Storage Gen2 w formacie Parquet.
* Skopiuj pliki w formacie tekstowym (CSV) w systemie plików w środowisku lokalnym i zapisywać do obiektów Blob platformy Azure, w formacie Avro.
* Skopiuj pliki spakowane z lokalnego systemu plików, a następnie zdekompresuj je do Azure Data Lake Storage Gen2.
* Kopiowanie danych w formacie tekstowym skompresowany (CSV) GZip z obiektów Blob platformy Azure i zapisu do usługi Azure SQL Database.
* I wiele więcej przypadków przy użyciu serializacji/deserializacji lub kompresji/dekompresji.

## <a name="supported-regions"></a>Obsługiwane regiony

To usługa zapewniająca działania kopiowania jest dostępna globalnie w regionach i lokalizacjach geograficznych na liście [lokalizacji środowiska Azure Integration Runtime](concepts-integration-runtime.md#integration-runtime-location). Dostępnie topologii zapewnia efektywne przenoszenia, zazwyczaj pozwala uniknąć przeskoków między regionami. Zobacz [usługi według regionów](https://azure.microsoft.com/regions/#services) dostępność usługi Data Factory i przenoszenia danych w regionie.

## <a name="configuration"></a>Konfigurowanie

Aby użyć działania kopiowania w usłudze Azure Data Factory, musisz:

1. **Utwórz połączone usługi dla źródłowego magazynu danych i magazynu danych ujścia.** Zobacz artykuł dotyczący łącznika "Właściwości połączonej usługi" sekcję na temat sposobu konfigurowania i obsługiwanych właściwości. Można znaleźć na liście obsługiwanych łączników w [obsługiwane magazyny danych i formatów](#supported-data-stores-and-formats) sekcji.
2. **Tworzenie zestawów danych dla źródła i ujścia.** Zapoznaj się z sekcją "właściwości zestawu danych" w artykule dotyczącym łącznika źródła i ujścia w temacie Jak skonfigurować i obsługiwane właściwości.
3. **Tworzenie potoku za pomocą działania kopiowania.** Następna sekcja zawiera przykład.

### <a name="syntax"></a>Składnia

Następujący szablon działania kopiowania zawiera stanowi wyczerpującej listy obsługiwanych właściwości. Określ te, które dopasowania ich do scenariusza.

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

### <a name="syntax-details"></a>Szczegóły składni

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type działania Copy musi mieć ustawioną wartość: **Copy** | Tak |
| inputs | Określ zestaw danych utworzony wskazującą na źródło danych. Działanie kopiowania obsługuje tylko jednego danych wejściowych. | Yes |
| outputs | Określ zestaw danych został utworzony, które punkty danych ujścia. Działanie kopiowania obsługuje tylko pojedynczego wyjścia. | Yes |
| typeProperties | Grupa właściwości, aby skonfigurować działanie kopiowania. | Yes |
| source | Określ typ źródła kopiowania i odpowiednie właściwości dotyczące sposobu pobierania danych.<br/><br/>Dowiedz się więcej szczegółów w sekcji "Właściwości działania kopiowania" w artykule łącznika na liście [obsługiwane magazyny danych i formatów](#supported-data-stores-and-formats). | Yes |
| sink | Określ typ obiektu sink kopiowania i odpowiednie właściwości dotyczące sposobu zapisywania danych.<br/><br/>Dowiedz się więcej szczegółów w sekcji "Właściwości działania kopiowania" w artykule łącznika na liście [obsługiwane magazyny danych i formatów](#supported-data-stores-and-formats). | Yes |
| translator | Określ mapowania kolumn jawne ze źródła do ujścia. Ma zastosowanie, gdy domyślne zachowanie kopiowania nie może spełnić Twoje potrzeby.<br/><br/>Dowiedz się, szczegółowe informacje z [schemat i dane mapowania typów](copy-activity-schema-and-type-mapping.md). | Nie |
| dataIntegrationUnits | Określ powerfulness z [Azure Integration Runtime](concepts-integration-runtime.md) umożliwiające kopiowanie danych. Wcześniej znane jako chmury jednostek przenoszenia danych (DMU). <br/><br/>Dowiedz się, szczegółowe informacje z [jednostek integracji danych](copy-activity-performance.md#data-integration-units). | Nie |
| parallelCopies | Określanie równoległości, który ma działanie kopiowania do użycia podczas odczytu danych ze źródła i zapisywania danych do ujścia.<br/><br/>Dowiedz się, szczegółowe informacje z [równoległych kopii](copy-activity-performance.md#parallel-copy). | Nie |
| enableStaging<br/>stagingSettings | Wybierz, aby przemieścić dane tymczasowe w magazynie obiektów blob, zamiast bezpośrednio kopiować dane ze źródła do ujścia.<br/><br/>Dowiedz się przydatne w scenariuszach i szczegółów konfiguracji z [kopiowania etapowego](copy-activity-performance.md#staged-copy). | Nie |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Wybierz sposób obsługi niezgodnych wierszy podczas kopiowania danych ze źródła do ujścia.<br/><br/>Dowiedz się, szczegółowe informacje z [odporność na uszkodzenia](copy-activity-fault-tolerance.md). | Nie |

## <a name="monitoring"></a>Monitorowanie

Możesz monitorować działanie kopiowania służy do uruchamiania w usłudze Azure Data Factory "Tworzenie i monitorowanie" w interfejsie użytkownika lub programowo.

### <a name="monitor-visually"></a>Monitorowanie wizualne

Aby wizualnego monitorowania uruchomienia działania kopiowania, przejdź do usługi data factory -> **tworzenie i monitorowanie** -> **kartę Monitor**, zobaczysz listę potok jest uruchamiany za pomocą łącza "Wyświetl uruchomienia działań"  **Akcje** kolumny.

![Monitorowanie uruchomień potoku](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Kliknij, aby wyświetlić listę działań w tym przebiegu potoku. W **akcje** kolumny, masz łącza do działania kopiowania w danych wejściowych, dane wyjściowe, błędy (w przypadku niepowodzenia uruchomienia działania kopiowania) i szczegóły.

![Monitorowanie uruchomień działania](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Kliknij przycisk "**szczegóły**" link w obszarze **akcje** Aby wyświetlić szczegóły wykonania działania kopiowania i charakterystyk wydajności. Przedstawia on informacje takie jak wolumin/wiersze/pliki danych skopiowane ze źródła do ujścia, przepływności, kroki przechodzi przez odpowiedni czas trwania i używane konfiguracje dla danego scenariusza kopiowania.

>[!TIP]
>W przypadku niektórych scenariuszy zobaczysz również pozycję "**porady dotyczące dostrajania wydajności**" na stronie monitorowanie kopiowania, która informuje o tym, co jest zidentyfikowane, i poprowadzi Cię przez informacje o tym, co należy zmienić, aby zwiększyć przepływność kopiowania, zobacz przykład z informacjami o [tym miejscu](#performance-and-tuning).

**Przykład: skopiuj z usługi Amazon S3 do usługi Azure Data Lake Store**
![szczegóły uruchamiania działania monitora](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Przykład: kopiowanie danych z usługi Azure SQL Database za pomocą usługi Azure SQL Data Warehouse kopiowania etapowego**
![szczegóły uruchamiania działania monitora](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Programowe monitorowanie

Szczegóły wykonania działania kopiowania i charakterystyki wydajności są również zwracane w sekcji wyjściowej > wyników uruchomienia działania kopiowania. Poniżej znajduje się wyczerpujący; zostaną wyświetlone tylko te mające zastosowanie do danego scenariusza kopiowania. Dowiedz się, jak monitorować działania uruchamiane z [szybkiego startu w sekcji monitorowanie](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Nazwa właściwości  | Opis | Jednostka |
|:--- |:--- |:--- |
| dataRead | Rozmiar danych odczytu ze źródła | Wartość Int64 **bajtów** |
| dataWritten | Rozmiar danych zapisany do ujścia | Wartość Int64 **bajtów** |
| filesRead | Liczba plików kopiowanych podczas kopiowania danych z usługi file storage. | Wartość Int64 (Brak jednostki) |
| filesWritten | Liczba plików kopiowanych podczas kopiowania danych do usługi file storage. | Wartość Int64 (Brak jednostki) |
| sourcePeakConnections | Maksymalna liczba jednoczesnych połączeń ustanowionych do źródłowego magazynu danych podczas uruchomienia działania kopiowania. | Wartość Int64 (Brak jednostki) |
| sinkPeakConnections | Maksymalna liczba jednoczesnych połączeń ustanowionych do magazynu danych ujścia podczas uruchomienia działania kopiowania. | Wartość Int64 (Brak jednostki) |
| rowsRead | Liczba wierszy, które są odczytywane ze źródła (nie dotyczy kopiowania binarnego). | Wartość Int64 (Brak jednostki) |
| rowsCopied | Liczba wierszy kopiowanych do ujścia (nie dotyczy kopiowania binarnego). | Wartość Int64 (Brak jednostki) |
| rowsSkipped | Liczba niezgodnych wierszy jest pomijane. Tę funkcję można włączyć przez zestaw "enableSkipIncompatibleRow" na wartość true. | Wartość Int64 (Brak jednostki) |
| copyDuration | Czas trwania kopiowania. | Wartość Int32 w ciągu kilku sekund |
| throughput | Stosunek transferu danych. | W liczbę zmiennoprzecinkową **KB/s** |
| sourcePeakConnections | Szczytowa liczba jednoczesnych połączeń ustanowionych w źródłowym magazynie danych podczas kopiowania. | Wartość Int32 |
| sinkPeakConnections| Szczytowa liczba jednoczesnych połączeń ustanowionych do magazynu danych ujścia podczas kopiowania.| Wartość Int32 |
| sqlDwPolyBase | Jeśli program PolyBase jest używany podczas kopiowania danych do usługi SQL Data Warehouse. | Wartość logiczna |
| redshiftUnload | Jeśli zwolnienie jest używany podczas kopiowania danych z usługi Redshift. | Wartość logiczna |
| hdfsDistcp | Jeśli narzędzia DistCp jest używany podczas kopiowania danych z systemu plików HDFS. | Wartość logiczna |
| effectiveIntegrationRuntime | Pokazują, które umożliwia zwiększenie możliwości dostępnych dla działania uruchamiania w formacie Runtime(s) integracji `<IR name> (<region if it's Azure IR>)`. | Tekst (ciąg) |
| usedDataIntegrationUnits | Skuteczne jednostek integracji danych podczas kopiowania. | Wartość Int32 |
| usedParallelCopies | Skuteczne parallelCopies podczas kopiowania. | Wartość Int32 |
| redirectRowPath | Ścieżka do dziennika pominięto niezgodnych wierszy w magazynie obiektów blob, należy skonfigurować w elemencie "redirectIncompatibleRowSettings". Poniżej przedstawiono przykład. | Tekst (ciąg) |
| executionDetails | Szczegółowe informacje na temat poszczególnych etapów działania kopiowania przechodzi przez, i odpowiadających jej krokach, czas trwania, konfiguracje używane, itp. Nie zaleca się przeanalizować w tej sekcji, ponieważ mogą ulec zmianie.<br/><br/>Na stronie ADF są również raportowane szczegółowe czasy trwania (w sekundach), w `detailedDurations`których podano poszczególne kroki. Czasy trwania tych kroków są wyłączne i tylko te, które dotyczą danego uruchomienia działania kopiowania, zostaną wyświetlone:<br/>- **Czas trwania kolejkowania** (`queuingDuration`): Czas, który upłynął do momentu rzeczywistego uruchomienia działania kopiowania w środowisku Integration Runtime. Jeśli używasz samoobsługowego środowiska IR, a ta wartość jest duża, Sugeruj, aby sprawdzić pojemność i użycie IR oraz skalować w górę i w dół zgodnie z obciążeniem. <br/>- **Czas trwania skryptu sprzed kopiowania** (`preCopyScriptDuration`): Upłynęło czasu między działaniem kopiowania, rozpoczynającym się od działania funkcji IR i kopiowania, kończący wykonywanie skryptu przed kopiowaniem w magazynie danych ujścia. Zastosuj podczas konfigurowania skryptu przed kopiowaniem. <br/>- **Czas do pierwszego bajtu** (`timeToFirstByte`): Czas, który upłynął między końcem poprzedniego kroku a IR otrzymuje pierwszy bajt z magazynu danych źródłowych. Zastosuj do źródła nieopartego na plikach. Jeśli ta wartość jest duża, Sugeruj, aby sprawdzić i zoptymalizować zapytanie lub serwer.<br/>- **Czas trwania transferu** (`transferDuration`): Czas, który upłynął między końcem poprzedniego kroku a środowiskiem IR transferu wszystkie dane ze źródła do ujścia. | Array |
| perfRecommendation | Kopiuj wskazówki dotyczące dostrajania wydajności. Zobacz sekcję dotyczącą [wydajności i dostrajania,](#performance-and-tuning) Aby uzyskać szczegółowe informacje. | Array |

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

Zobacz [schemat i dane mapowania typów](copy-activity-schema-and-type-mapping.md), który opisuje sposób mapowania dane źródła do ujścia w działaniu kopiowania.

## <a name="fault-tolerance"></a>Odporność na uszkodzenia

Domyślnie działanie kopiowania kończy kopiowanie danych i zwraca błąd, gdy napotka niezgodne dane między źródłem i ujścia. Jawnie można skonfigurować w celu pominięcia dziennika niezgodnych wierszy i kopiować tylko tych zgodnych danych do utworzenia kopii zakończyło się pomyślnie. Zobacz [działania kopiowania odporności na uszkodzenia](copy-activity-fault-tolerance.md) na więcej szczegółów.

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie

Zobacz [dostrajania przewodnik dotyczący wydajności działania kopiowania i](copy-activity-performance.md), która opisuje kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory. Ponadto Wyświetla zaobserwowanego podczas testowania wewnętrznego i w tym artykule omówiono różne sposoby, aby zoptymalizować wydajność działania kopiowania.

W niektórych przypadkach, gdy wykonujesz działanie kopiowania w podajniku APD, w górnej części [strony monitorowania działania kopiowania](#monitor-visually) zostanie wyświetlony ekran "**porady dotyczące dostrajania wydajności**", jak pokazano w poniższym przykładzie. Nie tylko informuje o wąskim Gardee zidentyfikowanym dla danego przebiegu kopiowania, ale również prowadzi użytkownika o tym, co należy zmienić, aby zwiększyć przepływność kopiowania. Porady dotyczące dostrajania wydajności obecnie udostępniają sugestie dotyczące korzystania z bazy danych w ramach kopiowania do Azure SQL Data Warehouse, aby zwiększyć Azure Cosmos DB RU lub Azure SQL DB jednostki DTU, gdy zasób po stronie magazynu danych jest wąskim gardłem, aby usunąć niepotrzebne etapy Kopiuj itp. Zasady dostrajania wydajności zostaną również stopniowo wzbogacane.

**Przykład: Kopiuj do usługi Azure SQL DB przy użyciu wskazówek dotyczących dostrajania wydajności**

W tym przykładzie podczas przebiegu kopiowania usługa ADF zwraca informację o tym, że ujścia usługi Azure SQL DB osiągnie duże użycie jednostek DTU, co spowalnia operacje zapisu, więc sugestią jest zwiększenie warstwy usługi Azure SQL DB o więcej jednostek DTU.

![Kopiuj monitorowanie ze wskazówkami dotyczącymi dostrajania wydajności](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Przyrostowa kopia
Data Factory obsługuje scenariusze przyrostowego kopiowania danych różnicowych z magazynu danych źródłowych do magazynu danych ujścia. Zobacz [samouczek: przyrostowe kopiowanie danych](tutorial-incremental-copy-overview.md).

## <a name="next-steps"></a>Następne kroki
Zobacz następujące Przewodniki Szybki Start, samouczków i przykładów:

- [Kopiowanie danych z jednej lokalizacji do innej lokalizacji w tej samej usłudze Azure Blob Storage](quickstart-create-data-factory-dot-net.md)
- [Kopiowanie danych z usługi Azure Blob Storage do usługi Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Kopiowanie danych z lokalnego programu SQL Server na platformie Azure](tutorial-hybrid-copy-powershell.md)
