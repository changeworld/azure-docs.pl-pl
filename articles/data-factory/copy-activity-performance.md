---
title: Przewodnik dotyczący wydajności i skalowalności działania kopiowania
description: Zapoznaj się z najważniejszymi czynnikami wpływającymi na wydajność przenoszenia danych w Azure Data Factory podczas korzystania z działania kopiowania.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/24/2019
ms.openlocfilehash: 1b1b02e310c98a78006d258333c0ec10e89e3b31
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927464"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Przewodnik dotyczący wydajności i skalowalności działania kopiowania

> [!div class="op_single_selector" title1="Wybierz używaną wersję Azure Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-activity-performance.md)
> * [Bieżąca wersja](copy-activity-performance.md)

Niezależnie od tego, czy chcesz przeprowadzić migrację danych na dużą skalę z usługi Data Lake, Enterprise Data Warehouse (rozszerzenia) na platformę Azure, czy chcesz pozyskać dane z różnych źródeł na platformie Azure na potrzeby analizy danych Big Data, ma kluczowe znaczenie dla osiągnięcia optymalnej wydajności i względem.  Azure Data Factory zapewnia wydajny, odporny na i ekonomiczny mechanizm pozyskiwania danych w odpowiedniej skali, dzięki czemu inżynierowie danych chcą tworzyć wysoce wydajne i skalowalne potoki pozyskiwania danych.

Po przeczytaniu tego artykułu, możliwe będzie odpowiadać na następujące pytania:

- Jaki poziom wydajności i skalowalności można osiągnąć za pomocą działania kopiowania APD na potrzeby migracji danych i scenariuszy pozyskiwania danych?

- Jakie kroki należy wykonać w celu dostosowania wydajności działania kopiowania ADF?
- Jakie pokrętła optymalizacji wydajności ADF można wykorzystać w celu optymalizacji wydajności dla pojedynczego uruchomienia działania kopiowania?
- Jakie inne czynniki mają być brane pod uwagę w przypadku optymalizacji wydajności kopiowania?

> [!NOTE]
> Jeśli nie znasz ogólnie działania kopiowania, przed przeczytaniem tego artykułu zapoznaj się z [omówieniem działania kopiowania](copy-activity-overview.md) .

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Kopiowanie wydajności i skalowalności w osiągalnej postaci ADF

System ADF oferuje architekturę bezserwerową, która umożliwia równoległość na różnych poziomach, dzięki czemu deweloperzy mogą tworzyć potoki w celu pełnego wykorzystania przepustowości sieci, a także liczby operacji we/wy magazynu i przepustowości w celu zmaksymalizowania przepływności przenoszenia danych w środowisku.  Oznacza to, że przepływność, którą można osiągnąć, można oszacować przez zmierzenie minimalnej przepływności oferowanej przez źródłowy magazyn danych, docelowy magazyn danych i przepustowość sieci między źródłem a miejscem docelowym.  Poniższa tabela oblicza czas trwania kopiowania na podstawie rozmiaru danych i limitu przepustowości dla danego środowiska. 

| Rozmiar danych/ <br/> bandwidth | 50 Mb/s    | 100 Mb/s  | 500 Mb/s  | 1 Gb/s   | 5 Gb/s   | 10 Gb/s  | 50 GB/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0,0 min   |
| **10 GB**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 GB**                  | 4,6 godz.    | 2,3 godz.   | 0,5 godz.   | 0,2 godz.  | 0,05 godz. | 0,02 godz. | 0,0 godz.   |
| **1 TB**                    | 46,6 godz.   | 23,3 godz.  | 4,7 godz.   | 2,3 godz.  | 0,5 godz.  | 0,2 godz.  | 0,05 godz.  |
| **10 TB**                   | 19,4 dni  | 9,7 dni  | 1,9 dni  | 0,9 dni | 0,2 dni | 0,1 dni | 0,02 dni |
| **100 TB**                  | 194,2 dni | 97,1 dni | 19,4 dni | 9,7 dni | 1,9 dni | 1 dni   | 0,2 dni  |
| **1 PB**                    | 64,7 mo    | 32,4 mo   | 6,5 mo    | 3,2 mo   | 0,6 mo   | 0,3 mo   | 0,06 mo   |
| **10 PB**                   | 647,3 mo   | 323,6 mo  | 64,7 mo   | 31,6 mo  | 6,5 mo   | 3,2 mo   | 0,6 mo    |

Kopiowanie na podajniku APD jest skalowalne na różnych poziomach:

![jak są skalowane kopie APD](media/copy-activity-performance/adf-copy-scalability.png)

- Przepływ sterowania ADF można uruchomić równolegle wiele działań kopiowania, na przykład przy użyciu [dla każdej pętli](control-flow-for-each-activity.md).
- Pojedyncze działanie kopiowania może korzystać z skalowalnych zasobów obliczeniowych: w przypadku korzystania z Azure Integration Runtime można określić [maksymalnie 256 DIUs](#data-integration-units) dla każdego działania kopiowania w sposób bezserwerowy. w przypadku korzystania z Integration Runtime samoobsługowego można ręcznie skalować maszynę w górę lub w poziomie na wielu maszynach ([maksymalnie 4 węzły](create-self-hosted-integration-runtime.md#high-availability-and-scalability)), a działanie pojedynczej kopii spowoduje partycjonowanie zestawu plików we wszystkich węzłach.
- Pojedyncze działanie kopiowania odczytuje i zapisuje dane w magazynie danych przy użyciu [równolegle](#parallel-copy)wielu wątków.

## <a name="performance-tuning-steps"></a>Kroki dostosowywania wydajności

Wykonaj następujące kroki, aby dostroić wydajność usługi Azure Data Factory za pomocą działania kopiowania.

1. **Pobierz zestaw danych testowych i Ustanów linię bazową.** Podczas fazy tworzenia Przetestuj potok za pomocą działania kopiowania względem reprezentatywnej próbki danych. Wybrany zestaw danych powinien reprezentować typowe wzorce danych (strukturę folderów, wzorzec pliku, schemat danych itp.) i jest wystarczająco duży, aby można było oszacować wydajność kopiowania, na przykład wykonanie operacji kopiowania trwa 10 minut lub dłużej. Zbierz szczegóły wykonania i charakterystyki wydajności następujące po [monitorowaniu działania kopiowania](copy-activity-overview.md#monitoring).

2. **Jak zmaksymalizować wydajność pojedynczego działania kopiowania**:

   Aby rozpocząć pracę z programem, zalecamy pierwsze zmaksymalizowanie wydajności przy użyciu jednego działania kopiowania.

   **Jeśli działanie kopiowania jest wykonywane na Azure Integration Runtime:**

   Rozpocznij od domyślnych wartości ustawień [jednostki integracji danych (DIU)](#data-integration-units) i [kopiowania równoległego](#parallel-copy) .  Wykonaj przebieg testu wydajnościowego i zanotuj osiągniętą wydajność, a także wartości rzeczywiste używane do DIUs i kopii równoległych.  Zapoznaj się z [monitorowaniem działania kopiowania](copy-activity-overview.md#monitoring) , jak zbierać wyniki uruchamiania i używane ustawienia wydajności.

   Teraz można przeprowadzać dodatkowe przebiegi testów wydajności, za każdym razem podwaja wartość ustawienia DIU.  Alternatywnie, jeśli uważasz, że wydajność zostanie osiągnięta przy użyciu domyślnego ustawienia, jest znacznie poniżej oczekiwań, można bardziej znacząco zwiększyć ustawienie DIU w kolejnym przebiegu testu.

   Działanie kopiowania powinno być skalowane niemal idealnie liniowo, gdy zwiększy się ustawienie DIU.  Jeśli po podwojeniu ustawienia DIU nie widzisz przepustowości podwójnie, mogą wystąpić dwie rzeczy:

   - Używany wzorzec kopiowania nie pozwala na Dodawanie więcej DIUs.  Mimo że określono większą wartość DIU, rzeczywista DIUa była taka sama, co oznacza, że przepływność jest taka sama jak wcześniej.  W takim przypadku Maksymalizuj zagregowaną przepływność, uruchamiając wiele kopii jednocześnie, odwołując się do kroku 3.
   - Dodając więcej DIUs (więcej informacji o mocy), a tym samym zapewniając wyższą szybkość wyodrębniania, przesyłania i ładowania danych, zarówno źródłowy magazyn danych, sieć między, jak i docelowy magazyn danych osiągnął wąskie gardło i może być ograniczone.  W takim przypadku spróbuj skontaktować się z administratorem magazynu danych lub administratorem sieci, aby podnieść górny limit, lub też Zmniejsz ustawienie DIU, dopóki nie zostanie zatrzymane ograniczenie przepustowości.

   **Jeśli działanie kopiowania jest wykonywane na samodzielnym Integration Runtime:**

   Zalecamy używanie dedykowanej maszyny niezależnej od serwera hostującym magazyn danych w celu hostowania środowiska Integration Runtime.

   Zacznij od domyślnych wartości ustawienia [kopiowania równoległego](#parallel-copy) i przy użyciu jednego węzła dla samodzielnego środowiska IR.  Wykonaj przebieg testu wydajnościowego i zanotuj osiągniętą wydajność.

   Jeśli chcesz osiągnąć wyższą przepływność, możesz skalować w górę lub w poziomie do własnego środowiska IR:

   - Jeśli użycie procesora CPU i dostępnej pamięci w węźle podczerwieni nie jest w pełni wykorzystane, ale wykonanie współbieżnych zadań zbliża się do limitu, należy skalować w górę, zwiększając liczbę współbieżnych zadań, które mogą być uruchamiane w węźle.  Aby uzyskać instrukcje, zobacz [tutaj](create-self-hosted-integration-runtime.md#scale-up) .
   - Jeśli z drugiej strony procesor CPU jest wysoki w węźle samodzielnego środowiska IR lub dostępna pamięć jest niska, można dodać nowy węzeł, aby ułatwić skalowanie obciążenia w wielu węzłach.  Aby uzyskać instrukcje, zobacz [tutaj](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

   Podczas skalowania w górę lub w poziomie środowiska samodzielnego środowiska IR należy powtórzyć przebieg testu wydajności, aby zobaczyć, czy coraz bardziej zwiększa się przepływność.  Jeśli przepływność zostanie zatrzymana, najprawdopodobniej źródłowy magazyn danych, sieć między nimi lub docelowy magazyn danych osiągnął wąskie gardło i zaczyna się uzyskać ograniczenie przepustowości. W takim przypadku spróbuj skontaktować się z administratorem magazynu danych lub administratorem sieci, aby podnieść górny limit lub alternatywnie, Wróć do poprzedniego ustawienia skalowania dla samodzielnego środowiska IR. 

3. **Jak zmaksymalizować zagregowaną przepływność, uruchamiając wiele kopii jednocześnie:**

   Teraz, gdy masz zmaksymalizowaną wydajność pojedynczego działania kopiowania, jeśli nie osiągnięto jeszcze górnych limitów przepływności dla środowiska — sieci, magazynu danych źródłowych i docelowego magazynu danych — można uruchomić wiele działań kopiowania równolegle przy użyciu konstrukcji przepływu sterowania ADF, takich jak [dla każdej pętli](control-flow-for-each-activity.md).

4. **Wskazówki dotyczące dostrajania wydajności i funkcje optymalizacji.** W niektórych przypadkach po uruchomieniu działania kopiowania w Azure Data Factory na początku [monitorowania działania kopiowania](copy-activity-overview.md#monitor-visually)zostanie wyświetlony komunikat "porady dotyczące dostrajania wydajności", jak pokazano w poniższym przykładzie. Komunikat informuje o wąskim gardła, który został zidentyfikowany dla danego przebiegu kopiowania. Przedstawiono w nim również informacje o tym, co należy zmienić w celu zwiększenia przepływności kopiowania. Porady dotyczące dostrajania wydajności obecnie udostępniają sugestie, takie jak:

   - Użyj podstawy podczas kopiowania danych do Azure SQL Data Warehouse.
   - Zwiększ liczbę jednostek żądania Azure Cosmos DB lub Azure SQL Database DTU (jednostki przepływności bazy danych), gdy zasób po stronie magazynu danych to wąskie gardło.
   - Usuń niezbędną kopię przygotowaną.

   Zasady dostrajania wydajności zostaną również stopniowo wzbogacane.

   **Przykład: Kopiuj do Azure SQL Database ze wskazówkami dotyczącymi dostrajania wydajności**

   W tym przykładzie podczas przebiegu kopiowania Azure Data Factory zauważyć, Azure SQL Database ujścia osiągnie duże użycie jednostek DTU, co spowalnia operacje zapisu. Sugestią jest zwiększenie warstwy Azure SQL Databaseej o więcej DTU. 

   ![Kopiuj monitorowanie ze wskazówkami dotyczącymi dostrajania wydajności](media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

   Ponadto należy pamiętać o następujących funkcjach optymalizacji wydajności:

   - [Kopiuj równoległe](#parallel-copy)
   - [Jednostki integracji danych](#data-integration-units)
   - [Kopiowania przejściowego](#staged-copy)
   - [Samoobsługowa skalowalność środowiska Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime)

5. **Rozwiń konfigurację do całego zestawu danych.** Gdy wyniki wykonywania i wydajność są zadowalające, można rozwinąć definicję i potok, aby pokryć cały zestaw danych.

## <a name="copy-performance-optimization-features"></a>Kopiowanie funkcji optymalizacji wydajności

Azure Data Factory zapewnia następujące funkcje optymalizacji wydajności:

- [Kopiuj równoległe](#parallel-copy)
- [Jednostki integracji danych](#data-integration-units)
- [Kopiowania przejściowego](#staged-copy)

### <a name="data-integration-units"></a>Jednostki integracji danych

Jednostka integracji danych to miara, która reprezentuje moc (kombinację procesora CPU, pamięci i alokacji zasobów sieciowych) pojedynczej jednostki w Azure Data Factory. Jednostka integracji danych ma zastosowanie tylko do [środowiska Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime), ale nie do [własnego środowiska Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

Zostanie naliczona opłata za **użycie DIUs \* Kopiuj czas trwania \* cena jednostkowa/DIU — godzina**. Zobacz bieżące ceny [tutaj](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). Waluta lokalna i oddzielne opłaty mogą dotyczyć poszczególnych typów subskrypcji.

Dozwolony DIUs do upoważnienia do uruchomienia działania kopiowania jest z **przedziału od 2 do 256**. Jeśli nie zostanie określony, lub wybierzesz opcję "automatycznie" w interfejsie użytkownika, Data Factory dynamicznie zastosować optymalne ustawienie DIU na podstawie pary Source-sink i wzorca danych. W poniższej tabeli wymieniono domyślne DIUs używane w różnych scenariuszach kopiowania:

| Skopiuj scenariusza | Domyślne DIUs określany przez usługę |
|:--- |:--- |
| Kopiowanie danych między magazynami oparte na plikach | Od 4 do 32 w zależności od liczby i rozmiaru plików |
| Kopiuj dane do Azure SQL Database lub Azure Cosmos DB |Od 4 do 16 w zależności od warstwy Azure SQL Database ujścia lub Cosmos DB (liczba DTU/jednostek ru) |
| Wszystkie inne scenariusze kopiowania | 4 |

Aby zastąpić to ustawienie domyślne, należy określić wartość dla **dataIntegrationUnits** właściwości w następujący sposób. *Rzeczywista liczba DIUs* używany w czasie wykonywania operacji kopiowania jest równa lub mniejsza niż skonfigurowana wartość, w zależności od wzorca usługi danych.

Można zobaczyć DIUs używane dla każdej kopii przebiegu w danych wyjściowych działania kopiowania podczas monitorowania uruchomienia działania. Aby uzyskać więcej informacji, zobacz [monitorowanie aktywności kopiowania](copy-activity-overview.md#monitoring).

> [!NOTE]
> Ustawienie DIUs o rozmiarze większym niż cztery jest obecnie stosowane tylko w przypadku kopiowania wielu plików z usługi Azure Blob/ADLS Gen1/ADLS Gen2/Amazon S3/Google Cloud Storage/Cloud FTP/Cloud SFTP lub z magazynu danych relacyjnych chmury z obsługą opcji partycji (w tym [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)) do innych magazynów danych w chmurze.

**Przykład:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="parallel-copy"></a>Kopiowanie równoległe

Możesz użyć właściwości **parallelCopies** , aby wskazać równoległość, która ma być używana przez działanie kopiowania. Tę właściwość można traktować jako maksymalną liczbę wątków w ramach działania kopiowania, które mogą być jednocześnie odczytywane ze źródła lub zapisywać do magazynów danych ujścia.

Dla każdego przebiegu działania kopiowania Azure Data Factory określa liczbę kopii równoległych, które mają być używane do kopiowania danych ze źródłowego magazynu danych do docelowego magazynu danych. Domyślna liczba kopii równoległych, których używa, zależy od typu używanego źródła i ujścia.

| Skopiuj scenariusza | Domyślna liczba równoległych kopii określany przez usługę |
| --- | --- |
| Kopiowanie danych między magazynami oparte na plikach |Zależy od rozmiaru plików i liczby DIUs używanych do kopiowania danych między dwoma magazynami danych w chmurze lub konfiguracją fizyczną środowiska Integration Runtime. |
| Kopiowanie z magazynu danych relacyjnych z włączoną opcją partycji (w tym z systemami [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)i [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source))|4 |
| Kopiowanie danych z dowolnego magazynu źródłowego do usługi Azure Table Storage |4 |
| Innych scenariuszach kopiowania |1 |

> [!TIP]
> Podczas kopiowania danych między magazynami opartymi na plikach, domyślne zachowanie zwykle zapewnia najlepszą przepływność. Domyślne zachowanie jest określane na podstawie wzorca pliku źródłowego.

Aby kontrolować obciążenie maszyn, które obsługują magazyny danych, lub dostosować wydajność kopiowania, można zastąpić wartość domyślną i określić wartość właściwości **parallelCopies** . Wartość musi być liczba całkowita większa lub równa 1. W czasie wykonywania w celu uzyskania najlepszej wydajności działanie kopiowania używa wartości, która jest mniejsza lub równa ustawionej wartości.

**Punkty do uwagi:**

- Podczas kopiowania danych między magazynami opartymi na plikach **parallelCopies** określa równoległość na poziomie pliku. Fragmentowanie w pojedynczym pliku odbywa się automatycznie i w sposób przezroczysty. Zaprojektowano w celu użycia najlepszego odpowiedniego rozmiaru fragmentu dla danego typu magazynu danych źródłowych w celu załadowania danych równolegle i ortogonalnych do **parallelCopies**. Rzeczywista liczba równoległych kopii usługi data movement service używa dla operacji kopiowania w czasie wykonywania jest nie więcej niż liczba plików, których masz. Jeśli zachowanie kopiowania ma wartość **mergeFile**, działanie kopiowania nie może korzystać z równoległości na poziomie plików.
- Podczas kopiowania danych z magazynów, które nie są oparte na plikach (z wyjątkiem programu [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [tabeli SAP](connector-sap-table.md#sap-table-as-source)i [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) Connector jako źródła z włączoną obsługą partycjonowania danych) do magazynów, które są oparte na plikach, Usługa przenoszenia danych ignoruje Właściwość **parallelCopies** . Nawet jeśli równoległości jest określona, nie zostanie zastosowane w tym przypadku.
- Właściwość **parallelCopies** jest prostopadła do **dataIntegrationUnits**. Pierwsza jest liczony we wszystkich jednostkach integracji danych.
- Po określeniu wartości właściwości **parallelCopies** należy wziąć pod uwagę wzrost obciążenia magazynów danych źródłowych i ujścia. Należy również rozważyć zwiększenie obciążenia do własnego środowiska Integration Runtime, jeśli działanie kopiowania jest przez niego uprawnione, na przykład w przypadku kopii hybrydowej. Ten wzrost obciążenia występuje szczególnie w przypadku wielu działań lub współbieżnych uruchomień tych samych działań, które działają w tym samym magazynie danych. Jeśli zauważysz, że magazyn danych lub własne środowisko Integration Runtime jest przeciążony, zmniejsz wartość **parallelCopies** , aby zwolnić obciążenie.

**Przykład:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

### <a name="staged-copy"></a>Kopiowania przejściowego

Podczas kopiowania danych z magazynu danych źródłowych do magazynu danych ujścia, można użyć magazynu obiektów Blob jako tymczasowego magazynu przejściowego. Przemieszczania jest szczególnie użyteczna w następujących przypadkach:

- **Chcesz pozyskać dane z różnych magazynów danych do SQL Data Warehouse za pośrednictwem bazy.** Usługa SQL Data Warehouse używa programu PolyBase jako mechanizm o wysokiej przepływności ładowanie dużej ilości danych do usługi SQL Data Warehouse. Dane źródłowe muszą znajdować się w magazynie obiektów blob lub Azure Data Lake Store i muszą spełniać dodatkowe kryteria. Podczas ładowania danych z magazynem danych innych niż usługi Blob storage lub Azure Data Lake Store, możesz aktywować dane kopiowanie za pośrednictwem tymczasowego przejściowego magazynu obiektów Blob. W takim przypadku Azure Data Factory wykonuje wymagane przekształcenia danych, aby upewnić się, że spełnia on wymagania bazy. Następnie używa programu PolyBase do ładowania danych do usługi SQL Data Warehouse wydajnie. Aby uzyskać więcej informacji, zobacz [przy użyciu technologii PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Czasami trwa przeprowadzenie hybrydowego przenoszenia danych (czyli kopiowania z lokalnego magazynu danych do magazynu danych w chmurze) przez wolne połączenie sieciowe.** Aby zwiększyć wydajność, można użyć kopii przygotowanej do skompresowania danych w środowisku lokalnym, co pozwala na przenoszenie danych do tymczasowego magazynu danych w chmurze. Następnie można zdekompresować dane w magazynie przemieszczania przed załadowaniem do docelowego magazynu danych.
- **Nie chcesz otwierać portów innych niż port 80 i port 443 w zaporze ze względu na firmowe zasady IT.** Na przykład podczas kopiowania danych z lokalnego magazynu danych ujścia Azure SQL Database lub ujścia Azure SQL Data Warehouse, musisz aktywować komunikacja wychodząca TCP na porcie 1433 dla zapory Windows i zaporą firmową. W tym scenariuszu kopia przygotowana może korzystać z własnego środowiska Integration Runtime, aby najpierw skopiować dane do wystąpienia tymczasowego magazynu obiektów BLOB za pośrednictwem protokołu HTTP lub HTTPS na porcie 443. Następnie może załadować dane do SQL Database lub SQL Data Warehouse z przemieszczania magazynu obiektów BLOB. W tym przepływie nie trzeba włączyć port 1433.

#### <a name="how-staged-copy-works"></a>Jak przygotowanych działania kopiowania

Po aktywowaniu funkcji przemieszczania najpierw jest skopiowanie danych z magazynu danych źródłowych do przejściowego magazynu obiektów Blob (skorzystaj z własnych). Następnie dane są kopiowane z przejściowy magazyn danych do magazynu danych ujścia. Azure Data Factory automatycznie zarządza przepływem dwuetapowym. Azure Data Factory również czyści dane tymczasowe z magazynu tymczasowego po zakończeniu przenoszenia danych.

![Kopiowania przejściowego](media/copy-activity-performance/staged-copy.png)

W przypadku aktywowania przenoszenia danych przy użyciu magazynu przemieszczania można określić, czy dane mają być kompresowane przed przeniesieniem danych ze źródłowego magazynu danych do tymczasowego lub przejściowego magazynu danych, a następnie zdekompresować przed przeniesieniem danych z tymczasowego lub przejściowego dat. Magazyn do magazynu danych ujścia.

Obecnie nie można kopiować danych między dwoma magazynami danych, które są połączone za pośrednictwem różnych urzędów certyfikacji samodzielnych, ani z kopią etapową lub bez niej. W tym scenariuszu można skonfigurować dwa jawne działanie kopiowania w łańcuchu w celu skopiowania danych ze źródła do przemieszczania z miejsca przejściowego do ujścia.

#### <a name="configuration"></a>Konfigurowanie

Skonfiguruj ustawienie **enableStaging** w działaniu kopiowania, aby określić, czy dane mają zostać przygotowane w magazynie obiektów BLOB przed załadowaniem ich do docelowego magazynu danych. Po ustawieniu **enableStaging** na `TRUE`należy określić dodatkowe właściwości wymienione w poniższej tabeli. Należy również utworzyć usługę Azure Storage lub usługi połączonej sygnatury dostępu współdzielonego na potrzeby przemieszczania, jeśli nie istnieje.

| Właściwość | Opis | Wartość domyślna | Wymagane |
| --- | --- | --- | --- |
| enableStaging |Określ, czy chcesz skopiować dane za pośrednictwem tymczasowego magazynu przejściowego. |Fałsz |Nie |
| linkedServiceName |Określ nazwę [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) połączone usługi, która odnosi się do wystąpienia magazynu, którego używasz jako tymczasowy magazyn przejściowy. <br/><br/> Nie można użyć magazynu z sygnaturą dostępu współdzielonego w celu załadowania danych do SQL Data Warehouse za pośrednictwem bazy. Można go użyć w innych scenariuszach. |ND |Tak, gdy **enableStaging** jest ustawiona na wartość TRUE |
| Ścieżka |Określ ścieżkę magazynu obiektów Blob, która ma zawierać użycia przemieszczonych danych. Jeśli nie podano ścieżki, usługa tworzy kontener do przechowywania danych tymczasowych. <br/><br/> Określ ścieżkę, tylko wtedy, gdy używasz magazynu przy użyciu sygnatury dostępu współdzielonego lub wymagają danych tymczasowych w określonej lokalizacji. |ND |Nie |
| Ustawieniem EnableCompression |Określa, czy dane mają być kompresowane przed skopiowaniem do lokalizacji docelowej. To ustawienie powoduje zmniejszenie ilości przesyłanych danych. |Fałsz |Nie |

>[!NOTE]
> W przypadku użycia kopiowania etapowego z włączoną kompresją usługa główna lub uwierzytelnianie MSI dla połączonej usługi obiektów BLOB są nieobsługiwane.

Oto przykładowa definicja działania kopiowania z właściwościami, które są opisane w powyższej tabeli:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

#### <a name="staged-copy-billing-impact"></a>Wpływ na rozliczenia kopiowania etapowego

Opłata jest naliczana na podstawie dwóch kroków: Kopiuj czas trwania i typ kopiowania.

* W przypadku korzystania z przemieszczania podczas kopiowania w chmurze, który kopiuje dane z magazynu danych w chmurze do innego magazynu danych w chmurze, to oba etapy, które są uprawnione przez środowisko uruchomieniowe Azure Integration Runtime, są obciążani [łączny czas trwania kopiowania dla kroku 1 i 2] x [cena jednostkowa kopiowania w chmurze].
* W przypadku korzystania z przemieszczania w ramach kopii hybrydowej, która polega na kopiowaniu danych z lokalnego magazynu danych do magazynu danych w chmurze, jednym etapem nieobsługiwanym przez środowisko Integration Runtime jest naliczana opłata za [okres kopiowania hybrydowego] x [cena jednostki kopiowania hybrydowego] + [czas trwania kopiowania w chmurze] x [cena jednostkowa kopiowania w chmurze].

## <a name="references"></a>Informacje

Poniżej znajdują się informacje dotyczące monitorowania wydajności i dostrajania dla niektórych obsługiwanych magazynów danych:

* Usługa Azure Storage, która obejmuje usługi BLOB Storage i Table Storage: [elementy docelowe skalowalności usługi Azure Storage](../storage/common/storage-scalability-targets.md) oraz [listę kontrolną wydajności i skalowalności usługi Azure Storage](../storage/common/storage-performance-checklist.md).
* Azure SQL Database: można [monitorować wydajność](../sql-database/sql-database-single-database-monitor.md) i sprawdzać wartość procentową jednostki transakcji bazy danych (DTU).
* Azure SQL Data Warehouse: jej możliwości są mierzone w jednostkach magazynu danych (jednostek dwu). Zobacz [zarządzanie mocą obliczeniową w Azure SQL Data Warehouse (omówienie)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [poziomy wydajności w Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server lokalnego: [monitorowanie i dostrajanie wydajności](https://msdn.microsoft.com/library/ms189081.aspx).
* Lokalny serwer plików: [dostrajanie wydajności dla serwerów plików](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi artykułami dotyczącymi działania kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Używanie Azure Data Factory do migrowania danych z usługi Data Lake lub magazynu danych na platformę Azure](data-migration-guidance-overview.md)
- [Migrowanie danych z usług Amazon S3 do usługi Azure Storage](data-migration-guidance-s3-azure-storage.md)
