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
ms.date: 01/28/2019
ms.author: jingwang
ms.openlocfilehash: 81a5f99b0babd79af0034f684c45bfcf1bb25bd8
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66425608"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Skopiuj wydajności i działania przewodnika dostrajania
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-copy-activity-performance.md)
> * [Bieżąca wersja](copy-activity-performance.md)


Działanie kopiowania w usłudze Azure fabryki danych zapewnia najwyższej jakości danych bezpieczny, niezawodny i wydajny ładowania rozwiązania. Go umożliwia dziesiątki kopię terabajtów danych każdego dnia w wielu różnych chmury i rozwiązań lokalnych magazynów danych. Błyskawiczne danych wydajność ładowania ma kluczowe znaczenie dla zapewnienia możesz skupić się na główny problem "dane big data": tworzenie zaawansowanych rozwiązań analizy i uzyskiwanie szczegółowych informacji z wszystkie te dane.

Platforma Azure udostępnia zestaw przeznaczonych dla przedsiębiorstw rozwiązaniach magazynu danych magazynu i danych, a działanie kopiowania oferuje wysoce zoptymalizowane środowiska, który jest łatwy do skonfigurowania i ładowaniem. Przy użyciu tylko jednej kopii działania programu można osiągnąć:

* Ładowanie danych do **Azure SQL Data Warehouse** na **1,2 GB/s**.
* Ładowanie danych do **usługi Azure Blob storage** na **1,0 GB/s**
* Ładowanie danych do **usługi Azure Data Lake Store** na **1,0 GB/s**

W tym artykule opisano:

* [Numery identyfikacyjne wydajności](#performance-reference) obsługiwane źródło i ujście magazynu danych, aby ułatwić planowanie projektu.
* Funkcje, które może zwiększyć przepływność kopii w różnych scenariuszach, w tym [jednostek integracji danych](#data-integration-units), [równoległych kopii](#parallel-copy), i [kopiowania etapowego](#staged-copy);
* [Wskazówki dotyczące dostrajania wydajności](#performance-tuning-steps) dotyczące dostrajania wydajności i kluczowych czynników, które mogą mieć wpływ na wydajność kopii.

> [!NOTE]
> Jeśli nie jesteś zaznajomiony z działaniem kopiowania ogólnie rzecz biorąc, zobacz [omówieniu działania kopiowania](copy-activity-overview.md) przed przeczytaniem tego artykułu.
>

## <a name="performance-reference"></a>Informacje dotyczące wydajności

Jako odwołanie, Poniższa tabela pokazuje liczbę przepływności kopiowania **w MB/s** dla danego źródła i ujścia par **w działaniu kopiowania pojedynczego uruchomienia** testów wewnętrznych. Dla porównania ilustruje też sposób różnymi ustawieniami [jednostek integracji danych](#data-integration-units) lub [skalowalność własne środowisko IR](concepts-integration-runtime.md#self-hosted-integration-runtime) (wiele węzłów) może pomóc w wydajności kopiowania.

![Macierz wydajności](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> Działanie kopiowania jest wykonywane na środowiska Azure Integration Runtime, minimalny jednostki integracji dozwolonych danych (wcześniej znane jako jednostek przenoszenia danych) wynosi dwa. Jeśli nie zostanie określony, zobacz jednostki integracji danych domyślne używane w [jednostek integracji danych](#data-integration-units).

Informacje, które należy zwrócić uwagę:

* Obliczana jest przepływność przy użyciu następującej formuły: [rozmiar danych do odczytu ze źródła] / [czas uruchomienia działania kopiowania].
* Numery odniesienia wydajności w tabeli są mierzone przy użyciu [TPC-H](http://www.tpc.org/tpch/) zestawu danych w działaniu kopiowania pojedynczego uruchomienia. Pliki testu dla magazynów opartych na plikach są wiele plików z 10GB, rozmiar.
* W magazynach danych platformy Azure źródła i ujścia znajdują się w tym samym regionie platformy Azure.
* Związanym z kopiowaniem hybrydowej między lokalizacją lokalną i chmurą magazynów danych na każdym węźle środowiskiem Integration Runtime został uruchomiony na komputerze, który został oddzielnie od magazynu danych przy użyciu poniższych specyfikacji. Gdy jedno działanie została uruchomiona, operacji kopiowania wykorzystywane tylko niewielką część procesora CPU, pamięć lub przepustowość sieci testowej maszyny.
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
> Można osiągnąć większą przepływność przy użyciu więcej danych integracji jednostki (DIU). Na przykład ze 100 DIUs możesz osiągnąć kopiowania danych z obiektów Blob platformy Azure do usługi Azure Data Lake Store w **1.0GBps**. Zobacz [jednostek integracji danych](#data-integration-units) sekcji, aby uzyskać szczegółowe informacje na temat tej funkcji i obsługiwany scenariusz. 

## <a name="data-integration-units"></a>Jednostki integracji danych

A **jednostki integracji danych (DIU)** (wcześniej znana pod nazwą jednostek przenoszenia danych w chmurze DMU) jest miarą, który reprezentuje możliwości (kombinację procesora CPU, pamięci i alokacji zasobów sieciowych) w pojedynczą jednostkę w usłudze Data Factory. **DIU ma zastosowanie tylko do [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)** , ale nie [własne środowisko IR](concepts-integration-runtime.md#self-hosted-integration-runtime).

**Minimalny jednostek integracji danych, przeznaczonych dla uruchomienia działania kopiowania wynosi dwa.** Jeśli nie zostanie określony, w poniższej tabeli wymieniono DIUs domyślne używane w scenariuszach różnych kopii:

| Skopiuj scenariusza | Domyślne DIUs określany przez usługę |
|:--- |:--- |
| Kopiowanie danych między magazynami oparte na plikach | Od 4 do 32, w zależności od liczby i rozmiaru plików. |
| Innych scenariuszach kopiowania | 4 |

Aby zastąpić to ustawienie domyślne, należy określić wartość dla **dataIntegrationUnits** właściwości w następujący sposób. **Dozwolone wartości** dla **dataIntegrationUnits** właściwość **256**. **Rzeczywista liczba DIUs** używany w czasie wykonywania operacji kopiowania jest równa lub mniejsza niż skonfigurowana wartość, w zależności od wzorca usługi danych. Aby uzyskać informacje na temat poziomu wydajności, może zostać wyświetlony po skonfigurowaniu większej liczby jednostek dla określonej kopii źródła i ujścia, zobacz [dotyczące wydajności](#performance-reference).

Możesz zobaczyć faktycznie używanych jednostek integracji danych dla poszczególnych kopii, uruchom w działaniu kopiowania danych wyjściowych w przypadku uruchomienia działania monitorowania. Dowiedz się, szczegółowe informacje z [skopiuj Monitorowanie działania](copy-activity-overview.md#monitoring).

> [!NOTE]
> Ustawienie DIUs **większy niż 4** obecnie ma zastosowanie tylko wtedy, gdy użytkownik **skopiować wielu plików z usługi Azure Storage/Data Lake Storage/Amazon S3/Google Cloud Storage/w chmurze FTP/w chmurze SFTP do wszelkich innych magazynami danych w chmurze**.
>

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

### <a name="data-integration-units-billing-impact"></a>Wpływ rozliczeniowym jednostki integracji danych

Ma ona **ważne** należy pamiętać, że opłaty są naliczane na podstawie czasu łączna liczba operacji kopiowania. Całkowity czas, przez jaki są naliczane w przypadku przenoszenia danych to łączny czas trwania między DIUs. Jeśli zadanie kopiowania używany do podejmowania jedną godzinę od dwóch jednostek chmury, a obecnie zajmuje 15 minut przy użyciu osiem jednostek chmury, ogólną kwotę rachunku pozostanie prawie taki sam.

## <a name="parallel-copy"></a>Kopiuj równoległe

Możesz użyć **parallelCopies** właściwości do wskazania równoległości, który działanie kopiowania w celu użycia. Tę właściwość można traktować jako maksymalną liczbę wątków w ramach działania kopiowania, które można odczytać ze źródła lub zapisywać swoich magazynów danych ujścia równolegle.

Dla każdego uruchomienia działania kopiowania usługi fabryka danych określa liczbę równoległych kopii na potrzeby kopiowania danych ze źródła danych przechowywane i danych docelowego. Domyślna liczba równoległych kopii, które używa zależy od typu źródła i ujścia, którego używasz:

| Skopiuj scenariusza | Domyślna liczba równoległych kopii określany przez usługę |
| --- | --- |
| Kopiowanie danych między magazynami oparte na plikach |Zależy od rozmiaru plików i liczby jednostek integracji danych (DIUs) umożliwia kopiowanie danych między dwoma magazynami danych w chmurze lub w konfiguracji fizycznego komputera środowiskiem Integration Runtime. |
| Kopiowanie danych z dowolnego źródłowego magazynu danych do usługi Azure Table storage |4 |
| Innych scenariuszach kopiowania |1 |

> [!TIP]
> Podczas kopiowania danych między magazynami oparte na plikach, zachowanie domyślne (określane automatycznie) zwykle zapewniają najlepsze przepływności. 

Aby kontrolować obciążenia na maszynach, które hostują swoje dane są przechowywane lub można dostrajanie wydajności kopiowania, możesz zastąpić wartość domyślną, a następnie określić wartość dla **parallelCopies** właściwości. Wartość musi być liczba całkowita większa lub równa 1. W czasie wykonywania Aby uzyskać najlepszą wydajność, działanie kopiowania używa wartość, która jest mniejsza niż lub równa wartości, który został ustawiony.

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

Informacje, które należy zwrócić uwagę:

* Podczas kopiowania danych między magazynami oparte na plikach, **parallelCopies** określić równoległości na poziomie plików. Segmentu w pojedynczym pliku sytuacja może mieć miejsce poniżej automatycznie i w sposób niewidoczny dla użytkownika, a zostało zaprojektowane na potrzeby ładowania danych w równoległych i prostopadły do parallelCopies najlepsze rozmiar fragmentu odpowiedniego dla typu magazynu danego źródła danych. Rzeczywista liczba równoległych kopii usługi data movement service używa dla operacji kopiowania w czasie wykonywania jest nie więcej niż liczba plików, których masz. Jeśli zachowanie kopiowania to **mergeFile**, działanie kopiowania nie mogą korzystać z równoległości na poziomie plików.
* Po określeniu wartości **parallelCopies** właściwość, należy wziąć pod uwagę wzrost obciążenia na źródło i ujście magazynu danych i środowiskiem Integration Runtime, działanie kopiowania ma odpowiednie uprawnienia przez niego na przykład dla hybrydowych kopii. Dzieje się tak szczególnie, gdy masz wiele działań lub równoczesnych uruchomień działań uruchamianych w odniesieniu do tego samego magazynu danych. Jeśli okaże się, że magazyn danych lub środowiskiem Integration Runtime jest przeciążony przy obciążeniu, Zmniejsz **parallelCopies** wartości do zmniejszenia obciążenia.
* Podczas kopiowania danych z magazynów, które nie są opartą na plikach do magazynów, które są oparte na plikach, usługi data movement service ignoruje **parallelCopies** właściwości. Nawet jeśli równoległości jest określona, nie zostanie zastosowane w tym przypadku.
* **parallelCopies** jest prostopadły do **dataIntegrationUnits**. Pierwsza jest liczony we wszystkich jednostkach integracji danych.

## <a name="staged-copy"></a>Kopiowania przejściowego

Podczas kopiowania danych z magazynu danych źródłowych do magazynu danych ujścia, można użyć magazynu obiektów Blob jako tymczasowego magazynu przejściowego. Przemieszczania jest szczególnie użyteczna w następujących przypadkach:

- **Aby pozyskiwać dane z różnych magazynów danych do usługi SQL Data Warehouse za pomocą programu PolyBase**. Usługa SQL Data Warehouse używa programu PolyBase jako mechanizm o wysokiej przepływności ładowanie dużej ilości danych do usługi SQL Data Warehouse. Źródło danych musi być w usłudze Blob storage lub Azure Data Lake Store i musi spełniać dodatkowe kryteria. Podczas ładowania danych z magazynem danych innych niż usługi Blob storage lub Azure Data Lake Store, możesz aktywować dane kopiowanie za pośrednictwem tymczasowego przejściowego magazynu obiektów Blob. W takiej sytuacji usługi fabryka danych wykonuje przekształcenia danych wymagane, aby upewnić się, że spełnia wymagania programu PolyBase. Następnie używa programu PolyBase do ładowania danych do usługi SQL Data Warehouse wydajnie. Aby uzyskać więcej informacji, zobacz [przy użyciu technologii PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Czasami potrzebny na przeprowadzenie hybrydowe przenoszenie danych (oznacza to, aby skopiować ze środowiska lokalnego magazynu danych do magazynu danych w chmurze) za pośrednictwem z wolnym połączeniem sieciowym**. W celu poprawy wydajności kopiowania przejściowego można użyć do skompresowania danych w sieci lokalnej, dzięki czemu zajmuje mniej czasu na przenoszenie danych do przejściowy magazyn danych w chmurze, a następnie dekompresji danych w magazynie przemieszczania przed załadowaniem do docelowego magazynu danych.
- **Nie chcesz otworzyć porty inne niż port 80 i portu 443 w zaporze, ze względu na zasady firmowe IT**. Na przykład podczas kopiowania danych z lokalnego magazynu danych ujścia Azure SQL Database lub ujścia Azure SQL Data Warehouse, musisz aktywować komunikacja wychodząca TCP na porcie 1433 dla zapory Windows i zaporą firmową. W tym scenariuszu kopiowania przejściowego można korzystać z zalet środowiskiem Integration Runtime, aby najpierw skopiować dane do magazynu obiektów Blob, przemieszczania wystąpienie za pośrednictwem protokołu HTTP lub HTTPS na porcie 443, a następnie załadować dane do bazy danych SQL Database lub SQL Data Warehouse z obszaru przemieszczania magazynu obiektów Blob. W tym przepływie nie trzeba włączyć port 1433.

### <a name="how-staged-copy-works"></a>Jak przygotowanych działania kopiowania

Po aktywowaniu funkcji przemieszczania najpierw jest skopiowanie danych z magazynu danych źródłowych do przejściowego magazynu obiektów Blob (skorzystaj z własnych). Następnie dane są kopiowane z przejściowy magazyn danych do magazynu danych ujścia. Data Factory automatycznie zarządza przepływem dwuetapowego dla Ciebie. Usługi Data Factory czyści dane tymczasowe z magazynu przejściowego także po ukończeniu przenoszenia danych.

![Kopiowania przejściowego](media/copy-activity-performance/staged-copy.png)

Gdy aktywujesz przenoszenie danych za pomocą magazynu przejściowego można określić, czy chcesz, aby dane skompresowane przed przenosi dane z magazynu danych źródłowych do magazynu danych tymczasowych lub tymczasowej, a następnie dekompresowane przed przystąpieniem do przenoszenia danych z przejściowej lub przemieszczania danych magazynu do magazynu danych ujścia.

Obecnie nie można skopiować danych między dwoma magazynami danych w środowisku lokalnym za pomocą magazynu przejściowego.

### <a name="configuration"></a>Konfigurowanie

Konfigurowanie **enableStaging** ustawienie w działaniu kopiowania, aby określić, czy dane zostaną umieszczone w magazynie obiektów Blob, zanim je załadujesz do docelowego magazynu danych. Po ustawieniu **enableStaging** do `TRUE`, określ dodatkowe właściwości, które są wymienione w następnej tabeli. Jeśli nie masz, musisz również utworzyć usługi Azure Storage lub magazynu udostępnionego usługi połączonej podpis dostępu dla przemieszczania.

| Właściwość | Opis | Wartość domyślna | Wymagane |
| --- | --- | --- | --- |
| **enableStaging** |Określ, czy chcesz skopiować dane za pośrednictwem tymczasowego magazynu przejściowego. |False |Nie |
| **linkedServiceName** |Określ nazwę [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) połączone usługi, która odnosi się do wystąpienia magazynu, którego używasz jako tymczasowy magazyn przejściowy. <br/><br/> Nie można używać magazynu przy użyciu sygnatury dostępu współdzielonego, aby załadować dane do usługi SQL Data Warehouse za pomocą programu PolyBase. Można go użyć w innych scenariuszach. |ND |Tak, gdy **enableStaging** jest ustawiona na wartość TRUE |
| **Ścieżka** |Określ ścieżkę magazynu obiektów Blob, która ma zawierać użycia przemieszczonych danych. Jeśli ścieżka nie zostanie określona, usługa tworzy kontener do przechowywania danych tymczasowych. <br/><br/> Określ ścieżkę, tylko wtedy, gdy używasz magazynu przy użyciu sygnatury dostępu współdzielonego lub wymagają danych tymczasowych w określonej lokalizacji. |ND |Nie |
| **enableCompression** |Określa, czy dane powinny zostać skompresowane przed skopiowaniem ich do miejsca docelowego. To ustawienie powoduje zmniejszenie ilości przesyłanych danych. |False |Nie |

>[!NOTE]
> Jeśli używasz kopiowania przejściowego z włączoną kompresją, jednostkę usługi lub uwierzytelniania tożsamości usługi Zarządzanej dla przejściowego obiektu blob połączona usługa nie jest obsługiwana.

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

* Korzystając z przemieszczania podczas kopiowania chmury (kopiowanie danych z magazynem danych w chmurze do innego magazynu danych w chmurze, oba etapy upoważniony przez Azure Integration Runtime) są naliczane [łączny czas trwania kopiowania kroki 1 i 2] x [Cena jednostkowa kopiowania chmury].
* Korzystając z przemieszczania podczas kopiowania hybrydowe (kopiowanie danych z lokalnego magazynu danych do magazynu danych w chmurze, jednego etapu upoważniony przez własne środowisko IR), opłaty są naliczane za [hybrydowej kopii czas trwania] x [Cena jednostkowa hybrydowej kopii] + [w chmurze czas kopiowania] x [chmury Cena jednostkowa kopiowania].

## <a name="performance-tuning-steps"></a>Kroki dostosowywania wydajności

Sugerujemy, wykonaj następujące kroki, aby dostosować wydajność usługi Data Factory za pomocą działania kopiowania:

1. **Ustalenie linii bazowej**. Podczas fazy opracowywania testowanie potoku za pomocą działania kopiowania względem przykładowych danych. Zbieraj szczegóły wykonania i charakterystyk wydajności [skopiuj Monitorowanie działania](copy-activity-overview.md#monitoring).

2. **Diagnozowanie i zoptymalizować wydajność**. Jeśli wydajność, której możesz obserwować nie spełniają Twoich oczekiwań, musisz określić wąskie gardła wydajności. Następnie zoptymalizować wydajność, aby usunąć lub zmniejszają efekt wąskich gardeł. 

    W niektórych przypadkach po wykonaniu działania kopiowania w usłudze ADF, bezpośrednio zobaczysz "**porady dotyczące dostrajania wydajności**" w górnej części [działanie monitorowania strony kopiowania](copy-activity-overview.md#monitor-visually) jak pokazano w poniższym przykładzie. Go nie pozwalają określić wąskie gardło do wykonywania kopii danej tylko przeprowadzi Cię w celu zmiany w taki sposób, aby zwiększyć przepływność kopiowania. Wydajność, której porady dotyczące dostrajania aktualnie zawierają sugestie, takich jak przy użyciu technologii PolyBase podczas kopiowania danych do usługi Azure SQL Data Warehouse, aby zwiększyć liczbę usługi Azure Cosmos DB RU lub jednostek DTU bazy danych SQL Azure, gdy zasobu na dane przechowywane po stronie jest wąskie gardło, aby usunąć niepotrzebne przygotowane kopiowania, itd. Dostrajanie reguł wydajności będą stopniowo wzbogacone także.

    **Przykład: kopiowania do bazy danych SQL Azure, za pomocą wskazówki dotyczące dostrajania wydajności**

    W tym przykładzie podczas kopiowania Uruchom ADF Zauważ, że ujścia Azure SQL DB osiągnie wysokie wykorzystanie jednostek DTU, której spowalnia operacje zapisu, dlatego sugestię jest zwiększenie warstwy bazy danych SQL Azure za pomocą więcej jednostek DTU. 

    ![Skopiuj monitorowanie za pomocą wskazówki dotyczące dostrajania wydajności](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    Ponadto poniżej przedstawiono niektóre typowe kwestie dotyczące. Pełny opis Diagnostyka wydajności wykracza poza zakres tego artykułu.

   * Funkcje wydajności:
     * [Kopiuj równoległe](#parallel-copy)
     * [Jednostki integracji danych](#data-integration-units)
     * [Kopiowania przejściowego](#staged-copy)
     * [Samodzielnie hostowany skalowalność produktu Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Własne środowisko Integration Runtime](#considerations-for-self-hosted-integration-runtime)
   * [Element źródłowy](#considerations-for-the-source)
   * [obiekt sink](#considerations-for-the-sink)
   * [Serializacja i deserializacja](#considerations-for-serialization-and-deserialization)
   * [Kompresja](#considerations-for-compression)
   * [Mapowanie kolumn](#considerations-for-column-mapping)
   * [Inne zagadnienia](#other-considerations)

3. **Rozwiń węzeł Konfiguracja do całego zestawu danych**. Gdy jesteś zadowolony z wyników wykonania i wydajności, można rozwinąć definicji i potoku w celu pokrycia całego zestawu danych.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Zagadnienia dotyczące własnego środowiska Integration Runtime

Jeśli Twoje działania kopiowania jest wykonywane na środowiskiem Integration Runtime, pamiętaj o następujących kwestiach:

**Instalator**: Firma Microsoft zaleca używanie dedykowanej maszynie hosta Integration Runtime. Zobacz [zagadnienia dotyczące korzystania z produktem Integration Runtime](concepts-integration-runtime.md).

**Skalowanie w poziomie**: Jednej logicznej środowiskiem Integration Runtime z co najmniej jeden węzeł może obsługiwać wielu uruchomień działania kopiowania, w tym samym czasie jednocześnie. Jeśli masz duże potrzebę na hybrydowe przenoszenie danych z dużą liczbą jednoczesnych kopii uruchomienia działania lub z dużej ilości danych do skopiowania, należy wziąć pod uwagę do [skalowania w poziomie własne środowisko IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability) tak, aby aprowizować więcej zasobów do Zapewnij kopiowania.

## <a name="considerations-for-the-source"></a>Zagadnienia dotyczące źródła

### <a name="general"></a>Ogólne

Pamiętaj, że źródłowy magazyn danych nie jest przeciążony innych obciążeń, które są uruchomione w lub przed nim.

Dla magazynów danych firmy Microsoft, zobacz [monitorowania i dostrajania tematy](#performance-reference) specyficznych dla magazynów danych i pomagają zrozumieć dane przechowywane charakterystyki wydajności, zminimalizować czas reakcji i zmaksymalizowania wydajności.

* Po skopiowaniu danych **z magazynu obiektów Blob w usłudze SQL Data Warehouse**, należy rozważyć użycie **PolyBase** do poprawienia wydajności. Zobacz [przy użyciu technologii PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) Aby uzyskać szczegółowe informacje.
* Po skopiowaniu danych **z systemu plików HDFS do usługi Azure Blob/Azure Data Lake Store**, należy rozważyć użycie **DistCp** do poprawienia wydajności. Zobacz [DistCp użycia w celu skopiowania danych z systemu plików HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) Aby uzyskać szczegółowe informacje.
* Po skopiowaniu danych **z usługi Redshift do usługi Azure SQL danych Warehouse/Azure obiekt BLob/Azure Data Lake Store**, należy rozważyć użycie **zwolnienie** do poprawienia wydajności. Zobacz [zwolnienie Użyj, aby skopiować dane z usługi Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) Aby uzyskać szczegółowe informacje.

### <a name="file-based-data-stores"></a>Magazyny danych opartych na plikach

* **Średni rozmiar pliku i liczba plików**: Działanie kopiowania przekazuje jeden plik danych w danym momencie. Przy użyciu tej samej ilości danych do przeniesienia ogólną przepustowość jest mniejszy, jeśli dane składa się z wielu małych plików, a nie kilka dużych plików z powodu fazie uruchamiania dla każdego pliku. W związku z tym jeśli to możliwe, łączenia małych plików do większych plikach w celu uzyskania większej przepływności.
* **Plik formatowania i kompresji**: Aby uzyskać więcej sposobów na zwiększenie wydajności, zobacz [zagadnienia dotyczące serializacji i deserializacji](#considerations-for-serialization-and-deserialization) i [zagadnienia dotyczące kompresji](#considerations-for-compression) sekcje.

### <a name="relational-data-stores"></a>Magazyny danych relacyjnych

* **Wzorzec danych**: Schemat tabeli ma wpływ na przepływność kopiowania. Rozmiar wiersza dużych zapewnia lepszą wydajność niż rozmiar wiersza mały, aby skopiować podobną ilość danych. Przyczyną jest to, że baza danych wydajniej można pobrać mniejszej liczby partii danych, które zawierają mniej wierszy.
* **Zapytanie lub procedura składowana**: Optymalizuj logiki kwerendy lub procedury składowanej, określonego w źródle działania kopiowania można pobrać danych bardziej efektywnie.

## <a name="considerations-for-the-sink"></a>Zagadnienia dotyczące ujścia

### <a name="general"></a>Ogólne

Pamiętaj, że źródłowy magazyn danych nie jest przeciążony innych obciążeń, które są uruchomione w lub przed nim.

Magazyny danych firmy Microsoft, można znaleźć [monitorowania i dostrajania tematy](#performance-reference) specyficznych dla magazynów danych. Te tematy mogą pomóc zrozumieć charakterystyki wydajności magazynu danych oraz jak zminimalizować czas reakcji i zmaksymalizowania wydajności.

* Po skopiowaniu danych **z magazynu obiektów Blob w usłudze SQL Data Warehouse**, należy rozważyć użycie **PolyBase** do poprawienia wydajności. Zobacz [przy użyciu technologii PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) Aby uzyskać szczegółowe informacje.
* Po skopiowaniu danych **z systemu plików HDFS do usługi Azure Blob/Azure Data Lake Store**, należy rozważyć użycie **DistCp** do poprawienia wydajności. Zobacz [DistCp użycia w celu skopiowania danych z systemu plików HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) Aby uzyskać szczegółowe informacje.
* Po skopiowaniu danych **z usługi Redshift do usługi Azure SQL danych Warehouse/Azure obiekt BLob/Azure Data Lake Store**, należy rozważyć użycie **zwolnienie** do poprawienia wydajności. Zobacz [zwolnienie Użyj, aby skopiować dane z usługi Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) Aby uzyskać szczegółowe informacje.

### <a name="file-based-data-stores"></a>Magazyny danych opartych na plikach

* **Skopiuj zachowanie**: Jeśli kopiujesz dane z magazynu danych oparte na plikach, działanie kopiowania ma trzy opcje za pośrednictwem **copyBehavior** właściwości. Ją zachowuje hierarchię, spłaszcza hierarchii lub scala plików. Zachowywanie albo spłaszczanie hierarchii ma niewielki lub zmniejszenie wydajności, ale scalanie plików powoduje zmniejszenie wydajności zwiększyć.
* **Plik formatowania i kompresji**: Zobacz [zagadnienia dotyczące serializacji i deserializacji](#considerations-for-serialization-and-deserialization) i [zagadnienia dotyczące kompresji](#considerations-for-compression) sekcje, aby uzyskać więcej sposobów na zwiększenie wydajności.

### <a name="relational-data-stores"></a>Magazyny danych relacyjnych

* **Skopiuj zachowanie**: W zależności od właściwości ustawiono dla **sqlSink**, działanie kopiowania zapisuje dane do docelowej bazy danych na różne sposoby.
  * Domyślnie używa usługi przenoszenia danych interfejsu API kopiowania zbiorczego wstawiania danych w Dołącz trybu, który zapewnia najlepszą wydajność.
  * Jeśli skonfigurujesz procedury składowanej w ujściu bazy danych stosuje się jeden wiersz danych w czasie zamiast jako ładowania zbiorczego. Wydajności znacznie spada. Jeśli zestaw danych jest duży, jeśli ma to zastosowanie, rozważ przejście na **preCopyScript** właściwości.
  * Jeśli skonfigurujesz **preCopyScript** uruchomić właściwości dla każdego działania kopiowania, usługa wyzwala skrypt i następnie wstawić dane za pomocą interfejsu API kopiowania zbiorczego. Na przykład aby zastąpić całą tabelę przy użyciu najnowszych danych, można określić skrypt, aby usunąć wszystkie rekordy przed ładowania zbiorczego nowe dane ze źródła.
* **Rozmiar danych wzorca i batch**:
  * Schemat tabeli ma wpływ na przepływność kopiowania. Aby skopiować podobną ilość danych, rozmiaru duży wiersz zapewnia lepszą wydajność niż rozmiar wiersza małych ponieważ bazy danych można efektywniej zatwierdzić mniejszej liczby partii danych.
  * Działanie kopiowania wstawia dane z serii partii. Możesz ustawić liczbę wierszy w zadaniu wsadowym, używając **writeBatchSize** właściwości. Jeśli dane zawierają małe wiersze, możesz ustawić **writeBatchSize** właściwość o wyższej wartości do korzystania z mniejszy narzut partii i wyższej przepływności. Jeśli rozmiar wiersza danych jest duży, należy zachować ostrożność, wraz ze zwiększeniem **writeBatchSize**. O wysokiej wartości może prowadzić do awarii kopiowania spowodowane przeciążeniem bazy danych.

### <a name="nosql-stores"></a>Magazynów NoSQL

* Aby uzyskać **Table storage**:
  * **Partycja**: Zapisywanie danych z partycjami przeplotem znacznie obniża wydajność. Sortowanie danych źródła według klucza partycji, dzięki czemu dane są wstawiane wydajnie w jednej partycji po drugim lub dostosować logiki można zapisać danych do jednej partycji.

## <a name="considerations-for-serialization-and-deserialization"></a>Zagadnienia dotyczące serializacji i deserializacji

Serializacja i deserializacja może wystąpić, gdy Twoje wejściowego zestawu danych lub zestawu danych wyjściowych jest plik. Zobacz [obsługiwane formaty plików i kompresji](supported-file-formats-and-compression-codecs.md) ze szczegółami dotyczącymi obsługiwanych formatów plików przez działanie kopiowania.

**Skopiuj zachowanie**:

* Kopiowanie plików między magazynami danych opartych na plikach:
  * Gdy wejściowe i wyjściowe zestawy danych zarówno ma takie same lub nie ustawienia formatu pliku, usługi data movement service wykonuje **kopia binarna** bez serializacji lub deserializacji. Zostanie wyświetlony wyższej przepustowości w porównaniu do scenariusza, w której ustawienia formatu pliku źródła i ujścia różnią się od siebie nawzajem.
  * Gdy dane wejściowe i dane wyjściowe zestawy danych zarówno w formacie tekstowym, a tylko kodowanie typ jest inny, usługi data movement service jest wyłącznie Konwersja kodowania. Nie wszystkie serializacji i deserializacji, co powoduje, że niektóre wydajności, obciążenie w porównaniu do kopia binarna.
  * Jeśli wejściowe i wyjściowe zestawy danych zarówno mają różne formaty plików lub różne konfiguracje, takie jak ograniczników, usługi data movement service deserializuje dane źródłowe do strumienia, przekształcania i serializować go do formatu wyjściowego, wskazane przez Ciebie. Ta operacja powoduje znacznie bardziej znaczące wydajności, obciążenie w porównaniu do innych scenariuszy.
* Podczas kopiowania plików do/z magazynu danych, który nie jest oparte na plikach (na przykład z magazynu oparte na plikach do relacyjnego magazynu) krok serializacji lub deserializacji jest wymagany. Ten krok powoduje znaczne obciążenie.

**Format pliku**: Format pliku, który wybierzesz mogą mieć wpływ na wydajności kopiowania. Na przykład Avro jest kompaktowego formatu binarnego, który przechowuje metadane z danymi. Posiada obsługi szerokiej gamy w ekosystemie usługi Hadoop do przetwarzania i wykonywania zapytań. Jednak Avro jest droższe do serializacji i deserializacji, co skutkuje niższe przepływności kopiowania w porównaniu do formatu tekstowego. Wybierz ustawienia formatu pliku w całym przepływie przetwarzania całościowo. Rozpoczyna się od co tworzą dane są przechowywane w magazynach danych źródłowych lub ma zostać wyodrębniony z systemów zewnętrznych; najlepszy format dla magazynu, przetwarzanie analityczne i wykonywania zapytań; i w jakim formacie dane powinny być eksportowane do składnic danych programów dla narzędzia do raportowania i wizualizacji. Czasami formatu pliku nieoptymalne do odczytu i zapisu wydajność może być dobrym rozwiązaniem, gdy należy wziąć pod uwagę całkowity analitycznych procesu.

## <a name="considerations-for-compression"></a>Zagadnienia dotyczące kompresji

Gdy zestaw danych wejściowych lub wyjściowych jest plik, można ustawić działania kopiowania, które można wykonać kompresji lub dekompresji, ponieważ zapisuje dane do lokalizacji docelowej. Po wybraniu kompresji, upewnij się zależność między wejścia/wyjścia (We/Wy) i procesora CPU. Kompresowanie danych koszty dodatkowe zasoby obliczeniowe. Ale w zamian zmniejsza we/wy sieci i magazynu. W zależności od danych może zostać wyświetlony boost w ogólną przepływność kopiowania.

**Koder-dekoder**: Każdy kodera-dekodera kompresji zapewnia korzyści. Na przykład bzip2 ma najniższą przepływności kopiowania, ale uzyskać najlepszą wydajność zapytań Hive przy użyciu bzip2, ponieważ podzielić ją do przetworzenia. Gzip jest to opcja najbardziej o zrównoważonym obciążeniu, a jest najczęściej używany. Wybierz koder-dekoder, który najlepiej odpowiada Twojemu scenariuszowi end-to-end.

**Poziom**: Możesz korzystać z dwóch opcji dla każdego kodera-dekodera kompresji: najszybszych skompresowane i optymalnie skompresowany. Najszybciej skompresowany opcji kompresuje dane tak szybko, jak to możliwe, nawet wtedy, gdy wynikowy plik nie jest optymalnie skompresowany. Opcja optymalnie skompresowany zużywa więcej czasu na kompresji i daje minimalnej ilości danych. Możesz przetestować obie opcje, aby zobaczyć, który zapewnia lepszą wydajność ogólną w Twoim przypadku.

**Jest brany pod uwagę**: Aby skopiować dużej ilości danych między lokalnym magazynie i w chmurze, należy rozważyć użycie [kopiowania etapowego](#staged-copy) z włączoną kompresją. Przy użyciu magazynu tymczasowego jest przydatne, gdy przepustowość sieci firmowej i usług platformy Azure jest czynnikiem ograniczającym i chcesz wejściowy zestaw danych i zestawie danych wyjściowych zarówno w postaci bez kompresji.

## <a name="considerations-for-column-mapping"></a>Informacje dotyczące mapowania kolumn

Możesz ustawić **columnMappings** właściwość w działaniu kopiowania do mapy wszystkie lub podzbiór danych wejściowych kolumn na kolumny danych wyjściowych. Po usługi data movement service odczytuje dane ze źródła, trzeba wykonać mapowania kolumn na danych, zanim go zapisuje dane do ujścia. To dodatkowe przetwarzanie zmniejsza przepustowość kopiowania.

W przypadku odpytywalny magazynie danych źródła, na przykład, jeśli magazynu relacyjnego, takich jak bazy danych SQL Database lub SQL Server, czy jest magazynu NoSQL, takie jak usługi Table storage lub Azure Cosmos DB, należy wziąć pod uwagę wypychanie filtrowanie kolumn i zmianę kolejności logikę w celu **zapytania** właściwości zamiast mapowania kolumn. W ten sposób projekcji występuje, gdy usługi data movement service odczytuje dane z magazynu danych źródłowych, w którym jest znacznie bardziej efektywne.

Dowiedz się więcej z [mapowanie schematu działania kopiowania](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Inne zagadnienia

Jeśli rozmiar danych, które mają zostać skopiowane jest duża, można dostosować logiki biznesowej dalsze rozdzielanie danych i zaplanować działanie kopiowania w celu uruchamiania częściej, aby zmniejszyć rozmiar danych dla każdego uruchomienia działania kopiowania.

Należy zachować ostrożność liczby zestawów danych i działania kopiowania wymaganie usługi Data Factory nawiązać połączenia z tym samym magazynie danych w tym samym czasie. Wiele zadań jednoczesnych kopii może ograniczać do przechowywania danych i spowodować pogorszenie wydajności, kopii zadania wewnętrzne ponownych prób, a w niektórych przypadkach niepowodzenia wykonywania.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Przykładowy scenariusz: Kopiowanie z lokalnego programu SQL Server do usługi Blob storage

**Scenariusz**: Potok został opracowany pod kątem kopiowanie danych z lokalnego programu SQL Server do usługi Blob storage w formacie CSV. Aby przyspieszyć zadanie kopiowania, pliki CSV należy skompresowane do formatu bzip2.

**Test i analiza**: Przepływność działania kopiowania jest mniejsza niż 2 MB/s, który jest znacznie wolniejsze niż testów porównawczych wydajności.

**Analiza wydajności i dostosowywania**: Aby rozwiązać problem z wydajnością, Przyjrzyjmy się jak dane są przetwarzane i przenoszone.

1. **Odczytywanie danych**: Środowisko Integration runtime otwiera połączenie z SQL Server i wysyła to zapytanie. Program SQL Server odpowiada, wysyłając strumień danych do środowiska integration runtime za pośrednictwem sieci intranet.
2. **Serializowanie i kompresować dane**: Środowisko Integration runtime serializuje strumień danych do formatu CSV i kompresuje dane do usługi bzip2 stream.
3. **Zapisywanie danych**: Środowisko Integration runtime przekazuje strumienia bzip2 do magazynu obiektów Blob za pośrednictwem Internetu.

Jak widać, dane są przetwarzane i przenoszone w sposób sekwencyjny przesyłania strumieniowego: SQL Server > LAN > środowiska Integration runtime > sieć WAN > Blob storage. **Ogólna wydajność jest kontrolowany za przepustowość minimalna w potoku**.

![Przepływ danych](./media/copy-activity-performance/case-study-pic-1.png)

Co najmniej jeden z następujących czynników może spowodować wąskie gardło:

* **Źródło**: Sam program SQL Server ma niskiej przepustowości, ze względu na duże obciążenia.
* **Może być samodzielnie hostowane środowisko IR**:
  * **LAN**: Środowisko Integration runtime znajduje się oni daleko od komputera serwera SQL i ma połączenie o niskiej przepustowości.
  * **Środowisko Integration runtime**: Środowisko Integration runtime została osiągnięta jej ograniczenia obciążenia, aby wykonywać następujące operacje:
    * **Serializacja**: Serializacja strumień danych do formatu CSV ma powolne przepływności.
    * **Kompresja**: Wybrano powolne kodera-dekodera kompresji (na przykład, bzip2, czyli 2,8 MB/s z Core i7).
  * **SIECI WAN**: Brakuje przepustowości między siecią firmową i usług platformy Azure (na przykład T1 = 1,544 KB/s; T2 = 6,312 KB/s).
* **Obiekt sink**: Magazyn obiektów blob ma niskiej przepustowości. (W tym scenariuszu jest mało prawdopodobne, ponieważ jej umowa SLA gwarantuje co najmniej 60 MB/s).

W tym przypadku bzip2 kompresji danych może być spowalniania cały potok. Przełączanie do kodera-dekodera kompresji gzip może jej obsługi ułatwiają realizację tego wąskiego gardła.

## <a name="reference"></a>Informacje ogólne

Poniżej przedstawiono monitorowanie wydajności i dostosowywania odwołania dla niektórych obsługiwanych magazynów danych:

* Usługa Azure Storage (w tym magazyn obiektów Blob i Table storage): [Cele skalowalności usługi Azure Storage](../storage/common/storage-scalability-targets.md) i [Lista kontrolna wydajności i skalowalności usługi Azure Storage](../storage/common/storage-performance-checklist.md)
* Azure SQL Database: Możesz [monitorować wydajność](../sql-database/sql-database-single-database-monitor.md) i sprawdź wartość procentowa jednostki (DTU) dla transakcji bazy danych
* Azure SQL Data Warehouse: Jej możliwości jest mierzony w liczbę jednostek magazynu danych (dwu); zobacz [Zarządzaj obliczeniowa w usłudze Azure SQL Data Warehouse (omówienie)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Usługa Azure Cosmos DB [Poziomy wydajności w usłudze Azure Cosmos DB](../cosmos-db/performance-levels.md)
* Na lokalnym serwerze SQL Server: [Monitorowanie i dostrajanie wydajności](https://msdn.microsoft.com/library/ms189081.aspx)
* Lokalny serwer plików: [Dostosowywanie na serwerach plików wydajności](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne artykuły dotyczące działania kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Mapowanie schematu działania kopiowania](copy-activity-schema-and-type-mapping.md)
- [Kopiuj działania odporności na uszkodzenia](copy-activity-fault-tolerance.md)
