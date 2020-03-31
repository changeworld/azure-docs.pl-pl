---
title: Copy Activity performance and tuning guide (Przewodnik dotyczący wydajności i dostosowywania działania kopiowania)
description: Dowiedz się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych w usłudze Azure Data Factory podczas korzystania z działania kopiowania.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c4ca328aa0ddc61d86a435b93fe775f294287b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527388"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Copy Activity performance and tuning guide (Przewodnik dotyczący wydajności i dostosowywania działania kopiowania)

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-copy-activity-performance.md)
> * [Wersja 2 (bieżąca wersja)](../copy-activity-performance.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Kopiowanie wydajności działania i dostrajania przewodnika dla fabryki danych](../copy-activity-performance.md).

Działanie kopiowania fabrycznego usługi Azure zapewnia najwyższej klasy bezpieczne, niezawodne i wydajne rozwiązanie do ładowania danych. Umożliwia kopiowanie dziesiątków terabajtów danych każdego dnia w bogatych magazynach danych w chmurze i lokalnych magazynach danych. Niesamowita wydajność ładowania danych jest kluczem do zapewnienia, że możesz skupić się na podstawowym problemie "dużych zbiorów danych": tworzeniu zaawansowanych rozwiązań analitycznych i uzyskiwaniu szczegółowych informacji ze wszystkich tych danych.

Platforma Azure udostępnia zestaw rozwiązań do przechowywania danych i magazynów danych klasy korporacyjnej, a działanie kopiowania oferuje wysoce zoptymalizowane środowisko ładowania danych, które można łatwo skonfigurować i skonfigurować. Za pomocą jednego działania kopiowania możesz osiągnąć:

* Ładowanie danych do **usługi Azure SQL Data Warehouse** z **prędkością 1,2 GB/s**. Aby uzyskać instruktaż z przypadkiem użycia, zobacz [Ładowanie 1 TB do usługi Azure SQL Data Warehouse w ramach 15 minut z usługi Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Ładowanie danych do **magazynu obiektów Blob platformy Azure** z **prędkością 1,0 GB/s**
* Ładowanie danych do **usługi Azure Data Lake Store** z **prędkością 1,0 GB/s**

W tym artykule opisano:

* [Numery referencyjne wydajności dla obsługiwanych magazynów](#performance-reference) danych źródłowych i ujścia, aby ułatwić planowanie projektu;
* Funkcje, które mogą zwiększyć przepływność kopiowania w różnych scenariuszach, w tym [jednostek przenoszenia danych w chmurze,](#cloud-data-movement-units) [kopiowania równoległego](#parallel-copy)i [kopiowania etapowego;](#staged-copy)
* [Wskazówki dotyczące dostrajania wydajności](#performance-tuning-steps) dotyczące sposobu dostrajania wydajności i kluczowych czynników, które mogą mieć wpływ na wydajność kopiowania.

> [!NOTE]
> Jeśli nie jesteś zaznajomiony z copy activity w ogóle, zobacz [Przenoszenie danych przy użyciu funkcji Kopiowania przed](data-factory-data-movement-activities.md) przeczytaniem tego artykułu.
>

## <a name="performance-reference"></a>Odwołanie do wydajności

Jako odwołanie w poniższej tabeli przedstawiono numer przepływności kopiowania w MBps dla danego źródła i zlewu par na podstawie wewnętrznych testów. Dla porównania pokazuje również, jak różne ustawienia [jednostek przenoszenia danych w chmurze](#cloud-data-movement-units) lub [skalowalności bramy zarządzania danymi](data-factory-data-management-gateway-high-availability-scalability.md) (wiele węzłów bramy) mogą pomóc w wydajności kopiowania.

![Macierz wydajności](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>W usłudze Azure Data Factory w wersji 1 minimalne jednostki przenoszenia danych w chmurze do kopiowania między chmurami są dwa. Jeśli nie zostanie określony, zobacz domyślne jednostki przenoszenia danych używane w [jednostkach przenoszenia danych](#cloud-data-movement-units)w chmurze .

**Uwagi do uwag:**
* Przepływność jest obliczana przy użyciu następującej formuły: [rozmiar danych odczytanych ze źródła]/[Czas trwania wykonywania działania kopiowania].
* Numery referencyjne wydajności w tabeli zostały zmierzone przy użyciu zestawu danych [TPC-H](http://www.tpc.org/tpch/) w jednym przebiegu działania kopiowania.
* W magazynach danych platformy Azure źródło i ujście znajdują się w tym samym regionie platformy Azure.
* W przypadku kopiowania hybrydowego między magazynami danych lokalnych i chmurowych każdy węzeł bramy był uruchomiony na komputerze, który był oddzielony od lokalnego magazynu danych o poniższej specyfikacji. Gdy pojedyncze działanie było uruchomione na bramie, operacja kopiowania zużywała tylko niewielką część procesora, pamięci lub przepustowości sieci komputera testowego. Dowiedz się więcej z [uwagi na temat Bramy zarządzania danymi](#considerations-for-data-management-gateway).
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
        <td>Network (Sieć)</td>
        <td>Interfejs internetowy: 10 Gb/s; interfejs intranetowy: 40 Gb/s</td>
    </tr>
    </table>


> [!TIP]
> Można osiągnąć wyższą przepustowość, wykorzystując więcej jednostek przenoszenia danych (DMU) niż domyślne maksymalne dmu, który jest 32 dla chmury do chmury wykonywania działania kopiowania. Na przykład za pomocą 100 dmu można osiągnąć kopiowanie danych z usługi Azure Blob do usługi Azure Data Lake Store w **1.0GBps**. Zobacz sekcji [jednostek przenoszenia danych w chmurze,](#cloud-data-movement-units) aby uzyskać szczegółowe informacje na temat tej funkcji i obsługiwanego scenariusza. Skontaktuj się z [pomocą techniczną platformy Azure,](https://azure.microsoft.com/support/) aby zażądać większej liczby obiektów DMU.

## <a name="parallel-copy"></a>Kopia równoległa
Dane ze źródła można odczytać lub zapisać dane do miejsca docelowego **równolegle w ramach uruchomienia działania kopiowania**. Ta funkcja zwiększa przepływność operacji kopiowania i skraca czas potrzebny do przenoszenia danych.

To ustawienie różni się od właściwości **współbieżności** w definicji działania. Właściwość **współbieżności** określa liczbę **równoczesnych przebiegów działania kopiowania** do przetwarzania danych z różnych okien działania (1 AM do 2 AM, 2 AM do 3 AM, 3 AM do 4 AM i tak dalej). Ta funkcja jest przydatna podczas wykonywania obciążenia historycznego. Możliwość kopiowania równoległego dotyczy **pojedynczego uruchomienia działania**.

Przyjrzyjmy się przykładowego scenariusza. W poniższym przykładzie wiele wycinków z przeszłości muszą zostać przetworzone. Usługa Data Factory uruchamia wystąpienie działania kopiowania (przebiegu działania) dla każdego plasterka:

* Plasterek danych z pierwszego okna aktywności (1:00 do 2:00) ==> przebieg działania 1
* Plasterek danych z drugiego okna aktywności (2:00 do 3:00) ==> przebieg aktywności 2
* Plasterek danych z drugiego okna aktywności (od 3:00 do 4:00) ==> przebieg aktywności 3

I tak dalej.

W tym przykładzie, gdy wartość **współbieżności** jest ustawiona na 2, **działanie uruchom 1** i **działanie uruchomić 2** skopiować dane z dwóch okien działania **jednocześnie** w celu zwiększenia wydajności przenoszenia danych. Jeśli jednak wiele plików jest skojarzonych z działaniem uruchamianym 1, usługa przenoszenia danych kopiuje pliki ze źródła do miejsca docelowego po jednym pliku naraz.

### <a name="cloud-data-movement-units"></a>Jednostki przenoszenia danych w chmurze
**Jednostka przenoszenia danych w chmurze (DMU)** jest miarą reprezentującą moc (kombinację procesora CPU, pamięci i alokacji zasobów sieciowych) pojedynczej jednostki w fabryce danych. DMU ma zastosowanie do operacji kopiowania między chmurami, ale nie w kopii hybrydowej.

**Minimalne jednostki przenoszenia danych w chmurze, aby umożliwić uruchamianie działania kopiowania jest dwa.** Jeśli nie zostanie określona, w poniższej tabeli wymieniono domyślne dmu używane w różnych scenariuszach kopiowania:

| Scenariusz kopiowania | Domyślne dmu określone przez usługę |
|:--- |:--- |
| Kopiowanie danych między magazynami opartymi na plikach | Od 4 do 16 w zależności od liczby i rozmiaru plików. |
| Wszystkie inne scenariusze kopiowania | 4 |

Aby zastąpić tę wartość domyślną, należy określić wartość właściwości **cloudDataMovementUnits** w następujący sposób. **Dozwolone wartości** dla **cloudDataMovementUnits** właściwości są 2, 4, 8, 16, 32. **Rzeczywista liczba dmu w chmurze,** które używa operacji kopiowania w czasie wykonywania jest równa lub mniejsza niż skonfigurowana wartość, w zależności od wzorca danych. Aby uzyskać informacje na temat poziomu przyrostu wydajności, który może zostać uzyskany podczas konfigurowania większej liczby jednostek dla określonego źródła kopiowania i ujścia, zobacz [odwołanie do wydajności](#performance-reference).

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

> [!NOTE]
> Jeśli potrzebujesz więcej procesorów DMU w chmurze, aby uzyskać wyższą przepływność, skontaktuj się z [pomocą techniczną platformy Azure.](https://azure.microsoft.com/support/) Ustawienie 8 i powyżej działa obecnie tylko wtedy, gdy **kopiujesz wiele plików z magazynu obiektów Blob/Data Lake Store/Amazon S3/cloud FTP/cloud SFTP do magazynu obiektów Blob/Data Lake Store/Bazy danych SQL platformy Azure**.
>

### <a name="parallelcopies"></a>parallelCopies (Kopia równoległa)
Można użyć **parallelCopies** właściwość wskazać równoległości, które mają być używane copy activity. Tę właściwość można interpretować jako maksymalną liczbę wątków w ramach działania kopiowania, które można odczytać ze źródła lub zapisywać w magazynach danych ujścia równolegle.

Dla każdego uruchomienia działania kopiowania usługa Data Factory określa liczbę równoległych kopii używanych do kopiowania danych z magazynu danych źródłowych i do docelowego magazynu danych. Domyślna liczba kopii równoległych, których używa, zależy od typu źródła i ujścia, którego używasz.

| Źródło i zlew | Domyślna liczba kopii równoległych określona przez usługę |
| --- | --- |
| Kopiowanie danych między magazynami opartymi na plikach (magazyn obiektów Blob; Magazyn Data Lake; Amazon S3; lokalny system plików; lokalnego hdfs) |Między 1 a 32. Zależy od rozmiaru plików i liczby jednostek przenoszenia danych w chmurze (DMU) używanych do kopiowania danych między dwoma magazynami danych w chmurze lub fizycznej konfiguracji komputera bramy używanej do kopiowania hybrydowego (do kopiowania danych do lub z lokalnego magazynu danych). |
| Kopiowanie danych z **dowolnego magazynu danych źródłowych do magazynu tabel platformy Azure** |4 |
| Wszystkie inne pary źródeł i zlewów |1 |

Zazwyczaj domyślne zachowanie powinno zapewniać najlepszą przepływność. Jednak aby kontrolować obciążenie na komputerach, które hostują magazyny danych lub dostroić wydajność kopiowania, można zastąpić wartość domyślną i określić wartość właściwości **parallelCopies.** Wartość musi zawierać się w przedziale od 1 do 32 (zarówno włącznie). W czasie wykonywania dla najlepszej wydajności działanie kopiowania używa wartości, która jest mniejsza lub równa ustawionej wartości.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
Uwagi do uwag:

* Podczas kopiowania danych między magazynami opartymi na **plikach, parallelCopies** określić równoległości na poziomie pliku. Fragmentowanie w ramach jednego pliku będzie się odbywać pod spodem automatycznie i przejrzyście i jest przeznaczony do używania najlepszego odpowiedniego rozmiaru fragmentu dla danego typu magazynu danych źródłowych, aby załadować dane równolegle i ortogonalne do równoległychCopies. Rzeczywista liczba kopii równoległych używanych przez usługę przenoszenia danych dla operacji kopiowania w czasie wykonywania nie jest większa niż liczba plików, które masz. Jeśli zachowanie kopiowania jest **mergeFile**, Działanie kopiowania nie może korzystać z równoległości na poziomie pliku.
* Po określeniu wartości dla **właściwości parallelCopies** należy wziąć pod uwagę wzrost obciążenia w magazynach danych źródłowych i ujścia oraz do bramy, jeśli jest to kopia hybrydowa. Dzieje się tak zwłaszcza wtedy, gdy masz wiele działań lub równoczesnych przebiegów tych samych działań, które są uruchamiane dla tego samego magazynu danych. Jeśli zauważysz, że magazyn danych lub brama jest przeciążony z obciążeniem, zmniejszyć **parallelCopies** wartość, aby zmniejszyć obciążenie.
* Podczas kopiowania danych ze sklepów, które nie są oparte na plikach do magazynów, które są oparte na plikach, usługa przenoszenia danych ignoruje **parallelCopies** właściwości. Nawet jeśli określono równoległość, nie jest stosowany w tym przypadku.

> [!NOTE]
> Aby użyć funkcji **parallelCopies,** podczas wykonywania kopii hybrydowej należy użyć bramy zarządzania danymi w wersji 1.11 lub nowszej.
>
>

Aby lepiej użyć tych dwóch właściwości i zwiększyć przepływność przenoszenia danych, zobacz przykładowe przypadki użycia. Nie trzeba konfigurować **parallelCopies,** aby skorzystać z domyślnego zachowania. Jeśli nie skonfigurować i **parallelCopies** jest zbyt mały, wiele dmu chmury może nie być w pełni wykorzystane.

### <a name="billing-impact"></a>Wpływ rozliczeń
**Należy** pamiętać, że opłaty są naliczane na podstawie całkowitego czasu operacji kopiowania. Jeśli zadanie kopiowania używane do podjęcia jednej godziny z jednej jednostki chmury, a teraz trwa 15 minut z czterech jednostek chmury, ogólny rachunek pozostaje prawie taka sama. Na przykład można użyć czterech jednostek chmury. Pierwsza jednostka chmury spędza 10 minut, druga, 10 minut, trzecia, 5 minut, a czwarta, 5 minut, wszystko w jednym przebiegu działania kopiowania. Opłata jest naliczana za całkowity czas kopiowania (przenoszenia danych), który wynosi 10 + 10 + 5 + 5 = 30 minut. Korzystanie **z parallelCopies** nie wpływa na rozliczenia.

## <a name="staged-copy"></a>Kopia etapowa
Podczas kopiowania danych z magazynu danych źródłowych do magazynu danych ujścia, można użyć magazynu obiektów Blob jako tymczasowego magazynu przemieszczania. Inscenizacja jest szczególnie przydatna w następujących przypadkach:

1. **Chcesz pozyskiwania danych z różnych magazynów danych do magazynu danych SQL za pośrednictwem PolyBase**. Usługa SQL Data Warehouse używa polybase jako mechanizmu o wysokiej przepływności do ładowania dużej ilości danych do magazynu danych SQL. Jednak dane źródłowe muszą znajdować się w magazynie obiektów Blob i muszą spełniać dodatkowe kryteria. Podczas ładowania danych z magazynu danych innych niż magazyn obiektów Blob, można aktywować kopiowanie danych za pośrednictwem tymczasowego tymczasowego magazynu obiektów blob. W takim przypadku usługa Data Factory wykonuje wymagane przekształcenia danych, aby upewnić się, że spełnia wymagania PolyBase. Następnie używa PolyBase do ładowania danych do magazynu danych SQL. Aby uzyskać więcej informacji, zobacz [PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). Aby uzyskać instruktaż z przypadkiem użycia, zobacz [Ładowanie 1 TB do usługi Azure SQL Data Warehouse w ramach 15 minut z usługi Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Czasami trwa trochę czasu, aby wykonać hybrydowy transfer danych (to znaczy, aby skopiować między lokalnym magazynem danych a magazynem danych w chmurze) za pośrednictwem wolnego połączenia sieciowego.** Aby zwiększyć wydajność, można skompresować dane lokalnie, dzięki czemu trwa mniej czasu, aby przenieść dane do magazynu danych przemieszczania w chmurze. Następnie można rozpakować dane w magazynie przemieszczania przed załadowaniem go do docelowego magazynu danych.
3. **Nie chcesz otwierać portów innych niż port 80 i port 443 w zaporze, ze względu na firmowe zasady IT.** Na przykład podczas kopiowania danych z lokalnego magazynu danych do ujścia bazy danych SQL platformy Azure lub ujścia usługi Azure SQL Data Warehouse, należy aktywować wychodzącą komunikację TCP na porcie 1433 zarówno dla zapory systemu Windows, jak i zapory firmowej. W tym scenariuszu skorzystaj z bramy, aby najpierw skopiować dane do wystąpienia przemieszczania magazynu obiektów Blob za pośrednictwem protokołu HTTP lub HTTPS na porcie 443. Następnie należy załadować dane do bazy danych SQL lub magazynu danych SQL z magazynu obiektów blob przemieszczania. W tym przepływie nie trzeba włączać portu 1433.

### <a name="how-staged-copy-works"></a>Jak działa kopia etapowa
Po uaktywnieniu funkcji przemieszczania najpierw dane są kopiowane z magazynu danych źródłowych do magazynu danych przemieszczania (przynieś własne). Następnie dane są kopiowane z magazynu danych przemieszczania do magazynu danych ujścia. Usługa Data Factory automatycznie zarządza przepływem dwuetapowym. Fabryka danych również czyści dane tymczasowe z magazynu przemieszczania po zakończeniu przenoszenia danych.

W scenariuszu kopiowania w chmurze (zarówno źródłowe, jak i ujście magazynów danych znajdują się w chmurze), brama nie jest używana. Usługa Fabryka danych wykonuje operacje kopiowania.

![Kopia etapowa: scenariusz chmury](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

W scenariuszu kopiowania hybrydowego (źródło jest lokalne, a obiekt sink znajduje się w chmurze), brama przenosi dane ze źródłowego magazynu danych do magazynu danych przemieszczania. Usługa data factory przenosi dane z magazynu danych przemieszczania do magazynu danych ujścia. Kopiowanie danych z magazynu danych w chmurze do lokalnego magazynu danych za pośrednictwem przemieszczania jest również obsługiwane przy przepływie odwróconym.

![Kopia etapowa: scenariusz hybrydowy](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Podczas włączania przenoszenia danych przy użyciu magazynu przemieszczania można określić, czy dane mają być kompresowane przed przeniesieniem danych ze źródłowego magazynu danych do tymczasowego lub przemieszczania magazynu danych, a następnie zdekompresować przed przeniesieniem danych z danych tymczasowych lub przejściowych magazyn danych ujścia.

Obecnie nie można kopiować danych między dwoma lokalnymi magazynami danych przy użyciu magazynu przemieszczania. Oczekujemy, że ta opcja będzie dostępna wkrótce.

### <a name="configuration"></a>Konfigurowanie
Skonfiguruj ustawienie **enableStaging** w funkcji Kopiowanie działania, aby określić, czy dane mają być przemieszczane w magazynie obiektów Blob przed załadowaniem ich do docelowego magazynu danych. Po ustawieniu **enableStaging** na WARTOŚĆ TRUE należy określić dodatkowe właściwości wymienione w następnej tabeli. Jeśli go nie masz, musisz również utworzyć usługę Azure Storage lub usługa dostępu współdzielonego usługi połączonego z dostępem do usługi dla przemieszczania.

| Właściwość | Opis | Wartość domyślna | Wymagany |
| --- | --- | --- | --- |
| **enableStaging (włączaniestaging)** |Określ, czy dane mają być kopiowane za pośrednictwem tymczasowego magazynu przemieszczania. |False |Nie |
| **linkedServiceName** |Określ nazwę usługi połączonej [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) lub [AzureStorageSas,](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) która odnosi się do wystąpienia magazynu, którego używasz jako tymczasowego magazynu przemieszczania. <br/><br/> Nie można użyć magazynu z podpisem dostępu współdzielonego do ładowania danych do magazynu danych SQL za pośrednictwem polybase. Można go używać we wszystkich innych scenariuszach. |Nie dotyczy |Tak, gdy **opcja enableStaging** jest ustawiona na WARTOŚĆ TRUE |
| **Ścieżka** |Określ ścieżkę magazynu obiektów Blob, która ma zawierać dane etapowe. Jeśli nie podasz ścieżkę, usługa tworzy kontener do przechowywania danych tymczasowych. <br/><br/> Określ ścieżkę tylko wtedy, gdy używasz magazynu z podpisem dostępu współdzielonego lub potrzebujesz danych tymczasowych, aby znajdować się w określonej lokalizacji. |Nie dotyczy |Nie |
| **enableKompresja** |Określa, czy dane mają być kompresowane przed skopiowaniem do miejsca docelowego. To ustawienie zmniejsza ilość przesyłanych danych. |False |Nie |

Oto przykładowa definicja działania kopiowania z właściwościami opisanymi w powyższej tabeli:

```json
"activities":[
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>Wpływ rozliczeń
Opłaty są naliczane na podstawie dwóch kroków: czasu trwania kopiowania i typu kopiowania.

* Podczas korzystania z przemieszczania podczas kopiowania w chmurze (kopiowanie danych z magazynu danych w chmurze do innego magazynu danych w chmurze) naliczana jest opłata [suma czasu trwania kopiowania dla kroków 1 i 2] x [cena jednostkowa kopiowania w chmurze].
* Podczas tworzenia przemieszczania podczas kopiowania hybrydowego (kopiowanie danych z lokalnego magazynu danych do magazynu danych w chmurze) pobierana jest opłata [czas trwania kopii hybrydowej] x [cena jednostkowa kopiowania hybrydowego] + [czas trwania kopiowania w chmurze] x [cena jednostkowa kopiowania w chmurze].

## <a name="performance-tuning-steps"></a>Kroki dostrajania wydajności
Zalecamy wykonanie tych kroków w celu dostrojenia wydajności usługi Data Factory za pomocą funkcji Kopiowanie:

1. **Ustal punkt odniesienia**. W fazie rozwoju przetestuj potok przy użyciu działania kopiowania względem próbki danych reprezentatywnych. Model [krojenia](data-factory-scheduling-and-execution.md) fabryki danych umożliwia ograniczenie ilości danych, z którymi pracujesz.

   Zbieraj czas wykonywania i charakterystyki wydajności za pomocą **monitorowania i zarządzania aplikacji**. Wybierz **pozycję Monitor & Zarządzaj** na stronie głównej usługi Data Factory. W widoku drzewa wybierz **wyjściowy zestaw danych**. Na liście **System aktywności wybierz** polecenie Uruchom działanie kopiowania. **Działanie system Windows** zawiera listę czasu trwania działania kopiowania i rozmiaru kopiowanych danych. Przepływność jest wymieniona w **Eksploratorze okien działania**. Aby dowiedzieć się więcej o aplikacji, zobacz [Monitorowanie potoków usługi Azure Data Factory i zarządzanie nimi przy użyciu aplikacji Do monitorowania i zarządzania](data-factory-monitor-manage-app.md).

   ![Szczegóły uruchamiania działania](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   W dalszej części artykułu można porównać wydajność i konfigurację scenariusza z [odwołaniem do wydajności](#performance-reference) działania kopiowania z naszych testów.
2. **Diagnozowanie i optymalizacja wydajności**. Jeśli wydajność, którą obserwujesz, nie spełnia Twoich oczekiwań, musisz zidentyfikować wąskie gardła wydajności. Następnie należy zoptymalizować wydajność, aby usunąć lub zmniejszyć efekt wąskich gardeł. Pełny opis diagnozy wydajności wykracza poza zakres tego artykułu, ale oto kilka typowych zagadnień:

   * Funkcje wydajności:
     * [Kopia równoległa](#parallel-copy)
     * [Jednostki przenoszenia danych w chmurze](#cloud-data-movement-units)
     * [Kopia etapowa](#staged-copy)
     * [Skalowalność bramy zarządzania danymi](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Brama zarządzania danymi](#considerations-for-data-management-gateway)
   * [Źródła](#considerations-for-the-source)
   * [Ujście](#considerations-for-the-sink)
   * [Serializacja i deserializacja](#considerations-for-serialization-and-deserialization)
   * [Kompresja](#considerations-for-compression)
   * [Mapowanie kolumn](#considerations-for-column-mapping)
   * [Inne względy](#other-considerations)
3. **Rozwiń konfigurację do całego zestawu danych**. Po spełnieniu wyników wykonania i wydajności można rozwinąć definicję i okres aktywny potoku, aby objąć cały zestaw danych.

## <a name="considerations-for-data-management-gateway"></a>Zagadnienia dotyczące bramy zarządzania danymi
**Konfiguracja bramy:** Zaleca się używanie dedykowanego komputera do hostowania bramy zarządzania danymi. Zobacz [Zagadnienia dotyczące korzystania z bramy zarządzania danymi](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Monitorowanie bramy i skalowanie w górę/wy :** Pojedyncza brama logiczna z co najmniej jednym węzłem bramy może obsługiwać wiele uruchomień działania kopiowania w tym samym czasie jednocześnie. Migawkę wykorzystania zasobów (CPU, memory, network(in/out) można wyświetlić w czasie zbliżonym do rzeczywistego na komputerze bramy, a także liczbę równoczesnych zadań uruchomionych w porównaniu z limitem w portalu Azure, zobacz [Monitoruj bramę w portalu](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Jeśli masz duże potrzeby w przypadku hybrydowego przenoszenia danych przy użyciu dużej liczby równoczesnych przebiegów działania kopiowania lub dużej ilości danych do skopiowania, należy rozważyć [skalowanie w górę lub skalowanie bramy w poziomie,](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) aby lepiej wykorzystać zasób lub udostępnić więcej zasobów, aby umożliwić kopiowanie.

## <a name="considerations-for-the-source"></a>Uwagi dotyczące źródła
### <a name="general"></a>Ogólne
Upewnij się, że podstawowy magazyn danych nie jest przeciążony przez inne obciążenia, które są uruchomione na lub przeciwko niemu.

W przypadku magazynów danych firmy Microsoft zobacz [monitorowanie i dostrajanie tematów](#performance-reference) specyficznych dla magazynów danych i pomagają zrozumieć charakterystykę wydajności magazynu danych, zminimalizować czasy odpowiedzi i zmaksymalizować przepływność.

Jeśli kopiujesz dane z magazynu obiektów Blob do magazynu danych SQL, należy rozważyć użycie **PolyBase** w celu zwiększenia wydajności. Szczegółowe informacje można znaleźć w programie [PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) Aby uzyskać instruktaż z przypadkiem użycia, zobacz [Ładowanie 1 TB do usługi Azure SQL Data Warehouse w ramach 15 minut z usługi Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Magazyny danych oparte na plikach
*(Obejmuje magazyn obiektów Blob, magazyn Data Lake Store, Amazon S3, lokalne systemy plików i lokalne systemy HDFS)*

* **Średni rozmiar pliku i liczba plików:** Skopiuj dane przesyła dane po jednym pliku naraz. Z taką samą ilością danych do przeniesienia, całkowita przepływność jest niższa, jeśli dane składają się z wielu małych plików, a nie kilku dużych plików ze względu na fazę bootstrap dla każdego pliku. W związku z tym, jeśli to możliwe, połączyć małe pliki w większe pliki, aby uzyskać wyższą przepustowość.
* **Format pliku i kompresja:** Aby uzyskać więcej sposobów na zwiększenie wydajności, zobacz [zagadnienia dotyczące serializacji i deserializacji](#considerations-for-serialization-and-deserialization) oraz zagadnienia dotyczące sekcji [kompresji.](#considerations-for-compression)
* W przypadku **scenariusza lokalnego systemu plików,** w którym wymagana jest **brama zarządzania danymi,** zobacz sekcję [Uwagi dotyczące bramy zarządzania danymi.](#considerations-for-data-management-gateway)

### <a name="relational-data-stores"></a>Relacyjne magazyny danych
*(Zawiera bazę danych SQL; Magazyn danych SQL; Amazon Redshift; Bazy danych programu SQL Server; i Oracle, MySQL, DB2, Teradata, Sybase i PostgreSQL itp.)*

* **Wzorzec danych:** Schemat tabeli wpływa na przepływność kopiowania. Duży rozmiar wiersza zapewnia lepszą wydajność niż mały rozmiar wiersza, aby skopiować taką samą ilość danych. Powodem jest to, że baza danych można bardziej efektywnie pobierać mniej partii danych, które zawierają mniej wierszy.
* **Kwerenda lub procedura składowana**: Zoptymalizuj logikę kwerendy lub procedury składowanej określonej w źródle działania kopiowania, aby bardziej efektywnie pobierać dane.
* W przypadku **lokalnych relacyjnych baz danych,** takich jak SQL Server i Oracle, które wymagają użycia **bramy zarządzania danymi,** zobacz sekcję Uwagi dotyczące bramy zarządzania danymi.

## <a name="considerations-for-the-sink"></a>Względy dotyczące zlewu
### <a name="general"></a>Ogólne
Upewnij się, że podstawowy magazyn danych nie jest przeciążony przez inne obciążenia, które są uruchomione na lub przeciwko niemu.

W przypadku magazynów danych firmy Microsoft zapoznaj się z [tematami monitorowania i dostrajania,](#performance-reference) które są specyficzne dla magazynów danych. Te tematy mogą pomóc w zrozumieniu charakterystyki wydajności magazynu danych i minimalizowanie czasu reakcji i maksymalizacji przepływności.

W przypadku kopiowania danych z **magazynu obiektów Blob** do **magazynu danych SQL**należy rozważyć użycie **polybase** w celu zwiększenia wydajności. Szczegółowe informacje można znaleźć w programie [PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) Aby uzyskać instruktaż z przypadkiem użycia, zobacz [Ładowanie 1 TB do usługi Azure SQL Data Warehouse w ramach 15 minut z usługi Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Magazyny danych oparte na plikach
*(Obejmuje magazyn obiektów Blob, magazyn Data Lake Store, Amazon S3, lokalne systemy plików i lokalne systemy HDFS)*

* **Zachowanie kopiowania:** Jeśli kopiujesz dane z innego magazynu danych opartego na plikach, działanie kopiowania ma trzy opcje za pośrednictwem właściwości **copyBehavior.** Zachowuje hierarchię, spłaszcza hierarchię lub scala pliki. Hierarchia zachowania lub spłaszczania ma niewielkie obciążenie wydajności lub nie ma na nie większego obciążenia, ale scalanie plików powoduje zwiększenie obciążenia wydajności.
* **Format pliku i kompresja:** Zobacz [zagadnienia dotyczące serializacji i deserializacji](#considerations-for-serialization-and-deserialization) oraz zagadnienia dotyczące sekcji [kompresji,](#considerations-for-compression) aby uzyskać więcej sposobów zwiększenia wydajności.
* **Magazyn obiektów blob:** Obecnie magazyn obiektów Blob obsługuje tylko blokowe obiekty blob dla zoptymalizowanych transferów danych i przepływności.
* W przypadku **scenariuszy lokalnych systemów plików** wymagających użycia **bramy zarządzania danymi**zobacz sekcję Uwagi dotyczące bramy [zarządzania danymi.](#considerations-for-data-management-gateway)

### <a name="relational-data-stores"></a>Relacyjne magazyny danych
*(Zawiera bazę danych SQL, magazyn danych SQL, bazy danych programu SQL Server i bazy danych Oracle)*

* **Zachowanie kopiowania:** W zależności od właściwości ustawionych dla **sqlSink,** działanie kopiowania zapisuje dane do docelowej bazy danych na różne sposoby.
  * Domyślnie usługa przenoszenia danych używa interfejsu API kopiowania zbiorczego do wstawiania danych w trybie dołączania, który zapewnia najlepszą wydajność.
  * Jeśli skonfigurujesz procedurę składowaną w zlewie, baza danych stosuje dane po jednym wierszu naraz, a nie jako obciążenie zbiorcze. Wydajność znacznie spada. Jeśli zestaw danych jest duży, w stosownych przypadkach należy rozważyć przełączenie się na używanie właściwości **sqlWriterCleanupScript.**
  * Jeśli skonfigurujesz właściwość **sqlWriterCleanupScript** dla każdego uruchomienia działania kopiowania, usługa wyzwala skrypt, a następnie użyjesz interfejsu API kopiowania zbiorczego, aby wstawić dane. Na przykład, aby zastąpić całą tabelę najnowszymi danymi, można określić skrypt, aby najpierw usunąć wszystkie rekordy przed zbiorczym ładowaniem nowych danych ze źródła.
* **Wzorzec danych i rozmiar partii:**
  * Schemat tabeli wpływa na przepływność kopiowania. Aby skopiować taką samą ilość danych, duży rozmiar wiersza zapewnia lepszą wydajność niż mały rozmiar wiersza, ponieważ baza danych może bardziej efektywnie zatwierdzać mniej partii danych.
  * Działanie kopiowania wstawia dane w serii partii. Można ustawić liczbę wierszy w partii przy użyciu **writeBatchSize** właściwości. Jeśli dane mają małe wiersze, można ustawić **writeBatchSize** właściwość o wyższej wartości, aby korzystać z niższych narzutów partii i wyższej przepływności. Jeśli rozmiar wiersza danych jest duży, należy zachować ostrożność podczas zwiększania **writeBatchSize**. Wysoka wartość może prowadzić do błędu kopiowania spowodowanego przeciążeniem bazy danych.
* W przypadku **lokalnych relacyjnych baz danych, takich** jak SQL Server i Oracle, które wymagają użycia **bramy zarządzania danymi,** zobacz sekcję [Uwagi dotyczące bramy zarządzania danymi.](#considerations-for-data-management-gateway)

### <a name="nosql-stores"></a>Sklepy NoSQL
*(Zawiera magazyn tabel i usługę Azure Cosmos DB)*

* Do **przechowywania tabel:**
  * **Partycja:** Zapisywanie danych na partycjach z przeplotem znacznie obniża wydajność. Sortuj dane źródłowe według klucza partycji, tak aby dane były skutecznie wstawiane do jednej partycji po drugiej, lub dostosuj logikę, aby zapisać dane na jednej partycji.
* Dla **usługi Azure Cosmos DB:**
  * **Rozmiar partii:** Właściwość **writeBatchSize** ustawia liczbę równoległych żądań usługi Azure Cosmos DB w celu utworzenia dokumentów. Można oczekiwać lepszej wydajności po zwiększeniu **writeBatchSize,** ponieważ więcej równoległych żądań są wysyłane do usługi Azure Cosmos DB. Jednak należy uważać na ograniczanie przepustowości podczas pisania w usłudze Azure Cosmos DB (komunikat o błędzie jest "Szybkość żądania jest duża"). Różne czynniki mogą powodować ograniczanie przepustowości, w tym rozmiar dokumentu, liczbę terminów w dokumentach i zasady indeksowania kolekcji docelowej. Aby osiągnąć wyższą przepływność kopiowania, należy rozważyć użycie lepszej kolekcji, na przykład S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Zagadnienia dotyczące serializacji i deserializacji
Serializacja i deserializacja może wystąpić, gdy zestaw danych wejściowych lub wyjściowy zestaw danych jest plikiem. Zobacz [Obsługiwane formaty plików i kompresji](data-factory-supported-file-and-compression-formats.md) ze szczegółami dotyczącymi obsługiwanych formatów plików według aktywności kopiowania.

**Zachowanie kopiowania**:

* Kopiowanie plików między magazynami danych opartymi na plikach:
  * Gdy zestawy danych wejściowych i wyjściowych mają takie same ustawienia formatu plików lub nie, usługa przenoszenia danych wykonuje kopię binarną bez serializacji lub deserializacji. Widać wyższą przepływność w porównaniu do scenariusza, w którym ustawienia formatu pliku źródła i ujścia różnią się od siebie.
  * Gdy dane wejściowe i wyjściowe zestawy zarówno są w formacie tekstowym i tylko typ kodowania jest inny, usługa przenoszenia danych tylko kodowanie konwersji. Nie wykonuje żadnych serializacji i deserializacji, co powoduje pewne obciążenie wydajności w porównaniu do kopii binarnej.
  * Gdy zarówno zestawy danych wejściowych, jak i wyjściowych mają różne formaty plików lub różne konfiguracje, takie jak ograniczniki, usługa przenoszenia danych deserializuje dane źródłowe do przesyłania strumieniowego, przekształcania, a następnie serializacji do wskazanego formatu wyjściowego. Ta operacja powoduje znacznie bardziej znaczące obciążenie wydajności w porównaniu do innych scenariuszy.
* Podczas kopiowania plików do/z magazynu danych, który nie jest oparty na plikach (na przykład z magazynu opartego na plikach do magazynu relacyjnego), wymagany jest krok serializacji lub deserializacji. Ten krok powoduje znaczne obciążenie wydajności.

**Format pliku**: Wybrany format pliku może mieć wpływ na wydajność kopiowania. Na przykład Avro jest kompaktowym formatem binarnym, który przechowuje metadane z danymi. Ma szerokie poparcie w ekosystemie Hadoop do przetwarzania i wykonywania zapytań. Jednak Avro jest droższe dla serializacji i deserializacji, co powoduje niższą przepustowość kopiowania w porównaniu do formatu tekstu. Dokonaj wyboru formatu pliku w całym przepływie przetwarzania całościowo. Zacznij od tego, w jakiej formie dane są przechowywane, źródłowe magazyny danych lub mają być wyodrębnione z systemów zewnętrznych; najlepszy format przechowywania, przetwarzania analitycznego i zapytań; i w jakim formacie dane powinny być eksportowane do składek danych w celu narzędzi do raportowania i wizualizacji. Czasami format pliku, który jest nieoptymalny dla wydajności odczytu i zapisu może być dobrym wyborem, jeśli wziąć pod uwagę ogólny proces analityczny.

## <a name="considerations-for-compression"></a>Zagadnienia dotyczące kompresji
Gdy zestaw danych wejściowych lub wyjściowych jest plikiem, można ustawić działanie kopiowania, aby wykonywać kompresję lub dekompresję podczas zapisywania danych do miejsca docelowego. Wybierając kompresję, należy dokonać kompromisu między wejściem/wyjściem (We/Wy) a procesorem CPU. Kompresja danych kosztuje dodatkowe zasoby obliczeniowe. Ale w zamian zmniejsza we/wy sieci i pamięci masowej. W zależności od danych może wystąpić wzrost ogólnej przepływności kopiowania.

**Kodek:** Działanie kopiowania obsługuje typy kompresji gzip, bzip2 i Deflate. Usługa Azure HDInsight może korzystać ze wszystkich trzech typów do przetwarzania. Każdy kodek kompresji ma zalety. Na przykład bzip2 ma najniższą przepływność kopiowania, ale otrzymasz najlepszą wydajność zapytania hive z bzip2, ponieważ można podzielić go do przetwarzania. Gzip jest najbardziej zrównoważoną opcją i jest używany najczęściej. Wybierz kodek, który najlepiej odpowiada twojemu scenariuszowi end-to-end.

**Poziom:** Możesz wybrać jedną z dwóch opcji dla każdego kodeka kompresji: najszybszy skompresowany i optymalnie skompresowany. Najszybsza opcja kompresji kompresuje dane tak szybko, jak to możliwe, nawet jeśli wynikowy plik nie jest optymalnie skompresowany. Optymalnie skompresowana opcja spędza więcej czasu na kompresji i daje minimalną ilość danych. Można przetestować obie opcje, aby zobaczyć, który zapewnia lepszą ogólną wydajność w twoim przypadku.

**Uwagę:** Aby skopiować dużą ilość danych między magazynem lokalnym a chmurą, należy rozważyć użycie tymczasowego magazynu obiektów blob z kompresją. Korzystanie z magazynu tymczasowego jest przydatne, gdy przepustowość sieci firmowej i usług platformy Azure jest czynnikiem ograniczającym i chcesz, aby zestaw danych wejściowych i dane wyjściowe były w formie nieskompresowanej. W szczególności można podzielić działanie pojedynczej kopii na dwie czynności kopiowania. Pierwsze działanie kopiowania jest kopiowane ze źródła do tymczasowego lub przemieszczania obiektu blob w postaci skompresowanej. Drugie działanie kopiowania kopiuje skompresowane dane z przemieszczania, a następnie dekompresuje podczas zapisu do ujścia.

## <a name="considerations-for-column-mapping"></a>Zagadnienia dotyczące mapowania kolumn
Można ustawić **columnMappings** właściwość w copy activity do mapowania wszystkich lub podzbiór kolumn wejściowych do kolumn wyjściowych. Po usługa przenoszenia danych odczytuje dane ze źródła, musi wykonać mapowanie kolumn na danych, zanim zapisuje dane do ujścia. To dodatkowe przetwarzanie zmniejsza przepływność kopiowania.

Jeśli źródłowy magazyn danych jest queryable, na przykład, jeśli jest to magazyn relacyjne, takich jak baza danych SQL lub SQL Server lub jeśli jest to magazyn NoSQL, takich jak magazyn tabel lub usługi Azure Cosmos DB, należy rozważyć wypychanie filtrowania kolumn i zmieniania kolejności logiki do właściwości **kwerendy** zamiast przy użyciu mapowania kolumn. W ten sposób projekcji występuje, gdy usługa przenoszenia danych odczytuje dane z magazynu danych źródłowych, gdzie jest znacznie bardziej wydajne.

## <a name="other-considerations"></a>Inne zagadnienia
Jeśli rozmiar danych, które chcesz skopiować jest duży, można dostosować logikę biznesową do dalszej partycji danych przy użyciu mechanizmu krojenia w fabryce danych. Następnie zaplanuj częstsze uruchamianie działania kopiowania w celu zmniejszenia rozmiaru danych dla każdego uruchomienia działania kopiowania.

Należy zachować ostrożność przy liczebnie zestawów danych i kopiuj działania wymagające usługi Data Factory do łącznika do tego samego magazynu danych w tym samym czasie. Wiele równoczesnych zadań kopiowania może ograniczyć magazyn danych i prowadzić do obniżenia wydajności, kopiowania wewnętrznych ponownych prób zadania, a w niektórych przypadkach błędów wykonywania.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Przykładowy scenariusz: kopiowanie z lokalnego magazynu programu SQL Server do obiektów Blob
**Scenariusz:** Potok jest zbudowany w celu kopiowania danych z lokalnego magazynu SQL Server do magazynu obiektów Blob w formacie CSV. Aby przyspieszyć zadanie kopiowania, pliki CSV powinny być skompresowane do formatu bzip2.

**Test i analiza:** Przepływność działania kopiowania jest mniejsza niż 2 MB/s, co jest znacznie wolniejsze niż wskaźnik wydajności.

**Analiza wydajności i dostrajanie:** Aby rozwiązać problem z wydajnością, przyjrzyjmy się, jak dane są przetwarzane i przenoszone.

1. **Odczyt danych:** Brama otwiera połączenie z programem SQL Server i wysyła zapytanie. SQL Server odpowiada, wysyłając strumień danych do bramy za pośrednictwem intranetu.
2. **Serializacja i kompresowanie danych:** Brama serializuje strumień danych do formatu CSV i kompresuje dane do strumienia bzip2.
3. **Zapisuj dane:** Brama przesyła strumień bzip2 do magazynu obiektów Blob za pośrednictwem Internetu.

Jak widać, dane są przetwarzane i przenoszone w sposób sekwencyjny przesyłania strumieniowego: SQL Server > LAN > Gateway > magazynu obiektów Blob > WAN. **Ogólna wydajność jest bramowana przez minimalną przepustowość w potoku.**

![Przepływ danych](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Co najmniej jeden z następujących czynników może spowodować wąskie gardło wydajności:

* **Źródło**: SQL Server sam ma niską przepustowość z powodu dużych obciążeń.
* **Brama zarządzania danymi:**
  * **LAN**: Brama znajduje się z dala od komputera SQL Server i ma połączenie o niskiej przepustowości.
  * **Brama:** Brama osiągnęła swoje ograniczenia obciążenia, aby wykonać następujące operacje:
    * **Serializacja:** Serializacja strumienia danych do formatu CSV ma powolną przepustowość.
    * **Kompresja:** Wybrano powolny kodek kompresji (na przykład bzip2, który jest 2,8 MB/s z Core i7).
  * **WAN:** Przepustowość między siecią firmową a usługami platformy Azure jest niska (na przykład T1 = 1544 kbps; T2 = 6312 kb/s).
* **Ujście:** Magazyn obiektów blob ma niską przepustowość. (Ten scenariusz jest mało prawdopodobny, ponieważ jego umowy SLA gwarantuje co najmniej 60 MB/s.

W takim przypadku kompresja danych bzip2 może spowalniać cały potok. Przełączenie na kodek kompresji gzip może ułatwić to wąskie gardło.

## <a name="sample-scenarios-use-parallel-copy"></a>Przykładowe scenariusze: używanie kopii równoległej
**Scenariusz I:** Skopiuj 1000 plików o rozmiarze 1 MB z lokalnego systemu plików do magazynu obiektów Blob.

**Analiza i dostrajanie wydajności:** Na przykład, jeśli zainstalowano bramę na komputerze czterordzeniowym, usługa Data Factory używa 16 kopii równoległych do jednoczesnego przenoszenia plików z systemu plików do magazynu obiektów Blob. To wykonanie równoległe powinno spowodować wysoką przepływność. Można również jawnie określić liczbę kopii równoległych. Podczas kopiowania wielu małych plików, kopie równoległe znacznie pomóc przepływności przy użyciu zasobów bardziej efektywnie.

![Scenariusz 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenariusz II:** Skopiuj 20 obiektów blob po 500 MB z magazynu obiektów Blob do usługi Data Lake Store Analytics, a następnie dostosuj wydajność.

**Analiza i dostrajanie wydajności:** W tym scenariuszu usługa Data Factory kopiuje dane z magazynu obiektów Blob do magazynu usługi Data Lake przy użyciu jednostek jednokopiowych **(parallelCopies ustawionych** na 1) i jednostek przenoszenia danych w jednej chmurze. Przepływność, którą obserwujesz, będzie zbliżona do opisanej w [sekcji odwołania do wydajności](#performance-reference).

![Scenariusz 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scenariusz III:** Rozmiar pojedynczego pliku jest większy niż kilkadziesiąt MB, a całkowita objętość jest duża.

**Analiza i obracanie wydajności:** Zwiększenie **równoległychcopies** nie powoduje lepszej wydajności kopiowania ze względu na ograniczenia zasobów dmu pojedynczej chmury. Zamiast tego należy określić więcej dmu chmury, aby uzyskać więcej zasobów do wykonywania przenoszenia danych. Nie należy określać wartość dla **parallelCopies** właściwości. Usługa Data Factory obsługuje równoległość dla Ciebie. W takim przypadku jeśli ustawisz **cloudDataMovementUnits** na 4, przepływność około cztery razy występuje.

![Scenariusz 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Tematy pomocy
Poniżej przedstawiono odwołania do monitorowania wydajności i dostrajania niektórych obsługiwanych magazynów danych:

* Magazyn obiektów Blob platformy Azure: [cele dotyczące skalowalności i wydajności magazynu obiektów Blob](../../storage/blobs/scalability-targets.md) oraz [listy kontrolnej wydajności i skalowalności magazynu obiektów Blob.](../../storage/blobs/storage-performance-checklist.md)
* Magazyn tabel platformy Azure: [cele dotyczące skalowalności i wydajności dla magazynu tabel](../../storage/tables/scalability-targets.md) oraz listy [kontrolnej wydajności i skalowalności dla magazynu tabel](../../storage/tables/storage-performance-checklist.md).
* Usługa Azure SQL Database: można [monitorować wydajność](../../sql-database/sql-database-single-database-monitor.md) i sprawdzać procent jednostki transakcji bazy danych (DTU)
* Usługa Azure SQL Data Warehouse: jego możliwości są mierzone w jednostkach magazynu danych (DWU); zobacz [Zarządzanie mocą obliczeniową w usłudze Azure SQL Data Warehouse (Omówienie)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Usługa Azure Cosmos DB: [poziomy wydajności w usłudze Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* Lokalny program SQL Server: [monitorowanie i dostosowywanie wydajności](https://msdn.microsoft.com/library/ms189081.aspx)
* Lokalny serwer plików: [dostrajanie wydajności serwerów plików](https://msdn.microsoft.com/library/dn567661.aspx)
