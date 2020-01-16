---
title: Użyj usługi Azure HDInsight Hadoop klastra na zestaw 1 TB danych — danych zespołu dla celów naukowych
description: Scenariusz end-to-end zatrudniających klastra usługi HDInsight Hadoop, aby skompilować i wdrożyć model przy użyciu dużego (1 TB) publicznie dostępnego zestawu danych za pomocą Team danych dla celów naukowych
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8d47f6f5b983c0f785c76d1b2cede815dda699a4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75968733"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Zespół danych dla celów naukowych w działaniu — przy użyciu klastra usługi Azure HDInsight Hadoop na zestawie danych 1 TB

W tym instruktażu przedstawiono sposób użycia zespołu danych dla celów naukowych w scenariuszu end-to-end z [klastra Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) do przechowywania, zapoznaj się z, inżynier ds. funkcji i w dół przykładowe dane z jednego z publicznie dostępnych [ Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) zestawów danych. Aby zbudować model klasyfikacji binarnej na tych danych używa usługi Azure Machine Learning. Pokazano również, jak do publikowania jednego z tych modeli jako usług sieci Web.

Istnieje również możliwość użycia IPython notebook do wykonywania zadań przedstawionych w tym przewodniku. Użytkownicy, którzy chcieliby Wypróbuj to podejście powinni skontaktować się [wskazówki Criteo przy użyciu połączenia ODBC programu Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) tematu.

## <a name="dataset"></a>Opis elementu Criteo zestawu danych
Criteo, danych jest zestaw danych prognozowania kliknij przycisk, który ma wielkość około 370 GB plików TSV gzip skompresowane (~1.3TB nieskompresowane) wchodzących w skład ponad miliard 4.3 rekordów. Jest ona pobierana z 24 dni kliknij dane udostępniane przez [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/). Dla wygody analityków danych dane dostępne dla nas, aby eksperymentować z zostało rozpakowane.

Każdy rekord w tym zestawie danych zawiera kolumny 40:

* Pierwsza kolumna jest kolumną etykietę wskazującą, czy użytkownik kliknie **Dodaj** (wartość 1) lub nie klikaj (wartość 0)
* są następnie 13 kolumny liczbowe, a
* ostatnich 26 są podzielone na kategorie kolumny

Kolumny są anonimowe i używać szeregu wyliczenia nazw: "Col1" (dla kolumny etykiety) na "Col40" (dla ostatniej kolumnie kategorii).

Poniżej przedstawiono fragment pierwszych 20 kolumny dwóch uwagi (wierszy) z tego zestawu danych:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb

W tym zestawie danych istnieją brakujących wartości w kolumnach liczbowych i podzielonych na kategorie. Opisano w prosty sposób obsługi brakujące wartości. Dodatkowe szczegóły danych są przedstawione w przypadku przechowywania ich do tabel programu Hive.

**Definicja:** *współczynnik przeglądowe (kont.):* procent kliknięć w danych. W tym zestawie danych Criteo kont to około 3.3% lub 0.033.

## <a name="mltasks"></a>Przykłady zadań prognoz
Dwa przykładowe prognozowania problemy zostały rozwiązane w tym przewodniku:

1. **Klasyfikacja binarna**: przewiduje się, czy użytkownik kliknął element Dodaj:

   * Klasa 0: Nie kliknij
   * Klasy 1: kliknij przycisk
2. **Regresja**: przewiduje prawdopodobieństwo kliknij ad z funkcji użytkownika.

## <a name="setup"></a>Ustaw się HDInsight klastra usługi Hadoop do analizy danych
**Uwaga:** zazwyczaj jest to **administratora** zadania.

Konfigurowanie środowiska nauki o danych platformy Azure do tworzenia rozwiązań do analizy predykcyjnej przy użyciu klastrów HDInsight w trzech krokach:

1. [Tworzenie konta magazynu](../../storage/common/storage-account-create.md): to konto magazynu jest używane do przechowywania danych w usłudze Azure Blob Storage. Dane używane w klastrach HDInsight są przechowywane w tym miejscu.
2. [Dostosowywanie klastrów Hadoop w usłudze Azure HDInsight do analizy danych](customize-hadoop-cluster.md): ten krok umożliwia utworzenie klastra usługi Azure HDInsight Hadoop za pomocą 64-bitowego pakietu Anaconda Python 2.7 zainstalowane we wszystkich węzłach. Istnieją dwie ważne czynności (opisanych w tym temacie) do wykonania podczas dostosowywania klastra HDInsight.

   * Musisz połączyć konta magazynu utworzonego w kroku 1 z klastrem usługi HDInsight podczas jego tworzenia. To konto magazynu jest używane do uzyskiwania dostępu do danych, które mogą być przetwarzane w klastrze.
   * Należy włączyć dostęp zdalny z węzłem głównym klastra po jego utworzeniu. Pamiętaj poświadczenia dostępu zdalnego, określone w tym miejscu (inne niż określone dla klastra podczas jego tworzenia): będą one potrzebne do wykonania poniższych procedur.
3. [Utwórz obszar roboczy Azure Machine Learning Studio (klasyczny)](../studio/create-workspace.md): ten Azure Machine Learning obszar roboczy jest używany do kompilowania modeli uczenia maszynowego po początkowej eksploracji danych i pobraniu próbek w klastrze usługi HDInsight.

## <a name="getdata"></a>Pobierz i korzystają z publicznych źródła danych
[Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) zestawu danych może zostać oceniony przez kliknięcie linku, akceptując warunki użytkowania i podając nazwę. Jak to wygląda migawka jest następujący:

![Zaakceptuj warunki Criteo](./media/hive-criteo-walkthrough/hLxfI2E.png)

Kliknij przycisk **Kontynuuj, aby pobrać** Aby dowiedzieć się więcej o zestawie danych i jej dostępność.

Dane znajdują się w publicznym [usługi Azure blob storage](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) lokalizacji: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. "wasb" odwołuje się do lokalizacji magazynu obiektów Blob platformy Azure.

1. Dane w tym magazynie obiektów blob publicznych składa się z trzech podfoldery rozpakowany danych.

   1. Podfolderu *nieprzetworzone/licznik/* zawiera pierwszy 21 dni danych — od dnia\_00 dnia\_20
   2. Nazwa podfolderu *nieprzetworzone/train/* składa się z jednego dnia danych, dzień\_21
   3. Podfolderu *nieprzetworzone/testowania/* składa się z dwóch dni dla danych, dzień\_22 i dzień\_23
2. Dla osób, które ma być uruchamiany przy użyciu danych pierwotnych gzip, są one również dostępne w folderze głównym *nieprzetworzone /* jako day_NN.gz, gdzie NN przechodzi z zakresu od 00 do 23.

Informacje o innym podejściu do uzyskania dostępu, zapoznaj się z, a model, w których te dane, które nie wymaga żadnych lokalnych plików do pobrania zostało wyjaśnione w dalszej części tego przewodnika, podczas tworzenia tabel programu Hive.

## <a name="login"></a>Zaloguj się do węzła głównego klastra
Aby zalogować się z węzłem głównym klastra, należy użyć [witryny Azure portal](https://ms.portal.azure.com) zlokalizować klastra. Kliknij ikonę syna HDInsight po lewej stronie, a następnie kliknij dwukrotnie nazwę klastra. Przejdź do **konfiguracji** , kliknij dwukrotnie ikonę POŁĄCZ w dolnej części strony, a następnie wprowadzić swoje poświadczenia dostępu zdalnego, po wyświetleniu monitu. Spowoduje to przejście do węzła głównego klastra.

Poniżej przedstawiono typowe pierwszego logowania do węzła głównego klastra wygląda następująco:

![Zaloguj się do klastra](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Po lewej stronie jest "Hadoop wiersza polecenia", czyli naszym najważniejszą metodą roboczą do eksploracji danych. Należy zauważyć dwa użyteczne adresy URL — "Hadoop Yarn Status" i "Hadoop nazwy węzła". Adres URL usługi yarn stanu pokazuje postęp zadania i węzeł adresu URL nazwa zwraca szczegółowe informacje dotyczące konfiguracji klastra.

Teraz są skonfigurowane i gotowe do rozpoczęcia pierwszej części tego przewodnika: Eksploracja danych przy użyciu technologii Hive i przygotowywanie danych dla usługi Azure Machine Learning.

## <a name="hive-db-tables"></a> Tworzenie bazy danych programu Hive i tabel
Aby utworzyć tabele programu Hive dla naszych Criteo zestawu danych, otwórz ***wiersza polecenia usługi Hadoop*** na pulpicie węzła głównego, a następnie wprowadź katalog Hive, wprowadzając polecenie

    cd %hive_home%\bin

> [!NOTE]
> Uruchom wszystkie polecenia gałąź w tym przewodniku z Hive bin / directory wiersza. Odpowiada on za wszelkie problemy, ścieżka automatycznie. Można użyć warunków "Gałęzi katalogu wiersz", "Hive bin / directory wiersza" i "wiersza polecenia usługi Hadoop" zamiennie.
>
> [!NOTE]
> Aby wykonać dowolne zapytanie programu Hive, jeden zawsze Użyj następujących poleceń:
>
>

        cd %hive_home%\bin
        hive

Po wyświetleniu Hive REPL, za pomocą "gałęzi >"Zaloguj się, po prostu wyciąć i wkleić kwerenda do wykonania go.

Poniższy kod tworzy bazę danych "criteo", a następnie generuje tabele 4:

* *tabeli w celu wygenerowania liczby* utworzone w dniu dni\_00 dnia\_20
* *tabeli do użycia jako zestaw train* utworzone w dniu\_21, i
* dwa *tabel do użycia jako zestawy danych testowych* utworzone w dniu\_22 i dzień\_23 odpowiednio.

Podziel na dwóch różnych tabel zestawu danych testowych, ponieważ jeden z dni jest dniem wolnym od pracy. Celem jest ustalenie modelu mogą wykryć różnice między dniem wolnym od pracy i innych dni wolnych od stawki za pomocą kliknięć.

Skrypt [przykładowe&#95;hive&#95;tworzenie&#95;criteo&#95;bazy danych&#95;i&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) jest wyświetlane w tym miejscu dla wygody:

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Te tabele są zewnętrzne, dzięki czemu można po prostu wskaż ich lokalizacji usługi Azure Blob Storage (wasb).

**Istnieją dwie metody wykonywania zapytania programu Hive w dowolny:**

1. **Przy użyciu wiersza polecenia REPL Hive**: pierwszy to wydać polecenie "gałąź" i skopiuj i Wklej zapytanie z wiersza polecenia REPL Hive. Aby to zrobić, należy wykonać:

        cd %hive_home%\bin
        hive

     Teraz w REPL wiersza polecenia, wycinanie i wklejanie zapytanie wykonuje je.
2. **Zapisywanie zapytania do pliku i wykonywania polecenia**: drugi cel to zapisać zapytania do pliku .hql ([przykładowe&#95;hive&#95;tworzenie&#95;criteo&#95;bazy danych&#95;i&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) a następnie należy wydać następujące polecenie, aby wykonać zapytanie:

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Upewnij się, tworzenie bazy danych i tabeli
Następnie upewnij się, tworzenie bazy danych za pomocą następującego polecenia z pojemnika Hive / directory wiersz:

        hive -e "show databases;"

Daje to:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Będzie to potwierdzenie utworzenia nowej bazy danych "criteo".

Aby zobaczyć, które tabele zostały utworzone, po prostu wydać polecenie w tym miejscu Hive bin / directory wiersz:

        hive -e "show tables in criteo;"

Następnie powinny zostać wyświetlone następujące dane wyjściowe:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="exploration"></a> Eksplorowanie danych w gałęzi
Teraz wszystko jest gotowe do wykonania czynności niektóre eksploracji danych podstawowych w gałęzi. Rozpocznij od liczby przykłady w pociągu i tabele danych testu.

### <a name="number-of-train-examples"></a>Wiele przykładów szkolenie
Zawartość [przykładowe&#95;hive&#95;liczba&#95;szkolenie&#95;tabeli&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) przedstawiono poniżej:

        SELECT COUNT(*) FROM criteo.criteo_train;

Daje to:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Alternatywnie jeden może również wydać następujące polecenie z pojemnika Hive / directory wiersz:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Wiele przykładów testu w dwa zestawy danych testowych
Teraz liczby przykłady w dwa zestawy danych testowych. Zawartość [przykładowe&#95;hive&#95;liczba&#95;criteo&#95;test&#95;dzień&#95;22&#95;tabeli&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) są tutaj:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Daje to:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

W zwykły sposób, również mogą wywołać skryptu z pojemnika Hive / katalog, w wierszu przez wydanie polecenia:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Na koniec sprawdź liczbę przykłady testów w zestawie danych testowych, oparte na dzień\_23.

Polecenie w tym celu jest podobne do właśnie przedstawionego (odnoszą się do [przykładowe&#95;hive&#95;liczba&#95;criteo&#95;test&#95;dzień&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Daje to:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Rozkład etykiet w zestawie danych szkolenie
Rozkład etykiet w zestawie danych train ma znaczenie. Aby to zobaczyć, czy wyświetlać zawartość [przykładowe&#95;hive&#95;criteo&#95;etykiety&#95;dystrybucji&#95;szkolenie&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Daje to rozkład etykiet:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Należy pamiętać, że wartość procentowa dodatnią etykiety o 3.3% (jak w przypadku oryginalnego zestawu danych).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Histogram dystrybucji niektóre zmienne liczbowe w zestawie danych szkolenie
Możesz użyć natywnych gałęzi "histogram\_liczbowe" funkcję, aby dowiedzieć się, jak wygląda rozkład Zmienne liczbowe. Poniżej przedstawiono zawartość [przykładowe&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Daje to następujące czynności:

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

Widok — POPRZECZNYCH rozłożenie kombinacja w gałęzi służy do generowania danych wyjściowych podobnego do SQL zamiast zwykle listy. Należy pamiętać, że w tej tabeli, pierwszą kolumnę odnosi się do Centrum bin, a druga częstotliwości bin.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Przybliżony percentyle niektóre zmienne liczbowe w zestawie danych szkolenie
Będących przedmiotem zainteresowania z Zmienne liczbowe jest również obliczeń przybliżony percentylach. Natywne w gałęzi "percentyl\_przybliżone użycie zasobów" zrobi to za NAS. Zawartość [przykładowe&#95;hive&#95;criteo&#95;przybliżony&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) są:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Daje to:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

Rozkład percentyle jest ściśle powiązane z dystrybucji histogram dowolnej zmiennej numerycznych zazwyczaj.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Znajdź liczbę unikatowych wartości dla niektórych kategorii kolumn w zestawie danych szkolenie
Kontynuowanie eksplorację danych, Znajdź, dla niektórych kolumn podzielonych na kategorie, liczbę unikatowych wartości, które przyjmują. Aby to zrobić, Pokaż zawartość [przykładowe&#95;hive&#95;criteo&#95;unikatowy&#95;wartości&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Daje to:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Należy pamiętać, że Col15 ma unikatowe wartości 19M! Za pomocą prostego technik, takich jak "hot jeden kodowania" do zakodowania takich o wielu wymiarach zmiennych kategorii nie jest możliwe. W szczególności o nazwie technika zaawansowanych, niezawodnych [uczenia z zlicza](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) co dzień do czynienia ten problem efektywnie wyjaśniono i przedstawione w artykule.

Na koniec Przyjrzyj się liczba unikatowych wartości dla niektórych innych kategorii kolumn także. Zawartość [przykładowe&#95;hive&#95;criteo&#95;unikatowy&#95;wartości&#95;wielu&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) są:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Daje to:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Ponownie należy pamiętać, że z wyjątkiem Col20, wszystkie pozostałe kolumny mają wiele unikatowych wartości.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Wystąpienie wspólnej liczbę par podzielonych na kategorie zmiennych w zestawie danych train

Wystąpienie wspólnej liczby par zmienne podzielonych na kategorie również ma znaczenie. Można to ustalić przy użyciu kodu w [przykładowe&#95;hive&#95;criteo&#95;sparowanych&#95;podzielonych na kategorie&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Odwrotnej kolejności liczby elementów w przypadku ich wystąpienia i spójrz na górę 15, w tym przypadku. To daje nam:

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> Przykładowe zestawy danych dla usługi Azure Machine Learning w dół
Istnienie zbadano zestawy danych i pokazano, jak to zrobić tego rodzaju eksploracji żadnych zmiennych (w tym kombinacje), dół przykładowych zestawów danych, dzięki czemu można tworzyć modele w usłudze Azure Machine Learning. Odwołania, która koncentruje się problem: podany zestaw atrybutów przykład (wartości funkcji z Col2 - Col40), przewidywania, czy Col1 jest 0 (nie kliknij) lub 1 (kliknij).

Przykładowe zestawy danych szkolenie i testowanie 1% oryginalnego rozmiaru w dół, funkcja gałęzi natywnych RAND(). Dalej skrypt [przykładowe&#95;hive&#95;criteo&#95;próbkowanie&#95;szkolenie&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) zrobi to za train zestawu danych:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Daje to:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

Skrypt [przykładowe&#95;hive&#95;criteo&#95;próbkowanie&#95;test&#95;dzień&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) zrobi to za dane z badań, dzień\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Daje to:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Na koniec skryptu [przykładowe&#95;hive&#95;criteo&#95;próbkowanie&#95;test&#95;dzień&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) zrobi to za dane z badań, dzień\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Daje to:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Dzięki temu możesz przystąpić do naszych dół train próbkowanych i testów zestawów danych przeznaczone do budowania modeli w usłudze Azure Machine Learning.

Przed przejściem do usługi Azure Machine Learning, która dotyczy tabeli liczba ma końcowe ważnym elementem. W następnej sekcji podrzędnych Tabela liczba została szczegółowo opisana w niektórych.

## <a name="count"></a> Krótki opis dyskusji w tabeli liczba
Jak kilku zmiennych podzielonych na kategorie ma bardzo wysokie wymiarach. W instruktażu, o nazwie zaawansowane techniki [uczenia z zlicza](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) do kodowania tych zmiennych w wydajny, niezawodny sposób zostanie wyświetlony. Więcej informacji na temat tej techniki jest podany link.

[!NOTE]
>W tym przewodniku koncentruje się na użycie liczba tabel do produkcji compact reprezentacje funkcji o wielu wymiarach podzielonych na kategorie. Nie jest jedynym sposobem, aby zakodować kategorii funkcji Aby uzyskać więcej informacji na temat innych metod zainteresowanych użytkowników można wyewidencjonować [jednego hot-encoding](https://en.wikipedia.org/wiki/One-hot) i [Tworzenie skrótu funkcji](https://en.wikipedia.org/wiki/Feature_hashing).
>

Tworzenia liczba tabel na dane dotyczące liczby, należy użyć danych w folderze nieprzetworzone/liczby. W sekcji modelowania użytkownicy są instruowani, jak tworzyć te tabele liczba kategorii funkcji od podstaw, lub też używać tabeli liczbę wstępnie skompilowanych dla ich eksploracji. W poniżej gdy "wstępnie utworzone tabele liczba" są określane, mamy na myśli przy użyciu tabel liczba, która została podana. W następnej sekcji znajdują się szczegółowe instrukcje dotyczące sposobu dostępu do tych tabel.

## <a name="aml"></a> Budowanie modelu z usługi Azure Machine Learning
Nasz model tworzenia procesu w usłudze Azure Machine Learning obejmuje następujące kroki:

1. [Pobieranie danych z tabel programu Hive w usłudze Azure Machine Learning](#step1)
2. [Utwórz eksperyment: Wyczyść dane i uczyń je funkcją z tabelami liczników](#step2)
3. [Tworzenie, szkolenie i klasyfikacja modelu](#step3)
4. [Ocena modelu](#step4)
5. [Opublikuj go jako usługę sieci web](#step5)

Teraz można przystąpić do tworzenia modeli w usłudze Azure Machine Learning studio. Nasze dół próbki danych zostanie zapisany jako tabele programu Hive w klastrze. Użyj usługi Azure Machine Learning **importu danych** modułu, aby odczytać te dane. Poświadczenia, które mają dostęp do konta magazynu tego klastra można znaleźć w poniżej.

### <a name="step1"></a> Krok 1: Pobieranie danych z tabel programu Hive w usłudze Azure Machine Learning, za pomocą modułu importu danych i wybrać go do eksperymentu uczenia maszynowego
Rozpocznij, wybierając **+ nowy** -> **EKSPERYMENTU** -> **pusty eksperyment**. Następnie w **wyszukiwania** pole w lewym górnym rogu, wyszukaj "Importuj dane". Przeciąganie i upuszczanie **importu danych** modułów na eksperyment kanwy (środkowa część ekranu), aby użyć modułu programu, aby uzyskać dostęp do danych.

Co to jest **importu danych** wygląda jak podczas pobierania danych z tabeli programu Hive:

![Importuj dane pobiera dane](./media/hive-criteo-walkthrough/i3zRaoj.png)

Aby uzyskać **Importuj dane** modułu, wartości parametrów, które znajdują się na rysunku są tylko przykładowe sortowania, konieczne będzie podanie wartości. Poniżej przedstawiono pewne ogólne wskazówki na temat sposobu wypełniania ustawić parametr dla **importu danych** modułu.

1. Wybierz "Zapytanie Hive" **źródła danych**
2. W **zapytanie bazy danych programu Hive** polu Wybierz prosty * FROM < swoje\_bazy danych\_name.your\_tabeli\_nazwy >-jest wystarczająca.
3. **Identyfikator URI serwera Hcatalog**: Jeśli klaster jest "abc", a następnie po prostu to: https://abc.azurehdinsight.net
4. **Nazwa konta użytkownika usługi Hadoop**: nazwa użytkownika, wybrany w momencie uruchomienia urządzeń do klastra. (Nie nazwa użytkownika dostępu zdalnego!)
5. **Hasło konta użytkownika usługi Hadoop**: hasło dla nazwy użytkownika, wybrany w momencie uruchomienia urządzeń do klastra. (Nie hasła dostępu zdalnego!)
6. **Lokalizacja danych wyjściowych**: wybierz pozycję "Azure"
7. **Nazwa konta usługi Azure storage**: Konto magazynu skojarzone z klastrem
8. **Klucz konta usługi Azure storage**: klucz konta magazynu skojarzonego z klastrem.
9. **Nazwa kontenera platformy Azure**: Jeśli nazwa klastra jest "abc", a następnie zazwyczaj jest to po prostu "abc,".

Gdy **importu danych** zakończy pobieranie danych (widoczna zielona znaczników w Module), zapisać te dane jako zestaw danych (z nazwą wybranego). Jak to wygląda:

![Importuj dane zapisywanie danych](./media/hive-criteo-walkthrough/oxM73Np.png)

Kliknij prawym przyciskiem myszy port wyjściowy **importu danych** modułu. Takie działanie spowoduje wyświetlenie **Zapisz jako zestaw danych** opcji i **Visualize** opcji. **Visualize** opcja, jeśli kliknięto, wyświetla 100 wierszy danych, wraz z prawy panel, który jest przydatne w przypadku niektórych statystyki podsumowujące. Aby zapisać dane, po prostu wybierz **Zapisz jako zestaw danych** i postępuj zgodnie z instrukcjami.

Aby wybrać zapisany zestaw danych do użycia w eksperyment machine learning, Znajdź zestawy danych, za pomocą **wyszukiwania** pole pokazano na poniższej ilustracji. Po prostu wpisz imię udostępniła zestawu danych częściowo można uzyskać do niego dostęp i przeciągnij zestaw danych na panelu głównego. Upuszczając go na główny panel zaznacza go do użytku w machine learning modelowania.

![Przeciągnij zestaw danych na panel główny](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> W tym zarówno pociągu, jak i zestawy danych testowych. Pamiętaj również użyć nazwy bazy danych i nazwy tabel, które udostępniła w tym celu. Wartości używanych na rysunku są przeznaczone wyłącznie dla ilustracji purposes.* *
>
>

### <a name="step2"></a> Krok 2: Tworzenie prostego eksperymentu w usłudze Azure Machine Learning do przewidywania kliknięć / nie kliknięć
Nasz Azure Machine Learning Studio (klasyczny) wygląda następująco:

![Eksperymentu uczenia maszynowego](./media/hive-criteo-walkthrough/xRpVfrY.png)

Teraz Zbadaj najważniejsze składniki tego eksperymentu. Przeciągnij naszych train zapisane i najpierw przetestować zestawów danych do kanwy eksperymentu.

#### <a name="clean-missing-data"></a>Czyszczenie brakujących danych
**Clean Missing Data** modułu jest sugeruje nazwa: go czyści brakujące dane w sposób, który może być określone przez użytkownika. Szukaj do tego modułu, aby wyświetlić to:

![Wyczyść brakujące dane](./media/hive-criteo-walkthrough/0ycXod6.png)

W tym miejscu wybrał opcję Zastąp wszystkie brakujące wartości 0. Istnieją inne również opcji, które są wyświetlane, analizując listy rozwijane w module.

#### <a name="feature-engineering-on-the-data"></a>Inżynieria danych
Może to być milionów unikatowe wartości w przypadku niektórych kategorii funkcji dużych zestawów danych. Za pomocą metod naiwni, takie jak kodowanie hot jeden reprezentujący takich funkcji o wielu wymiarach, podzielone na kategorie jest całkowicie niecelowe. W tym instruktażu pokazano, jak korzystać z funkcji count przy użyciu wbudowanych modułów usługi Azure Machine Learning, aby wygenerować compact reprezentujących zmienne o wielu wymiarach podzielonych na kategorie. Wynik końcowy jest mniejszy rozmiar modelu, krótszy czas szkolenia i metryki wydajności, które są dość porównywalne z przy użyciu innych technik.

##### <a name="building-counting-transforms"></a>Tworzenie zliczanie przekształceń
Aby tworzyć funkcje count, należy użyć **kompilacji zliczanie Przekształcanie** modułu, który jest dostępny w usłudze Azure Machine Learning. Moduł wygląda następująco:

![Zliczanie przekształcania modułu właściwości kompilacji](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![kompilacji zliczanie przekształcania modułu](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> W **liczba kolumn** wprowadź te kolumny, które chcesz wykonać liczby na. Zazwyczaj są to (jak wspomniano) o wielu wymiarach podzielone na kategorie kolumny. Pamiętaj, że zestaw danych Criteo ma 26 podzielone na kategorie kolumny: od Col15 do Col40. W tym miejscu możesz liczyć na wszystkich z nich i zapewnić ich indeksów (od 15 do 40 rozdzielone przecinkami, jak pokazano).
>

Aby użyć modułu programu w trybie MapReduce (odpowiedni dla dużych zestawów danych), musisz mieć dostęp do klastra usługi HDInsight Hadoop (używana w przypadku eksploracji funkcji mogą być ponownie używane w tym celu także) i jego poświadczenia. Jakie wypełniane wartości jak wygląda (Zastąp wartości przykładowe z tymi, które są istotne dla własnego przypadek użycia) można znaleźć w poprzednich ilustracjach.

![Parametry modułu](./media/hive-criteo-walkthrough/05IqySf.png)

Na powyższej ilustracji przedstawiono, jak Podaj lokalizację wejściowego obiektu blob. Ta lokalizacja ma danych zarezerwowana dla tworzenia liczba tabel na.

Po zakończeniu tego modułu zapisać transformację dla później, klikając prawym przyciskiem myszy moduł i wybierając **Zapisz jako przekształcenia** opcji:

![Opcja "Zapisz jako przekształcenie"](./media/hive-criteo-walkthrough/IcVgvHR.png)

W naszych powyższej architekturze eksperymentu zestaw danych "ytransform2" odpowiada dokładnie Przekształć zapisanego liczba. W pozostałej części tego eksperymentu, zakłada się, że czytnik używane **kompilacji zliczanie Przekształcanie** modułu na niektóre dane do generowania liczb i można następnie użyć tych liczb do generowania liczba funkcji na zestawach danych, szkolenie i testowanie.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Wybieranie, jakie liczby funkcji do uwzględnienia w ramach zestawów danych szkolenie i testowanie
Raz liczba gotową transformacji, użytkownik może wybrać funkcjach do uwzględnienia w ich train a testów, zestawów danych przy użyciu **zmodyfikować parametry tabeli liczba** modułu. Aby informacje były kompletne ten moduł jest tu ukazywany. Jednak w celu uproszczenia nie faktycznie jest używana w naszym doświadczeniu.

![Modyfikowanie tabeli liczba parametrów](./media/hive-criteo-walkthrough/PfCHkVg.png)

W takim jak widać, kolizję dziennika mają być używane i wycofania kolumny jest ignorowana. Można również ustawić parametry, takie jak próg bin wyrzucania elementów, ile pseudolosowego wcześniejszych przykładów, aby dodać wygładzanie oraz określić, czy używać dowolnej szumu Laplacian lub nie. Wszystkie te funkcje są zaawansowane i zauważyć, że wartości domyślne to dobry punkt wyjścia dla użytkowników, którzy są nowe do tego typu funkcji generowania.

##### <a name="data-transformation-before-generating-the-count-features"></a>Przekształcanie danych przed wygenerowaniem funkcje zliczania
Teraz skoncentrować się na ważny punkt o przekształcaniu naszej szkolenie i testowanie dane przed faktycznie generowania funkcji count. Należy pamiętać, że istnieją dwa **wykonanie skryptu języka R** moduły używane przed zastosowaniem przekształcenia liczba do naszych danych.

![Wykonaj moduły skryptów języka R](./media/hive-criteo-walkthrough/aF59wbc.png)

Oto pierwszy skrypt języka R:

![Pierwszy skrypt języka R](./media/hive-criteo-walkthrough/3hkIoMx.png)

Ten skrypt języka R zmienia nazwę nasze kolumny do nazwy "Col1" do "Col40". Jest to spowodowane przekształcenie liczba oczekuje nazwy tego formatu.

Drugi skrypt języka R równoważy dystrybucji między klasami dodatnie i ujemne (klasy 1 i 0 odpowiednio) przez pobieranie próbek na dół ujemna klasy. Skrypt języka R w tym miejscu pokazuje, jak to zrobić:

![Drugi skrypt języka R](./media/hive-criteo-walkthrough/91wvcwN.png)

W tym prosty skrypt języka R "pos\_minus\_współczynnik" służy do ustawiania ilości równowagę między dodatnich i ujemnych klasy. Jest to ważne, ponieważ usprawnienie nierównowagi klasy zwykle ma do wydajności dla klasyfikacji problemów, których dystrybucja klasy jest nierówne (odwołania, w tym przypadku masz 3.3% dodatnią klasy a ujemna 96.7%).

##### <a name="applying-the-count-transformation-on-our-data"></a>Stosowanie przekształcenia liczba na danych
Na koniec można użyć **Zastosuj przekształcenie** moduł stosowanie przekształceń liczba na naszych szkolenie i testowanie zestawów danych. Ten moduł przyjmuje przekształcenie liczba zapisanych, jako jedne dane wejściowe i szkolenie lub badania zestawów danych jako dane wejściowe i zwraca dane za pomocą funkcji count. Przedstawiono tutaj:

![Zastosuj przekształcenie modułu](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Fragment funkcjach liczba wyglądać jak
Jest to istotne, aby zobaczyć, jak wyglądają funkcji count w naszym przypadku. Poniżej przedstawiono fragment to:

![Funkcje zliczania](./media/hive-criteo-walkthrough/FO1nNfw.png)

Ten fragment pokazuje, że dla kolumn liczone, możesz zliczeń Pobierz i dziennika kolizję oprócz wszelkich odpowiednich backoffs.

Teraz można przystąpić do tworzenia modelu usługi Azure Machine Learning, używając tych zestawów przekształconych danych. W następnej sekcji pokazano, jak można to zrobić.

### <a name="step3"></a> Krok 3: Tworzenie, szkolenie i klasyfikacja modelu

#### <a name="choice-of-learner"></a>Wybór learner
Najpierw musisz wybrać uczeń. Użyj drzewa decyzyjnego dwuklasowych jako uczeń naszych. Poniżej przedstawiono domyślne opcje dla tego uczeń:

![Parametry Two-Class Boosted Decision drzewa](./media/hive-criteo-walkthrough/bH3ST2z.png)

Eksperymentu wybierz wartości domyślne. Należy pamiętać, że wartości domyślne są zwykle zrozumiałe i sposób uzyskać szybkie linie bazowe na wydajność. Na wydajność można poprawić, sprawdzaniu parametrów, jeśli zdecydujesz się po utworzeniu planu bazowego.

#### <a name="train-the-model"></a>Trenowanie modelu
Szkolenia, po prostu wywołać **Train Model** modułu. Dwa obiekty wejściowe do niego są uczeń Two-Class Boosted Decision drzewa i szkolenie zestaw danych. Jest to pokazane poniżej:

![Train Model modułu](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Ocena modelu
Po utworzeniu trenowanego modelu, wszystko jest gotowe, wynik dla zestawu danych testowych i ocenić jej wydajność. To zrobić za pomocą **Score Model** modułu pokazano na poniższym rysunku, wraz z **Evaluate Model** modułu:

![Moduł Score Model (Generowanie wyników przez model)](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step4"></a> Krok 4: Oceny modelu
Na koniec należy analizowanie wydajności modelu. Zazwyczaj dwa problemy klasyfikacji (binarnych) klasy miarą dobrej jest AUC. Wizualizację tych danych, podpinanie **Score Model** moduł **Evaluate Model** modułu dla tego. Klikając **Visualize** na **Evaluate Model** modułu daje grafiki podobny do następującego:

![Ocena modelu BDT modułu](./media/hive-criteo-walkthrough/0Tl0cdg.png)

W danych binarnych (lub dwuklasowego) klasyfikacji problemów z miarą dobrej dokładności przewidywania jest powierzchni pod krzywą (AUC). W poniższej sekcji pokazano naszych wyników przy użyciu tego modelu w naszym zestawie danych testowych. Aby uzyskać dostęp do tej, kliknij prawym przyciskiem myszy port wyjściowy **Evaluate Model** modułu i następnie **Visualize**.

![Wizualizuj modułu Evaluate Model](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step5"></a> Krok 5: Publikowanie modelu jako usługi sieci Web
Możliwość publikowania model usługi Azure Machine Learning jako usługi sieci web z co najmniej problemów jest funkcją cenne składania powszechnie dostępne. Po zakończeniu tej operacji, każdy użytkownik może wykonywać wywołania do usługi sieci web z danymi wejściowymi, że potrzebują prognoz dotyczących, a usługi sieci web używa modelu do zwracania tych prognozy.

Aby to zrobić, należy najpierw zapisać naszych uczonego modelu w postaci Uczonego modelu obiektu. Jest to realizowane przez kliknięcie prawym przyciskiem myszy **Train Model** moduł i przy użyciu **Zapisz jako Uczonego modelu** opcji.

Następnie utwórz dane wejściowe i wyjściowe dla naszej usługi sieci web:

* portu wejściowego pobiera dane, w tym samym formularzu jako wymagających prognoz dotyczących danych
* port wyjściowy zwraca sklasyfikowane etykiety i prawdopodobieństwa.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Wybierz kilka wierszy danych dla portu wejściowego
Jest łatwa w użyciu **Zastosuj przekształcenie SQL** modułu, aby wybrać tylko 10 wierszy, która będzie służyć jako dane wejściowe portu. Wybierz tylko te wiersze danych dla naszych portu wejściowego przy użyciu zapytań SQL, pokazano poniżej:

![Dane wejściowe portu](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Usługa sieci Web
Teraz można przystąpić do uruchomienia małych eksperyment, który może służyć do publikowania usługę sieci web.

#### <a name="generate-input-data-for-webservice"></a>Generuj dane wejściowe dla usługi sieci Web
Jako krok zerowego ponieważ tabela liczba jest duża, podjąć kilka wierszy danych testowych i generowanie danych wyjściowych z niej za pomocą funkcji count. Może to służyć jako format danych wejściowych dla naszej usługi sieci Web. Jest to pokazane poniżej:

![Utwórz BDT danych wejściowych](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Format danych wejściowych, można użyć w danych wyjściowych **Featurized liczba** modułu. Gdy eksperymenty to uruchomiona zostanie zakończone, Zapisz dane wyjściowe z **Featurized liczba** modułu jako zestaw danych. Ten zestaw danych jest używany do wprowadzania danych w ramach usługi sieci Web.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Ocenianie eksperymentu usługi publikowania w sieci Web
Po pierwsze jest wyświetlany, jak to wygląda. Struktura niezbędne jest **Score Model** moduł, który akceptuje naszej uczonego modelu obiektu i kilka wierszy danych wejściowych, które zostały wygenerowane w poprzednich krokach za pomocą **Featurized liczba** modułu. Użyj "Wybieranie kolumn w zestawie danych", aby projekt etykiety Scored i prawdopodobieństwa wynik.

![Wybieranie kolumn w zestawie danych](./media/hive-criteo-walkthrough/kRHrIbe.png)

Zwróć uwagę sposób, w jaki **Select Columns in Dataset** modułu może służyć do "filtrowanie" danych z zestawu danych. Zawartość przedstawiono poniżej:

![Filtrowanie za pomocą Select Columns in Dataset modułu](./media/hive-criteo-walkthrough/oVUJC9K.png)

Aby uzyskać niebieski dane wejściowe i dane wyjściowe portów, możesz po prostu kliknij **przygotowanie webservice** w prawym dolnym rogu. Uruchamianie tego eksperymentu również pozwala nam publikować usługi sieci web: kliknij **OPUBLIKOWAĆ usługi sieci WEB** ikonę u dołu tutaj prawej strony, jak pokazano:

![Publikowanie usługi sieci Web](./media/hive-criteo-walkthrough/WO0nens.png)

Po opublikowaniu usługi sieci Web, Pobierz przekierowanie do strony, która wygląda w ten sposób:

![Pulpit nawigacyjny usługi internetowej](./media/hive-criteo-walkthrough/YKzxAA5.png)

Zwróć uwagę, dwa linki do usługi sieci Web po lewej stronie:

* **ŻĄDAŃ/odpowiedzi** usługi (lub RRS) jest przeznaczona dla prognoz jednego i co została wykorzystana w tym warsztatów.
* **Wykonywanie WSADOWE** Service (BES) jest używany dla prognoz usługi batch i wymaga, że dane wejściowe używane do prognozowania znajdują się w usłudze Azure Blob Storage.

Klikając łącze **ŻĄDAŃ/odpowiedzi** przyjmuje nam stronę, która daje nam wstępnie puszkach kodu w języku C#, python i R. Ten kod może wygodnie służyć do wykonywania wywołań do usługi sieci Web. Należy zauważyć, że klucz interfejsu API na tej stronie musi być używane do uwierzytelniania.

Jest to wygodne skopiuj ten kod języka python do nowej komórki w IPython notebook.

Oto segment kodu w języku python przy użyciu poprawnego klucza interfejsu API.

![Kod języka Python](./media/hive-criteo-walkthrough/f8N4L4g.png)

Należy pamiętać, że domyślny klucz interfejsu API jest zastępowany przy użyciu klucza interfejsu API naszej usługi sieci Web. Klikając **Uruchom** w tej komórce w IPython notebook daje następującą odpowiedź:

![Program IPython odpowiedzi](./media/hive-criteo-walkthrough/KSxmia2.png)

Dla obu test przykładowe pytania (w ramach JSON skrypt języka python), możesz wrócić odpowiedzi w formie "Scored Labels Scored Probabilities". W tym przypadku wartości domyślne zostały tak dobrane, wstępnie zwięzłych kod zapewnia (0 dla wszystkich kolumnach liczbowych i ciąg "value" dla wszystkich kolumn kategorii).

To już koniec nasz przewodnik pokazujący sposób obsługi zestawu danych na dużą skalę, za pomocą usługi Azure Machine Learning. Wprowadzenie terabajt danych, tworzony model predykcyjny i wdrożyć go jako usługę sieci web w chmurze.

