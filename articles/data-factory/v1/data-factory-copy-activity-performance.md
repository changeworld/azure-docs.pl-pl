---
title: Skopiuj wydajności działania i dostrajania przewodnik | Dokumentacja firmy Microsoft
description: Więcej informacji na temat kluczowych czynników wpływających na wydajność przepływu danych w fabryce danych Azure, korzystając z działania kopiowania.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b54138c5197d1c5870eed6fd4782e47c6a8b0300
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Skopiuj wydajności działania i dostrajania przewodnik

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-copy-activity-performance.md)
> * [Wersja 2 — wersja zapoznawcza](../copy-activity-performance.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [skopiuj dostrajania przewodnik dla fabryki danych w wersji 2 i wydajności działania](../copy-activity-performance.md).

Działanie kopiowania fabryki danych Azure oferuje najwyższej jakości danych bezpieczne, niezawodne i wysoko wydajnych ładowania rozwiązania. Go umożliwia utworzenie kopii dziesiątki terabajtów danych codziennie przez szeroki zakres chmury i lokalnych magazynów danych. Ładowanie wydajności danych ogromną fast ma kluczowe znaczenie dla zapewnienia można skoncentrować się na temat problemu "danych big data" core: kompilowanie rozwiązań zaawansowane analizy i uzyskiwanie szczegółowych informacji z wszystkie te dane.

Platforma Azure oferuje zestaw korporacyjnej rozwiązania dotyczące danych magazynu i danych magazynu i działanie kopiowania oferuje zoptymalizowanego ładowania środowisko, która jest łatwa do skonfigurowania i danych. Z po prostu działanie pojedynczej kopii można uzyskać:

* Ładowanie danych do **magazyn danych SQL Azure** w **1,2 GB/s**. Aby uzyskać wskazówki z przypadkiem użycia, zobacz [załadować 1 TB do usługi Azure SQL Data Warehouse z fabryką danych Azure w obszarze 15 minut](data-factory-load-sql-data-warehouse.md).
* Ładowanie danych do **magazynu obiektów Blob Azure** na **1.0 GB/s**
* Ładowanie danych do **Azure Data Lake Store** na **1.0 GB/s**

W tym artykule opisano:

* [Numery wydajności](#performance-reference) obsługiwane źródłowy i odbiorczy magazyny danych do planowania projektu.
* Funkcje, które może zwiększyć przepływność kopiowania w różnych scenariuszy, w tym [jednostki przepływu danych w chmurze](#cloud-data-movement-units), [równoległych kopii](#parallel-copy), i [przemieszczane kopiowania](#staged-copy);
* [Wskazówki dotyczące dostrajania wydajności](#performance-tuning-steps) w sposób dostrajania wydajności i kluczowych czynników, które mogą mieć wpływ na wydajność kopiowania.

> [!NOTE]
> Jeśli nie masz doświadczenia w obsłudze działania kopiowania ogólnie rzecz biorąc, zobacz [przenoszenia danych za pomocą działania kopiowania](data-factory-data-movement-activities.md) przed przeczytaniem tego artykułu.
>

## <a name="performance-reference"></a>Informacje dotyczące wydajności

Jako odwołanie pod tabelą pokazuje liczbę przepływności kopiowania w MB/s dla danego par źródłowy i odbiorczy testów wewnętrznych. Porównanie można go również pokazano, jak różne ustawienia [jednostki przepływu danych w chmurze](#cloud-data-movement-units) lub [skalowalność brama zarządzania danymi](data-factory-data-management-gateway-high-availability-scalability.md) (wiele węzłów bramy) pozwalają na wydajność kopiowania.

![Macierz wydajności](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>W fabryce danych Azure w wersji 1 jednostki przepływu danych chmury minimalnego kopiowania w chmurze na chmurze wynosi dwa. Jeśli nie zostanie określony, zobacz jednostki przepływu danych domyślne używane w [jednostki przepływu danych w chmurze](#cloud-data-movement-units).

**Informacje, które należy zwrócić uwagę:**
* Przepływność jest obliczana przy użyciu następującej formuły: [read rozmiar danych ze źródła] / [czas trwania działania kopiowania].
* Numery wydajności w tabeli są mierzone przy użyciu [TPC-H](http://www.tpc.org/tpch/) zestawu danych w działaniu pojedynczej kopii Uruchom.
* W magazynie danych Azure źródło i ujście są tego samego regionu platformy Azure.
* Dla kopii hybrydowej między lokalnymi i w chmurze magazyny danych każdy węzeł bramy była uruchomiona na komputerze, który został oddzielony od z lokalnym magazynem danych z poniżej specyfikacji. Operacja kopiowania pojedyncze działanie była uruchomiona na bramy, używane tylko niewielką część procesora CPU, pamięć lub przepustowość sieci testowej maszyny. Dowiedz się więcej o [brany pod uwagę dla bramy zarządzania danymi](#considerations-for-data-management-gateway).
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
        <td>Interfejs internetowy: 10 GB/s; interfejsu intranetowego: 40 GB/s</td>
    </tr>
    </table>


> [!TIP]
> Wyższej przepustowości można osiągnąć dzięki wykorzystaniu jednostki przepływu więcej danych (DMUs) niż domyślna maksymalna DMUs, co stanowi 32 dla działania kopiowania w chmurze na chmurze, uruchom. Na przykład z 100 DMUs, możesz osiągnąć kopiowanie danych z obiektu Blob Azure do usługi Azure Data Lake Store **1.0GBps**. Zobacz [jednostki przepływu danych w chmurze](#cloud-data-movement-units) sekcji, aby uzyskać więcej informacji dotyczących tej funkcji i obsługiwany scenariusz. Skontaktuj się z [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/) żądania DMUs więcej.

## <a name="parallel-copy"></a>Kopiuj równoległych
Można odczytać danych ze źródła lub zapisać danych do lokalizacji docelowej **równolegle w ramach działania kopiowania, uruchom**. Ta funkcja rozszerza przepływność operacji kopiowania i skraca czas potrzebny do przenoszenia danych.

To ustawienie jest inny niż **współbieżności** właściwości w definicji działania. **Współbieżności** właściwość określa liczbę **uruchamia równoczesne działanie kopiowania** do przetwarzania danych z innego działania systemu windows (1: 00 do 2 AM, 2 AM 3 AM 3 AM do 4 AM i tak dalej). Ta możliwość jest przydatne podczas wykonywania historycznych obciążenia. Możliwość kopiowania równoległych dotyczy **pojedynczy uruchamiania działania**.

Oto przykładowy scenariusz. W poniższym przykładzie wielu wycinków z przeszłości muszą być przetworzone. Fabryka danych uruchamia wystąpienia działania kopiowania (Uruchom działania) dla każdego wycinka:

* Wycinek danych z pierwszym oknie działania (1: 00 AM do 2) == > działania Uruchom 1
* Wycinek danych z drugiego okna działania (2 AM do 3 AM) == > działania Uruchom 2
* Wycinek danych z drugiego okna działania (3 AM do 4: 00) == > działania Uruchom 3

I tak dalej.

W tym przykładzie podczas **współbieżności** wartość jest równa 2, **działania Uruchom 1** i **działania Uruchom 2** skopiować dane z dwóch okien działania **jednocześnie** Aby zwiększyć wydajność przepływu danych. Jednak jeśli wiele plików są skojarzone z działania Uruchom 1, usługa przenoszenia danych kopiuje pliki ze źródła do jednego pliku docelowego naraz.

### <a name="cloud-data-movement-units"></a>Jednostki przepływu danych w chmurze
A **jednostki przepływu danych w chmurze (DMU)** miary, która odzwierciedla wydajność (kombinacja Procesora, pamięci i alokacji zasobów w sieci) w pojedynczą jednostkę w fabryce danych. DMU ma zastosowanie do operacji kopiowania w chmurze do chmury, ale nie w kopii hybrydowej.

**Jednostki przepływu danych minimalnego chmury dla uruchamiania działania kopiowania wynosi dwa.** Jeśli nie zostanie określony, w poniższej tabeli wymieniono DMUs domyślne używane w scenariuszach różnych kopiowania:

| Skopiuj scenariusza | Domyślne DMUs określone przez usługę |
|:--- |:--- |
| Kopiowanie danych między magazynów opartych na plikach | Od 2 do 16, w zależności od liczby i rozmiaru plików. |
| Inne scenariusze kopiowania | 2 |

Aby zastąpić to ustawienie domyślne, należy określić wartość dla **cloudDataMovementUnits** właściwości w następujący sposób. **Dozwolone wartości** dla **cloudDataMovementUnits** to 2, 4, 8, 16, 32. **Rzeczywistą liczbę chmury DMUs** używany w czasie wykonywania operacji kopiowania jest równa lub mniejsza niż skonfigurowana wartość, w zależności od tego wzorca sieci danych. Uzyskać informacje na temat poziomu są bardziej wydajne, można uzyskać po skonfigurowaniu więcej jednostek dla konkretnej kopii źródłowy i odbiorczy, zobacz [dotyczące wydajności](#performance-reference).

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
> Jeśli potrzebujesz więcej chmury DMUs umożliwiających uzyskanie większej produktywności, skontaktuj się z [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/). Obecnie działa tylko wtedy, gdy ustawienie 8 i nowszych można **skopiować wielu plików z obiektu Blob magazynu/Data Lake Store/Amazon S3/w chmurze SFTP FTP/w chmurze do obiektu Blob magazynu/Data Lake Store/usługi Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies
Można użyć **parallelCopies** Właściwość wskazująca równoległości interesujące Kopiuj działania do użycia. Tej właściwości można traktować jako maksymalną liczbę wątków w ramach działania kopiowania, który można ze źródła do odczytu lub zapisu z magazynów danych zbiornika równolegle.

Dla każdego działania kopiowania Uruchom fabryki danych określa liczbę równoległych kopii umożliwia kopiowanie danych z źródła danych, przechowywania i dane miejsce docelowe przechowywania. Domyślna liczba równoległych kopii, których używa zależy od typu źródłowy i odbiorczy, którego używasz.  

| Źródłowy i odbiorczy | Liczba równoległych kopii domyślne określone przez usługę |
| --- | --- |
| Kopiowanie danych między magazynów opartych na plikach (magazynu obiektów Blob; Data Lake Store; Amazon S3; lokalnego systemu plików; lokalny system plików HDFS) |Od 1 do 32. Zależy od rozmiaru plików i liczby jednostek przepływu danych chmury (DMUs) umożliwia kopiowanie danych między dwa magazyny danych w chmurze lub fizycznych Konfiguracja komputera bramy, służącą do kopii hybrydowej (kopiowanie danych do lub z lokalnego magazynu danych). |
| Kopiowanie danych z **źródła danych magazynu do magazynu tabel Azure** |4 |
| Wszystkie inne źródłowy i odbiorczy pary |1 |

Zazwyczaj domyślne zachowanie powinien zapewnić najlepszą wydajność. Jednak do kontrolowania obciążenia na maszynach hostujących dane są przechowywane lub do dopasowywania wydajności kopii, możesz zastąpić wartość domyślną i określ wartość **parallelCopies** właściwości. Wartość musi być od 1 do 32 (zarówno z wartościami granicznymi). W czasie wykonywania Aby uzyskać najlepszą wydajność, działanie kopiowania używa wartość, która jest mniejsza lub równa wartości zostanie ustawiona.

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

* Po skopiowaniu danych między magazynów opartych na plikach, **parallelCopies** określić równoległości na poziomie plików. Podziału w jednym pliku sytuacja może mieć miejsce pod automatycznie i w sposób niewidoczny dla użytkownika, a zostało zaprojektowane na potrzeby ładowania danych w równoległych i prostopadłym do parallelCopies najlepsze rozmiar fragmentu odpowiedniego dla typu danego źródła danych magazynu. Rzeczywista liczba równoległych kopii używa usługi przenoszenia danych w czasie wykonywania operacji kopiowania jest nie więcej niż liczba plików. Jeśli jest to zachowanie kopii **mergeFile**, aktywność kopiowania nie może korzystać z równoległości poziomie plików.
* Po określeniu wartość **parallelCopies** właściwość, należy wziąć pod uwagę wzrost obciążenia na magazyny danych źródłowy i odbiorczy, a także do bramy, jest kopią hybrydowego. Ma to miejsce, zwłaszcza jeśli masz wiele działań lub uruchamia równoczesnych działań uruchamianych z tego samego magazynu danych. Jeśli okaże się, że magazyn danych lub bramy jest przeciążony obciążenia w, Zmniejsz **parallelCopies** wartość na zmniejszenie obciążenia.
* Po skopiowaniu danych z magazynów, które nie są opartą na plikach do magazynów, które są oparte na pliku usługi przenoszenia danych ignoruje **parallelCopies** właściwości. Nawet wtedy, gdy określono równoległości, nie zostanie zastosowane w tym przypadku.

> [!NOTE]
> Należy użyć bramy zarządzania danymi w wersji 1.11 lub nowszej, aby użyć **parallelCopies** funkcji po wykonaniu kopii hybrydowej.
>
>

Aby lepiej wykorzystać te dwie właściwości i ulepszyć Twoje przepływność przepływu danych, zobacz [przykładowe przypadki użycia](#case-study-use-parallel-copy). Nie trzeba skonfigurować **parallelCopies** mógł korzystać z zachowaniem domyślnym. Jeśli skonfigurujesz i **parallelCopies** jest za mały, chmury wielu DMUs może nie być w pełni wykorzystywane.  

### <a name="billing-impact"></a>Wpływ rozliczeń
Ma ona **ważne** pamiętać, że są naliczane na podstawie całkowitej czasu operacji kopiowania. Jeśli zadanie kopiowania używane do podjęcia godzinę z jednostką jednej chmury i obecnie trwa 15 minut z czterech jednostki chmury, zestawienie ogólnej pozostaje prawie takie same. Na przykład używasz cztery jednostki chmury. Pierwszy jednostki chmury spędza na 10 minut, a drugi, 10 minut, trzeci co 5 minut, a czwarty co 5 minut, uruchom wszystko w jednym działaniu kopiowania. Naliczane są opłaty za czas całkowita kopiowania (przenoszenie danych), który jest 10 + 10 + 5 + 5 = 30 minut. Przy użyciu **parallelCopies** nie ma wpływu na rozliczenia.

## <a name="staged-copy"></a>Kopiuj przemieszczanego
Po skopiowaniu danych z magazynu danych źródła do ujścia magazynu danych można używać magazynu obiektów Blob jako magazyn tymczasowy tymczasowej. Przemieszczania jest szczególnie przydatne w następujących przypadkach:

1. **Aby pozyskiwania danych z różnych baz danych do usługi SQL Data Warehouse przy użyciu programu PolyBase**. Usługa SQL Data Warehouse używa PolyBase jako mechanizm wysokiej przepustowości ładowanie dużej ilości danych do usługi SQL Data Warehouse. Źródło danych musi być w magazynie obiektów Blob i musi spełniać kryteria. Podczas ładowania danych z magazynu danych innego niż magazynu obiektów Blob, możesz przeprowadzić aktywację dane kopiowanie za pośrednictwem tymczasowego przemieszczania magazynu obiektów Blob. W takim przypadku fabryki danych wykonuje przekształcenia danych wymagane, aby upewnić się, że spełnia wymagania programu PolyBase. Następnie używa PolyBase, aby załadować dane do usługi SQL Data Warehouse. Aby uzyskać więcej informacji, zobacz [Użyj programu PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). Aby uzyskać wskazówki z przypadkiem użycia, zobacz [załadować 1 TB do usługi Azure SQL Data Warehouse z fabryką danych Azure w obszarze 15 minut](data-factory-load-sql-data-warehouse.md).
2. **Czasami zajmuje trochę czasu, aby wykonać przenoszenia danych hybrydowych (oznacza to, aby skopiować między danymi lokalnymi magazynu i danych w chmurze magazynu) przez powolne połączenie sieciowe**. Aby zwiększyć wydajność, można skompresować danych w sieci lokalnej, aby zajmuje mniej czasu, aby przenieść dane do przemieszczania danych przechowywanych w chmurze. Następnie można zdekompresować danych w magazynie przemieszczania przed załadowaniem w magazynie danych docelowym.
3. **Nie chcesz otworzyć porty inne niż port 80 i portu 443 w zaporze, z powodu zasad firmowych IT**. Na przykład podczas kopiowania danych z lokalnego magazynu danych do zbiornika bazy danych SQL Azure lub ujścia magazynu danych SQL Azure, musisz aktywować komunikacji wychodzącej TCP na porcie 1433 dla zapory systemu Windows i firmowej zapory. W tym scenariuszu korzystać z bramy do pierwszego kopiowania danych do wystąpienia tymczasowej magazynu obiektów Blob za pośrednictwem protokołu HTTP lub HTTPS na porcie 443. Następnie załadować dane do bazy danych SQL lub SQL Data Warehouse z obszaru przemieszczania magazynu obiektów Blob. W tym przepływie nie trzeba włączyć port 1433.

### <a name="how-staged-copy-works"></a>Jak przemieszczanego działania kopiowania
Po aktywowaniu funkcji przemieszczania najpierw dane są kopiowane z magazynu danych źródła w magazynie danych tymczasowych (użycie własnego). Następnie dane są kopiowane z magazynu danych tymczasowych do ujścia magazynu danych. Fabryka danych automatycznie zarządza przepływu dwuetapowej dla Ciebie. Fabryka danych czyści dane z magazynu przemieszczania także po zakończeniu przenoszenia danych.

Brama nie jest używany w scenariuszu kopiowania do chmury (źródłowy i odbiorczy dane magazynów znajdują się w chmurze). Usługi fabryka danych z wykonuje operacje kopiowania.

![Przemieszczane kopiowania: Scenariusz chmury](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

W scenariuszu hybrydowym kopiowania (źródło jest lokalnie i sink znajduje się w chmurze), bramy przenosi dane z magazynu danych źródła do tymczasowej magazynu danych. Usługi fabryka danych przenosi dane z magazynu danych tymczasowych do ujścia magazynu danych. Kopiowanie danych z magazynu danych chmury z magazynem danych lokalnych za pośrednictwem trybu przejściowego jest również obsługiwane z przepływem odwróconej.

![Przemieszczane kopiowania: scenariusza hybrydowego](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Po uaktywnieniu przepływu danych przy użyciu tymczasowego magazynu można określić, czy mają dane skompresowane przed przeniesieniem danych z magazynu danych źródła z magazynem danych tymczasowych lub przemieszczania, a następnie dekompresowane przed przenoszenia danych z przejściowej lub przemieszczania danych Przechowaj ujścia magazynu danych.

Obecnie nie można skopiować danych między dwa lokalnych magazynów danych przy użyciu magazynu tymczasowego. Oczekujemy tę opcję, aby wkrótce dostępna.

### <a name="configuration"></a>Konfigurowanie
Skonfiguruj **enableStaging** ustawienie w przypadku działania kopiowania, aby określić, czy dane umieszczane w magazynie obiektów Blob, przed załadowaniem do magazynu danych docelowym. Podczas ustawiania **enableStaging** na wartość PRAWDA, określ dodatkowe właściwości, które są wymienione w następnej tabeli. Jeśli nie masz, należy także utworzyć magazynu Azure lub magazynu udostępnionego usługi połączone podpisu dostępu dla przemieszczania.

| Właściwość | Opis | Wartość domyślna | Wymagane |
| --- | --- | --- | --- |
| **enableStaging** |Określ, czy chcesz skopiować dane za pośrednictwem przejściowej przemieszczania magazynu. |False |Nie |
| **linkedServiceName** |Określ nazwę [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) lub [element AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) połączonej usługi, która odwołuje się do wystąpienia magazynu, który jest używany jako magazyn tymczasowy tymczasowej. <br/><br/> Aby załadować dane do usługi SQL Data Warehouse przy użyciu programu PolyBase nie można używać magazynu sygnatury dostępu współdzielonego. Można go użyć w innych scenariuszach. |ND |Tak, gdy **enableStaging** ma wartość TRUE |
| **Ścieżka** |Określ ścieżki do magazynu obiektów Blob, który ma zawierać przemieszczonych danych. Jeśli ścieżka nie zostanie określona, usługa tworzy kontener do przechowywania danych tymczasowych. <br/><br/> Określ ścieżkę tylko w przypadku używania magazynu z sygnatury dostępu współdzielonego lub wymagają dane tymczasowe w określonej lokalizacji. |ND |Nie |
| **enableCompression** |Określa, czy dane powinny skompresowany, zanim zostanie on skopiowany do miejsca docelowego. To ustawienie pozwala ograniczyć ilość danych transferowanych. |False |Nie |

Oto przykład definicji działanie kopiowania z właściwościami, które zostały opisane w powyższej tabeli:

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
Są naliczane na podstawie dwa kroki: kopiowanie czas trwania i skopiować typu.

* Gdy używasz przemieszczania podczas kopiowania chmury (kopiowanie danych z magazynu danych chmury do innego magazynu danych w chmurze), naliczane są opłaty [łączny czas trwania kopiowania kroki 1 i 2] x [cenie jednostkowej kopiowania chmury].
* Gdy używasz przemieszczania podczas kopiowania hybrydowych (kopiowanie danych z lokalnego magazynu danych w magazynie danych w chmurze), naliczane są opłaty [hybrydowego kopiowania czas] x [cenie jednostkowej kopiowania hybrydowego] + [czas trwania kopiowania w chmurze] x [cenie jednostkowej kopiowania chmury].

## <a name="performance-tuning-steps"></a>Kroki dostrajania wydajności
Zaleca się, że należy wykonać następujące kroki w celu dostrojenia wydajności usługi fabryka danych z działania kopiowania:

1. **Ustalanie linii bazowej**. W fazie projektowania przetestować potoku sieci za pomocą działania kopiowania przed przykładowych danych reprezentatywnych. Korzystając z fabryką danych [fragmentowania modelu](data-factory-scheduling-and-execution.md) Aby ograniczyć ilość danych z.

   Zbieranie czasu wykonywania i charakterystyki wydajności przy użyciu **monitorowanie i zarządzanie aplikacjami**. Wybierz **Monitor & Zarządzaj** na stronie głównej fabryki danych. W widoku drzewa wybierz **wyjściowy zestaw danych**. W **okien działania** wybierz uruchamiania działania kopiowania. **Okien działania** Wyświetla czas trwania działania kopiowania i rozmiar danych, który jest skopiowany. Przepływność ma na liście **działania okna Eksploratora**. Aby dowiedzieć się więcej na temat aplikacji, zobacz [monitorowanie i zarządzanie nimi potoki fabryki danych Azure za pomocą aplikacji do zarządzania i monitorowania](data-factory-monitor-manage-app.md).

   ![Szczegóły uruchamiania działania](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   W dalszej części tego artykułu możesz porównać wydajności i konfiguracji scenariusz działania kopiowania [dotyczące wydajności](#performance-reference) z naszych testów.
2. **Diagnozowanie i zoptymalizować wydajność**. Jeśli wydajności, które należy obserwować nie spełnia Twoich oczekiwań, trzeba będzie zidentyfikować wąskich gardeł wydajności. Następnie optymalizacji wydajności, usunąć lub ograniczenia wpływu wąskich gardeł. Pełny opis wydajności diagnostyki wykracza poza zakres tego artykułu, ale poniżej przedstawiono niektóre typowe kwestie wymagające rozważenia:

   * Funkcje wydajności:
     * [Kopiuj równoległych](#parallel-copy)
     * [Jednostki przepływu danych w chmurze](#cloud-data-movement-units)
     * [Kopiuj przemieszczanego](#staged-copy)
     * [Skalowalność zarządzania bramy danych](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Brama zarządzania danymi](#considerations-for-data-management-gateway)
   * [Źródło](#considerations-for-the-source)
   * [obiekt sink](#considerations-for-the-sink)
   * [Serializacja i deserializacja](#considerations-for-serialization-and-deserialization)
   * [Kompresja](#considerations-for-compression)
   * [Mapowanie kolumny](#considerations-for-column-mapping)
   * [Inne zagadnienia](#other-considerations)
3. **Rozwiń węzeł Konfiguracja, aby cały zestaw danych**. Po zakończeniu wykonywania wyników i wydajności, możesz rozszerzyć definicji i okres aktywności potoku, aby pokrywał cały zestaw danych.

## <a name="considerations-for-data-management-gateway"></a>Zagadnienia dotyczące bramy zarządzania danymi
**Instalator bramy**: zalecane jest użycie dedykowanych maszyny do hosta bramy zarządzania danymi. Zobacz [zagadnienia dotyczące korzystania z bramy zarządzania danymi](data-factory-data-management-gateway.md#considerations-for-using-gateway).  

**Monitorowanie bramy i w górę/skalowalnych w poziomie**: pojedyncza brama logicznych z co najmniej jeden węzeł bramy może obsługiwać wielu uruchomień działania kopiowania w tym samym czasie jednocześnie. Można wyświetlić migawki czasie niemal rzeczywistym wykorzystania zasobów (CPU, pamięci, network(in/out), itp.) na komputerze bramy, a także liczbę współbieżnych zadań uruchomiona, a limit w portalu Azure, zobacz [Monitor bramy w portalu](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Jeśli masz duże konieczność na hybrydowego przenoszenia danych z dużą liczbą jednoczesnych kopii uruchomień działania lub z dużą ilością danych do skopiowania wziąć pod uwagę [skalowanie w górę i skalowania w poziomie bramy](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) tak, aby lepiej wykorzystywać zasobu lub zainicjować obsługi administracyjnej więcej zasobów dla kopiowania. 

## <a name="considerations-for-the-source"></a>Zagadnienia dotyczące źródła
### <a name="general"></a>Ogólne
Pamiętaj, że odpowiedni magazyn danych nie jest przeciążony przez innych obciążeń uruchomionych na nim.

Dla magazynów danych firmy Microsoft, zobacz [monitorowania i dostrajania tematy](#performance-reference) specyficznych dla magazynów danych i ułatwiające zrozumienie, jakie dane przechowywania charakterystyki wydajności, zminimalizować czas reakcji i zmaksymalizować przepustowość.

Po skopiowaniu danych z magazynu obiektów Blob do magazynu danych SQL, należy rozważyć użycie **PolyBase** zwiększania wydajności. Zobacz [Użyj programu PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) szczegółowe informacje. Aby uzyskać wskazówki z przypadkiem użycia, zobacz [załadować 1 TB do usługi Azure SQL Data Warehouse z fabryką danych Azure w obszarze 15 minut](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Magazyny danych opartych na plikach
*(W tym magazynu obiektów Blob, Data Lake Store, Amazon S3, systemów lokalnych plików i lokalnego systemu plików HDFS)*

* **Średni rozmiar pliku i liczba plików**: działanie kopiowania transfery danych jeden plik w czasie. Z tego samego ilość danych do przeniesienia ogólną przepustowość jest mniejszy, jeśli dane zawiera wiele małych plików zamiast kilka dużych plików z powodu fazy ładowania początkowego dla każdego pliku. W związku z tym jeśli to możliwe, połączenie małych plików do większych plików, aby uzyskać wyższą przepływność.
* **Plik formatu i kompresji**: na więcej sposobów poprawy wydajności, zobacz [uwagi do serializacji i deserializacji](#considerations-for-serialization-and-deserialization) i [zagadnienia dotyczące kompresji](#considerations-for-compression) sekcje.
* Dla **lokalnego systemu plików** scenariusz, w którym **brama zarządzania danymi** jest wymagane, zobacz [zagadnienia dotyczące bramy zarządzania danymi](#considerations-for-data-management-gateway) sekcji.

### <a name="relational-data-stores"></a>Magazyny danych relacyjnych
*(W tym bazy danych SQL. Magazyn danych SQL; Amazon Redshift; Bazy danych programu SQL Server; i Oracle, MySQL, DB2 Teradata, Sybase i PostgreSQL bazy danych itp.)*

* **Wzorzec danych**: schemat tabeli ma wpływ na przepustowość kopiowania. Rozmiar wiersza dużych zapewnia lepszą wydajność niż rozmiar wiersza mała, aby skopiować samą ilość danych. Przyczyną jest to, że bazy danych wydajniej można pobrać partie mniej danych, które zawierają mniej wierszy.
* **Zapytanie lub procedura składowana**: Optymalizacja logiki kwerendy lub procedury składowanej, określ w źródle działanie kopiowania na pobieranie danych wydajniej.
* Dla **lokalnych relacyjnych baz danych**, takie jak SQL Server i Oracle, które wymagają użycia **brama zarządzania danymi**, zobacz [zagadnienia dotyczące bramy zarządzania danymi](#considerations-on-data-management-gateway) sekcji.

## <a name="considerations-for-the-sink"></a>Zagadnienia dotyczące sink
### <a name="general"></a>Ogólne
Pamiętaj, że odpowiedni magazyn danych nie jest przeciążony przez innych obciążeń uruchomionych na nim.

Magazyny danych firmy Microsoft, można znaleźć w temacie [monitorowania i dostrajania tematy](#performance-reference) specyficznych dla magazynów danych. Te tematy mogą ułatwić zrozumienie charakterystyki wydajności magazynu danych i jak zminimalizować czas reakcji i zmaksymalizować przepustowość.

Jeśli kopiujesz dane z **magazynu obiektów Blob** do **SQL Data Warehouse**, należy rozważyć użycie **PolyBase** zwiększania wydajności. Zobacz [Użyj programu PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) szczegółowe informacje. Aby uzyskać wskazówki z przypadkiem użycia, zobacz [załadować 1 TB do usługi Azure SQL Data Warehouse z fabryką danych Azure w obszarze 15 minut](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Magazyny danych opartych na plikach
*(W tym magazynu obiektów Blob, Data Lake Store, Amazon S3, systemów lokalnych plików i lokalnego systemu plików HDFS)*

* **Skopiuj zachowanie**: po skopiowaniu danych z magazynu danych opartych na plikach różne działania kopiowania zawiera trzy pozycje za pośrednictwem **copyBehavior** właściwości. Ją zachowuje hierarchię, spłaszcza hierarchii lub scala plików. Zachowywanie albo zmniejszenie liczby poziomów hierarchii ma niewielkiego lub żadnego zmniejszenie wydajności, ale scalanie plików powoduje zmniejszenie wydajności zwiększyć.
* **Plik formatu i kompresji**: zobacz [uwagi do serializacji i deserializacji](#considerations-for-serialization-and-deserialization) i [zagadnienia dotyczące kompresji](#considerations-for-compression) sekcje na więcej sposobów poprawy wydajności.
* **Magazyn obiektów blob**: obecnie obsługuje magazynu obiektów Blob tylko blokowe obiekty BLOB do transferu danych zoptymalizowanych i przepływności.
* Dla **lokalnych systemów plików** scenariusze, które wymagają stosowania **brama zarządzania danymi**, zobacz [zagadnienia dotyczące bramy zarządzania danymi](#considerations-for-data-management-gateway) sekcji.

### <a name="relational-data-stores"></a>Magazyny danych relacyjnych
*(W tym bazy danych SQL, SQL Data Warehouse, baz danych programu SQL Server i Oracle — bazy danych)*

* **Skopiuj zachowanie**: w zależności od właściwości ustawionych dla **sqlSink**, działanie kopiowania zapisuje dane do docelowej bazy danych na różne sposoby.
  * Domyślnie używa usługi przepływu danych interfejsu API kopiowania zbiorczego wstawiania danych w Dołącz tryb, który zapewnia najlepszą wydajność.
  * Jeśli skonfigurujesz procedury składowanej w obiekt sink bazy danych ma zastosowanie jeden wiersz danych w czasie zamiast jako ładowania zbiorczego. Wydajność porzuca znacznie. Jeśli zestaw danych jest duży, jeśli ma to zastosowanie, rozważ przejście na **sqlWriterCleanupScript** właściwości.
  * Jeśli skonfigurujesz **sqlWriterCleanupScript** Uruchom właściwości dla każdego działania kopiowania, Usługa uruchamia skrypt, a następnie użyj interfejsu API kopiowania zbiorczego do wstawiania danych. Na przykład aby zastąpić całą tabelę przy użyciu najnowszych danych, można określić skrypt, aby usunąć wszystkie rekordy przed ładowania zbiorczego nowe dane ze źródła.
* **Rozmiar danych wzorca i partii**:
  * Schemat tabeli ma wpływ na przepustowość kopiowania. Aby skopiować samą ilość danych, rozmiar wiersza dużych zapewnia lepszą wydajność niż rozmiar wiersza małych ponieważ bazy danych można zatwierdzić wydajniej mniej partie danych.
  * Działanie kopiowania wstawia danych w serii partii. Należy określić liczbę wierszy w partii, przy użyciu **writeBatchSize** właściwości. Jeśli dane mają małe wiersze, można ustawić **writeBatchSize** o wyższej wartości do korzystania z mniejszy narzut partii i wyższej przepustowości. Jeśli rozmiar wiersza danych jest duży, należy zachować ostrożność, wraz ze zwiększeniem **writeBatchSize**. Wysokiej wartości może prowadzić do awarii kopiowania spowodowane przeciążeniem bazy danych.
* Dla **lokalnych relacyjnych baz danych** , takich jak SQL Server i Oracle, które wymagają użycia **brama zarządzania danymi**, zobacz [zagadnienia dotyczące bramy zarządzania danymi](#considerations-for-data-management-gateway)sekcji.

### <a name="nosql-stores"></a>Magazynów NoSQL
*(W tym magazynu tabel i bazy danych rozwiązania Cosmos Azure)*

* Aby uzyskać **tabeli magazynu**:
  * **Partycja**: zapisywanie danych z partycjami przeplotem znacznie powoduje spadek wydajności. Sortować dane źródłowe klucza partycji, dzięki czemu dane są wstawiane wydajnie w jednej partycji po drugim lub Dostosuj logiki można zapisać danych do jednej partycji.
* Aby uzyskać **rozwiązania Cosmos Azure DB**:
  * **Rozmiar partii**: **writeBatchSize** właściwość ustawia liczbę równoległych żądań do usługi Azure DB rozwiązania Cosmos tworzenie dokumentów. Lepszą wydajność można oczekiwać, wraz ze zwiększeniem **writeBatchSize** ponieważ więcej żądań równoległe są wysyłane do bazy danych Azure rozwiązania Cosmos. Jednak obserwować ograniczania przepustowości podczas zapisywania do rozwiązania Cosmos Azure DB (komunikat o błędzie jest "liczby żądań jest duża"). Różne czynniki może spowodować ograniczanie rozmiar dokumentu, w tym liczbę dokumentów oraz zasady indeksowania w kolekcji docelowej. Do osiągnięcia wyższej przepustowości kopiowania, należy rozważyć użycie lepsze kolekcji, na przykład S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Zagadnienia dotyczące serializacja i deserializacja
Serializacja i deserializacja może wystąpić, gdy Twoje wejściowy zestaw danych lub zestawu danych wyjściowych jest plikiem. Zobacz [obsługiwane formaty plików i kompresji](data-factory-supported-file-and-compression-formats.md) ze szczegółami obsługiwane formaty plików przez działanie kopiowania.

**Skopiuj zachowanie**:

* Kopiowanie plików między magazynami danych opartych na plikach:
  * Jeśli wejściowych i wyjściowych zestawów danych zarówno ma takie same lub żadnych ustawień formatu pliku, usługa przenoszenia danych wykonuje kopię binarne bez serializacji lub deserializacji. Zostanie wyświetlony wyższej przepustowości w porównaniu do scenariusza, w którym ustawienia formatu plik źródłowy i odbiorczy różnią się od siebie nawzajem.
  * Podczas wprowadzania i dane wyjściowe zestawy danych zarówno w formacie tekstowym i tylko kodowanie typ jest inny, usługa przenoszenia danych jest wyłącznie Konwersja kodowania. Nie wszystkie serializacji i deserializacji, co powoduje, że niektóre wydajności obciążenie w porównaniu do kopiowania binarnego.
  * Jeśli wejściowych i wyjściowych zestawów danych zarówno ma różnych formatach plików lub różnych konfiguracji, takich jak ogranicznik, usługa przenoszenia danych deserializuje źródła danych do strumienia, przekształcanie i serializować go w formacie wyjściowym wskazana. Ta operacja powoduje ważniejsze wydajności obciążenie w porównaniu do innych scenariuszy.
* Podczas kopiowania plików z magazynem danych, który nie jest opartych na plikach (na przykład z magazynu opartych na plikach do relacyjnego magazynu) krok serializacji lub deserializacji jest wymagany. Ten krok powoduje znaczne obciążenie.

**Format pliku**: format pliku, możesz wybrać może mieć wpływ na wydajność kopiowania. Na przykład Avro jest compact format binarny, który przechowuje metadane z danymi. Ma ona szeroki zakres obsługi w ekosystemie Hadoop do przetwarzania i zapytań. Jednak Avro jest droższe do serializacji i deserializacji, co prowadzi do dolnej przepływności kopiowania w porównaniu do formatu tekstowego. Wybierz ustawienia formatu pliku w procesie przetwarzania całościowo. Rozpoczynanie co tworzą dane są przechowywane w źródła magazyny danych lub w celu wyodrębnienia z systemów zewnętrznych; format najlepszy przechowywania, przetwarzania analitycznego i badania; i format, jakie dane powinny zostać wyeksportowane do składnic danych programów dla narzędzi do raportowania i wizualizacji. Czasami formacie, który jest nieoptymalne do odczytu i wydajność zapisu może być dobrym rozwiązaniem, gdy należy wziąć pod uwagę procesu ogólnego analitycznych.

## <a name="considerations-for-compression"></a>Zagadnienia dotyczące kompresji
Zestaw danych wejściowych lub wyjściowych jest plikiem, można ustawić działanie kopiowania do wykonania kompresja lub dekompresja zapisuje dane do lokalizacji docelowej. Po wybraniu kompresji wprowadzeniu zależności między operacjami wejścia/wyjścia (We/Wy) i procesora CPU. Kompresowanie danych koszty dodatkowe w zasoby obliczeniowe. Ale w zamian zmniejsza we/wy sieci i magazynu. W zależności od danych mogą pojawić się zwiększenie wydajności w ogólną przepustowość kopiowania.

**Koder-dekoder**: działanie kopiowania obsługuje typy kompresję Deflate, bzip2 i gzip. Usługa Azure HDInsight mogą używać wszystkich trzech typów przetwarzania. Każdy koder-dekoder kompresji ma zalety. Na przykład bzip2 ma najniższą przepływności kopiowania, ale uzyskać najlepszą wydajność zapytań Hive z bzip2, ponieważ podziel go do przetwarzania. Gzip jest najbardziej zrównoważonym opcji i jest najczęściej używany. Wybierz najlepiej pasujące do danego scenariusza end-to-end koder-dekoder.

**Poziom**: są dostępne dwie opcje dla każdego koder-dekoder kompresji: najszybciej skompresowane i optymalnie skompresowane. Najszybciej skompresowany opcji kompresuje dane tak szybko jak to możliwe, nawet jeśli nie jest optymalnie skompresowany plik wynikowy. Opcja optymalnie skompresowany zużywa więcej czasu na kompresji i zwraca minimalnej ilości danych. Można przetestować obie opcje, aby zobaczyć, która zapewnia lepszą wydajność ogólną w Twoim przypadku.

**Wchodzi w grę**: do kopiowania dużych ilości danych między chmurą a magazynu lokalnego, należy wziąć pod uwagę przy użyciu magazynu obiektów blob tymczasowe kompresji. Przy użyciu tymczasowego magazynu jest przydatne, gdy przepustowość sieci firmowej i usługami Azure jest czynnikiem ograniczającym i ma wejściowego zestawu danych i zestawu danych wyjściowych zarówno w skompresowanej. W szczególności można podzielić działania pojedynczej kopii kopii dwóch działań. Pierwsze działanie kopiowania kopiuje ze źródła do tymczasowego lub tymczasowej obiektu blob w postaci skompresowanej. Drugi działanie kopiowania kopiuje skompresowane dane z obszaru przemieszczania, a następnie dekompresuje podczas zapisuje obiekt sink.

## <a name="considerations-for-column-mapping"></a>Zagadnienia dotyczące mapowania kolumn
Można ustawić **columnMappings** właściwości w przypadku działania kopiowania mapy wszystkie lub podzbiór wejściowych kolumn na kolumny danych wyjściowych. Po usługi przenoszenia danych odczytuje dane ze źródła, musi wykonać mapowanie kolumn danych przed zapisuje dane do ujścia. To dodatkowe przetwarzanie ogranicza przepływność kopiowania.

W przypadku zapytań sklepu źródła danych, na przykład, czy jest relacyjnego magazynu, takich jak SQL Database lub SQL Server, czy jest magazynu NoSQL, takie jak magazyn tabel lub bazy danych rozwiązania Cosmos platformy Azure, należy wziąć pod uwagę wypychanie filtrowanie kolumn i zmianę kolejności logikę **kwerendy** właściwości zamiast mapowania kolumn. W ten sposób projekcji przeprowadzana usługi przenoszenia danych odczytuje dane z magazynu danych źródła, gdy są one bardziej wydajne.

## <a name="other-considerations"></a>Inne zagadnienia
Jeśli rozmiar danych, którą chcesz skopiować jest duży, można dostosować logiki biznesowej do partycji dalsze dane przy użyciu mechanizmu skalowania w fabryce danych. Następnie Zaplanuj działanie kopiowania do uruchamiania częściej, aby zredukować rozmiar danych dla każdego uruchamiania działania kopiowania.

Należy zachować ostrożność liczbę zestawów danych i działania kopiowania wymagające fabryki danych do łącznika do tego samego magazynu danych, w tym samym czasie. Wielu zadań jednoczesnych kopii może ograniczyć magazynu danych i prowadzić do pogorszenie wydajności, kopii zadania wewnętrzny ponownych prób, a w niektórych przypadkach, błędy wykonania.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Przykładowy scenariusz: kopiowanie z lokalnego programu SQL Server do magazynu obiektów Blob
**Scenariusz**: utworzeniu potoku można skopiować danych z lokalnego serwera SQL do magazynu obiektów Blob w formacie CSV. Aby przyspieszyć zadanie kopiowania, pliki CSV powinny być kompresowane do formatu bzip2.

**Badanie i analiza**: przepływność działanie kopiowania jest mniej niż 2 MB/s, który jest znacznie mniejsza niż testów porównawczych wydajności.

**Analiza wydajności i dostrajania**: Aby rozwiązać problem z wydajnością, Przyjrzyjmy się jak dane są przetwarzane i przenoszone.

1. **Odczytanie danych**: bramy otwiera połączenie z programem SQL Server i wysyła zapytanie. SQL Server odpowiada, wysyłając strumienia danych z bramą za pośrednictwem sieci intranet.
2. **Serializować i kompresji danych**: serializuje strumienia danych do formatu CSV, bramy i kompresuje dane do strumienia bzip2.
3. **Zapisu danych**: bramy przekazywania strumienia bzip2 do magazynu obiektów Blob za pośrednictwem Internetu.

Jak widać, dane są przetwarzane i przenoszone przesyłania strumieniowego sekwencyjnie: SQL Server > LAN > bramy > WAN > magazynu obiektów Blob. **Ogólną wydajność jest uzyskiwany za minimalnej przepustowości w potoku**.

![Przepływ danych](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Co najmniej jeden z następujących czynników może spowodować wąskie gardło:

* **Źródło**: sam serwer SQL ma niskiej przepustowości z powodu dużymi obciążeniami.
* **Brama zarządzania danymi**:
  * **LAN**: bramy znajduje się daleko od komputera programu SQL Server i ma połączenie o niskiej przepustowości.
  * **Brama**: bramy osiągnęła swoje ograniczenia obciążenia można wykonywać następujące operacje:
    * **Serializacja**: serializacja strumienia danych do formatu CSV ma powolne przepływności.
    * **Kompresja**: wybrano koder-dekoder kompresji powolne (na przykład, bzip2, czyli Core i7 2,8 MB/s).
  * **WAN**: brakuje przepustowości między siecią firmową i usługami Azure (na przykład T1 = 1,544 KB/s; T2 = 6,312 KB/s).
* **Obiekt sink**: magazyn obiektów Blob ma niskiej przepustowości. (W tym scenariuszu jest mało prawdopodobne, ponieważ jego umowy dotyczącej poziomu usług gwarantuje co najmniej 60 MB/s).

W takim przypadku bzip2 kompresji danych może być spowolnienie całego procesu. Przełączanie do koder-dekoder kompresji gzip może ułatwić to "wąskie gardło".

## <a name="sample-scenarios-use-parallel-copy"></a>Przykładowe scenariusze: Użyj równoległych kopii
**Scenariusz I:** 1000 1 MB pliki z lokalną systemu plików do magazynu obiektów Blob.

**Analiza i dostrajania wydajności**: na przykład, jeśli jest zainstalowana brama na komputerze czterordzeniowe fabryki danych używa 16 równoległych kopii do przenoszenia plików z systemu plików do magazynu obiektów Blob jednocześnie. To wykonywanie równoległe powinno spowodować wysokiej przepływności. Można również jawnie określić liczbę równoległych kopii. Podczas kopiowania wiele małych plików, równoległe kopie znacznie pomocy przepływność przy użyciu zasobów bardziej efektywnie.

![Scenariusz 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenariusz II**: Skopiuj 20 obiekty BLOB 500 MB z magazynu obiektów Blob do magazynu usługi Data Lake Analytics, a następnie dostrajania wydajności.

**Analiza i dostrajania wydajności**: W tym scenariuszu fabryki danych kopiuje dane z magazynu obiektów Blob do usługi Data Lake Store za pomocą jedną kopię (**parallelCopies** ustawioną wartość 1) i danych w chmurze pojedynczej jednostki przepływu. Przepływność widoczny będzie zbliżona opisana [wydajności odwołanie sekcji](#performance-reference).   

![Scenariusz 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scenariusz III**: rozmiar pliku jest większy niż dziesiątki MB i całkowitej ilości jest duży.

**Analiza i włączanie wydajności**: zwiększenie **parallelCopies** nie zapewnia lepszą wydajność kopiowania z powodu ograniczenia zasobów DMU jednym chmury. Zamiast tego należy określać więcej chmury DMUs, aby uzyskać więcej zasobów do wykonania przepływu danych. Nie określaj wartości dla **parallelCopies** właściwości. Fabryka danych obsługuje równoległości dla Ciebie. W tym przypadku jeśli ustawisz **cloudDataMovementUnits** przepływności około cztery razy występuje — 4,.

![Scenariusz 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Informacje ogólne
Poniżej przedstawiono monitorowania wydajności i dostrajania odwołań dla niektórych obsługiwanych magazynów:

* Magazyn Azure (w tym magazynie obiektów Blob i Magazyn tabel): [wartości docelowe skalowalności magazynu Azure](../../storage/common/storage-scalability-targets.md) i [Lista kontrolna wydajności i skalowalności magazynu Azure](../../storage/common/storage-performance-checklist.md)
* Azure SQL Database: Można [monitorowania wydajności](../../sql-database/sql-database-single-database-monitor.md) i sprawdź procent jednostki (bazy danych DTU) transakcji bazy danych
* Usługa Azure SQL Data Warehouse: Zdolność jest mierzony w jednostki magazynu danych (dwu); zobacz [Zarządzaj obliczeniowe zasilania w usłudze Azure SQL Data Warehouse (omówienie)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure DB rozwiązania Cosmos: [poziomy wydajności w usłudze Azure DB rozwiązania Cosmos](../../cosmos-db/performance-levels.md)
* Lokalny program SQL Server: [monitora i dostrajanie wydajności](https://msdn.microsoft.com/library/ms189081.aspx)
* Lokalny serwer plików: [dostrajania wydajności dla serwerów plików](https://msdn.microsoft.com/library/dn567661.aspx)
