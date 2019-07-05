---
title: Skopiuj dostrajania przewodnik dotyczący wydajności działania i w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat kluczowych czynników wpływających na wydajność przenoszenia danych w usłudze Azure Data Factory, korzystając z działania kopiowania.
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
ms.openlocfilehash: face3719f32ccb44e7479150e94417496141f90b
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509559"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Skopiuj wydajności i działania przewodnika dostrajania
> [!div class="op_single_selector" title1="Wybierz wersję usługi Azure Data Factory, której używasz:"]
> * [Wersja 1](v1/data-factory-copy-activity-performance.md)
> * [Bieżąca wersja](copy-activity-performance.md)


Działanie kopiowania w usłudze Azure Data Factory zapewnia najwyższej jakości danych bezpieczny, niezawodny i wydajny ładowania rozwiązania. Służy on do kopiowania dziesiątki terabajtów danych każdego dnia w wielu różnych magazynów danych w chmurze i lokalnych. Szybkie ładowanie danych wydajności ma kluczowe znaczenie dla zapewnienia, że możesz skupić się na problemie danych big data core: tworzenie zaawansowanych rozwiązań analizy i uzyskiwanie szczegółowych informacji z wszystkie te dane.

System Azure oferuje zestaw przeznaczonych dla przedsiębiorstw w rozwiązaniach magazynu danych magazynu i danych. Działanie kopiowania oferuje wysoce zoptymalizowane ładowania środowiska, który jest łatwy do skonfigurowania i danych. Za pomocą działania kopiowania pojedynczego należy załadować dane do:

* Usługi Azure SQL Data Warehouse z szybkością 1,2 GB/s.
* Usługa Azure Blob storage 1,0 GB/s.
* Azure Data Lake Store na 1,0 GB/s.

W tym artykule opisano:

* [Numery identyfikacyjne wydajności](#performance-reference) obsługiwane źródło i ujście magazynu danych, aby pomóc Ci zaplanować projekt.
* Funkcje, które może zwiększyć przepływność kopii w różnych scenariuszach, w tym [jednostek integracji danych](#data-integration-units) (DIUs) [równoległych kopii](#parallel-copy), i [kopiowania etapowego](#staged-copy).
* [Wskazówki dotyczące dostrajania wydajności](#performance-tuning-steps) dotyczące dostrajania wydajności i kluczowych czynników, które mogą wpłynąć na wydajność kopii.

> [!NOTE]
> Jeśli nie znasz działania kopiowania ogólnie rzecz biorąc, zobacz [omówienie działania kopiowania](copy-activity-overview.md) przed przeczytaniem tego artykułu.
>

## <a name="performance-reference"></a>Informacje dotyczące wydajności

Jako odwołanie w poniższej tabeli przedstawiono liczbę przepływności kopiowania w MB/s dla danego źródła i pary ujścia w działaniu kopiowania pojedynczego uruchomienia w oparciu o wewnętrznych testy. Dla porównania ilustruje też sposób różnymi ustawieniami [jednostek integracji danych](#data-integration-units) lub [Self-Hosted integration runtime skalowalność](concepts-integration-runtime.md#self-hosted-integration-runtime) (wiele węzłów) może pomóc w wydajności kopiowania.

![Macierz wydajności](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> Po uruchomieniu działania kopiowania na środowisko IR Azure minimalny jednostki integracji dozwolonych danych (wcześniej znane jako jednostek przenoszenia danych) wynosi dwa. Jeśli nie zostanie określony, zobacz domyślnych jednostkach integracji danych, używany w [jednostek integracji danych](#data-integration-units).

**Informacje, które należy zwrócić uwagę:**

* Obliczana jest przepływność przy użyciu następującej formuły: [rozmiar danych do odczytu ze źródła] / [działanie kopiowania, czas trwania przebiegu].
* Numery odniesienia wydajności w tabeli zostały mierzone przy użyciu [TPC-H](http://www.tpc.org/tpch/) zestawu danych w działaniu kopiowania pojedynczego uruchomienia. Pliki testu dla magazynów opartych na plikach są wiele plików z 10 GB, rozmiar.
* W magazynach danych platformy Azure źródła i ujścia znajdują się w tym samym regionie platformy Azure.
* Związanym z kopiowaniem hybrydowej między lokalizacją lokalną i chmurą magazyny danych, każdy węzeł Self-Hosted integration runtime była uruchomiona na komputerze, na którym został oddzielnie od magazynu danych przy użyciu następujących specyfikacji. Gdy jedno działanie została uruchomiona, operacji kopiowania wykorzystywane tylko niewielką część procesora CPU, pamięć lub przepustowość sieci testowej maszyny.
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
        <td>Interfejs internetowy: 10 GB/s; Interfejs sieci intranet: 40 GB/s</td>
    </tr>
    </table>


> [!TIP]
> Za pomocą DIUs więcej można osiągnąć większą przepływność. Na przykład za pomocą 100 DIUs, możesz skopiować dane z usługi Azure Blob storage do usługi Azure Data Lake Store na 1,0 GB/s. Aby uzyskać więcej informacji na temat tej funkcji i obsługiwany scenariusz, zobacz [jednostek integracji danych](#data-integration-units) sekcji. 

## <a name="data-integration-units"></a>Jednostki integracji danych

Jednostka integracji danych jest miary, która reprezentuje możliwości (kombinację procesora CPU, pamięci i alokacji zasobów sieciowych) w pojedynczą jednostkę w usłudze Azure Data Factory. Jednostka integracji danych ma zastosowanie tylko do [Azure IR](concepts-integration-runtime.md#azure-integration-runtime), ale nie [może być samodzielnie hostowane środowisko IR](concepts-integration-runtime.md#self-hosted-integration-runtime).

Minimalny DIUs umożliwiające działanie kopiowania wynosi dwa. Jeśli nie zostanie określony, w poniższej tabeli wymieniono DIUs domyślne używane w scenariuszach różnych kopii:

| Skopiuj scenariusza | Domyślne DIUs określany przez usługę |
|:--- |:--- |
| Kopiowanie danych między magazynami oparte na plikach | Od 4 do 32, w zależności od liczby i rozmiaru plików |
| Kopiowanie danych do usługi Azure SQL Database lub Azure Cosmos DB |Od 4 do 16, w zależności od ujścia usługi Azure SQL Database i Cosmos DB w warstwie (liczba jednostek Dtu/RUs) |
| Innych scenariuszach kopiowania | 4 |

Aby zastąpić to ustawienie domyślne, należy określić wartość dla **dataIntegrationUnits** właściwości w następujący sposób. *Dozwolone wartości* dla **dataIntegrationUnits** właściwość jest maksymalnie 256. *Rzeczywista liczba DIUs* używany w czasie wykonywania operacji kopiowania jest równa lub mniejsza niż skonfigurowana wartość, w zależności od wzorca usługi danych. Aby uzyskać informacje na temat poziomu wydajności, może zostać wyświetlony po skonfigurowaniu większej liczby jednostek dla określonej kopii źródła i ujścia, zobacz [dotyczące wydajności](#performance-reference).

Możesz zobaczyć DIUs używane dla poszczególnych kopii, uruchom w danych wyjściowych działania kopiowania w przypadku monitorowania uruchomienie działania. Aby uzyskać więcej informacji, zobacz [skopiuj Monitorowanie działania](copy-activity-overview.md#monitoring).

> [!NOTE]
> Ustawienie DIUs większych niż cztery obecnie ma zastosowanie tylko wtedy, gdy skopiować wielu plików z usługi Azure Storage, usługi Azure Data Lake Storage, Amazon S3, usłudze Google Cloud Storage, w chmurze FTP lub SFTP w chmurze do innych magazynów danych chmury.
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

Należy pamiętać, że opłaty są naliczane na podstawie łącznego czasu operacji kopiowania. Całkowity czas, przez jaki opłaty są naliczane w przypadku przenoszenia danych to łączny czas trwania między DIUs. Jeśli zadanie kopiowania używany do podejmowania jedną godzinę od dwóch jednostek chmury, a obecnie zajmuje 15 minut przy użyciu osiem jednostek chmury, ogólną kwotę rachunku pozostanie prawie taki sam.

## <a name="parallel-copy"></a>Kopiuj równoległe

Możesz użyć **parallelCopies** właściwości do wskazania równoległości, który działanie kopiowania, aby użyć. Tę właściwość można traktować jako maksymalną liczbę wątków w ramach działania kopiowania, które można odczytać ze źródła lub zapisywać swoich magazynów danych ujścia równolegle.

Dla każdego uruchomienia działania kopiowania usługi Azure Data Factory określa liczbę równoległych kopii na potrzeby kopiowania danych ze źródła danych przechowywane i danych docelowego. Domyślna liczba równoległych kopii, które używa zależy od typu źródła i ujścia, którego używasz.

| Skopiuj scenariusza | Domyślna liczba równoległych kopii określany przez usługę |
| --- | --- |
| Kopiowanie danych między magazynami oparte na plikach |Zależy od rozmiaru plików i liczba DIUs umożliwia kopiowanie danych między dwoma magazynami danych w chmurze lub w konfiguracji fizycznego komputera Self-Hosted integration runtime. |
| Kopiowanie danych z dowolnego źródłowego magazynu do usługi Azure Table storage |4 |
| Innych scenariuszach kopiowania |1 |

> [!TIP]
> Podczas kopiowania danych między magazynami oparte na plikach, domyślne zachowanie zwykle daje najlepsze przepływności. Domyślnym zachowaniem jest określana automatycznie na podstawie Twojej wzorca pliku źródłowego.

Aby kontrolować obciążenia na maszynach, które hostują swoje dane są przechowywane lub można dostrajanie wydajności kopiowania, można zastąpić wartością domyślną i określić wartość dla **parallelCopies** właściwości. Wartość musi być liczba całkowita większa lub równa 1. W czasie wykonywania Aby uzyskać najlepszą wydajność, działanie kopiowania używa wartość, która jest mniejsza niż lub równa wartości, który został ustawiony.

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

**Informacje, które należy zwrócić uwagę:**

* Podczas kopiowania danych między magazynami oparte na plikach, **parallelCopies** określa równoległości na poziomie plików. Segmentu w pojedynczym pliku odbywa się poniżej, automatycznie i w sposób niewidoczny dla użytkownika. Ustalono, aby użyć najlepiej odpowiednich fragmentów rozmiar typu magazynu danego źródła danych do ładowania danych w sposób równoległy i prostopadły do **parallelCopies**. Rzeczywista liczba równoległych kopii usługi data movement service używa dla operacji kopiowania w czasie wykonywania jest nie więcej niż liczba plików, których masz. Jeśli zachowanie kopiowania to **mergeFile**, działanie kopiowania nie mogą korzystać z równoległości na poziomie plików.
* Przy kopiowaniu danych z magazynów, które nie są opartą na plikach (z wyjątkiem bazy danych Oracle jako źródło z włączoną partycjonowanie danych) do magazynów, które są oparte na pliku usługi data movement service ignoruje **parallelCopies** właściwości. Nawet jeśli równoległości jest określona, nie zostanie zastosowane w tym przypadku.
* **ParallelCopies** właściwość jest prostopadły do **dataIntegrationUnits**. Pierwsza jest liczony we wszystkich jednostkach integracji danych.
* Po określeniu wartości **parallelCopies** właściwość, należy wziąć pod uwagę wzrost obciążenia na źródła i ujścia magazynów danych. Również wziąć pod uwagę wzrost obciążenia do własnego środowiska integration runtime, jeśli działanie kopiowania ma odpowiednie uprawnienia, na przykład dla hybrydowych kopii. Dzieje się to zwiększenie obciążenia, zwłaszcza jeśli masz wiele działań lub równoczesnych uruchomień działań uruchamianych w odniesieniu do tego samego magazynu danych. Jeśli okaże się, czy w magazynie danych lub własnego środowiska integration runtime jest przeciążony przy obciążeniu, Zmniejsz **parallelCopies** wartości do zmniejszenia obciążenia.

## <a name="staged-copy"></a>Kopiowania przejściowego

Podczas kopiowania danych z magazynu danych źródłowych do magazynu danych ujścia, można użyć magazynu obiektów Blob jako tymczasowego magazynu przejściowego. Przemieszczania jest szczególnie użyteczna w następujących przypadkach:

- **Chcesz pozyskiwać dane z różnych magazynów danych do usługi SQL Data Warehouse za pomocą programu PolyBase.** Usługa SQL Data Warehouse używa programu PolyBase jako mechanizm o wysokiej przepływności ładowanie dużej ilości danych do usługi SQL Data Warehouse. Źródło danych musi być w usłudze Blob storage lub Azure Data Lake Store i musi spełniać dodatkowe kryteria. Podczas ładowania danych z magazynem danych innych niż usługi Blob storage lub Azure Data Lake Store, możesz aktywować dane kopiowanie za pośrednictwem tymczasowego przejściowego magazynu obiektów Blob. W takim przypadku usługi Azure Data Factory wykonuje przekształcenia danych wymagane, aby upewnić się, że spełnia wymagania programu PolyBase. Następnie używa programu PolyBase do ładowania danych do usługi SQL Data Warehouse wydajnie. Aby uzyskać więcej informacji, zobacz [przy użyciu technologii PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Czasami potrzebny na przeprowadzenie hybrydowe przenoszenie danych (oznacza to, aby skopiować ze środowiska lokalnego magazynu danych do magazynu danych w chmurze) za pośrednictwem z wolnym połączeniem sieciowym.** W celu poprawy wydajności kopiowania przejściowego można użyć do skompresowania danych w sieci lokalnej, dzięki czemu zajmuje mniej czasu na przenoszenie danych do przejściowy magazyn danych w chmurze. Następnie można zdekompresować danych w magazynie przemieszczania przed załadowaniem do docelowego magazynu danych.
- **Nie chcesz otworzyć porty inne niż port 80 i 443 w zaporze ze względu na zasady firmowe IT.** Na przykład podczas kopiowania danych z lokalnego magazynu danych ujścia Azure SQL Database lub ujścia Azure SQL Data Warehouse, musisz aktywować komunikacja wychodząca TCP na porcie 1433 dla zapory Windows i zaporą firmową. W tym scenariuszu kopiowania przejściowego można korzystać z własnego środowiska integration runtime najpierw skopiować dane do magazynu obiektów Blob, przemieszczania wystąpienie za pośrednictwem protokołu HTTP lub HTTPS na porcie 443. Następnie go załadować dane do bazy danych SQL Database lub SQL Data Warehouse z obszaru przemieszczania magazynu obiektów Blob. W tym przepływie nie trzeba włączyć port 1433.

### <a name="how-staged-copy-works"></a>Jak przygotowanych działania kopiowania

Po aktywowaniu funkcji przemieszczania najpierw jest skopiowanie danych z magazynu danych źródłowych do przejściowego magazynu obiektów Blob (skorzystaj z własnych). Następnie dane są kopiowane z przejściowy magazyn danych do magazynu danych ujścia. Usługa Azure Data Factory automatycznie zarządza przepływem dwuetapowego dla Ciebie. Usługa Azure Data Factory czyści dane tymczasowe z magazynu przejściowego także po ukończeniu przenoszenia danych.

![Kopiowania przejściowego](media/copy-activity-performance/staged-copy.png)

Gdy aktywujesz przenoszenie danych za pomocą magazynu przejściowego można określić, czy chcesz przechowywać dane, które mają być kompresowane przed przeniesieniem danych ze źródła danych, do tymczasowego lub magazynie danych przemieszczania następnie dekompresowane przed przenieść dane z tymczasowych lub tymczasowej dat Magazyn na magazyn danych ujścia.

Obecnie nie można skopiować dane między magazynami danych dwóch, które są połączone za pośrednictwem różnych IRs produktem, za pomocą ani bez kopiowania przejściowego. W przypadku takiego scenariusza można skonfigurować dwa działania jawnie łańcuchowych kopiowania do skopiowania ze źródła do wdrażania przejściowego, a następnie z tymczasowej do ujścia.

### <a name="configuration"></a>Konfigurowanie

Konfigurowanie **enableStaging** ustawienie w działaniu kopiowania, aby określić, czy dane zostaną umieszczone w magazynie obiektów Blob, zanim je załadujesz do docelowego magazynu danych. Po ustawieniu **enableStaging** do `TRUE`, określ dodatkowe właściwości, które są wymienione w poniższej tabeli. Musisz także utworzyć usługi Azure Storage lub magazynu udostępnionego usługi połączone podpisu dostęp do przemieszczania, jeśli nie masz.

| Właściwość | Opis | Wartość domyślna | Wymagane |
| --- | --- | --- | --- |
| enableStaging |Określ, czy chcesz skopiować dane za pośrednictwem tymczasowego magazynu przejściowego. |False |Nie |
| linkedServiceName |Określ nazwę [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) połączone usługi, która odnosi się do wystąpienia magazynu, którego używasz jako tymczasowy magazyn przejściowy. <br/><br/> Nie można używać magazynu przy użyciu sygnatury dostępu współdzielonego, aby załadować dane do usługi SQL Data Warehouse za pomocą programu PolyBase. Można go użyć w innych scenariuszach. |ND |Tak, gdy **enableStaging** jest ustawiona na wartość TRUE |
| ścieżka |Określ ścieżkę magazynu obiektów Blob, która ma zawierać użycia przemieszczonych danych. Jeśli nie podano ścieżki, usługa tworzy kontener do przechowywania danych tymczasowych. <br/><br/> Określ ścieżkę, tylko wtedy, gdy używasz magazynu przy użyciu sygnatury dostępu współdzielonego lub wymagają danych tymczasowych w określonej lokalizacji. |ND |Nie |
| enableCompression |Określa, czy dane powinny zostać skompresowane przed skopiowaniem ich do miejsca docelowego. To ustawienie powoduje zmniejszenie ilości przesyłanych danych. |False |Nie |

>[!NOTE]
> Jeśli używasz kopiowania przejściowego z włączoną kompresją, jednostkę usługi lub uwierzytelniania tożsamości usługi Zarządzanej dla przejściowego obiektu blob połączonej usługi nie jest obsługiwane.

Oto przykładowa definicja działania kopiowania przy użyciu właściwości, które są opisane w powyższej tabeli:

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

Opłaty są naliczane na podstawie dwóch kroków: czas trwania kopiowania i skopiuj typu.

* Korzystając z przemieszczania podczas kopiowania w chmurze, który kopiuje dane z magazynem danych w chmurze do innego magazynu danych w chmurze, zarówno etapów upoważnionych przez środowisko uruchomieniowe integracji platformy Azure, opłaty są naliczane [łączny czas trwania kopiowania kroki 1 i 2] x [Cena jednostkowa kopiowania chmury].
* Korzystając z przemieszczania podczas kopiowania hybrydowego, który kopiuje dane z lokalnego magazynu danych do magazynu danych w chmurze, jednego etapu upoważniony przez własne środowisko integration runtime, opłaty są naliczane dla [hybrydowej kopii czas trwania] x [Cena jednostkowa hybrydowej kopii] + [w chmurze czas kopiowania] x [Cena jednostkowa kopiowania chmury].

## <a name="performance-tuning-steps"></a>Kroki dostosowywania wydajności

Wykonaj następujące czynności, aby dostosować wydajność usługi Azure Data Factory za pomocą działania kopiowania.

1. **Ustalenie linii bazowej.** Podczas fazy opracowywania testowanie potoku za pomocą działania kopiowania względem przykładowych danych. Zbieraj szczegóły wykonania i charakterystyk wydajności [skopiuj Monitorowanie działania](copy-activity-overview.md#monitoring).

2. **Diagnozowanie i zoptymalizowania wydajności.** Jeśli wydajność, której możesz obserwować nie spełniają Twoich oczekiwań, należy określić wąskie gardła wydajności. Następnie zoptymalizować wydajność, aby usunąć lub zmniejszają efekt wąskich gardeł.

    W niektórych przypadkach po uruchomieniu działania kopiowania w usłudze Azure Data Factory, zobaczysz komunikat "Porady dotyczące dostrajania wydajności" w górnej części [działanie monitorowania strony kopiowania](copy-activity-overview.md#monitor-visually), jak pokazano w poniższym przykładzie. Komunikat informujący o tym wąskiego gardła, który został zidentyfikowany dla uruchomienia danej kopii. Również prowadzi użytkownika na tym, co można zmienić na zwiększenie wydajności, przepływności kopiowania. Porady dotyczące dostrajania wydajności zapewniają obecnie sugestie, takich jak:

    - Podczas kopiowania danych do usługi Azure SQL Data Warehouse przy użyciu technologii PolyBase.
    - Zwiększ jednostek żądań usługi Azure Cosmos DB lub Dtu bazy danych SQL Azure (jednostki przepływności bazy danych), gdy zasób po stronie magazynu danych jest wąskie gardło.
    - Usuń niepotrzebne kopiowania przejściowego.

    Dostrajanie reguł wydajności będą stopniowo wzbogacone także.

    **Przykład: Skopiuj do usługi Azure SQL Database z porady dotyczące dostrajania wydajności**

    W tym przykładzie podczas kopiowania z uruchamiania, usługi Azure Data Factory powiadomienia ujścia, którą usługa Azure SQL Database osiągnie wysokie wykorzystanie jednostek DTU, której spowalnia operacje zapisu. Sugestia jest zwiększenie warstwy usługi Azure SQL Database przy użyciu więcej jednostek Dtu. 

    ![Skopiuj monitorowanie za pomocą wskazówki dotyczące dostrajania wydajności](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    Ponadto poniżej przedstawiono niektóre typowe kwestie dotyczące. Pełny opis Diagnostyka wydajności wykracza poza zakres tego artykułu.

   * Funkcje wydajności:
     * [Kopiuj równoległe](#parallel-copy)
     * [Jednostki integracji danych](#data-integration-units)
     * [Kopiowania przejściowego](#staged-copy)
     * [Self-Hosted integration runtime skalowalności](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Infrastruktura Integration Runtime (Self-hosted)](#considerations-for-self-hosted-integration-runtime)
   * [Element źródłowy](#considerations-for-the-source)
   * [obiekt sink](#considerations-for-the-sink)
   * [Serializacja i deserializacja](#considerations-for-serialization-and-deserialization)
   * [Kompresja](#considerations-for-compression)
   * [Mapowanie kolumn](#considerations-for-column-mapping)
   * [Inne zagadnienia](#other-considerations)

3. **Rozwiń węzeł Konfiguracja do całego zestawu danych.** Gdy jesteś zadowolony z wyników wykonania i wydajności, można rozwinąć definicji i potoku w celu pokrycia całego zestawu danych.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Zagadnienia dotyczące własnego środowiska integration runtime

Jeśli Twoje działania kopiowania jest uruchomiony na własne środowisko integration runtime, pamiętaj o następujących kwestiach:

**Instalator**: Firma Microsoft zaleca używanie dedykowanej maszynie hosta integration Runtime. Zobacz [zagadnienia dotyczące korzystania z może być samodzielnie hostowane środowisko IR](concepts-integration-runtime.md).

**Skalowanie w poziomie**: Jednej logicznej własnego środowiska integration runtime z co najmniej jeden węzeł może obsługiwać wiele uruchomienia działania kopiowania w tym samym czasie jednocześnie. Jeśli masz duże potrzebę na hybrydowe przenoszenie danych z dużą liczbą jednoczesnych kopii uruchomienia działania lub z dużą ilością danych do skopiowania, należy wziąć pod uwagę [skalowanie w poziomie własnego środowiska integration runtime](create-self-hosted-integration-runtime.md#high-availability-and-scalability) aby aprowizować dodatkowe zasoby Zapewnij kopiowania.

## <a name="considerations-for-the-source"></a>Zagadnienia dotyczące źródła

### <a name="general"></a>Ogólne

Pamiętaj, że źródłowy magazyn danych nie jest przeciążony innych obciążeń, które są uruchomione w lub przed nim.

Dla magazynów danych firmy Microsoft, zobacz [monitorowania i dostrajania tematy](#performance-reference) specyficznych dla magazynów danych. Te tematy mogą pomóc zrozumieć charakterystyki wydajności magazynu danych oraz jak zminimalizować czas reakcji i zmaksymalizowania wydajności.

* Jeśli kopiujesz dane z magazynu obiektów Blob do usługi SQL Data Warehouse, należy wziąć pod uwagę przy użyciu programu PolyBase do poprawienia wydajności. Aby uzyskać więcej informacji, zobacz [przy użyciu technologii PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Jeśli kopiujesz dane z systemu plików HDFS do usługi Azure Blob storage lub Azure Data Lake Store, należy rozważyć korzystanie z narzędzia DistCp do poprawienia wydajności. Aby uzyskać więcej informacji, zobacz [DistCp użycia w celu skopiowania danych z systemu plików HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Jeśli kopiujesz dane z usługi Redshift do usługi Azure SQL Data Warehouse, Azure BLob storage lub Azure Data Lake Store, należy rozważyć użycie zwolnienia do poprawienia wydajności. Aby uzyskać więcej informacji, zobacz [zwolnienie Użyj, aby skopiować dane z usługi Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Magazyny danych opartych na plikach

* **Średni rozmiar pliku i liczba plików**: Działanie kopiowania przekazuje jeden plik danych w danym momencie. Przy użyciu tej samej ilości danych do przeniesienia ogólną przepustowość jest mniejszy, jeśli dane składa się z wielu małych plików, a nie kilka dużych plików z powodu fazie uruchamiania dla każdego pliku. Jeśli to możliwe należy połączyć małych plików w większych plikach w celu uzyskania większej przepływności.
* **Plik formatowania i kompresji**: Aby uzyskać więcej sposobów na zwiększenie wydajności, zobacz [zagadnienia dotyczące serializacji i deserializacji](#considerations-for-serialization-and-deserialization) i [zagadnienia dotyczące kompresji](#considerations-for-compression) sekcje.

### <a name="relational-data-stores"></a>Magazyny danych relacyjnych

* **Wzorzec danych**: Schemat tabeli ma wpływ na przepływność kopiowania. Rozmiar wiersza dużych zapewnia lepszą wydajność w porównaniu do skopiowania podobną ilość danych o rozmiarze małe wiersza. Przyczyną jest to, że baza danych wydajniej można pobrać mniejszej liczby partii danych, które zawierają mniej wierszy.
* **Zapytanie lub procedura składowana**: Optymalizuj logiki zapytanie lub procedura składowana Ciebie źródła działania kopiowania można pobrać danych bardziej efektywnie.

## <a name="considerations-for-the-sink"></a>Zagadnienia dotyczące ujścia

### <a name="general"></a>Ogólne

Pamiętaj, że źródłowy magazyn danych nie jest przeciążony innych obciążeń, które są uruchomione w lub przed nim.

Dla magazynów danych firmy Microsoft, zobacz [monitorowania i dostrajania tematy](#performance-reference) specyficznych dla magazynów danych. Te tematy mogą pomóc zrozumieć charakterystyki wydajności magazynu danych oraz jak zminimalizować czas reakcji i zmaksymalizowania wydajności.

* Jeśli kopiujesz dane z dowolnego magazynu danych Azure SQL Data Warehouse, należy wziąć pod uwagę przy użyciu programu PolyBase do poprawienia wydajności. Aby uzyskać więcej informacji, zobacz [przy użyciu technologii PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Jeśli kopiujesz dane z systemu plików HDFS do usługi Azure Blob storage lub Azure Data Lake Store, należy rozważyć korzystanie z narzędzia DistCp do poprawienia wydajności. Aby uzyskać więcej informacji, zobacz [DistCp użycia w celu skopiowania danych z systemu plików HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Jeśli kopiujesz dane z usługi Redshift do usługi Azure SQL Data Warehouse, Azure Blob storage lub Azure Data Lake Store, należy rozważyć użycie zwolnienia do poprawienia wydajności. Aby uzyskać więcej informacji, zobacz [zwolnienie Użyj, aby skopiować dane z usługi Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Magazyny danych opartych na plikach

* **Skopiuj zachowanie**: Jeśli kopiujesz dane z magazynu danych oparte na plikach, działanie kopiowania ma trzy opcje za pośrednictwem **copyBehavior** właściwości. Ją zachowuje hierarchię, spłaszcza hierarchii lub scala plików. Zachowywanie albo spłaszczanie hierarchii ma niewielki lub zmniejszenie wydajności, ale scalanie plików powoduje zmniejszenie wydajności zwiększyć.
* **Plik formatowania i kompresji**: Aby uzyskać więcej sposobów na zwiększenie wydajności, zobacz [zagadnienia dotyczące serializacji i deserializacji](#considerations-for-serialization-and-deserialization) i [zagadnienia dotyczące kompresji](#considerations-for-compression) sekcje.

### <a name="relational-data-stores"></a>Magazyny danych relacyjnych

* **Skopiuj domniemanie zachowanie i wydajność**: Istnieją różne sposoby zapisywania danych do ujścia SQL. Dowiedz się więcej z [najlepsze praktyki dotyczące ładowania danych do usługi Azure SQL Database](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database).

* **Rozmiar danych wzorca i batch**:
  * Schemat tabeli ma wpływ na przepływność kopiowania. Aby skopiować podobną ilość danych, rozmiaru duży wiersz zapewnia lepszą wydajność niż rozmiar wiersza małych ponieważ bazy danych można efektywniej zatwierdzić mniejszej liczby partii danych.
  * Działanie kopiowania wstawia dane z serii partii. Możesz ustawić liczbę wierszy w zadaniu wsadowym, używając **writeBatchSize** właściwości. Jeśli dane zawierają małe wiersze, możesz ustawić **writeBatchSize** właściwość o wyższej wartości do korzystania z mniejszy narzut partii i wyższej przepływności. Jeśli rozmiar wiersza danych jest duży, należy zachować ostrożność, wraz ze zwiększeniem **writeBatchSize**. O wysokiej wartości może prowadzić do awarii kopiowania spowodowane przeciążeniem bazy danych.

### <a name="nosql-stores"></a>Magazynów NoSQL

* Aby uzyskać **Table storage**:
  * **Partycja**: Zapisywanie danych z partycjami przeplotem znacznie obniża wydajność. Sortowanie danych źródła według klucza partycji, dzięki czemu dane są wstawiane wydajnie w jednej partycji po drugim. Lub możesz dostosować logiki można zapisać danych do jednej partycji.

## <a name="considerations-for-serialization-and-deserialization"></a>Zagadnienia dotyczące serializacji i deserializacji

Serializacja i deserializacja może wystąpić, gdy wejściowy zestaw danych lub wyjściowy zestaw danych jest plikiem. Aby uzyskać więcej informacji na temat obsługiwanych formatów plików przez działanie kopiowania, zobacz [obsługiwane formaty plików i kompresji](supported-file-formats-and-compression-codecs.md).

**Skopiuj zachowanie**:

* Kopiowanie plików między magazynami danych opartych na plikach:
  * Jeśli danych wejściowych i wyjściowych zestawów danych zarówno mają takie same lub nie ustawienia formatu pliku, usługi data movement service wykonuje *kopia binarna* bez serializacji lub deserializacji. Zostanie wyświetlony wyższej przepustowości w porównaniu do scenariusza, w której ustawienia formatu pliku źródła i ujścia różnią się od siebie nawzajem.
  * Gdy dane wejściowe i wyjściowe zestawy danych zarówno w formacie tekstowym, a tylko kodowanie typ jest inny, usługi data movement service jest wyłącznie Konwersja kodowania. Nie wszystkie serializacji i deserializacji, co powoduje, że niektóre wydajności, obciążenie w porównaniu do kopia binarna.
  * Jeśli danych wejściowych i wyjściowych zestawów danych zarówno mają różne formaty plików lub różne konfiguracje, takie jak ograniczników, usługi data movement service deserializuje dane źródłowe do strumienia, przekształcania i serializować go do formatu wyjściowego, wskazane przez Ciebie. Ta operacja powoduje znacznie bardziej znaczące wydajności, obciążenie w porównaniu do innych scenariuszy.
* Podczas kopiowania plików do / z magazynu danych, która nie jest plik oparty na przykład z magazynu oparte na plikach do relacyjnego magazynu serializacji lub deserializacji krok jest wymagany. Ten krok powoduje znaczne obciążenie.

**Format pliku**: Format pliku, który wybierzesz mogą mieć wpływ na wydajności kopiowania. Na przykład Avro jest kompaktowego formatu binarnego, który przechowuje metadane z danymi. Posiada obsługi szerokiej gamy w ekosystemie usługi Hadoop do przetwarzania i wykonywania zapytań. Avro jest droższe do serializacji i deserializacji, co skutkuje niższe przepływności kopiowania w porównaniu do formatu tekstowego. 

Wybierz ustawienia formatu pliku w całym przepływie przetwarzania całościowo. Uruchom za pomocą:

- Co tworzą dane są przechowywane w magazynach danych źródłowych lub ma zostać wyodrębniony z systemów zewnętrznych.
- Najlepszy format dla magazynu, przetwarzanie analityczne i wykonywania zapytań.
- W jakim formacie powinny wyeksportowane dane do składnic danych programów dla narzędzia do raportowania i wizualizacji.

Czasami formatu pliku nieoptymalne do odczytu i zapisu wydajność może być dobrym rozwiązaniem, gdy należy wziąć pod uwagę całkowity analitycznych procesu.

## <a name="considerations-for-compression"></a>Zagadnienia dotyczące kompresji

Gdy zestaw danych wejściowych lub wyjściowych jest plik, można ustawić działania kopiowania, które można wykonać kompresji lub dekompresji, ponieważ zapisuje dane do lokalizacji docelowej. Po wybraniu kompresji, upewnij się zależność między wejścia/wyjścia (We/Wy) i procesora CPU. Kompresowanie danych koszty dodatkowe zasoby obliczeniowe. Ale w zamian zmniejsza we/wy sieci i magazynu. W zależności od danych możesz zobaczyć boost w ogólną przepływność kopiowania.

**Koder-dekoder**: Każdy kodera-dekodera kompresji zapewnia korzyści. Na przykład bzip2 ma najniższą przepływności kopiowania, ale uzyskać najlepszą wydajność zapytań Hive przy użyciu bzip2, ponieważ podzielić ją do przetworzenia. Gzip jest to opcja najbardziej o zrównoważonym obciążeniu, a następnie jest ono używane najczęściej często. Wybierz koder-dekoder, który najlepiej odpowiada Twojemu scenariuszowi end-to-end.

**Poziom**: Możesz korzystać z dwóch opcji dla każdego kodera-dekodera kompresji: najszybszych skompresowane i optymalnie skompresowany. Najszybciej skompresowany opcji kompresuje dane tak szybko, jak to możliwe, nawet wtedy, gdy wynikowy plik nie jest optymalnie skompresowany. Opcja optymalnie skompresowany zużywa więcej czasu na kompresji i daje minimalnej ilości danych. Możesz przetestować obie opcje, aby zobaczyć, który zapewnia lepszą wydajność ogólną w Twoim przypadku.

**Jest brany pod uwagę**: Aby skopiować dużej ilości danych między lokalnym magazynie i w chmurze, należy rozważyć użycie [kopiowania etapowego](#staged-copy) z włączoną kompresją. Korzystanie z magazynu tymczasowego jest przydatne, gdy przepustowość sieci firmowej i usług platformy Azure jest czynnikiem ograniczającym i ma zestaw wejściowy i wyjściowy zestaw danych zarówno w skompresowanej.

## <a name="considerations-for-column-mapping"></a>Informacje dotyczące mapowania kolumn

Możesz ustawić **columnMappings** właściwość w działaniu kopiowania do mapy wszystkie lub podzbiór danych wejściowych kolumn na kolumny danych wyjściowych. Po usługi data movement service odczytuje dane ze źródła, trzeba wykonać mapowania kolumn na danych, zanim go zapisuje dane do ujścia. To dodatkowe przetwarzanie zmniejsza przepustowość kopiowania.

W przypadku odpytywalny magazynie danych źródła, na przykład, jeśli magazynu relacyjnego, takich jak bazy danych SQL Database lub SQL Server, czy jest magazynu NoSQL, takie jak usługi Table storage lub Azure Cosmos DB, należy wziąć pod uwagę wypychanie filtrowanie kolumn i zmianę kolejności logikę w celu **zapytania** właściwości zamiast mapowania kolumn. W ten sposób projekcji występuje, gdy usługi data movement service odczytuje dane z magazynu danych źródłowych, w którym jest znacznie bardziej efektywne.

Dowiedz się więcej z [skopiować mapowania schematu działania](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Inne zagadnienia

Jeśli rozmiar danych, które mają zostać skopiowane jest duża, można dostosować logiki biznesowej na kolejne partycje dane. Można zaplanować działania kopiowania do uruchamiania częściej, aby zmniejszyć rozmiar danych dla każdego działania kopiowania, które jest uruchamiane.

Należy zachować ostrożność liczby zestawów danych i skopiuj działań, które wymagają usługi Azure Data Factory nawiązać połączenia z tym samym magazynie danych w tym samym czasie. Wiele zadań jednoczesnych kopii może ograniczać do przechowywania danych i spowodować pogorszenie wydajności, kopii zadania wewnętrzne ponownych prób, a w niektórych przypadkach niepowodzenia wykonywania.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Przykładowy scenariusz: Kopiowanie z lokalnego programu SQL server do usługi Blob storage

**Scenariusz**: Potok został opracowany pod kątem kopiowanie danych z lokalnego programu SQL server do usługi Blob storage w formacie CSV. Aby przyspieszyć zadanie kopiowania, pliki CSV należy skompresowane do formatu bzip2.

**Test i analiza**: Przepływność działania kopiowania jest mniej niż 2 MB/s, który jest znacznie wolniejsze niż testów porównawczych wydajności.

**Analiza wydajności i dostosowywania**: Aby rozwiązać problem z wydajnością, Przyjrzyjmy się jak dane są przetwarzane i przenoszone.

- **Odczytywanie danych**: Środowisko integration runtime otwiera połączenie z SQL Server i wysyła to zapytanie. Program SQL Server odpowiada, wysyłając strumień danych do środowiska integration runtime za pośrednictwem sieci intranet.
- **Serializowanie i kompresować dane**: Środowisko integration runtime serializuje strumień danych do formatu CSV i kompresuje dane do usługi bzip2 stream.
- **Zapisywanie danych**: Środowisko integration runtime przekazuje strumienia bzip2 do magazynu obiektów Blob za pośrednictwem Internetu.

Jak widać, dane są przetwarzane i przenoszone w sposób sekwencyjny przesyłania strumieniowego: SQL Server > LAN > środowiska Integration runtime > sieć WAN > Blob storage. Ogólna wydajność jest uzyskiwany za przepustowość minimalna w potoku.

![Przepływ danych](./media/copy-activity-performance/case-study-pic-1.png)

Co najmniej jeden z następujących czynników może spowodować wąskie gardło:

* **Źródło**: Sam program SQL Server ma niskiej przepustowości, ze względu na duże obciążenia.
* **Może być samodzielnie hostowane środowisko IR**:
  * **LAN**: Środowisko Integration runtime znajduje się oni daleko od komputera serwera SQL i ma połączenie o niskiej przepustowości.
  * **Środowisko Integration runtime**: Środowisko Integration runtime została osiągnięta jej ograniczenia obciążenia, aby wykonywać następujące operacje:
    * **Serializacja**: Serializacja strumień danych do formatu CSV ma powolne przepływności.
    * **Kompresja**: Wybrano powolne kodera-dekodera kompresji, na przykład, bzip2, czyli 2,8 MB/s z Core i7.
  * **SIECI WAN**: Przepustowość między siecią firmową i usług platformy Azure jest niskie, na przykład T1 = 1,544 KB/s; T2 = 6,312 KB/s.
* **Obiekt sink**: Magazyn obiektów blob ma niskiej przepustowości. Ten scenariusz jest mało prawdopodobne, ponieważ jego umową dotyczącą poziomu usług (SLA) gwarantuje co najmniej 60 MB/s.

W tym przypadku bzip2 kompresji danych może być spowalniania cały potok. Przełączanie do kodera-dekodera kompresji gzip może jej obsługi ułatwiają realizację tego wąskiego gardła.

## <a name="references"></a>Dokumentacja

Poniżej przedstawiono monitorowanie wydajności i dostosowywania odwołania dla niektórych obsługiwanych magazynów danych:

* Usługa Azure Storage, która obejmuje usługi Blob storage i Table storage: [Cele skalowalności usługi Azure Storage](../storage/common/storage-scalability-targets.md) i [Lista kontrolna wydajności i skalowalności usługi Azure Storage](../storage/common/storage-performance-checklist.md).
* Azure SQL Database: Możesz [monitorować wydajność](../sql-database/sql-database-single-database-monitor.md) i sprawdź wartość procentowa jednostki transakcji bazy danych (DTU).
* Azure SQL Data Warehouse: Jej możliwości jest mierzony w liczbę jednostek magazynu danych (dwu). Zobacz [Zarządzaj obliczeniowa w usłudze Azure SQL Data Warehouse (omówienie)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Usługa Azure Cosmos DB [Poziomy wydajności w usłudze Azure Cosmos DB](../cosmos-db/performance-levels.md).
* Na lokalnym serwerze SQL Server: [Monitorowanie i dostrajanie wydajności](https://msdn.microsoft.com/library/ms189081.aspx).
* Lokalny serwer plików: [Dostrajanie wydajności dla serwerów plików](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne artykuły dotyczące działania kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Mapowanie schematu działania kopiowania](copy-activity-schema-and-type-mapping.md)
- [Kopiuj działania odporności na uszkodzenia](copy-activity-fault-tolerance.md)
