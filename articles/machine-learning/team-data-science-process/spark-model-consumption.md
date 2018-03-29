---
title: Operacjonalizuj modele uczenia wbudowane Spark maszyny | Dokumentacja firmy Microsoft
description: Jak obciążenia i przechowywane w usłudze Azure Blob Storage (WASB) z języka Python modeli uczenia wynik.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 626305a2-0abf-4642-afb0-dad0f6bd24e9
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: bradsev
ms.openlocfilehash: 3a586d40a9d195fe5ec3fa456d450cb3d86f2e9d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Operacjonalizuj modele uczenia wbudowane Spark maszyny
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

W tym temacie przedstawiono sposób operacjonalizacji modelu uczenia maszynowego zapisane (ML) przy użyciu języka Python w klastrach HDInsight Spark. Go opisano, jak załadować modeli uczenia maszyny, które zostały utworzone przy użyciu Spark MLlib i przechowywane w Azure Blob Storage (WASB) i sposobie ich wynik z zestawami danych, które również były przechowywane w WASB. Informuje jak wstępnie przetworzyć dane wejściowe, transformacja funkcji za pomocą funkcji indeksowania i kodowanie w zestawie narzędzi programu MLlib, jak utworzyć obiekt etykietą punktu danych, który może służyć jako dane wejściowe dla oceniania przy użyciu modeli uczenia Maszynowego. Modele oceniania obejmują regresji liniowej, Regresja logistyczna losowe modeli lasu i modele drzewa zwiększania gradientu.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Klastry Spark i notesów Jupyter
Kroki instalacji i kod, który umożliwia operacjonalizację model usługi uczenie Maszynowe są udostępniane w ramach tego przewodnika dla przy użyciu klastra usługi HDInsight Spark w wersji 1.6, a także klastra Spark 2.0. Kod dla tych procedur jest również udostępniany w notesach Jupyter.

### <a name="notebook-for-spark-16"></a>Notesu platformy Spark w wersji 1.6
[PySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) notesu Jupyter pokazano, jak operacjonalizacji zapisany model przy użyciu języka Python w klastrach usługi HDInsight. 

### <a name="notebook-for-spark-20"></a>Notesu platformy Spark 2.0
Aby zmodyfikować notesu Jupyter do wersji 1.6 Spark do korzystania z klastra usługi HDInsight Spark 2.0, Zastąp plik kodu Python z [ten plik](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Ten kod przedstawia sposób korzystać z modeli utworzonych Spark 2.0.


## <a name="prerequisites"></a>Wymagania wstępne

1. Potrzebujesz konta platformy Azure i Spark 1.6 (lub Spark 2.0) klastra usługi HDInsight w tym przewodniku. Zobacz [przegląd danych nauki używania platformy Spark w usłudze Azure HDInsight](spark-overview.md) instrukcje na temat sposobu spełniają tych wymagań. Ten temat zawiera również opis taksówki 2013 NYC danych używany w tym miejscu i instrukcje dotyczące sposobu wykonania kodu z notesu Jupyter w klastrze Spark. 
2. Należy także utworzyć modeli do oceny w tym miejscu pracy za pomocą uczenia maszynowego [Eksploracja danych i modelowanie z Spark](spark-data-exploration-modeling.md) tematu dla klastra Spark w wersji 1.6 lub komputery przenośne Spark 2.0. 
3. Notesów Spark 2.0 użyć dodatkowego zestawu danych dla zadania klasyfikacji, dobrze znanego linii lotniczych na czas wyjścia zestawu danych z 2011 i 2012. Opis notesów i łącza do nich znajdują się w [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierające je. Ponadto kod w tym miejscu w notesach połączony jest rodzajowy i powinny działać na dowolnym klastra Spark. Jeśli nie używasz Spark w usłudze HDInsight, konfiguracja klastra i czynności administracyjne mogą być nieco inne niż to, co przedstawiono w tym miejscu. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Instalatora: lokalizacje magazynu, biblioteki i wstępnie zdefiniowane kontekstu Spark
Platforma Spark jest możliwość odczytu i zapisu do obiektu Blob magazynu Azure (WASB). Dlatego żadnych istniejących danych przechowywanych mogą być przetwarzane przy użyciu platformy Spark i ponownie przechowywane w WASB wyniki.

Aby zapisać modele lub pliki w WASB, ścieżka musi prawidłowo określone. Domyślny kontener dołączony do klastra Spark można odwoływać się przy użyciu ścieżki rozpoczynającej się od: *"wasb lokalizacje"*. Poniższy przykład kodu Określa lokalizację danych do odczytu i ścieżkę do katalogu magazynu modelu, w którym jest zapisany modelu danych wyjściowych. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Ustawianie ścieżki katalogu dla lokalizacji przechowywania w WASB
Modele są zapisywane w: "wasb: / / / użytkownik/remoteuser/NYCTaxi/modele". Jeśli ta ścieżka nie jest poprawnie ustawiona, modele nie są ładowane do oceniania.

Scored wyniki zostały zapisane w: "wasb: / / / użytkownik/remoteuser/NYCTaxi/ScoredResults". Jeśli ścieżka do folderu jest nieprawidłowa, wyniki nie są zapisywane w tym folderze.   

> [!NOTE]
> Ścieżka lokalizacji plików można można kopiować i wklejać do symboli zastępczych w ten kod z danych wyjściowych ostatnią komórkę **machine-learning-data-science-spark-data-exploration-modeling.ipynb** notesu.   
> 
> 

Oto kod, aby ustawić ścieżki katalogu: 

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
Ustaw kontekst spark i zaimportuj wymagane biblioteki z następującym kodem

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Wstępnie zdefiniowane kontekstu Spark i poleceń magicznych PySpark
Jądra PySpark, które są dostarczane z notesów Jupyter ma wstępnie zdefiniowane kontekstu. Dlatego nie trzeba ustawić Spark lub tworzenia kontekstów Hive jawnie, przed rozpoczęciem pracy z aplikacją. Są one dostępne dla Ciebie domyślnie. Konteksty te są:

* sc - platformy Spark 
* Element sqlContext - gałęzi

Jądro PySpark zawiera kilka wstępnie zdefiniowanych "poleceń magicznych", które są specjalne polecenia, które można wywoływać z %%. Istnieją dwa polecenia, które są używane w tych przykładach kodu.

* **%% lokalnego** określono, że kod w kolejnych wierszy jest wykonywane lokalnie. Kod musi być prawidłowy kod języka Python.
* **%% sql -o <variable name>** 
* Wykonuje zapytanie Hive względem element sqlContext. Jeśli parametr -o zostanie przekazany, wynik kwerendy jest utrwalona w %% lokalny kontekst Python jako Pandas dataframe.

Dla więcej informacji na temat jądra notesów Jupyter i wstępnie zdefiniowane "magics" który zapewniają, zobacz [jądra dostępne dla notesu Jupyter klastrze HDInsight Spark w systemie Linux klastrów HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Pozyskiwania danych i Utwórz ramkę, oczyszczony danych
Ta sekcja zawiera kod szereg zadań wymaganych do pozyskiwania danych do oceny. Odczyt w próbce dołączonego do 0,1% taksówki podróży i taryfy pliku (przechowywane jako plik .tsv), format danych, a następnie tworzy ramkę Wyczyść dane.

Pliki podróży i taryfy taksówki zostały dołączone oparte na na procedury w: [zespołu danych nauki procesu w działaniu: z użyciem klastrów usługi HDInsight Hadoop](hive-walkthrough.md) tematu.

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

Czas wykonywania nad komórką: 46.37 sekund

## <a name="prepare-data-for-scoring-in-spark"></a>Przygotowanie danych do oceniania w łączniku Spark
W tej sekcji przedstawiono sposób indeksu, kodowanie i skalowanie funkcji podzielone na kategorie, aby przygotować je do użycia w algorytmów uczenia nadzorowanego MLlib dla funkcji klasyfikacji i regresji.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Funkcja transformacji: indeks i kodowania podzielone na kategorie funkcje dla danych wejściowych do modeli wyników.
W tej sekcji przedstawiono sposób indeksu kategorii danych przy użyciu `StringIndexer` i funkcji przy użyciu kodowania `OneHotEncoder` modeli danych wejściowych.

[StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) koduje kolumny ciąg etykiet do kolumny indeksów etykiety. Indeksy są uporządkowane według częstotliwości etykiety. 

[OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) mapy kolumny indeksów etykiety z kolumną wektorów binarnego z co najwyżej jedną co wartość. Ten typ kodowania umożliwia algorytmy, które oczekują ciągłego ważnych funkcji, takich jak Regresja logistyczna ma zostać zastosowany do funkcji podzielone na kategorie.

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

Czas wykonywania nad komórką: 5.37 sekund

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Tworzenie obiektów RDD z tablicami funkcji dla danych wejściowych do modeli
Ta sekcja zawiera kod, który pokazuje, jak dane tekstowe podzielone na kategorie jako obiekt RDD indeksu i hot jeden kodowania jej dzięki mogą być używane do nauczenia i przetestowania Regresja logistyczna MLlib i oparta na drzewie modeli. Indeksowane dane są przechowywane w [odporność rozproszonych zestawu danych (RDD)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) obiektów. Są to podstawowe abstrakcji w łączniku Spark. Obiekt RDD reprezentuje niezmienne partycjonowanej kolekcję elementów, które może być obsługiwany przez równolegle z platformy Spark.

Zawiera także kod, który pokazuje, jak skalować dane z `StandardScalar` podał MLlib do użycia w regresji liniowej z stochastycznego gradientu spadku (SGD), popularnych Algorytm uczenia szeroką gamę machine learning modeli. [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) służy do funkcji do wariancji jednostki skalowania. Skalowanie funkcji normalizacji danych, nazywany również temu, że funkcji z wartościami powszechnie rozchodów są nie podany nadmiernego porównać w celu funkcji. 

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

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
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

Czas wykonywania nad komórką: 11.72 sekund

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Wynik z modelem Regresja logistyczna i zapisać dane wyjściowe do obiektu blob
Kod w tej sekcji przedstawiono sposób załadować logistyczna Model regresji, który został zapisany w magazynie obiektów blob platformy Azure i użyć go do prognozowania, czy w podróży taksówki otrzymuje poradę, wynik go z metryki standardowej klasyfikacji, a następnie zapisz i wykreślenia wyniki do obiektu blob stora GE. Scored wyniki są przechowywane w obiektach RDD. 

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

Czas wykonywania nad komórką: 19.22 sekund

## <a name="score-a-linear-regression-model"></a>Score Model regresji liniowej
My używamy [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) do uczenia modelu regresji liniowej, przy użyciu stochastycznego gradientu spadku (SGD) na potrzeby optymalizacji na potrzeby prognozowania ilość Porada płatnej. 

Kod w tej sekcji przedstawiono sposób ładowanie modelu regresji liniowej z magazynu obiektów blob platformy Azure, wynik, korzystając ze zmiennych skalowana, a następnie zapisz wyniki obiektu blob.

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

Czas wykonywania nad komórką: 16.63 sekund

## <a name="score-classification-and-regression-random-forest-models"></a>Wynik klasyfikacji i regresji losowe modeli lasu
Kod w tej sekcji przedstawiono sposób załadować zapisanych klasyfikacji i regresji losowe lasu modeli zapisane w magazynie obiektów blob platformy Azure, wynik ich wydajności przy użyciu klasyfikatora standardowe i regresji miary, a następnie zapisz wyniki do magazynu obiektów blob.

[Losowe lasów](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) są komplety drzewa decyzyjnego.  Łączą wiele drzew decyzyjnych, aby zmniejszyć ryzyko overfitting. Losowe lasów mogą obsługiwać funkcje podzielone na kategorie, rozszerzyć ustawienie wieloklasowej klasyfikacji, nie wymagają funkcji skalowania i są w stanie przechwytywania nieliniowość i interakcje funkcji. Losowe lasach są jednym z modeli dla funkcji klasyfikacji i regresji uczenia maszynowego najbardziej popularnych.

[Spark.mllib](http://spark.apache.org/mllib/) obsługuje losowe lasów binarnej i wieloklasowej klasyfikacji i regresji przy użyciu funkcji zarówno podzielone na kategorie, jak i ciągłe. 

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

Czas wykonywania nad komórką: 31.07 sekund

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Wynik klasyfikacji i regresji gradientu zwiększania drzewa modeli
Kod w tej sekcji przedstawiono sposób załadować klasyfikacji i regresji gradientu zwiększania drzewa modeli z magazynu obiektów blob platformy Azure, wynik ich wydajności przy użyciu klasyfikatora standardowe i regresji miary, a następnie zapisz wyniki do magazynu obiektów blob. 

**Spark.mllib** obsługuje GBTs dla binarnego klasyfikacji i regresji przy użyciu funkcji zarówno podzielone na kategorie, jak i ciągłe. 

[Gradientu drzew zwiększania](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) są komplety drzewa decyzyjnego. GBTs uczenie drzew decyzyjnych wielokrotnie powtarzane, aby zminimalizować funkcję utraty. GBTs mogą obsługiwać funkcje podzielone na kategorie, nie wymagają funkcji skalowania i są w stanie przechwytywania nieliniowość i interakcje funkcji. Ich można również w ustawieniu multiklasa klasyfikacji.

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

Czas wykonywania nad komórką: 14.6 sekund

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

## <a name="consume-spark-models-through-a-web-interface"></a>Korzystanie z modeli Spark przy użyciu interfejsu sieci web
Platforma Spark zapewnia mechanizm zdalnie przesłania zadania wsadowe lub interakcyjnych zapytań przy użyciu interfejsu REST z składnik o nazwie Livy. Livy jest domyślnie włączone w klastrze Spark w usłudze HDInsight. Aby uzyskać więcej informacji o Livy, zobacz: [Spark przesyłania zadania zdalnie przy użyciu programu Livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Można użyć programu Livy zdalnie przesłać zadanie wsadowe wyniki pliku, który jest przechowywany w obiekcie blob Azure, a następnie zapisuje wyniki do innego obiektu blob. Aby to zrobić, możesz przekazać skrypt w języku Python z  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) do obiektu blob klastra Spark. Można użyć narzędzia, takiego jak **Eksploratora usługi Microsoft Azure Storage** lub **AzCopy** do Skopiuj skrypt do obiektu blob klastra. W tym przypadku możemy przekazać skrypt ***wasb:///example/python/ConsumeGBNYCReg.py***.   

> [!NOTE]
> Klawisze dostępu, które użytkownik musi można znaleźć w portalu dla konta magazynu skojarzone z klastrem Spark. 
> 
> 

Po przekazaniu do tej lokalizacji, ten skrypt jest uruchamiany w ramach klastra Spark w kontekście rozproszonych. Ładuje modelu i uruchamia prognoz na plików wejściowych, na podstawie modelu.  

Ten skrypt można wywoływać zdalnie, definiując prostego żądania HTTPS/REST na Livy.  Oto polecenia curl, aby utworzyć żądanie HTTP można zdalnie wywołać skrypt w języku Python. Zamień CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME odpowiednie wartości dla klastra Spark.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Dowolnego języka w systemie zdalnym służy do wywołania zadania Spark przy użyciu programu Livy za prosty wywołania protokołu HTTPS z uwierzytelnianiem podstawowym.   

> [!NOTE]
> Będzie łatwe w użyciu żądań Python biblioteki podczas wprowadzania to wywołanie HTTP, ale nie jest aktualnie zainstalowany domyślny usługi Azure Functions. Dlatego starsze bibliotek HTTP są używane zamiast tego.   
> 
> 

Oto kod języka Python dla wywołania HTTP:

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILBLE BY DEFAULT
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


Można również dodać ten kod języka Python, aby [usługi Azure Functions](https://azure.microsoft.com/documentation/services/functions/) aby wyzwolić przesłania zadania Spark wyników obiektu blob na podstawie różnych zdarzeń, takich jak czasomierza, tworzenia lub aktualizacji obiektu blob. 

Środowisko klienta wolnego kodu, należy użyć [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) do oceniania, definiując akcji HTTP w partii Spark wywołania **projektanta aplikacji logiki** oraz ustawienie jego parametrów. 

* Z portalu Azure, Utwórz nową aplikację logiki, wybierając **+ nowy** -> **sieci Web i mobilność** -> **aplikacji logiki**. 
* Aby wyświetlić **projektanta aplikacji logiki**, wprowadź nazwę aplikacji logiki i Plan usługi App Service.
* Wybierz akcję, HTTP i wprowadź parametry pokazano na poniższej ilustracji:

![Projektant aplikacji usługi Logic Apps](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Co dalej?
**Krzyżowe sprawdzanie poprawności i kominów hyperparameter**: zobacz [zaawansowane Eksploracja danych i modelowania z Spark](spark-advanced-data-exploration-modeling.md) na jak modeli można uczony przy użyciu kominów krzyżowego sprawdzania poprawności i parametru funkcji hyper.

