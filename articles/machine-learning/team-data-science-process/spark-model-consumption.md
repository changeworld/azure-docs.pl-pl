---
title: Operationalize modele uczenia maszynowego stworzone przez Spark - Team Data Science Process
description: Jak załadować i uzyskać ocenę modeli uczenia przechowywanych w usłudze Azure Blob Storage (WASB) w języku Python.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718552"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Operationalize modele uczenia maszynowego oparte na spark

W tym temacie pokazano, jak operacjonalizacji zapisanego modelu uczenia maszynowego (ML) przy użyciu języka Python w klastrach platformy SPARK usługi HDInsight. Opisano w nim sposób ładowania modeli uczenia maszynowego, które zostały utworzone przy użyciu platformy Spark MLlib i przechowywane w usłudze Azure Blob Storage (WASB) oraz sposób oceniania ich za pomocą zestawów danych, które również były przechowywane w pliku WASB. Pokazuje, jak wstępnie przetworzyć dane wejściowe, przekształcić funkcje przy użyciu funkcji indeksowania i kodowania w zestawie narzędzi MLlib i jak utworzyć obiekt danych oznaczonych punkt, który może służyć jako dane wejściowe do oceniania z modeli ML. Modele używane do oceniania obejmują regresję liniową, regresję logistyczną, losowe modele lasu i modele drzewa zwiększania gradientu.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Klastry iskier i notesy Jupyter
Kroki instalacji i kod do operacjonalizacji modelu ML są podane w tym instruktażu przy użyciu klastra platformy HDInsight Spark 1.6, a także klastra platformy Spark 2.0. Kod dla tych procedur jest również w notesach Jupyter.

### <a name="notebook-for-spark-16"></a>Notes dla platformy Spark 1.6
[PySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter notebook pokazuje, jak operacjonalizacji zapisanego modelu przy użyciu języka Python w klastrach HDInsight. 

### <a name="notebook-for-spark-20"></a>Notes dla platformy Spark 2.0
Aby zmodyfikować notes Jupyter dla platformy Spark 1.6 w celu użycia z klastrem HDInsight Spark 2.0, zastąp plik kodu Języka Python [tym plikiem](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Ten kod pokazuje, jak korzystać z modeli utworzonych w spark 2.0.


## <a name="prerequisites"></a>Wymagania wstępne

1. Aby ukończyć ten przewodnik, potrzebujesz konta platformy Azure i klastra usługi Spark 1.6 (lub Spark 2.0). Zobacz [omówienie nauki o danych przy użyciu platformy Spark w usłudze Azure HDInsight,](spark-overview.md) aby uzyskać instrukcje dotyczące spełniania tych wymagań. Ten temat zawiera również opis nyc 2013 Taxi dane używane tutaj i instrukcje dotyczące wykonywania kodu z notesu Jupyter w klastrze Platformy Spark. 
2. Utwórz modele uczenia maszynowego, które mają być oceniane w tym miejscu, pracując za pomocą [tematu Eksploracja i modelowanie danych za pomocą](spark-data-exploration-modeling.md) programu Spark dla klastra platformy Spark 1.6 lub notesów platformy Spark 2.0. 
3. Notesy platformy Spark 2.0 używają dodatkowego zestawu danych dla zadania klasyfikacji, znanego zestawu danych odlotu linii lotniczych na czas z lat 2011 i 2012. Opis notesów i łącza do nich znajdują się w [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierającego je. Ponadto kod w tym miejscu i w połączonych notesach jest ogólny i powinien działać w dowolnym klastrze platformy Spark. Jeśli nie używasz programu HDInsight Spark, kroki konfiguracji klastra i zarządzania klastra mogą się nieznacznie różnić od czynności przedstawionych w tym miejscu. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Konfiguracja: lokalizacje pamięci masowej, biblioteki i wstępnie ustawiony kontekst platformy Spark
Spark jest w stanie odczytywać i zapisywać do obiektu blob usługi Azure Storage (WASB). Tak więc wszystkie istniejące dane tam przechowywane mogą być przetwarzane przy użyciu spark i wyniki przechowywane ponownie w WASB.

Aby zapisać modele lub pliki w WASB, ścieżka musi być określona poprawnie. Do domyślnego kontenera dołączonego do klastra Platformy Spark można odwoływać się przy użyciu ścieżki rozpoczynającej się od: *"wasb///"*. Poniższy przykład kodu określa lokalizację danych do odczytu i ścieżkę dla katalogu magazynu modelu, do którego jest zapisywane dane wyjściowe modelu. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Ustawianie ścieżek katalogów dla lokalizacji magazynu w wasb
Modele są zapisywane w: "wasb:///user/remoteuser/NYCTaxi/Models". Jeśli ta ścieżka nie jest ustawiona poprawnie, modele nie są ładowane do oceniania.

Wynik został zapisany w: "wasb:///user/remoteuser/NYCTaxi/ScoredResults". Jeśli ścieżka do folderu jest niepoprawna, wyniki nie są zapisywane w tym folderze.   

> [!NOTE]
> Lokalizacje ścieżki pliku można kopiować i wklejać do symboli zastępczych w tym kodzie z danych wyjściowych ostatniej komórki notesu **machine-learning-data-science-spark-data-exploration-modeling.ipynb.**   
> 
> 

Oto kod do ustawiania ścieżek katalogów: 

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

**Wyjście:**

datetime.datetime(2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Importowanie bibliotek
Ustawianie kontekstu iskry i importowanie niezbędnych bibliotek za pomocą następującego kodu

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Wstępnie ustawiony kontekst Iskry i magia PySpark
Jądra PySpark, które są dostarczane z notesów Jupyter mają wstępnie ustawiony kontekst. W związku z tym nie trzeba ustawić konteksty Spark lub Hive jawnie przed rozpoczęciem pracy z aplikacją, którą tworzysz. Te konteksty są domyślnie dostępne:

* sc - dla Spark 
* sqlContext - dla gałęzi

Jądro PySpark zawiera kilka wstępnie zdefiniowanych "magii", które są specjalnymi poleceniami, które można wywołać za pomocą %%. Istnieją dwa takie polecenia, które są używane w tych przykładach kodu.

* **%%local** Określono, że kod w kolejnych wierszach jest wykonywany lokalnie. Kod musi być prawidłowy kod języka Python.
* **%%sql -o \<nazwa zmiennej>** 
* Wykonuje kwerendę hive względem sqlContext. Jeśli parametr -o jest przekazywany, wynik kwerendy jest zachowywany w kontekście %%local Python jako ramka danych Pandas.

Aby uzyskać więcej informacji na temat jąder notebooków Jupyter i wstępnie zdefiniowanych "magii", które dostarczają, zobacz [Jądra dostępne dla notebooków Jupyter z klastrami HDInsight Spark Linux w programie HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Połknienie danych i tworzenie wyczyszczonej ramki danych
Ta sekcja zawiera kod dla serii zadań wymaganych do pozyskiwania danych do oceny. Odczyt w połączonej próbce 0,1% pliku przejazdu taksówką i taryfy (przechowywany jako plik tsv), sformatuj dane, a następnie tworzy czystą ramkę danych.

Przejazd taksówką i pliki taryf zostały połączone na podstawie procedury przewidzianej w: [Proces nauki o danych zespołu w działaniu: przy użyciu klastrów HDInsight Hadoop](hive-walkthrough.md) tematu.

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

**Wyjście:**

Czas wykonania powyżej komórki: 46,37 sekundy

## <a name="prepare-data-for-scoring-in-spark"></a>Przygotowywanie danych do oceniania w spark
W tej sekcji pokazano, jak indeksować, kodować i skalować funkcje kategoryczne, aby przygotować je do użycia w algorytmach uczenia nadzorowanego MLlib do klasyfikacji i regresji.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Transformacja funkcji: indeks i kodowanie funkcji kategorycznych dla wprowadzania danych do modeli do oceniania
W tej sekcji pokazano, jak indeksować `StringIndexer` dane kategorii `OneHotEncoder` przy użyciu i kodować funkcje z wejściem do modeli.

[StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) koduje kolumnę ciąg etykiet do kolumny indeksów etykiet. Indeksy są uporządkowane według częstotliwości etykiet. 

[OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) mapuje kolumnę indeksów etykiet do kolumny wektorów binarnych, z co najwyżej jedną jedną wartością. To kodowanie umożliwia algorytmy, które oczekują ciągłej wartości funkcji, takich jak regresja logistyczna, mają być stosowane do funkcji kategorycznych.

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

**Wyjście:**

Czas wykonania powyżej komórki: 5,37 sekundy

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Tworzenie obiektów RDD z tablicami elementów do wprowadzania danych do modeli
Ta sekcja zawiera kod, który pokazuje, jak indeksować dane tekstowe jako obiekt RDD i kodować go z jednym hot, dzięki czemu może służyć do szkolenia i testowania regresji logistycznej MLlib i modeli opartych na drzewach. Indeksowane dane są przechowywane w obiektach [Resilient Distributed DataSet (RDD).](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) RdD są podstawowe abstrakcji w Spark. Obiekt RDD reprezentuje niezmienną, podzieloną na partycje kolekcję elementów, które mogą być obsługiwane równolegle z spark.

Zawiera również kod, który pokazuje, `StandardScalar` jak skalować dane z dostarczonych przez MLlib do użycia w regresji liniowej z Stochastic Gradient Descent (SGD), popularny algorytm do szkolenia szeroki zakres modeli uczenia maszynowego. [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) służy do skalowania operacji do odchylenia jednostki. Skalowanie operacji, znany również jako normalizacji danych, ubezpiecza, że funkcje z powszechnie wypłaconych wartości nie są podane nadmierne ważenie w funkcji obiektywnej. 

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

**Wyjście:**

Czas wykonania powyżej komórki: 11,72 sekundy

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Wynik z modelu regresji logistycznej i zapisz dane wyjściowe do obiektu blob
Kod w tej sekcji pokazuje, jak załadować model regresji logistycznej, który został zapisany w magazynie obiektów blob platformy Azure i użyć go do przewidywania, czy wskazówka jest wypłacana na wycieczkę taksówką, ocena go ze standardowymi metrykami klasyfikacji, a następnie zapisać i wykreślić wyniki do magazynu obiektów blob . Wyniki wynik są przechowywane w obiektach RDD. 

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

**Wyjście:**

Czas wykonania powyżej komórki: 19,22 sekundy

## <a name="score-a-linear-regression-model"></a>Ocena modelu regresji liniowej
Użyliśmy [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) do szkolenia modelu regresji liniowej przy użyciu Stochastic Gradient Descent (SGD) do optymalizacji do przewidywania kwoty końcówki wypłacane. 

Kod w tej sekcji pokazuje, jak załadować model regresji liniowej z magazynu obiektów blob platformy Azure, wynik przy użyciu skalowanych zmiennych, a następnie zapisać wyniki z powrotem do obiektu blob.

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


**Wyjście:**

Czas wykonania powyżej komórki: 16,63 sekundy

## <a name="score-classification-and-regression-random-forest-models"></a>Klasyfikacja wyników i regresja Losowe modele lasu
Kod w tej sekcji pokazuje, jak załadować zapisane klasyfikacji i regresji losowych modeli lasu zapisane w magazynie obiektów blob platformy Azure, ocena ich wydajności za pomocą standardowych miar klasyfikacji i regresji, a następnie zapisać wyniki z powrotem do magazynu obiektów blob.

[Losowe lasy](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) to zespoły drzew decyzyjnych.  Łączą one wiele drzew decyzyjnych, aby zmniejszyć ryzyko overfitting. Lasy losowe mogą obsługiwać funkcje kategoryczne, rozciągać się na ustawienie klasyfikacji wieloklasowej, nie wymaga skalowania operacji i są w stanie przechwytywać nieliniowe i interakcje funkcji. Lasy losowe są jednym z najbardziej udanych modeli uczenia maszynowego dla klasyfikacji i regresji.

[spark.mllib](https://spark.apache.org/mllib/) obsługuje losowe lasy dla klasyfikacji binarnej i wieloklasowej oraz dla regresji, przy użyciu zarówno ciągłych, jak i kategorycznych funkcji. 

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

**Wyjście:**

Czas wykonania powyżej komórki: 31,07 sekundy

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Klasyfikacja wyników i modelowanie drzewa gradientu regresji
Kod w tej sekcji pokazuje, jak załadować klasyfikacji i regresji gradientu zwiększenie modeli drzewa z magazynu obiektów blob platformy Azure, ocena ich wydajności za pomocą standardowych miar klasyfikacji i regresji, a następnie zapisać wyniki z powrotem do magazynu obiektów blob. 

**spark.mllib** obsługuje GBTS dla klasyfikacji binarnej i regresji, przy użyciu zarówno ciągłych, jak i kategorycznych funkcji. 

[Gradient Zwiększenie drzewa](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) są zespoły drzew decyzyjnych. GBTS drzewa decyzyjne pociągu iteracyjne, aby zminimalizować funkcję utraty. Gbts może obsługiwać funkcje kategoryczne, nie wymagają skalowania operacji i są w stanie przechwytywać nieliniowe i interakcje funkcji. Algorytm ten może być również używany w ustawieniu klasyfikacji wieloklasowej.

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

**Wyjście:**

Czas wykonania powyżej komórki: 14,6 sekundy

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Oczyszczanie obiektów z pamięci i drukowanie lokalizacji plików z punktowanymi punktami
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


**Wyjście:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

PromowaneTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

Wzmocniona Lokalizacja Pliku 23.56.860740.txt GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Korzystanie z modeli platformy Spark za pośrednictwem interfejsu internetowego
Platforma Spark udostępnia mechanizm zdalnego przesyłania zadań wsadowych lub zapytań interaktywnych za pośrednictwem interfejsu REST ze składnikiem o nazwie Livy. Livy jest domyślnie włączona w klastrze HDInsight Spark. Aby uzyskać więcej informacji na temat Livy, zobacz: [Przesyłanie zadań spark zdalnie za pomocą Livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Livy służy do zdalnego przesyłania zadania, że partia ocenia plik, który jest przechowywany w obiekcie blob platformy Azure, a następnie zapisuje wyniki do innego obiektu blob. Aby to zrobić, należy przesłać skrypt Pythona z  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) do obiektu blob klastra platformy Spark. Za pomocą narzędzia, takiego jak **Microsoft Azure Storage Explorer** lub **AzCopy,** można skopiować skrypt do obiektu blob klastra. W naszym przypadku przesłaliśmy skrypt do ***wasb:///example/python/ConsumeGBNYCReg.py***.   

> [!NOTE]
> Klucze dostępu, które są potrzebne można znaleźć w portalu dla konta magazynu skojarzonego z klastrem Platformy Spark. 
> 
> 

Po przekazaniu do tej lokalizacji ten skrypt jest uruchamiany w klastrze platformy Spark w kontekście rozproszonym. Ładuje model i uruchamia prognoz na pliki wejściowe na podstawie modelu.  

Ten skrypt można wywołać zdalnie, tworząc proste żądanie HTTPS/REST w livy.  Oto polecenie curl do konstruowania żądania HTTP do zdalnego wywoływania skryptu Języka Python. Zastąp CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME odpowiednimi wartościami dla klastra Spark.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Do wywołania zadania Spark za pośrednictwem livy można użyć dowolnego języka w systemie zdalnym, wykonując proste połączenie HTTPS z uwierzytelnianiem podstawowym.   

> [!NOTE]
> Byłoby wygodne do korzystania z biblioteki żądań języka Python podczas wykonywania tego wywołania HTTP, ale nie jest obecnie instalowany domyślnie w usłudze Azure Functions. Zamiast tego używane są starsze biblioteki HTTP.   
> 
> 

Oto kod Pythona dla wywołania HTTP:

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


Można również dodać ten kod języka Python do [usługi Azure Functions,](https://azure.microsoft.com/documentation/services/functions/) aby wyzwolić przesłanie zadania platformy Spark, które ocenia obiekt blob na podstawie różnych zdarzeń, takich jak czasomierz, tworzenie lub aktualizowanie obiektu blob. 

Jeśli wolisz środowisko klienta wolne od kodu, użyj [usługi Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) do wywołania oceniania partii Platformy Spark przez zdefiniowanie akcji HTTP w **Projektancie aplikacji logiki** i ustawienie jego parametrów. 

* W witrynie Azure portal utwórz nową aplikację Logika, wybierając **opcję +Nowa** -> **aplikacja logiki sieci Web +** -> **mobilna**. 
* Aby przywołać **Projektanta aplikacji logiki,** wprowadź nazwę aplikacji logiki i planu usługi aplikacji.
* Wybierz akcję HTTP i wprowadź parametry pokazane na poniższym rysunku:

![Projektant usługi Logic Apps](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Co dalej?
**Krzyżowe sprawdzanie poprawności i tworzenie hiperparametrów:** Zobacz [Zaawansowane eksploracji danych i modelowania z Spark,](spark-advanced-data-exploration-modeling.md) w jaki sposób modele mogą być trenowane przy użyciu krzyżowego sprawdzania poprawności i hyper-parametrów zamiatanie.

