---
title: Korzystanie z klastra Azure HDInsight Hadoop w 1 TB zestawu danych — proces nauki danych zespołu
description: Korzystanie z procesu nauki danych zespołu do kompleksowego scenariusza wykorzystującego klaster usługi HDInsight Hadoop do kompilowania i wdrażania modelu przy użyciu dużego dostępnego publicznie zestawu danych (1 TB)
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
ms.openlocfilehash: eca19b3774ad285cb143ffc2b6c53360bec85fa4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492345"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Proces nauki danych zespołu w akcji — Używanie klastra Azure HDInsight Hadoop w zestawie danych 1 TB

W tym instruktażu pokazano, jak używać procesu nauki danych zespołu w kompleksowym scenariuszu z [klastrem Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) , który umożliwia przechowywanie, eksplorowanie i Tworzenie przykładowych danych z jednego z dostępnych publicznie zestawów [dataCriteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) . Używa Azure Machine Learning, aby utworzyć binarny model klasyfikacji dla tych danych. Przedstawiono w nim również sposób publikowania jednego z tych modeli jako usługi sieci Web.

Istnieje również możliwość użycia notesu IPython do wykonywania zadań przedstawionych w tym instruktażu. Użytkownicy, którzy chcą skorzystać z tej metody, powinni zapoznać się z [przewodnikiem Criteo przy użyciu połączenia ODBC Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) .

## <a name="dataset"></a>Opis zestawu danych Criteo
Dane Criteo to zestaw danych przewidywania kliknij, który jest około 370GB skompresowanych plików TSV (~ 1.3 TB nieskompresowanych), składający się z ponad 4 300 000 000 rekordów. Jest ona pobierana z 24-dniowego danych, które zostały udostępnione przez [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/). Dla wygody analityków danych dane dostępne dla nas do eksperymentowania z programem zostały rozpakowane.

Każdy rekord w tym zestawie danych zawiera 40 kolumn:

* Pierwsza kolumna jest kolumną etykiety, która wskazuje, czy użytkownik klika przycisk **Dodaj** (wartość 1) lub nie klika (wartość 0)
* kolejne 13 kolumn są wartościami liczbowymi i
* Ostatnie 26 są kolumnami kategorii

Kolumny są anonimowe i używają serii wyliczeniowych nazw: "Kol1" (dla kolumny etykieta) do "Col40" (dla ostatniej kolumny kategorii).

Poniżej znajduje się fragment pierwszych 20 kolumn dwóch obserwacji (wierszy) z tego zestawu danych:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb

Brak wartości w kolumnach liczbowych i kategorii w tym zestawie danych. Opisana jest prosta metoda obsługi brakujących wartości. Dodatkowe szczegóły danych są zbadane podczas zapisywania ich w tabelach programu Hive.

**Definicja:** *stawka kliknięć:* wartość procentowa kliknięć w danych. W tym Criteo zestawie danych, Rob ma około 3,3% lub 0,033.

## <a name="mltasks"></a>Przykłady zadań przewidywania
W tym instruktażu przedstawiono dwa przykładowe problemy z prognozą:

1. **Klasyfikacja binarna**: przewidywanie, czy użytkownik kliknął dodanie:

   * Klasa 0: nie klikaj
   * Klasa 1: kliknij przycisk
2. **Regresja**: przewiduje prawdopodobieństwo kliknięcia usługi AD przez funkcję użytkownika.

## <a name="setup"></a>Konfigurowanie klastra usługi HDInsight Hadoop na potrzeby analizy danych
**Uwaga:** Jest to zazwyczaj zadanie **administracyjne** .

Skonfiguruj środowisko nauki danych platformy Azure do tworzenia rozwiązań analizy predykcyjnej z klastrami usługi HDInsight w trzech krokach:

1. [Utwórz konto magazynu](../../storage/common/storage-quickstart-create-account.md): to konto magazynu służy do przechowywania danych w usłudze Azure Blob Storage. Dane używane w klastrach usługi HDInsight są przechowywane w tym miejscu.
2. [Dostosowywanie klastrów Azure HDInsight Hadoop do analizy danych](customize-hadoop-cluster.md): w tym kroku jest tworzony klaster Azure HDInsight Hadoop z 64-bitową Anaconda Python 2,7 zainstalowany na wszystkich węzłach. W przypadku dostosowywania klastra usługi HDInsight należy wykonać dwa ważne kroki (opisane w tym temacie).

   * Podczas tworzenia należy połączyć konto magazynu utworzone w kroku 1 z klastrem usługi HDInsight. To konto magazynu służy do uzyskiwania dostępu do danych, które mogą być przetwarzane w klastrze.
   * Należy włączyć dostęp zdalny do węzła głównego klastra po jego utworzeniu. Zapamiętaj poświadczenia dostępu zdalnego określone w tym miejscu (inne niż określone dla klastra podczas jego tworzenia): należy wykonać następujące procedury.
3. [Utwórz obszar roboczy Azure Machine Learning Studio (klasyczny)](../studio/create-workspace.md): ten Azure Machine Learning obszar roboczy jest używany do kompilowania modeli uczenia maszynowego po początkowej eksploracji danych i pobraniu próbek w klastrze usługi HDInsight.

## <a name="getdata"></a>Pobieranie danych z publicznego źródła i korzystanie z nich
Dostęp do zestawu danych [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) można uzyskać, klikając link, akceptując warunki użytkowania i podając nazwę. Migawka tego wyglądu wygląda następująco:

![Zaakceptuj warunki Criteo](./media/hive-criteo-walkthrough/hLxfI2E.png)

Kliknij przycisk **Kontynuuj, aby pobrać** , aby dowiedzieć się więcej na temat zestawu danych i jego dostępności.

Dane znajdują się w publicznej lokalizacji [magazynu obiektów blob platformy Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) : wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. "Wasb" odwołuje się do lokalizacji Blob Storage platformy Azure.

1. Dane w tym publicznym magazynie obiektów BLOB składają się z trzech podfolderów niespakowanego danych.

   1. Podfolder *RAW/Count/* zawiera pierwsze 21 dni danych — od dnia\_od 00 do dnia\_20
   2. Podfolder *RAW/pociąg/* składa się z pojedynczego dnia danych, dzień\_21
   3. Podfolder *RAW/test/* składa się z dwóch dni danych, dzień\_22 i dzień\_23
2. Dla osób, które chcą zacząć od danych z surowego strumienia gzip, są one również dostępne w folderze głównym *RAW/* as day_NN. gz, gdzie nn ma wartość od 00 do 23.

Alternatywna metoda uzyskiwania dostępu do tych danych, eksplorowania ich i modelowania, które nie wymagają pobrania plików lokalnych, została omówiona w dalszej części tego instruktażu podczas tworzenia tabel programu Hive.

## <a name="login"></a>Logowanie do węzła głównego klastra
Aby zalogować się do węzła głównego klastra, użyj [Azure Portal](https://ms.portal.azure.com) , aby zlokalizować klaster. Kliknij ikonę HDInsight słoni po lewej stronie, a następnie dwukrotnie kliknij nazwę klastra. Przejdź do karty **Konfiguracja** , kliknij dwukrotnie ikonę Połącz w dolnej części strony, a następnie wprowadź swoje poświadczenia dostępu zdalnego po wyświetleniu monitu. Spowoduje to przejście do węzła głównego klastra.

Oto typowe Pierwsze logowanie do klastra węzła głównego wygląda następująco:

![Logowanie do klastra](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Po lewej stronie jest "wiersz polecenia usługi Hadoop", który jest nasz WorkHorse do eksploracji danych. Zwróć uwagę na dwa przydatne adresy URL — "stan przędzy usługi Hadoop" i "węzeł nazwy usługi Hadoop". Adres URL stanu przędzenia pokazuje postęp zadania, a adres URL węzła nazwy zawiera szczegółowe informacje o konfiguracji klastra.

Teraz wszystko jest gotowe do rozpoczęcia pierwszej części przewodnika: Eksploracja danych przy użyciu usługi Hive i przygotowywania danych do Azure Machine Learning.

## <a name="hive-db-tables"></a>Tworzenie bazy danych i tabel programu Hive
Aby utworzyć tabele Hive dla naszego zestawu danych Criteo, Otwórz ***wiersz polecenia Hadoop*** na pulpicie węzła głównego, a następnie wprowadź katalog Hive, wprowadzając polecenie

    cd %hive_home%\bin

> [!NOTE]
> Uruchom wszystkie polecenia Hive w tym instruktażu w wierszu bin/Directory programu Hive. Obejmuje to automatyczne rozwiązywanie problemów ze ścieżką. Możesz użyć warunków "wiersz usługi Hive", "wiersz polecenia" w katalogu Hive "i" wiersz poleceń usługi Hadoop "w sposób wymienny.
>
> [!NOTE]
> Aby wykonać dowolne zapytanie programu Hive, można zawsze używać następujących poleceń:
>
>

        cd %hive_home%\bin
        hive

Gdy REPL Hive zostanie wyświetlony ze znakiem "Hive >", po prostu Wytnij i wklej zapytanie, aby je uruchomić.

Poniższy kod tworzy bazę danych "Criteo", a następnie generuje 4 tabele:

* *tabela służąca do generowania liczników* utworzonych na dzień dnia\_od 00 do dnia\_20,
* tabela, która *ma być używana jako zestaw danych do uczenia* w dniu\_21 i
* dwie *tabele do użycia jako zestawy danych testowych* skompilowane codziennie\_22 i dzień\_23.

Podziel zestaw danych testu na dwie różne tabele, ponieważ jeden z dni jest świętem. Celem jest określenie, czy model może wykrywać różnice między dniami wolnymi i nieświątecznymi od stawki kliknięcia.

[&#95;Przykładowy skrypt Hive&#95;tworzy&#95;bazę danych&#95;&#95;Criteo i&#95;tabele. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) jest tutaj wyświetlana dla wygody:

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

Wszystkie te tabele są zewnętrzne, dzięki czemu możesz po prostu wskazać ich lokalizacje usługi Azure Blob Storage (wasb).

**Istnieją dwa sposoby wykonywania zapytania programu Hive:**

1. **Za pomocą wiersza polecenia programu Hive REPL**: Pierwszy polega na wystawieniu polecenia "Hive" i skopiowaniu i wklejeniu zapytania w wierszu polecenia programu Hive REPL. W tym celu wykonaj następujące czynności:

        cd %hive_home%\bin
        hive

     Teraz w wierszu polecenia REPL wycinanie i wklejanie zapytania jest wykonywane.
2. **Zapisywanie zapytań do pliku i wykonywanie polecenia**: drugi polega na zapisaniu zapytań w pliku. HQL ([&#95;przykład&#95;Hive Create&#95;Criteo&#95;Database&#95;i&#95;Tables. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)), a następnie wydać następujące polecenie polecenie do wykonania zapytania:

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Potwierdzenie utworzenia bazy danych i tabeli
Następnie potwierdź Tworzenie bazy danych za pomocą następującego polecenia w wierszu bin lub w katalogu Hive:

        hive -e "show databases;"

Zapewnia to:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Spowoduje to potwierdzenie utworzenia nowej bazy danych "Criteo".

Aby zobaczyć, które tabele zostały utworzone, wystarczy wydać polecenie w tym miejscu w wierszu bin/Directory programu Hive:

        hive -e "show tables in criteo;"

Następnie powinny pojawić się następujące dane wyjściowe:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="exploration"></a>Eksploracja danych w usłudze Hive
Teraz wszystko jest gotowe do wykonania pewnej podstawowej eksploracji danych w usłudze Hive. Zacznij od policzania przykładów w tabelach uczenie i dane testowe.

### <a name="number-of-train-examples"></a>Liczba przykładów pouczenia
Poniżej przedstawiono zawartość [&#95;przykładu&#95;&#95;&#95;pouczenia&#95;się tabeli. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) przedstawiono tutaj:

        SELECT COUNT(*) FROM criteo.criteo_train;

To daje:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Alternatywnie, jeden może również wydać następujące polecenie w wierszu bin lub w katalogu Hive:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Liczba przykładów testowych w dwóch zestawach testów
Teraz Policz przykłady w dwóch testowych zestawach danych. Zawartość [przykładowej&#95;liczby&#95;&#95;gałęzi Criteo&#95;w&#95;dniu&#95;22&#95;przykłady tabeli&#95;. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) są następujące:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

To daje:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Jak zwykle, można również wywołać skrypt w wierszu bin/Directory programu Hive, wydając polecenie:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Na koniec należy sprawdzić liczbę przykładów testowych w zestawie danych testowych na podstawie dnia\_23.

To polecenie jest podobne do podanego w tym przypadku (zobacz [Przykładowa&#95;&#95;liczba&#95;gałęzi Criteo&#95;&#95;&#95;23&#95;przykłady. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Zapewnia to:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Dystrybucja etykiet w zestawie danych pociągu
Rozkład etykiet w zestawie danych uczenia jest interesujący. Aby to zobaczyć, Pokaż zawartość [przykładowej&#95;gałęzi&#95;Criteo&#95;&#95;&#95;&#95;](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql)retailion retails Table. HQL:

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Daje to rozkład etykiet:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Należy zauważyć, że wartość procentowa etykiet dodatnich jest około 3,3% (spójna z oryginalnym zestawem danych).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Rozkłady histogramów niektórych zmiennych liczbowych w zestawie danych pociągu
Możesz użyć natywnej funkcji "histogram\_liczbowej", aby dowiedzieć się, jak wygląda rozkład zmiennych liczbowych. Poniżej znajdują się zawartości [&#95;przykładowej&#95;Criteo&#95;histogramu&#95;numeric. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Daje to następujące kwestie:

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

Połączenie POPRZECZNe — rozłożenie w programie Hive służy do generowania danych wyjściowych przypominających kod SQL zamiast zwykłej listy. Zwróć uwagę, że w tej tabeli pierwsza kolumna odpowiada centrum bin, a druga — częstotliwość pojemnika.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Przybliżone percentyle niektórych zmiennych liczbowych w zestawie danych pociągu
Również znaczenie dla zmiennych liczbowych to obliczenie przybliżonych percentylów. Natywne "percentyle"\_w przybliżeniu "robi to dla nas. Zawartość [&#95;przykładowej gałęzi&#95;Criteo&#95;przybliżone&#95;percentyle. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) :

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

To daje:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

Rozkład percentylów jest ściśle powiązany z rozkładem histogramu dowolnej zmiennej liczbowej.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Znajdź liczbę unikatowych wartości dla niektórych kolumn kategorii w zestawie danych uczenia
Kontynuuj eksplorację danych, Znajdź, w przypadku niektórych kolumn kategorii, liczbę unikatowych wartości, które przyjmują. Aby to zrobić, Pokaż zawartość [przykładowej&#95;gałęzi&#95;Criteo&#95;&#95;unikatowych&#95;wartości categoricals. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

To daje:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Należy pamiętać, że Col15 ma 19M unikatowe wartości! Użycie technik algorytmie, takich jak "jednostronicowe kodowanie", aby kodować takie zmienne kategorii, które nie są możliwe. W szczególności zapoznaj się ze szczegółowo zaawansowaną, niezawodną techniką o nazwie [uczenie się z licznami](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) służącymi do efektywnego rozwiązywania tego problemu.

Na koniec sprawdź liczbę unikatowych wartości dla niektórych innych kategorii kolumn. Zawartość [&#95;przykładowej gałęzi&#95;Criteo&#95;unikatowych&#95;wartości&#95;wiele&#95;categoricals. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) są następujące:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

To daje:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Należy pamiętać, że z wyjątkiem Col20, wszystkie inne kolumny mają wiele unikatowych wartości.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Liczba współwystąpień zmiennych kategorii w zestawie danych uczenia

Te same liczby wystąpień zmiennych kategorii są również istotne. Można to określić przy użyciu kodu w [przykładowej&#95;gałęzi&#95;Criteo&#95;sparowanych&#95;kategorii&#95;Counts. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Odwróć liczbę według ich wystąpienia i spójrz na pierwsze 15 w tym przypadku. Daje to nam następujące uwagi:

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

## <a name="downsample"></a>Przykładowe zestawy danych dla Azure Machine Learning
Zapoznanie się z zestawami danych i przedstawienie sposobu tego typu eksploracji dla wszelkich zmiennych (w tym kombinacji), a następnie w dół, aby umożliwić skompilowanie modeli w Azure Machine Learning. Należy odwołać się, aby skoncentrować się na problemie: nadano zestaw przykładowych atrybutów (wartości funkcji z Col2-Col40), przewidywanie, jeśli Kol1 jest 0 (nie kliknięcia) lub 1 (kliknij).

Aby w dół próbkować i testować zestawy danych do 1% oryginalnego rozmiaru, użyj natywnej funkcji RAND (). Następny skrypt, [przykładowy&#95;&#95;criteoa&#95;próbkowanie&#95;w dół&#95;zestawu danych. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) to dla zestawu danych szkolenia:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

To daje:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

[Przykładowy&#95;skrypt Hive&#95;Criteo&#95;próbkowanie w&#95;ciągu&#95;dnia&#95;testowego 22&#95;zestaw danych. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) robi to dla danych testowych, dzień\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

To daje:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Na koniec [&#95;przykładowy skrypt Hive&#95;Criteo&#95;próbkowanie w&#95;ciągu&#95;dnia&#95;testowego 23&#95;zestaw danych. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) wykonuje go dla danych testowych, dzień\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

To daje:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Dzięki temu możesz przystąpić do użycia naszego przykładowego szkolenia i testów zestawów danych do kompilowania modeli w Azure Machine Learning.

Przed przejściem do Azure Machine Learning należy uzyskać końcowy składnik, który dotyczy tabeli Count. W następnej podsekcji tabeli Count opisano szczegółowo.

## <a name="count"></a>Krótka dyskusja w tabeli Count
Po wykorzystaniu kilku zmiennych kategorii ma bardzo wysokie wymiary. W przewodniku zaprezentowano zaawansowaną technikę o nazwie [uczenie się z licznikami](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) , aby zakodować te zmienne w efektywny, niezawodny sposób. Więcej informacji na temat tej techniki znajduje się w podanym łączu.

[!NOTE]
>W tym instruktażu koncentruje się na korzystaniu z tabel Count, aby generować kompaktowe reprezentacje funkcji kategorii. Nie jest to jedyna metoda kodowania funkcji kategorii. Aby uzyskać więcej informacji na temat innych technik, zainteresowani użytkownicy mogą wyewidencjonować [jednostronicowe kodowanie](https://en.wikipedia.org/wiki/One-hot) i [funkcję tworzenia skrótów](https://en.wikipedia.org/wiki/Feature_hashing).
>

Aby skompilować liczbę tabel z danymi liczbowymi, użyj danych w folderze RAW/Count. W sekcji modelowanie użytkownicy są pokazani, jak kompilować te tabele liczb dla funkcji kategorii od podstaw, lub też użyć wstępnie skompilowanej tabeli Count dla swoich eksploracji. W poniższych tematach, gdy "wstępnie skompilowane tabele zliczania" są określane przy użyciu dostarczonych tabel Count. Szczegółowe instrukcje dotyczące uzyskiwania dostępu do tych tabel znajdują się w następnej sekcji.

## <a name="aml"></a>Kompilowanie modelu przy użyciu Azure Machine Learning
Nasz proces kompilowania modelu w Azure Machine Learning wykonuje następujące czynności:

1. [Pobierz dane z tabel programu Hive do Azure Machine Learning](#step1)
2. [Utwórz eksperyment: Wyczyść dane i uczyń je funkcją z tabelami liczników](#step2)
3. [Kompilowanie, uczenie i Ocena modelu](#step3)
4. [Oceń model](#step4)
5. [Publikowanie modelu jako usługi sieci Web](#step5)

Teraz możesz przystąpić do kompilowania modeli w programie Azure Machine Learning Studio. Nasze przykładowe dane są zapisywane jako tabele Hive w klastrze. Użyj modułu Azure Machine Learning **Importuj dane** , aby odczytać te dane. Poświadczenia dostępu do konta magazynu tego klastra są podane w poniższych tematach.

### <a name="step1"></a>Krok 1. Pobieranie danych z tabel programu Hive do Azure Machine Learning przy użyciu modułu Importuj dane i wybieranie go dla eksperymentu uczenia maszynowego
Zacznij od wybrania programu **+ NEW** -> **eksperyment** -> **pustego eksperymentu**. Następnie w polu **wyszukiwania** w lewym górnym rogu Wyszukaj pozycję "Importuj dane". Przeciągnij i upuść moduł **Importuj dane** na kanwę eksperymentu (środkową część ekranu), aby użyć modułu do uzyskiwania dostępu do danych.

Oto, jak wyglądają **dane importu** podczas pobierania danych z tabeli programu Hive:

![Importuj dane pobierają dane](./media/hive-criteo-walkthrough/i3zRaoj.png)

W przypadku modułu **Import danych** wartości parametrów, które są podane w grafice, to tylko przykłady sortowania wartości, które należy podać. Poniżej przedstawiono ogólne wskazówki dotyczące sposobu wypełniania zestawu parametrów dla modułu **Importuj dane** .

1. Wybierz pozycję "zapytanie Hive" dla **źródła danych**
2. W polu **zapytania bazy danych programu Hive** proste wybieranie * z <\_bazy danych\_.\_tabelę\_nazwa >-jest wystarczająca.
3. **Identyfikator URI serwera Hcatalog**: Jeśli klaster ma wartość "ABC", to po prostu: https://abc.azurehdinsight.net
4. **Nazwa konta użytkownika usługi Hadoop**: Nazwa użytkownika wybrana w momencie wypróbowania klastra. (Nie jest to nazwa użytkownika dostępu zdalnego!)
5. **Hasło konta użytkownika usługi Hadoop**: hasło dla nazwy użytkownika wybranej podczas pracy z klastrem. (Nie jest to hasło dostępu zdalnego!)
6. **Lokalizacja danych wyjściowych**: wybierz pozycję "Azure"
7. **Nazwa konta usługi Azure Storage**: konto magazynu skojarzone z klastrem
8. **Klucz konta usługi Azure Storage**: klucz konta magazynu skojarzonego z klastrem.
9. **Nazwa kontenera platformy Azure**: Jeśli nazwa klastra to "ABC", jest to po prostu "ABC", zazwyczaj.

Gdy **Importowanie danych** zakończy pobieranie danych (zielony znacznik w module), Zapisz te dane jako zestaw danych (z wybraną nazwą). Co wygląda następująco:

![Importuj dane zapisywania danych](./media/hive-criteo-walkthrough/oxM73Np.png)

Kliknij prawym przyciskiem myszy port wyjściowy modułu **Importuj dane** . Spowoduje to wyświetlenie opcji **Zapisz jako zestaw danych** i opcji **wizualizacji** . Opcja **Wizualizacja** , jeśli została kliknięta, wyświetla 100 wierszy danych wraz z prawym panelem, który jest przydatny w przypadku niektórych statystyk podsumowujących. Aby zapisać dane, po prostu wybierz pozycję **Zapisz jako zestaw danych** i postępuj zgodnie z instrukcjami.

Aby wybrać zapisany zestaw danych do użycia w doświadczeniu uczenia maszynowego, Znajdź zestawy danych przy użyciu pola **wyszukiwania** pokazanego na poniższej ilustracji. Następnie po prostu wpisz nazwę zestawu danych, aby uzyskać do niego dostęp, i przeciągnij zestaw danych na panel główny. Porzucenie go do panelu głównego wybiera do użycia w modelu uczenia maszynowego.

![Przeciągnij zestaw danych na panel główny](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Wykonaj tę czynność dla zestawów danych pociągowych i testowych. Należy również pamiętać, aby użyć nazwy bazy danych i nazw tabel, które zostały nadaną do tego celu. Wartości używane na rysunku są przeznaczone wyłącznie do celów ilustracyjnych. * *
>
>

### <a name="step2"></a>Krok 2. Tworzenie prostego eksperymentu w Azure Machine Learning do przewidywania kliknięć/kliknięcia
Nasz Azure Machine Learning Studio (klasyczny) wygląda następująco:

![Machine Learning eksperyment](./media/hive-criteo-walkthrough/xRpVfrY.png)

Teraz sprawdź najważniejsze składniki tego eksperymentu. Najpierw przeciągnij nasze zapisane szkolenia i testowe zestawy danych na naszą kanwę eksperymentu.

#### <a name="clean-missing-data"></a>Czyszczenie brakujących danych
W **czystym module nieobecnych danych** jest sugerowana Nazwa: program czyści brakujące dane w sposób, który może być określony przez użytkownika. Przejdź do tego modułu, aby zobaczyć:

![Wyczyść brakujące dane](./media/hive-criteo-walkthrough/0ycXod6.png)

W tym miejscu wybrano, aby zastąpić wszystkie brakujące wartości wartością 0. Dostępne są również inne opcje, które można zobaczyć, przeglądając listę rozwijaną w module.

#### <a name="feature-engineering-on-the-data"></a>Inżynieria funkcji na danych
Niektóre funkcje kategorii dużych zestawów danych mogą mieć miliony unikatowych wartości. Korzystanie z metod algorytmie, takich jak jednostronicowe kodowanie do reprezentowania takich wysoko wielowymiarowych funkcji kategorii, jest całkowicie niewykonalne. W tym instruktażu pokazano, jak używać funkcji count przy użyciu wbudowanych modułów Azure Machine Learning, aby generować kompaktowe reprezentacje tych zmiennych kategorii. Wynikiem końcowym jest mniejszy rozmiar modelu, krótszy czas uczenia i metryki wydajności, które są znacznie porównywalne z wykorzystaniem innych technik.

##### <a name="building-counting-transforms"></a>Kompilowanie transformacji inwentaryzacyjnych
Aby kompilować funkcje zliczania, należy użyć modułu **transformacji zliczania kompilacji** , który jest dostępny w Azure Machine Learning. Moduł wygląda następująco:

![właściwości modułu przekształcenia obliczeń](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![tworzenia modułu przekształcenia obliczeń](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> W polu **Liczba kolumn** wprowadź te kolumny, dla których chcesz wykonywać obliczenia. Zazwyczaj są to (jak wspomniane) kolumny kategorii. Należy pamiętać, że zestaw danych Criteo ma 26 kategorii kolumn: od Col15 do Col40. W tym miejscu Policz wszystkie z nich i przekaż ich indeksy (od 15 do 40 oddzielone przecinkami, jak pokazano).
>

Aby można było użyć modułu w trybie MapReduce (odpowiednie dla dużych zestawów danych), potrzebny jest dostęp do klastra usługi HDInsight Hadoop (używanym do eksplorowania funkcji), a także jego poświadczenia. Poprzednie ilustracje ilustrują, jakie są wartości wypełniane. (Zastąp wartości podane dla ilustracji, które są odpowiednie dla własnego przypadku użycia).

![Parametry modułu](./media/hive-criteo-walkthrough/05IqySf.png)

Powyższy rysunek przedstawia sposób wprowadzania lokalizacji wejściowego obiektu BLOB. Ta lokalizacja zawiera dane zarezerwowane na potrzeby kompilowania tabel liczbowych.

Po zakończeniu działania tego modułu Zapisz przekształcenie później, klikając prawym przyciskiem myszy moduł i wybierając opcję **Zapisz jako przekształcenia** :

![Opcja "Zapisz jako przekształcenie"](./media/hive-criteo-walkthrough/IcVgvHR.png)

W naszej architekturze eksperymentów pokazanych powyżej zestaw danych "ytransform2" odpowiada dokładnie na zapisane przekształcenie Count. W pozostałej części tego eksperymentu przyjęto założenie, że czytelnik użył modułu **transformacji zliczania kompilacji** na niektórych danych w celu wygenerowania liczników, a następnie użyje tych liczników w celu wygenerowania funkcji zliczania w ramach testów i zestawów danych testowych.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Wybieranie funkcji liczby do uwzględnienia w ramach szkolenia i testów zestawów danych
Gdy Przekształć liczbę gotowy, użytkownik może wybrać funkcje, które mają być uwzględnione w swoich szkoleniach i testowych zestawach danych przy użyciu modułu **Modyfikuj tabelę parametrów tabeli** . W celu zapewnienia kompletności ten moduł jest pokazywany w tym miejscu. Ale w interesie uproszczenia nie są faktycznie używane w doświadczeniu.

![Modyfikuj parametry tabeli Count](./media/hive-criteo-walkthrough/PfCHkVg.png)

W takim przypadku, jak to możliwe, należy użyć dziennika szanse, a kolumna wycofywania jest ignorowana. Można również ustawić parametry, takie jak próg zasobnika elementów bezużytecznych, liczba powolnych przykładów, które należy dodać do wygładzania, oraz czy należy używać dowolnego szumu Laplacian. Wszystkie te funkcje są zaawansowane i należy zauważyć, że wartości domyślne są dobrym punktem wyjścia dla użytkowników, którzy są nowością dla tego typu generacji funkcji.

##### <a name="data-transformation-before-generating-the-count-features"></a>Przekształcanie danych przed wygenerowaniem funkcji count
Teraz koncentruje się na ważnym punkcie dotyczącym przekształcania szkolenia i danych testowych przed faktycznym generowaniem funkcji count. Należy pamiętać, że po zastosowaniu transformacji Count do naszych danych są używane dwa moduły **wykonywania skryptów języka R** .

![Wykonaj moduły skryptu języka R](./media/hive-criteo-walkthrough/aF59wbc.png)

Oto pierwszy skrypt języka R:

![Pierwszy skrypt języka R](./media/hive-criteo-walkthrough/3hkIoMx.png)

Ten skrypt języka R zmienia nazwy kolumn na "Kol1" na "Col40". Wynika to z faktu, że transformacja Count oczekuje nazw tego formatu.

Drugi skrypt języka R równoważy rozkład między klasami dodatnimi i negatywnymi (odpowiednio klasy 1 i 0) przez próbkowanie klasy negatywnej. Skrypt języka R w tym miejscu pokazuje, jak to zrobić:

![Drugi skrypt języka R](./media/hive-criteo-walkthrough/91wvcwN.png)

W tym prostym skrypcie języka R, współczynnik "pos\_ujemna\_" służy do ustawiania wielkości równowagi między dodatnią i ujemną klasą. Jest to ważne, ponieważ zwiększenie nierównowagi klasy zazwyczaj ma zalety wydajności w przypadku problemów klasyfikacji, gdy dystrybucja klas jest skośna (należy odwołać się do klasy pozytywnej o 3,3% i 96,7% klasy negatywnej).

##### <a name="applying-the-count-transformation-on-our-data"></a>Zastosowanie przekształcenia Count na naszych danych
Na koniec można użyć modułu **przekształcenie** , aby zastosować transformacje liczników do naszych testów i zestawów danych testowych. Ten moduł przyjmuje zapisane przekształcenia Count jako jedną wartość wejściową, a pociąg lub test zestawy danych jako inne dane wejściowe i zwraca dane z funkcjami Count. Przedstawiono tutaj:

![Zastosuj moduł transformacji](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Fragment informacji o liczbie wyglądających na wygląd funkcji
Warto zobaczyć, jakie funkcje liczby wyglądają jak w naszym przypadku. Oto fragment tego:

![Liczba funkcji](./media/hive-criteo-walkthrough/FO1nNfw.png)

Ten fragment pokazuje, że w odniesieniu do kolumn, które są zliczane, otrzymujesz liczniki i szanse dziennika oprócz wszelkich istotnych odniesień.

Teraz można przystąpić do kompilowania modelu Azure Machine Learning przy użyciu tych przekształconych zestawów danych. W następnej sekcji pokazano, jak to zrobić.

### <a name="step3"></a>Krok 3. kompilowanie, uczenie i Ocena modelu

#### <a name="choice-of-learner"></a>Wybór
Najpierw musisz wybrać naukę. Korzystaj z dwuklasowego drzewa decyzyjnego. Poniżej przedstawiono domyślne opcje tego rozwiązania:

![Dwuklasowe parametry drzewa decyzyjnego](./media/hive-criteo-walkthrough/bH3ST2z.png)

Na potrzeby eksperymentu wybierz wartości domyślne. Należy pamiętać, że wartości domyślne są zwykle zrozumiałe i dobrym sposobem na uzyskanie szybkich linii bazowych wydajności. Można zwiększyć wydajność przez odczyszczenie parametrów, jeśli wybierzesz opcję po utworzeniu linii bazowej.

#### <a name="train-the-model"></a>Trenowanie modelu
W przypadku szkolenia po prostu wywołaj moduł **uczenia modelu** . Dwa dane wejściowe do niego to informacje o podwyższej klasie i zestawie danych szkolenia. Jest to pokazane tutaj:

![Moduł uczenia modelu](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Ocena modelu
Gdy masz model szkolony, możesz przystąpić do oceny zestawu danych testowych i oszacować jego wydajność. W tym celu należy użyć modułu oceny **wyniku** pokazanego na poniższym rysunku, wraz z modułem **Oceń model** :

![Moduł Score Model (Generowanie wyników przez model)](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step4"></a>Krok 4. oszacowanie modelu
Na koniec należy analizować wydajność modelu. Zwykle w przypadku problemów klasyfikacji dwóch klas (binarnych) dobrym środkiem jest AUC. Aby je wizualizować, należy podłączyć moduł **modelu wyników** do modułu **oceny modelu** . Kliknięcie przycisku **Wizualizuj** w module **Oceń model** daje grafikę podobną do następującej:

![Oceń model BDT modułu](./media/hive-criteo-walkthrough/0Tl0cdg.png)

W przypadku problemów klasyfikacji w postaci binarnej (lub dwóch klas) dobrym środkiem dokładności przewidywania jest obszar w obszarze krzywa (AUC). W poniższej sekcji przedstawiono nasze wyniki przy użyciu tego modelu na naszym testowym zestawie danych. Aby to zrobić, kliknij prawym przyciskiem myszy port wyjściowy modułu **szacowania model** , a następnie wybierz polecenie **Wizualizuj**.

![Wizualizuj ocenę modułu modelu](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step5"></a>Krok 5. Publikowanie modelu jako usługi sieci Web
Możliwość opublikowania Azure Machine Learning modelu jako usług sieci Web z co najmniej Fuss jest cenną funkcją do zapewnienia, że jest ona szeroko dostępna. Gdy wszystko będzie gotowe, każdy może wykonywać wywołania usługi sieci Web z danymi wejściowymi, dla których są potrzebne przewidywania, a usługa sieci Web używa modelu do zwrócenia tych prognoz.

Aby to zrobić, najpierw Zapisz nasz model przeszkolony jako obiekt modelu przeszkolonego. W tym celu należy kliknąć prawym przyciskiem myszy moduł **uczenie modelu** i użyć opcji **Zapisz jako przeszkolony model** .

Następnie utwórz porty wejściowe i wyjściowe dla naszej usługi sieci Web:

* port wejściowy pobiera dane w tym samym formularzu co dane, które są potrzebne do prognozowania
* Port wyjściowy zwraca etykiety z oceną i powiązane prawdopodobieństwa.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Wybierz kilka wierszy danych dla portu wejściowego
Aby wybrać tylko 10 wierszy do obsłużenia jako dane wejściowego portu, wygodnie jest użyć modułu **zastosowania przekształcenia języka SQL** . Wybierz tylko te wiersze danych dla portu wejściowego, używając zapytania SQL pokazanego tutaj:

![Dane wejściowe portu](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Usługa sieci Web
Teraz możesz przystąpić do uruchamiania małego eksperymentu, którego można użyć do opublikowania naszej usługi sieci Web.

#### <a name="generate-input-data-for-webservice"></a>Generowanie danych wejściowych dla usługi WebService
Jako krok zerowego, ponieważ tabela Count jest duża, zrób kilka wierszy danych testowych i generuje z niej dane wyjściowe z funkcjami Count. Może to stanowić format danych wejściowych dla naszej sieci Web. Jest to pokazane tutaj:

![Tworzenie danych wejściowych BDT](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> W przypadku formatu danych wejściowych Użyj danych wyjściowych modułu **Count featurized** . Po zakończeniu tego eksperymentu Zapisz dane wyjściowe z modułu **Count featurized** jako zestaw danych. Ten zestaw danych jest używany jako dane wejściowe w sieci Web.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Eksperyment oceniający publikowanie usługi sieci Web
Najpierw pokazano, jak to wygląda. Podstawowa struktura jest modułem **modelu oceny** , który akceptuje nasz przeszkolony obiekt modelu oraz kilka wierszy danych wejściowych, które zostały wygenerowane w poprzednich krokach przy użyciu modułu **Count featurized** . Użyj opcji "Wybierz kolumny w zestawie danych", aby wykreślić etykiety oceny i wyniki prawdopodobieństwa.

![Wybieranie kolumn w zestawie danych](./media/hive-criteo-walkthrough/kRHrIbe.png)

Zwróć uwagę, jak w module **Wybieranie kolumn w zestawie danych** można używać do filtrowania danych z zestawu danych. Zawartość jest pokazana tutaj:

![Filtrowanie za pomocą modułu SELECT Columns in DataSet](./media/hive-criteo-walkthrough/oVUJC9K.png)

Aby uzyskać niebieskie porty wejściowe i wyjściowe, po prostu kliknij pozycję **Przygotuj** usługę sieci Web w prawym dolnym rogu. Uruchomienie tego eksperymentu umożliwia również opublikowanie usługi sieci Web: kliknij ikonę **Publikuj usługę sieci Web** w prawym dolnym rogu, poniżej:

![Publikowanie usługi sieci Web](./media/hive-criteo-walkthrough/WO0nens.png)

Po opublikowaniu usługi WebService nastąpi przekierowanie do strony, która wygląda następująco:

![Pulpit nawigacyjny usługi internetowej](./media/hive-criteo-walkthrough/YKzxAA5.png)

Zwróć uwagę na dwa linki dla usług WebService po lewej stronie:

* Usługa **żądania/odpowiedzi** (lub RR) jest przeznaczona dla pojedynczych prognoz i jest to, co zostało wykorzystane w tej warsztatie.
* Usługa **wykonywania wsadowego** (BES) jest używana na potrzeby prognoz partii i wymaga, aby dane wejściowe używane do tworzenia prognoz znajdować się w usłudze Azure Blob Storage.

Kliknięcie linku **żądanie/odpowiedź** prowadzi do strony, która zapewnia nam kod wstępnie przetwórczy w C#języku Python i języku R. Ten kod może być wygodnie używany do wykonywania wywołań do usługi sieci Web. Należy pamiętać, że klucz interfejsu API na tej stronie musi być używany do uwierzytelniania.

Można skopiować ten kod w języku Python do nowej komórki w notesie IPython.

Oto segment kodu języka Python z poprawnym kluczem interfejsu API.

![Kod języka Python](./media/hive-criteo-walkthrough/f8N4L4g.png)

Należy zauważyć, że domyślny klucz interfejsu API został zastąpiony przez nasz klucz interfejsu API usługi WebService. Kliknięcie przycisku **Uruchom** w tej komórce w notesie IPython daje następujące odpowiedzi:

![IPython odpowiedź](./media/hive-criteo-walkthrough/KSxmia2.png)

Na potrzeby dwóch przykładowych testów (w strukturze JSON skryptu w języku Python) odpowiedzi są odbierane w formie "etykiet oceny", co oznacza prawdopodobieństwa ". W takim przypadku wartości domyślne zostały wybrane, że kod wstępnie przewidziany przez program zapewnia (0 dla wszystkich kolumn liczbowych i ciąg "value" dla wszystkich kolumn kategorii).

Zakończymy nasze wskazówki dotyczące obsługi zestawu danych na dużą skalę przy użyciu Azure Machine Learning. Zaczynasz od terabajta danych, konstruujesz model przewidywania i wdrożono go jako usługę sieci Web w chmurze.

