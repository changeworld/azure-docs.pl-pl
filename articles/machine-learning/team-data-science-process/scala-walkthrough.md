---
title: Analiza danych przy użyciu języka Scala i aparatu Spark na platformie Azure — zespołu danych dla celów naukowych
description: Jak korzystać z języka Scala dla zadania uczenia maszynowego nadzorowanych za pomocą platformy Spark skalowalne MLlib i Spark ML pakietów w klastrze usługi Azure HDInsight Spark.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: cdc37ace4687fe978030f528dcd5cbc87da596f0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60589569"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Analiza danych przy użyciu języka Scala i platformy Spark na platformie Azure
W tym artykule pokazano, jak korzystać z języka Scala dla zadania uczenia maszynowego nadzorowanych za pomocą platformy Spark skalowalne MLlib i Spark ML pakietów w klastrze usługi Azure HDInsight Spark. Przeprowadzi Cię on zadania, które stanowią [danych dla celów naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): pozyskiwanie danych i eksploracji, wizualizacji, technicznego opracowywania funkcji, modelowania i użycie modelu. Modele w artykule obejmują regresji logistycznej liniowego i liniowa, losowych lasów i wzmocnione gradientu drzew (GBTs), oprócz dwie typowe zadania uczenia maszynowego nadzorowanego:

* Problem regresji: Prognozowanie kwota tip ($), komunikacji dwustronnej taksówek
* Klasyfikacja binarna: Prognozowanie porady lub brak porady komunikacji dwustronnej taksówek (1/0)

Proces modelowania wymaga uczenie i Ewaluacja testowego zestawu danych i dokładność istotne metryki. W tym artykule nauczysz się sposobu przechowywania tych modeli usługi Azure Blob Storage oraz jak ocena i oceny wydajności predykcyjne. W tym artykule opisano również bardziej zaawansowanych tematów dotyczących sposobu optymalizacji modeli za pomocą zaczynają krzyżowego sprawdzania poprawności i parametrów. Dane używane jest przykładem 2013 NYC taksówek podróży i klasie zestawu danych dostępne w serwisie GitHub.

[Scala](https://www.scala-lang.org/), języka, w oparciu o maszynę wirtualną Java integruje się koncepcje językowe zorientowane obiektowo i funkcjonalne. Jest skalowalne język, który dobrze nadaje się do przetwarzania rozproszonego w chmurze i działa w klastrach usługi Azure Spark.

[Platforma Spark](https://spark.apache.org/) to platforma przetwarzania równoległego typu open source, która obsługuje przetwarzanie w pamięci w celu zwiększania wydajności aplikacji do analizowania danych big data. Aparat przetwarzania Spark zaprojektowano pod kątem szybkości, łatwości użycia i zaawansowanych możliwości analitycznych. Możliwości obliczeń rozproszonych w pamięci platforma Spark ułatwiają dobrym wyborem w przypadku algorytmów iteracyjnych używanych w machine learning i obliczeniach na grafach. [Spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) pakiet zawiera zbiór jednolite interfejsy API wysokiego poziomu, zbudowany na podstawie danych klatek, które mogą pomóc Ci tworzenie i dostosowywanie praktyczne usługi machine learning potoków. [Biblioteka MLlib](https://spark.apache.org/mllib/) to biblioteka uczenia maszynowego na skalowalnej platforma Spark, który udostępnia funkcje modelowania na tym środowisku rozproszonym.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) to oferta hostowanymi na platformie Azure, typu open source platformy Spark. On również obejmuje obsługę notesów Jupyter Scala w klastrze Spark i mogą uruchamiać interakcyjne zapytania Spark SQL do przekształcania, filtrować i wizualizacji danych przechowywanych w usłudze Azure Blob storage. Uruchom Scala fragmenty kodu w niniejszym artykule udostępniają rozwiązań, które pokazują odpowiednie powierzchnie, które umożliwiają wizualizację danych w aplikacji Jupyter notebooks zainstalowane w klastrach Spark. Kroki modelowania w tych tematach ma kod, który pokazuje, jak uczenie, ocenę, Zapisz i zużywać każdy rodzaj modelu.

Kroki instalacji i kodu w tym artykule dotyczą usługi Azure HDInsight 3.4 Spark 1.6. Jednak kod, w tym artykule oraz w [notesu programu Jupyter Scala](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) są ogólne i powinna działać w dowolnym klastrze Spark. Kroki konfiguracji i zarządzania klastrem, może być nieco inne niż przedstawionego w tym artykule, jeśli nie używasz platformy HDInsight Spark.

> [!NOTE]
> Dla tematu, który pokazuje, jak używać języka Python, a nie w języku Scala, w celu wykonania zadań w procesie nauki o danych end-to-end, zobacz [do nauki o danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
* Wymagana jest subskrypcja platformy Azure. Jeśli nie masz już jeden, [uzyskać bezpłatna wersja próbna platformy](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Potrzebujesz klastra Azure HDInsight 3.4 Spark 1.6 do wykonania poniższych procedur. Aby utworzyć klaster, zobacz instrukcje w [rozpocząć pracę: Tworzenie platformy Apache Spark w usłudze Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Ustaw typ klastra i wersji na **wybierz typ klastra** menu.

![Konfiguracja typu klastra HDInsight](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Opis danych podróży taksówek NYC i instrukcje dotyczące sposobu wykonania kodu z notesu Jupyter w klastrze Spark, zobacz w odpowiednich sekcjach w [Omówienie programu do nauki o danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Wykonanie kodu z języka Scala z notesu Jupyter w klastrze Spark
Można uruchomić notesu programu Jupyter w witrynie Azure portal. Znajdź klaster Spark na pulpicie nawigacyjnym, a następnie kliknij go wprowadzić na stronie zarządzania dla klastra. Następnie kliknij przycisk **pulpity nawigacyjne klastra**, a następnie kliknij przycisk **notesu programu Jupyter** aby otworzyć notes skojarzonego z klastrem Spark.

![Pulpit nawigacyjny klastra i notesy Jupyter](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Możesz także będą mogli notesów programu Jupyter w https://&lt;clustername&gt;.azurehdinsight.net/jupyter. Zastąp *clustername* nazwą klastra. Konieczne jest hasło dla konta administratora dostęp do notesów programu Jupyter.

![Przejdź do notesów programu Jupyter, używając nazwy klastra](./media/scala-walkthrough/spark-jupyter-notebook.png)

Wybierz **Scala** wyświetlić katalog, który zawiera kilka przykładów notesów to wstępnie spakowane zestawy, które używają interfejsu API PySpark. Eksploracji, modelowania i oceniania, za pomocą notesu Scala.ipynb, który zawiera kod przykłady w tym zestawie tematów platformy Spark na jest dostępny [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

Możesz przekazać Notes bezpośrednio z serwisu GitHub, aby serwer notesu Jupyter w klastrze Spark. Na stronie głównej programu Jupyter, kliknij przycisk **przekazywanie** przycisku. W Eksploratorze plików, wklej adres URL usługi GitHub (nieprzetworzonej zawartości) notesu Scala, a następnie kliknij przycisk **Otwórz**. Notes Scala jest dostępna pod adresem URL:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Konfiguracja: Wstępnie zdefiniowane konteksty Spark i Hive, poleceń magicznych platformy Spark i bibliotek platformy Spark
### <a name="preset-spark-and-hive-contexts"></a>Wstępnie zdefiniowane konteksty Spark i Hive
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Jądra platformy Spark, które są dostarczane z notesów Jupyter mają wstępnie kontekstów. Nie trzeba jawnie ustawić platformy Spark, lub opracowujesz kontekstów gałęzi, przed rozpoczęciem pracy z aplikacją. Wstępnie ustawionych kontekstów są następujące:

* `sc` Aby uzyskać odporne rozproszone zestawy
* `sqlContext` Aby uzyskać HiveContext

### <a name="spark-magics"></a>Poleceń magicznych platformy Spark
Jądra Spark zawiera kilka wstępnie zdefiniowanych "poleceń magicznych", które są specjalne polecenia, które można wywoływać za pomocą `%%`. Dwa z tych poleceń, są używane w następujących przykładach kodu.

* `%%local` Określa lokalnie wykonać kod w kolejnych wierszy. Kod musi być prawidłowym kodem Scala.
* `%%sql -o <variable name>` wykonuje zapytanie programu Hive względem `sqlContext`. Jeśli `-o` parametr jest przekazywany, wynik kwerendy są utrwalane w `%%local` kontekstu Scala jako rozwiązania Spark data frame.

Aby dowiedzieć się więcej o jądra notesów programu Jupyter i ich wstępnie zdefiniowanego "magics", należy wywołać za pomocą `%%` (na przykład `%%local`), zobacz [jądra, które są dostępne dla notesów programu Jupyter przy użyciu platformy Spark HDInsight w systemie Linux klastrów HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Importuj biblioteki
Zaimportuj z platformy Spark, MLlib i innych bibliotek, które będą potrzebne przy użyciu następującego kodu.

    # IMPORT SPARK AND JAVA LIBRARIES
    import org.apache.spark.sql.SQLContext
    import org.apache.spark.sql.functions._
    import java.text.SimpleDateFormat
    import java.util.Calendar
    import sqlContext.implicits._
    import org.apache.spark.sql.Row

    # IMPORT SPARK SQL FUNCTIONS
    import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
    import org.apache.spark.sql.functions.rand

    # IMPORT SPARK ML FUNCTIONS
    import org.apache.spark.ml.Pipeline
    import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
    import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
    import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
    import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
    import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

    # IMPORT SPARK MLLIB FUNCTIONS
    import org.apache.spark.mllib.linalg.{Vector, Vectors}
    import org.apache.spark.mllib.util.MLUtils
    import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
    import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
    import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
    import org.apache.spark.mllib.tree.configuration.BoostingStrategy
    import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
    import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

    # SPECIFY SQLCONTEXT
    val sqlContext = new SQLContext(sc)


## <a name="data-ingestion"></a>Wprowadzanie danych
Pierwszym krokiem w procesie nauki o danych jest w celu pozyskiwania danych, które mają być analizowane. Wprowadzasz dane ze źródeł zewnętrznych lub systemy, w którym znajduje się w środowisku Eksplorowanie i modelowanie danych. W tym artykule dane, pobieranie są przyłączone do przykładowych 0,1% pliku podróży i klasie taksówek (przechowywany jako plik tsv). Środowisko Eksplorowanie i modelowanie danych jest platformy Spark. Ta sekcja zawiera kod, aby wykonać poniższą sekwencję zadań:

1. Ustaw ścieżki katalogu do przechowywania danych i modelu.
2. Przeczytaj w zestawie danych wejściowych (przechowywany jako plik tsv).
3. Zdefiniować schemat danych i czyszczenie danych.
4. Utwórz ramkę danych oczyszczony i buforowanie w pamięci.
5. Zarejestruj dane jako tabelę tymczasową w SQLContext.
6. Odpytać tabelę, a następnie zaimportuj wyniki do ramki danych.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Ustaw ścieżki katalogu lokalizacje przechowywania w magazynie obiektów Blob platformy Azure
Platforma Spark może odczytywać i zapisywania ich w magazynie obiektów Blob platformy Azure. Można przetworzyć żadnych istniejących danych za pomocą platformy Spark, a następnie ponownie zapisać wyniki w magazynie obiektów Blob.

Aby zapisać modeli lub pliki w magazynie obiektów Blob, należy prawidłowo określić ścieżkę. Odwołanie kontener domyślny, dołączony do klastra Spark przy użyciu ścieżki, która rozpoczyna się od `wasb:///`. Odwoływać się do innych lokalizacji za pomocą `wasb://`.

Poniższy przykład kodu Określa dane wejściowe do odczytu i ścieżkę do magazynu obiektów Blob, który jest dołączony do klastra Spark w lokalizacji, w którym zostanie zapisany modelu.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importuj dane, tworzyć RDD i zdefiniować ramkę danych według schematu
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
    val sqlContext = new SQLContext(sc)
    val taxi_schema = StructType(
        Array(
            StructField("medallion", StringType, true),
            StructField("hack_license", StringType, true),
            StructField("vendor_id", StringType, true),
            StructField("rate_code", DoubleType, true),
            StructField("store_and_fwd_flag", StringType, true),
            StructField("pickup_datetime", StringType, true),
            StructField("dropoff_datetime", StringType, true),
            StructField("pickup_hour", DoubleType, true),
            StructField("pickup_week", DoubleType, true),
            StructField("weekday", DoubleType, true),
            StructField("passenger_count", DoubleType, true),
            StructField("trip_time_in_secs", DoubleType, true),
            StructField("trip_distance", DoubleType, true),
            StructField("pickup_longitude", DoubleType, true),
            StructField("pickup_latitude", DoubleType, true),
            StructField("dropoff_longitude", DoubleType, true),
            StructField("dropoff_latitude", DoubleType, true),
            StructField("direct_distance", StringType, true),
            StructField("payment_type", StringType, true),
            StructField("fare_amount", DoubleType, true),
            StructField("surcharge", DoubleType, true),
            StructField("mta_tax", DoubleType, true),
            StructField("tip_amount", DoubleType, true),
            StructField("tolls_amount", DoubleType, true),
            StructField("total_amount", DoubleType, true),
            StructField("tipped", DoubleType, true),
            StructField("tip_class", DoubleType, true)
            )
        )

    # CAST VARIABLES ACCORDING TO THE SCHEMA
    val taxi_temp = (taxi_train_file.map(_.split("\t"))
                            .filter((r) => r(0) != "medallion")
                            .map(p => Row(p(0), p(1), p(2),
                                p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                                p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                                p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                                p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


    # CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
    val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
            .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
            .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
            .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
            .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
            .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
            .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
            .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
            .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

    # CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Dane wyjściowe:**

Czas, aby uruchomić komórkę: 8 sekund.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Odpytać tabelę i importowanie wyników w ramce danych.
Następnie odpytać tabelę w klasie, pasażerskich i danymi Porada; Filtrowanie danych w uszkodzona i odległe; i drukowanie kilka wierszy.

    # QUERY THE DATA
    val sqlStatement = """
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train
        WHERE passenger_count > 0 AND passenger_count < 7
        AND fare_amount > 0 AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 AND tip_amount < 25
    """
    val sqlResultsDF = sqlContext.sql(sqlStatement)

    # SHOW ONLY THE TOP THREE ROWS
    sqlResultsDF.show(3)

**Dane wyjściowe:**

| fare_amount | passenger_count | tip_amount | Przechylony |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Eksploracja i wizualizacja danych
Po przekazaniu danych do programu platformy Spark, następnym krokiem w procesie nauki o danych jest lepiej zrozumieć dane za pośrednictwem eksploracji i wizualizacji. W tej sekcji możesz sprawdzić dane taksówek wystosowanie zapytań SQL. Następnie można zaimportować wyników do ramki danych do wykreślenia docelowych zmiennych i potencjalnego funkcji kontroli przy użyciu funkcji automatycznego wizualizacji Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Dane wykresu za pomocą lokalnego i magicznym wyrażeniem SQL
Domyślnie dane wyjściowe wszystkie fragmenty kodu, który można uruchomić z poziomu notesu programu Jupyter jest dostępna w kontekście sesji, który jest trwały na węzłach procesu roboczego. Jeśli chcesz zapisać podróż do węzłów procesu roboczego dla każdego obliczenia, a jeśli wszystkie dane potrzebne do Twojego obliczeń jest dostępna lokalnie w węźle serwera programu Jupyter, (czyli węzła głównego), możesz użyć `%%local` magic systemem wstawki kodu programu Jupyter serwer.

* **Magicznym wyrażeniem SQL** (`%%sql`). Jądro HDInsight Spark obsługuje proste wbudowane HiveQL zapytania względem SQLContext. (`-o VARIABLE_NAME`) Argument będzie się powtarzał wyniki kwerendy SQL jako Pandas ramki danych na serwerze programu Jupyter. Oznacza to, że będzie ona dostępna w trybie lokalnym.
* `%%local` **Magiczna**. `%%local` Magic uruchamia kod lokalnie na serwerze programu Jupyter, który jest węzłem klastra HDInsight. Zazwyczaj można użyć `%%local` magic w połączeniu z `%%sql` magic z `-o` parametru. `-o` Parametr będzie utrwalanie danych wyjściowych, zapytania SQL lokalnie, a następnie `%%local` magic powodowało kolejny zbiór fragment kodu w celu uruchomienia lokalnie dane wyjściowe zapytań SQL, który jest trwały lokalnie.

### <a name="query-the-data-by-using-sql"></a>Wykonywanie zapytań o dane przy użyciu języka SQL
To zapytanie pobiera rund taksówek taryfy kwotę, liczby pasażerów i wielkość porada.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

W poniższym kodzie `%%local` magic tworzy ramkę danych lokalnych, sqlResults. SqlResults służy do wykreślenia przy użyciu matplotlib.

> [!TIP]
> Magiczna lokalnego jest używana wiele razy, w tym artykule. Przykładowe, jeśli zestaw danych jest duży, aby utworzyć ramkę danych, który można umieścić w lokalnej pamięci.
> 
> 

### <a name="plot-the-data"></a>Dane wykresu
Możliwe będzie wykreślanie przy użyciu kodu w języku Python, po ramka danych jest w kontekście lokalnego jako Pandas ramki danych.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 Jądra Spark automatycznie wizualizuje dane wyjściowe zapytań SQL (HiveQL), po uruchomieniu kodu. Możesz wybrać między kilka rodzajów wizualizacji:

* Tabela
* Kołowy
* Kreska
* Obszar
* Słupkowy

Poniżej przedstawiono kod, aby przedstawić dane:

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # PLOT TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 80, -2, 20])
    plt.show()


**Dane wyjściowe:**

![Porada kwota histogramu](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Porada kwota według liczby pasażerów](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Porada kwota według ilości klasie](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Tworzenie funkcji i przekształcanie funkcji i następnie przeznaczonego do przygotowania danych dla danych wejściowych do modelowania funkcji
Dla funkcji modelowania oparta na drzewie Spark ML i MLlib musisz przygotować obiektu docelowego i funkcji przy użyciu różnych technik, takich jak proces pakowania, indeksowanie, hot jeden kodowania i wektoryzacji. Poniżej przedstawiono procedury w tej sekcji:

1. Tworzenie nowej funkcji przez **pakowania** godzin ruchu czasu zasobników.
2. Zastosuj **indeksowania i jeden na gorąco kodowanie** do kategorii funkcji.
3. **Przykładowe i podzielić zestawu danych** w ułamkach szkolenia i testowania.
4. **Określ zmienną szkolenia i funkcje**, następnie utwórz indeksowanych lub hot jeden zakodowane, szkolenie i testowanie wejściowego punktu etykietami odporne rozproszone zestawów danych (danych) lub ramki danych.
5. Automatycznie **kategoryzowania i vectorize funkcje i obiekty docelowe** do użycia jako dane wejściowe dla modeli uczenia maszynowego.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Utwórz nową funkcję według godzin pakowania do przedziałów czasu ruchu
Ten kod przedstawia sposób tworzenia nowej funkcji o pakowania godzin w przedziałów czasu ruchu oraz wynikowe ramki danych w pamięci w pamięci podręcznej. Gdzie ramki danych i danych są stosowane cyklicznie, buforowanie prowadzi do poprawy czasu wykonania. W związku z tym będzie buforować danych i ramki danych na wiele etapów w ramach poniższych procedur.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    val sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train
    """
    val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indeksowanie i hot jeden kodowanie funkcji podzielonych na kategorie
Modelowania i przewidywanie funkcje MLlib wymagają funkcji z podzielonych na kategorie danych wejściowych, które mają być indeksowane lub zakodowane przed użyciem. W tej sekcji pokazano, jak indeksu lub zakodować kategorii funkcji dla danych wejściowych do funkcji modelowania.

Musisz indeksu lub zakodować swoje modele na różne sposoby, w zależności od modelu. Na przykład modele regresji logistycznej liniowego i liniowa wymagają hot jeden kodowania. Na przykład funkcji z trzech kategorii można rozszerzyć do trzech kolumnach funkcji. Każda kolumna będzie zawierać 0 lub 1 w zależności od kategorii wystąpienia wartości. Udostępnia MLlib [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkcja hot jeden kodowania. Ten koder mapuje kolumny indeksów etykiety z kolumną wektorów binarnych z co najwyżej jeden — wartość typu single. Za pomocą tego kodowania algorytmy, które oczekują liczbowe ważnych funkcji, takich jak regresji logistycznej, można zastosować do kategorii funkcji.

W tym miejscu możesz przekształcić tylko cztery zmienne, aby wyświetlić przykłady, które są ciągami znaków. Możesz również indeks innych zmiennych, takich jak dni tygodnia, reprezentowane przez wartości liczbowych, jak zmienne podzielonych na kategorie.

W przypadku indeksowanie, użyj `StringIndexer()`w przypadku hot jeden kodowanie, użyj `OneHotEncoder()` funkcji z MLlib. Poniżej przedstawiono kod, aby zaindeksować i kodowanie funkcji podzielonych na kategorie:

    # CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # INDEX AND ENCODE VENDOR_ID
    val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
    val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
    val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
    val encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
    val indexed = stringIndexer.transform(encoded1)
    val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
    val encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
    val indexed = stringIndexer.transform(encoded2)
    val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
    val encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
    val indexed = stringIndexer.transform(encoded3)
    val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
    val encodedFinal = encoder.transform(indexed)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Dane wyjściowe:**

Czas, aby uruchomić komórkę: 4 sekundy.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Przykładowy skrypt i dzielenia zestawu danych w ułamkach szkolenie i testowanie
Ten kod tworzy losowej próbki danych (w tym przykładzie 25%). Próbkowanie nie jest wymagany w tym przykładzie ze względu na rozmiar zestawu danych, w tym artykule opisano, jak do przykładowy dzięki czemu będzie wiadomo, jak własne problemów w razie potrzeby. W przypadku dużych przykłady to zapisać znaczną ilość czasu, gdy uczyć modele. Następnie Podziel próbki na część szkolenia (75%, w tym przykładzie) i testowania część (25%, w tym przykładzie) do użycia w funkcji klasyfikacji i regresji modelowania.

Dodaj losową liczbę (od 0 do 1) do każdego wiersza (w kolumnie "rand"), który może służyć do wybrania złożeń krzyżowego sprawdzania poprawności podczas szkolenia.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    val samplingFraction = 0.25;
    val trainingFraction = 0.75;
    val testingFraction = (1-trainingFraction);
    val seed = 1234;
    val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
    val sampledDFcount = encodedFinalSampledTmp.count().toInt

    val generateRandomDouble = udf(() => {
        scala.util.Random.nextDouble
    })

    # ADD A RANDOM NUMBER FOR CROSS-VALIDATION
    val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

    # SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
    # INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
    val trainData = splits(0)
    val testData = splits(1)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Dane wyjściowe:**

Czas, aby uruchomić komórkę: 2 sekundy.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Określ zmienną szkolenia i funkcje, a następnie utwórz indeksowanych lub hot jeden zakodowane szkolenie i testowanie danych wejściowych z etykietą punktu danych lub danych ramek
Ta sekcja zawiera kod, który pokazuje, jak indeksowanie danych podzielonych na kategorie tekstu jako typ danych oznaczonych punktu i Zakoduj je, aby można było używać szkolenie i testowanie regresji logistycznej MLlib i innych modeli klasyfikacji. Obiekty oznaczone punktu są zestawów danych, które są sformatowane w sposób, które są wymagane jako dane wejściowe przez większość algorytmów w MLlib uczenia maszynowego. A [etykietą punktu](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) lokalnego wektor gęstą lub rozrzedzony, skojarzony z etykiety/odpowiedzi.

W tym kodzie należy określić (zależnych) Zmienna docelowa i funkcji na potrzeby uczenia modeli. Następnie należy utworzyć indeksowanych lub hot jeden zakodowane szkolenie i testowanie danych wejściowych z etykietą punktu danych lub danych ramek.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
    val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
    val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

    # SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
    val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
    val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

    # CREATE INDEXED LABELED POINT RDD OBJECTS
    val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

    # CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
    val indexedTESTbinaryDF = indexedTESTbinary.toDF()
    val indexedTRAINregDF = indexedTRAINreg.toDF()
    val indexedTESTregDF = indexedTESTreg.toDF()

    # CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
    val OneHotTRAIN = assemblerOneHot.transform(trainData)
    val OneHotTEST = assemblerOneHot.transform(testData)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Dane wyjściowe:**

Czas, aby uruchomić komórkę: 4 sekundy.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Automatyczne kategoryzowanie i vectorize funkcje i obiekty docelowe do użycia jako dane wejściowe dla modeli uczenia maszynowego
Użyj Spark ML do klasyfikowania obiektu docelowego i funkcji do użycia w funkcji modelowania oparta na drzewie. Kod wykonuje dwie czynności:

* Tworzy binarny obiektu docelowego dla klasyfikacji, przypisując wartość 0 lub 1 do każdego punktu danych, od 0 do 1 przy użyciu wartości próg wynosi 0,5.
* Automatycznie klasyfikuje funkcji. Liczba unikatowych wartości liczbowe dla dowolnej funkcji, jest mniej niż 32, ta funkcja jest dzielony na kategorie.

Poniżej przedstawiono kod dla tych dwóch zadań.

    # CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
    # CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINregDF)
    val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Binarny model klasyfikacji: Przewidywania, czy należy zwrócić Porada
W tej sekcji utworzysz trzy typy modeli klasyfikacji binarnej w celu przewidywania, czy należy zwrócić Porada:

* A **modelu regresji logistycznej** przy użyciu Spark ML `LogisticRegression()` — funkcja
* A **model klasyfikacji losowe lasu** przy użyciu Spark ML `RandomForestClassifier()` — funkcja
* A **gradientu zwiększenie model klasyfikacji drzewa** przy użyciu MLlib `GradientBoostedTrees()` — funkcja

### <a name="create-a-logistic-regression-model"></a>Tworzenie modelu regresji logistycznej
Następnie należy utworzyć model regresji logistycznej przy użyciu Spark ML `LogisticRegression()` funkcji. Utworzysz model tworzenia kodu w serii kroków:

1. **Uczenie modelu** danych za pomocą jeden zestaw parametrów.
2. **Ocena modelu** na testowego zestawu danych za pomocą metryk.
3. **Zapisz model** w usłudze Blob storage do użytku w przyszłości.
4. **Klasyfikacja modelu** względem danych testowych.
5. **Wykreślania wyniki** z odbiornikiem operacyjnego krzywych cechy (ROC).

Poniżej przedstawiono kod dla tych procedur:

    # CREATE A LOGISTIC REGRESSION MODEL
    val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
    val lrModel = lr.fit(OneHotTRAIN)

    # PREDICT ON THE TEST DATA SET
    val predictions = lrModel.transform(OneHotTEST)

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)

    # SAVE THE MODEL
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LogisticRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

Obciążenia, ocena i zapisać wyniki.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
    val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON THE TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
    predictions.registerTempTable("testResults")

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC RESULTS
    println("ROC on test data = " + ROC)


**Dane wyjściowe:**

ROC na danych testowych = 0.9827381497557599

Za pomocą języka Python na lokalnym ramki danych Pandas do wykreślenia krzywej ROC.

    # QUERY THE RESULTS
    %%sql -q -o sqlResults
    SELECT tipped, probability from testResults


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
    predictions_pddf = sqlResults[["tipped","probFloat"]]

    # PREDICT THE ROC CURVE
    # predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
    prob = predictions_pddf["probFloat"]
    fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT THE ROC CURVE
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()


**Dane wyjściowe:**

![Porada lub nie krzywej ROC Porada](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Utwórz model klasyfikacji losowe lasu
Następnie utwórz model klasyfikacji losowe lasu za pomocą Spark ML `RandomForestClassifier()` funkcji, a następnie ocenę modelu na danych testowych.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE THE RANDOM FOREST CLASSIFIER MODEL
    val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

    # FIT THE MODEL
    val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
    val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

    # EVALUATE THE MODEL
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(predictions)
    println("F1 score on test data: " + Test_f1Score);

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)


**Dane wyjściowe:**

ROC na danych testowych = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>Utwórz model klasyfikacji GBT
Następnie utwórz model klasyfikacji GBT przy użyciu MLlib firmy `GradientBoostedTrees()` funkcji, a następnie ocenę modelu na danych testowych.

    # TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE GBT CLASSIFICATION MODEL
    val boostingStrategy = BoostingStrategy.defaultParams("Classification")
    boostingStrategy.numIterations = 20
    boostingStrategy.treeStrategy.numClasses = 2
    boostingStrategy.treeStrategy.maxDepth = 5
    boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    # TRAIN THE MODEL
    val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

    # SAVE THE MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "GBT_Classification__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    gbtModel.save(sc, filename);

    # EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
    val labelAndPreds = indexedTESTbinary.map { point =>
      val prediction = gbtModel.predict(point.features)
      (point.label, prediction)
    }
    val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
    //println("Learned classification GBT model:\n" + gbtModel.toDebugString)
    println("Test Error = " + testErr)

    # USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
    val metrics = new MulticlassMetrics(labelAndPreds)
    println(s"Precision: ${metrics.precision}")
    println(s"Recall: ${metrics.recall}")
    println(s"F1 Score: ${metrics.fMeasure}")

    val metrics = new BinaryClassificationMetrics(labelAndPreds)
    println(s"Area under PR curve: ${metrics.areaUnderPR}")
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC METRIC
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**Dane wyjściowe:**

Obszar pod krzywą ROC: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Model regresji: Przewidywanie kwota Porada
W tej sekcji utworzysz dwa rodzaje modele regresji, by kwota Porada:

* A **uczenia modelu regresji liniowej umorzyć** przy użyciu Spark ML `LinearRegression()` funkcji. Będzie zapisać model i ocenę modelu na danych testowych.
* A **gradientu drzewa modelu regresji** przy użyciu Spark ML `GBTRegressor()` funkcji.

### <a name="create-a-regularized-linear-regression-model"></a>Tworzenie modelu regresji liniowej umorzyć
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
    val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

    # FIT THE MODEL BY USING DATA FRAMES
    val lrModel = lr.fit(OneHotTRAIN)
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
    val trainingSummary = lrModel.summary
    println(s"numIterations: ${trainingSummary.totalIterations}")
    println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
    trainingSummary.residuals.show()
    println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
    println(s"r2: ${trainingSummary.r2}")

    # SAVE THE MODEL IN AZURE BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LinearRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

    # PRINT THE COEFFICIENTS
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = lrModel.transform(OneHotTEST)

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Dane wyjściowe:**

Czas, aby uruchomić komórkę: 13 sekundach.

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
    val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
    predictions.registerTempTable("testResults")

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("R-sqr on test data = " + r2)


**Dane wyjściowe:**

R-sqr na danych testowych = 0.5960320470835743

Następnie zapytanie wyniki testów jako ramkę danych i zwizualizować go za pomocą AutoVizWidget i matplotlib.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

Ten kod tworzy ramkę danych lokalnych na podstawie wyników zapytania i drukuje dane. `%%local` Magic tworzy ramkę danych lokalnych `sqlResults`, którego można użyć do wykreślenia z matplotlib.

> [!NOTE]
> Ta magic Spark jest używana wiele razy, w tym artykule. W przypadku dużych ilości danych, powinny być przykładowe Utwórz ramkę danych, który można umieścić w lokalnej pamięci.
> 
> 

Tworzenie wykresów przy użyciu języka Python matplotlib.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    sqlResults
    %matplotlib inline
    import numpy as np

    # PLOT THE RESULTS
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    #ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 8])
    plt.show(ax)

**Dane wyjściowe:**

![Kwota Porada: Rzeczywiste a przewidywane](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Utworzyć model regresji GBT
Utworzyć model regresji GBT przy użyciu Spark ML `GBTRegressor()` funkcji, a następnie ocenę modelu na danych testowych.

[Wzmocnione gradientu drzew](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) są decyzyjne drzewa decyzyjne. GBTs uczenie drzew decyzyjnych interakcyjnie, aby zminimalizować funkcję utraty. Można użyć GBTs regresji i klasyfikacji. One może obsługiwać funkcje podzielonych na kategorie, skalowanie funkcja nie jest wymagane i przechwytywać nonlinearities i interakcje funkcji. Użytkownik może ich użyć także w ustawieniu kontra klasyfikacji.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # TRAIN A GBT REGRESSION MODEL
    val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
    val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

    # MAKE PREDICTIONS
    val predictions = gbtModel.transform(indexedTESTwithCatFeat)

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(predictions)


    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("Test R-sqr is: " + Test_R2);


**Dane wyjściowe:**

Test R-sqr jest: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Narzędzia zaawansowane modelowanie optymalizacji
W tej sekcji użyjesz machine learning narzędzia, które deweloperzy często korzystają z modelu optymalizacji. W szczególności można zoptymalizować modeli uczenia maszynowego trzy różne sposoby, za pomocą parametru zaczynają i krzyżowego sprawdzania poprawności:

* Podzielić dane na szkolenie i sprawdzania poprawności zestawów, optymalizowanie modelu przy użyciu parametrów zaczynają na zestaw szkoleniowy i oceny w zestawie sprawdzania poprawności (regresja liniowa)
* Optymalizowanie modelu przy użyciu krzyżowego sprawdzania poprawności i parametrów sprawdzaniu za pomocą funkcji CrossValidator Spark ML (Klasyfikacja binarna)
* Optymalizowanie modelu przy użyciu niestandardowego kodu krzyżowego sprawdzania poprawności i zaczynają parametr używać dowolnej maszyny uczenia zestaw funkcji i parametrów (regresja liniowa)

**Krzyżowa Weryfikacja** to technika, która ocenia, jak dobrze modelu skonfigurowanych pod kątem w znanego zestawu danych będzie uogólnić do prognozowania funkcje zestawów danych, które go nie przeprowadzono. Ogólnej idei ta technika jest, czy model jest uczony w zestawie danych znanych danych, a następnie dokładność jej prognozy są testowane w odniesieniu do niezależnego zestawu danych. Standardowa implementacja polega ona na dzieleniu zestawu danych do *k*-składa, a następnie szkolenie modelu w okrężne we wszystkich oprócz jednego złożeń.

**Optymalizacja parametr Hyper** problemu wybierania zestaw hyper parametrów dla algorytmu uczenia, zwykle z celem miary wydajności tego algorytmu o zestaw danych, niezależnie od optymalizacji. Funkcji hyper parametru jest wartość, należy określić poza procedury szkolenia modelu. Założeń o wartościach parametrów może mieć wpływ na elastyczność i dokładności modelu. Drzewa decyzyjne mają hiper parametrami, na przykład, takich jak żądany głębi i liczba pozostawia w drzewie. Należy ustawić termin spadek błędu klasyfikacji dla maszyny wektor pomocy technicznej (SVM).

Typowym sposobem wykonania optymalizacji parametrów jest użycie wyszukiwania siatki, nazywany również **parametrów**. W wyszukiwaniu siatki kompleksowe przeszukiwanie odbywa się za pośrednictwem wartości określony podzbiór miejsce parametrów algorytmu uczenia. Krzyżowa Weryfikacja, można podać Metryka wydajności, aby posortować optymalne wyniki generowane przez algorytm wyszukiwania siatki. Jeśli używasz zaczynają parametrów krzyżowa Weryfikacja, możesz pomóc limit problemów, takich jak overfitting modelu do danych treningowych. Dzięki temu model zachowuje zdolności do zastosowania do ogólne zestawu danych, z którego został wyodrębniony dane szkoleniowe.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optymalizowanie modelu regresji liniowej z zaczynają parametrów
Następnie Podziel dane na szkolenie i sprawdzanie poprawności zestawów, użyj parametrów sprawdzaniu na zestaw szkoleniowy, optymalizowanie modelu w celu oceny na zestawie sprawdzania poprawności (regresja liniowa).

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # RENAME `tip_amount` AS A LABEL
    val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    OneHotTRAINLabeled.cache()
    OneHotTESTLabeled.cache()

    # DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
    val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

    # DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
    val trainPct = 0.75
    val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = trainValidationSplit.fit(OneHotTRAINLabeled)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    println("Test R-sqr is: " + Test_R2);


**Dane wyjściowe:**

Test R-sqr jest: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optymalizuj model klasyfikacji binarnej za pomocą zaczynają krzyżowego sprawdzania poprawności i parametrów
W tej sekcji dowiesz się, jak optymalizować model klasyfikacji binarnej przy użyciu zaczynają krzyżowego sprawdzania poprawności i parametrów. Ta metoda korzysta z uczenia Maszynowego platformy Spark `CrossValidator` funkcji.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
    val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    indexedTRAINwithCatFeatBinTargetRF.cache()
    indexedTESTwithCatFeatBinTargetRF.cache()

    # DEFINE THE ESTIMATOR FUNCTION
    val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

    # SPECIFY THE NUMBER OF FOLDS
    val numFolds = 3

    # DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
    val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

    # COMPUTE THE TEST F1 SCORE
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Dane wyjściowe:**

Czas, aby uruchomić komórkę: 33 sekundy.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Optymalizacja uczenia modelu regresji liniowej przy użyciu niestandardowego kodu krzyżowego sprawdzania poprawności i zaczynają parametru
Następnie zoptymalizować modelu za pomocą kodu niestandardowego i zidentyfikować najlepszych parametrów modelu przy użyciu kryterium największej dokładności. Następnie utwórz końcowego modelu, ocena modelu na danych testowych i zapisywanie modelu w usłudze Blob storage. Na koniec załadować modelu, ocenianie danych testowych i ocenić dokładności.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

    val nFolds = 3
    val numModels = paramGrid.size
    val numParamsinGrid = 2

    # SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
    val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    var maxDepth = -1
    var numTrees = -1
    var param = ""
    var paramval = -1
    var validateLB = -1.0
    var validateUB = -1.0
    val h = 1.0 / nFolds;
    val RMSE  = Array.fill(numModels)(0.0)

    # CREATE K-FOLDS
    val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


    # LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
    for (i <- 0 to (nFolds-1)) {
        validateLB = i * h
        validateUB = (i + 1) * h
        val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
        val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
        val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        validationLabPt.cache()
        trainCVLabPt.cache()

        for (nParamSets <- 0 to (numModels-1)) {
            for (nParams <- 0 to (numParamsinGrid-1)) {
                param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
                paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
                if (param == "maxDepth") {maxDepth = paramval}
                if (param == "numTrees") {numTrees = paramval}
            }
            val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=numTrees, maxDepth=maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)
            val labelAndPreds = validationLabPt.map { point =>
                                                     val prediction = rfModel.predict(point.features)
                                                     ( prediction, point.label )
                                                    }
            val validMetrics = new RegressionMetrics(labelAndPreds)
            val rmse = validMetrics.rootMeanSquaredError
            RMSE(nParamSets) += rmse
        }
        validationLabPt.unpersist();
        trainCVLabPt.unpersist();
    }
    val minRMSEindex = RMSE.indexOf(RMSE.min)

    # GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
    var best_maxDepth = -1
    var best_numTrees = -1
    for (nParams <- 0 to (numParamsinGrid-1)) {
        param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
        paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
        if (param == "maxDepth") {best_maxDepth = paramval}
        if (param == "numTrees") {best_numTrees = paramval}
    }

    # CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
    val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)

    # SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "BestCV_RF_Regression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    best_rfModel.save(sc, filename);

    # PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = best_rfModel.predict(point.features)
                                            ( prediction, point.label )
                                           }

    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


    # LOAD THE MODEL
    val savedRFModel = RandomForestModel.load(sc, filename)

    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = savedRFModel.predict(point.features)
                                            ( prediction, point.label )
                                           }
    # TEST THE MODEL
    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**Dane wyjściowe:**

Czas, aby uruchomić komórkę: 61 sekund.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Używanie modeli uczenia maszynowego platformy Spark utworzonych automatycznie przy użyciu języka Scala
Aby uzyskać omówienie tematów, które przeprowadzą Cię przez zadania, wchodzące w skład procesu do nauki o danych na platformie Azure, zobacz [zespołu danych dla celów naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

[Wskazówki dotyczące procesu do nauki o danych zespołu](walkthroughs.md) opisuje innych instruktaży end-to-end, które przedstawiają kroki w procesie nauki o danych zespołu w określonych scenariuszach. Przewodniki pokazują również sposób łączenia w chmurze i lokalnych narzędzi i usług w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji.

[Ocenianie modeli uczenia maszynowego utworzonych na platformie Spark](spark-model-consumption.md) dowiesz się, jak za pomocą kodu z języka Scala automatycznie ładować i oceniać nowe zestawy danych przy użyciu modeli usługi machine learning, wbudowane w platformy Spark i zapisane w usłudze Azure Blob storage. Postępuj zgodnie z instrukcjami, pod warunkiem że i po prostu Zamień na kod języka Python Scala kod w tym artykule zużycia automatycznych.

