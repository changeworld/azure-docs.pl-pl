---
title: Nauka o danych przy użyciu scala i spark na platformie Azure — proces nauki o danych zespołu
description: Jak używać Scali do nadzorowanych zadań uczenia maszynowego z pakietami MLlib i Spark ML w klastrze platformy Azure HDInsight Spark.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b36a3faab49ee8d51c25aa18879e6f5d1db8c2fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76716762"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Analiza danych przy użyciu języka Scala i platformy Spark na platformie Azure
W tym artykule pokazano, jak używać Scala do nadzorowanych zadań uczenia maszynowego z pakietami MLlib skalowalne MLlib i Spark ML w klastrze platformy Azure HDInsight Spark. Przeprowadzi Cię przez zadania, które tworzą [proces data science:](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)pozyskiwania i eksploracji danych, wizualizacji, inżynierii funkcji, modelowania i zużycia modelu. Modele w tym artykule obejmują regresję logistyczną i liniową, losowe lasy i drzewa z gradientem (GBT), oprócz dwóch typowych nadzorowanych zadań uczenia maszynowego:

* Problem regresji: Przewidywanie kwoty końcówki ($) dla podróży taksówką
* Klasyfikacja binarna: Przewidywanie końcówki lub braku końcówki (1/0) na wycieczkę taksówką

Proces modelowania wymaga szkolenia i oceny na zestaw danych testowych i odpowiednie metryki dokładności. W tym artykule dowiesz się, jak przechowywać te modele w magazynie obiektów Blob platformy Azure oraz jak oceniać i oceniać ich wydajność predykcyjną. W tym artykule opisano również bardziej zaawansowane tematy dotyczące optymalizacji modeli przy użyciu krzyżowego sprawdzania poprawności i przesączania hiperparametrów. Wykorzystane dane to próbka taksówki 2013 NYC i zestaw danych taryfy dostępne na GitHub.

[Scala](https://www.scala-lang.org/), język oparty na maszynie wirtualnej Java, integruje zorientowane obiektowo i funkcjonalne pojęcia językowe. Jest to skalowalny język, który doskonale nadaje się do przetwarzania rozproszonego w chmurze i działa w klastrach platformy Azure Spark.

[Spark](https://spark.apache.org/) to struktura przetwarzania równoległego typu open source, która obsługuje przetwarzanie w pamięci w celu zwiększenia wydajności aplikacji do analizy dużych zbiorów danych. Silnik przetwarzania Spark został stworzony z myślą o szybkości, łatwości obsługi i zaawansowanej analizie. Rozproszone możliwości obliczeniowe firmy Spark w pamięci sprawiają, że jest to dobry wybór dla algorytmów iteracyjnych w uczeniu maszynowym i obliczeniach wykresów. Pakiet [spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) zawiera jednolity zestaw interfejsów API wysokiego poziomu opartych na ramkach danych, które mogą pomóc w tworzeniu i dostrojeniu praktycznych potoków uczenia maszynowego. [MLlib](https://spark.apache.org/mllib/) to skalowalna biblioteka uczenia maszynowego firmy Spark, która zapewnia możliwości modelowania w tym rozproszonym środowisku.

[HdInsight Spark](../../hdinsight/spark/apache-spark-overview.md) to hostowana na platformie Azure oferta platformy Spark typu open source. Obejmuje również obsługę notesów Jupyter Scala w klastrze Platformy Spark i może uruchamiać interaktywne zapytania programu Spark SQL w celu przekształcania, filtrowania i wizualizowania danych przechowywanych w magazynie obiektów Blob platformy Azure. Fragmenty kodu Scala w tym artykule, które zapewniają rozwiązania i pokaż odpowiednie wykresy do wizualizacji danych uruchamianych w notesach Jupyter zainstalowanych w klastrach platformy Spark. Kroki modelowania w tych tematach mają kod, który pokazuje, jak trenować, oceniać, zapisywać i używać każdego typu modelu.

Kroki konfiguracji i kod w tym artykule są dla usługi Azure HDInsight 3.4 Spark 1.6. Jednak kod w tym artykule i w [Notesie Scala Jupyter](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) są ogólne i powinny działać na każdym klastrze Platformy Spark. Kroki konfiguracji klastra i zarządzania mogą się nieznacznie różnić od czynności przedstawionych w tym artykule, jeśli nie jest używany program HDInsight Spark.

> [!NOTE]
> W temacie, który pokazuje, jak używać języka Python zamiast Scala do wykonywania zadań dla kompleksowego procesu nauki o danych, zobacz [Nauki o danych przy użyciu platformy Spark w usłudze Azure HDInsight.](spark-overview.md)
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
* Wymagana jest subskrypcja platformy Azure. Jeśli jeszcze go nie masz, [uzyskaj bezpłatną wersję próbną platformy Azure.](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)
* Aby wykonać następujące procedury, potrzebujesz klastra usługi Azure HDInsight 3.4 Spark 1.6. Aby utworzyć klaster, zobacz instrukcje w [wprowadzenie: Tworzenie platformy Apache Spark w usłudze Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Ustaw typ i wersję klastra w menu **Wybierz typ klastra.**

![Konfiguracja typu klastra usługi HDInsight](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Aby uzyskać opis danych podróży taksówką NYC i instrukcje dotyczące wykonywania kodu z notesu Jupyter w klastrze Platformy Spark, zobacz odpowiednie sekcje [w omówienie nauki o danych przy użyciu platformy Spark na platformie Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Wykonywanie kodu Scala z notesu Jupyter w klastrze Spark
Notes Jupyter można uruchomić z witryny Azure Portal. Znajdź klaster platformy Spark na pulpicie nawigacyjnym, a następnie kliknij go, aby wejść na stronę zarządzania klastra. Następnie kliknij pozycję **Pulpity nawigacyjne klastra**, a następnie kliknij pozycję **Notes programu Jupyter,** aby otworzyć notes skojarzony z klastrem Spark.

![Pulpit nawigacyjny klastra i notesy Jupyter](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Można również uzyskać dostęp do notesów Jupyter w https://&lt;clustername&gt;.azurehdinsight.net/jupyter. Zastąp *nazwę klastra* nazwą klastra. Aby uzyskać dostęp do notesów Jupyter, potrzebujesz hasła do konta administratora.

![Przejdź do notesów Jupyter przy użyciu nazwy klastra](./media/scala-walkthrough/spark-jupyter-notebook.png)

Wybierz **scala,** aby wyświetlić katalog zawierający kilka przykładów paczkowanych notesów korzystających z interfejsu API PySpark. Program Exploration Modelowanie i ocenianie przy użyciu notesu Scala.ipynb zawierającego przykłady kodu dla tego pakietu tematów platformy Spark są dostępne w [usłudze GitHub.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala)

Notes można przekazać bezpośrednio z usługi GitHub na serwer notebooka Jupyter w klastrze platformy Spark. Na stronie głównej Jupyter kliknij przycisk **Przekaż.** W eksploratorze plików wklej adres URL usługi GitHub (zawartość nieprzetworzona) notesu Scala, a następnie kliknij przycisk **Otwórz**. Notes Scala jest dostępny pod następującym adresem URL:

[Eksploracja-Modelowanie i punktacja-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Konfiguracja: wstępnie ustawione konteksty iskry i ula, magia iskry i biblioteki iskier
### <a name="preset-spark-and-hive-contexts"></a>Wstępnie ustawione konteksty iskry i gałęzi
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Jądra Spark, które są dostarczane z notesów Jupyter mają wstępnie ustawione konteksty. Nie trzeba jawnie ustawić konteksty Spark lub Hive przed rozpoczęciem pracy z aplikacją, którą tworzysz. Wstępnie ustawione konteksty to:

* `sc`dla SparkContext
* `sqlContext`dla HiveContext

### <a name="spark-magics"></a>Magia iskry
Jądro Spark zawiera kilka wstępnie zdefiniowanych "magii", które są specjalnymi poleceniami, które można wywołać za pomocą `%%`. Dwa z tych poleceń są używane w następujących przykładach kodu.

* `%%local`określa, że kod w kolejnych wierszach będzie wykonywany lokalnie. Kod musi być prawidłowy kod Scala.
* `%%sql -o <variable name>`wykonuje kwerendę hive `sqlContext`względem . Jeśli `-o` parametr jest przekazywany, wynik kwerendy `%%local` jest zachowywany w kontekście Scali jako ramki danych Platformy Spark.

Aby uzyskać więcej informacji na temat jąder notebooków Jupyter i ich wstępnie `%%` zdefiniowanych "magii", z którymi dzwonisz (na `%%local`przykład), zobacz [Jądra dostępne dla notebooków Jupyter z klastrami HDInsight Spark Linux w programie HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Importowanie bibliotek
Zaimportuj iskrę, MLlib i inne biblioteki, które będą potrzebne przy użyciu następującego kodu.

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
Pierwszym krokiem w procesie nauki o danych jest pozyskiwania danych, które chcesz analizować. Przenieś dane z zewnętrznych źródeł lub systemów, w których znajdują się w środowisku eksploracji i modelowania danych. W tym artykule dane, które pojechasz jest połączony 0,1% próbki podróży taksówką i pliku taryfy (przechowywane jako plik .tsv). Środowisko eksploracji i modelowania danych to Spark. Ta sekcja zawiera kod do wykonania następującej serii zadań:

1. Ustawianie ścieżek katalogów dla przechowywania danych i modeli.
2. Odczyt w zestawie danych wejściowych (przechowywanych jako plik tsv).
3. Zdefiniuj schemat danych i wyczyść dane.
4. Utwórz oczyszczoną ramkę danych i buforuj ją w pamięci.
5. Zarejestruj dane jako tabelę tymczasową w SQLContext.
6. Kwerenda tabela i zaimportować wyniki do ramki danych.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Ustawianie ścieżek katalogów dla lokalizacji magazynu w magazynie obiektów Blob platformy Azure
Platforma Spark może odczytywać i zapisywać w magazynie obiektów blob platformy Azure. Za pomocą platformy Spark można przetwarzać dowolne istniejące dane, a następnie ponownie przechowywać wyniki w magazynie obiektów Blob.

Aby zapisać modele lub pliki w magazynie obiektów Blob, należy poprawnie określić ścieżkę. Odwołaj się do domyślnego kontenera dołączonego do `wasb:///`klastra Platformy Spark przy użyciu ścieżki, która zaczyna się od programu . Odwoływać się `wasb://`do innych lokalizacji za pomocą programu .

Poniższy przykładowy kod określa lokalizację danych wejściowych do odczytu i ścieżkę do magazynu obiektów Blob, który jest dołączony do klastra platformy Spark, w którym model zostanie zapisany.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importowanie danych, tworzenie rdd i definiowanie ramki danych zgodnie ze schematem
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

Czas uruchomienia komórki: 8 sekund.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Kwerenda tabela i import wyników w ramce danych
Następnie przeszukuj tabelę dotyczącą danych taryfy, pasażera i porad; odfiltrowywanie uszkodzonych i odsysających danych; i wydrukować kilka wierszy.

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

| fare_amount | passenger_count | tip_amount | Wyrzucenia |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Eksploracja i wizualizacja danych
Po przesunieniu danych do platformy Spark następnym krokiem w procesie nauki o danych jest uzyskanie głębszego zrozumienia danych za pomocą eksploracji i wizualizacji. W tej sekcji można sprawdzić dane taksówki przy użyciu zapytań SQL. Następnie zaimportuj wyniki do ramki danych, aby wykreślić zmienne docelowe i potencjalne obiekty do kontroli wizualnej przy użyciu funkcji automatycznej wizualizacji Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Użyj magii lokalnej i SQL, aby wykreślić dane
Domyślnie dane wyjściowe dowolnego fragmentu kodu uruchamianego z notesu Jupyter jest dostępny w kontekście sesji, która jest utrwalona w węzłach procesu roboczego. Jeśli chcesz zapisać podróż do węzłów procesu roboczego dla każdego obliczenia i jeśli wszystkie dane potrzebne do obliczeń są dostępne lokalnie w węźle `%%local` serwera Jupyter (który jest węzłem głównym), można użyć magii do uruchomienia fragmentu kodu na serwerze Jupyter.

* **Magia** `%%sql`SQL ( ). Jądro HDInsight Spark obsługuje łatwe wbudowane zapytania hiveQl przeciwko SQLContext. Argument`-o VARIABLE_NAME`( ) utrzymuje dane wyjściowe kwerendy SQL jako ramki danych Pandas na serwerze Jupyter. To ustawienie oznacza, że dane wyjściowe będą dostępne w trybie lokalnym.
* `%%local`**magia**. Magia `%%local` uruchamia kod lokalnie na serwerze Jupyter, który jest węzłem głównym klastra HDInsight. Zazwyczaj używasz `%%local` magii w `%%sql` połączeniu z `-o` magią z parametrem. Parametr `-o` będzie zachowywać dane wyjściowe kwerendy `%%local` SQL lokalnie, a następnie magic wyzwoli następny zestaw fragment kodu do uruchomienia lokalnie względem danych wyjściowych zapytań SQL, który jest utrwalony lokalnie.

### <a name="query-the-data-by-using-sql"></a>Zapytanie o dane przy użyciu języka SQL
To zapytanie pobiera przejazdy taksówką według kwoty taryfy, liczby pasażerów i kwoty napiwku.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

W poniższym kodzie `%%local` magic tworzy lokalną ramkę danych sqlResults. Za pomocą sqlResults można wykreślić za pomocą matplotlib.

> [!TIP]
> Lokalna magia jest używana wiele razy w tym artykule. Jeśli zestaw danych jest duży, należy spróbować, aby utworzyć ramkę danych, która może zmieścić się w pamięci lokalnej.
> 
> 

### <a name="plot-the-data"></a>Wykreślanie danych
Można wykreślić przy użyciu kodu Pythona po ramce danych jest w kontekście lokalnym jako ramki danych Pandas.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 Jądro Spark automatycznie wizualizuje dane wyjściowe zapytań SQL (HiveQL) po uruchomieniu kodu. Można wybrać jeden z kilku typów wizualizacji:

* Tabela
* Kołowy
* Wiersz
* Obszar
* Słupkowy

Oto kod do wykreślenia danych:

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

![Histogram kwoty końcówki](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Kwota napiwku według liczby pasażerów](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Kwota napiwku według kwoty taryfy](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Tworzenie funkcji i przekształcanie funkcji, a następnie przygotowywanie danych do wprowadzania do funkcji modelowania
W przypadku funkcji modelowania opartego na drzewach z platformy Spark ML i MLlib należy przygotować obiekt docelowy i funkcje przy użyciu różnych technik, takich jak binning, indeksowanie, kodowanie na gorąco i wektoryzacja. Oto procedury, których należy przestrzegać w tej sekcji:

1. Utwórz nową funkcję przez **binning** godzin w zasobniku czasu ruchu.
2. Zastosuj **indeksowanie i kodowanie jedno-gorące** do funkcji kategorycznych.
3. **Próbka i podzielić zestaw danych na** części szkoleniowe i testowe.
4. **Określ zmienną szkoleniową i funkcje**, a następnie utwórz indeksowane lub jedno-gorące zakodowane szkolenia i testowanie danych wejściowych oznaczonych punktami odpornych na rozproszone zestawy danych (RDD) lub ramek danych.
5. Automatyczne **kategoryzowanie i wektoryzowanie funkcji i obiektów docelowych** do użycia jako dane wejściowe dla modeli uczenia maszynowego.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Tworzenie nowej funkcji przez godziny binningu w zasobniku czasu ruchu
Ten kod pokazuje, jak utworzyć nową funkcję przez binning godzin w zasobników czasu ruchu i jak buforować wynikową ramkę danych w pamięci. Gdzie RDD i ramki danych są używane wielokrotnie, buforowanie prowadzi do wydłużonego czasu wykonywania. W związku z tym będziesz buforować rdds i ramki danych na kilku etapach w poniższych procedurach.

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


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indeksowanie i jedno-gorące kodowanie funkcji kategorycznych
Modelowanie i przewidywanie funkcji MLlib wymagają funkcji z kategorycznych danych wejściowych, które mają być indeksowane lub kodowane przed użyciem. W tej sekcji pokazano, jak indeksować lub kodować funkcje kategoryczne do wprowadzania danych do funkcji modelowania.

Należy indeksować lub kodować modele na różne sposoby, w zależności od modelu. Na przykład modele regresji logistycznej i liniowej wymagają kodowania na gorąco. Na przykład operację z trzema kategoriami można rozszerzyć na trzy kolumny obiektowe. Każda kolumna będzie zawierać 0 lub 1 w zależności od kategorii obserwacji. MLlib udostępnia [onehotencoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkcji do kodowania na gorąco. Ten koder mapuje kolumnę indeksów etykiet do kolumny wektorów binarnych z co najwyżej jedną jedną wartością. Za pomocą tego kodowania algorytmy, które oczekują numeryczne wartościowe funkcje, takie jak regresja logistyczna, mogą być stosowane do funkcji kategorycznych.

W tym miejscu można przekształcić tylko cztery zmienne, aby pokazać przykłady, które są ciągi znaków. Można również indeksować inne zmienne, takie jak dzień tygodnia, reprezentowane przez wartości liczbowe, jako zmienne kategoryczne.

Do indeksowania, `StringIndexer()`używania i kodowania na `OneHotEncoder()` gorąco należy używać funkcji mllib. Oto kod do indeksowania i kodowania funkcji kategorycznych:

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

Czas uruchomienia komórki: 4 sekundy.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Próbka i podzielenie zestawu danych na frakcje treningowe i testowe
Ten kod tworzy losowe próbkowanie danych (25%, w tym przykładzie). Chociaż próbkowanie nie jest wymagane w tym przykładzie ze względu na rozmiar zestawu danych, w artykule pokazano, jak można próbkować, dzięki czemu wiesz, jak go używać do własnych problemów, gdy jest to potrzebne. Gdy próbki są duże, może to zaoszczędzić dużo czasu podczas szkolenia modeli. Następnie podziel próbkę na część szkoleniową (75%, w tym przykładzie) i część testową (25%, w tym przykładzie), aby użyć jej w modelowaniu klasyfikacji i regresji.

Dodaj liczbę losową (od 0 do 1) do każdego wiersza (w kolumnie "rand"), która może służyć do wybierania fałd sprawdzania krzyżowej podczas szkolenia.

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

Czas uruchomienia komórki: 2 sekundy.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Określ zmienną szkoleniową i funkcje, a następnie utwórz indeksowane lub zakodowane na gorąco szkolenia i testowanie danych wejściowych oznaczonych rdd lub ramek danych
Ta sekcja zawiera kod, który pokazuje, jak indeksować dane tekstowe kategoryczne jako typ danych oznaczonych punkt i zakodować go, dzięki czemu można go używać do szkolenia i testowania regresji logistycznej MLlib i innych modeli klasyfikacji. Obiekty punktowe oznaczone etykietami są RDD, które są sformatowane w sposób, który jest potrzebny jako dane wejściowe przez większość algorytmów uczenia maszynowego w MLlib. [Oznaczony punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) jest wektorem lokalnym, gęstym lub rzadkim, skojarzonym z etykietą/odpowiedzią.

W tym kodzie należy określić zmienną docelową (zależną) i funkcje używane do szkolenia modeli. Następnie należy utworzyć indeksowane lub jedno-gorące zakodowane szkolenia i testowania danych wejściowych oznaczonych rdds punkt lub ramki danych.

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

Czas uruchomienia komórki: 4 sekundy.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Automatyczne kategoryzowanie i wektoryzowanie funkcji i obiektów docelowych do użycia jako wejścia dla modeli uczenia maszynowego
Użyj platformy Spark ML, aby kategoryzować obiekt docelowy i funkcje używane w funkcjach modelowania opartego na drzewach. Kod kończy dwa zadania:

* Tworzy binarny obiekt docelowy klasyfikacji, przypisując wartość 0 lub 1 do każdego punktu danych między 0 a 1 przy użyciu wartości progowej 0,5.
* Automatycznie kategoryzuje operacje. Jeśli liczba różnych wartości liczbowych dla dowolnej operacji jest mniejsza niż 32, ta funkcja jest klasyfikowana.

Oto kod dla tych dwóch zadań.

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



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Binarny model klasyfikacji: Wytypuj, czy wskazówka powinna być płatna
W tej sekcji utworzysz trzy typy modeli klasyfikacji binarnej, aby przewidzieć, czy wskazówka powinna być płatna:

* **Model regresji logistycznej** przy `LogisticRegression()` użyciu funkcji Spark ML
* **Losowy model klasyfikacji lasów** przy `RandomForestClassifier()` użyciu funkcji Spark ML
* **Gradient zwiększa model klasyfikacji drzewa** przy użyciu `GradientBoostedTrees()` funkcji MLlib

### <a name="create-a-logistic-regression-model"></a>Tworzenie modelu regresji logistycznej
Następnie należy utworzyć model regresji logistycznej `LogisticRegression()` przy użyciu funkcji platformy Spark ML. Kod budynku modelu można utworzyć w serii kroków:

1. **Trenuj** dane modelu za pomocą jednego zestawu parametrów.
2. **Oceń model** na zestawie danych testowych z metrykami.
3. **Zapisz model** w magazynie obiektów Blob do przyszłego zużycia.
4. **Ocena modelu względem** danych testowych.
5. **Wykreślić wyniki** za pomocą krzywych charakterystyki charakterystycznej dla odbiornika (ROC).

Oto kod dla tych procedur:

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

Wczytaj, wynik i zapisz wyniki.

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

ROC na danych testowych = 0,9827381497557599

Użyj języka Python w lokalnych ramkach danych Pandas, aby wykreślić krzywą ROC.

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

![Krzywa ROC końcówki lub bez końcówki](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Tworzenie modelu klasyfikacji lasów losowych
Następnie należy utworzyć losowy model klasyfikacji `RandomForestClassifier()` lasu przy użyciu funkcji platformy Spark ML, a następnie ocenić model na podstawie danych testowych.

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

ROC na danych testowych = 0,9847103571552683

### <a name="create-a-gbt-classification-model"></a>Tworzenie modelu klasyfikacji GBT
Następnie należy utworzyć model klasyfikacji GBT przy `GradientBoostedTrees()` użyciu funkcji MLlib, a następnie ocenić model na danych testowych.

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

Powierzchnia pod krzywą ROC: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Model regresji: przewidywanie kwoty końcówki
W tej sekcji utworzysz dwa typy modeli regresji, aby przewidzieć kwotę końcówki:

* **Zasiedniuzowany model regresji liniowej** przy użyciu funkcji Spark ML. `LinearRegression()` Zapiszesz model i ocenisz model na podstawie danych testowych.
* **Model regresji drzewa zwiększający gradient** przy `GBTRegressor()` użyciu funkcji platformy Spark ML.

### <a name="create-a-regularized-linear-regression-model"></a>Tworzenie uregulowanego modelu regresji liniowej
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

Czas uruchomienia komórki: 13 sekund.

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

Następnie zapytaj wyniki testu jako ramkę danych i użyj autovizWidget i matplotlib, aby ją wizualizować.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

Kod tworzy lokalną ramkę danych z danych wyjściowych kwerendy i kreśli dane. Magia `%%local` tworzy lokalną ramkę danych, `sqlResults`której można użyć do wykreślenia za pomocą matplotlib.

> [!NOTE]
> Ta magia Spark jest używana wiele razy w tym artykule. Jeśli ilość danych jest duża, należy próbkować, aby utworzyć ramkę danych, która może zmieścić się w pamięci lokalnej.
> 
> 

Tworzenie wykresów przy użyciu python matplotlib.

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

![Ilość porad: Rzeczywista a przewidywana](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Tworzenie modelu regresji GBT
Utwórz model regresji GBT `GBTRegressor()` przy użyciu funkcji platformy Spark ML, a następnie oceń model na podstawie danych testowych.

[Drzewa z gradientem](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) są zespołami drzew decyzyjnych. GBTS trenuje drzewa decyzyjne iteracyjne, aby zminimalizować funkcję utraty. Można użyć GBTS do regresji i klasyfikacji. Mogą obsługiwać funkcje kategoryczne, nie wymagają skalowania operacji i mogą przechwytywać nieliniowe i interakcje funkcji. Można ich również używać w ustawieniu klasyfikacji wieloklasowej.

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

## <a name="advanced-modeling-utilities-for-optimization"></a>Zaawansowane narzędzia do modelowania do optymalizacji
W tej sekcji używasz narzędzi uczenia maszynowego, które deweloperzy często używają do optymalizacji modelu. W szczególności można zoptymalizować modele uczenia maszynowego na trzy różne sposoby przy użyciu parametrów zamiatanie i krzyżowe sprawdzanie poprawności:

* Podziel dane na zestawy pociągów i sprawdzania poprawności, zoptymalizuj model przy użyciu hiperparametrów w zestawie szkoleniowym i oceń zestaw sprawdzania poprawności (regresja liniowa)
* Optymalizuj model przy użyciu krzyżowego sprawdzania poprawności i przesączania hiperparametrów przy użyciu funkcji CrossValidator platformy Spark ML (klasyfikacja binarna)
* Optymalizuj model przy użyciu niestandardowego kodu sprawdzania poprawności krzyżowej i zamiatanie parametrów, aby użyć dowolnej funkcji uczenia maszynowego i zestawu parametrów (regresja liniowa)

**Krzyżowe sprawdzanie poprawności** jest techniką, która ocenia, jak dobrze model przeszkolony na znany zestaw danych będzie uogólniać do przewidywania funkcji zestawów danych, na których nie został przeszkolony. Ogólną ideą tej techniki jest to, że model jest przeszkolony na zestaw danych znanych danych, a następnie dokładność jego prognoz jest testowany na niezależny zestaw danych. Wspólną implementacją jest podzielenie zestawu danych na *k*-folds, a następnie trenowanie modelu w sposób okrężny na wszystkich, z ale jednym z fałd.

**Optymalizacja hiperparametrów** jest problemem wyboru zestawu hipermetro parametrów dla algorytmu uczenia się, zwykle w celu optymalizacji miary wydajności algorytmu na niezależnym zestawie danych. Hiperparametr jest wartością, którą należy określić poza procedurą szkolenia modelu. Założenia dotyczące wartości hiperparametrów mogą mieć wpływ na elastyczność i dokładność modelu. Drzewa decyzyjne mają hiper-parametry, na przykład, takie jak żądana głębokość i liczba liści w drzewie. Należy ustawić termin kary błędnej klasyfikacji dla maszyny wektorowej wsparcia (SVM).

Typowym sposobem optymalizacji hiperparametrów jest użycie wyszukiwania w siatce, zwanego również **wyciągnięciem po parametrach**. W wyszukiwaniu siatki wyszukiwanie jest wykonywane za pomocą wartości określonego podzbioru przestrzeni hiperparametrycznego dla algorytmu uczenia się. Krzyżowe sprawdzanie poprawności może dostarczyć metryki wydajności, aby uporządkować optymalne wyniki uzyskane przez algorytm wyszukiwania siatki. Jeśli używasz krzyżowego sprawdzania poprawności hiperparametrów zamiatanie, można ograniczyć problemy, takie jak overfitting modelu do danych szkoleniowych. W ten sposób model zachowuje zdolność do zastosowania do ogólnego zestawu danych, z których wyodrębniono dane szkoleniowe.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optymalizacja modelu regresji liniowej za pomocą przesączania hiperparametrów
Następnie należy podzielić dane na zestawy pociągów i sprawdzania poprawności, użyj hiperparametru zamiatanie na zestaw szkolenia do optymalizacji modelu i oceny na zestaw sprawdzania poprawności (regresji liniowej).

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

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optymalizacja modelu klasyfikacji binarnej przy użyciu krzyżowej weryfikacji i przesączania hiperparametrów
W tej sekcji pokazano, jak zoptymalizować model klasyfikacji binarnej przy użyciu krzyżowego sprawdzania poprawności i hiperparametrów zamiatanie. Spowoduje to użycie `CrossValidator` funkcji Platformy Spark ML.

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

Czas uruchomienia komórki: 33 sekundy.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Optymalizacja modelu regresji liniowej przy użyciu niestandardowego kodu sprawdzania poprawności krzyżowej i zamiatanie parametrów
Następnie należy zoptymalizować model przy użyciu kodu niestandardowego i zidentyfikować najlepsze parametry modelu przy użyciu kryterium najwyższej dokładności. Następnie utwórz ostateczny model, oceń model na test danych i zapisz model w magazynie obiektów Blob. Na koniec załaduj model, wynik danych testowych i oceń dokładność.

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

Czas uruchomienia komórki: 61 sekund.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Korzystaj z modeli uczenia maszynowego stworzonego przez spark automatycznie dzięki firmie Scala
Aby uzyskać omówienie tematów, które przenikają przez zadania, które obejmują proces nauki o danych na platformie Azure, zobacz [Proces nauki o danych zespołu.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

[Przewodniki proces nauki o danych zespołu](walkthroughs.md) opisano inne wskazówki end-to-end, które pokazują kroki w procesie nauki o danych zespołu dla określonych scenariuszy. W przewodnikach pokazano również, jak połączyć narzędzia i usługi w chmurze i lokalnie w przepływ pracy lub potok, aby utworzyć inteligentną aplikację.

[Wyniki oparte na modelach uczenia maszynowego utworzonych przez platformę Spark](spark-model-consumption.md) pokazują, jak używać kodu Scala do automatycznego ładowania i oceniania nowych zestawów danych za pomocą modeli uczenia maszynowego wbudowanych w platformę Spark i zapisanych w magazynie obiektów Blob platformy Azure. Możesz postępować zgodnie z instrukcjami tam i po prostu zastąpić kod Pythona kodem Scala w tym artykule do automatycznego zużycia.

