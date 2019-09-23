---
title: Mapowanie wydajności przepływu danych i przewodnik dostrajania w Azure Data Factory | Microsoft Docs
description: Zapoznaj się z najważniejszymi czynnikami wpływającymi na wydajność przepływów danych w Azure Data Factory w przypadku korzystania z mapowania przepływów danych.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 09/22/2019
ms.openlocfilehash: e4b3e08c0cc7fc1ead2aed551c228c6a1165c3b6
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180860"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Przewodnik dotyczący wydajności i dostrajania przepływu danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory mapowanie przepływów danych udostępnia interfejs przeglądarki bez kodu, który umożliwia projektowanie, wdrażanie i organizowanie transformacji danych na dużą skalę.

> [!NOTE]
> Jeśli przed zapisaniem tego artykułu nie znasz ogólnie przepływów danych dotyczących mapowania ADF, zobacz [Omówienie przepływów danych](concepts-data-flow-overview.md) .
>

> [!NOTE]
> Podczas projektowania i testowania przepływów danych z poziomu interfejsu użytkownika usługi ADF należy włączyć przełącznik debugowania, aby można było wykonywać przepływy danych w czasie rzeczywistym bez czekania na rozgrzewanie klastra.
>

![Przycisk Debuguj](media/data-flow/debugb1.png "Debuguj")

## <a name="monitor-data-flow-performance"></a>Monitorowanie wydajności przepływu danych

Podczas projektowania przepływów danych mapowania w przeglądarce można testować poszczególne pojedyncze przekształcenia, klikając kartę Podgląd danych w dolnym okienku ustawienia dla każdej transformacji. Następnym krokiem, który należy wykonać, jest przetestowanie kompleksowego przepływu danych w projektancie potoku. Dodaj działanie wykonaj przepływ danych i użyj przycisku Debuguj, aby przetestować wydajność przepływu danych. W dolnym okienku okna potoku zobaczysz ikonę Eyeglass w obszarze "akcje":

![Monitor przepływu danych](media/data-flow/mon002.png "Monitor przepływu danych 2")

Kliknięcie tej ikony spowoduje wyświetlenie planu wykonania i kolejnego profilu wydajności przepływu danych. Korzystając z tych informacji, można oszacować wydajność przepływu danych w odniesieniu do różnych źródeł danych. Należy pamiętać, że można przystąpić do 1 minuty czasu konfigurowania wykonywania zadań klastra w ogólnym obliczaniu wydajności i w przypadku korzystania z Azure Integration Runtime domyślnego, konieczne może być również dodanie 5 minut czasu.

![Monitorowanie przepływu danych](media/data-flow/mon003.png "Monitor przepływu danych 3")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optymalizacja pod kątem Azure SQL Database i Azure SQL Data Warehouse

![Część źródłowa](media/data-flow/sourcepart3.png "Część źródłowa")

### <a name="partition-your-source-data"></a>Partycjonowanie danych źródłowych

* Przejdź do pozycji "Optymalizuj" i wybierz pozycję "Źródło". Ustaw konkretną kolumnę tabeli lub typ w zapytaniu.
* W przypadku wybrania opcji "kolumna" wybierz kolumnę partycji.
* Ponadto ustaw maksymalną liczbę połączeń z bazą danych Azure SQL. Możesz wypróbować wyższe ustawienie, aby uzyskać równoległe połączenia z bazą danych. Jednak niektóre przypadki mogą spowodować szybszą wydajność dzięki ograniczonej liczbie połączeń.
* Tabele źródłowej bazy danych nie muszą być partycjonowane.
* Ustawienie zapytania w transformacji źródłowej zgodnej ze schematem partycjonowania tabeli bazy danych zezwoli na użycie przez aparat źródłowej bazy danych operacji eliminacji partycji.
* Jeśli źródło nie jest już podzielone na partycje, moduł ADF nadal będzie używać partycjonowania danych w środowisku transformacji Spark na podstawie klucza wybieranego w transformacji źródłowej.

### <a name="set-batch-size-and-query-on-source"></a>Ustawianie rozmiaru partii i zapytania dla źródła

![Źródło](media/data-flow/source4.png "Źródło")

* Ustawienie rozmiaru wsadu spowoduje, że funkcja ADF będzie przechowywać dane w zestawach w pamięci zamiast wierszy po wierszu. Jest to ustawienie opcjonalne i w węzłach obliczeniowych można wylogować się z zasobów, jeśli ich rozmiar nie jest prawidłowy.
* Ustawienie zapytania może umożliwiać filtrowanie wierszy bezpośrednio w źródle, zanim będą nawet docierać do przepływu danych do przetworzenia, co może spowodować szybsze pozyskiwanie danych.
* Jeśli używasz zapytania, możesz dodać opcjonalne wskazówki dotyczące zapytań dla bazy danych SQL Azure, tj. odczytu niezatwierdzonego

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>Ustaw poziom izolacji dla ustawień transformacji źródła dla zestawów danych SQL

* Odczytanie niezatwierdzone zapewni szybsze wyniki zapytania dotyczące transformacji źródłowej

![Poziom izolacji](media/data-flow/isolationlevel.png "Poziom izolacji")

### <a name="set-sink-batch-size"></a>Ustaw rozmiar wsadu ujścia

![Obiekt sink](media/data-flow/sink4.png "Obiekt sink")

* Aby uniknąć przetwarzania wierszy na wierszach przepływów danych, ustaw wartość "rozmiar wsadu" w ustawieniach ujścia usługi Azure SQL DB. Spowoduje to wyświetlenie podajnika APD w celu przetwarzania zapisów w bazie danych w partiach na podstawie podanego rozmiaru.

### <a name="set-partitioning-options-on-your-sink"></a>Ustawianie opcji partycjonowania w Twoim zbiorniku

* Nawet jeśli nie masz partycjonowanych danych w docelowych tabelach usługi Azure SQL DB, przejdź do karty Optymalizacja i ustaw partycjonowanie.
* Bardzo często mówiąc, po prostu, gdy funkcja ADF używa partycjonowania okrężnego w klastrach wykonywania platformy Spark, może znacznie przyspieszyć ładowanie danych zamiast wymuszać wszystkie połączenia z jednego węzła/partycji.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Zwiększ rozmiar aparatu obliczeniowego w Azure Integration Runtime

![Nowy IR](media/data-flow/ir-new.png "Nowy IR")

* Zwiększ liczbę rdzeni, aby zwiększyć liczbę węzłów i zapewnić większą moc obliczeniową do wykonywania zapytań i zapisywania do bazy danych Azure SQL.
* Wypróbuj opcje "obliczenia zoptymalizowane" i "zoptymalizowane pod kątem pamięci", aby zastosować więcej zasobów do węzłów obliczeniowych.

### <a name="unit-test-and-performance-test-with-debug"></a>Test jednostkowy i test wydajności z debugowaniem

* Podczas testowania jednostkowego przepływy danych ustaw przycisk "Debugowanie przepływu danych" na wartość włączone.
* W Projektancie przepływu danych Użyj karty Podgląd danych na przekształceń, aby wyświetlić wyniki logiki transformacji.
* Testowanie jednostkowe przepływów danych z projektanta potoku przez umieszczenie na kanwie projektu potoku działania dotyczącego przepływu danych i użycie przycisku "Debuguj" do przetestowania.
* Testowanie w trybie debugowania będzie działać w rozgrzanym środowisku klastra bez konieczności oczekiwania na odczekanie klastra just in Time.
* Podczas debugowania podglądu danych w środowisku projektanta przepływu danych można ograniczyć ilość danych, które można testować za pomocą każdego źródła, ustawiając limit wierszy z linku ustawienia debugowania w interfejsie użytkownika projektanta przepływu danych. Należy pamiętać, że należy najpierw włączyć tryb debugowania.

![Ustawienia debugowania](media/data-flow/debug-settings.png "Ustawienia debugowania")

* Podczas testowania przepływów danych z poziomu wykonywania debugowania potoku można ograniczyć liczbę wierszy używanych do testowania przez ustawienie rozmiaru próbkowania dla każdego źródła. Należy pamiętać, aby wyłączyć próbkowanie podczas planowania potoków zgodnie z regularnym harmonogramem operacyjnym.

![Próbkowanie wierszy](media/data-flow/source1.png "Próbkowanie wierszy")

### <a name="disable-indexes-on-write"></a>Wyłącz indeksy przy zapisie
* Użyj działania procedury składowanej w potoku ADF przed działaniem przepływu danych, które powoduje wyłączenie indeksów w tabelach docelowych, które są zapisywane na podstawie ujścia.
* Po działaniu przepływu danych Dodaj kolejne przechowywane działanie procedury, które włączyło te indeksy.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Zwiększanie rozmiaru bazy danych SQL Azure
* Zaplanuj zmianę rozmiarów źródła i ujścia usługi Azure SQL DB przed uruchomieniem potoku, aby zwiększyć przepływność i zminimalizować ograniczenie przepustowości platformy Azure po osiągnięciu limitów jednostek DTU.
* Po zakończeniu wykonywania potoku można zmienić rozmiar baz danych z powrotem na ich normalną szybkość uruchamiania.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Optymalizacja pod kątem Azure SQL Data Warehouse

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>Ładowanie danych luzem przy użyciu funkcji przemieszczania za pośrednictwem bazy

* Aby zapobiec przetwarzaniu przepływów danych przez wiersz po wierszu, ustaw opcję "przemieszczanie" w ustawieniach ujścia, aby można było użyć wieloskładnikowego, aby uniknąć wstawiania wierszy w wierszu do DW. Spowoduje to, że funkcja ADF będzie używać bazy danych, aby można było ładować dane zbiorczo.
* Gdy wykonujesz działanie przepływu danych z potoku, z włączonym przemieszczaniem wstępnym, musisz wybrać lokalizację magazynu obiektów BLOB dla danych przemieszczania na potrzeby ładowania zbiorczego.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Zwiększanie rozmiaru magazynu danych SQL Azure

* Zaplanuj zmianę rozmiarów źródła i ujścia usługi Azure SQL DW przed uruchomieniem potoku, aby zwiększyć przepływność i zminimalizować ograniczenie przepustowości platformy Azure po osiągnięciu limitów jednostek dwu.

* Po zakończeniu wykonywania potoku można zmienić rozmiar baz danych z powrotem na ich normalną szybkość uruchamiania.

## <a name="optimize-for-files"></a>Optymalizuj dla plików

* Możesz kontrolować liczbę partycji, które będą używane przez ADF. Dla każdego źródła & transformacji, a także poszczególnych transformacji pojedynczych, można ustawić schemat partycjonowania. W przypadku mniejszych plików można wybrać opcję "pojedyncza partycja" może czasami pracować lepiej i szybciej niż w przypadku, gdy platforma Spark ma dzielić małe pliki.
* Jeśli nie masz wystarczającej ilości informacji na temat danych źródłowych, możesz wybrać partycjonowanie "Round Robin" i ustawić liczbę partycji.
* W przypadku eksplorowania danych i znajdowania kolumn, które mogą być dobrymi kluczami skrótów, użyj opcji partycjonowania skrótów.
* Podczas debugowania w podglądzie danych i w potoku debugowania należy zauważyć, że rozmiar i rozmiary próbkowania źródłowych elementów DataSet opartych na plikach mają zastosowanie tylko do liczby zwracanych wierszy, a nie liczby odczytanych wierszy. Jest to ważne, ponieważ może to skutkować wydajnością wykonywania debugowania i może spowodować niepowodzenie przepływu.
* Należy pamiętać, że klastry debugowania są domyślnie małymi klastrami z jednym węzłem, dlatego należy użyć tymczasowych małych plików do debugowania. Przejdź do pozycji Ustawienia debugowania i wskaż mały podzestaw danych przy użyciu pliku tymczasowego.

![Ustawienia debugowania](media/data-flow/debugsettings3.png "Ustawienia debugowania")

### <a name="file-naming-options"></a>Opcje nazewnictwa plików

* Domyślnym charakterem zapisu przekształconych danych w przepływie danych w ramach mapowania ADF jest zapis do zestawu danych, który ma połączoną usługę obiektu BLOB lub ADLS. Należy ustawić ten zestaw danych, aby wskazywał do folderu lub kontenera, a nie plik o nazwie.
* Przepływy danych używają Azure Databricks Spark do wykonania, co oznacza, że dane wyjściowe zostaną podzielone na wiele plików na podstawie domyślnego partycjonowania platformy Spark lub schematu partycjonowania, który został jawnie wybrany.
* Bardzo powszechną operacją w przepływie danych ADF jest wybranie opcji "wyjście do pojedynczego pliku", aby wszystkie pliki składników wyjściowych zostały scalone w jednym pliku wyjściowym.
* Jednak ta operacja wymaga, aby dane wyjściowe były ograniczone do pojedynczej partycji w pojedynczym węźle klastra.
* Należy pamiętać o tym podczas wybierania tej popularnej opcji. Zasoby węzłów klastra można uruchamiać w przypadku łączenia wielu dużych plików źródłowych w jedną partycję pliku wyjściowego.
* Aby uniknąć wyczerpania zasobów węzłów obliczeniowych, można zachować domyślny lub jawny schemat partycjonowania w podajniku APD, który optymalizuje wydajność, a następnie dodać kolejne działanie kopiowania w potoku, które scala wszystkie pliki części z folderu wyjściowego z nowym pojedynczym rozszerzeniem. Zasadniczo ta technika oddziela akcję transformacji od scalania plików i osiąga ten sam wynik, co ustawienie "dane wyjściowe do pojedynczego pliku".

### <a name="looping-through-file-lists"></a>Zapętlenie za poorednictwem list plików

W większości wystąpień przepływy danych w podajniku APD będą wykonywane lepiej z potoku, który umożliwia przekształcanie źródła przepływu danych w celu iteracji wielu plików. Innymi słowy, preferowane jest używanie symboli wieloznacznych lub list plików wewnątrz źródła w przepływie danych niż w przypadku iteracji na dużej liście plików przy użyciu instrukcji ForEach w potoku, wywołując przepływ danych do wykonania dla każdej iteracji. Proces przepływu danych będzie wykonywany szybciej, umożliwiając zapętlenie w przepływie danych.

Na przykład jeśli mam listę plików danych z lipca 2019, które chcą przetworzyć w folderze w Blob Storage, bardziej wydajnym sposobem jest wywołanie działania Execute Flow przepływ danych z potoku i użycie symbolu wieloznacznego w źródle, takiego jak ten :

```DateFiles/*_201907*.txt```

Wykonanie tej czynności będzie lepszym rozwiązaniem niż wyszukiwanie magazynu obiektów BLOB w potoku, który następnie iteruje we wszystkich dopasowanych plikach za pomocą działania instrukcji EXECUTE Flow przepływu danych wewnątrz.

### <a name="increase-the-size-of-your-debug-cluster"></a>Zwiększ rozmiar klastra debugowania

Domyślnie włączenie debugowania będzie używać domyślnego środowiska Azure Integration Runtime, które jest tworzone automatycznie dla każdej fabryki danych. Ten domyślny Azure IR jest ustawiany dla 8 rdzeni, 4 dla węzła sterownika i 4 dla węzła procesu roboczego przy użyciu ogólnych właściwości obliczeń. Podczas testowania większej ilości danych można zwiększyć rozmiar klastra debugowania, tworząc nowe Azure IR z większą konfiguracją i wybierając tę nową Azure IR po włączeniu debugowania. Spowoduje to, że funkcja ADF użyje tego Azure IR do wyświetlania podglądu danych i debugowania potoku przy użyciu przepływów danych.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi artykułami dotyczącymi przepływu danych związanymi z wydajnością:

- [Karta Optymalizacja przepływu danych](concepts-data-flow-optimize-tab.md)
- [Działanie przepływu danych](control-flow-execute-data-flow-activity.md)
- [Monitorowanie wydajności przepływu danych](concepts-data-flow-monitoring.md)
