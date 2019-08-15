---
title: Przewodnik dotyczący wydajności i dostrajania działania kopiowania w Azure Data Factory | Microsoft Docs
description: Zapoznaj się z najważniejszymi czynnikami wpływającymi na wydajność przenoszenia danych w Azure Data Factory podczas korzystania z działania kopiowania.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: d8ce0a4f6bacdd1c8c858d474e6f3957a23c6357
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967360"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Przewodnik dotyczący wydajności i dostrajania działania kopiowania
> [!div class="op_single_selector" title1="Wybierz używaną wersję Azure Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-activity-performance.md)
> * [Bieżąca wersja](copy-activity-performance.md)


Działanie kopiowania Azure Data Factory zapewnia pierwszą klasę rozwiązanie bezpiecznego i niezawodnego ładowania danych o wysokiej wydajności. Można jej używać do kopiowania dziesiątek danych każdego dnia w wielu różnych magazynach danych w chmurze i lokalnych. Szybka wydajność ładowania danych to klucz, aby zapewnić możliwość skoncentrowania się na podstawowym problemie z danymi Big Data: tworzenie zaawansowanych rozwiązań analitycznych i uzyskiwanie szczegółowych informacji ze wszystkich tych danych.

Platforma Azure udostępnia zestaw rozwiązań magazynu danych klasy korporacyjnej i magazynów danych. Działanie kopiowania oferuje wysoce zoptymalizowane środowisko ładowania danych, które jest łatwe do skonfigurowania i skonfigurowania. Za pomocą pojedynczego działania kopiowania można załadować dane do:

* Azure SQL Data Warehouse o 1,2 GB/s.
* Magazyn obiektów blob platformy Azure o 1,0 GB/s.
* Azure Data Lake Store o 1,0 GB/s.

W tym artykule opisano:

* [Numery odwołań wydajności](#performance-reference) dla obsługiwanych magazynów danych źródłowych i ujścia, które ułatwiają planowanie projektu.
* Funkcje, które mogą zwiększyć przepływność kopiowania w różnych scenariuszach, w tym [jednostki integracji danych](#data-integration-units) (DIUs), [kopie równoległe](#parallel-copy)i [kopie etapowe](#staged-copy).
* [Wskazówki dotyczące dostrajania](#performance-tuning-steps) wydajności dotyczące dostrajania wydajności i kluczowych czynników, które mogą mieć wpływ na wydajność kopiowania.

> [!NOTE]
> Jeśli nie znasz ogólnie działania kopiowania, przed przeczytaniem tego artykułu zapoznaj się z [omówieniem działania kopiowania](copy-activity-overview.md) .
>

## <a name="performance-reference"></a>Informacje dotyczące wydajności

W poniższej tabeli przedstawiono numer przepływności kopiowania w MB/s dla danej pary źródłowej i ujścia w pojedynczym działaniu kopiowania na podstawie testów wewnętrznych. W celu przeprowadzenia porównania pokazano również, jak różne ustawienia [jednostek integracji danych](#data-integration-units) lub samodzielna [skalowalność środowiska Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime) (wiele węzłów) mogą pomóc w wydajności kopiowania.

![Macierz wydajności](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> Gdy działanie kopiowania działa w środowisku uruchomieniowym platformy Azure, minimalna dozwolona jednostka integracji danych (znana wcześniej jako jednostki przenoszenia danych) to dwie. Jeśli nie zostanie określony, należy zapoznać się z domyślnymi jednostkami integracji danych używanymi w [jednostkach integracji danych](#data-integration-units).

**Punkty do uwagi:**

* Przepływność jest obliczana przy użyciu następującej formuły: [rozmiar danych odczytanych ze źródła]/[czas trwania uruchomienia działania kopiowania].
* Numery odwołań wydajności w tabeli były mierzone przy użyciu zestawu danych [TPC-H](http://www.tpc.org/tpch/) w jednym przebiegu działania kopiowania. Pliki testowe dla magazynów opartych na plikach to wiele plików o rozmiarze 10 GB.
* W magazynach danych platformy Azure źródła i ujścia znajdują się w tym samym regionie platformy Azure.
* W przypadku kopii hybrydowej między lokalnymi i magazynami danych w chmurze każdy węzeł środowiska Integration Runtime został uruchomiony na komputerze, który był oddzielony od magazynu danych, przy użyciu następującej specyfikacji. Gdy jedno działanie została uruchomiona, operacji kopiowania wykorzystywane tylko niewielką część procesora CPU, pamięć lub przepustowość sieci testowej maszyny.
    <table>
    <tr>
        <td>Procesor CPU</td>
        <td>32 rdzenie 2,20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Memory (Pamięć)</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Sieć</td>
        <td>Interfejs internetowy: 10 GB/s; Interfejs intranetowy: 40 GB/s</td>
    </tr>
    </table>


> [!TIP]
> Większą przepływność można osiągnąć przy użyciu więcej DIUs. Na przykład w przypadku 100 DIUs można skopiować dane z usługi Azure Blob Storage do Azure Data Lake Store z 1,0 GB/s. Aby uzyskać więcej informacji na temat tej funkcji i obsługiwanego scenariusza, zobacz sekcję [jednostki integracji danych](#data-integration-units) . 

## <a name="data-integration-units"></a>Jednostki integracji danych

Jednostka integracji danych to miara, która reprezentuje moc (kombinację procesora CPU, pamięci i alokacji zasobów sieciowych) pojedynczej jednostki w Azure Data Factory. Jednostka integracji danych ma zastosowanie tylko do [środowiska Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime), ale nie do [własnego środowiska Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

Minimalny DIUs do upoważnienia do uruchomienia działania kopiowania to dwa. Jeśli nie zostanie określony, w poniższej tabeli wymieniono DIUs domyślne używane w scenariuszach różnych kopii:

| Skopiuj scenariusza | Domyślne DIUs określany przez usługę |
|:--- |:--- |
| Kopiowanie danych między magazynami oparte na plikach | Od 4 do 32 w zależności od liczby i rozmiaru plików |
| Kopiuj dane do Azure SQL Database lub Azure Cosmos DB |Od 4 do 16 w zależności od warstwy Azure SQL Database ujścia lub Cosmos DB (liczba DTU/jednostek ru) |
| Wszystkie inne scenariusze kopiowania | 4 |

Aby zastąpić to ustawienie domyślne, należy określić wartość dla **dataIntegrationUnits** właściwości w następujący sposób. *Dozwolone wartości* właściwości **dataIntegrationUnits** to 256. *Rzeczywista liczba DIUs* używany w czasie wykonywania operacji kopiowania jest równa lub mniejsza niż skonfigurowana wartość, w zależności od wzorca usługi danych. Aby uzyskać informacje na temat poziomu wydajności, może zostać wyświetlony po skonfigurowaniu większej liczby jednostek dla określonej kopii źródła i ujścia, zobacz [dotyczące wydajności](#performance-reference).

Można zobaczyć DIUs używane dla każdej kopii przebiegu w danych wyjściowych działania kopiowania podczas monitorowania uruchomienia działania. Aby uzyskać więcej informacji, zobacz [monitorowanie aktywności kopiowania](copy-activity-overview.md#monitoring).

> [!NOTE]
> Ustawienie DIUs o rozmiarze większym niż cztery jest obecnie stosowane tylko w przypadku kopiowania wielu plików z usługi Azure Storage, Azure Data Lake Storage, Amazon S3, Google Cloud Storage, Cloud FTP lub SFTP w chmurze do innych magazynów danych w chmurze.
>

**Przykład**

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

### <a name="data-integration-units-billing-impact"></a>Wpływ rozliczeniowym jednostki integracji danych

Należy pamiętać, że opłata jest naliczana na podstawie łącznego czasu operacji kopiowania. Łączny czas trwania naliczania na przemieszczenie danych to suma czasu trwania w DIUs. Jeśli zadanie kopiowania używany do podejmowania jedną godzinę od dwóch jednostek chmury, a obecnie zajmuje 15 minut przy użyciu osiem jednostek chmury, ogólną kwotę rachunku pozostanie prawie taki sam.

## <a name="parallel-copy"></a>Kopiowanie równoległe

Możesz użyć właściwości **parallelCopies** , aby wskazać równoległość, która ma być używana przez działanie kopiowania. Tę właściwość można traktować jako maksymalną liczbę wątków w ramach działania kopiowania, które mogą być jednocześnie odczytywane ze źródła lub zapisywać do magazynów danych ujścia.

Dla każdego przebiegu działania kopiowania Azure Data Factory określa liczbę kopii równoległych, które mają być używane do kopiowania danych ze źródłowego magazynu danych do docelowego magazynu danych. Domyślna liczba kopii równoległych, których używa, zależy od typu używanego źródła i ujścia.

| Skopiuj scenariusza | Domyślna liczba równoległych kopii określany przez usługę |
| --- | --- |
| Kopiowanie danych między magazynami oparte na plikach |Zależy od rozmiaru plików i liczby DIUs używanych do kopiowania danych między dwoma magazynami danych w chmurze lub konfiguracją fizyczną środowiska Integration Runtime. |
| Kopiowanie danych z dowolnego magazynu źródłowego do usługi Azure Table Storage |4 |
| Innych scenariuszach kopiowania |1 |

> [!TIP]
> Podczas kopiowania danych między magazynami opartymi na plikach, domyślne zachowanie zwykle zapewnia najlepszą przepływność. Domyślne zachowanie jest określane na podstawie wzorca pliku źródłowego.

Aby kontrolować obciążenie maszyn, które obsługują magazyny danych, lub dostosować wydajność kopiowania, można zastąpić wartość domyślną i określić wartość właściwości **parallelCopies** . Wartość musi być liczba całkowita większa lub równa 1. W czasie wykonywania w celu uzyskania najlepszej wydajności działanie kopiowania używa wartości, która jest mniejsza lub równa ustawionej wartości.

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

**Punkty do uwagi:**

* Podczas kopiowania danych między magazynami opartymi na plikach **parallelCopies** określa równoległość na poziomie pliku. Fragmentowanie w pojedynczym pliku odbywa się automatycznie i w sposób przezroczysty. Zaprojektowano w celu użycia najlepszego odpowiedniego rozmiaru fragmentu dla danego typu magazynu danych źródłowych w celu załadowania danych równolegle i ortogonalnych do **parallelCopies**. Rzeczywista liczba równoległych kopii usługi data movement service używa dla operacji kopiowania w czasie wykonywania jest nie więcej niż liczba plików, których masz. Jeśli zachowanie kopiowania ma wartość **mergeFile**, działanie kopiowania nie może korzystać z równoległości na poziomie plików.
* W przypadku kopiowania danych z magazynów, które nie są oparte na plikach (z wyjątkiem programu [Oracle](connector-oracle.md#oracle-as-source), [Teradata](connector-teradata.md#teradata-as-source), [tabeli SAP](connector-sap-table.md#sap-table-as-source)i [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) Connector jako źródło z włączoną funkcją partycjonowania danych), do magazynów, które są oparte na plikach, Usługa przenoszenia danych ignoruje Właściwość **parallelCopies** . Nawet jeśli równoległości jest określona, nie zostanie zastosowane w tym przypadku.
* Właściwość **parallelCopies** jest prostopadła do **dataIntegrationUnits**. Pierwsza jest liczony we wszystkich jednostkach integracji danych.
* Po określeniu wartości właściwości **parallelCopies** należy wziąć pod uwagę wzrost obciążenia magazynów danych źródłowych i ujścia. Należy również rozważyć zwiększenie obciążenia do własnego środowiska Integration Runtime, jeśli działanie kopiowania jest przez niego uprawnione, na przykład w przypadku kopii hybrydowej. Ten wzrost obciążenia występuje szczególnie w przypadku wielu działań lub współbieżnych uruchomień tych samych działań, które działają w tym samym magazynie danych. Jeśli zauważysz, że magazyn danych lub własne środowisko Integration Runtime jest przeciążony, zmniejsz wartość **parallelCopies** , aby zwolnić obciążenie.

## <a name="staged-copy"></a>Kopiowania przejściowego

Podczas kopiowania danych z magazynu danych źródłowych do magazynu danych ujścia, można użyć magazynu obiektów Blob jako tymczasowego magazynu przejściowego. Przemieszczania jest szczególnie użyteczna w następujących przypadkach:

- **Chcesz pozyskać dane z różnych magazynów danych do SQL Data Warehouse za pośrednictwem bazy.** Usługa SQL Data Warehouse używa programu PolyBase jako mechanizm o wysokiej przepływności ładowanie dużej ilości danych do usługi SQL Data Warehouse. Dane źródłowe muszą znajdować się w magazynie obiektów blob lub Azure Data Lake Store i muszą spełniać dodatkowe kryteria. Podczas ładowania danych z magazynem danych innych niż usługi Blob storage lub Azure Data Lake Store, możesz aktywować dane kopiowanie za pośrednictwem tymczasowego przejściowego magazynu obiektów Blob. W takim przypadku Azure Data Factory wykonuje wymagane przekształcenia danych, aby upewnić się, że spełnia on wymagania bazy. Następnie używa programu PolyBase do ładowania danych do usługi SQL Data Warehouse wydajnie. Aby uzyskać więcej informacji, zobacz [przy użyciu technologii PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Czasami trwa przeprowadzenie hybrydowego przenoszenia danych (czyli kopiowania z lokalnego magazynu danych do magazynu danych w chmurze) przez wolne połączenie sieciowe.** Aby zwiększyć wydajność, można użyć kopii przygotowanej do skompresowania danych w środowisku lokalnym, co pozwala na przenoszenie danych do tymczasowego magazynu danych w chmurze. Następnie można zdekompresować dane w magazynie przemieszczania przed załadowaniem do docelowego magazynu danych.
- **Nie chcesz otwierać portów innych niż port 80 i port 443 w zaporze ze względu na firmowe zasady IT.** Na przykład podczas kopiowania danych z lokalnego magazynu danych ujścia Azure SQL Database lub ujścia Azure SQL Data Warehouse, musisz aktywować komunikacja wychodząca TCP na porcie 1433 dla zapory Windows i zaporą firmową. W tym scenariuszu kopia przygotowana może korzystać z własnego środowiska Integration Runtime, aby najpierw skopiować dane do wystąpienia tymczasowego magazynu obiektów BLOB za pośrednictwem protokołu HTTP lub HTTPS na porcie 443. Następnie może załadować dane do SQL Database lub SQL Data Warehouse z przemieszczania magazynu obiektów BLOB. W tym przepływie nie trzeba włączyć port 1433.

### <a name="how-staged-copy-works"></a>Jak przygotowanych działania kopiowania

Po aktywowaniu funkcji przemieszczania najpierw jest skopiowanie danych z magazynu danych źródłowych do przejściowego magazynu obiektów Blob (skorzystaj z własnych). Następnie dane są kopiowane z przejściowy magazyn danych do magazynu danych ujścia. Azure Data Factory automatycznie zarządza przepływem dwuetapowym. Azure Data Factory również czyści dane tymczasowe z magazynu tymczasowego po zakończeniu przenoszenia danych.

![Kopiowania przejściowego](media/copy-activity-performance/staged-copy.png)

W przypadku aktywowania przenoszenia danych przy użyciu magazynu przemieszczania można określić, czy dane mają być kompresowane przed przeniesieniem danych ze źródłowego magazynu danych do tymczasowego lub przejściowego magazynu danych, a następnie zdekompresować przed przeniesieniem danych z tymczasowego lub przejściowego dat. Magazyn do magazynu danych ujścia.

Obecnie nie można kopiować danych między dwoma magazynami danych, które są połączone za pośrednictwem różnych urzędów certyfikacji samodzielnych, ani z kopią etapową lub bez niej. W tym scenariuszu można skonfigurować dwa jawne działanie kopiowania w łańcuchu w celu skopiowania danych ze źródła do przemieszczania z miejsca przejściowego do ujścia.

### <a name="configuration"></a>Konfigurowanie

Skonfiguruj ustawienie **enableStaging** w działaniu kopiowania, aby określić, czy dane mają zostać przygotowane w magazynie obiektów BLOB przed załadowaniem ich do docelowego magazynu danych. Po ustawieniu **enableStaging** na `TRUE`, określ dodatkowe właściwości wymienione w poniższej tabeli. Należy również utworzyć usługę Azure Storage lub usługi połączonej sygnatury dostępu współdzielonego na potrzeby przemieszczania, jeśli nie istnieje.

| Właściwość | Opis | Wartość domyślna | Wymagane |
| --- | --- | --- | --- |
| enableStaging |Określ, czy chcesz skopiować dane za pośrednictwem tymczasowego magazynu przejściowego. |False |Nie |
| linkedServiceName |Określ nazwę [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) połączone usługi, która odnosi się do wystąpienia magazynu, którego używasz jako tymczasowy magazyn przejściowy. <br/><br/> Nie można użyć magazynu z sygnaturą dostępu współdzielonego w celu załadowania danych do SQL Data Warehouse za pośrednictwem bazy. Można go użyć w innych scenariuszach. |ND |Tak, gdy **enableStaging** jest ustawiona na wartość TRUE |
| path |Określ ścieżkę magazynu obiektów Blob, która ma zawierać użycia przemieszczonych danych. Jeśli nie podano ścieżki, usługa tworzy kontener do przechowywania danych tymczasowych. <br/><br/> Określ ścieżkę, tylko wtedy, gdy używasz magazynu przy użyciu sygnatury dostępu współdzielonego lub wymagają danych tymczasowych w określonej lokalizacji. |ND |Nie |
| Ustawieniem EnableCompression |Określa, czy dane mają być kompresowane przed skopiowaniem do lokalizacji docelowej. To ustawienie powoduje zmniejszenie ilości przesyłanych danych. |False |Nie |

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

### <a name="staged-copy-billing-impact"></a>Wpływ na rozliczenia kopiowania etapowego

Opłata jest naliczana na podstawie dwóch kroków: Kopiuj czas trwania i typ kopiowania.

* W przypadku korzystania z przemieszczania podczas kopiowania w chmurze, który kopiuje dane z magazynu danych w chmurze do innego magazynu danych w chmurze, to oba etapy, które są uprawnione przez środowisko uruchomieniowe Azure Integration Runtime, są obciążani [łączny czas trwania kopiowania dla kroku 1 i 2] x [cena jednostkowa kopiowania w chmurze].
* W przypadku korzystania z przemieszczania w ramach kopii hybrydowej, która polega na kopiowaniu danych z lokalnego magazynu danych do magazynu danych w chmurze, jednym etapem nieobsługiwanym przez środowisko Integration Runtime jest naliczana opłata za [okres kopiowania hybrydowego] x [cena jednostki kopiowania hybrydowego] + [czas trwania kopiowania w chmurze] x [cena jednostkowa kopiowania w chmurze].

## <a name="performance-tuning-steps"></a>Kroki dostosowywania wydajności

Wykonaj następujące kroki, aby dostroić wydajność usługi Azure Data Factory za pomocą działania kopiowania.

1. **Ustanów linię bazową.** Podczas fazy tworzenia Przetestuj potok za pomocą działania kopiowania względem reprezentatywnej próbki danych. Zbierz szczegóły wykonania i charakterystyki wydajności następujące po [monitorowaniu działania kopiowania](copy-activity-overview.md#monitoring).

2. **Diagnozuj i Optymalizuj wydajność.** Jeśli obserwowanie wydajności nie spełnia oczekiwań, zidentyfikuj wąskie gardła wydajności. Następnie zoptymalizować wydajność, aby usunąć lub zmniejszają efekt wąskich gardeł.

    W niektórych przypadkach po uruchomieniu działania kopiowania w Azure Data Factory w górnej części [strony monitorowania działania kopiowania](copy-activity-overview.md#monitor-visually)zostanie wyświetlony komunikat "porady dotyczące dostrajania wydajności", jak pokazano w poniższym przykładzie. Komunikat informuje o wąskim gardła, który został zidentyfikowany dla danego przebiegu kopiowania. Przedstawiono w nim również informacje o tym, co należy zmienić w celu zwiększenia przepływności kopiowania. Porady dotyczące dostrajania wydajności obecnie udostępniają sugestie, takie jak:

    - Użyj podstawy podczas kopiowania danych do Azure SQL Data Warehouse.
    - Zwiększ liczbę jednostek żądania Azure Cosmos DB lub Azure SQL Database DTU (jednostki przepływności bazy danych), gdy zasób po stronie magazynu danych to wąskie gardło.
    - Usuń niezbędną kopię przygotowaną.

    Zasady dostrajania wydajności zostaną również stopniowo wzbogacane.

    **Przykład: Kopiuj do Azure SQL Database ze wskazówkami dotyczącymi dostrajania wydajności**

    W tym przykładzie podczas przebiegu kopiowania Azure Data Factory zauważyć, Azure SQL Database ujścia osiągnie duże użycie jednostek DTU, co spowalnia operacje zapisu. Sugestią jest zwiększenie warstwy Azure SQL Databaseej o więcej DTU. 

    ![Kopiuj monitorowanie ze wskazówkami dotyczącymi dostrajania wydajności](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    Ponadto poniżej przedstawiono kilka typowych zagadnień. Pełny opis diagnostyki wydajności wykracza poza zakres tego artykułu.

   * Funkcje wydajności:
     * [Kopiuj równoległe](#parallel-copy)
     * [Jednostki integracji danych](#data-integration-units)
     * [Kopiowania przejściowego](#staged-copy)
     * [Samoobsługowa skalowalność środowiska Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Infrastruktura Integration Runtime (Self-hosted)](#considerations-for-self-hosted-integration-runtime)
   * [Element źródłowy](#considerations-for-the-source)
   * [obiekt sink](#considerations-for-the-sink)
   * [Serializacja i deserializacja](#considerations-for-serialization-and-deserialization)
   * [Kompresja](#considerations-for-compression)
   * [Mapowanie kolumn](#considerations-for-column-mapping)
   * [Inne zagadnienia](#other-considerations)

3. **Rozwiń konfigurację do całego zestawu danych.** Gdy wyniki wykonywania i wydajność są zadowalające, można rozwinąć definicję i potok, aby pokryć cały zestaw danych.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Zagadnienia dotyczące samodzielnego środowiska Integration Runtime

Jeśli działanie kopiowania działa w ramach własnego środowiska Integration Runtime, należy zwrócić uwagę na następujące kwestie:

**Konfiguracja**: Zalecamy używanie dedykowanego komputera do hostowania środowiska Integration Runtime. Zapoznaj się [z zagadnieniami dotyczącymi korzystania z własnego środowiska Integration Runtime](concepts-integration-runtime.md).

**Skalowanie w poziomie**: Pojedyncze logiczne środowisko Integration Runtime z co najmniej jednym węzłem może jednocześnie obsługiwać wiele operacji kopiowania jednocześnie. Jeśli istnieje duże zapotrzebowanie na hybrydowe przenoszenie danych przy użyciu dużej liczby równoczesnych operacji kopiowania lub dużej ilości danych do skopiowania, należy rozważyć [skalowanie środowiska Integration Runtime (własne środowisko](create-self-hosted-integration-runtime.md#high-availability-and-scalability) ) w celu udostępnienia większej ilości zasobów.

## <a name="considerations-for-the-source"></a>Zagadnienia dotyczące źródła

### <a name="general"></a>Ogólne

Upewnij się, że źródłowy magazyn danych nie jest przeciążony przez inne obciążenia, które są uruchomione w systemie lub.

Aby uzyskać informacje dotyczące magazynów danych firmy Microsoft, zobacz [Tematy dotyczące monitorowania i dostrajania](#performance-reference) , które są specyficzne dla magazynów danych. Te tematy mogą pomóc zrozumieć charakterystyki wydajności magazynu danych oraz jak zminimalizować czas reakcji i zmaksymalizowania wydajności.

* Jeśli skopiujesz dane z magazynu obiektów BLOB do SQL Data Warehouse, rozważ użycie bazy danych w celu zwiększenia wydajności. Aby uzyskać więcej informacji, zobacz [przy użyciu technologii PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* W przypadku kopiowania danych z systemu plików HDFS do usługi Azure Blob Storage lub Azure Data Lake Store należy rozważyć użycie pomocą distcp w celu zwiększenia wydajności. Aby uzyskać więcej informacji, zobacz [Używanie pomocą distcp do kopiowania danych z](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)systemu plików HDFS.
* W przypadku kopiowania danych z programu RedShift do Azure SQL Data Warehouse, Azure BLob Storage lub Azure Data Lake Store, rozważ użycie UNLOAD w celu zwiększenia wydajności. Aby uzyskać więcej informacji, zobacz [Używanie Unload do kopiowania danych z usługi Amazon RedShift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Magazyny danych opartych na plikach

* **Średni rozmiar pliku i liczba plików**: Działanie Copy (kopiowanie) przesyła dane jednocześnie. Przy użyciu tej samej ilości danych do przeniesienia ogólną przepustowość jest mniejszy, jeśli dane składa się z wielu małych plików, a nie kilka dużych plików z powodu fazie uruchamiania dla każdego pliku. Jeśli to możliwe, Połącz małe pliki z większymi plikami, aby uzyskać większą przepływność.
* **Format pliku i kompresja**: Aby uzyskać więcej informacji na temat poprawy wydajności, zobacz [zagadnienia dotyczące serializacji i deserializacji](#considerations-for-serialization-and-deserialization) oraz zagadnienia dotyczące [kompresji](#considerations-for-compression) .

### <a name="relational-data-stores"></a>Magazyny danych relacyjnych

* **Wzorzec danych**: Schemat tabeli ma wpływ na przepływność kopiowania. Duży rozmiar wiersza zapewnia lepszą wydajność niż mały rozmiar wiersza w celu skopiowania tej samej ilości danych. Przyczyną jest to, że baza danych wydajniej można pobrać mniejszej liczby partii danych, które zawierają mniej wierszy.
* **Zapytanie lub procedura składowana**: Zoptymalizuj logikę zapytania lub procedury składowanej, którą określisz w źródle działania kopiowania, aby efektywniej pobierać dane.

## <a name="considerations-for-the-sink"></a>Zagadnienia dotyczące ujścia

### <a name="general"></a>Ogólne

Upewnij się, że źródłowy magazyn danych nie jest przeciążony przez inne obciążenia, które są uruchomione w systemie lub.

Aby uzyskać informacje dotyczące magazynów danych firmy Microsoft, zobacz [Tematy dotyczące monitorowania i dostrajania](#performance-reference) , które są specyficzne dla magazynów danych. Te tematy mogą pomóc zrozumieć charakterystyki wydajności magazynu danych oraz jak zminimalizować czas reakcji i zmaksymalizowania wydajności.

* W przypadku kopiowania danych z dowolnego magazynu danych do Azure SQL Data Warehouse należy rozważyć użycie podstawy, aby zwiększyć wydajność. Aby uzyskać więcej informacji, zobacz [przy użyciu technologii PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* W przypadku kopiowania danych z systemu plików HDFS do usługi Azure Blob Storage lub Azure Data Lake Store należy rozważyć użycie pomocą distcp w celu zwiększenia wydajności. Aby uzyskać więcej informacji, zobacz [Używanie pomocą distcp do kopiowania danych z](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)systemu plików HDFS.
* W przypadku kopiowania danych z programu RedShift do Azure SQL Data Warehouse, Azure Blob Storage lub Azure Data Lake Store, rozważ użycie UNLOAD w celu zwiększenia wydajności. Aby uzyskać więcej informacji, zobacz [Używanie Unload do kopiowania danych z usługi Amazon RedShift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Magazyny danych opartych na plikach

* **Skopiuj zachowanie**: W przypadku kopiowania danych z innego magazynu danych opartego na plikach działanie kopiowania ma trzy opcje za pośrednictwem właściwości **copyBehavior** . Ją zachowuje hierarchię, spłaszcza hierarchii lub scala plików. Zachowywanie albo spłaszczanie hierarchii ma niewielki lub zmniejszenie wydajności, ale scalanie plików powoduje zmniejszenie wydajności zwiększyć.
* **Format pliku i kompresja**: Aby uzyskać więcej informacji na temat poprawy wydajności, zobacz [zagadnienia dotyczące serializacji i deserializacji](#considerations-for-serialization-and-deserialization) oraz zagadnienia dotyczące [kompresji](#considerations-for-compression) .

### <a name="relational-data-stores"></a>Magazyny danych relacyjnych

* **Zachowanie kopiowania i skuteczność wydajności**: Istnieją różne sposoby zapisywania danych w ujściach SQL. Dowiedz się więcej z [najlepszych rozwiązań dotyczących ładowania danych do Azure SQL Database](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database).

* **Rozmiar danych wzorca i batch**:
  * Schemat tabeli ma wpływ na przepływność kopiowania. Aby skopiować podobną ilość danych, rozmiaru duży wiersz zapewnia lepszą wydajność niż rozmiar wiersza małych ponieważ bazy danych można efektywniej zatwierdzić mniejszej liczby partii danych.
  * Działanie kopiowania wstawia dane w szeregu partii. Możesz ustawić liczbę wierszy w zadaniu wsadowym, używając **writeBatchSize** właściwości. Jeśli dane zawierają małe wiersze, możesz ustawić **writeBatchSize** właściwość o wyższej wartości do korzystania z mniejszy narzut partii i wyższej przepływności. Jeśli rozmiar wiersza danych jest duży, należy zachować ostrożność, wraz ze zwiększeniem **writeBatchSize**. O wysokiej wartości może prowadzić do awarii kopiowania spowodowane przeciążeniem bazy danych.

### <a name="nosql-stores"></a>Magazynów NoSQL

* Aby uzyskać **Table storage**:
  * **Partycja**: Zapisywanie danych na partycjach z przeplotem znacznie zmniejsza wydajność. Posortuj dane źródłowe według klucza partycji, aby dane były wstawiane efektywnie do jednej partycji. Można też dostosować logikę, aby zapisać dane w jednej partycji.

## <a name="considerations-for-serialization-and-deserialization"></a>Zagadnienia dotyczące serializacji i deserializacji

Serializacja i deserializacja może wystąpić, gdy wejściowy zestaw danych lub wyjściowy zestaw danych jest plikiem. Aby uzyskać więcej informacji na temat obsługiwanych formatów plików przez działanie kopiowania, zobacz [obsługiwane formaty plików i kompresji](supported-file-formats-and-compression-codecs.md).

**Skopiuj zachowanie**:

* Kopiowanie plików między magazynami danych opartych na plikach:
  * Gdy wejściowe i wyjściowe zestawy danych mają takie same ustawienia formatu plików, Usługa przenoszenia danych wykonuje kopię binarną bez serializacji lub deserializacji. Zostanie wyświetlony wyższej przepustowości w porównaniu do scenariusza, w której ustawienia formatu pliku źródła i ujścia różnią się od siebie nawzajem.
  * Gdy wejściowe i wyjściowe zestawy danych są w formacie tekstowym, a tylko typ kodowania jest inny, Usługa przenoszenia danych wykonuje tylko konwersję kodowania. Nie wszystkie serializacji i deserializacji, co powoduje, że niektóre wydajności, obciążenie w porównaniu do kopia binarna.
  * Jeśli wejściowe i wyjściowe zestawy danych mają różne formaty plików lub różne konfiguracje, takie jak ograniczniki, Usługa przenoszenia danych deserializacji dane źródłowe w celu przesyłania strumieniowego, przekształcania i serializacji go do wskazanego formatu danych wyjściowych. Ta operacja powoduje znacznie bardziej znaczące wydajności, obciążenie w porównaniu do innych scenariuszy.
* Podczas kopiowania plików do lub z magazynu danych, który nie jest oparty na pliku, na przykład z magazynu opartego na plikach do magazynu relacyjnego, wymagany jest krok serializacji lub deserializacji. Ten krok powoduje znaczne obciążenie.

**Format pliku**: Wybrany format pliku może mieć wpływ na wydajność kopiowania. Na przykład Avro jest kompaktowego formatu binarnego, który przechowuje metadane z danymi. Posiada obsługi szerokiej gamy w ekosystemie usługi Hadoop do przetwarzania i wykonywania zapytań. Avro jest droższa do serializacji i deserializacji, co skutkuje mniejszą przepływność kopiowania w porównaniu z formatem tekstowym. 

Wybierz ustawienia formatu pliku w całym przepływie przetwarzania całościowo. Zacznij od:

- Zawartość, w której są przechowywane dane, magazyny danych źródłowych lub do wyodrębnienia z systemów zewnętrznych.
- Najlepszy format magazynu, przetwarzania analitycznego i wykonywania zapytań.
- W jakim formacie dane mają zostać wyeksportowane do składnic danych w celu raportowania i narzędzi do wizualizacji.

Czasami formatu pliku nieoptymalne do odczytu i zapisu wydajność może być dobrym rozwiązaniem, gdy należy wziąć pod uwagę całkowity analitycznych procesu.

## <a name="considerations-for-compression"></a>Zagadnienia dotyczące kompresji

Gdy zestaw danych wejściowych lub wyjściowych jest plikiem, można ustawić działanie kopiowania w celu przeprowadzenia kompresji lub dekompresji w miarę zapisywania danych w miejscu docelowym. Po wybraniu kompresji, upewnij się zależność między wejścia/wyjścia (We/Wy) i procesora CPU. Kompresowanie danych koszty dodatkowe zasoby obliczeniowe. Ale w zamian zmniejsza we/wy sieci i magazynu. W zależności od danych można zobaczyć zwiększenie ogólnej przepływności kopiowania.

**Koder-dekoder**: Każdy koder-dekoder kompresji ma zalety. Na przykład bzip2 ma najniższą przepływności kopiowania, ale uzyskać najlepszą wydajność zapytań Hive przy użyciu bzip2, ponieważ podzielić ją do przetworzenia. Gzip to najbardziej zrównoważona opcja i jest używana najczęściej. Wybierz koder-dekoder, który najlepiej odpowiada Twojemu scenariuszowi end-to-end.

**Poziom**: Dla każdego kodera-dekoder kompresji można wybrać jedną z dwóch opcji: najszybszy skompresowany i optymalnie skompresowany. Opcja najszybsza skompresowana kompresuje dane tak szybko, jak to możliwe, nawet jeśli plik nie jest optymalnie kompresowany. Opcja optymalnie skompresowany zużywa więcej czasu na kompresji i daje minimalnej ilości danych. Możesz przetestować obie opcje, aby zobaczyć, który zapewnia lepszą wydajność ogólną w Twoim przypadku.

**Uwaga**: Aby skopiować dużą ilość danych między magazynem lokalnym i chmurą, należy rozważyć użycie [przygotowanej kopii](#staged-copy) z włączoną kompresją. Korzystanie z magazynu tymczasowego jest przydatne, gdy przepustowość sieci firmowej i usług platformy Azure jest czynnikiem ograniczającym, i chcesz, aby wejściowy zestaw danych i wyjściowy zestaw danych były w postaci nieskompresowanej.

## <a name="considerations-for-column-mapping"></a>Informacje dotyczące mapowania kolumn

Można ustawić właściwość **ColumnMappings** w działaniu kopiowania, aby zamapować wszystkie lub podzbiór kolumn wejściowych na kolumny wyjściowe. Po usługi data movement service odczytuje dane ze źródła, trzeba wykonać mapowania kolumn na danych, zanim go zapisuje dane do ujścia. To dodatkowe przetwarzanie zmniejsza przepustowość kopiowania.

W przypadku odpytywalny magazynie danych źródła, na przykład, jeśli magazynu relacyjnego, takich jak bazy danych SQL Database lub SQL Server, czy jest magazynu NoSQL, takie jak usługi Table storage lub Azure Cosmos DB, należy wziąć pod uwagę wypychanie filtrowanie kolumn i zmianę kolejności logikę w celu **zapytania** właściwości zamiast mapowania kolumn. W ten sposób rzutowanie występuje, gdy usługa przenoszenia danych odczytuje dane ze źródłowego magazynu danych, co jest znacznie bardziej wydajne.

Dowiedz się więcej z [mapowania schematu działania kopiowania](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Inne zagadnienia

Jeśli rozmiar danych, które mają zostać skopiowane, jest duży, można dostosować logikę biznesową w celu dalszej partycjonowania danych. Działanie kopiowania można zaplanować częściej, aby zmniejszyć rozmiar danych dla każdego działania kopiowania, które działa.

Należy zachować ostrożność dotyczącą liczby zestawów danych i działań kopiowania, które wymagają Azure Data Factory do łączenia się z tym samym magazynem danych w tym samym czasie. Wiele zadań jednoczesnych kopii może ograniczać do przechowywania danych i spowodować pogorszenie wydajności, kopii zadania wewnętrzne ponownych prób, a w niektórych przypadkach niepowodzenia wykonywania.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Przykładowy scenariusz: Kopiowanie z lokalnego programu SQL Server do magazynu obiektów BLOB

**Scenariusz**: Potok został utworzony w celu skopiowania danych z lokalnego programu SQL Server do magazynu obiektów BLOB w formacie CSV. Aby przyspieszyć zadanie kopiowania, pliki CSV należy skompresowane do formatu bzip2.

**Testowanie i analiza**: Przepływność działania kopiowania jest mniejsza niż 2 MB/s, czyli znacznie wolniejsze niż wynikowy test wydajności.

**Analiza wydajności i dostrajanie**: Aby rozwiązać problem z wydajnością, przyjrzyjmy się, jak dane są przetwarzane i przenoszone.

- **Odczytaj dane**: Środowisko Integration Runtime otwiera połączenie do SQL Server i wysyła zapytanie. SQL Server reaguje, wysyłając strumień danych do środowiska Integration Runtime za pośrednictwem intranetu.
- **Serializowanie i kompresowanie danych**: Środowisko Integration Runtime serializować strumień danych do formatu CSV i kompresuje dane do strumienia bzip2.
- **Zapisz dane**: Środowisko Integration Runtime przekazuje strumień bzip2 do magazynu obiektów BLOB za pośrednictwem Internetu.

Jak widać, dane są przetwarzane i przenoszone w sposób sekwencyjny przesyłania strumieniowego: SQL Server > LAN > Integration Runtime > sieci WAN > BLOB Storage. Ogólna wydajność jest planowana przez minimalną przepływność w potoku.

![Przepływ danych](./media/copy-activity-performance/case-study-pic-1.png)

Co najmniej jeden z następujących czynników może spowodować wąskie gardło:

* **Źródło**: Sam SQL Server ma niską przepływność ze względu na duże obciążenia.
* **Własne środowisko Integration Runtime**:
  * **SIEĆ LAN**: Środowisko Integration Runtime znajduje się daleko od maszyny SQL Server i ma połączenie o niskiej przepustowości.
  * **Środowisko Integration Runtime**: Środowisko Integration Runtime osiągnęło swoje ograniczenia dotyczące obciążenia, aby wykonać następujące operacje:
    * **Serializacja**: Serializowanie strumienia danych do formatu CSV ma niską przepływność.
    * **Kompresja**: Wybrano długi koder-dekoder kompresji, na przykład bzip2, czyli 2,8 MB/s z rdzeniem Core i7.
  * **SIEĆ WAN**: Przepustowość między siecią firmową i usługami platformy Azure jest niska, na przykład T1 = 1 544 KB/s; T2 = 6 312 KB/s.
* **Ujścia**: Magazyn obiektów BLOB ma niską przepływność. Ten scenariusz jest mało prawdopodobne, ponieważ jego umowa dotycząca poziomu usług (SLA) gwarantuje minimalną 60 MB/s.

W tym przypadku bzip2 kompresji danych może być spowalniania cały potok. Przełączanie do kodera-dekodera kompresji gzip może jej obsługi ułatwiają realizację tego wąskiego gardła.

## <a name="references"></a>Odwołania

Poniżej znajdują się informacje dotyczące monitorowania wydajności i dostrajania dla niektórych obsługiwanych magazynów danych:

* Usługa Azure Storage, która obejmuje magazyn obiektów blob i magazyn tabel: [Elementy docelowe skalowalności usługi Azure Storage](../storage/common/storage-scalability-targets.md) i [Lista kontrolna wydajności i skalowalności usługi Azure Storage](../storage/common/storage-performance-checklist.md).
* Azure SQL Database: Możesz [monitorować wydajność](../sql-database/sql-database-single-database-monitor.md) i sprawdzać wartość procentową jednostki transakcji bazy danych (DTU).
* Azure SQL Data Warehouse: Jego możliwości są mierzone w jednostkach magazynu danych (jednostek dwu). Zobacz [zarządzanie mocą obliczeniową w Azure SQL Data Warehouse (omówienie)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Usługa Azure Cosmos DB [Poziomy wydajności w Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server lokalna: [Monitorowanie i dostrajanie wydajności](https://msdn.microsoft.com/library/ms189081.aspx).
* Lokalny serwer plików: [Dostrajanie wydajności dla serwerów plików](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi artykułami dotyczącymi działania kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Mapowanie schematu działania kopiowania](copy-activity-schema-and-type-mapping.md)
- [Kopiuj działania odporności na uszkodzenia](copy-activity-fault-tolerance.md)
