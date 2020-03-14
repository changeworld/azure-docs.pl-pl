---
title: Skopiuj wydajności i działania przewodnika dostrajania
description: Więcej informacji na temat kluczowych czynników wpływających na wydajność przenoszenia danych w usłudze Azure Data Factory, korzystając z działania kopiowania.
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
ms.openlocfilehash: 9ca44b1917cfaed5d01c31f8f06d98e5e4b611a8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281940"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Skopiuj wydajności i działania przewodnika dostrajania

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-copy-activity-performance.md)
> * [Wersja 2 (bieżąca wersja)](../copy-activity-performance.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Przewodnik dotyczący wydajności i dostrajania działania kopiowania dla Data Factory](../copy-activity-performance.md).

Działanie kopiowania w usłudze Azure fabryki danych zapewnia najwyższej jakości danych bezpieczny, niezawodny i wydajny ładowania rozwiązania. Go umożliwia dziesiątki kopię terabajtów danych każdego dnia w wielu różnych chmury i rozwiązań lokalnych magazynów danych. Błyskawiczne danych wydajność ładowania ma kluczowe znaczenie dla zapewnienia możesz skupić się na główny problem "dane big data": tworzenie zaawansowanych rozwiązań analizy i uzyskiwanie szczegółowych informacji z wszystkie te dane.

Platforma Azure udostępnia zestaw przeznaczonych dla przedsiębiorstw rozwiązaniach magazynu danych magazynu i danych, a działanie kopiowania oferuje wysoce zoptymalizowane środowiska, który jest łatwy do skonfigurowania i ładowaniem. Przy użyciu tylko jednej kopii działania programu można osiągnąć:

* Ładowanie danych do **Azure SQL Data Warehouse** o **1,2 GB/s**. Aby zapoznać się z przewodnikiem dotyczącym przypadku użycia, zobacz [ładowanie 1 TB do Azure SQL Data Warehouse na 15 minut z Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Ładowanie danych do **usługi Azure Blob Storage** o **1,0 GB/s**
* Ładowanie danych do **Azure Data Lake Store** o **1,0 GB/s**

W tym artykule opisano:

* [Numery odwołań wydajności](#performance-reference) dla obsługiwanych magazynów danych źródłowych i ujścia, które ułatwiają planowanie projektu;
* Funkcje, które mogą zwiększyć przepływność kopiowania w różnych scenariuszach, w tym [jednostki przenoszenia danych w chmurze](#cloud-data-movement-units), [Kopiowanie równoległe](#parallel-copy)i [Kopiowanie etapowe](#staged-copy);
* [Wskazówki dotyczące dostrajania](#performance-tuning-steps) wydajności dotyczące dostrajania wydajności i kluczowych czynników, które mogą mieć wpływ na wydajność kopiowania.

> [!NOTE]
> Jeśli nie znasz ogólnie działania kopiowania, zobacz [przenoszenie danych za pomocą działania kopiowania](data-factory-data-movement-activities.md) przed przeczytaniem tego artykułu.
>

## <a name="performance-reference"></a>Informacje dotyczące wydajności

W poniższej tabeli przedstawiono informacje o przepływności kopiowania w MB/s dla danej pary źródłowej i ujścia na podstawie testów wewnętrznych. W przypadku porównania pokazano również, w jaki sposób różne ustawienia [jednostek przenoszenia danych w chmurze](#cloud-data-movement-units) lub [skalowalności bramy zarządzanie danymi](data-factory-data-management-gateway-high-availability-scalability.md) (wiele węzłów bramy) mogą pomóc w wydajności kopiowania.

![Macierz wydajności](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>W Azure Data Factory wersja 1 minimalna jednostka przenoszenia danych w chmurze dla kopii z chmury do chmury jest równa 2. Jeśli nie zostanie określony, zobacz domyślne jednostki przenoszenia danych używane w [jednostkach przenoszenia danych w chmurze](#cloud-data-movement-units).

**Punkty do uwagi:**
* Obliczana jest przepływność przy użyciu następującej formuły: [rozmiar danych do odczytu ze źródła] / [czas uruchomienia działania kopiowania].
* Numery referencyjne wydajności w tabeli były mierzone przy użyciu zestawu danych [TPC-H](http://www.tpc.org/tpch/) w jednym przebiegu działania kopiowania.
* W magazynach danych platformy Azure źródła i ujścia znajdują się w tym samym regionie platformy Azure.
* W przypadku kopiowania hybrydowego między magazynami lokalnymi i w chmurze każdy węzeł bramy był uruchomiony na komputerze, który był oddzielony od lokalnego magazynu danych, zgodnie z poniższą specyfikacją. Gdy jedno działanie zostało uruchomione na bramie, operacja kopiowania zużywał tylko niewielką część procesora CPU, pamięci lub przepustowości sieci. Dowiedz się więcej na temat [Zarządzanie danymi Gateway](#considerations-for-data-management-gateway).
    <table>
    <tr>
        <td>Procesor CPU</td>
        <td>32 rdzenie 2,20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Pamięć</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Network</td>
        <td>Interfejs internetowy: 10 GB/s; Interfejs sieci intranet: 40 GB/s</td>
    </tr>
    </table>


> [!TIP]
> Większą przepływność można osiągnąć, wykorzystując więcej jednostek przenoszenia danych (DMUs) niż domyślna maksymalna wartość DMUs, która jest 32 do uruchomienia działania kopiowania z chmury do chmury. Na przykład w przypadku 100 DMUs można osiągnąć kopiowanie danych z usługi Azure BLOB do Azure Data Lake Store przy użyciu **1.0 GB/s**. Aby uzyskać szczegółowe informacje na temat tej funkcji i obsługiwanego scenariusza, zobacz sekcję dotyczącą [jednostek przenoszenia danych w chmurze](#cloud-data-movement-units) . Skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/) , aby zażądać większej DMUs.

## <a name="parallel-copy"></a>Kopiowanie równoległe
Można odczytywać dane ze źródła lub zapisywać dane w miejscu docelowym **równolegle w ramach uruchomienia działania kopiowania**. Ta funkcja zwiększa przepływność operacji kopiowania i skraca czas przenoszenia danych.

To ustawienie różni się od właściwości **współbieżności** w definicji działania. Właściwość **współbieżność** określa liczbę **współbieżnych uruchomień działania kopiowania** do przetwarzania danych z różnych okien działania (1 am do 2 am, 2 am do 3 am, 3 am i tak dalej). Ta funkcja jest przydatna, gdy wykonujesz obciążenie historyczne. Możliwość kopiowania równoległego ma zastosowanie do **pojedynczego uruchomienia działania**.

Przyjrzyjmy się przykładowym scenariuszom. W poniższym przykładzie należy przetworzyć wiele wycinków z przeszłości. Data Factory uruchamia wystąpienie działania kopiowania (uruchomienie działania) dla każdego wycinka:

* Wycinek danych z pierwszego okna działania (od 1 do 2 AM) = = > działania uruchomienia 1
* Wycinek danych z drugiego okna działania (od 2 do 3 AM) = = > działania uruchomienia 2
* Wycinek danych z drugiego okna działania (3 AM do 4 AM) = = > działania uruchomienia 3

I tak dalej.

W tym przykładzie, gdy wartość **współbieżności** jest ustawiona na 2, **uruchomienie działania 1** i **uruchomienie działania 2** kopiuje dane z dwóch okien działania **jednocześnie** w celu zwiększenia wydajności przenoszenia danych. Jeśli jednak wiele plików jest skojarzonych z uruchomieniem działania 1, Usługa przenoszenia danych kopiuje pliki z lokalizacji źródłowej do jednego pliku docelowego w danym momencie.

### <a name="cloud-data-movement-units"></a>Jednostki przenoszenia danych w chmurze
**Jednostka przenoszenia danych w chmurze (DMU)** to miara, która reprezentuje moc (KOMBINACJĘ procesora CPU, pamięci i alokacji zasobów sieciowych) pojedynczej jednostki w Data Factory. DMU ma zastosowanie do operacji kopiowania z chmury do chmury, ale nie w kopii hybrydowej.

**Minimalną liczbę jednostek przenoszenia danych w chmurze na potrzeby uruchomienia działania kopiowania są dwa.** Jeśli nie zostanie określona, Poniższa tabela zawiera listę domyślnych DMUs używanych w różnych scenariuszach kopiowania:

| Skopiuj scenariusza | Domyślna DMUs określona przez usługę |
|:--- |:--- |
| Kopiowanie danych między magazynami oparte na plikach | Od 4 do 16 w zależności od liczby i rozmiaru plików. |
| Innych scenariuszach kopiowania | 4 |

Aby zastąpić to ustawienie domyślne, określ wartość właściwości **cloudDataMovementUnits** w następujący sposób. **Dozwolone wartości** właściwości **cloudDataMovementUnits** to 2, 4, 8, 16, 32. **Rzeczywista liczba DMUs w chmurze** , której operacja kopiowania używa w czasie wykonywania, jest równa lub mniejsza niż skonfigurowana wartość, w zależności od wzorca danych. Aby uzyskać informacje o poziomie wzmocnienia wydajności, które można uzyskać podczas konfigurowania większej liczby jednostek dla określonego źródła i ujścia kopii, zobacz [informacje dotyczące wydajności](#performance-reference).

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
> Jeśli potrzebujesz więcej DMUs w chmurze o wyższej przepływności, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/). Ustawienie 8 i nowsze działa obecnie tylko **w przypadku kopiowania wielu plików z usługi BLOB Storage/Data Lake Store/Amazon S3/Cloud FTP/Cloud SFTP do usługi BLOB Storage/Data Lake Store/Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies
Możesz użyć właściwości **parallelCopies** , aby wskazać równoległość, która ma być używana przez działanie kopiowania. Tę właściwość można traktować jako maksymalną liczbę wątków w ramach działania kopiowania, które można odczytać ze źródła lub zapisywać swoich magazynów danych ujścia równolegle.

Dla każdego uruchomienia działania kopiowania usługi fabryka danych określa liczbę równoległych kopii na potrzeby kopiowania danych ze źródła danych przechowywane i danych docelowego. Domyślna liczba kopii równoległych, których używa, zależy od typu używanego źródła i ujścia.

| Źródło i ujścia | Domyślna liczba równoległych kopii określany przez usługę |
| --- | --- |
| Kopiowanie danych między magazynami opartymi na plikach (BLOB Storage; Data Lake Store; Amazon S3; lokalny system plików; lokalny system plików HDFS) |Od 1 do 32. Zależy od rozmiaru plików i liczby jednostek przenoszenia danych w chmurze (DMUs) służących do kopiowania danych między dwoma magazynami danych w chmurze lub fizycznej konfiguracji maszyny bramy używanej do kopiowania hybrydowego (do kopiowania danych do lub z lokalnego magazynu danych). |
| Kopiowanie danych z **dowolnego źródłowego magazynu danych do usługi Azure Table Storage** |4 |
| Wszystkie inne pary źródłowe i ujścia |1 |

Zwykle zachowanie domyślne powinno zapewnić najlepszą przepływność. Aby jednak kontrolować obciążenie maszyn, które obsługują magazyny danych, lub dostosować wydajność kopiowania, można zastąpić wartość domyślną i określić wartość właściwości **parallelCopies** . Wartość musi zawierać się w przedziale od 1 do 32 (włącznie). W czasie wykonywania Aby uzyskać najlepszą wydajność, działanie kopiowania używa wartość, która jest mniejsza niż lub równa wartości, który został ustawiony.

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

* Podczas kopiowania danych między magazynami opartymi na plikach **parallelCopies** określa równoległość na poziomie pliku. Segmentu w pojedynczym pliku sytuacja może mieć miejsce poniżej automatycznie i w sposób niewidoczny dla użytkownika, a zostało zaprojektowane na potrzeby ładowania danych w równoległych i prostopadły do parallelCopies najlepsze rozmiar fragmentu odpowiedniego dla typu magazynu danego źródła danych. Rzeczywista liczba równoległych kopii usługi data movement service używa dla operacji kopiowania w czasie wykonywania jest nie więcej niż liczba plików, których masz. Jeśli zachowanie kopiowania ma wartość **mergeFile**, działanie Copy nie może korzystać z równoległości na poziomie plików.
* Po określeniu wartości właściwości **parallelCopies** należy wziąć pod uwagę wzrost obciążenia dla magazynów danych źródłowych i ujścia oraz bramę, jeśli jest to kopia hybrydowa. Dzieje się tak szczególnie, gdy masz wiele działań lub równoczesnych uruchomień działań uruchamianych w odniesieniu do tego samego magazynu danych. Jeśli zauważysz, że magazyn danych lub Brama są przeciążone, zmniejsz wartość **parallelCopies** , aby zwolnić obciążenie.
* Podczas kopiowania danych z magazynów, które nie są oparte na plikach, do magazynów, które są oparte na plikach, Usługa przenoszenia danych ignoruje Właściwość **parallelCopies** . Nawet jeśli równoległości jest określona, nie zostanie zastosowane w tym przypadku.

> [!NOTE]
> Aby użyć funkcji **parallelCopies** w przypadku wykonywania kopii hybrydowej, należy użyć bramy zarządzanie danymi w wersji 1,11 lub nowszej.
>
>

Aby lepiej wykorzystać te dwie właściwości i zwiększyć przepływność przenoszenia danych, zobacz przykładowe przypadki użycia. Nie musisz konfigurować **parallelCopies** , aby korzystać z zachowania domyślnego. Jeśli skonfigurujesz i **parallelCopies** jest za mały, wiele DMUs w chmurze może nie być w pełni wykorzystane.

### <a name="billing-impact"></a>Wpływ rozliczeń
Należy pamiętać, że **opłata jest** naliczana na podstawie łącznego czasu operacji kopiowania. Jeśli zadanie kopiowania używane do podzielenia jednej godziny z jedną jednostką w chmurze, a teraz trwa 15 minut z czterema jednostkami w chmurze, cały rachunek pozostaje niemal taki sam. Na przykład należy użyć czterech jednostek chmur. Pierwsza jednostka chmury poświęca 10 minut, drugi, 10 minut, trzeci, 5 minut i czwarty, 5 minut, wszystkie w jednym uruchomieniu działania kopiowania. Opłata jest naliczana za całkowity czas kopiowania (przenoszenia danych), czyli 10 + 10 + 5 + 5 = 30 minut. Korzystanie z **parallelCopies** nie ma wpływu na rozliczenia.

## <a name="staged-copy"></a>Kopiowania przejściowego
Podczas kopiowania danych z magazynu danych źródłowych do magazynu danych ujścia, można użyć magazynu obiektów Blob jako tymczasowego magazynu przejściowego. Przemieszczania jest szczególnie użyteczna w następujących przypadkach:

1. Chcesz pozyskać **dane z różnych magazynów danych do SQL Data Warehouse za pośrednictwem bazy**. Usługa SQL Data Warehouse używa programu PolyBase jako mechanizm o wysokiej przepływności ładowanie dużej ilości danych do usługi SQL Data Warehouse. Dane źródłowe muszą jednak znajdować się w usłudze BLOB Storage i muszą spełniać dodatkowe kryteria. Podczas ładowania danych z magazynu danych innego niż magazyn obiektów blob, można aktywować kopiowanie danych za pośrednictwem tymczasowego tymczasowego magazynu obiektów BLOB. W takiej sytuacji usługi fabryka danych wykonuje przekształcenia danych wymagane, aby upewnić się, że spełnia wymagania programu PolyBase. Następnie używa programu PolyBase do ładowania danych do usługi SQL Data Warehouse. Aby uzyskać więcej informacji, zobacz [Korzystanie z bazy danych w celu ładowania do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). Aby zapoznać się z przewodnikiem dotyczącym przypadku użycia, zobacz [ładowanie 1 TB do Azure SQL Data Warehouse na 15 minut z Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Czasami trwa przeprowadzenie hybrydowego przenoszenia danych (czyli kopiowania między lokalnym magazynem danych i magazynem danych w chmurze) przez wolne połączenie sieciowe**. Aby zwiększyć wydajność, można skompresować dane lokalnie, aby przełączać dane do tymczasowego magazynu danych w chmurze. Następnie można zdekompresować dane w magazynie przemieszczania przed załadowaniem ich do docelowego magazynu danych.
3. **Nie chcesz otwierać portów innych niż port 80 i port 443 w zaporze ze względu na firmowe zasady IT**. Na przykład podczas kopiowania danych z lokalnego magazynu danych ujścia Azure SQL Database lub ujścia Azure SQL Data Warehouse, musisz aktywować komunikacja wychodząca TCP na porcie 1433 dla zapory Windows i zaporą firmową. W tym scenariuszu należy skorzystać z bramy, aby najpierw skopiować dane do wystąpienia tymczasowego magazynu obiektów BLOB za pośrednictwem protokołu HTTP lub HTTPS na porcie 443. Następnie Załaduj dane do SQL Database lub SQL Data Warehouse z przemieszczania magazynu obiektów BLOB. W tym przepływie nie trzeba włączyć port 1433.

### <a name="how-staged-copy-works"></a>Jak przygotowanych działania kopiowania
Gdy uaktywniasz funkcję przemieszczania, najpierw dane są kopiowane z magazynu danych źródłowych do przemieszczania magazynu danych (własne). Następnie dane są kopiowane z przejściowy magazyn danych do magazynu danych ujścia. Data Factory automatycznie zarządza przepływem dwuetapowego dla Ciebie. Usługi Data Factory czyści dane tymczasowe z magazynu przejściowego także po ukończeniu przenoszenia danych.

W scenariuszu kopiowania w chmurze (magazyny danych źródłowych i ujścia znajdują się w chmurze) Brama nie jest używana. Usługa Data Factory wykonuje operacje kopiowania.

![Przygotowana kopia: scenariusz chmury](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

W scenariuszu kopiowania hybrydowego (Źródło jest lokalne i ujścia jest w chmurze), Brama przenosi dane z magazynu danych źródłowych do magazynu danych przemieszczania. Usługa Data Factory przenosi dane z tymczasowego magazynu danych do magazynu danych ujścia. Kopiowanie danych z magazynu danych w chmurze do lokalnego magazynu danych za pośrednictwem przemieszczania jest również obsługiwane przez odwrócony przepływ.

![Przygotowana kopia: scenariusz hybrydowy](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Gdy aktywujesz przenoszenie danych za pomocą magazynu przejściowego można określić, czy chcesz, aby dane skompresowane przed przenosi dane z magazynu danych źródłowych do magazynu danych tymczasowych lub tymczasowej, a następnie dekompresowane przed przystąpieniem do przenoszenia danych z przejściowej lub przemieszczania danych magazynu do magazynu danych ujścia.

Obecnie nie można skopiować danych między dwoma magazynami danych w środowisku lokalnym za pomocą magazynu przejściowego. Oczekujemy, że ta opcja będzie dostępna wkrótce.

### <a name="configuration"></a>Konfiguracja
Skonfiguruj ustawienie **enableStaging** w działaniu Copy, aby określić, czy dane mają zostać przygotowane w magazynie obiektów BLOB przed załadowaniem ich do docelowego magazynu danych. Po ustawieniu **enableStaging** na true, określ dodatkowe właściwości wymienione w następnej tabeli. Jeśli nie masz, musisz również utworzyć usługi Azure Storage lub magazynu udostępnionego usługi połączonej podpis dostępu dla przemieszczania.

| Właściwość | Opis | Wartość domyślna | Wymagane |
| --- | --- | --- | --- |
| **enableStaging** |Określ, czy chcesz skopiować dane za pośrednictwem tymczasowego magazynu przejściowego. |Fałsz |Nie |
| **linkedServiceName** |Określ nazwę połączonej usługi [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) lub [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) , która odwołuje się do wystąpienia magazynu, którego używasz jako tymczasowego magazynu przemieszczania. <br/><br/> Nie można używać magazynu przy użyciu sygnatury dostępu współdzielonego, aby załadować dane do usługi SQL Data Warehouse za pomocą programu PolyBase. Można go użyć w innych scenariuszach. |N/D |Tak, gdy **enableStaging** jest ustawiona na wartość true |
| **path** |Określ ścieżkę magazynu obiektów Blob, która ma zawierać użycia przemieszczonych danych. Jeśli ścieżka nie zostanie określona, usługa tworzy kontener do przechowywania danych tymczasowych. <br/><br/> Określ ścieżkę, tylko wtedy, gdy używasz magazynu przy użyciu sygnatury dostępu współdzielonego lub wymagają danych tymczasowych w określonej lokalizacji. |N/D |Nie |
| **Ustawieniem EnableCompression** |Określa, czy dane powinny zostać skompresowane przed skopiowaniem ich do miejsca docelowego. To ustawienie powoduje zmniejszenie ilości przesyłanych danych. |Fałsz |Nie |

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

### <a name="billing-impact"></a>Wpływ rozliczeń
Opłaty są naliczane na podstawie dwóch kroków: czas trwania kopiowania i skopiuj typu.

* W przypadku korzystania z przemieszczania w trakcie kopiowania w chmurze (kopiowanie danych z magazynu danych w chmurze do innego magazynu danych w chmurze) naliczane są opłaty [łączny czas trwania kopiowania dla krok 1 i krok 2] x [cena jednostkowa kopiowania w chmurze].
* W przypadku korzystania z przemieszczania w trakcie kopiowania hybrydowego (kopiowanie danych z lokalnego magazynu danych do magazynu danych w chmurze) opłata jest naliczana za [czas trwania kopiowania hybrydowego] x [cena jednostkowa kopiowania hybrydowego] + [czas trwania kopiowania w chmurze] x [cena jednostkowa kopiowania w chmurze].

## <a name="performance-tuning-steps"></a>Kroki dostosowywania wydajności
Sugerujemy, wykonaj następujące kroki, aby dostosować wydajność usługi Data Factory za pomocą działania kopiowania:

1. **Ustanów linię bazową**. Podczas fazy opracowywania testowanie potoku za pomocą działania kopiowania względem przykładowych danych. Możesz użyć [modelu](data-factory-scheduling-and-execution.md) rozcinania Data Factory, aby ograniczyć ilość danych, z którymi pracujesz.

   Zbierz charakterystyki czasu wykonywania i wydajności za pomocą **aplikacji do monitorowania i zarządzania**. Wybierz pozycję **monitoruj & Zarządzaj** na stronie głównej Data Factory. W widoku drzewa wybierz **wyjściowy zestaw danych**. Na liście **okna działania** wybierz przebieg działania kopiowania. **Okna działania** wyświetlają czas trwania działania kopiowania i rozmiar kopiowanych danych. Przepływność jest wymieniona w **Eksploratorze okien działania**. Aby dowiedzieć się więcej na temat aplikacji, zobacz [monitorowanie potoków Azure Data Factory i zarządzanie nimi za pomocą aplikacji do monitorowania i zarządzania](data-factory-monitor-manage-app.md).

   ![Szczegóły uruchamiania działania](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   W dalszej części artykułu można porównać wydajność i konfigurację Twojego scenariusza, aby skopiować [Informacje o wydajności](#performance-reference) działania z naszych testów.
2. **Diagnozuj i Optymalizuj wydajność**. Jeśli wydajność, której możesz obserwować nie spełniają Twoich oczekiwań, musisz określić wąskie gardła wydajności. Następnie zoptymalizować wydajność, aby usunąć lub zmniejszają efekt wąskich gardeł. Pełny opis Diagnostyka wydajności wykracza poza zakres tego artykułu, ale poniżej przedstawiono niektóre typowe kwestie dotyczące:

   * Funkcje wydajności:
     * [Kopiowanie równoległe](#parallel-copy)
     * [Jednostki przenoszenia danych w chmurze](#cloud-data-movement-units)
     * [Kopia przygotowana](#staged-copy)
     * [Skalowalność bramy Zarządzanie danymi](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Brama zarządzania danymi](#considerations-for-data-management-gateway)
   * [Element źródłowy](#considerations-for-the-source)
   * [Fotografii](#considerations-for-the-sink)
   * [Serializacja i deserializacja](#considerations-for-serialization-and-deserialization)
   * [Skompresowane](#considerations-for-compression)
   * [Mapowanie kolumn](#considerations-for-column-mapping)
   * [Inne zagadnienia](#other-considerations)
3. **Rozwiń konfigurację do całego zestawu danych**. Gdy wyniki wykonywania i wydajność są zadowalające, można rozwinąć definicję i aktywny okres potoku w celu pokrycia całego zestawu danych.

## <a name="considerations-for-data-management-gateway"></a>Zagadnienia dotyczące bramy Zarządzanie danymi
**Konfiguracja bramy**: zalecamy używanie dedykowanego komputera do hostowania bramy zarządzanie danymi. Zapoznaj się [z zagadnieniami dotyczącymi używania bramy zarządzanie danymi](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Monitorowanie i skalowanie bramy**: pojedyncza brama logiczna z co najmniej jednym węzłem bramy może jednocześnie obsłużyć wiele przebiegów kopiowania jednocześnie. Można wyświetlić migawkę wykorzystania zasobów (procesor CPU, pamięć, Sieć (WE/wychodzącą) itp.) na maszynie bramy oraz liczbę współbieżnych zadań uruchomionych w ramach Azure Portal, zobacz [monitorowanie bramy w portalu](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Jeśli istnieje duże zapotrzebowanie na hybrydowe przenoszenie danych z dużą liczbą współbieżnych przebiegów kopiowania lub z dużą ilością danych do skopiowania, należy rozważyć [skalowanie bramy w górę lub w poziomie](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) w celu lepszego wykorzystania zasobów lub zapewnienia większej ilości zasobów w celu uzyskania uprawnień do kopiowania.

## <a name="considerations-for-the-source"></a>Zagadnienia dotyczące źródła
### <a name="general"></a>Ogólne
Pamiętaj, że źródłowy magazyn danych nie jest przeciążony innych obciążeń, które są uruchomione w lub przed nim.

W przypadku magazynów danych firmy Microsoft zapoznaj się z tematami dotyczącymi [monitorowania i dostrajania](#performance-reference) , które są specyficzne dla magazynów danych, i poznanie charakterystyki wydajności magazynu danych, minimalizacji czasów odpowiedzi i maksymalnej przepływności.

Jeśli skopiujesz dane z magazynu obiektów BLOB do SQL Data Warehouse, rozważ użycie **bazy** danych w celu zwiększenia wydajności. Aby uzyskać szczegółowe informacje [, zobacz Korzystanie z bazy Azure SQL Data Warehouse danych](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) . Aby zapoznać się z przewodnikiem dotyczącym przypadku użycia, zobacz [ładowanie 1 TB do Azure SQL Data Warehouse na 15 minut z Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Magazyny danych opartych na plikach
*(W tym magazyn obiektów blob, Data Lake Store, Amazon S3, lokalne systemy plików i lokalne HDFS)*

* **Średni rozmiar pliku i liczba plików**: działanie Copy transferuje dane w jednym pliku naraz. Przy użyciu tej samej ilości danych do przeniesienia ogólną przepustowość jest mniejszy, jeśli dane składa się z wielu małych plików, a nie kilka dużych plików z powodu fazie uruchamiania dla każdego pliku. W związku z tym jeśli to możliwe, łączenia małych plików do większych plikach w celu uzyskania większej przepływności.
* **Format pliku i kompresja**: Aby uzyskać więcej informacji na temat poprawy wydajności, zobacz [zagadnienia dotyczące serializacji i deserializacji](#considerations-for-serialization-and-deserialization) oraz [zagadnienia](#considerations-for-compression) dotyczące sekcji kompresji.
* W przypadku scenariusza **lokalnego systemu plików** , w którym **brama zarządzanie danymi** jest wymagana, zapoznaj się z sekcją [uwagi dotyczące zarządzanie danymi Gateway](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Magazyny danych relacyjnych
*(Zawiera SQL Database; SQL Data Warehouse; Amazon RedShift; Bazy danych SQL Server; i bazy danych Oracle, MySQL, DB2, Teradata, Sybase i PostgreSQL itp.)*

* **Wzorzec danych**: schemat tabeli wpływa na przepływność kopiowania. Rozmiar wiersza dużych zapewnia lepszą wydajność niż rozmiar wiersza mały, aby skopiować podobną ilość danych. Przyczyną jest to, że baza danych wydajniej można pobrać mniejszej liczby partii danych, które zawierają mniej wierszy.
* **Zapytanie lub procedura składowana**: Optymalizacja logiki zapytania lub procedury składowanej określonej w źródle działania kopiowania w celu bardziej wydajnego pobierania danych.
* **W przypadku lokalnych relacyjnych baz danych**, takich jak SQL Server i Oracle, które wymagają korzystania z **bramy zarządzanie danymi**, zobacz sekcję Uwagi dotyczące bramy zarządzanie danymi.

## <a name="considerations-for-the-sink"></a>Zagadnienia dotyczące ujścia
### <a name="general"></a>Ogólne
Pamiętaj, że źródłowy magazyn danych nie jest przeciążony innych obciążeń, które są uruchomione w lub przed nim.

W przypadku magazynów danych firmy Microsoft należy zapoznać się z [tematami dotyczącymi monitorowania i dostrajania](#performance-reference) , które są specyficzne dla magazynów danych. Te tematy mogą pomóc zrozumieć charakterystyki wydajności magazynu danych oraz jak zminimalizować czas reakcji i zmaksymalizowania wydajności.

Jeśli kopiujesz dane z **magazynu obiektów BLOB** do **SQL Data Warehouse**, rozważ użycie **bazy** danych w celu zwiększenia wydajności. Aby uzyskać szczegółowe informacje [, zobacz Korzystanie z bazy Azure SQL Data Warehouse danych](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) . Aby zapoznać się z przewodnikiem dotyczącym przypadku użycia, zobacz [ładowanie 1 TB do Azure SQL Data Warehouse na 15 minut z Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Magazyny danych opartych na plikach
*(W tym magazyn obiektów blob, Data Lake Store, Amazon S3, lokalne systemy plików i lokalne HDFS)*

* **Zachowanie kopiowania**: w przypadku kopiowania danych z innego magazynu danych opartego na plikach, działanie kopiowania ma trzy opcje za pośrednictwem właściwości **copyBehavior** . Ją zachowuje hierarchię, spłaszcza hierarchii lub scala plików. Zachowywanie albo spłaszczanie hierarchii ma niewielki lub zmniejszenie wydajności, ale scalanie plików powoduje zmniejszenie wydajności zwiększyć.
* **Format pliku i kompresowanie**: Zapoznaj się z [zagadnieniami dotyczącymi serializacji i deserializacji](#considerations-for-serialization-and-deserialization) oraz [zagadnień dotyczących kompresji](#considerations-for-compression) , aby uzyskać więcej sposobów na zwiększenie wydajności.
* **BLOB Storage**: obecnie magazyn obiektów BLOB obsługuje tylko blokowe obiekty blob w celu zoptymalizowania transferu danych i przepływności.
* W przypadku scenariuszy **z lokalnymi systemami plików** , które wymagają korzystania z **bramy zarządzanie danymi**, zobacz sekcję [zagadnienia dotyczące zarządzanie danymi bramy](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Magazyny danych relacyjnych
*(Zawiera SQL Database, SQL Data Warehouse, SQL Server baz danych i bazy danych Oracle)*

* **Zachowanie kopiowania**: w zależności od właściwości ustawionych dla elementu **sqlsink**, działanie Copy zapisuje dane w docelowej bazie danych na różne sposoby.
  * Domyślnie używa usługi przenoszenia danych interfejsu API kopiowania zbiorczego wstawiania danych w Dołącz trybu, który zapewnia najlepszą wydajność.
  * Jeśli skonfigurujesz procedury składowanej w ujściu bazy danych stosuje się jeden wiersz danych w czasie zamiast jako ładowania zbiorczego. Wydajności znacznie spada. Jeśli zestaw danych jest duży, w razie potrzeby Rozważ przełączenie do użycia właściwości **sqlWriterCleanupScript** .
  * W przypadku skonfigurowania właściwości **sqlWriterCleanupScript** dla każdego przebiegu działania kopiowania usługa wyzwala skrypt, a następnie korzysta z interfejsu API kopiowania zbiorczego do wstawiania danych. Na przykład aby zastąpić całą tabelę przy użyciu najnowszych danych, można określić skrypt, aby usunąć wszystkie rekordy przed ładowania zbiorczego nowe dane ze źródła.
* **Wzorzec danych i rozmiar wsadu**:
  * Schemat tabeli ma wpływ na przepływność kopiowania. Aby skopiować podobną ilość danych, rozmiaru duży wiersz zapewnia lepszą wydajność niż rozmiar wiersza małych ponieważ bazy danych można efektywniej zatwierdzić mniejszej liczby partii danych.
  * Działanie kopiowania wstawia dane z serii partii. Można ustawić liczbę wierszy w partii przy użyciu właściwości **writeBatchSize** . Jeśli dane zawierają małe wiersze, można ustawić właściwość **writeBatchSize** o wyższej wartości, aby korzystać z niższych nakładów wsadowych i większej przepływności. Jeśli rozmiar wiersza danych jest duży, należy zachować ostrożność w przypadku zwiększenia **writeBatchSize**. O wysokiej wartości może prowadzić do awarii kopiowania spowodowane przeciążeniem bazy danych.
* **W przypadku lokalnych relacyjnych baz danych** , takich jak SQL Server i Oracle, które wymagają korzystania z **bramy zarządzanie danymi**, zobacz sekcję [uwagi dotyczące bramy zarządzanie danymi](#considerations-for-data-management-gateway) .

### <a name="nosql-stores"></a>Magazynów NoSQL
*(Obejmuje magazyn tabel i Azure Cosmos DB)*

* W przypadku usługi **Table Storage**:
  * **Partycja**: zapisywanie danych na partycjach z przeplotem znacznie zmniejsza wydajność. Sortowanie danych źródła według klucza partycji, dzięki czemu dane są wstawiane wydajnie w jednej partycji po drugim lub dostosować logiki można zapisać danych do jednej partycji.
* Dla **Azure Cosmos DB**:
  * **Rozmiar wsadu**: Właściwość **writeBatchSize** ustawia liczbę żądań równoległych do usługi Azure Cosmos DB, aby można było tworzyć dokumenty. Po zwiększeniu **writeBatchSize** można oczekiwać większej wydajności, ponieważ do Azure Cosmos DB są wysyłane więcej żądań równoległych. Należy jednak zapoznać się z ograniczeniami podczas zapisywania do Azure Cosmos DB (komunikat o błędzie: "częstotliwość żądań jest duża"). Różne czynniki mogą prowadzić do ograniczania przepustowości, w tym rozmiaru dokumentu, liczby warunków w dokumentach oraz zasad indeksowania kolekcji docelowej. Aby osiągnąć wyższą przepływność kopiowania, rozważ użycie większej kolekcji, na przykład S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Zagadnienia dotyczące serializacji i deserializacji
Serializacja i deserializacja może wystąpić, gdy Twoje wejściowego zestawu danych lub zestawu danych wyjściowych jest plik. Zobacz [obsługiwane formaty plików i kompresji](data-factory-supported-file-and-compression-formats.md) ze szczegółowymi informacjami na temat obsługiwanych formatów plików przez działanie kopiowania.

**Zachowanie kopiowania**:

* Kopiowanie plików między magazynami danych opartych na plikach:
  * Gdy zestawy danych wejściowych i wyjściowych mają takie same ustawienia formatu plików, Usługa przenoszenia danych wykonuje kopię binarną bez serializacji lub deserializacji. Zostanie wyświetlony wyższej przepustowości w porównaniu do scenariusza, w której ustawienia formatu pliku źródła i ujścia różnią się od siebie nawzajem.
  * Gdy dane wejściowe i dane wyjściowe zestawy danych zarówno w formacie tekstowym, a tylko kodowanie typ jest inny, usługi data movement service jest wyłącznie Konwersja kodowania. Nie wszystkie serializacji i deserializacji, co powoduje, że niektóre wydajności, obciążenie w porównaniu do kopia binarna.
  * Jeśli wejściowe i wyjściowe zestawy danych zarówno mają różne formaty plików lub różne konfiguracje, takie jak ograniczników, usługi data movement service deserializuje dane źródłowe do strumienia, przekształcania i serializować go do formatu wyjściowego, wskazane przez Ciebie. Ta operacja powoduje znacznie bardziej znaczące wydajności, obciążenie w porównaniu do innych scenariuszy.
* Podczas kopiowania plików do/z magazynu danych, który nie jest oparte na plikach (na przykład z magazynu oparte na plikach do relacyjnego magazynu) krok serializacji lub deserializacji jest wymagany. Ten krok powoduje znaczne obciążenie.

**Format pliku**: wybrany format pliku może mieć wpływ na wydajność kopiowania. Na przykład Avro jest kompaktowego formatu binarnego, który przechowuje metadane z danymi. Posiada obsługi szerokiej gamy w ekosystemie usługi Hadoop do przetwarzania i wykonywania zapytań. Jednak Avro jest droższe do serializacji i deserializacji, co skutkuje niższe przepływności kopiowania w porównaniu do formatu tekstowego. Wybierz ustawienia formatu pliku w całym przepływie przetwarzania całościowo. Rozpoczyna się od co tworzą dane są przechowywane w magazynach danych źródłowych lub ma zostać wyodrębniony z systemów zewnętrznych; najlepszy format dla magazynu, przetwarzanie analityczne i wykonywania zapytań; i w jakim formacie dane powinny być eksportowane do składnic danych programów dla narzędzia do raportowania i wizualizacji. Czasami formatu pliku nieoptymalne do odczytu i zapisu wydajność może być dobrym rozwiązaniem, gdy należy wziąć pod uwagę całkowity analitycznych procesu.

## <a name="considerations-for-compression"></a>Zagadnienia dotyczące kompresji
Gdy zestaw danych wejściowych lub wyjściowych jest plik, można ustawić działania kopiowania, które można wykonać kompresji lub dekompresji, ponieważ zapisuje dane do lokalizacji docelowej. Po wybraniu kompresji, upewnij się zależność między wejścia/wyjścia (We/Wy) i procesora CPU. Kompresowanie danych koszty dodatkowe zasoby obliczeniowe. Ale w zamian zmniejsza we/wy sieci i magazynu. W zależności od danych może zostać wyświetlony boost w ogólną przepływność kopiowania.

**Koder-dekoder**: działanie Copy obsługuje typy kompresji gzip, BZIP2 i Wklęśnięcie. Usługa Azure HDInsight może zużywać wszystkie trzy typy do przetwarzania. Każdy koder-dekoder kompresji ma zalety. Na przykład bzip2 ma najniższą przepływności kopiowania, ale uzyskać najlepszą wydajność zapytań Hive przy użyciu bzip2, ponieważ podzielić ją do przetworzenia. Gzip jest to opcja najbardziej o zrównoważonym obciążeniu, a jest najczęściej używany. Wybierz koder-dekoder, który najlepiej odpowiada Twojemu scenariuszowi end-to-end.

**Poziom**: można wybrać jedną z dwóch opcji dla każdego kodera kodek kompresji: najszybszy skompresowany i optymalnie skompresowany. Najszybciej skompresowany opcji kompresuje dane tak szybko, jak to możliwe, nawet wtedy, gdy wynikowy plik nie jest optymalnie skompresowany. Opcja optymalnie skompresowany zużywa więcej czasu na kompresji i daje minimalnej ilości danych. Możesz przetestować obie opcje, aby zobaczyć, który zapewnia lepszą wydajność ogólną w Twoim przypadku.

**Uwaga**: Aby skopiować dużą ilość danych między magazynem lokalnym i chmurą, należy rozważyć użycie tymczasowego magazynu obiektów blob z kompresją. Przy użyciu magazynu tymczasowego jest przydatne, gdy przepustowość sieci firmowej i usług platformy Azure jest czynnikiem ograniczającym i chcesz wejściowy zestaw danych i zestawie danych wyjściowych zarówno w postaci bez kompresji. Dokładniej, można przerwać pojedyncze działanie kopiowania w dwóch działaniach kopiowania. Pierwsze działanie kopiowania Kopiuje ze źródła do tymczasowego lub tymczasowego obiektu BLOB w postaci skompresowanej. Drugie działanie kopiowania Kopiuje skompresowane dane z przemieszczania, a następnie dekompresuje podczas zapisywania do ujścia.

## <a name="considerations-for-column-mapping"></a>Informacje dotyczące mapowania kolumn
Można ustawić właściwość **ColumnMappings** w działaniu Copy, aby mapować wszystkie lub podzbiór kolumn wejściowych do kolumn danych wyjściowych. Po usługi data movement service odczytuje dane ze źródła, trzeba wykonać mapowania kolumn na danych, zanim go zapisuje dane do ujścia. To dodatkowe przetwarzanie zmniejsza przepustowość kopiowania.

Jeśli źródłowy magazyn danych to queryable, na przykład, jeśli jest to magazyn relacyjny, taki jak SQL Database lub SQL Server, lub jeśli jest to Magazyn NoSQL, taki jak Table Storage lub Azure Cosmos DB, rozważ wypchnięcie reguły filtrowania kolumn i zmiany kolejności dla właściwości **zapytania** zamiast korzystania z mapowania kolumn. W ten sposób projekcji występuje, gdy usługi data movement service odczytuje dane z magazynu danych źródłowych, w którym jest znacznie bardziej efektywne.

## <a name="other-considerations"></a>Inne zagadnienia
Jeśli rozmiar danych, które mają zostać skopiowane, jest duży, można dostosować logikę biznesową, aby dodatkowo podzielić dane przy użyciu mechanizmu odcinania w Data Factory. Następnie należy zaplanować działanie kopiowania częściej, aby zmniejszyć rozmiar danych dla każdego uruchomienia działania kopiowania.

Należy zachować ostrożność w zakresie liczby zestawów danych i działań kopiowania wymagających Data Factory łączników do tego samego magazynu danych w tym samym czasie. Wiele zadań jednoczesnych kopii może ograniczać do przechowywania danych i spowodować pogorszenie wydajności, kopii zadania wewnętrzne ponownych prób, a w niektórych przypadkach niepowodzenia wykonywania.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Przykładowy scenariusz: kopiowanie danych z lokalnego serwera SQL do magazynu obiektów Blob
**Scenariusz**: potok został utworzony w celu kopiowania danych z SQL Server lokalnego do magazynu obiektów BLOB w formacie CSV. Aby przyspieszyć zadanie kopiowania, pliki CSV należy skompresowane do formatu bzip2.

**Testowanie i analiza**: przepływność działania kopiowania jest mniejsza niż 2 MB/s, co jest znacznie wolniejsze niż wynikowy test wydajności.

**Analiza wydajności i dostrajanie**: Aby rozwiązać problem z wydajnością, przyjrzyjmy się, jak dane są przetwarzane i przenoszone.

1. **Odczytaj dane**: Brama otwiera połączenie w celu SQL Server i wysyła zapytanie. SQL Server odpowiada, wysyłając strumień danych do bramy za pośrednictwem intranetu.
2. **Serializowanie i kompresowanie danych**: Brama serializować strumień danych do formatu CSV i kompresuje dane do strumienia bzip2.
3. **Zapisz dane**: Brama przekazuje strumień bzip2 do magazynu obiektów BLOB za pośrednictwem Internetu.

Jak widać, dane są przetwarzane i przenoszone w sposób sekwencyjny przesyłania strumieniowego: SQL Server > LAN > Gateway > sieci WAN > BLOB Storage. **Ogólna wydajność jest planowana przez minimalną przepływność w potoku**.

![Przepływ danych](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Co najmniej jeden z następujących czynników może spowodować wąskie gardło:

* **Źródło**: SQL Server samo ma niską przepływność ze względu na duże obciążenia.
* **Zarządzanie danymi Gateway**:
  * **LAN**: Brama znajduje się daleko od maszyny SQL Server i ma połączenie o niskiej przepustowości.
  * **Brama**: Brama osiągnęła swoje ograniczenia dotyczące obciążenia, aby wykonać następujące operacje:
    * **Serializacja**: Serializowanie strumienia danych do formatu CSV ma niską przepływność.
    * **Kompresowanie**: wybrano wolny koder-dekoder kompresji (na przykład bzip2, czyli 2,8 MB/s z rdzeniem Core i7).
  * **Sieć WAN**: przepustowość między siecią firmową i usługami platformy Azure jest niska (na przykład T1 = 1 544 KB/s; T2 = 6 312 KB/s).
* **Ujścia**: Magazyn obiektów BLOB ma niską przepływność. (W tym scenariuszu jest mało prawdopodobne, ponieważ jej umowa SLA gwarantuje co najmniej 60 MB/s).

W tym przypadku bzip2 kompresji danych może być spowalniania cały potok. Przełączanie do kodera-dekodera kompresji gzip może jej obsługi ułatwiają realizację tego wąskiego gardła.

## <a name="sample-scenarios-use-parallel-copy"></a>Przykładowe scenariusze: Użyj kopiowania równoległego
**Scenariusz:** Skopiuj pliki 1 000 1-MB z lokalnego systemu plików do magazynu obiektów BLOB.

**Dostrajanie analizy i wydajności**: na przykład jeśli masz zainstalowaną bramę na maszynie z czterema procesorami, Data Factory używa 16 równoległych kopii do przenoszenia plików z systemu plików do magazynu obiektów BLOB współbieżnie. To wykonywanie równoległe powinno powodować wysoką przepływność. Można również jawnie określić liczbę kopii równoległych. W przypadku kopiowania wielu małych plików kopie równoległe znacznie ułatwiają przepływność przy użyciu zasobów.

![Scenariusz 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenariusz II**: Kopiuj 20 obiektów blob z 500 MB każdego z magazynu obiektów Blob do Data Lake Store Analytics, a następnie dostosuj wydajność.

**Dostrajanie analizy i wydajności**: w tym scenariuszu Data Factory kopiuje dane z usługi BLOB storage do Data Lake Store przy użyciu pojedynczej kopii (**parallelCopies** ) i jednostek przenoszenia danych w chmurze. Zaobserwuj przepływność w pobliżu tego, co zostało opisane w [sekcji Informacje o wydajności](#performance-reference).

![Scenariusz 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scenariusz III**: rozmiar pojedynczego pliku jest większy niż dziesiątki MB, a całkowita ilość jest duża.

**Włączanie analizy i wydajności**: zwiększenie **parallelCopies** nie powoduje zwiększenia wydajności kopiowania ze względu na ograniczenia zasobów jednej chmury DMU. Zamiast tego należy określić więcej DMUs w chmurze, aby uzyskać więcej zasobów do przeprowadzenia przenoszenia danych. Nie określaj wartości właściwości **parallelCopies** . Data Factory obsługuje równoległości. W tym przypadku ustawienie **cloudDataMovementUnits** na 4 spowoduje przepełnienie przepływności około czterech razy.

![Scenariusz 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Dokumentacja
Poniżej znajdują się informacje dotyczące monitorowania wydajności i dostrajania dla niektórych obsługiwanych magazynów danych:

* Azure Blob Storage: [elementy docelowe skalowalności i wydajności dla magazynu obiektów BLOB](../../storage/blobs/scalability-targets.md) i [wydajności i skalowalności dla usługi BLOB Storage](../../storage/blobs/storage-performance-checklist.md).
* Azure Table Storage: [elementy docelowe skalowalności i wydajności dla magazynu tabel](../../storage/tables/scalability-targets.md) oraz [listę kontrolną wydajności i skalowalności w magazynie tabel](../../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: można [monitorować wydajność](../../sql-database/sql-database-single-database-monitor.md) i sprawdzać wartość procentową jednostki transakcji bazy danych (DTU)
* Azure SQL Data Warehouse: jej możliwości są mierzone w jednostkach magazynu danych (jednostek dwu); Zobacz [zarządzanie mocą obliczeniową w Azure SQL Data Warehouse (omówienie)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [poziomy wydajności w Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* SQL Server lokalny: [monitorowanie i dostrajanie wydajności](https://msdn.microsoft.com/library/ms189081.aspx)
* Lokalny serwer plików: [dostrajanie wydajności dla serwerów plików](https://msdn.microsoft.com/library/dn567661.aspx)
