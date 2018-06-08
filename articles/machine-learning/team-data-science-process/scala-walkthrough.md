---
title: Nauki danych przy użyciu języka Scala i Spark na platformie Azure | Dokumentacja firmy Microsoft
description: Jak używać języka Scala dla zadania uczenia nadzorowanego maszyny z Spark skalowalne MLlib i Spark ML pakiety w klastrze usługi Azure HDInsight Spark.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: a7c97153-583e-48fe-b301-365123db3780
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 16e4af4dd7f5c2bd14d70cc28225dfc750ce3bea
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838514"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Analiza danych przy użyciu języka Scala i platformy Spark na platformie Azure
W tym artykule przedstawiono sposób użycia Scala dla zadania uczenia nadzorowanego maszyny z Spark skalowalne MLlib i Spark ML pakiety w klastrze usługi Azure HDInsight Spark. Przeprowadza użytkownika przez zadania, które stanowią [procesu nauki danych](http://aka.ms/datascienceprocess): wprowadzanie danych i eksploracja, wizualizacji engineering funkcji, modelowania i zużycia modelu. Modele w artykule obejmują Regresja logistyczna i liniowych, losowe lasów i boosted gradientu drzew (GBTs), oprócz dwie typowe zadania uczenia nadzorowanego maszyny:

* Problem regresji: prognozowania kwota tip ($) w podróży taksówki
* Klasyfikacji binarnej: prognozowania porada lub brak porady w podróży taksówki (1/0)

Proces modelowania wymaga uczenie i Ewaluacja testowego zestawu danych i metryk odpowiednich dokładności. W tym artykule można Dowiedz się, jak do przechowywania tych modeli w magazynie obiektów Blob platformy Azure oraz jak wynik i ocena wydajności predykcyjnej. W tym artykule omówiono także bardziej zaawansowanych tematów dotyczących sposobu zoptymalizować modele za pomocą kominów krzyżowego sprawdzania poprawności i parametru funkcji hyper. Dane używane jest przykładowe 2013 NYC taksówki podróży i taryfy zestawu danych dostępne w witrynie GitHub.

[Scala](http://www.scala-lang.org/), funkcjonalności i zorientowany obiektowo język pojęcia integruje się język oparty na maszynie wirtualnej Java. Jest skalowalna język dobrze nadają się do rozproszonego przetwarzania w chmurze, która działa w klastrze Spark w usłudze Azure.

[Platforma Spark](http://spark.apache.org/) jest platforma przetwarzania równoległego open source, która obsługuje przetwarzanie w pamięci w celu zwiększania wydajności aplikacji do analizy danych big data. Aparat przetwarzania Spark zaprojektowano pod kątem szybkości, łatwości użycia i zaawansowanych możliwości analitycznych. Możliwości rozproszone obliczenia w pamięci platforma Spark stanowić dobry wybór w przypadku algorytmów iteracyjnych używanych w machine learning i obliczeniach na grafach. [Spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) pakiet zawiera uniform zestaw interfejsów API wysokiego poziomu, rozszerzający danych ramek, które ułatwiają tworzenie i dostrajania praktyczne machine learning potoków. [MLlib](http://spark.apache.org/mllib/) platforma Spark uczenia maszynowego Skalowalna biblioteka, udostępnia funkcje modelowania w tym środowisku rozproszonym.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) jest oferty hostowanymi na platformie Azure open source platformy Spark. On również obsługą notesów Jupyter Scala w klastrze Spark i można uruchomić interakcyjnych zapytań Spark SQL do przekształcania, filtrować i wizualizacji danych przechowywanych w magazynie obiektów Blob platformy Azure. Uruchom Scala fragmenty kodu w tym artykule dostarczające rozwiązań i Pokaż odpowiednich powierzchni do wizualizacji danych w notesach Jupyter zainstalowany w klastrze Spark. Kroki modelowania w tych tematach mają kodu, pokazujący sposób uczenia, ocenić, zapisywania i korzystać z każdego typu modelu.

Kroki instalacji i kodu w tym artykule dotyczą Azure HDInsight 3.4 Spark 1.6. Jednak kod w tym artykule i w [notesu Jupyter Scala](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) są ogólne i powinny działać na dowolnym klastra Spark. Kroki konfiguracji i zarządzania klastra mogą być nieco inne niż co to jest wyświetlany w tym artykule, jeśli nie używasz HDInsight Spark.

> [!NOTE]
> Dla tematu, pokazujący, jak używać języka Python, a nie Scala wykonywanie zadań w procesie nauki danych na całej trasie, zobacz [nauki danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
* Wymagana jest subskrypcja platformy Azure. Jeśli użytkownik nie ma jeszcze jeden, [uzyskać Azure bezpłatnej wersji próbnej](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Należy Azure HDInsight 3.4 Spark 1.6 klastra wykonaj następujące procedury. Aby utworzyć klaster, zobacz instrukcje w [wprowadzenie: tworzenie Apache Spark w usłudze Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Ustaw typ klastra i wersji na **wybierz typ klastra** menu.

![Konfiguracja typu klastra usługi HDInsight](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Opis NYC taksówki podróży danych i instrukcje dotyczące sposobu wykonania kodu z notesu Jupyter w klastrze Spark, zobacz w odpowiednich sekcjach [przegląd danych nauki używania platformy Spark w usłudze Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Wykonanie kodu języka Scala z notesu Jupyter w klastrze Spark
Można uruchomić notesu Jupyter z portalu Azure. Znajdź klastra Spark na pulpicie nawigacyjnym, a następnie kliknij go do wprowadź strony zarządzania dla klastra. Następnie kliknij przycisk **pulpitów nawigacyjnych klastrów**, a następnie kliknij przycisk **notesu Jupyter** otworzyć notesu skojarzony z klastrem Spark.

![Pulpit nawigacyjny klastra i notesów Jupyter](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Również można uzyskać dostęp notesów Jupyter w https://&lt;clustername&gt;.azurehdinsight.net/jupyter. Zastąp *clustername* z nazwą klastra. Potrzebne hasło dla konta administratora dostęp do notesów Jupyter.

![Przejdź do notesów Jupyter przy użyciu nazwy klastra](./media/scala-walkthrough/spark-jupyter-notebook.png)

Wybierz **Scala** wyświetlić katalog, który ma kilka przykładów opakowaniach jednostkowych notebooki, które korzystają z interfejsu API PySpark. Przykłady eksploracji modelowania i oceniania za pomocą notesu Scala.ipynb, który zawiera kod dla tego zestawu tematy Spark jest dostępna na [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

Możesz przekazać notesu bezpośrednio z serwisu GitHub do serwera notesu Jupyter w klastrze Spark. Na stronie głównej Jupyter kliknij **przekazać** przycisku. W Eksploratorze plików, wklej adres URL usługi GitHub (nieprzetworzonej zawartości) notesu Scala, a następnie kliknij przycisk **Otwórz**. Scala notesu jest dostępny pod adresem URL:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Instalatora: Konteksty ustawienie wstępne Spark i Hive, poleceń magicznych Spark i biblioteki Spark
### <a name="preset-spark-and-hive-contexts"></a>Konteksty Spark i Hive ustawień wstępnych.
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Jądra Spark, które są dostarczane z notesów Jupyter mają wstępnie kontekstach. Nie musisz jawnie ustawić Spark lub tworzenia kontekstów gałęzi, przed rozpoczęciem pracy z aplikacją. Predefiniowanych kontekstach są:

* `sc` dla SparkContext
* `sqlContext` dla HiveContext

### <a name="spark-magics"></a>Platforma Spark poleceń magicznych
Jądra Spark zawiera kilka wstępnie zdefiniowanych "poleceń magicznych", które są specjalne polecenia, które można wywoływać z `%%`. Dwa z tych poleceń są używane w następujących przykładach kodu.

* `%%local` Określa lokalnie wykonać kod w kolejnych wierszach. Kod musi być prawidłowy kod języka Scala.
* `%%sql -o <variable name>` wykonuje zapytanie Hive względem `sqlContext`. Jeśli `-o` parametr jest przekazywany, w wyniku zapytania jest trwały `%%local` kontekstu Scala jako ramki danych platformy Spark.

Dla więcej informacji na temat jądra notesów Jupyter i ich wstępnie zdefiniowanych "magics" która zostanie wywołana za pomocą `%%` (na przykład `%%local`), zobacz [jądra dostępne dla notesu Jupyter klastrze HDInsight Spark w systemie Linux klastrów HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Importuj biblioteki
Importowanie Spark, MLlib i innych bibliotek, które będą potrzebne przy użyciu następującego kodu.

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
Pierwszym krokiem w procesie nauki danych jest pozyskiwania danych, które mają być analizowane. Można przenosić dane ze źródeł zewnętrznych lub systemy, w którym znajduje się w środowisku eksploracji i modelowanie danych. W tym artykule dane, które można pozyskiwania jest połączone próbka 0,1% (przechowywane jako plik .tsv) pliku podróży i taryfy taksówki. Środowisko eksploracji i modelowanie danych jest Spark. Ta sekcja zawiera kod, aby wykonać poniższą sekwencję zadań:

1. Ustawianie ścieżki katalogu dla magazynu danych i modelu.
2. Odczyt w zestawie danych wejściowych (przechowywane jako plik .tsv).
3. Zdefiniuj schemat dla danych i czyszczenie danych.
4. Utwórz ramkę, oczyszczony danych i ją buforują w pamięci.
5. Rejestruje dane jako element SQLContext tabeli tymczasowej.
6. Zapytanie tabeli i zaimportuj wyniki do ramki danych.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Ustawianie ścieżki katalogu dla lokalizacji przechowywania w magazynie obiektów Blob platformy Azure
Platforma Spark można odczytu i zapisu do magazynu obiektów Blob Azure. Można używać platformy Spark można przetworzyć żadnych istniejących danych, a następnie ponownie zapisane wyniki w magazynie obiektów Blob.

Aby zapisać modele lub plików w magazynie obiektów Blob, należy poprawnie określić ścieżkę. Odwołanie dołączony do klastra Spark przy użyciu ścieżki, która rozpoczyna się od domyślnego kontenera `wasb:///`. Odwołania z innych lokalizacji za pomocą `wasb://`.

Poniższy przykład kodu określa lokalizacji danych wejściowych do odczytu i ścieżkę do magazynu obiektów Blob, który jest dołączony do klastra Spark, w którym zostanie zapisany modelu.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importuj dane, tworzyć RDD i zdefiniowanie ramki danych według schematu
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

Czas uruchamiania komórki: 8 sekund.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Zapytanie tabeli i importowanie wyników w ramce danych
Następnie zapytania tabeli taryfy, pasażerów i Porada danych; Filtrowanie danych uszkodzona i odległe mniejsze; i drukowanie kilka wierszy.

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

## <a name="data-exploration-and-visualization"></a>Eksploracja danych i wizualizacji
Po przywróceniu danych do platformy Spark następnym krokiem w procesie nauki danych jest lepiej zrozumieć dane za pośrednictwem eksploracji i wizualizacji. W tej sekcji należy zbadać taksówki danych za pomocą zapytania SQL. Następnie można zaimportować wyników do ramki danych do wykreślenia docelowych zmiennych i potencjalnego funkcje kontroli visual przy użyciu funkcji automatycznego wizualizacji Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Użyj lokalnej i SQL magic do wykreślenia danych
Domyślnie dane wyjściowe wszelkie fragmenty kodu, które można uruchomić z notesu Jupyter jest dostępna w kontekście sesji, która jest utrwalony na węzłów procesu roboczego. Jeśli chcesz zapisać podróży z węzłami procesu roboczego do każdego obliczeń, a jeśli wszystkie dane potrzebne do Twojej obliczeń jest dostępny lokalnie na węźle serwera Jupyter (który jest węzłem głównym), możesz użyć `%%local` magic do uruchomienia fragmentu kodu na Jupyter serwer.

* **Magiczna SQL** (`%%sql`). Jądro Spark w usłudze HDInsight obsługuje łatwe wbudowanego HiveQL zapytań dotyczących element SQLContext. (`-o VARIABLE_NAME`) Argument będzie się powtarzał wyniki kwerendy SQL jako Pandas ramki danych na serwerze Jupyter. Oznacza to, że będzie on dostępny w trybie lokalnym.
* `%%local` **Magiczna**. `%%local` Magic uruchamia kod lokalnie na serwerze Jupyter, który jest węzłem głównym klastra usługi HDInsight. Zazwyczaj `%%local` magic w połączeniu z `%%sql` magic z `-o` parametru. `-o` Parametru czy zachować dane wyjściowe kwerendy SQL lokalnie, a następnie `%%local` magic spowoduje wywołanie następnego zestawu fragment kodu w celu uruchomienia lokalnie wynik zapytania SQL jest trwały lokalnie.

### <a name="query-the-data-by-using-sql"></a>Zapytanie danych przy użyciu języka SQL
To zapytanie pobiera rund taksówki kwota taryfy, liczba osób i wielkość poradę.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

W poniższym kodzie `%%local` magic tworzy ramkę danych lokalnych, sqlResults. SqlResults służy do wykreślenia przy użyciu matplotlib.

> [!TIP]
> Lokalne magic jest używana wiele razy w tym artykule. Jeśli zestaw danych jest duży, sprawdź przykładowe utworzyć ramki danych, który można umieścić w pamięci lokalnej.
> 
> 

### <a name="plot-the-data"></a>Danych
Można przedstawić przy użyciu kodu języka Python, po ramka danych znajduje się w kontekście lokalnego jako Pandas ramki danych.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 Jądra Spark automatycznie wizualizuje wynik zapytania SQL (HiveQL), po uruchomieniu kodu. Można wybrać różne wizualizacje:

* Tabela
* Kołowy
* Kreska
* Obszar
* Słupkowy

Oto kod danych:

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

![Porada kwota histogram](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Porada kwota według liczby osób](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Porada kwota kwotę Taryfy](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Tworzenie funkcji i przekształcanie funkcji, a następnie przygotowywanie danych na dane wejściowe do funkcji modelowania
Dla funkcji oparta na drzewie modelowania z Spark ML i MLlib należy przygotować docelowy i funkcji przy użyciu różnych technik, takich jak binning, indeksowania hot jeden kodowania i vectorization. Poniżej przedstawiono procedurę należy wykonać w tej sekcji:

1. Utwórz nową funkcję przez **binning** przedziałów czasu godzin w ruchu.
2. Zastosuj **indeksowanie i hot jeden kodowanie** podzielone na kategorie funkcji.
3. **Przykładowe i podziałem w zestawie danych** do ułamków szkoleniowych i testów.
4. **Określ zmienną szkolenia i funkcje**, następnie tworzenie indeksowanych lub hot jeden zakodowane uczenie i testowanie wejściowego punktu etykietą odporność rozproszonych zestawów danych (RDDs) lub ramek danych.
5. Automatycznie **klasyfikowanie i vectorize funkcje i obiekty docelowe** do użycia jako dane wejściowe dla modeli uczenia maszyny.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Utwórz nową funkcję o binning godziny do ruchu przedziałów czasu
Ten kod przedstawia sposób tworzenia nowej funkcji według godzin binning do ruchu przedziałów czasu i sposób pamięci podręcznej wynikowe ramki danych w pamięci. Gdzie ramki RDDs i dane są używane wielokrotnie, buforowanie prowadzi do ulepszone czasu wykonywania. W związku z tym będzie buforować RDDs i ramek danych w kilku etapach w poniższych procedurach.

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


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indeksowanie i hot jeden kodowanie funkcji podzielone na kategorie
Modelowania i prognozowania z kategorii danych wejściowych indeksowanego lub zakodowane przed użyciem funkcji wymagają funkcji MLlib. W tej sekcji przedstawiono sposób indeksu lub zakodować podzielone na kategorie funkcje na dane wejściowe do funkcji modelowania.

Musisz indeksu lub zakodować modeli na różne sposoby, w zależności od modelu. Na przykład Regresja logistyczna i liniowej modele wymagają dynamicznego jednego kodowania. Na przykład funkcja z trzech kategorii można rozszerzyć do trzech kolumn funkcji. Każda kolumna może zawierać 0 lub 1 w zależności od rodzaju obserwacji. Udostępnia MLlib [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkcja dynamicznego jednego kodowania. Ten koder mapuje kolumny indeksów etykiety z kolumną wektorów binarnego z co najwyżej jedną co wartość. Z tego kodowania algorytmy, które oczekują numeryczny ważnych funkcji, takich jak Regresja logistyczna może odnosić się do funkcji podzielone na kategorie.

W tym miejscu możesz przekształcić tylko cztery zmienne, aby wyświetlić przykłady, które są ciągami znaków. Można również indeksu innych zmiennych, takich jak dzień tygodnia, reprezentowany przez wartości liczbowe, jako zmienne podzielone na kategorie.

Indeksowanie, użyj `StringIndexer()`i hot jeden kodowanie, użyj `OneHotEncoder()` funkcji z MLlib. Oto kod do indeksu i kodowania funkcji podzielone na kategorie:

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

Czas uruchamiania komórki: 4 sekundy.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Przykładowe i podziału do ułamków szkoleniowych i testów w zestawie danych
Ten kod tworzy losowej próbki danych (w tym przykładzie 25%). Próbkowanie nie jest wymagany w tym przykładzie ze względu na rozmiar zestawu danych, artykuł pokazuje, jak można przykładowe, aby wiedzieć, jak użyć jej do własnych problemów, gdy jest wymagane. W przypadku dużych przykłady to zapisanie długiego czasu podczas uczenia modeli. Następnie podzielone próbki części szkolenia (75%, w tym przykładzie) i testowania część (25%, w tym przykładzie) do użycia w klasyfikacji i regresji modelowania.

Dodaj liczbę losową (między 0 a 1) do każdego wiersza (w kolumnie "rand"), który może służyć do wybrania złożeń krzyżowego sprawdzania poprawności podczas uczenia.

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

Czas uruchamiania komórki: 2 sekundy.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Określ zmienną szkolenia i funkcje, a następnie utwórz indeksowanych lub hot jeden kodowane celów szkoleniowych i testów danych wejściowych z etykietą punktu RDDs lub danych ramki
Ta sekcja zawiera kod, pokazujący sposób indeksować dane tekstowe podzielone na kategorie jako typ etykietą punktu danych i kodować je, aby użyć w celu nauczenia i przetestowania Regresja logistyczna MLlib i inne modele klasyfikacji. Obiekty etykietą punktu są RDDs sformatowanych w taki sposób, który jest potrzebny jako danych wejściowych przez większość algorytmów w MLlib uczenia maszynowego. A [etykietą punktu](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) lokalnego wektora, zawierające gęsto lub rozrzedzony, skojarzony z etykiety/odpowiedź.

W tym kodzie Określ zmienną docelową (zależnych) i funkcji do użycia w celu przeszkolenia modeli. Następnie można utworzyć indeksowanych lub hot jeden kodowane celów szkoleniowych i testów z etykietą punktu RDDs lub danych ramek danych wejściowych.

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

Czas uruchamiania komórki: 4 sekundy.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Automatycznie klasyfikowanie i vectorize funkcje i obiekty docelowe do użycia jako dane wejściowe dla modeli uczenia maszynowego
Umożliwia klasyfikowanie docelowych i funkcji do użycia w funkcji oparta na drzewie modelowania Spark ML. Kod wykonuje dwie czynności:

* Tworzy binarne docelowy klasyfikacji przypisując wartość 0 lub 1 w każdym punkcie danych pomiędzy 0 a 1 przy użyciu progu wartości 0,5.
* Automatycznie kategoryzuje funkcji. Liczba unikatowych wartości liczbowe dla dowolnej funkcji jest mniejsza niż 32, kategoryzowane tej funkcji.

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



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Model klasyfikacji binarnej: prognozowania, czy należy zwrócić porady
W tej sekcji można tworzyć trzy typy modele klasyfikacji binarnej do prognozowania, czy należy zwrócić Porada:

* A **modelu Regresja logistyczna** przy użyciu Spark ML `LogisticRegression()` — funkcja
* A **model klasyfikacji losowe lasu** przy użyciu Spark ML `RandomForestClassifier()` — funkcja
* A **gradientu zwiększania wyniku model klasyfikacji drzewa** za pomocą MLlib `GradientBoostedTrees()` — funkcja

### <a name="create-a-logistic-regression-model"></a>Tworzenie modelu Regresja logistyczna
Następnie należy utworzyć model Regresja logistyczna przy użyciu Spark ML `LogisticRegression()` funkcji. Można utworzyć modelu kompilowania kodu w serii kroków:

1. **Nauczenia modelu** danych za pomocą jednego zestawu parametrów.
2. **Oceń model** na testowego zestawu danych o metryki.
3. **Zapisz model** w magazynie obiektów Blob do użycia w przyszłości.
4. **Klasyfikacja modelu** względem danych testowych.
5. **Wykreślenia wyniki** z odbiornikiem operacyjnego krzywych cechy (ROC).

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

Obciążenia, wynik i zapisać wyniki.

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

W systemie Python lokalnego ramek danych Pandas do wykreślenia krzywą ROC.

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

![Porada lub nie krzywą ROC Porada](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

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
Następnie utwórz model klasyfikacji GBT przy użyciu jego MLlib `GradientBoostedTrees()` funkcji, a następnie ocenę modelu na danych testowych.

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

## <a name="regression-model-predict-tip-amount"></a>Model regresji: przewidywanie kwota Porada
W tej sekcji można tworzyć dwa typy regresji modeli na potrzeby prognozowania kwota Porada:

* A **model regresji liniowej umorzyć** przy użyciu Spark ML `LinearRegression()` funkcji. Należy zapisać model i ocena modelu na danych testowych.
* A **zwiększania gradientu drzewa modelu regresji** przy użyciu Spark ML `GBTRegressor()` funkcji.

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

Czas uruchamiania komórki: 13 sekund.

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

R — sqr na danych testowych = 0.5960320470835743

Następnie wyniki testu zapytania jako ramki danych i użyj AutoVizWidget i matplotlib do wizualizacji go.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

Kod tworzy ramkę danych lokalnych z wyników kwerendy i zawiera dane. `%%local` Magic tworzy ramkę dane lokalne `sqlResults`, które służy do wykreślenia z matplotlib.

> [!NOTE]
> Ta magic Spark jest używana wiele razy w tym artykule. W przypadku dużych ilości danych, powinny przykładowe utworzyć ramki danych, który można umieścić w pamięci lokalnej.
> 
> 

Utwórz powierzchni za pomocą matplotlib języka Python.

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

![Porada kwota: rzeczywiste a przewidywane](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Tworzenie modelu regresji GBT
Tworzenie modelu regresji GBT przy użyciu Spark ML `GBTRegressor()` funkcji, a następnie ocenę modelu na danych testowych.

[Boosted gradientu drzew](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) są komplety drzewa decyzyjnego. GBTs uczenie drzew decyzyjnych wielokrotnie powtarzane, aby zminimalizować funkcję utraty. Można użyć GBTs regresji i klasyfikacji. One mogą obsługiwać funkcje podzielone na kategorie, nie wymagają funkcji skalowania i przechwytywać nonlinearities i interakcje funkcji. Możesz również można używać ich w ustawieniu multiklasa klasyfikacji.

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

Jest test R-sqr: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Modelowania zaawansowanych narzędzi do optymalizacji
W tej sekcji można za pomocą narzędzi learning maszyny, które deweloperzy często używane na potrzeby optymalizacji modelu. W szczególności za pomocą parametru kominów i krzyżowego sprawdzania poprawności można zoptymalizować machine learning modeli trzy różne sposoby:

* Podział danych na zestawy pociągu i sprawdzania poprawności, optymalizacja modelu przy użyciu funkcji hyper parametr kominów na zestaw szkoleniowy i oceny na zestawie sprawdzania poprawności (regresja liniowa)
* Optymalizacja modelu przy użyciu krzyżowego sprawdzania poprawności i funkcji hyper parametru profilach za pomocą funkcji CrossValidator Spark ML (klasyfikacji binarnej)
* Optymalizacja modelu przy użyciu kodu niestandardowego krzyżowego sprawdzania poprawności i parametr kominów używać żadnych machine learning zestaw funkcji i parametr (regresja liniowa)

**Krzyżowe sprawdzanie poprawności** to technika, który ocenia, jak model ćwiczenie znanego zestawu danych zostanie generalize do prognozowania funkcje zestawów danych, które go nie przeprowadzono uczenia. Ogólne ideą ta technika jest, czy model jest uczony w zestawie danych znanych danych, a następnie dokładność jego prognoz jest testowana niezależnego zestawu danych. Standardowa implementacja polega ona na dzieleniu zestawu danych do *k*-złożeń, a następnie nauczenia modelu w okrężne na wszystkie oprócz jednego złożeń.

**Optymalizacja parametru Hyper** jest problem wybrać zestaw funkcji hyper-parametrów Algorytm uczenia, zwykle z celem optymalizacji miara wydajności algorytm na niezależnego zestawu danych. Funkcji hyper parametr to wartość, która należy określić poza procedury szkolenie modelu. Założenia dotyczące wartości parametrów funkcji hyper może mieć wpływ na dokładność modelu i elastyczność. Drzewa decyzyjne mieć funkcji hyper parametrów, na przykład żądaną głębokość i liczby pozostawia w drzewie. Należy ustawić termin kar błędną klasyfikację maszyny wektorowa obsługa (SVM).

Typowym sposobem wykonania optymalizacji parametru funkcji hyper jest użycie wyszukiwanie siatki, nazywany również **odchylenia parametru**. W wyszukiwaniu siatki kompleksowe przeszukiwanie odbywa się za pomocą wartości podzbiór określonego obszaru funkcji hyper parametr Algorytm uczenia. Krzyżowe sprawdzanie poprawności można podać metryki wydajności, aby posortować optymalne wyniki utworzone przez algorytm wyszukiwania siatki. Jeśli używasz krzyżowe sprawdzanie poprawności parametru hyper kominów może pomóc limit problemów, takich jak overfitting przy użyciu modelu danych szkoleniowych. W ten sposób modelu zachowuje pojemności do zastosowania do ogólny zestaw danych, z którego został wyodrębniony danych szkoleniowych.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optymalizacja model regresji liniowej z kominów hyper parametru
Następnie podzielenia danych na zestawy pociągu i sprawdzania poprawności, użyj funkcji hyper parametru profilach na zestaw szkoleniowy, można zoptymalizować modelu oraz ocenić na zestawie sprawdzania poprawności (regresja liniowa).

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

Jest test R-sqr: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optymalizacja model klasyfikacji binarnej przy użyciu kominów krzyżowego sprawdzania poprawności i funkcji hyper parametru
Tej sekcji przedstawiono, jak zoptymalizować model klasyfikacji binarnej przy użyciu kominów krzyżowego sprawdzania poprawności i parametr hyper. Ta metoda korzysta z ML Spark `CrossValidator` funkcji.

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

Czas uruchamiania komórki: 33 sekundy.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Optymalizacja model regresji liniowej przy użyciu kodu niestandardowego krzyżowego sprawdzania poprawności i kominów parametru
Następnie zoptymalizować modelu przy użyciu niestandardowego kodu i zidentyfikować najlepszych parametrów modelu przy użyciu kryterium najwyższy dokładności. Następnie utwórz ostatecznego modelu, ewaluacji modelu na danych testowych i zapisać modelu w magazynie obiektów Blob. Na koniec załadować modelu, wynik testu danych i oceny dokładności.

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

Czas uruchamiania komórki: 61 sekund.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Korzystanie z modeli uczenia wbudowane Spark maszyny automatycznie z języka Scala
Omówienie tematów, które umożliwia przeprowadzenie zadań wchodzące w skład procesu nauki danych na platformie Azure, zobacz [proces nauki danych zespołu](http://aka.ms/datascienceprocess).

[Wskazówki dotyczące procesu nauki danych Team](walkthroughs.md) opisano inne end-to-end wskazówki, które pokazują czynności w procesie nauki zespołu danych w określonych scenariuszach. Wskazówki dotyczące przedstawiono również sposób łączenia chmurze i lokalnych narzędzi i usług w przepływie pracy lub potoku, aby utworzyć aplikację inteligentnego.

[Wynik modeli uczenia maszynowego wbudowane Spark](spark-model-consumption.md) przedstawiono sposób za pomocą kodu języka Scala można automatycznie załadować oraz wynik nowe zestawy danych przy użyciu modeli uczenia maszynowego wbudowane Spark i zapisane w magazynie obiektów Blob Azure. Postępuj zgodnie z instrukcjami istnieje i po prostu zastąpić Python Scala kodu w tym artykule wykorzystania automatycznych.

