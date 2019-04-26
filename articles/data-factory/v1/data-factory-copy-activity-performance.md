---
title: Skopiuj wydajności i działania przewodnika dostrajania | Dokumentacja firmy Microsoft
description: Więcej informacji na temat kluczowych czynników wpływających na wydajność przenoszenia danych w usłudze Azure Data Factory, korzystając z działania kopiowania.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ec8c58e4ced0d8df958e242b9c1671aeed8c2ee6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60488224"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Skopiuj wydajności i działania przewodnika dostrajania

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-copy-activity-performance.md)
> * [Wersja 2 (bieżąca wersja)](../copy-activity-performance.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [skopiuj wydajności i działania przewodnika dostrajania dla usługi Data Factory](../copy-activity-performance.md).

Działanie kopiowania w usłudze Azure fabryki danych zapewnia najwyższej jakości danych bezpieczny, niezawodny i wydajny ładowania rozwiązania. Go umożliwia dziesiątki kopię terabajtów danych każdego dnia w wielu różnych chmury i rozwiązań lokalnych magazynów danych. Błyskawiczne danych wydajność ładowania ma kluczowe znaczenie dla zapewnienia możesz skupić się na główny problem "dane big data": tworzenie zaawansowanych rozwiązań analizy i uzyskiwanie szczegółowych informacji z wszystkie te dane.

Platforma Azure udostępnia zestaw przeznaczonych dla przedsiębiorstw rozwiązaniach magazynu danych magazynu i danych, a działanie kopiowania oferuje wysoce zoptymalizowane środowiska, który jest łatwy do skonfigurowania i ładowaniem. Przy użyciu tylko jednej kopii działania programu można osiągnąć:

* Ładowanie danych do **Azure SQL Data Warehouse** na **1,2 GB/s**. Aby uzyskać wskazówki z przypadkami użycia, zobacz [ładowanie 1 TB w usłudze Azure SQL Data Warehouse z mniej niż 15 minut przy użyciu usługi Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Ładowanie danych do **usługi Azure Blob storage** na **1,0 GB/s**
* Ładowanie danych do **usługi Azure Data Lake Store** na **1,0 GB/s**

W tym artykule opisano:

* [Numery identyfikacyjne wydajności](#performance-reference) obsługiwane źródło i ujście magazynu danych, aby ułatwić planowanie projektu.
* Funkcje, które może zwiększyć przepływność kopii w różnych scenariuszach, w tym [jednostek przenoszenia danych w chmurze](#cloud-data-movement-units), [równoległych kopii](#parallel-copy), i [kopiowania etapowego](#staged-copy);
* [Wskazówki dotyczące dostrajania wydajności](#performance-tuning-steps) dotyczące dostrajania wydajności i kluczowych czynników, które mogą mieć wpływ na wydajność kopii.

> [!NOTE]
> Jeśli nie jesteś zaznajomiony z działaniem kopiowania ogólnie rzecz biorąc, zobacz [przenoszenie danych za pomocą działania kopiowania](data-factory-data-movement-activities.md) przed przeczytaniem tego artykułu.
>

## <a name="performance-reference"></a>Informacje dotyczące wydajności

Jako odwołanie Poniższa tabela zawiera numer przepływności kopiowania w MB/s dla danego źródła i ujścia par testów wewnętrznych. Dla porównania ilustruje też sposób różnymi ustawieniami [jednostek przenoszenia danych w chmurze](#cloud-data-movement-units) lub [skalowalność bramy zarządzania danymi](data-factory-data-management-gateway-high-availability-scalability.md) (wiele węzłów bramy) może pomóc w wydajności kopiowania.

![Macierz wydajności](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>W usłudze Azure Data Factory w wersji 1 jednostek przenoszenia danych w chmurze minimalny związanym z kopiowaniem chmury z chmurą wynosi dwa. Jeśli nie zostanie określony, zobacz jednostek przenoszenia danych na domyślne używane w [jednostek przenoszenia danych w chmurze](#cloud-data-movement-units).

**Informacje, które należy zwrócić uwagę:**
* Obliczana jest przepływność przy użyciu następującej formuły: [rozmiar danych do odczytu ze źródła] / [czas uruchomienia działania kopiowania].
* Numery odniesienia wydajności w tabeli są mierzone przy użyciu [TPC-H](http://www.tpc.org/tpch/) działanie kopiowania pojedynczy zestaw danych.
* W magazynach danych platformy Azure źródła i ujścia znajdują się w tym samym regionie platformy Azure.
* Związanym z kopiowaniem hybrydowej między lokalizacją lokalną i chmurą magazyny danych, każdy węzeł bramy była uruchomiona na komputerze, na którym został oddzielnie od magazynu danych w środowisku lokalnym za pomocą poniżej specyfikacji. Gdy jedno działanie był uruchomiony w bramie, operacji kopiowania wykorzystywane tylko niewielką część procesora CPU, pamięć lub przepustowość sieci testowej maszyny. Dowiedz się więcej z [ważną kwestią dotyczącą bramy zarządzania danymi](#considerations-for-data-management-gateway).
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
> Można osiągnąć większą przepływność dzięki wykorzystaniu więcej jednostek przenoszenia danych w (DMUs) niż domyślna maksymalna DMUs, czyli 32 w przypadku uruchomienia działania kopiowania w chmurze do chmury. Na przykład ze 100 DMUs możesz osiągnąć kopiowania danych z obiektów Blob platformy Azure do usługi Azure Data Lake Store w **1.0GBps**. Zobacz [jednostek przenoszenia danych w chmurze](#cloud-data-movement-units) sekcji, aby uzyskać szczegółowe informacje na temat tej funkcji i obsługiwany scenariusz. Skontaktuj się z pomocą [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/) żądania DMUs więcej.

## <a name="parallel-copy"></a>Kopiuj równoległe
Można odczytać danych ze źródła lub zapisu danych do lokalizacji docelowej **równolegle w ramach uruchomienia działania kopiowania**. Ta funkcja zwiększa przepływność operacji kopiowania i skraca czas potrzebny do przenoszenia danych.

To ustawienie jest inny niż **współbieżności** właściwości w definicji działania. **Współbieżności** właściwości określa liczbę **uruchomieniu działania kopiowania współbieżnych** do przetwarzania danych z innego działania systemu windows (1: 00 do 2: 00, 2 AM do 3 AM, 3 AM do 4 AM i tak dalej). Ta możliwość jest przydatne, gdy wykonujesz historycznych obciążenia. Możliwość kopiowania równoległe dotyczy **pojedynczego uruchomienia działania**.

Przyjrzyjmy się przykładowy scenariusz. W poniższym przykładzie wielu wycinki wcześniejsze muszą być przetworzone. Fabryki danych jest uruchamiane wystąpienie działania kopiowania (uruchomienia działania) dla każdego wycinka:

* Wycinek danych z okna pierwsze działanie (1: 00 do 2: 00) == > 1 uruchamiania działania
* Wycinek danych z drugiego okna działania (2: 00 do 3: 00) == > 2 uruchamiania działania
* Wycinek danych z drugiego okna działania (3: 00 do 4: 00) == > 3 uruchamiania działania

I tak dalej.

W tym przykładzie po **współbieżności** wartość jest równa 2, **1 uruchamiania działania** i **2 uruchamiania działania** skopiować dane z dwóch okien działania **jednocześnie** do poprawy wydajności przenoszenia danych. Jednak jeśli skojarzonych kilka plików za pomocą działania Uruchom 1 usługi data movement service kopiuje pliki ze źródła do jednego pliku docelowego w danym momencie.

### <a name="cloud-data-movement-units"></a>Jednostek przenoszenia danych w chmurze
A **jednostek przenoszenia danych w chmurze (DMU)** jest miarą, który reprezentuje możliwości (kombinację procesora CPU, pamięci i alokacji zasobów sieciowych) w pojedynczą jednostkę w usłudze Data Factory. Dotyczy to DMU dla operacji kopiowania w chmurze do chmury, ale nie w hybrydowej kopii.

**Jednostek przenoszenia danych w chmurze minimalny przeznaczonych dla uruchomienia działania kopiowania wynosi dwa.** Jeśli nie zostanie określony, w poniższej tabeli wymieniono DMUs domyślne używane w scenariuszach różnych kopii:

| Skopiuj scenariusza | Domyślne DMUs określany przez usługę |
|:--- |:--- |
| Kopiowanie danych między magazynami oparte na plikach | Od 4 do 16, w zależności od liczby i rozmiaru plików. |
| Innych scenariuszach kopiowania | 4 |

Aby zastąpić to ustawienie domyślne, należy określić wartość dla **cloudDataMovementUnits** właściwości w następujący sposób. **Dozwolone wartości** dla **cloudDataMovementUnits** właściwości są 2, 4, 8, 16, 32. **Rzeczywista liczba chmury DMUs** używany w czasie wykonywania operacji kopiowania jest równa lub mniejsza niż skonfigurowana wartość, w zależności od wzorca usługi danych. Aby uzyskać informacje na temat poziomu wydajności, może zostać wyświetlony po skonfigurowaniu większej liczby jednostek dla określonej kopii źródła i ujścia, zobacz [dotyczące wydajności](#performance-reference).

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
> Jeśli potrzebujesz więcej chmury DMUs większej przepływności, skontaktuj się z [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/). Obecnie działa tylko wtedy, gdy ustawienie 8 i nowszych możesz **skopiować wielu plików z obiektu Blob magazynu/Data Lake Store/Amazon S3/w chmurze/w chmurze FTP, SFTP do obiektu Blob magazynu/Data Lake Store/Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies
Możesz użyć **parallelCopies** właściwości do wskazania równoległości, który działanie kopiowania w celu użycia. Tę właściwość można traktować jako maksymalną liczbę wątków w ramach działania kopiowania, które można odczytać ze źródła lub zapisywać swoich magazynów danych ujścia równolegle.

Dla każdego uruchomienia działania kopiowania usługi fabryka danych określa liczbę równoległych kopii na potrzeby kopiowania danych ze źródła danych przechowywane i danych docelowego. Domyślna liczba równoległych kopii, które używa zależy od typu źródła i ujścia, którego używasz.

| Źródła i ujścia | Domyślna liczba równoległych kopii określany przez usługę |
| --- | --- |
| Kopiowanie danych między magazynami oparte na plikach (usługi Blob storage; Data Lake Store; Amazon S3; lokalny system plików; w środowisku lokalnym systemem plików HDFS) |Od 1 do 32. Zależy od rozmiaru plików i liczby jednostek przenoszenia danych w chmurze (DMUs) umożliwia kopiowanie danych między dwoma magazynami danych w chmurze lub fizyczną konfigurację maszyny bramy służącą do kopii hybrydowej (kopiowanie danych do / z lokalnego magazynu danych). |
| Kopiowanie danych z **dowolnego źródła danych przechowywane w usłudze Azure Table storage** |4 |
| Wszystkie inne źródła i ujścia pary |1 |

Zazwyczaj domyślne zachowanie powinien zapewnić najlepsze przepływności. Jednak do kontrolowania obciążenia na maszynach obsługujących Twoje dane są przechowywane, albo można dostrajanie wydajności kopiowania, możesz zastąpić wartość domyślną, a następnie określić wartość dla **parallelCopies** właściwości. Wartość musi być z zakresu od 1 do 32 (oba włącznie). W czasie wykonywania Aby uzyskać najlepszą wydajność, działanie kopiowania używa wartość, która jest mniejsza niż lub równa wartości, który został ustawiony.

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
Informacje, które należy zwrócić uwagę:

* Podczas kopiowania danych między magazynami oparte na plikach, **parallelCopies** określić równoległości na poziomie plików. Segmentu w pojedynczym pliku sytuacja może mieć miejsce poniżej automatycznie i w sposób niewidoczny dla użytkownika, a zostało zaprojektowane na potrzeby ładowania danych w równoległych i prostopadły do parallelCopies najlepsze rozmiar fragmentu odpowiedniego dla typu magazynu danego źródła danych. Rzeczywista liczba równoległych kopii usługi data movement service używa dla operacji kopiowania w czasie wykonywania jest nie więcej niż liczba plików, których masz. Jeśli zachowanie kopiowania to **mergeFile**, działanie kopiowania nie mogą korzystać z równoległości na poziomie plików.
* Po określeniu wartości **parallelCopies** właściwość, należy wziąć pod uwagę wzrost obciążenia na źródło i ujście magazynu danych, a także do bramy, jest to kopia hybrydowych. Dzieje się tak szczególnie, gdy masz wiele działań lub równoczesnych uruchomień działań uruchamianych w odniesieniu do tego samego magazynu danych. Jeśli okaże się, że magazyn danych lub bramy jest przeciążony przy obciążeniu, Zmniejsz **parallelCopies** wartości do zmniejszenia obciążenia.
* Podczas kopiowania danych z magazynów, które nie są opartą na plikach do magazynów, które są oparte na plikach, usługi data movement service ignoruje **parallelCopies** właściwości. Nawet jeśli równoległości jest określona, nie zostanie zastosowane w tym przypadku.

> [!NOTE]
> Aby użyć, należy użyć bramy zarządzania danymi wersji 1.11 lub nowszej **parallelCopies** funkcji po wykonaniu kopii hybrydowej.
>
>

Lepiej korzystać z tych dwóch właściwości i zwiększyć przepływność przenoszenia danych, zobacz przykładowe przypadki użycia. Nie trzeba konfigurować **parallelCopies** z zalet domyślnego zachowania. Jeśli skonfigurujesz i **parallelCopies** jest zbyt mały, wiele chmur DMUs może nie w pełni wykorzystywane.

### <a name="billing-impact"></a>Opłaty wpływ
Ma ona **ważne** należy pamiętać, że opłaty są naliczane na podstawie czasu łączna liczba operacji kopiowania. Jeśli zadanie kopiowania używany do podejmowania jedną godzinę z jednostką jedna chmura, a obecnie zajmuje 15 minut za cztery jednostki chmury, ogólną kwotę rachunku pozostanie prawie takie same. Na przykład możesz użyć cztery jednostki chmury. Pierwszy jednostki chmury — 10 minut, druga, 10 minut, trzecie 5 minut i czwarta, co 5 minut, uruchom wszystko w jednym działaniem kopiowania. Opłata jest naliczana raz całkowita kopiowania (przenoszenia danych), czyli 10 + 10 + 5 + 5 = 30 minut. Za pomocą **parallelCopies** nie ma wpływu na rozliczenia.

## <a name="staged-copy"></a>Kopiowania przejściowego
Podczas kopiowania danych z magazynu danych źródłowych do magazynu danych ujścia, można użyć magazynu obiektów Blob jako tymczasowego magazynu przejściowego. Przemieszczania jest szczególnie użyteczna w następujących przypadkach:

1. **Aby pozyskiwać dane z różnych magazynów danych do usługi SQL Data Warehouse za pomocą programu PolyBase**. Usługa SQL Data Warehouse używa programu PolyBase jako mechanizm o wysokiej przepływności ładowanie dużej ilości danych do usługi SQL Data Warehouse. Źródło danych musi być w magazynie obiektów Blob i musi spełniać dodatkowe kryteria. Podczas ładowania danych z magazynu danych innego niż magazynu obiektów Blob, możesz aktywować dane kopiowanie za pośrednictwem tymczasowego przejściowego magazynu obiektów Blob. W takiej sytuacji usługi fabryka danych wykonuje przekształcenia danych wymagane, aby upewnić się, że spełnia wymagania programu PolyBase. Następnie używa programu PolyBase do ładowania danych do usługi SQL Data Warehouse. Aby uzyskać więcej informacji, zobacz [przy użyciu technologii PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). Aby uzyskać wskazówki z przypadkami użycia, zobacz [ładowanie 1 TB w usłudze Azure SQL Data Warehouse z mniej niż 15 minut przy użyciu usługi Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Czasami potrzebny na przeprowadzenie hybrydowe przenoszenie danych (oznacza to, aby skopiować między lokalnymi danymi magazynu i danych w chmurze w sklepie) za pośrednictwem z wolnym połączeniem sieciowym**. Aby zwiększyć wydajność, można skompresować danych w sieci lokalnej, aby zajmuje mniej czasu na przenoszenie danych do przejściowy magazyn danych w chmurze. Następnie można zdekompresować danych w magazynie przemieszczania, zanim je załadujesz do docelowego magazynu danych.
3. **Nie chcesz otworzyć porty inne niż port 80 i portu 443 w zaporze, ze względu na zasady firmowe IT**. Na przykład podczas kopiowania danych z lokalnego magazynu danych ujścia Azure SQL Database lub ujścia Azure SQL Data Warehouse, musisz aktywować komunikacja wychodząca TCP na porcie 1433 dla zapory Windows i zaporą firmową. W tym scenariuszu skorzystaj z bramy do pierwszego kopiowania danych do wystąpienia przejściowego magazynu obiektów Blob za pośrednictwem protokołu HTTP lub HTTPS na porcie 443. Następnie należy załadować dane do bazy danych SQL Database lub SQL Data Warehouse z obszaru przemieszczania magazynu obiektów Blob. W tym przepływie nie trzeba włączyć port 1433.

### <a name="how-staged-copy-works"></a>Jak przygotowanych działania kopiowania
Podczas aktywowania funkcji przemieszczania najpierw dane są kopiowane z magazynu danych źródłowych w magazynie danych przemieszczania (skorzystaj z własnych). Następnie dane są kopiowane z przejściowy magazyn danych do magazynu danych ujścia. Data Factory automatycznie zarządza przepływem dwuetapowego dla Ciebie. Usługi Data Factory czyści dane tymczasowe z magazynu przejściowego także po ukończeniu przenoszenia danych.

Brama nie jest używany w scenariuszu kopii do chmury (zarówno źródła i ujścia dane, które sklepy znajdują się w chmurze). Usługa Data Factory wykonuje operacje kopiowania.

![Kopiowania przejściowego: Scenariusz chmury](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

W scenariuszu kopii hybrydowej (źródło znajduje się w środowisku lokalnym i ujścia znajduje się w chmurze), bramy przenosi dane z magazynu danych źródłowych do przejściowy magazyn danych. Usługa Data Factory przenosi dane z przejściowy magazyn danych do magazynu danych ujścia. Kopiowanie danych z magazynem danych w chmurze do lokalnego magazynu danych za pośrednictwem wdrażania przejściowego jest obsługiwana także z przepływem odwróconej.

![Kopiowania przejściowego: Scenariusza hybrydowego](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Gdy aktywujesz przenoszenie danych za pomocą magazynu przejściowego można określić, czy chcesz, aby dane skompresowane przed przenosi dane z magazynu danych źródłowych do magazynu danych tymczasowych lub tymczasowej, a następnie dekompresowane przed przystąpieniem do przenoszenia danych z przejściowej lub przemieszczania danych magazynu do magazynu danych ujścia.

Obecnie nie można skopiować danych między dwoma magazynami danych w środowisku lokalnym za pomocą magazynu przejściowego. Oczekujemy, że ta opcja była dostępna wkrótce.

### <a name="configuration"></a>Konfigurowanie
Konfigurowanie **enableStaging** ustawienie w działaniu kopiowania, aby określić, czy dane zostaną umieszczone w magazynie obiektów Blob, zanim je załadujesz do docelowego magazynu danych. Po ustawieniu **enableStaging** ma wartość TRUE, określić dodatkowe właściwości, które są wymienione w następnej tabeli. Jeśli nie masz, musisz również utworzyć usługi Azure Storage lub magazynu udostępnionego usługi połączonej podpis dostępu dla przemieszczania.

| Właściwość | Opis | Wartość domyślna | Wymagane |
| --- | --- | --- | --- |
| **enableStaging** |Określ, czy chcesz skopiować dane za pośrednictwem tymczasowego magazynu przejściowego. |False |Nie |
| **linkedServiceName** |Określ nazwę [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) lub [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) połączone usługi, która odnosi się do wystąpienia magazynu, którego używasz jako tymczasowy magazyn przejściowy. <br/><br/> Nie można używać magazynu przy użyciu sygnatury dostępu współdzielonego, aby załadować dane do usługi SQL Data Warehouse za pomocą programu PolyBase. Można go użyć w innych scenariuszach. |ND |Tak, gdy **enableStaging** jest ustawiona na wartość TRUE |
| **Ścieżka** |Określ ścieżkę magazynu obiektów Blob, która ma zawierać użycia przemieszczonych danych. Jeśli ścieżka nie zostanie określona, usługa tworzy kontener do przechowywania danych tymczasowych. <br/><br/> Określ ścieżkę, tylko wtedy, gdy używasz magazynu przy użyciu sygnatury dostępu współdzielonego lub wymagają danych tymczasowych w określonej lokalizacji. |ND |Nie |
| **enableCompression** |Określa, czy dane powinny zostać skompresowane przed skopiowaniem ich do miejsca docelowego. To ustawienie powoduje zmniejszenie ilości przesyłanych danych. |False |Nie |

Oto przykładowa definicja działania kopiowania przy użyciu właściwości, które są opisane w powyższej tabeli:

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

### <a name="billing-impact"></a>Opłaty wpływ
Opłaty są naliczane na podstawie dwóch kroków: czas trwania kopiowania i skopiuj typu.

* Korzystając z przemieszczania podczas kopiowania chmury (kopiowanie danych z magazynem danych w chmurze do innego magazynu danych w chmurze), rozliczenie nastąpi [łączny czas trwania kopiowania kroki 1 i 2] x [Cena jednostkowa kopiowania chmury].
* Korzystając z przemieszczania podczas kopiowania hybrydowe (kopiowanie danych z lokalnego magazynu danych do magazynu danych w chmurze), opłaty są naliczane za [hybrydowej kopii czas trwania] x [Cena jednostkowa hybrydowej kopii] + [w chmurze czas kopiowania] x [Cena jednostkowa kopiowania chmury].

## <a name="performance-tuning-steps"></a>Kroki dostosowywania wydajności
Sugerujemy, wykonaj następujące kroki, aby dostosować wydajność usługi Data Factory za pomocą działania kopiowania:

1. **Ustalenie linii bazowej**. Podczas fazy opracowywania testowanie potoku za pomocą działania kopiowania względem przykładowych danych. Możesz użyć usługi Data Factory [model tworzenia wycinków](data-factory-scheduling-and-execution.md) Aby ograniczyć ilość danych możesz pracować.

   Zbieranie czasu wykonywania i charakterystyk wydajności przy użyciu **monitorowanie i zarządzanie aplikacjami**. Wybierz **monitorowanie i zarządzanie** na stronie głównej fabryki danych. W widoku drzewa wybierz **wyjściowy zestaw danych**. W **Windows działanie** listy, wybierz polecenie uruchomienia działania kopiowania. **Windows działanie** Wyświetla czas trwania działania kopiowania i rozmiaru danych, który jest kopiowany. Przepływność jest wymieniony w **Eksploratorze okien działania**. Aby dowiedzieć się więcej o aplikacji, zobacz [monitorowanie potoków i zarządzanie nimi usługi Azure Data Factory przy użyciu aplikacji do zarządzania i monitorowania](data-factory-monitor-manage-app.md).

   ![Szczegóły uruchamiania działania](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   W dalszej części tego artykułu, możesz porównać wydajność i konfiguracji scenariusza z działaniem kopiowania [dotyczące wydajności](#performance-reference) z naszych testach.
2. **Diagnozowanie i zoptymalizować wydajność**. Jeśli wydajność, której możesz obserwować nie spełniają Twoich oczekiwań, musisz określić wąskie gardła wydajności. Następnie zoptymalizować wydajność, aby usunąć lub zmniejszają efekt wąskich gardeł. Pełny opis Diagnostyka wydajności wykracza poza zakres tego artykułu, ale poniżej przedstawiono niektóre typowe kwestie dotyczące:

   * Funkcje wydajności:
     * [Kopiuj równoległe](#parallel-copy)
     * [Jednostek przenoszenia danych w chmurze](#cloud-data-movement-units)
     * [Kopiowania przejściowego](#staged-copy)
     * [Skalowalność bramy zarządzania danych](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Brama zarządzania danymi](#considerations-for-data-management-gateway)
   * [Element źródłowy](#considerations-for-the-source)
   * [obiekt sink](#considerations-for-the-sink)
   * [Serializacja i deserializacja](#considerations-for-serialization-and-deserialization)
   * [Kompresja](#considerations-for-compression)
   * [Mapowanie kolumn](#considerations-for-column-mapping)
   * [Inne zagadnienia](#other-considerations)
3. **Rozwiń węzeł Konfiguracja do całego zestawu danych**. Gdy jesteś zadowolony z wyników wykonania i wydajności, można rozwinąć definicji i okres aktywności potoku w celu pokrycia całego zestawu danych.

## <a name="considerations-for-data-management-gateway"></a>Zagadnienia dotyczące bramy zarządzania danymi
**Instalator bramy**: Zaleca się, że używasz dedykowane maszyny na hoście bramy zarządzania danymi. Zobacz [zagadnienia związane z używaniem bramy zarządzania danymi](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Brama monitorowania i skalowania w górę/w poziomie**: Pojedyncza brama logicznych z co najmniej jeden węzeł bramy może obsługiwać wielu uruchomień działania kopiowania w tym samym czasie jednocześnie. Możesz wyświetlić migawki niemal w czasie rzeczywistym wykorzystania zasobów (procesor CPU, pamięci, network(in/out), itp.) na maszynie bramy, a także liczbę współbieżnych zadań uruchamiania i limit w witrynie Azure portal, zobacz [bramy monitorowanie w portalu](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Jeśli masz duże potrzebę na hybrydowe przenoszenie danych z dużą liczbą jednoczesnych kopii uruchomienia działania lub z dużej ilości danych do skopiowania, należy wziąć pod uwagę do [skalowanie w górę lub w poziomie bramy](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) tak, aby lepiej wykorzystywać zasobu lub, aby ustanowić więcej zasobów przeznaczonych dla kopiowania.

## <a name="considerations-for-the-source"></a>Zagadnienia dotyczące źródła
### <a name="general"></a>Ogólne
Pamiętaj, że źródłowy magazyn danych nie jest przeciążony innych obciążeń, które są uruchomione w lub przed nim.

Dla magazynów danych firmy Microsoft, zobacz [monitorowania i dostrajania tematy](#performance-reference) specyficznych dla magazynów danych i pomagają zrozumieć dane przechowywane charakterystyki wydajności, zminimalizować czas reakcji i zmaksymalizowania wydajności.

Jeśli kopiowanie danych z magazynu obiektów Blob w usłudze SQL Data Warehouse, należy rozważyć użycie **PolyBase** do poprawienia wydajności. Zobacz [przy użyciu technologii PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) Aby uzyskać szczegółowe informacje. Aby uzyskać wskazówki z przypadkami użycia, zobacz [ładowanie 1 TB w usłudze Azure SQL Data Warehouse z mniej niż 15 minut przy użyciu usługi Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Magazyny danych opartych na plikach
*(W tym magazyn obiektów Blob, Data Lake Store, Amazon S3, systemy plików lokalnych i lokalnym systemem plików HDFS)*

* **Średni rozmiar pliku i liczba plików**: Działanie kopiowania przekazuje jeden plik danych w danym momencie. Przy użyciu tej samej ilości danych do przeniesienia ogólną przepustowość jest mniejszy, jeśli dane składa się z wielu małych plików, a nie kilka dużych plików z powodu fazie uruchamiania dla każdego pliku. W związku z tym jeśli to możliwe, łączenia małych plików do większych plikach w celu uzyskania większej przepływności.
* **Plik formatowania i kompresji**: Aby uzyskać więcej sposobów na zwiększenie wydajności, zobacz [zagadnienia dotyczące serializacji i deserializacji](#considerations-for-serialization-and-deserialization) i [zagadnienia dotyczące kompresji](#considerations-for-compression) sekcje.
* Dla **lokalnego systemu plików** scenariusz, w którym **bramy zarządzania danymi** jest wymagane, zobacz [zagadnienia dotyczące bramy zarządzania danymi](#considerations-for-data-management-gateway) sekcji.

### <a name="relational-data-stores"></a>Magazyny danych relacyjnych
*(W tym bazy danych SQL. Usługa SQL Data Warehouse; Usługi Amazon Redshift; Bazy danych programu SQL Server; i Oracle, MySQL, DB2, Teradata, Sybase i PostgreSQL bazami danych itp.)*

* **Wzorzec danych**: Schemat tabeli ma wpływ na przepływność kopiowania. Rozmiar wiersza dużych zapewnia lepszą wydajność niż rozmiar wiersza mały, aby skopiować podobną ilość danych. Przyczyną jest to, że baza danych wydajniej można pobrać mniejszej liczby partii danych, które zawierają mniej wierszy.
* **Zapytanie lub procedura składowana**: Optymalizuj logiki kwerendy lub procedury składowanej, określonego w źródle działania kopiowania można pobrać danych bardziej efektywnie.
* Dla **lokalnych relacyjnych baz danych**, takich jak SQL Server i Oracle, które korzystają z **bramy zarządzania danymi**, zobacz sekcję Uwagi w sekcji bramy zarządzania danymi.

## <a name="considerations-for-the-sink"></a>Zagadnienia dotyczące ujścia
### <a name="general"></a>Ogólne
Pamiętaj, że źródłowy magazyn danych nie jest przeciążony innych obciążeń, które są uruchomione w lub przed nim.

Magazyny danych firmy Microsoft, można znaleźć [monitorowania i dostrajania tematy](#performance-reference) specyficznych dla magazynów danych. Te tematy mogą pomóc zrozumieć charakterystyki wydajności magazynu danych oraz jak zminimalizować czas reakcji i zmaksymalizowania wydajności.

Jeśli kopiujesz dane z **magazynu obiektów Blob** do **SQL Data Warehouse**, należy rozważyć użycie **PolyBase** do poprawienia wydajności. Zobacz [przy użyciu technologii PolyBase do ładowania danych do usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) Aby uzyskać szczegółowe informacje. Aby uzyskać wskazówki z przypadkami użycia, zobacz [ładowanie 1 TB w usłudze Azure SQL Data Warehouse z mniej niż 15 minut przy użyciu usługi Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Magazyny danych opartych na plikach
*(W tym magazyn obiektów Blob, Data Lake Store, Amazon S3, systemy plików lokalnych i lokalnym systemem plików HDFS)*

* **Skopiuj zachowanie**: Jeśli kopiujesz dane z magazynu danych oparte na plikach, działanie kopiowania ma trzy opcje za pośrednictwem **copyBehavior** właściwości. Ją zachowuje hierarchię, spłaszcza hierarchii lub scala plików. Zachowywanie albo spłaszczanie hierarchii ma niewielki lub zmniejszenie wydajności, ale scalanie plików powoduje zmniejszenie wydajności zwiększyć.
* **Plik formatowania i kompresji**: Zobacz [zagadnienia dotyczące serializacji i deserializacji](#considerations-for-serialization-and-deserialization) i [zagadnienia dotyczące kompresji](#considerations-for-compression) sekcje, aby uzyskać więcej sposobów na zwiększenie wydajności.
* **Magazynu obiektów blob**: Obecnie usługa magazynu obiektów Blob obsługuje tylko blokowe obiekty BLOB do transferowania danych zoptymalizowanego i przepływności.
* Dla **lokalnych systemów plików** scenariusze, które korzystają z **bramy zarządzania danymi**, zobacz [zagadnienia dotyczące bramy zarządzania danymi](#considerations-for-data-management-gateway) sekcji.

### <a name="relational-data-stores"></a>Magazyny danych relacyjnych
*(W tym bazy danych SQL Database, SQL Data Warehouse, baz danych programu SQL Server i baz danych Oracle)*

* **Skopiuj zachowanie**: W zależności od właściwości ustawiono dla **sqlSink**, działanie kopiowania zapisuje dane do docelowej bazy danych na różne sposoby.
  * Domyślnie używa usługi przenoszenia danych interfejsu API kopiowania zbiorczego wstawiania danych w Dołącz trybu, który zapewnia najlepszą wydajność.
  * Jeśli skonfigurujesz procedury składowanej w ujściu bazy danych stosuje się jeden wiersz danych w czasie zamiast jako ładowania zbiorczego. Wydajności znacznie spada. Jeśli zestaw danych jest duży, jeśli ma to zastosowanie, rozważ przejście na **sqlWriterCleanupScript** właściwości.
  * Jeśli skonfigurujesz **sqlWriterCleanupScript** uruchomić właściwości dla każdego działania kopiowania, usługa wyzwala skrypt i następnie wstawić dane za pomocą interfejsu API kopiowania zbiorczego. Na przykład aby zastąpić całą tabelę przy użyciu najnowszych danych, można określić skrypt, aby usunąć wszystkie rekordy przed ładowania zbiorczego nowe dane ze źródła.
* **Rozmiar danych wzorca i batch**:
  * Schemat tabeli ma wpływ na przepływność kopiowania. Aby skopiować podobną ilość danych, rozmiaru duży wiersz zapewnia lepszą wydajność niż rozmiar wiersza małych ponieważ bazy danych można efektywniej zatwierdzić mniejszej liczby partii danych.
  * Działanie kopiowania wstawia dane z serii partii. Możesz ustawić liczbę wierszy w zadaniu wsadowym, używając **writeBatchSize** właściwości. Jeśli dane zawierają małe wiersze, możesz ustawić **writeBatchSize** właściwość o wyższej wartości do korzystania z mniejszy narzut partii i wyższej przepływności. Jeśli rozmiar wiersza danych jest duży, należy zachować ostrożność, wraz ze zwiększeniem **writeBatchSize**. O wysokiej wartości może prowadzić do awarii kopiowania spowodowane przeciążeniem bazy danych.
* Dla **lokalnych relacyjnych baz danych** , takich jak SQL Server i Oracle, które korzystają z **bramy zarządzania danymi**, zobacz [zagadnienia dotyczące bramy zarządzania danymi](#considerations-for-data-management-gateway)sekcji.

### <a name="nosql-stores"></a>Magazynów NoSQL
*(W tym usługi Table storage i Azure Cosmos DB)*

* Aby uzyskać **Table storage**:
  * **Partycja**: Zapisywanie danych z partycjami przeplotem znacznie obniża wydajność. Sortowanie danych źródła według klucza partycji, dzięki czemu dane są wstawiane wydajnie w jednej partycji po drugim lub dostosować logiki można zapisać danych do jednej partycji.
* Aby uzyskać **usługi Azure Cosmos DB**:
  * **Wielkość partii**: **WriteBatchSize** właściwość ustawia liczbę równoległych żądań do usługi Azure Cosmos DB do tworzenia dokumentów. Można oczekiwać, że lepszą wydajność wraz ze zwiększeniem **writeBatchSize** ponieważ więcej żądań równoległych są wysyłane do usługi Azure Cosmos DB. Jednakże Obejrzyj ograniczania podczas zapisu do usługi Azure Cosmos DB (komunikat o błędzie jest "wskaźnik żądań jest duży"). Różne czynniki może spowodować ograniczenie rozmiaru dokumentu, w tym liczbę dokumentów i zasady indeksowania kolekcji docelowej. W celu uzyskania większej przepływności kopiowania, należy rozważyć użycie kolekcji lepsze, np. S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Zagadnienia dotyczące serializacji i deserializacji
Serializacja i deserializacja może wystąpić, gdy Twoje wejściowego zestawu danych lub zestawu danych wyjściowych jest plik. Zobacz [obsługiwane formaty plików i kompresji](data-factory-supported-file-and-compression-formats.md) ze szczegółami dotyczącymi obsługiwanych formatów plików przez działanie kopiowania.

**Skopiuj zachowanie**:

* Kopiowanie plików między magazynami danych opartych na plikach:
  * Gdy wejściowe i wyjściowe zestawy danych zarówno ma takie same lub nie ustawienia formatu pliku, usługi data movement service wykonuje kopia binarna bez serializacji lub deserializacji. Zostanie wyświetlony wyższej przepustowości w porównaniu do scenariusza, w której ustawienia formatu pliku źródła i ujścia różnią się od siebie nawzajem.
  * Gdy dane wejściowe i dane wyjściowe zestawy danych zarówno w formacie tekstowym, a tylko kodowanie typ jest inny, usługi data movement service jest wyłącznie Konwersja kodowania. Nie wszystkie serializacji i deserializacji, co powoduje, że niektóre wydajności, obciążenie w porównaniu do kopia binarna.
  * Jeśli wejściowe i wyjściowe zestawy danych zarówno mają różne formaty plików lub różne konfiguracje, takie jak ograniczników, usługi data movement service deserializuje dane źródłowe do strumienia, przekształcania i serializować go do formatu wyjściowego, wskazane przez Ciebie. Ta operacja powoduje znacznie bardziej znaczące wydajności, obciążenie w porównaniu do innych scenariuszy.
* Podczas kopiowania plików do/z magazynu danych, który nie jest oparte na plikach (na przykład z magazynu oparte na plikach do relacyjnego magazynu) krok serializacji lub deserializacji jest wymagany. Ten krok powoduje znaczne obciążenie.

**Format pliku**: Format pliku, który wybierzesz mogą mieć wpływ na wydajności kopiowania. Na przykład Avro jest kompaktowego formatu binarnego, który przechowuje metadane z danymi. Posiada obsługi szerokiej gamy w ekosystemie usługi Hadoop do przetwarzania i wykonywania zapytań. Jednak Avro jest droższe do serializacji i deserializacji, co skutkuje niższe przepływności kopiowania w porównaniu do formatu tekstowego. Wybierz ustawienia formatu pliku w całym przepływie przetwarzania całościowo. Rozpoczyna się od co tworzą dane są przechowywane w magazynach danych źródłowych lub ma zostać wyodrębniony z systemów zewnętrznych; najlepszy format dla magazynu, przetwarzanie analityczne i wykonywania zapytań; i w jakim formacie dane powinny być eksportowane do składnic danych programów dla narzędzia do raportowania i wizualizacji. Czasami formatu pliku nieoptymalne do odczytu i zapisu wydajność może być dobrym rozwiązaniem, gdy należy wziąć pod uwagę całkowity analitycznych procesu.

## <a name="considerations-for-compression"></a>Zagadnienia dotyczące kompresji
Gdy zestaw danych wejściowych lub wyjściowych jest plik, można ustawić działania kopiowania, które można wykonać kompresji lub dekompresji, ponieważ zapisuje dane do lokalizacji docelowej. Po wybraniu kompresji, upewnij się zależność między wejścia/wyjścia (We/Wy) i procesora CPU. Kompresowanie danych koszty dodatkowe zasoby obliczeniowe. Ale w zamian zmniejsza we/wy sieci i magazynu. W zależności od danych może zostać wyświetlony boost w ogólną przepływność kopiowania.

**Koder-dekoder**: Działanie kopiowania obsługuje typy kompresji Deflate, bzip2 i gzip. Usługa Azure HDInsight mogą używać wszystkich trzech typów w celu przetworzenia. Każdy kodera-dekodera kompresji zapewnia korzyści. Na przykład bzip2 ma najniższą przepływności kopiowania, ale uzyskać najlepszą wydajność zapytań Hive przy użyciu bzip2, ponieważ podzielić ją do przetworzenia. Gzip jest to opcja najbardziej o zrównoważonym obciążeniu, a jest najczęściej używany. Wybierz koder-dekoder, który najlepiej odpowiada Twojemu scenariuszowi end-to-end.

**Poziom**: Możesz korzystać z dwóch opcji dla każdego kodera-dekodera kompresji: najszybszych skompresowane i optymalnie skompresowany. Najszybciej skompresowany opcji kompresuje dane tak szybko, jak to możliwe, nawet wtedy, gdy wynikowy plik nie jest optymalnie skompresowany. Opcja optymalnie skompresowany zużywa więcej czasu na kompresji i daje minimalnej ilości danych. Możesz przetestować obie opcje, aby zobaczyć, który zapewnia lepszą wydajność ogólną w Twoim przypadku.

**Jest brany pod uwagę**: Aby skopiować dużej ilości danych między lokalnym magazynie i w chmurze, należy rozważyć użycie magazynu tymczasowego obiektu blob przy użyciu kompresji. Przy użyciu magazynu tymczasowego jest przydatne, gdy przepustowość sieci firmowej i usług platformy Azure jest czynnikiem ograniczającym i chcesz wejściowy zestaw danych i zestawie danych wyjściowych zarówno w postaci bez kompresji. Dokładniej mówiąc można podzielić działania pojedynczej kopii kopii dwóch działań. Pierwsze działanie kopiowania kopiuje ze źródła do przejściowym lub przejściowego obiektu blob w postaci skompresowany. Drugie działanie kopiowania kopiuje skompresowane dane z obszaru przemieszczania, a następnie dekompresuje podczas zapisuje obiekt sink.

## <a name="considerations-for-column-mapping"></a>Informacje dotyczące mapowania kolumn
Możesz ustawić **columnMappings** właściwość w działaniu kopiowania do mapy wszystkie lub podzbiór danych wejściowych kolumn na kolumny danych wyjściowych. Po usługi data movement service odczytuje dane ze źródła, trzeba wykonać mapowania kolumn na danych, zanim go zapisuje dane do ujścia. To dodatkowe przetwarzanie zmniejsza przepustowość kopiowania.

W przypadku odpytywalny magazynie danych źródła, na przykład, jeśli magazynu relacyjnego, takich jak bazy danych SQL Database lub SQL Server, czy jest magazynu NoSQL, takie jak usługi Table storage lub Azure Cosmos DB, należy wziąć pod uwagę wypychanie filtrowanie kolumn i zmianę kolejności logikę w celu **zapytania** właściwości zamiast mapowania kolumn. W ten sposób projekcji występuje, gdy usługi data movement service odczytuje dane z magazynu danych źródłowych, w którym jest znacznie bardziej efektywne.

## <a name="other-considerations"></a>Inne zagadnienia
Jeśli rozmiar danych, które mają zostać skopiowane jest duża, można dostosować logiki biznesowej na kolejne partycje dane w usłudze Data Factory przy użyciu mechanizmu skalowania. Następnie należy zaplanować działanie kopiowania w celu uruchamiania częściej, aby zmniejszyć rozmiar danych dla każdego uruchomienia działania kopiowania.

Należy zachować ostrożność liczby zestawów danych i działania kopiowania usługi Data Factory konieczności łącznika do tego samego magazynu danych w tym samym czasie. Wiele zadań jednoczesnych kopii może ograniczać do przechowywania danych i spowodować pogorszenie wydajności, kopii zadania wewnętrzne ponownych prób, a w niektórych przypadkach niepowodzenia wykonywania.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Przykładowy scenariusz: Kopiowanie z lokalnego programu SQL Server do usługi Blob storage
**Scenariusz**: Potok został opracowany pod kątem kopiowanie danych z lokalnego programu SQL Server do usługi Blob storage w formacie CSV. Aby przyspieszyć zadanie kopiowania, pliki CSV należy skompresowane do formatu bzip2.

**Test i analiza**: Przepływność działania kopiowania jest mniejsza niż 2 MB/s, który jest znacznie wolniejsze niż testów porównawczych wydajności.

**Analiza wydajności i dostosowywania**: Aby rozwiązać problem z wydajnością, Przyjrzyjmy się jak dane są przetwarzane i przenoszone.

1. **Odczytywanie danych**: Brama otwiera połączenie z SQL Server i wysyła to zapytanie. Program SQL Server reaguje wysyłania strumienia danych do bramy za pośrednictwem sieci intranet.
2. **Serializowanie i kompresować dane**: Brama serializuje strumień danych do formatu CSV i kompresuje dane do usługi bzip2 stream.
3. **Zapisywanie danych**: Brama przekazuje strumienia bzip2 do magazynu obiektów Blob za pośrednictwem Internetu.

Jak widać, dane są przetwarzane i przenoszone w sposób sekwencyjny przesyłania strumieniowego: Program SQL Server > LAN > bramy > sieć WAN > Blob storage. **Ogólna wydajność jest kontrolowany za przepustowość minimalna w potoku**.

![Przepływ danych](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Co najmniej jeden z następujących czynników może spowodować wąskie gardło:

* **Źródło**: Sam program SQL Server ma niskiej przepustowości, ze względu na duże obciążenia.
* **Brama zarządzania danymi**:
  * **LAN**: Brama znajduje się oni daleko od komputera serwera SQL i ma połączenie o niskiej przepustowości.
  * **Gateway**: Bramy osiągnęła jej ograniczenia obciążenia, aby wykonywać następujące operacje:
    * **Serializacja**: Serializacja strumień danych do formatu CSV ma powolne przepływności.
    * **Kompresja**: Wybrano powolne kodera-dekodera kompresji (na przykład, bzip2, czyli 2,8 MB/s z Core i7).
  * **SIECI WAN**: Brakuje przepustowości między siecią firmową i usług platformy Azure (na przykład T1 = 1,544 KB/s; T2 = 6,312 KB/s).
* **Obiekt sink**: Magazyn obiektów blob ma niskiej przepustowości. (W tym scenariuszu jest mało prawdopodobne, ponieważ jej umowa SLA gwarantuje co najmniej 60 MB/s).

W tym przypadku bzip2 kompresji danych może być spowalniania cały potok. Przełączanie do kodera-dekodera kompresji gzip może jej obsługi ułatwiają realizację tego wąskiego gardła.

## <a name="sample-scenarios-use-parallel-copy"></a>Przykładowe scenariusze: Użyj równoległych kopii
**Scenariusz I:** Skopiuj 1000 plików 1 MB z lokalnym systemem plików do magazynu obiektów Blob.

**Analiza i dostrajanie wydajności**: Na przykład po zainstalowaniu bramy na komputerze czterordzeniowe Data Factory przy użyciu 16 kopie równoległe przenoszenie plików z systemu plików do magazynu obiektów Blob równocześnie. To wykonywanie równoległe powinno spowodować wysokiej przepływności. Można również jawnie określić liczbę równoległych kopii. Podczas kopiowania wiele małych plików równoległe kopie znacznie ułatwić przepływności bardziej efektywnie korzystając z zasobów.

![Scenariusz 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenariusz II**: Skopiuj 20 obiektów blob 500 MB z magazynu obiektów Blob do usługi Data Lake Store Analytics, a następnie Dostosuj wydajność do wymagań.

**Analiza i dostrajanie wydajności**: W tym scenariuszu usługi Data Factory kopiuje dane z magazynu obiektów Blob do Data Lake Store przy użyciu pojedynczej kopii (**parallelCopies** ustawiona na 1) i jednostek przenoszenia danych w chmurze pojedynczej. Przepływności, można zaobserwować będzie bliski, opisana [wydajności sekcję](#performance-reference).

![Scenariusz 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scenariusz III**: Rozmiar pojedynczego pliku jest większy niż dziesiątki MB i łączny wolumin jest duży.

**Analiza i włączanie wydajności**: Zwiększenie **parallelCopies** nie powoduje podniesienia wydajności kopiowania ze względu na ograniczenia zasobów DMU jedną chmurą. Zamiast tego należy określić więcej chmury DMUs, aby uzyskać więcej zasobów do wykonania przenoszenia danych. Nie określaj wartości **parallelCopies** właściwości. Usługa Data Factory obsługuje równoległości dla Ciebie. W tym przypadku jeśli ustawisz **cloudDataMovementUnits** na 4, przepływności o cztery razy występuje.

![Scenariusz 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Informacje ogólne
Poniżej przedstawiono monitorowanie wydajności i dostosowywania odwołania dla niektórych obsługiwanych magazynów danych:

* Usługa Azure Storage (w tym magazyn obiektów Blob i Table storage): [Cele skalowalności usługi Azure Storage](../../storage/common/storage-scalability-targets.md) i [Lista kontrolna wydajności i skalowalności usługi Azure Storage](../../storage/common/storage-performance-checklist.md)
* Azure SQL Database: Możesz [monitorować wydajność](../../sql-database/sql-database-single-database-monitor.md) i sprawdź wartość procentowa jednostki (DTU) dla transakcji bazy danych
* Azure SQL Data Warehouse: Jej możliwości jest mierzony w liczbę jednostek magazynu danych (dwu); zobacz [Zarządzaj obliczeniowa w usłudze Azure SQL Data Warehouse (omówienie)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Usługa Azure Cosmos DB [Poziomy wydajności w usłudze Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* Na lokalnym serwerze SQL Server: [Monitorowanie i dostrajanie wydajności](https://msdn.microsoft.com/library/ms189081.aspx)
* Lokalny serwer plików: [Dostosowywanie na serwerach plików wydajności](https://msdn.microsoft.com/library/dn567661.aspx)
