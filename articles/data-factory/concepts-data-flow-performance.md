---
title: Mapowanie przepływu danych wydajności i dostosowywania przewodnik dotyczący usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat kluczowych czynników wpływających na wydajność przepływów danych w usłudze Azure Data Factory, korzystając z przepływów danych mapowania.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 1ee266d7d9846a357dce613817affdb0cde5bfdc
ms.sourcegitcommit: e6cb7ca206a125c05acfd431b5a64391a8dcc6b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67569024"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Mapowanie wydajności przepływów danych i dostosowywania przewodnik

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Platformy Azure Data Factory mapowanie przepływu danych udostępniają interfejs niekorzystające z kodu przeglądarki do projektowania, wdrażania i Organizuj Przekształcanie danych w dużej skali.

> [!NOTE]
> Jeśli nie jesteś zaznajomiony z usługi ADF mapowanie przepływu danych ogólnie rzecz biorąc, zobacz [omówienie przepływów danych](concepts-data-flow-overview.md) przed przeczytaniem tego artykułu.
>

> [!NOTE]
> Podczas projektowania i testowania przepływów danych z poziomu interfejsu użytkownika usługi ADF, upewnij się włączyć przełącznik debugowanie dzięki czemu możliwe jest wykonanie przepływów danych w czasie rzeczywistym bez konieczności oczekiwania na klastrze do rozgrzewki.
>

![Debugowanie przycisk](media/data-flow/debugb1.png "debugowania")

## <a name="monitor-data-flow-performance"></a>Monitorowanie wydajności przepływu danych

Podczas projektowania usługi danych mapowania przepływy w przeglądarce, możesz test jednostkowy Każde przekształcenie poszczególnych, klikając kartę Podgląd danych, w dolnym okienku ustawienia dla każdego transformacji. Następnym krokiem, jakie należy podjąć jest testowanie usługi danych przepływ end-to-end w Projektancie potoku. Dodaj działanie wykonania przepływu danych i użyj przycisku debugowania do testowania wydajności przepływu danych. W dolnym okienku w oknie potok będzie widoczna ikona eyeglass w obszarze "Akcje":

![Monitor przepływu danych](media/data-flow/mon002.png "Monitor 2 przepływ danych")

Klikając tę ikonę, zostanie wyświetlona plan wykonywania i profil wydajności kolejnych przepływu danych. Te informacje można użyć do oszacowania wydajności przepływu danych z różnych wielkości źródeł danych. Należy pamiętać, że można założyć 1 minuta czasu konfiguracji wykonywania zadania dla klastra, w obliczeniach ogólnej wydajności i korzystania z domyślnego środowiska Azure Integration Runtime może być konieczne dodanie 5 minut czasu klastra w górę pokrętła także.

![Monitorowanie przepływu danych](media/data-flow/mon003.png "Monitor 3 przepływ danych")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optymalizacja dla usługi Azure SQL Database i Azure SQL Data Warehouse

![Źródło część](media/data-flow/sourcepart3.png "część źródła")

### <a name="partition-your-source-data"></a>Partycjonowanie danych źródłowych

* Przejdź do "Optymalizuj" i wybierz pozycję "Źródło". Ustaw kolumnę określonej tabeli lub typu w zapytaniu.
* Jeśli została wybrana opcja "kolumnę", następnie wybierz kolumny partycji.
* Ponadto można ustawić maksymalną liczbę połączeń do bazy danych SQL Azure. Możesz spróbować wyższe ustawienie do uzyskania równoległych połączeń z bazą danych. Jednak czasami może spowodować szybsze działanie z ograniczoną liczbę połączeń.
* Źródło tabel bazy danych nie trzeba być dzielony na partycje.
* Ustawianie zapytania w swoją transformację źródłowego, który pasuje do schematu partycjonowania tabeli bazy danych umożliwi aparatu bazy danych źródła, wykorzystywać eliminacji partycji.
* Jeśli źródło nie jest partycjonowany, ADF będą nadal używać partycjonowania w środowisku przekształcania platformy Spark na podstawie klucza wybranej podczas przekształcania źródła danych.

### <a name="set-batch-size-and-query-on-source"></a>Ustaw rozmiar partii i zapytania w źródle

![Źródło](media/data-flow/source4.png "źródła")

* Ustawianie rozmiaru partii zleca usługi ADF, aby przechowywać dane w zestawach w pamięci, a nie wiersz po wierszu. Jest to ustawienie opcjonalne i może zostać wykorzystać zasoby w węzłach obliczeniowych, jeśli nie są one prawidłowo wielkości.
* Ustawianie zapytania umożliwia filtrowanie wierszy po prawej stronie w miejscu źródłowym przed nawet przychodzących dla przepływu danych do przetwarzania, co ułatwia szybsze pozyskiwania danych początkowych.
* Jeśli używasz zapytania, można dodać wskazówki zapytania opcjonalne dla Twojej bazy danych SQL Azure, czyli READ UNCOMMITTED

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>Ustaw poziom izolacji ustawieniami przekształcania źródła dla zestawów danych SQL

* Odczyt nieprzekazany zapewnia szybsze wyniki zapytania na przekształcenie źródła

![Poziom izolacji](media/data-flow/isolationlevel.png "poziom izolacji")

### <a name="set-sink-batch-size"></a>Ustaw rozmiar partii ujścia

![Obiekt sink](media/data-flow/sink4.png "ujście")

* Aby uniknąć przetwarzania wiersz po wierszu przepływów danych, należy ustawić "rozmiar partii" w ustawieniach obiektu sink dla bazy danych SQL Azure. Informuje, że usługi ADF, aby proces bazy danych zapisuje w partiach, w zależności od rozmiaru, pod warunkiem.

### <a name="set-partitioning-options-on-your-sink"></a>Ustaw opcje obiektu sink partycjonowanie

* Nawet jeśli nie masz dane podzielone na partycje w tabelach bazy danych SQL Azure docelowego, przejdź na kartę Optymalizuj i ustawić partycjonowania.
* Bardzo często po prostu informuje usługi ADF, aby używa działanie okrężne partycjonowania w klastrach platformy Spark wykonanie powoduje szybsze ładowanie zamiast wymuszania wielokrotnego wszystkich połączeń z jednego węzła/partycji danych.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Zwiększ rozmiar Twojej aparatu obliczeniowego w Azure Integration Runtime

![Nowe środowisko IR](media/data-flow/ir-new.png "nowe środowisko IR")

* Zwiększ liczbę rdzeni, które spowoduje zwiększenie liczby węzłów i udostępnić więcej mocy obliczeniowej w celu wykonywania zapytań i zapisu do bazy danych SQL Azure.
* Wypróbuj "Obliczenia zoptymalizowane pod kątem" i "Zoptymalizowanego pod kątem pamięci" opcje do zastosowania większej ilości zasobów do węzłów obliczeniowych.

### <a name="unit-test-and-performance-test-with-debug"></a>Testów jednostkowych i testów wydajności za pomocą debugowania

* Po jednostki testowania przepływu danych, ustaw "Debug przepływ danych" na wartość ON.
* W Projektancie przepływu danych karta Podgląd danych na przekształceń do wyświetlania wyników logiki przekształcania.
* Test jednostkowy dane przepływy przy użyciu projektanta potoku, umieszczając działanie z przepływu danych w projekcie potoku kanwy i testowanie za pomocą przycisku "Debugowanie".
* Testowanie w trybie debugowania będą działać względem środowisku na żywo klastrowym przygotowaniu bez konieczności oczekiwania na telefoniczny pokrętła klaster just in time.

### <a name="disable-indexes-on-write"></a>Wyłącz indeksy podczas zapisu
* Użyj ADF potoku przechowywane procedury działania przed działaniem usługi przepływ danych, które wyłączają indeksów w tabelach docelowych, które są zapisywane z magazynem ujścia.
* Po działania przepływu danych Dodaj kolejne działanie przechowywanej, która umożliwiała te indeksy.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Zwiększ rozmiar bazy danych SQL Azure
* Zaplanuj zmiany rozmiaru źródła i ujścia Azure SQL DB zanim przebieg, który ogranicza potoku, aby zwiększyć przepływność i minimalizowanie ograniczania platformy Azure, gdy liczba jednostek DTU.
* Po zakończeniu wykonywanie potoku można zmienić rozmiar bazy danych do ich normalna szybkość wykonywania.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Optymalizacja dla usługi Azure SQL Data Warehouse

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>Ładowanie danych zbiorczo za pomocą programu Polybase za pomocą przemieszczania

* Aby uniknąć przetwarzania wiersz po wierszu przepływów danych, należy ustawić opcję "Staging" w ustawieniach obiektu Sink tak, aby ADF dzięki technologii Polybase, aby uniknąć wstawia wiersz po wierszu do magazynu danych. Wydać polecenie usługi ADF, aby przy użyciu technologii Polybase, aby dane mogą być ładowane w trybie zbiorczym.
* Podczas wykonywania Twoje działanie przepływu danych w potoku, za pomocą przemieszczania jest włączona, będzie konieczne wybranie lokalizacji magazynu obiektów Blob, dane tymczasowe do ładowania zbiorczego.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Zwiększ rozmiar usługi SQL data Warehouse platformy Azure

* Zaplanuj zmiany rozmiaru źródła i ujścia Azure SQL data Warehouse, przed uruchomieniem potoku, aby zwiększyć przepływność i minimalizowanie ograniczania platformy Azure, po osiągnięciu limitów jednostek DWU.

* Po zakończeniu wykonywanie potoku można zmienić rozmiar bazy danych do ich normalna szybkość wykonywania.

## <a name="optimize-for-files"></a>Optymalizacja pod kątem plików

* Można kontrolować, jak wiele partycji, używających usługi ADF. Na każde Przekształcenie źródła i ujścia, a także każde przekształcenie poszczególnych można ustawić schematu partycjonowania. W przypadku plików mniejszych może się okazać, wybierając "Jednej partycji" może czasami pracować, lepsze i szybsze niż pytaniem platformy Spark w celu podzielenia małych plików.
* Jeśli nie masz wystarczająco dużo informacji o źródle danych, możesz "Działanie okrężne" partycjonowanie i ustaw liczbę partycji.
* Eksplorowanie danych, okazać, że są kolumn, które mogą być klawiszy skrótu dobre za pomocą skrótu partycjonowanie opcji.

### <a name="file-naming-options"></a>Opcje nazewnictwa plików

* Domyślny rodzaj pisanie przekształcone dane w usłudze ADF mapowanie przepływu danych jest do zapisu do zestawu danych, który zawiera obiekt Blob lub połączonej usługi Azure Data Lake Store. Należy ustawić ten zestaw danych, aby wskazywał folder lub kontenera, wskazanego pliku.
* Użycie przepływów danych, Azure Spark usługi Databricks na wykonanie, co oznacza, że dane wyjściowe zostanie ona podzielona przez wiele plików, na podstawie albo domyślne Spark partycjonowania lub partycjonowanie schemat, które zostały wybrane.
* Bardzo często operacji w przepływu danych w usłudze ADF jest wybrać opcję "Dane wyjściowe do pojedynczego pliku", tak aby wszystkie pliki części danych wyjściowych są scalane w pojedynczym wyjściowym plik.
* Ta operacja wymaga jednak, że dane wyjściowe zmniejsza się do jednej partycji na pojedynczym węźle klastra.
* Miej to na uwadze podczas wybierania to popularne rozwiązanie. Można uruchomić wykorzystać zasoby węzła klastra, w przypadku łączenia wielu plików źródłowych duże w pojedynczym wyjściowym pliku partycji.
* Aby uniknąć wyczerpaniem zasobów obliczeniowych, w węźle, zachowaj domyślne lub jawnego schematu partycjonowania w usłudze ADF, który optymalizuje wydajność, a następnie dodaj kolejne działania kopiowania w potoku, który łączy wszystkie części pliki z folderu danych wyjściowych do pojedynczego nowe plik. Zasadniczo ta technika oddziela akcji transformacji z scalanie plików i osiąga ten sam wynik jako ustawienie "dane wyjściowe do pojedynczego pliku".

### <a name="looping-through-file-lists"></a>Zapętlenie przez listy plików

W większości przypadków przepływu danych w usłudze ADF wykona lepiej z potokiem, który umożliwia przekształcenie źródła przepływu danych do iteracji przez wiele plików. Innymi słowy zaleca się używać symboli wieloznacznych lub listy plików w źródle danych przepływu, aby przejść przez listę dużych plików za pomocą instrukcji ForEach w potoku, wywołanie wykonania przepływu danych w każdej iteracji. Proces przepływu danych będą wykonywane szybciej, umożliwiając tworzenie pętli do wewnątrz przepływu danych.

Na przykład jeśli mam listę plików danych z lipca 2019 I chcesz przetworzyć w folderze w usłudze Blob Storage, będzie wydajniej wywołać jeden raz działania wykonywania przepływu danych z potokiem i użyć symbolu wieloznacznego w źródle, jak to :

```DateFiles/*_201907*.txt```

To będą działać lepiej niż wyszukiwania względem Store obiektów Blob w potoku, która następnie wykonuje iterację we wszystkich dopasowanych plików przy użyciu działania wykonywania przepływu danych wewnątrz pętli ForEach.

## <a name="next-steps"></a>Następne kroki

Zobacz inne przepływ danych artykuły związane z wydajnością:

- [Zakładka Optymalizuj przepływ danych](concepts-data-flow-optimize-tab.md)
- [Działanie przepływu danych](control-flow-execute-data-flow-activity.md)
- [Monitorowanie wydajności przepływu danych](concepts-data-flow-monitoring.md)
