---
title: Proces nauki danych zespołu w akcji — przy użyciu klastra usługi Azure HDInsight Hadoop w zestawie danych 1 TB | Dokumentacja firmy Microsoft
description: Scenariusz end-to-end wykorzystujących klastra usługi Hadoop w usłudze HDInsight do tworzenia i wdrażania modelu przy użyciu dużych publicznie dostępnych dataset (1 TB) przy użyciu procesu nauki danych Team
services: machine-learning,hdinsight
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 72d958c4-3205-49b9-ad82-47998d400d2b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev
ms.openlocfilehash: 760e08643fb3e71478fc899278591569da1d515b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Proces nauki danych zespołu w akcji — przy użyciu klastra usługi Azure HDInsight Hadoop na 1 TB zestawu danych

W tym przewodniku przedstawiono sposób użycia procesu nauki danych zespołu w scenariuszu end-to-end z [klastra usługi Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) do przechowywania, Eksploruj funkcję odtwarzania i w dół przykładowych danych z jednej z dostępnych publicznie [ Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) zestawów danych. Uczenie maszynowe Azure są używane do tworzenia modelu klasyfikacji binarnej na tych danych. Widoczny jest również sposób publikowania jednego z tych modeli jako usługę sieci Web.

Istnieje również możliwość Notes IPython służy do wykonywania zadań przedstawionych w tym przewodniku. Aby skorzystać z tej metody użytkownicy powinni skontaktować [wskazówki Criteo za pomocą połączenia ODBC programu Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) tematu.

## <a name="dataset"></a>Opis elementu Criteo Dataset
Criteo, danych to około 370 GB plików TSV gzip skompresowane (~1.3TB nieskompresowane), zestawu danych prognozowania kliknij składającej się z więcej niż 4.3 miliardy rekordów. Jest ona pobierana z 24 dni kliknij danych udostępnionych przez [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Dla wygody analityków danych danych dostępnych do nas eksperymentować zostało rozpakowane.

Każdy rekord w tym elemencie dataset zawiera kolumny 40:

* Pierwsza kolumna jest kolumną etykietę wskazującą, czy użytkownik kliknie **Dodaj** (wartość 1) lub w ogóle nie kliknij jedną (wartość 0)
* następnie 13 kolumny są numeryczną, i
* ostatni 26 są podzielone na kategorie kolumn

Kolumny są anonimowe i szereg wyliczany nazwy: "Col1" (dla kolumny etykiety) do "Col40" (dla ostatniej kolumny podzielone na kategorie).            

Poniżej przedstawiono fragment 20 pierwszych kolumn dwóch uwag (wiersze) z tego zestawu danych:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

W tym zestawie danych istnieją brakujące wartości w kolumnach liczbowych i podzielone na kategorie. Opisano prostą metodę obsługi brakujące wartości. Dodatkowe szczegóły danych są przedstawione podczas zapisywania ich w tabele programu Hive.

**Definicja:** *szybkość przeglądowego (kont.):* procent kliknięć w danych. W tym zestawie danych Criteo Ewidencyjne to około 3.3% lub 0.033.

## <a name="mltasks"></a>Przykłady prognozowania zadań
Dwa przykładowe prognozowania problemy zostały rozwiązane w tym przewodniku:

1. **Klasyfikacji binarnej**: wskazuje, czy użytkownik kliknął add:
   
   * Klasa 0: Nie kliknij
   * Klasa 1: kliknij przycisk
2. **Regresja**: prognozuje prawdopodobieństwo kliknij ad z funkcji użytkownika.

## <a name="setup"></a>Ustaw się HDInsight klastra usługi Hadoop do analizy danych
**Uwaga:** jest to zazwyczaj **Admin** zadań.

Konfigurowanie środowiska Azure nauki danych do tworzenia rozwiązań analizy predykcyjnej z klastrami usługi HDInsight w trzy kroki:

1. [Utwórz konto magazynu](../../storage/common/storage-create-storage-account.md): to konto magazynu jest używany do przechowywania danych w magazynie obiektów Blob Azure. Dane używane w klastrach HDInsight są przechowywane w tym miejscu.
2. [Dostosowywanie Hadoop w usłudze Azure Hdinsight do analizy danych](customize-hadoop-cluster.md): ten krok umożliwia utworzenie klastra usługi Azure HDInsight Hadoop z 64-bitowych Anaconda Python 2.7 zainstalowane we wszystkich węzłach. Istnieją dwie ważne czynności (opisanej w tym temacie) do wykonania w przypadku dostosowywania klastra usługi HDInsight.
   
   * Należy połączyć utworzony w kroku 1 z klastrem usługi HDInsight, po utworzeniu konta magazynu. To konto magazynu jest używane do uzyskiwania dostępu do danych, które mogą być przetwarzane w ramach klastra.
   * Należy włączyć dostęp zdalny do węzła głównego klastra po jego utworzeniu. Pamiętaj poświadczenia dostępu zdalnego określone w tym miejscu (inny niż określone dla klastra podczas jego tworzenia): potrzebne do wykonania poniższych procedur.
3. [Utwórz obszar roboczy usługi Azure ML](../studio/create-workspace.md): to Azure Machine Learning obszaru roboczego jest używany do tworzenia modeli uczenia maszyny po Eksploracja danych początkowych i w dół próbkowania w klastrze usługi HDInsight.

## <a name="getdata"></a>Pobierz i wykorzystują dane ze źródła publiczny
[Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) zestawu danych jest możliwy klikając łącze, akceptowania warunków użytkowania i podanie nazwy. Migawki to wygląda jak jest następujący:

![Zaakceptuj postanowienia Criteo](./media/hive-criteo-walkthrough/hLxfI2E.png)

Kliknij przycisk **Kontynuuj, aby pobrać** Aby dowiedzieć się więcej o zestawie danych i ich dostępność.

Dane znajdują się w folderze publicznym [magazynu obiektów blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) lokalizacji: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. "wasb" odwołuje się do lokalizacji magazynu obiektów Blob Azure. 

1. Dane w tym magazynie obiektów blob publicznego składa się z trzech podfoldery rozpakowane danych.
   
   1. Podfolder *raw/licznik/* zawiera pierwszy 21 dni dane — od dnia\_00 do dnia\_20
   2. Podfolder *raw/train/* składa się z jednego dnia danych, dzień\_21
   3. Podfolder *pierwotnych i testowanie/* składa się z dwóch dni danych, dzień\_22 i dzień\_23
2. Dla osób, które chcesz rozpocząć od danych pierwotnych gzip, są one również dostępne w folderze głównym *raw /* jako day_NN.gz, gdzie NN przechodzi od 00 do 23.

Informacje o innym podejściu do uzyskania dostępu, zapoznaj się i model te dane, które nie wymaga żadnych lokalne pliki do pobrania jest szczegółowo w dalszej części tego przewodnika po utworzymy tabele programu Hive.

## <a name="login"></a>Zaloguj się do headnode klastra
Aby zalogować się do headnode klastra, należy użyć [portalu Azure](https://ms.portal.azure.com) można znaleźć klastra. Kliknij ikonę Słoń HDInsight po lewej stronie, a następnie kliknij dwukrotnie nazwę klastra. Przejdź do **konfiguracji** , kliknij dwukrotnie ikonę POŁĄCZ na dole strony, a następnie wprowadź swoje poświadczenia dostępu zdalnego, po wyświetleniu monitu. Zostanie wyświetlone headnode klastra.

Poniżej przedstawiono typowe pierwszego logowania do headnode klastra wygląda następująco:

![Zaloguj się do klastra](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Po lewej stronie jest "Hadoop wiersza polecenia", czyli naszych najważniejszą metodą roboczą dla Eksploracja danych. Zwróć uwagę, adresów URL przydatne - "Hadoop Yarn stanu" i "Hadoop nazwa węzła". Adres URL stanu yarn przedstawia postęp zadania oraz adres URL węzła nazwa zwraca szczegółowe informacje dotyczące konfiguracji klastra.

Teraz są skonfigurowane i gotowe do rozpoczęcia pierwszej części tego przewodnika: Eksploracja danych przy użyciu Hive i przygotowanie danych do usługi Azure Machine Learning.

## <a name="hive-db-tables"></a> Utwórz gałąź bazy danych i tabel
Aby utworzyć tabele programu Hive dla naszych Criteo zestawu danych, otwórz ***wiersza polecenia platformy Hadoop*** na pulpicie węzła głównego i wprowadź katalog Hive przez wprowadzenie polecenia

    cd %hive_home%\bin

> [!NOTE]
> Uruchom wszystkie polecenia gałęzi w tym przewodniku z gałęzi bin / directory wiersza. Odpowiada on za wszelkie problemy ścieżki automatycznie. Można użyć warunków "Hive katalogu wiersza", "bin gałęzi / directory wiersza" i "wiersza polecenia usługi Hadoop" zamiennie.
> 
> [!NOTE]
> Aby wykonać wszelkie zapytania Hive, jeden zawsze Użyj następujących poleceń:
> 
> 

        cd %hive_home%\bin
        hive

Po wyświetleniu REPL gałąź z "hive >"Zaloguj się, po prostu wyciąć i wkleić ją wykonać kwerendę.

Poniższy kod tworzy bazę danych "criteo", a następnie generuje 4 tabel:

* *tabeli do wygenerowania liczby* oparty na dzień dni\_00 do dnia\_20,
* *tabeli jako zestawu danych train* oparty na dzień\_21, i
* dwa *tabel do użycia jako zestawy danych test* oparty na dzień\_22 i dzień\_23 odpowiednio.

Podziel na dwóch różnych tabelach zestawu danych testowych, ponieważ jeden z dni jest dniem wolnym od pracy. Celem jest ustalenie modelu można wykryć różnice między dni wolnych i nie święta od szybkości kliknięć.

Skrypt [próbki&#95;hive&#95;utworzyć&#95;criteo&#95;bazy danych&#95;i&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) są wyświetlane tutaj dla wygody:

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

Te tabele są zewnętrzne, więc możesz po prostu wskazać ich lokalizacji magazynu obiektów Blob Azure (wasb).

**Istnieją dwa sposoby wykonania zapytania Hive dowolny:**

1. **Przy użyciu wiersza polecenia REPL Hive**: pierwsza to wydać polecenie "hive" i skopiuj i Wklej zapytanie z wiersza polecenia REPL Hive. Aby to zrobić, należy wykonać:
   
        cd %hive_home%\bin
        hive
   
     Teraz w wierszu polecenia REPL, wycinanie i wklejanie zapytanie go uruchomi.
2. **Zapisywanie zapytań do pliku i wykonywania polecenia**: drugi to można zapisać zapytania do pliku .hql ([próbki&#95;hive&#95;utworzyć&#95;criteo&#95;bazy danych&#95;i&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) a następnie należy wydać następujące polecenie, aby wykonać zapytanie:
   
        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Potwierdź utworzenie bazy danych i tabeli
Następnie potwierdź utworzenie bazy danych przy użyciu następującego polecenia z gałęzi bin / directory wiersza:

        hive -e "show databases;"

Dzięki temu:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Jest to potwierdzenie utworzenia nowej bazy danych "criteo".

Aby wyświetlić tabele zostały utworzone, po prostu wydać polecenie tutaj z bin gałęzi / directory wiersza:

        hive -e "show tables in criteo;"

Następnie powinny być widoczne następujące dane wyjściowe:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="exploration"></a> Eksploracja danych w gałęzi
Teraz można przystąpić do eksploracji niektóre podstawowe dane w gałęzi. Rozpocznij od liczby przykłady w pociągu i przetestować tabel danych.

### <a name="number-of-train-examples"></a>Liczba train przykłady
Zawartość [próbki&#95;hive&#95;liczba&#95;uczenia&#95;tabeli&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) są wyświetlane tutaj:

        SELECT COUNT(*) FROM criteo.criteo_train;

Daje to:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Alternatywnie jedną może również wydać następujące polecenie z bin gałęzi / directory wiersza:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Wiele przykładów testu w dwóch zestawach testu
Teraz określić liczbę przykłady w dwóch zestawach testu. Zawartość [próbki&#95;hive&#95;liczba&#95;criteo&#95;test&#95;dzień&#95;22&#95;tabeli&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) są tutaj:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Daje to:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

W zwykły sposób, również może wywołać skrypt z gałęzi bin / directory monit przez wydanie polecenia:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Na koniec należy zbadać liczba przykłady testu w zestawu danych testowych na podstawie dnia\_23.

Polecenia w tym celu jest podobny do przedstawionego właśnie (odwoływać się do [próbki&#95;hive&#95;liczba&#95;criteo&#95;test&#95;dzień&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Dzięki temu:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Dystrybucji etykiety w zestawie danych pociągu
Rozkład etykiety w zestawie danych pociągu jest. Aby to sprawdzić, czy wyświetlać zawartość [próbki&#95;hive&#95;criteo&#95;etykiety&#95;dystrybucji&#95;uczenia&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Daje to dystrybucji etykiety:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Należy pamiętać, że odsetek dodatnią etykiety około 3.3% (zgodne z oryginalnego zestawu danych).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Dystrybucje Histogram niektóre liczbowe zmiennych w zestawie danych pociągu
Można użyć natywnego gałęzi "histogram\_liczbowa" funkcji, aby dowiedzieć się, jak wygląda rozkład zmiennych liczbowych. Poniżej przedstawiono zawartość [próbki&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

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

PENETRACJA widok - rozłożenie kombinacja w gałęzi służy do generowania danych wyjściowych przypominającego SQL zamiast standardowego listy. Należy pamiętać, że w tej tabeli, pierwsza kolumna odpowiada Centrum bin, a drugi częstotliwości bin.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Przybliżony percentylu niektóre liczbowe zmiennych w zestawie danych pociągu
Płynących ze zmiennymi liczbowych jest również obliczenia przybliżonej percentylu. Gałąź do natywnego "percentyl\_przybliżone" robi to firmie Microsoft. Zawartość [próbki&#95;hive&#95;criteo&#95;przybliżonej&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) są:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Daje to:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

Rozkład percentylu blisko związane zwykle do dystrybucji histogram żadnych liczbowa zmiennej.         

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Znajdź liczbę unikatowych wartości dla niektórych podzielone na kategorie kolumn w zestawie danych pociągu
Kontynuowanie Eksploracja danych, Znajdź, dla niektórych kolumn podzielone na kategorie, liczbę unikatowych wartości, które podejmują. Aby to zrobić, Pokaż zawartość [próbki&#95;hive&#95;criteo&#95;unikatowy&#95;wartości&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Daje to:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Należy pamiętać, że Col15 ma unikatowe wartości 19M! Przy użyciu prostego technik, takich jak "hot jeden kodowanie" do kodowania tych wymiarów wysokiej zmienne podzielone na kategorie nie jest możliwe. W szczególności wywołana metoda zaawansowanych, niezawodnych [uczenia z liczby](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) dla wydajne rozwiązania problemu tego problemu jest wyjaśniono i przedstawiono.

Na koniec sprawdź liczbę unikatowych wartości dla niektórych innych kategorii kolumn oraz. Zawartość [próbki&#95;hive&#95;criteo&#95;unikatowy&#95;wartości&#95;wiele&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) są:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Daje to:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Ponownie należy pamiętać, że z wyjątkiem Col20, innych kolumn ma wiele unikatowych wartości.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Wystąpienie wspólnej liczby par podzielone na kategorie zmiennych w zestawie danych pociągu

Liczby wystąpień wspólnej par podzielone na kategorie zmiennych jest również przydatne. Można to ustalić przy użyciu kodu w [próbki&#95;hive&#95;criteo&#95;sparowanego&#95;podzielone na kategorie&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Odwróć kolejność zliczeń według ich wystąpienia i Znajdź najwyżej 15 w takim przypadku. Dzięki temu nam:

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

## <a name="downsample"></a> Dół przykładowych zestawów danych dla usługi Azure Machine Learning
Posiadanie przedstawione zestawy danych i przedstawiono sposób wykonywania tego typu eksploracji zmiennych (w tym kombinacji), dół przykładowych zestawów danych, dzięki czemu mogą być wbudowane w modelach w usłudze Azure Machine Learning. Odwołania, który koncentruje się problem: podany zestaw atrybutów przykład (funkcja wartości z Col2 - Col40), prognozowania, jeśli Col1 jest 0 (nie kliknij) lub 1 (kliknij).

W dół przykładowych zestawów danych pociągu i testu % 1 oryginalny rozmiar, użyj funkcji macierzystej RAND() gałęzi. Skrypt dalej [próbki&#95;hive&#95;criteo&#95;próbkowanie&#95;uczenia&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) robi to train zestawu danych:

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

Skrypt [próbki&#95;hive&#95;criteo&#95;próbkowanie&#95;test&#95;dzień&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) zrobi to za dane testowe dzień\_22:

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


Na koniec skryptu [próbki&#95;hive&#95;criteo&#95;próbkowanie&#95;test&#95;dzień&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) zrobi to za dane testowe dzień\_23:

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

Dzięki temu można przystąpić do naszej dół train próbki i testów zestawów danych do tworzenia modeli w usłudze Azure Machine Learning.

Brak ważnych finalnych przed przejściem do usługi Azure Machine Learning, która dotyczy liczby tabeli. W następnej sekcji podrzędne Tabela liczba została szczegółowo opisana w niektórych.

## <a name="count"></a> Krótkie omówienie w tabeli liczby
Jak widać, kilku zmiennych podzielone na kategorie ma bardzo duże wymiary. W tym przewodnikiem zaawansowanych technik o nazwie [uczenia z liczby](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) do kodowania tych zmiennych w wydajne, są prezentowane niezawodny sposób. Więcej informacji na temat tej metody jest łączem.

[!NOTE]
>W tym przewodniku omówiono przede wszystkim za pomocą liczby tabel do produkcji compact reprezentacje wymiarów wysokiej funkcji podzielone na kategorie. To nie jest jedynym sposobem, aby zakodować podzielone na kategorie funkcji; Aby uzyskać więcej informacji na temat innych metod, można wyewidencjonować zainteresowanych użytkowników [co hot-encoding](http://en.wikipedia.org/wiki/One-hot) i [Tworzenie skrótu funkcji](http://en.wikipedia.org/wiki/Feature_hashing).
>

Tworzenia liczba tabel na danych o liczbie, należy użyć danych do folderu raw/liczby. W sekcji modelowania użytkowników przedstawiono sposób tworzenia tych tabel liczba kategorii funkcji od początku, lub też używać tabeli liczbę wstępnie skompilowanych dla ich eksploracji. W jaki sposób podczas "wstępnie skompilowany liczba tabel" są określane, możemy oznacza przy użyciu tabel liczba, które zostały opublikowane. Szczegółowe informacje na temat dostęp do tych tabel znajdują się w następnej sekcji.

## <a name="aml"></a> Tworzenie modelu przy użyciu usługi Azure Machine Learning
Nasz model budowania procesu w usłudze Azure Machine Learning obejmuje następujące kroki:

1. [Pobierz dane z tabele programu Hive w usłudze Azure Machine Learning](#step1)
2. [Tworzenie eksperymentu: czyszczenie danych i featurize z liczby tabel](#step2)
3. [Tworzenie, szkolenia i klasyfikacja modelu](#step3)
4. [Ocena modelu](#step4)
5. [Opublikuj model jako usługę sieci web](#step5)

Teraz można przystąpić do tworzenia modeli w usłudze Azure Machine Learning studio. Nasze dół próbki danych zostanie zapisany jako tabele programu Hive w klastrze. Użyj usługi Azure Machine Learning **i zaimportuj dane** modułu do tych danych do odczytu. Poświadczenia, aby uzyskać dostęp do konta magazynu tego klastra znajdują się w poniżej.

### <a name="step1"></a> Krok 1: Pobierz dane z tabel gałęzi do usługi Azure Machine Learning, za pomocą modułu importowanie danych i wybrać go do eksperymentu uczenia maszynowego
Najpierw wybrać **+ nowy** -> **EKSPERYMENTU** -> **pusty eksperyment**. Następnie z **wyszukiwania** pole u góry po lewej, wyszukaj "Import Data". Przeciągnij i upuść **i zaimportuj dane** modułu do eksperymentu kanwy (środkowej części ekranu), aby użyć modułu programu dla dostępu do danych.

Co to jest **i zaimportuj dane** wygląda jak podczas pobierania danych z tabeli Hive:

![Importuj dane pobiera dane](./media/hive-criteo-walkthrough/i3zRaoj.png)

Dla **i zaimportuj dane** modułu, wartości parametrów, które znajdują się na rysunku przedstawiono tylko sortowanie wymaga podania wartości. Poniżej przedstawiono pewne ogólne wskazówki na temat sposobu wypełniania zestawu parametrów dla **i zaimportuj dane** modułu.

1. Wybierz "Zapytania Hive" **źródła danych**
2. W **zapytanie Hive bazy danych** polu Wybierz proste * FROM < Twojej\_bazy danych\_name.your\_tabeli\_nazwy >-jest wystarczająca.
3. **Identyfikator URI serwera Hcatalog**: Jeśli klaster jest "abc", to po prostu: https://abc.azurehdinsight.net
4. **Nazwa konta użytkownika Hadoop**: nazwa użytkownika, wybierany w momencie uruchomienia urządzeń klastra. (Nie nazwa użytkownika dostępu zdalnego!)
5. **Hasło konta użytkownika Hadoop**: hasło dla nazwy użytkownika, wybierany w momencie uruchomienia urządzeń klastra. (Nie hasła dostępu zdalnego!)
6. **Lokalizacja danych wyjściowych**: wybierz polecenie "Azure"
7. **Nazwa konta magazynu Azure**: konta magazynu skojarzone z klastra
8. **Klucz konta magazynu Azure**: klucz konta magazynu skojarzone z klastrem.
9. **Nazwa kontenera Azure**: Jeśli nazwa klastra jest "abc", a następnie zazwyczaj jest to po prostu "abc",.

Raz **i zaimportuj dane** zakończeniu pobieranie danych (możesz zobaczyć zielony znacznik w Module), Zapisz te dane jako zestawu danych (o nazwie wybranych przez użytkownika). Co to wygląda następująco:

![Importuj dane zapisywanie danych](./media/hive-criteo-walkthrough/oxM73Np.png)

Kliknij prawym przyciskiem myszy portem wyjściowym **i zaimportuj dane** modułu. Takie działanie spowoduje wyświetlenie **Zapisz jako zestawu danych** opcji i **wizualizacja** opcji. **Wizualizacja** opcji, po kliknięciu wyświetla 100 wierszy danych, wraz z prawym panelu, który jest przydatne w przypadku niektórych podsumowania statystyk. Aby zapisać dane, po prostu zaznacz **Zapisz jako zestawu danych** i postępuj zgodnie z instrukcjami.

Aby wybrać zapisany zestaw danych do użycia w eksperymencie machine learning, Znajdź w zestawach danych przy użyciu **wyszukiwania** pole pokazano na poniższej ilustracji. Po prostu wpisz limit nazwa nadana częściowo się do niego dostęp, a następnie przeciągnij zestaw danych na panelu głównego zestawu danych. Upuszczenie go na panelu głównego zaznacza go do użycia w machine learning modelowania.

![Zestaw danych Drage na panel główny](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> W tym pociąg i zestawy danych testu. Ponadto należy użyć nazwy bazy danych i nazw tabel, których można użyć w tym celu. Wartości używanych na rysunku są przeznaczone wyłącznie dla ilustracji purposes.* *
> 
> 

### <a name="step2"></a> Krok 2: Tworzenie prostego eksperymentu w uczenie maszynowe Azure do prognozowania kliknięć / nie kliknięcia
Nasze eksperymentu uczenia Maszynowego Azure wygląda następująco:

![Machine Learning eksperymentu](./media/hive-criteo-walkthrough/xRpVfrY.png)

Teraz zbadać najważniejsze składniki tego eksperymentu. Przeciągnij naszych zapisane pociągu i przetestowanie zestawów danych do naszej kanwy eksperymentu.

#### <a name="clean-missing-data"></a>Brak danych
**Clean Missing Data** modułu jest sugeruje nazwa: go czyści brakujące dane w sposób, który może być określone przez użytkownika. Szukaj do tego modułu, aby wyświetlić to:

![Wyczyść brakujące dane](./media/hive-criteo-walkthrough/0ycXod6.png)

W tym miejscu postanowiono Zamień wszystkie brakujące wartości 0. Istnieją inne opcje zostaną również, które są wyświetlane analizując list rozwijanych w module.

#### <a name="feature-engineering-on-the-data"></a>Inżynieria na danych
Może istnieć miliony unikatowe wartości w przypadku niektórych funkcji podzielone na kategorie dużych zestawów danych. Przy użyciu metod prosty przykład hot jeden kodowanie reprezentujący takich funkcji podzielone na kategorie wymiarów wysokiej jest całkowicie będzie niemożliwe. W tym przewodniku pokazano, jak używać funkcji count przy użyciu wbudowanych modułów uczenia maszynowego Azure, aby wygenerować compact reprezentacje tych wymiarów wysokiej zmiennych podzielone na kategorie. Wynik końcowy jest mniejszy rozmiar modelu, szkolenia szybsze i metryki wydajności, które są dość porównywalna przy użyciu innych technik.

##### <a name="building-counting-transforms"></a>Kompilowanie zliczania transformacji
Aby utworzyć liczby funkcji, należy użyć **kompilacji zliczania przekształcenie** moduł, który jest dostępny w usłudze Azure Machine Learning. Moduł wygląda następująco:

![Utworzenie modułu zliczania przekształcenie](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![kompilacji zliczania przekształcenie modułu](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT] 
> W **liczba kolumn** wprowadź te kolumny, które chcesz wykonać na liczby. Zazwyczaj są to (jak wspomniano) wymiarów wysokiej kolumny podzielone na kategorie. Należy pamiętać, że element dataset Criteo zawiera kolumny podzielone na kategorie 26: z Col15 do Col40. W tym miejscu liczba na wszystkich z nich i nadaj ich indeksów (od 15 do 40 oddzielonych przecinkami, jak pokazano).
> 

Aby użyć modułu programu w trybie MapReduce (odpowiedni dla dużych zestawów danych), potrzebny jest dostęp do klastra usługi HDInsight Hadoop (używane do eksplorowania funkcji mogą być ponownie używane w tym celu również) i jego poświadczenia. Poprzednie ilustracjach jakie wypełniane wartości wygląda jak (Zastąp wartości ilustracyjną z tymi, które są istotne dla własnego przypadek użycia).

![Parametry modułu](./media/hive-criteo-walkthrough/05IqySf.png)

Powyższej ilustracji przedstawiono sposób wprowadź lokalizację blob danych wejściowych. Ta lokalizacja zawiera dane, zarezerwowane dla tworzenia liczbę tabel w.

Po zakończeniu tego modułu zapisać transformacji dla później przez kliknięcie prawym przyciskiem myszy modułu i wybierając **Zapisz jako transformacji** opcji:

![Opcję "Zapisz jako transformacji"](./media/hive-criteo-walkthrough/IcVgvHR.png)

W naszej architektury eksperymentu przedstawionych powyżej zestaw danych "ytransform2" odpowiada dokładnie transformacji liczba zapisanych. W pozostałej części tego eksperymentu, zakłada się, że czytnik używane **kompilacji zliczania przekształcenie** moduł na niektóre dane do generowania liczby i można następnie użyć tych liczby do generowania liczby funkcji na pociągu i testowania zestawów danych.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Wybieranie, jakie liczbę funkcji do uwzględnienia jako część pociągu i testowania zestawów danych
Raz gotowe przekształcenia liczba użytkownik może wybrać funkcje do uwzględnienia w ich train a testów, zestawów danych przy użyciu **zmodyfikować liczby parametrów tabeli** modułu. Aby informacje były kompletne ten moduł jest wyświetlany w tym miejscu. Jednak w celu uproszczenia nie faktycznie jest używana w naszym doświadczeniu.

![Modyfikowanie tabeli liczba parametrów](./media/hive-criteo-walkthrough/PfCHkVg.png)

W takim przypadku jak widać, którzy dziennika mają być używane, i wycofywania kolumny jest ignorowana. Można również ustawić parametry, takie jak próg bin odzyskiwanie, ile artykule wcześniejsze przykłady można dodać wygładzanie oraz określić, czy używać żadnych szumu Laplacian lub nie. Wszystkie te funkcje są zaawansowane i zauważyć, że wartości domyślne to dobry punkt wyjścia dla użytkowników, którzy dopiero zaczynasz korzystać z tego typu funkcji generowania.

##### <a name="data-transformation-before-generating-the-count-features"></a>Przekształcenia danych przed wygenerowaniem funkcji count
Teraz skoncentrować się na ważnych punktu o Przekształcanie naszych train i testowania danych przed wygenerowaniem faktycznie funkcji count. Należy pamiętać, że istnieją dwa **wykonanie skryptu języka R** moduły używane przed stosowana jest transformacja liczba naszych danych.

![Wykonanie skryptu języka R modułów](./media/hive-criteo-walkthrough/aF59wbc.png)

W tym miejscu jest pierwszym skrypt języka R:

![Pierwszy skrypt języka R](./media/hive-criteo-walkthrough/3hkIoMx.png)

Ten skrypt języka R zmienia nazwę naszych kolumn do nazwy "Col1" do "Col40". Jest to spowodowane przekształcenia liczba oczekuje nazwy w tym formacie.

Drugi skrypt języka R równoważy dystrybucji między klasami dodatnie i ujemne (klasy 1 i 0 odpowiednio) za pomocą klasy ujemne w dół — próbkowania. Skrypt języka R w tym miejscu pokazano, jak to zrobić:

![Drugi skrypt języka R](./media/hive-criteo-walkthrough/91wvcwN.png)

W tym prostego skryptu języka R "pos\_minus\_współczynnik" jest używana do ustawiania wielkość balansu między dodatnie i ujemne klasy. Jest to ważne, ponieważ ulepszanie nierównowaga klasy zwykle ma zwiększenia wydajności dla klasyfikacji problemów w przypadku dystrybucji klasy niesymetryczna (odwołanie w takim przypadku istnienia klasy dodatnią 3.3% i 96.7% ujemna klasy).

##### <a name="applying-the-count-transformation-on-our-data"></a>Stosowania przekształcenia liczba na naszych danych
Ponadto można użyć **Zastosuj przekształcenie** modułu do stosowania przekształceń liczba na naszych train i testowania zestawów danych. Ten moduł pobiera przekształcenia liczby zapisanej jako jedno wejście i pociągu lub testowym zestawy danych jako dane wejściowe i zwraca danych za pomocą funkcji count. Przedstawiono tutaj:

![Zastosuj przekształcenie modułu](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Fragment, jakie funkcje count wyglądać
Jest to istotne, aby zobaczyć, jak wyglądają liczba funkcji w tym przypadku. Poniżej przedstawiono fragment to:

![Liczba funkcji](./media/hive-criteo-walkthrough/FO1nNfw.png)

Ten fragment pokazuje, czy w kolumnach zliczane uzyskać liczby i prawdopodobieństwo oprócz wszelkie istotne backoffs dziennika.

Teraz można przystąpić do tworzenia modelu uczenia maszynowego Azure przy użyciu tych przekształcone zestawów danych. W następnej sekcji opisano, jak to zrobić.

### <a name="step3"></a> Krok 3: Tworzenie uczenia i klasyfikacja modelu

#### <a name="choice-of-learner"></a>Wybór uczeń
Najpierw musisz wybrać uczeń. Korzystanie z drzewa decyzji boosted dwuklasowych jako naszych uczeń. Poniżej przedstawiono wartości domyślne dla tego uczeń:

![Parametry Two-Class Boosted drzewa decyzyjnego](./media/hive-criteo-walkthrough/bH3ST2z.png)

Eksperymentu wybierz wartości domyślne. Należy pamiętać, że wartości domyślne są zazwyczaj przydatne i sposób uzyskać szybkie planów bazowych na wydajność. Na wydajność można poprawić przez profilach parametrów, jeśli użytkownik chce po utworzeniu linii bazowej.

#### <a name="train-the-model"></a>Uczenie modelu
Szkolenie, po prostu Wywołaj **Train Model** modułu. Dwóch danych wejściowych do niego są uczeń Two-Class Boosted drzewa decyzyjnego i naszym zestawie danych pociągu. To jest następujący:

![Train Model modułu](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Ocena modelu
Po uzyskaniu nauczonego modelu wszystko jest gotowe do wyniku na zestawu danych testowych i ocenę jego wydajności. W tym celu za pomocą **Score Model** modułu pokazano na poniższej ilustracji, wraz z **Evaluate Model** modułu:

![Moduł Score Model (Generowanie wyników przez model)](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step4"></a> Krok 4: Ocena modelu
Na koniec należy analizowanie wydajności modelu. W przypadku problemów klasyfikacji (binarnego) dwie klasy dobrej miary jest zazwyczaj, AUC. To wizualizować, Podłączanie **Score Model** moduł **Evaluate Model** modułu dla tego. Kliknięcie przycisku **Visualize** na **Evaluate Model** modułu daje grafiki podobny do następującego:

![Ocena modelu BDT modułu](./media/hive-criteo-walkthrough/0Tl0cdg.png)

W danych binarnych (lub dwie klasy) klasyfikacji problemów, dobrym miara dokładności prognozy jest obszar w krzywej (AUC). W tej części opisano naszych wyników przy użyciu tego modelu w naszym zestawu danych testowych. Aby uzyskać dostęp do tej, kliknij prawym przyciskiem myszy portem wyjściowym **Evaluate Model** modułu, a następnie **Visualize**.

![Wizualizuj modułu Evaluate Model](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step5"></a> Krok 5: Opublikuj model jako usługę sieci Web
Umożliwia publikowanie modelu uczenia maszynowego Azure jako usługi sieci web z co najmniej problemów jest przydatna funkcja dokonywania szeroko dostępne. Po zakończeniu tej operacji, każda osoba, która wykonywania wywołań do usługi sieci web z danych wejściowych muszą prognoz dotyczących, czy usługa sieci web używa modelu do zwrócenia tych prognoz.

W tym celu należy najpierw zapisać uczonego modelu obiektu Trenowanego modelu. Jest to realizowane przez kliknięcie prawym przyciskiem myszy **Train Model** modułu i przy użyciu **Zapisz jako Uczonego modelu** opcji.

Następnie tworzenie danych wejściowych i wyjściowych portów dla naszej usługi sieci web:

* port wejściowy pobiera dane w tym samym formularzu jako dane, które należy prognoz dotyczących
* port wyjściowy zwraca etykiety oceniane i prawdopodobieństwa.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Wybierz kilka wierszy danych dla portu wejściowego
Jest łatwe w użyciu **Zastosuj przekształcenie SQL** moduł wybrać tylko 10 wierszy jako danych wejściowych portów. Wybierz tylko te wiersze danych dla naszych portu wejściowego przy użyciu zapytania SQL, pokazano poniżej:

![Port wejściowy danych](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Usługa sieci Web
Teraz można przystąpić do uruchomienia małych eksperymentu, która może być używana do publikowania naszej usługi sieci web.

#### <a name="generate-input-data-for-webservice"></a>Generuj dane wejściowe dla usługi sieci Web
Krokiem zeroth ponieważ tabeli count jest duży, wykonać kilka wierszy danych testowych i generowanie danych wyjściowych z jej z funkcji count. Może to służyć jako formatu danych wejściowych naszej usługi sieci Web. To jest następujący:

![Utwórz BDT danych wejściowych](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Format danych wejściowych, można użyć w danych wyjściowych **Featurizer liczba** modułu. Po zakończeniu uruchamiania to eksperymentu, Zapisz dane wyjściowe z **Featurizer liczba** modułu jako zestawu danych. Ten zestaw danych jest używany dla danych wejściowych w usługi sieci Web.
> 
> 

#### <a name="scoring-experiment-for-publishing-webservice"></a>Ocenianie eksperymentu publikowania usługi sieci Web
Po pierwsze jest wyświetlany, to wygląda następująco. Struktura niezbędne jest **Score Model** moduł, który akceptuje naszych obiekt uczonego modelu i kilka wierszy danych wejściowych, które zostały wygenerowane w poprzednich krokach za pomocą **Featurizer liczba** modułu. Użyj "Wybieranie kolumn w zestawie danych" do projektu etykiety Scored i wynik prawdopodobieństwa.

![Wybieranie kolumn w zestawie danych](./media/hive-criteo-walkthrough/kRHrIbe.png)

Powiadomienie jak **Select Columns in Dataset** modułu może służyć do "filtrowanie" danych z zestawu danych. Zawartość przedstawiono poniżej:

![Filtrowanie z Select Columns in Dataset modułu](./media/hive-criteo-walkthrough/oVUJC9K.png)

Aby uzyskać niebieski danych wejściowych i wyjściowych portów, po prostu kliknij **przygotowanie webservice** w prawym dolnym rogu. Uruchomienie tego eksperymentu również pozwoli nam opublikować usługi sieci web: kliknij **OPUBLIKOWAĆ usługi sieci WEB** ikonę u dołu tutaj prawym, wyświetlane:

![Publikowanie usługi sieci Web](./media/hive-criteo-walkthrough/WO0nens.png)

Po opublikowaniu usługi sieci Web, przekierowanie do strony, która wygląda w ten sposób:

![Pulpit nawigacyjny usługi sieci Web](./media/hive-criteo-walkthrough/YKzxAA5.png)

Zwróć uwagę dwa łącza do usług sieci Web po lewej stronie:

* **Żądanie/odpowiedź** usługi (lub RR) jest przeznaczony dla jednego prognoz i co została wykorzystana w tym workshop.
* **BATCH EXECUTION** usługi (BES) służy do przewidywania partii i wymaga danych wejściowych w celu tworzenia prognoz, które znajdują się w magazynie obiektów Blob Azure.

Kliknięcie łącza **żądanie/odpowiedź** przyjmuje nam stronę, która udostępnia wstępnie puszkach kodu w języku C#, python i R. Ten kod może wygodnie służyć do wykonywania wywołań do usługi sieci Web. Należy pamiętać, że klucz interfejsu API na tej stronie musi być używane do uwierzytelniania.

Jest wygodną kopiować ten kod języka python w komórce nowego notesu IPython.

Oto segment kodu python przy użyciu poprawnego klucza interfejsu API.

![Kod języka Python](./media/hive-criteo-walkthrough/f8N4L4g.png)

Należy pamiętać, że klucz interfejsu API naszych usług sieci Web został zastąpiony domyślny klucz interfejsu API. Kliknięcie przycisku **Uruchom** w tej komórce w IPython notesu daje następującą odpowiedź:

![IPython odpowiedzi](./media/hive-criteo-walkthrough/KSxmia2.png)

Dla obu test przykłady pytania (w ramach JSON skrypt w języku python), możesz wrócić odpowiedzi w formie "Scored Labels Scored Probabilities". W tym przypadku wartości domyślne zostały wybrane czy wstępnie zdefiniowanym kod stanowi (0 dla wszystkich kolumn liczbowych i ciąg "value" dla wszystkich kolumn podzielone na kategorie).

Zakończenie nasze wskazówki przedstawiający sposób obsługi na dużą skalę zestawu danych za pomocą usługi Azure Machine Learning. Wprowadzenie terabajtów danych, utworzyć modelu prognozy i wdrożyć go jako usługę sieci web w chmurze.

