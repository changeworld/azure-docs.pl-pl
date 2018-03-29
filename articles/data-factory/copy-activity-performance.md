---
title: Skopiuj wydajności działania i dostrajania przewodnik w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat kluczowych czynników wpływających na wydajność przepływu danych w fabryce danych Azure, korzystając z działania kopiowania.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: jingwang
ms.openlocfilehash: c43973a7e5070676fc0f32a4c8923d57a479f884
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Skopiuj wydajności działania i dostrajania przewodnik
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-copy-activity-performance.md)
> * [Wersja 2 — wersja zapoznawcza](copy-activity-performance.md)


Działanie kopiowania fabryki danych Azure oferuje najwyższej jakości danych bezpieczne, niezawodne i wysoko wydajnych ładowania rozwiązania. Go umożliwia utworzenie kopii dziesiątki terabajtów danych codziennie przez szeroki zakres chmury i lokalnych magazynów danych. Ładowanie wydajności danych ogromną fast ma kluczowe znaczenie dla zapewnienia można skoncentrować się na temat problemu "danych big data" core: kompilowanie rozwiązań zaawansowane analizy i uzyskiwanie szczegółowych informacji z wszystkie te dane.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [wydajności działania kopiowania w fabryce danych w wersji 1](v1/data-factory-copy-activity-performance.md).

Platforma Azure oferuje zestaw korporacyjnej rozwiązania dotyczące danych magazynu i danych magazynu i działanie kopiowania oferuje zoptymalizowanego ładowania środowisko, która jest łatwa do skonfigurowania i danych. Z po prostu działanie pojedynczej kopii można uzyskać:

* Ładowanie danych do **magazyn danych SQL Azure** w **1,2 GB/s**.
* Ładowanie danych do **magazynu obiektów Blob Azure** na **1.0 GB/s**
* Ładowanie danych do **Azure Data Lake Store** na **1.0 GB/s**

W tym artykule opisano:

* [Numery wydajności](#performance-reference) obsługiwane źródłowy i odbiorczy magazyny danych do planowania projektu.
* Funkcje, które może zwiększyć przepływność kopiowania w różnych scenariuszy, w tym [jednostki przepływu danych w chmurze](#cloud-data-movement-units), [równoległych kopii](#parallel-copy), i [przemieszczane kopiowania](#staged-copy);
* [Wskazówki dotyczące dostrajania wydajności](#performance-tuning-steps) w sposób dostrajania wydajności i kluczowych czynników, które mogą mieć wpływ na wydajność kopiowania.

> [!NOTE]
> Jeśli nie masz doświadczenia w obsłudze działania kopiowania ogólnie rzecz biorąc, zobacz [omówienie działania kopiowania](copy-activity-overview.md) przed przeczytaniem tego artykułu.
>

## <a name="performance-reference"></a>Informacje dotyczące wydajności

Jako odwołanie, w poniższej tabeli pokazuje liczbę przepływności kopiowania **w MB/s** dla danego par źródłowy i odbiorczy **w działaniu pojedynczej kopii Uruchom** testów wewnętrznych. Porównanie można go również pokazano, jak różne ustawienia [jednostki przepływu danych w chmurze](#cloud-data-movement-units) lub [skalowalność środowiska uruchomieniowego integracji Self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime) (wiele węzłów) pozwalają na wydajność kopiowania.

![Macierz wydajności](./media/copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>W fabryce danych Azure w wersji 2 gdy działanie kopiowania jest wykonywana w środowisku uruchomieniowym integracji Azure jednostki przepływu danych minimalny dozwolony chmury wynosi dwa. Jeśli nie zostanie określony, zobacz jednostki przepływu danych domyślne używane w [jednostki przepływu danych w chmurze](#cloud-data-movement-units).

Informacje, które należy zwrócić uwagę:

* Przepływność jest obliczana przy użyciu następującej formuły: [read rozmiar danych ze źródła] / [czas trwania działania kopiowania].
* Numery wydajności w tabeli są mierzone przy użyciu [TPC-H](http://www.tpc.org/tpch/) zestawu danych w działaniu pojedynczej kopii Uruchom.
* W magazynie danych Azure źródło i ujście są tego samego regionu platformy Azure.
* Dla kopii hybrydowej między lokalnymi i w chmurze magazyny danych każdy węzeł środowiska uruchomieniowego integracji Self-hosted była uruchomiona na komputerze, który został oddzielony od magazynu danych z poniżej specyfikacji. Operacja kopiowania pojedyncze działanie było uruchomione, używane tylko niewielką część procesora CPU, pamięć lub przepustowość sieci testowej maszyny.
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
> Wyższej przepustowości można osiągnąć za pomocą więcej danych przemieszczania jednostki (DMUs) niż domyślne dozwolona maksymalna DMUs, które są 32 dla działania kopiowania w chmurze na chmurze, uruchom. Na przykład z 100 DMUs, możesz osiągnąć kopiowanie danych z obiektu Blob Azure do usługi Azure Data Lake Store **1.0GBps**. Zobacz [jednostki przepływu danych w chmurze](#cloud-data-movement-units) sekcji, aby uzyskać więcej informacji dotyczących tej funkcji i obsługiwany scenariusz. Skontaktuj się z [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/) żądania DMUs więcej.

## <a name="cloud-data-movement-units"></a>Jednostki przepływu danych w chmurze

A **jednostki przepływu danych w chmurze (DMU)** miary, która odzwierciedla wydajność (kombinacja Procesora, pamięci i alokacji zasobów w sieci) w pojedynczą jednostkę w fabryce danych. **DMU ma zastosowanie tylko do [środowiska uruchomieniowego integracji Azure](concepts-integration-runtime.md#azure-integration-runtime)**, ale nie [środowiska uruchomieniowego integracji Self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime).

**Jednostki przepływu danych minimalnego chmury dla uruchamiania działania kopiowania wynosi dwa.** Jeśli nie zostanie określony, w poniższej tabeli wymieniono DMUs domyślne używane w scenariuszach różnych kopiowania:

| Skopiuj scenariusza | Domyślne DMUs określone przez usługę |
|:--- |:--- |
| Kopiowanie danych między magazynów opartych na plikach | Od 4 do 32 w zależności od liczby i rozmiaru plików. |
| Inne scenariusze kopiowania | 4 |

Aby zastąpić to ustawienie domyślne, należy określić wartość dla **cloudDataMovementUnits** właściwości w następujący sposób. **Dozwolone wartości** dla **cloudDataMovementUnits** właściwość jest **256**. **Rzeczywistą liczbę chmury DMUs** używany w czasie wykonywania operacji kopiowania jest równa lub mniejsza niż skonfigurowana wartość, w zależności od tego wzorca sieci danych. Uzyskać informacje na temat poziomu są bardziej wydajne, można uzyskać po skonfigurowaniu więcej jednostek dla konkretnej kopii źródłowy i odbiorczy, zobacz [dotyczące wydajności](#performance-reference).

Widać jednostki przepływu danych chmury faktycznie używana dla poszczególnych kopii uruchamiania w przypadku działania kopiowania danych wyjściowych podczas uruchamiania działania monitorowania. Dowiedz się więcej szczegółów z [skopiuj Monitorowanie działania](copy-activity-overview.md#monitoring).

> [!NOTE]
> Jeśli potrzebujesz więcej chmury DMUs umożliwiających uzyskanie większej produktywności, skontaktuj się z [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/). Ustawienie 8 i nowszych jest obecnie obsługiwane tylko wtedy, gdy użytkownik **skopiować wielu plików z obiektu Blob magazynu/Data Lake Store/Amazon S3/w chmurze SFTP FTP/w chmurze do innych magazynów danych chmury**.
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
            "cloudDataMovementUnits": 32
        }
    }
]
```

### <a name="cloud-data-movement-units-billing-impact"></a>Jednostki przepływu danych chmury rozliczeń wpływu

Ma ona **ważne** pamiętać, że są naliczane na podstawie całkowitej czasu operacji kopiowania. Całkowity czas, które są rozliczane dla przepływu danych jest łączny czas trwania między DMUs. Zadanie kopiowania używana do zbierania godzinę z dwóch jednostek chmury, teraz trwa 15 minut z jednostkami chmury osiem zestawienie ogólnej pozostaje prawie takie same.

## <a name="parallel-copy"></a>Kopiuj równoległych

Można użyć **parallelCopies** Właściwość wskazująca równoległości interesujące Kopiuj działania do użycia. Tej właściwości można traktować jako maksymalną liczbę wątków w ramach działania kopiowania, który można ze źródła do odczytu lub zapisu z magazynów danych zbiornika równolegle.

Dla każdego działania kopiowania Uruchom fabryki danych określa liczbę równoległych kopii umożliwia kopiowanie danych z źródła danych, przechowywania i dane miejsce docelowe przechowywania. Domyślna liczba równoległych kopii, których używa zależy od typu źródłowy i odbiorczy, którego używasz:

| Skopiuj scenariusza | Liczba równoległych kopii domyślne określone przez usługę |
| --- | --- |
| Kopiowanie danych między magazynów opartych na plikach |Zależy od rozmiaru plików i liczby jednostek chmury danych przepływu (DMUs) umożliwia kopiowanie danych między dwa magazyny danych w chmurze lub fizyczną konfigurację środowiska uruchomieniowego integracji Self-hosted maszyny. |
| Kopiowanie danych z dowolnego źródła danych magazynu do magazynu tabel Azure |4 |
| Inne scenariusze kopiowania |1 |

[!TIP]
> Podczas kopiowania danych między magazynów opartych na plikach, domyślne zachowanie (automatycznie określoną) zwykle zapewniają najlepszą wydajność. 

Aby kontrolować obciążenia na maszynach hostujących dane są przechowywane lub do dopasowywania wydajności kopii, możesz zastąpić wartość domyślną i określ wartość **parallelCopies** właściwości. Wartość musi być liczbą całkowitą większą niż lub równa 1. W czasie wykonywania Aby uzyskać najlepszą wydajność, działanie kopiowania używa wartość, która jest mniejsza lub równa wartości zostanie ustawiona.

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

* Po skopiowaniu danych między magazynów opartych na plikach, **parallelCopies** określić równoległości na poziomie plików. Podziału w jednym pliku sytuacja może mieć miejsce pod automatycznie i w sposób niewidoczny dla użytkownika, a zostało zaprojektowane na potrzeby ładowania danych w równoległych i prostopadłym do parallelCopies najlepsze rozmiar fragmentu odpowiedniego dla typu danego źródła danych magazynu. Rzeczywista liczba równoległych kopii używa usługi przenoszenia danych w czasie wykonywania operacji kopiowania jest nie więcej niż liczba plików. Jeśli jest to zachowanie kopii **mergeFile**, aktywność kopiowania nie może korzystać z równoległości poziomie plików.
* Po określeniu wartość **parallelCopies** właściwości, warto rozważyć zwiększenie obciążenia na magazyny danych źródłowy i odbiorczy i środowiska uruchomieniowego integracji Self-Hosted, gdy działanie kopiowania jest upoważniony przez nią z na przykład hybrydowego kopii. Ma to miejsce, zwłaszcza jeśli masz wiele działań lub uruchamia równoczesnych działań uruchamianych z tego samego magazynu danych. Jeśli zauważysz jest przeciążony magazyn danych lub środowisko uruchomieniowe integracji Self-hosted obciążenia, Zmniejsz **parallelCopies** wartość na zmniejszenie obciążenia.
* Po skopiowaniu danych z magazynów, które nie są opartą na plikach do magazynów, które są oparte na pliku usługi przenoszenia danych ignoruje **parallelCopies** właściwości. Nawet wtedy, gdy określono równoległości, nie zostanie zastosowane w tym przypadku.
* **parallelCopies** prostopadłym do **cloudDataMovementUnits**. Pierwsza jest traktowane przez wszystkie chmury danych przemieszczania jednostki.

## <a name="staged-copy"></a>Kopiuj przemieszczanego

Po skopiowaniu danych z magazynu danych źródła do ujścia magazynu danych można używać magazynu obiektów Blob jako magazyn tymczasowy tymczasowej. Przemieszczania jest szczególnie przydatne w następujących przypadkach:

- **Aby pozyskiwania danych z różnych baz danych do usługi SQL Data Warehouse przy użyciu programu PolyBase**. Usługa SQL Data Warehouse używa PolyBase jako mechanizm wysokiej przepustowości ładowanie dużej ilości danych do usługi SQL Data Warehouse. Źródło danych musi być w magazynie obiektów Blob lub usługi Azure Data Lake Store i musi spełniać kryteria. Podczas ładowania danych z magazynu danych innego niż magazynu obiektów Blob lub usługi Azure Data Lake Store, można uaktywnić dane kopiowanie za pośrednictwem tymczasowego przemieszczania magazynu obiektów Blob. W takim przypadku fabryki danych wykonuje przekształcenia danych wymagane, aby upewnić się, że spełnia wymagania programu PolyBase. Następnie używa PolyBase wydajnie ładowanie danych do usługi SQL Data Warehouse. Aby uzyskać więcej informacji, zobacz [Użyj programu PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Czasami zajmuje trochę czasu, aby wykonać przenoszenia danych hybrydowych (oznacza to, aby skopiować z lokalnego magazynu danych w magazynie danych w chmurze) przez powolne połączenie sieciowe**. Aby zwiększyć wydajność, przygotowanego kopiowania służy do skompresowania danych w sieci lokalnej, dzięki czemu zajmuje mniej czasu, aby przenieść dane do przemieszczania danych przechowywanych w chmurze, a następnie dekompresji danych w magazynie przemieszczania przed załadowaniem w magazynie danych docelowego.
- **Nie chcesz otworzyć porty inne niż port 80 i portu 443 w zaporze, z powodu zasad firmowych IT**. Na przykład podczas kopiowania danych z lokalnego magazynu danych do zbiornika bazy danych SQL Azure lub ujścia magazynu danych SQL Azure, musisz aktywować komunikacji wychodzącej TCP na porcie 1433 dla zapory systemu Windows i firmowej zapory. W tym scenariuszu przemieszczanego kopii można wykorzystać środowiska uruchomieniowego integracji Self-hosted najpierw skopiować dane do magazynu obiektów Blob za pośrednictwem protokołu HTTP lub HTTPS na porcie 443 na potrzeby przemieszczania wystąpienia, a następnie załadować dane do bazy danych SQL lub SQL Data Warehouse z obszaru przemieszczania magazynu obiektów Blob. W tym przepływie nie trzeba włączyć port 1433.

### <a name="how-staged-copy-works"></a>Jak przemieszczanego działania kopiowania

Po aktywowaniu funkcji przemieszczania najpierw dane są kopiowane z magazynu danych źródła do tymczasowej magazynu obiektów Blob (użycie własnego). Następnie dane są kopiowane z magazynu danych tymczasowych do ujścia magazynu danych. Fabryka danych automatycznie zarządza przepływu dwuetapowej dla Ciebie. Fabryka danych czyści dane z magazynu przemieszczania także po zakończeniu przenoszenia danych.

![Kopiuj przemieszczanego](media/copy-activity-performance/staged-copy.png)

Po uaktywnieniu przepływu danych przy użyciu tymczasowego magazynu można określić, czy mają dane skompresowane przed przeniesieniem danych z magazynu danych źródła z magazynem danych tymczasowych lub przemieszczania, a następnie dekompresowane przed przenoszenia danych z przejściowej lub przemieszczania danych Przechowaj ujścia magazynu danych.

Obecnie nie można skopiować danych między dwa lokalnych magazynów danych przy użyciu magazynu tymczasowego.

### <a name="configuration"></a>Konfigurowanie

Skonfiguruj **enableStaging** ustawienie w przypadku działania kopiowania, aby określić, czy dane umieszczane w magazynie obiektów Blob, przed załadowaniem do magazynu danych docelowym. Podczas ustawiania **enableStaging** do `TRUE`, określ dodatkowe właściwości, które są wymienione w następnej tabeli. Jeśli nie masz, należy także utworzyć magazynu Azure lub magazynu udostępnionego usługi połączone podpisu dostępu dla przemieszczania.

| Właściwość | Opis | Wartość domyślna | Wymagane |
| --- | --- | --- | --- |
| **enableStaging** |Określ, czy chcesz skopiować dane za pośrednictwem przejściowej przemieszczania magazynu. |False |Nie |
| **linkedServiceName** |Określ nazwę [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) połączonej usługi, która odwołuje się do wystąpienia magazynu, który jest używany jako magazyn tymczasowy tymczasowej. <br/><br/> Aby załadować dane do usługi SQL Data Warehouse przy użyciu programu PolyBase nie można używać magazynu sygnatury dostępu współdzielonego. Można go użyć w innych scenariuszach. |ND |Tak, gdy **enableStaging** ma wartość TRUE |
| **Ścieżka** |Określ ścieżki do magazynu obiektów Blob, który ma zawierać przemieszczonych danych. Jeśli ścieżka nie zostanie określona, usługa tworzy kontener do przechowywania danych tymczasowych. <br/><br/> Określ ścieżkę tylko w przypadku używania magazynu z sygnatury dostępu współdzielonego lub wymagają dane tymczasowe w określonej lokalizacji. |ND |Nie |
| **enableCompression** |Określa, czy dane powinny skompresowany, zanim zostanie on skopiowany do miejsca docelowego. To ustawienie pozwala ograniczyć ilość danych transferowanych. |False |Nie |

Oto przykład definicji działanie kopiowania z właściwościami, które zostały opisane w powyższej tabeli:

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

### <a name="staged-copy-billing-impact"></a>Przemieszczane kopiowania rozliczeń wpływu

Są naliczane na podstawie dwa kroki: kopiowanie czas trwania i skopiować typu.

* Gdy używasz przemieszczania podczas kopiowania chmury (kopiowanie danych z magazynu danych chmury do innego magazynu danych chmury, oba etapy upoważnionych przez środowisko uruchomieniowe integracji Azure), naliczane są opłaty [łączny czas trwania kopiowania kroki 1 i 2] x [cenie jednostkowej kopiowania chmury].
* Gdy używasz przemieszczania podczas kopiowania hybrydowych (kopiowanie danych z lokalnego magazynu danych w magazynie danych chmury jednego etapu upoważnionych przez środowisko uruchomieniowe integracji Self-hosted), naliczane są opłaty [hybrydowego kopiowania czas] x [cenie jednostkowej kopiowania hybrydowego] + [czas trwania kopiowania w chmurze] x [chmury Kopiuj cenie jednostkowej].

## <a name="performance-tuning-steps"></a>Kroki dostrajania wydajności

Zaleca się, że należy wykonać następujące kroki w celu dostrojenia wydajności usługi fabryka danych z działania kopiowania:

1. **Ustalanie linii bazowej**. W fazie projektowania przetestować potoku sieci za pomocą działania kopiowania przed przykładowych danych reprezentatywnych. Zbieranie szczegółów wykonywania i charakterystyk wydajności [skopiuj Monitorowanie działania](copy-activity-overview.md#monitoring).

2. **Diagnozowanie i zoptymalizować wydajność**. Jeśli wydajności, które należy obserwować nie spełnia Twoich oczekiwań, trzeba będzie zidentyfikować wąskich gardeł wydajności. Następnie optymalizacji wydajności, usunąć lub ograniczenia wpływu wąskich gardeł. Pełny opis wydajności diagnostyki wykracza poza zakres tego artykułu, ale poniżej przedstawiono niektóre typowe kwestie wymagające rozważenia:

   * Funkcje wydajności:
     * [Kopiuj równoległych](#parallel-copy)
     * [Jednostki przepływu danych w chmurze](#cloud-data-movement-units)
     * [Kopiuj przemieszczanego](#staged-copy)
     * [Hostowanie Samoobsługowe skalowalność integrację środowiska uruchomieniowego](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Hostowanie Samoobsługowe integrację środowiska uruchomieniowego](#considerations-for-self-hosted-integration-runtime)
   * [Źródło](#considerations-for-the-source)
   * [obiekt sink](#considerations-for-the-sink)
   * [Serializacja i deserializacja](#considerations-for-serialization-and-deserialization)
   * [Kompresja](#considerations-for-compression)
   * [Mapowanie kolumny](#considerations-for-column-mapping)
   * [Inne zagadnienia](#other-considerations)

3. **Rozwiń węzeł Konfiguracja, aby cały zestaw danych**. Po zakończeniu wykonywania wyników i wydajności, możesz rozszerzyć definicji i potoku, aby pokrywał cały zestaw danych.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Zagadnienia dotyczące środowiska uruchomieniowego integracji hostowanie Samoobsługowe

Jeśli Twoje działania kopiowania jest wykonywana w środowisku uruchomieniowym integracji Self-hosted, należy uwzględnić następujące informacje:

**Instalator**: zalecane jest użycie dedykowanych maszyny do środowiska uruchomieniowego integracji hosta. Zobacz [zagadnienia dotyczące przy użyciu środowiska uruchomieniowego integracji Self-hosted](concepts-integration-runtime.md).

**Skalowanie w poziomie**: jednej logicznej Self-hosted integracji środowiska uruchomieniowego z co najmniej jeden węzeł może obsługiwać wielu uruchomień działania kopiowania w tym samym czasie jednocześnie. Jeśli masz duże konieczność na hybrydowego przenoszenia danych z dużą liczbą jednoczesnych kopii uruchomień działania lub z dużą ilością danych do skopiowania wziąć pod uwagę [skalowania środowiska uruchomieniowego integracji Self-hosted](create-self-hosted-integration-runtime.md#high-availability-and-scalability) tak, aby udostępnić więcej zasobów do zwiększenie możliwości dostępnych dla kopiowania.

## <a name="considerations-for-the-source"></a>Zagadnienia dotyczące źródła

### <a name="general"></a>Ogólne

Pamiętaj, że odpowiedni magazyn danych nie jest przeciążony przez innych obciążeń uruchomionych na nim.

Dla magazynów danych firmy Microsoft, zobacz [monitorowania i dostrajania tematy](#performance-reference) specyficznych dla magazynów danych i ułatwiające zrozumienie, jakie dane przechowywania charakterystyki wydajności, zminimalizować czas reakcji i zmaksymalizować przepustowość.

* Po skopiowaniu danych **z magazynu obiektów Blob SQL Data Warehouse**, rozważ użycie **PolyBase** zwiększania wydajności. Zobacz [Użyj programu PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) szczegółowe informacje.
* Po skopiowaniu danych **z systemu plików HDFS Azure obiekt Blob/usługi Azure Data Lake store**, rozważ użycie **narzędzia DistCp** zwiększania wydajności. Zobacz [Użyj narzędzia DistCp, aby skopiować dane z systemu plików HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) szczegółowe informacje.
* Po skopiowaniu danych **z Redshift do usługi Azure SQL danych magazynu/Azure obiekt BLob/usługi Azure Data Lake Store**, rozważ użycie **zwolnienie** zwiększania wydajności. Zobacz [zwolnienie Użyj można skopiować danych z usługi Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) szczegółowe informacje.

### <a name="file-based-data-stores"></a>Magazyny danych opartych na plikach

* **Średni rozmiar pliku i liczba plików**: działanie kopiowania transfery danych jeden plik w czasie. Z tego samego ilość danych do przeniesienia ogólną przepustowość jest mniejszy, jeśli dane zawiera wiele małych plików zamiast kilka dużych plików z powodu fazy ładowania początkowego dla każdego pliku. W związku z tym jeśli to możliwe, połączenie małych plików do większych plików, aby uzyskać wyższą przepływność.
* **Plik formatu i kompresji**: na więcej sposobów poprawy wydajności, zobacz [uwagi do serializacji i deserializacji](#considerations-for-serialization-and-deserialization) i [zagadnienia dotyczące kompresji](#considerations-for-compression) sekcje.

### <a name="relational-data-stores"></a>Magazyny danych relacyjnych

* **Wzorzec danych**: schemat tabeli ma wpływ na przepustowość kopiowania. Rozmiar wiersza dużych zapewnia lepszą wydajność niż rozmiar wiersza mała, aby skopiować samą ilość danych. Przyczyną jest to, że bazy danych wydajniej można pobrać partie mniej danych, które zawierają mniej wierszy.
* **Zapytanie lub procedura składowana**: Optymalizacja logiki kwerendy lub procedury składowanej, określ w źródle działanie kopiowania na pobieranie danych wydajniej.

## <a name="considerations-for-the-sink"></a>Zagadnienia dotyczące sink

### <a name="general"></a>Ogólne

Pamiętaj, że odpowiedni magazyn danych nie jest przeciążony przez innych obciążeń uruchomionych na nim.

Magazyny danych firmy Microsoft, można znaleźć w temacie [monitorowania i dostrajania tematy](#performance-reference) specyficznych dla magazynów danych. Te tematy mogą ułatwić zrozumienie charakterystyki wydajności magazynu danych i jak zminimalizować czas reakcji i zmaksymalizować przepustowość.

* Po skopiowaniu danych **z magazynu obiektów Blob SQL Data Warehouse**, rozważ użycie **PolyBase** zwiększania wydajności. Zobacz [Użyj programu PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) szczegółowe informacje.
* Po skopiowaniu danych **z systemu plików HDFS Azure obiekt Blob/usługi Azure Data Lake store**, rozważ użycie **narzędzia DistCp** zwiększania wydajności. Zobacz [Użyj narzędzia DistCp, aby skopiować dane z systemu plików HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) szczegółowe informacje.
* Po skopiowaniu danych **z Redshift do usługi Azure SQL danych magazynu/Azure obiekt BLob/usługi Azure Data Lake Store**, rozważ użycie **zwolnienie** zwiększania wydajności. Zobacz [zwolnienie Użyj można skopiować danych z usługi Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) szczegółowe informacje.

### <a name="file-based-data-stores"></a>Magazyny danych opartych na plikach

* **Skopiuj zachowanie**: po skopiowaniu danych z magazynu danych opartych na plikach różne działania kopiowania zawiera trzy pozycje za pośrednictwem **copyBehavior** właściwości. Ją zachowuje hierarchię, spłaszcza hierarchii lub scala plików. Zachowywanie albo zmniejszenie liczby poziomów hierarchii ma niewielkiego lub żadnego zmniejszenie wydajności, ale scalanie plików powoduje zmniejszenie wydajności zwiększyć.
* **Plik formatu i kompresji**: zobacz [uwagi do serializacji i deserializacji](#considerations-for-serialization-and-deserialization) i [zagadnienia dotyczące kompresji](#considerations-for-compression) sekcje na więcej sposobów poprawy wydajności.

### <a name="relational-data-stores"></a>Magazyny danych relacyjnych

* **Skopiuj zachowanie**: w zależności od właściwości ustawionych dla **sqlSink**, działanie kopiowania zapisuje dane do docelowej bazy danych na różne sposoby.
  * Domyślnie używa usługi przepływu danych interfejsu API kopiowania zbiorczego wstawiania danych w Dołącz tryb, który zapewnia najlepszą wydajność.
  * Jeśli skonfigurujesz procedury składowanej w obiekt sink bazy danych ma zastosowanie jeden wiersz danych w czasie zamiast jako ładowania zbiorczego. Wydajność porzuca znacznie. Jeśli zestaw danych jest duży, jeśli ma to zastosowanie, rozważ przejście na **preCopyScript** właściwości.
  * Jeśli skonfigurujesz **preCopyScript** Uruchom właściwości dla każdego działania kopiowania, Usługa uruchamia skrypt, a następnie użyj interfejsu API kopiowania zbiorczego do wstawiania danych. Na przykład aby zastąpić całą tabelę przy użyciu najnowszych danych, można określić skrypt, aby usunąć wszystkie rekordy przed ładowania zbiorczego nowe dane ze źródła.
* **Rozmiar danych wzorca i partii**:
  * Schemat tabeli ma wpływ na przepustowość kopiowania. Aby skopiować samą ilość danych, rozmiar wiersza dużych zapewnia lepszą wydajność niż rozmiar wiersza małych ponieważ bazy danych można zatwierdzić wydajniej mniej partie danych.
  * Działanie kopiowania wstawia danych w serii partii. Należy określić liczbę wierszy w partii, przy użyciu **writeBatchSize** właściwości. Jeśli dane mają małe wiersze, można ustawić **writeBatchSize** o wyższej wartości do korzystania z mniejszy narzut partii i wyższej przepustowości. Jeśli rozmiar wiersza danych jest duży, należy zachować ostrożność, wraz ze zwiększeniem **writeBatchSize**. Wysokiej wartości może prowadzić do awarii kopiowania spowodowane przeciążeniem bazy danych.

### <a name="nosql-stores"></a>Magazynów NoSQL

* Aby uzyskać **tabeli magazynu**:
  * **Partycja**: zapisywanie danych z partycjami przeplotem znacznie powoduje spadek wydajności. Sortować dane źródłowe klucza partycji, dzięki czemu dane są wstawiane wydajnie w jednej partycji po drugim lub Dostosuj logiki można zapisać danych do jednej partycji.

## <a name="considerations-for-serialization-and-deserialization"></a>Zagadnienia dotyczące serializacja i deserializacja

Serializacja i deserializacja może wystąpić, gdy Twoje wejściowy zestaw danych lub zestawu danych wyjściowych jest plikiem. Zobacz [obsługiwane formaty plików i kompresji](supported-file-formats-and-compression-codecs.md) ze szczegółami obsługiwane formaty plików przez działanie kopiowania.

**Skopiuj zachowanie**:

* Kopiowanie plików między magazynami danych opartych na plikach:
  * Jeśli wejściowych i wyjściowych zestawów danych zarówno ma takie same lub żadnych ustawień formatu pliku, usługa przenoszenia danych wykonuje **binarne kopiowania** bez serializacji lub deserializacji. Zostanie wyświetlony wyższej przepustowości w porównaniu do scenariusza, w którym ustawienia formatu plik źródłowy i odbiorczy różnią się od siebie nawzajem.
  * Podczas wprowadzania i dane wyjściowe zestawy danych zarówno w formacie tekstowym i tylko kodowanie typ jest inny, usługa przenoszenia danych jest wyłącznie Konwersja kodowania. Nie wszystkie serializacji i deserializacji, co powoduje, że niektóre wydajności obciążenie w porównaniu do kopiowania binarnego.
  * Jeśli wejściowych i wyjściowych zestawów danych zarówno ma różnych formatach plików lub różnych konfiguracji, takich jak ogranicznik, usługa przenoszenia danych deserializuje źródła danych do strumienia, przekształcanie i serializować go w formacie wyjściowym wskazana. Ta operacja powoduje ważniejsze wydajności obciążenie w porównaniu do innych scenariuszy.
* Podczas kopiowania plików z magazynem danych, który nie jest opartych na plikach (na przykład z magazynu opartych na plikach do relacyjnego magazynu) krok serializacji lub deserializacji jest wymagany. Ten krok powoduje znaczne obciążenie.

**Format pliku**: format pliku, możesz wybrać może mieć wpływ na wydajność kopiowania. Na przykład Avro jest compact format binarny, który przechowuje metadane z danymi. Ma ona szeroki zakres obsługi w ekosystemie Hadoop do przetwarzania i zapytań. Jednak Avro jest droższe do serializacji i deserializacji, co prowadzi do dolnej przepływności kopiowania w porównaniu do formatu tekstowego. Wybierz ustawienia formatu pliku w procesie przetwarzania całościowo. Rozpoczynanie co tworzą dane są przechowywane w źródła magazyny danych lub w celu wyodrębnienia z systemów zewnętrznych; format najlepszy przechowywania, przetwarzania analitycznego i badania; i format, jakie dane powinny zostać wyeksportowane do składnic danych programów dla narzędzi do raportowania i wizualizacji. Czasami formacie, który jest nieoptymalne do odczytu i wydajność zapisu może być dobrym rozwiązaniem, gdy należy wziąć pod uwagę procesu ogólnego analitycznych.

## <a name="considerations-for-compression"></a>Zagadnienia dotyczące kompresji

Zestaw danych wejściowych lub wyjściowych jest plikiem, można ustawić działanie kopiowania do wykonania kompresja lub dekompresja zapisuje dane do lokalizacji docelowej. Po wybraniu kompresji wprowadzeniu zależności między operacjami wejścia/wyjścia (We/Wy) i procesora CPU. Kompresowanie danych koszty dodatkowe w zasoby obliczeniowe. Ale w zamian zmniejsza we/wy sieci i magazynu. W zależności od danych mogą pojawić się zwiększenie wydajności w ogólną przepustowość kopiowania.

**Koder-dekoder**: każdego koder-dekoder kompresji ma zalety. Na przykład bzip2 ma najniższą przepływności kopiowania, ale uzyskać najlepszą wydajność zapytań Hive z bzip2, ponieważ podziel go do przetwarzania. Gzip jest najbardziej zrównoważonym opcji i jest najczęściej używany. Wybierz najlepiej pasujące do danego scenariusza end-to-end koder-dekoder.

**Poziom**: są dostępne dwie opcje dla każdego koder-dekoder kompresji: najszybciej skompresowane i optymalnie skompresowane. Najszybciej skompresowany opcji kompresuje dane tak szybko jak to możliwe, nawet jeśli nie jest optymalnie skompresowany plik wynikowy. Opcja optymalnie skompresowany zużywa więcej czasu na kompresji i zwraca minimalnej ilości danych. Można przetestować obie opcje, aby zobaczyć, która zapewnia lepszą wydajność ogólną w Twoim przypadku.

**Wchodzi w grę**: do kopiowania dużych ilości danych między chmurą a magazynu lokalnego, należy rozważyć użycie [przemieszczane kopiowania](#staged-copy) z włączoną kompresją. Przy użyciu tymczasowego magazynu jest przydatne, gdy przepustowość sieci firmowej i usługami Azure jest czynnikiem ograniczającym i ma wejściowego zestawu danych i zestawu danych wyjściowych zarówno w skompresowanej.

## <a name="considerations-for-column-mapping"></a>Zagadnienia dotyczące mapowania kolumn

Można ustawić **columnMappings** właściwości w przypadku działania kopiowania mapy wszystkie lub podzbiór wejściowych kolumn na kolumny danych wyjściowych. Po usługi przenoszenia danych odczytuje dane ze źródła, musi wykonać mapowanie kolumn danych przed zapisuje dane do ujścia. To dodatkowe przetwarzanie ogranicza przepływność kopiowania.

W przypadku zapytań sklepu źródła danych, na przykład, czy jest relacyjnego magazynu, takich jak SQL Database lub SQL Server, czy jest magazynu NoSQL, takie jak magazyn tabel lub bazy danych rozwiązania Cosmos platformy Azure, należy wziąć pod uwagę wypychanie filtrowanie kolumn i zmianę kolejności logikę **kwerendy** właściwości zamiast mapowania kolumn. W ten sposób projekcji przeprowadzana usługi przenoszenia danych odczytuje dane z magazynu danych źródła, gdy są one bardziej wydajne.

Dowiedz się więcej o [mapowanie schematu działanie kopiowania](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Inne zagadnienia

Jeśli rozmiar danych, którą chcesz skopiować jest duży, można dostosować logiki biznesowej do dalszego partycji danych i zaplanować działanie kopiowania do uruchamiania częściej, aby zredukować rozmiar danych dla każdego uruchamiania działania kopiowania.

Należy zachować ostrożność liczbę zestawów danych i działania kopiowania wymagające fabryki danych nawiązać połączenia z tym samym magazynie danych w tym samym czasie. Wielu zadań jednoczesnych kopii może ograniczyć magazynu danych i prowadzić do pogorszenie wydajności, kopii zadania wewnętrzny ponownych prób, a w niektórych przypadkach, błędy wykonania.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Przykładowy scenariusz: kopiowanie z lokalnego programu SQL Server do magazynu obiektów Blob

**Scenariusz**: utworzeniu potoku można skopiować danych z lokalnego serwera SQL do magazynu obiektów Blob w formacie CSV. Aby przyspieszyć zadanie kopiowania, pliki CSV powinny być kompresowane do formatu bzip2.

**Badanie i analiza**: przepływność działanie kopiowania jest mniej niż 2 MB/s, który jest znacznie mniejsza niż testów porównawczych wydajności.

**Analiza wydajności i dostrajania**: Aby rozwiązać problem z wydajnością, Przyjrzyjmy się jak dane są przetwarzane i przenoszone.

1. **Odczytanie danych**: środowiska uruchomieniowego integracji otwiera połączenie z programem SQL Server i wysyła kwerendę. SQL Server odpowiada, wysyłając strumienia danych do środowiska uruchomieniowego integracji za pośrednictwem sieci intranet.
2. **Serializować i kompresji danych**: środowiska uruchomieniowego integracji serializuje strumienia danych do formatu CSV i kompresuje dane do strumienia bzip2.
3. **Zapisu danych**: środowiska uruchomieniowego integracji przekazywania strumienia bzip2 do magazynu obiektów Blob za pośrednictwem Internetu.

Jak widać, dane są przetwarzane i przenoszone przesyłania strumieniowego sekwencyjnie: SQL Server > LAN > środowiska uruchomieniowego integracji > WAN > magazynu obiektów Blob. **Ogólną wydajność jest uzyskiwany za minimalnej przepustowości w potoku**.

![Przepływ danych](./media/copy-activity-performance/case-study-pic-1.png)

Co najmniej jeden z następujących czynników może spowodować wąskie gardło:

* **Źródło**: sam serwer SQL ma niskiej przepustowości z powodu dużymi obciążeniami.
* **Środowiska uruchomieniowego integracji hosta samodzielnego**:
  * **LAN**: środowiska uruchomieniowego integracji znajduje się daleko od komputera programu SQL Server i ma połączenie o niskiej przepustowości.
  * **Integracja środowiska uruchomieniowego**: środowiska uruchomieniowego integracji osiągnięto jego ograniczenia obciążenia, wykonaj następujące operacje:
    * **Serializacja**: serializacja strumienia danych do formatu CSV ma powolne przepływności.
    * **Kompresja**: wybrano koder-dekoder kompresji powolne (na przykład, bzip2, czyli Core i7 2,8 MB/s).
  * **WAN**: brakuje przepustowości między siecią firmową i usługami Azure (na przykład T1 = 1,544 KB/s; T2 = 6,312 KB/s).
* **Obiekt sink**: magazyn obiektów Blob ma niskiej przepustowości. (W tym scenariuszu jest mało prawdopodobne, ponieważ jego umowy dotyczącej poziomu usług gwarantuje co najmniej 60 MB/s).

W takim przypadku bzip2 kompresji danych może być spowolnienie całego procesu. Przełączanie do koder-dekoder kompresji gzip może ułatwić to "wąskie gardło".

## <a name="reference"></a>Informacje ogólne

Oto monitorowania wydajności i dostrajania odwołań dla niektórych obsługiwanych magazynów:

* Magazyn Azure (w tym magazynie obiektów Blob i Magazyn tabel): [wartości docelowe skalowalności magazynu Azure](../storage/common/storage-scalability-targets.md) i [Lista kontrolna wydajności i skalowalności magazynu Azure](../storage/common/storage-performance-checklist.md)
* Azure SQL Database: Można [monitorowania wydajności](../sql-database/sql-database-single-database-monitor.md) i sprawdź procent jednostki (bazy danych DTU) transakcji bazy danych
* Usługa Azure SQL Data Warehouse: Zdolność jest mierzony w jednostki magazynu danych (dwu); zobacz [Zarządzaj obliczeniowe zasilania w usłudze Azure SQL Data Warehouse (omówienie)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure DB rozwiązania Cosmos: [poziomy wydajności w usłudze Azure DB rozwiązania Cosmos](../cosmos-db/performance-levels.md)
* Lokalny program SQL Server: [monitora i dostrajanie wydajności](https://msdn.microsoft.com/library/ms189081.aspx)
* Lokalny serwer plików: [dostrajania wydajności dla serwerów plików](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne artykuły działania kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Mapowanie schematu działania kopiowania](copy-activity-schema-and-type-mapping.md)
- [Kopiuj działania odporność na uszkodzenia](copy-activity-fault-tolerance.md)
