---
title: Zoperacjonalizować modele uczenia maszynowego platformy Spark utworzonych - zespołu danych dla celów naukowych
description: Jak załadować i ocenianie modeli uczenia przechowywanych w usłudze Azure Blob Storage (WASB) za pomocą języka Python.
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
ms.openlocfilehash: 3f02690d7c54581ed80b521e8222d1bd5964c878
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718552"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Zoperacjonalizować modele uczenia maszynowego utworzonych na platformie Spark

W tym temacie pokazano, jak do obsługi operacji modelu uczenia maszynowego zapisane (ML) przy użyciu języka Python w klastrach HDInsight Spark. Go w tym artykule opisano sposób ładowania modeli uczenia maszynowego, które zostały utworzone przy użyciu MLlib platformy Spark i przechowywane w usłudze Azure Blob Storage (WASB) oraz ocena ich z zestawami danych, które są także przechowywane w WASB. Pokazuje, jak wstępnie przetworzyć dane wejściowe, przekształcić funkcji za pomocą indeksowaniem i kodowanie funkcji w zestawie narzędzi MLlib oraz sposób tworzenia obiektu etykietą punktu danych, który może służyć jako dane wejściowe do oceniania przy użyciu modeli uczenia Maszynowego. Modele użyte do oceniania obejmują regresji liniowej, regresji logistycznej, losowych modele lasu i modele drzewa zwiększania wyniku gradientu.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Klastry Spark i notesy Jupyter
Kroki instalacji i kodu do obsługi operacji modelu usługi uczenie Maszynowe są dostarczane w ramach tego przewodnika dotyczące korzystania z klastra usługi HDInsight Spark 1.6, a także klastra Spark w wersji 2.0. Kod dla tych procedur jest również udostępniany w notesy Jupyter.

### <a name="notebook-for-spark-16"></a>Notes dla aparatu Spark 1.6
[PySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) notesu programu Jupyter pokazuje, jak operacjonalizować model zapisanej przy użyciu języka Python w klastrach HDInsight. 

### <a name="notebook-for-spark-20"></a>Notes dla aparatu Spark 2.0
Aby zmodyfikować notesu programu Jupyter dla aparatu Spark 1.6 do korzystania z klastra usługi HDInsight Spark 2.0, należy zastąpić plik kodu języka Python za pomocą [ten plik](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Ten kod przedstawia sposób korzystanie z modeli utworzonych w wersji 2.0 platformy Spark.


## <a name="prerequisites"></a>Wymagania wstępne

1. Potrzebujesz konta platformy Azure i platformy Spark 1.6 (lub Spark 2.0) klastra HDInsight w celu przeprowadzenia tego instruktażu. Zobacz [Omówienie programu do nauki o danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md) w jaki sposób spełnić te wymagania. Ten temat zawiera również opis dane taksówek 2013 NYC użyty tutaj i instrukcje dotyczące sposobu wykonania kodu z notesu Jupyter w klastrze Spark. 
2. Utwórz modele uczenia maszynowego, które mają zostać ocenione w tym miejscu przez zapoznanie się z tematem [eksplorowanie i modelowanie danych za pomocą platformy Spark](spark-data-exploration-modeling.md) dla klastra Spark 1,6 lub notesów platformy Spark 2,0. 
3. Notesy platformy Spark 2.0 użycia dodatkowego zestawu danych dla zadania klasyfikacji, dobrze znane linie lotnicze w czasie wyjścia zestawu danych z 2011 i 2012. Opis notesów i łącza do nich znajdują się w [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierające je. Ponadto kod w tym miejscu w notesach połączonej jest ogólny i powinna działać w dowolnym klastrze Spark. Jeśli nie używasz platformy HDInsight Spark, konfiguracja klastra i czynności administracyjne mogą nieznacznie różnić się od przedstawionego w tym miejscu. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Instalacji: lokalizacje przechowywania, biblioteki i wstępnie zdefiniowane kontekstu aparatu Spark
Platforma Spark jest możliwość odczytu i zapisu do obiektu Blob magazynu Azure (WASB). Dlatego żadnych istniejących danych przechowywanych mogą być przetwarzane przy użyciu platformy Spark i najlepszych wyników ponownie przechowywane w WASB.

Aby zapisać modeli lub pliki w WASB, ścieżka musi być określona poprawnie. Kontener domyślny, dołączony do klastra Spark można się odwoływać przy użyciu ścieżki rozpoczynającej się od: *"wasb / / /"* . Poniższy przykład kodu Określa lokalizację danych do odczytu i ścieżki katalogu magazynu modelu, do którego dane wyjściowe modelu są zapisywane. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Ustaw ścieżki katalogu lokalizacje przechowywania w WASB
Modele są zapisywane w: "wasb: / / / / remoteuser/NYCTaxi/modelach użytkowników". Jeśli ta ścieżka nie jest ustawiona poprawnie, modele nie są ładowane do oceniania.

Scored wyniki zostały zapisane w: "wasb: / / / użytkownik/remoteuser/NYCTaxi/ScoredResults". Jeśli ścieżka do folderu jest nieprawidłowa, wyniki nie są zapisywane w tym folderze.   

> [!NOTE]
> Ścieżki lokalizacji pliku można skopiowane i wklejone do symbole zastępcze w tym kodzie z danych wyjściowych ostatnią komórkę **machine-learning-data-science-spark-data-exploration-modeling.ipynb** notesu.   
> 
> 

Poniżej przedstawiono kod, aby ustawić ścieżek katalogów: 

    # LOCATION OF DATA TO BE SCORED (TEST DATA)
    taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 

    # SET SCORDED RESULT DIRECTORY PATH
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 

    # FILE LOCATIONS FOR THE MODELS TO BE SCORED
    logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
    linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
    randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
    randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
    BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
    BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

    # RECORD START TIME
    import datetime
    datetime.datetime.now()

**DANE WYJŚCIOWE:**

datetime.datetime(2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Importuj biblioteki
Ustaw kontekst aparatu spark i zaimportuj wymagane biblioteki z następującym kodem

    #IMPORT LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime


### <a name="preset-spark-context-and-pyspark-magics"></a>Ustawienie wstępne kontekstu aparatu Spark i poleceń magicznych PySpark
Jądra PySpark, które są dostarczane z notesów Jupyter mają wstępnie kontekstu. W związku z tym przed rozpoczęciem pracy z aplikacją, którą tworzysz, nie trzeba jawnie ustawiać kontekstów platformy Spark ani Hive. Te konteksty są domyślnie dostępne:

* SC - dla platformy Spark 
* sqlContext - programu Hive

Jądra PySpark zawiera kilka wstępnie zdefiniowanych "poleceń magicznych", które są specjalne polecenia, które można wywoływać za pomocą %%. Istnieją dwa polecenia, które są używane w tych przykładach kodu.

* **%% lokalnego** określono, że kod w kolejnych wierszy jest wykonywane lokalnie. Kod musi być prawidłowy kod języka Python.
* **%% Nazwa zmiennej \<SQL-o >** 
* Wykonuje zapytanie programu Hive względem sqlContext. Jeśli parametr -o zostanie przekazana, wynik kwerendy są utrwalane w %% kontekstu Python lokalnego jako Pandas dataframe.

Aby więcej informacji na temat jądra notesów programu Jupyter i wstępnie zdefiniowanego "magics", zapewniają one, zobacz [jądra, które są dostępne dla notesów programu Jupyter przy użyciu platformy Spark HDInsight w systemie Linux klastrów HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Pozyskiwanie danych i Utwórz ramkę danych operacji czyszczenia
Ta sekcja zawiera kod dla szeregu zadania wymagane w celu pozyskiwania danych, aby zostać ocenione. Odczytać w dołączonym do przykładowych 0,1% taksówek podróży i klasie pliku (przechowywany jako plik tsv), formatu danych, a następnie tworzy ramkę Wyczyść dane.

Pliki podróży i klasie taksówek zostały przyłączone oparte na po procedury w: [zespołu danych dla celów naukowych w działaniu: przy użyciu klastrów usługi HDInsight Hadoop](hive-walkthrough.md) tematu.

    # INGEST DATA AND CREATE A CLEANED DATA FRAME

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_test_file = sc.textFile(taxi_test_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_test_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)

    # CREATE DATA FRAME
    taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_cleaned.cache()
    taxi_df_test_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_test_cleaned.registerTempTable("taxi_test")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**DANE WYJŚCIOWE:**

Czas wykonywania powyżej komórki: 46.37 sekund

## <a name="prepare-data-for-scoring-in-spark"></a>Przygotowuje dane do oceniania na platformie Spark
W tej sekcji przedstawiono sposób indeks, kodowanie i skalowania funkcje podzielonych na kategorie, aby przygotować je do użycia w algorytmów uczenia MLlib Tryb nadzorowany dla klasyfikacji i regresji.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Funkcja transformacji: indeks i kodowanie kategorii funkcji dla danych wejściowych modeli do oceniania
W tej sekcji pokazano, jak i indeksowanie danych podzielonych na kategorie za pomocą `StringIndexer` i kodowanie funkcji `OneHotEncoder` wejściowych modeli.

[StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) koduje kolumną z ciągami etykiet do kolumny indeksów etykiety. Indeksy są uporządkowane według częstotliwości etykiety. 

[OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) mapuje kolumną indeksów etykiety z kolumną wektorów binarnych z co najwyżej jeden — wartość typu single. To kodowanie umożliwia algorytmy, które oczekują ciągłe ważnych funkcji, takich jak regresji logistycznej, mają być stosowane do kategorii funkcje.

    #INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_test 
    """
    taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_with_newFeatures.cache()
    taxi_df_test_with_newFeatures.count()

    # INDEX AND ONE-HOT ENCODING
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_test_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)

    # INDEX AND ENCODE TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**DANE WYJŚCIOWE:**

Czas wykonywania powyżej komórki: 5.37 sekund

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Tworzenie obiektów RDD z tablicami funkcji dla danych wejściowych modeli
Ta sekcja zawiera kod, który pokazuje, jak indeksowanie danych podzielonych na kategorie tekstu jako obiekt RDD i hot jeden Zakoduj je, dzięki czemu może służyć do nauczenia i przetestowania regresji logistycznej MLlib i modeli oparta na drzewie. Indeksowane dane są przechowywane w [odporne rozproszone zestawu danych (RDD)](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) obiektów. Odporne to podstawowe streszczenie w platformie Spark. Obiekt RDD reprezentuje niezmienne, podzielone na partycje Kolekcja elementów, które mogą być stosowane równolegle z platformą Spark.

Zawiera ona także kod, który pokazuje, jak skalować dane za pomocą `StandardScalar` udostępniane przez MLlib do użycia w regresji liniowej z stochastycznego gradientu zejścia (SGD), popularnych algorytm szkoleniowe szeroką gamę modeli uczenia maszynowego. [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) służy do funkcji do wariancji jednostki skalowania. Funkcja skalowania, nazywana również normalizacji danych ubezpieczycielom, że funkcje o wartościach powszechnie rozchodów są nie udzieliła nadmierne porównać w celu funkcji. 

    # CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
    oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)

    # FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
    indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
    oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)

    # SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
    scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
    oneHotTESTregScaled = scaler.transform(oneHotTESTreg)

    # CACHE RDDS IN MEMORY
    indexedTESTbinary.cache();
    oneHotTESTbinary.cache();
    indexedTESTreg.cache();
    oneHotTESTreg.cache();
    oneHotTESTregScaled.cache();

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**DANE WYJŚCIOWE:**

Czas wykonywania powyżej komórki: 11.72 sekund

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Generowanie wyników z modelu regresji logistycznej i zapisać dane wyjściowe do obiektu blob
Kod w tej sekcji pokazano, jak załadować logistycznej Model regresji, który został zapisany w usłudze Azure blob storage i użyć go do przewidywania, czy porady zostało opłacone w podróży taksówek, ocena go za pomocą metryk standardowych klasyfikacji, a następnie zapisz i wyniki do obiektu blob stora wykresu GE. Scored wyniki są przechowywane w obiektach RDD. 

    # SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    ## LOAD SAVED MODEL
    savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
    predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))

    ## SAVE SCORED RESULTS (RDD) TO BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
    dirfilename = scoredResultDir + logisticregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**DANE WYJŚCIOWE:**

Czas wykonywania powyżej komórki: 19.22 sekund

## <a name="score-a-linear-regression-model"></a>Ocenianie modelu regresji liniowej
Użyliśmy [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) do uczenia modelu regresji liniowej, za pomocą stochastycznego gradientu zejścia (SGD) do optymalizacji do prognozowania ilość Porada płatne. 

Kod w tej sekcji przedstawiono sposób załadować modelu regresji liniowej z usługi Azure blob storage, ocenianie, korzystając ze zmiennych skalowanych, a następnie zapisz wyniki do obiektu blob.

    #SCORE LINEAR REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #LOAD LIBRARIES
    from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel

    # LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
    savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
    predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = scoredResultDir + linearregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**DANE WYJŚCIOWE:**

Czas wykonywania powyżej komórki: 16.63 sekund

## <a name="score-classification-and-regression-random-forest-models"></a>Ocenianie klasyfikacji i regresji losowe lasu modeli
Kod w tej sekcji pokazano, jak załadować zapisanych klasyfikacji i regresji losowe lasu modeli zapisane w usłudze Azure blob storage, ocenić ich ze standardowego klasyfikatora i miarami regresji, a następnie zapisz wyniki magazynu obiektów blob.

[Losowe lasów](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) są decyzyjne drzewa decyzyjne.  Łączą wiele drzewa decyzyjne, aby zmniejszyć ryzyko overfitting. Losowe lasy mogą obsługiwać kategorii funkcji dotyczyć ustawienie klasyfikacji wieloklasowej, skalowanie funkcja nie jest wymagane i są w stanie przechwytywania nieliniowość i interakcje funkcji. Losowe lasy są jednymi z najbardziej popularnych modeli, które w funkcji klasyfikacji i regresji uczenia maszynowego.

[Spark.mllib](https://spark.apache.org/mllib/) obsługuje losowe lasów binarne i wieloklasowej klasyfikacji i regresji, korzystanie z funkcji ciągłego i podzielonych na kategorie. 

    # SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES    
    from pyspark.mllib.tree import RandomForest, RandomForestModel


    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**DANE WYJŚCIOWE:**

Czas wykonywania powyżej komórki: 31.07 sekund

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Ocenianie modeli klasyfikacji i regresji gradientu zwiększania wyniku drzewa
Kod w tej sekcji przedstawiono sposób załadować modeli klasyfikacji i regresji gradientu zwiększania wyniku drzewa z usługi Azure blob storage, ocena wydajności ze standardowego klasyfikatora i miarami regresji, a następnie zapisz wyniki magazynu obiektów blob. 

**platforma Spark. mllib** obsługuje GBTS dla klasyfikacji binarnej oraz regresję przy użyciu funkcji ciągłego i kategorii. 

[Drzewa podwyższające liczbę gradientów](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) są kompletnymi drzewami decyzyjnymi. GBTSe drzewa decyzyjne w sposób iteracyjny, aby zminimalizować funkcję strat. Program GBTS może obsługiwać funkcje kategorii, nie wymaga skalowania funkcji i może przechwytywać interakcje i funkcje. Ten algorytm może być również używany w ustawieniu klasyfikacji wieloklasowej.

    # SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    #LOAD AND SCORE THE MODEL
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    # LOAD AND SCORE MODEL 
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**DANE WYJŚCIOWE:**

Czas wykonywania powyżej komórki: 14.6 sekund

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Czyszczenie obiektów z pamięci i Drukuj oceniane lokalizacje plików
    # UNPERSIST OBJECTS CACHED IN MEMORY
    taxi_df_test_cleaned.unpersist()
    indexedTESTbinary.unpersist();
    oneHotTESTbinary.unpersist();
    indexedTESTreg.unpersist();
    oneHotTESTreg.unpersist();
    oneHotTESTregScaled.unpersist();


    # PRINT OUT PATH TO SCORED OUTPUT FILES
    print "logisticRegFileLoc: " + logisticregressionfilename;
    print "linearRegFileLoc: " + linearregressionfilename;
    print "randomForestClassificationFileLoc: " + rfclassificationfilename;
    print "randomForestRegFileLoc: " + rfregressionfilename;
    print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
    print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**DANE WYJŚCIOWE:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Korzystanie z modeli Spark za pośrednictwem interfejsu sieci web
Platforma Spark udostępnia mechanizm do zdalnego przesyłania zadań wsadowych i interaktywnych zapytań za pośrednictwem interfejsu REST za pomocą składnika o nazwie usługi Livy. Usługi Livy jest włączona domyślnie w klastrze usługi HDInsight Spark. Aby uzyskać więcej informacji na temat usługi Livy, zobacz: [przesyłania zadań platformy Spark zdalnie przy użyciu programu Livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Można użyć usługi Livy zdalnie przesłać zadanie usługi batch wyniki pliku, który jest przechowywany w usłudze Azure blob, a następnie zapisuje wyniki do innego obiektu blob. Aby to zrobić, Przekaż skrypt języka Python z  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) do obiektu blob klastra platformy Spark. Można użyć narzędzia, takiego jak **Microsoft Azure Storage Explorer** lub **AzCopy** do Skopiuj skrypt do obiektu blob klastra. W naszym przypadku możemy przekazać skrypt, aby ***wasb:///example/python/ConsumeGBNYCReg.py***.   

> [!NOTE]
> Klucze dostępu, które możesz muszą znajdują się w portalu dla konta magazynu skojarzonego z klastrem Spark. 
> 
> 

Po przekazaniu plików do tej lokalizacji, ten skrypt jest uruchamiany w ramach klastra Spark w kontekście rozproszonych. Model ładuje i uruchamia prognozy na plików wejściowych, na podstawie modelu.  

Ten skrypt można wywoływać zdalnie, wprowadzając proste żądania HTTPS/REST na usługi Livy.  Poniżej przedstawiono polecenia curl, aby utworzyć żądanie HTTP do zdalnego wywołania skryptu języka Python. Zamień CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME odpowiednie wartości dla klastra Spark.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Aby wywołać zadanie Spark za pomocą usługi Livy przez wywołania prostego protokołu HTTPS z uwierzytelnianiem podstawowym, można używać dowolnego języka w systemie zdalnym.   

> [!NOTE]
> Byłoby wygodne za pomocą biblioteki Python żądań podczas wprowadzania tego wywołania HTTP, ale nie jest obecnie zainstalowana domyślnie w usłudze Azure Functions. Aby zamiast tego jest używana starsza biblioteki HTTP.   
> 
> 

Poniżej przedstawiono kod języka Python dla wywołania HTTP:

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILABLE BY DEFAULT
    import httplib, urllib, base64

    # REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
    host = '<spark cluster name>.azurehdinsight.net:443'
    username='<username>'
    password='<password>'

    #AUTHORIZATION
    conn = httplib.HTTPSConnection(host)
    auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
    headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}

    # SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
    # IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
    r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
    response = conn.getresponse().read()
    print(response)
    conn.close()


Można również dodać ten kod języka Python w celu [usługi Azure Functions](https://azure.microsoft.com/documentation/services/functions/) do wyzwolenia przesyłanie zadań platformy Spark, która ocenia obiektu blob na podstawie różnych zdarzeń, takich jak timer, tworzenie lub aktualizowanie obiektu blob. 

Środowisko bezpłatnej klienckim kodu, należy użyć [usługi Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) do wywołania wsadowego Spark oceniania, definiując akcji HTTP na **Projektant aplikacji logiki** i ustawiając jego parametry. 

* W witrynie Azure portal utworzyć nową aplikację logiki, wybierając **+ nowy** -> **sieci Web i mobilność** -> **aplikacji logiki**. 
* Aby wyświetlić **Projektant aplikacji logiki**, wprowadź nazwę aplikacji logiki i planu usługi App Service.
* Wybieranie akcji HTTP, a następnie wprowadź parametry pokazano na poniższej ilustracji:

![Projektant aplikacji usługi Logic Apps](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Co dalej?
**Krzyżowa Weryfikacja i zaczynają hiperparametrycznego**: zobacz [zaawansowane Eksplorowanie i modelowanie za pomocą platformy Spark danych](spark-advanced-data-exploration-modeling.md) w sposób modeli może być uczony przy użyciu zaczynają krzyżowego sprawdzania poprawności i parametrów.

