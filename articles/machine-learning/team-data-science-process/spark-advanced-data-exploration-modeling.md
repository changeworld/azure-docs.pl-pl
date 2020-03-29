---
title: Zaawansowane eksploracja i modelowanie danych za pomocą platformy Spark — proces nauki o danych zespołowych
description: Funkcja HDInsight Spark umożliwia eksplorację danych i szkolenie modeli klasyfikacji binarnej i regresji przy użyciu sprawdzania poprawności krzyżowej i optymalizacji hiperparametrycznego.
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
ms.openlocfilehash: 15d9d186ef36ee9181a6ce0386aa9cc5de7838e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718655"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Zaawansowane eksplorowanie i modelowanie danych za pomocą platformy Spark

W tym instruktażu używa hdinsight Spark do eksploracji danych i szkolenia klasyfikacji binarnej i modeli regresji przy użyciu krzyżowego sprawdzania poprawności i optymalizacji hiperparametru na próbce podróży taksówką NYC i taryfy 2013 dataset. Przeprowadzi Cię przez kroki [procesu nauki o danych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, przy użyciu klastra platformy SPARK USŁUGI HDInsight do przetwarzania i obiektów blob platformy Azure do przechowywania danych i modeli. Proces eksploruje i wizualizuje dane wprowadzone z obiektu blob usługi Azure Storage, a następnie przygotowuje dane do tworzenia modeli predykcyjnych. Python został użyty do zakodować rozwiązanie i pokazać odpowiednie wykresy. Te modele są budowane przy użyciu zestawu narzędzi Spark MLlib do wykonywania zadań klasyfikacji binarnej i modelowania regresji. 

* Zadaniem **klasyfikacji binarnej** jest przewidywanie, czy napiwek jest opłacany za podróż. 
* Zadaniem **regresji** jest przewidywanie ilości końcówki na podstawie innych funkcji porad. 

Kroki modelowania zawierają również kod pokazujący, jak trenować, oceniać i zapisywać każdy typ modelu. Temat obejmuje niektóre z tego samego terenu co [eksploracji danych i modelowania z Spark](spark-data-exploration-modeling.md) tematu. Ale jest bardziej "zaawansowany", ponieważ używa również krzyżowego sprawdzania poprawności z hiperparametrycznym zamiataniem, aby trenować optymalnie dokładne modele klasyfikacji i regresji. 

**Krzyżowe sprawdzanie poprawności (CV)** jest techniką, która ocenia, jak dobrze model przeszkolony na znanym zestawie danych uogólnia do przewidywania funkcji zestawów danych, na których nie został przeszkolony.  Wspólną implementacją używaną w tym miejscu jest podzielenie zestawu danych na k fałdy, a następnie trenowanie modelu w sposób okrężny na wszystkich, z ale jednym z fałd. Zdolność modelu do przewidywania dokładnie podczas testowania na niezależny zestaw danych w tym krotnie nie jest używany do uczenia modelu jest oceniana.

**Optymalizacja hiperparametryczne** to problem wyboru zestawu hiperparametrów dla algorytmu uczenia się, zwykle w celu optymalizacji miary wydajności algorytmu na niezależnym zestawie danych. **Hiperparametry są wartościami,** które muszą być określone poza procedurą szkolenia modelu. Założenia dotyczące tych wartości mogą mieć wpływ na elastyczność i dokładność modeli. Drzewa decyzyjne mają hiperparametry, na przykład, takie jak pożądana głębokość i liczba liści w drzewie. Obsługa maszyn wektorowych (SVMs) wymaga ustawienia terminu kary błędnej klasyfikacji. 

Typowym sposobem wykonywania optymalizacji hiperparametryczne używane w tym miejscu jest wyszukiwanie siatki lub **wyciągnięcie po parametrach**. To wyszukiwanie przechodzi przez podzbiór przestrzeni hiperparametryczne dla algorytmu uczenia się. Sprawdzanie poprawności krzyżowej może dostarczyć metryki wydajności, aby uporządkować optymalne wyniki uzyskane przez algorytm wyszukiwania siatki. CV używane z przesączaniem hiperparametru pomaga ograniczyć problemy, takie jak overfitting modelu do danych szkoleniowych, dzięki czemu model zachowuje zdolność do zastosowania do ogólnego zestawu danych, z których wyodrębniono dane szkoleniowe.

Modele, których używamy obejmują regresję logistyczną i liniową, losowe lasy i drzewa wzmocnione gradientem:

* [Regresja liniowa z SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) jest modelem regresji liniowej, który używa metody Stochastic Gradient Descent (SGD) oraz optymalizacji i skalowania operacji w celu przewidywania kwot końcówki zapłaconych. 
* [Regresja logistyczna z LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) lub regresją "logit" jest modelem regresji, który może być używany, gdy zmienna zależna jest kategoryczna do klasyfikacji danych. LBFGS to algorytm optymalizacji quasi-Newtona, który przybliża algorytm Broyden-Fletcher-Goldfarb-Shanno (BFGS) przy użyciu ograniczonej ilości pamięci komputera i jest szeroko stosowany w uczeniu maszynowym.
* [Losowe lasy](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) to zespoły drzew decyzyjnych.  Łączą one wiele drzew decyzyjnych, aby zmniejszyć ryzyko overfitting. Lasy losowe są używane do regresji i klasyfikacji i mogą obsługiwać funkcje kategoryczne i mogą być rozszerzone do ustawienia klasyfikacji wieloklasowej. Nie wymagają one skalowania operacji i są w stanie przechwytywać nieliniowe i interakcje funkcji. Lasy losowe są jednym z najbardziej udanych modeli uczenia maszynowego dla klasyfikacji i regresji.
* [Gradient wzmocnione drzewa](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) są zespoły drzew decyzyjnych. GBTS drzewa decyzyjne pociągu iteracyjne, aby zminimalizować funkcję utraty. GBTS jest używany do regresji i klasyfikacji i może obsługiwać funkcje kategoryczne, nie wymagają skalowania operacji i są w stanie przechwytywać nieliniowe i interakcje funkcji. Mogą być również używane w ustawieniach klasyfikacji wieloklasowej.

Przykłady modelowania przy użyciu cv i hyperparameter sweep są wyświetlane dla problemu klasyfikacji binarnej. Prostsze przykłady (bez parametrów wyciągnięcia po ścieżce) są prezentowane w głównym temacie dla zadań regresji. Ale w dodatku, sprawdzanie poprawności przy użyciu elastycznej sieci dla regresji liniowej i CV z parametrem sweep przy użyciu losowej regresji lasu są również prezentowane. **Siatka elastyczna** jest regularną metodą regresji dopasowywania modeli regresji liniowej, która liniowo łączy metryki L1 i L2 jako kary metod [lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) i [ridge.](https://en.wikipedia.org/wiki/Tikhonov_regularization)   

<!-- -->

> [!NOTE]
> Chociaż zestaw narzędzi Spark MLlib jest przeznaczony do pracy na dużych zestawach danych, stosunkowo mała próbka (~30 Mb przy użyciu wierszy 170K, około 0,1% oryginalnego zestawu danych NYC) jest używana tutaj dla wygody. Ćwiczenie podane w tym miejscu działa wydajnie (w około 10 minut) w klastrze HDInsight z 2 węzłami procesu roboczego. Ten sam kod, z niewielkimi modyfikacjami, może służyć do przetwarzania większych zestawów danych, z odpowiednimi modyfikacjami buforowania danych w pamięci i zmiany rozmiaru klastra.

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>Konfiguracja: klastry i notesy platformy Spark
Kroki konfiguracji i kod są podane w tym instruktażu do korzystania z programu HDInsight Spark 1.6. Ale notebooki Jupyter są dostępne zarówno dla klastrów HDInsight Spark 1.6, jak i Spark 2.0. Opis notesów i łącza do nich znajdują się w [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierającego je. Ponadto kod w tym miejscu i w połączonych notesach jest ogólny i powinien działać w dowolnym klastrze platformy Spark. Jeśli nie używasz programu HDInsight Spark, kroki konfiguracji klastra i zarządzania klastra mogą się nieznacznie różnić od czynności przedstawionych w tym miejscu. Dla wygody, oto linki do notebooków Jupyter dla Spark 1.6 i 2.0 do uruchomienia w jądrze pyspark serwera notebooków Jupyter:

### <a name="spark-16-notebooks"></a>Notesy Spark 1.6

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Zawiera tematy w #1 notebooków i rozwoju modelu przy użyciu dostrajania hiperparametrycznego i krzyżowej weryfikacji.

### <a name="spark-20-notebooks"></a>Notesy Spark 2.0

[Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Ten plik zawiera informacje na temat sposobu eksploracji danych, modelowania i oceniania w klastrach platformy Spark 2.0.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Konfiguracja: lokalizacje pamięci masowej, biblioteki i wstępnie ustawiony kontekst platformy Spark
Spark jest w stanie odczytywać i zapisywać do obiektu blob usługi Azure Storage (znany również jako WASB). Tak więc wszystkie istniejące dane tam przechowywane mogą być przetwarzane przy użyciu spark i wyniki przechowywane ponownie w WASB.

Aby zapisać modele lub pliki w WASB, ścieżka musi być określona poprawnie. Do domyślnego kontenera dołączonego do klastra platformy Spark można odwoływać się przy użyciu ścieżki rozpoczynającej się od: "wasb:///". Inne lokalizacje są odwoływane przez "wasb://".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Ustawianie ścieżek katalogów dla lokalizacji magazynu w wasb
Poniższy przykładowy kod określa lokalizację danych do odczytu i ścieżkę dla katalogu magazynu modelu, do którego jest zapisywane dane wyjściowe modelu:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";


    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**Wyjście**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Importowanie bibliotek
Importuj niezbędne biblioteki z następującym kodem:

    # LOAD PYSPARK LIBRARIES
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
Jądra PySpark, które są dostarczane z notesów Jupyter mają wstępnie ustawiony kontekst. Dlatego nie trzeba ustawić konteksty Spark lub Hive jawnie przed rozpoczęciem pracy z aplikacją, którą tworzysz. Te konteksty są domyślnie dostępne. Konteksty te są następujące:

* sc - dla Spark 
* sqlContext - dla gałęzi

Jądro PySpark zawiera kilka wstępnie zdefiniowanych "magii", które są specjalnymi poleceniami, które można wywołać za pomocą %%. Istnieją dwa takie polecenia, które są używane w tych przykładach kodu.

* **%%local** Określa, że kod w kolejnych wierszach ma być wykonywany lokalnie. Kod musi być prawidłowy kod języka Python.
* **%%sql -o \<nazwa zmiennej>** Wykonuje kwerendę hive względem sqlContext. Jeśli parametr -o jest przekazywany, wynik kwerendy jest zachowywany w kontekście %%local Python jako Pandas DataFrame.

Aby uzyskać więcej informacji na temat jąder notebooków Jupyter i wstępnie zdefiniowanych "magii", które dostarczają, zobacz [Jądra dostępne dla notebooków Jupyter z klastrami HDInsight Spark Linux w programie HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Pozyskiwania danych z publicznego obiektu blob:
Pierwszym krokiem w procesie nauki o danych jest pozyskiwanie danych do analizy ze źródeł, w których znajduje się w środowisku eksploracji i modelowania danych. To środowisko jest Spark w tym instruktażu. Ta sekcja zawiera kod do wykonania serii zadań:

* połknąć próbkę danych do modelowania
* odczytu w wejściowym zestawie danych (przechowywanym jako plik tsv)
* formatowanie i czyszczenie danych
* tworzenie i buforowanie obiektów (RDD lub ramek danych) w pamięci
* zarejestrować go jako temp-table w kontekście SQL.

Oto kod pozyskiwania danych.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
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

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    # CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Wyjście**

Czas wykonania powyżej komórki: 276,62 sekundy

## <a name="data-exploration--visualization"></a>Eksploracja danych & wizualizacja
Po wprowadzeniu danych do platformy Spark następnym krokiem w procesie nauki o danych jest uzyskanie głębszego zrozumienia danych poprzez eksplorację i wizualizację. W tej sekcji analizujemy dane taksówek przy użyciu zapytań SQL i wykreślić zmienne docelowe i funkcje prospektywne do kontroli wizualnej. W szczególności kreślimy częstotliwość liczby pasażerów w podróżach taksówką, częstotliwość kwot napiwków oraz to, jak wskazówki różnią się w zależności od kwoty płatności i typu.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Wykreślić histogram częstotliwości liczby pasażerów w próbie przejazdów taksówką
Ten kod i kolejne fragmenty kodu używają magii SQL do wykonywania zapytań o przykładową i lokalną magię w celu wykreślenia danych.

* **Magia`%%sql`SQL ( )** Jądro HDInsight PySpark obsługuje łatwe wbudowane zapytania HiveQL względem sqlContext. Argument (-o VARIABLE_NAME) utrzymuje dane wyjściowe kwerendy SQL jako Pandas DataFrame na serwerze Jupyter. Oznacza to, że jest on dostępny w trybie lokalnym.
* ** `%%local` Magia** służy do uruchamiania kodu lokalnie na serwerze Jupyter, który jest headnode klastra HDInsight. Zazwyczaj używasz `%%local` magii `%%sql -o` po magii jest używany do uruchamiania kwerendy. Parametr -o będzie zachowywać dane wyjściowe zapytania SQL lokalnie. Następnie `%%local` magia wyzwala następny zestaw fragmentów kodu do uruchomienia lokalnie względem danych wyjściowych zapytań SQL, które zostały utrwalone lokalnie. Dane wyjściowe są automatycznie wizualizowane po uruchomieniu kodu.

Ta kwerenda pobiera przejazdy według liczby pasażerów. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Ten kod tworzy lokalną ramkę danych z danych wyjściowych kwerendy i kreśli dane. Magia `%%local` tworzy lokalną ramkę `sqlResults`danych, która może być używana do drukowania za pomocą matplotlib. 

<!-- -->

> [!NOTE]
> Ta magia PySpark jest używana wiele razy w tym instruktażu. Jeśli ilość danych jest duża, należy próbkować, aby utworzyć ramkę danych, która może zmieścić się w pamięci lokalnej.

<!-- -->

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Oto kod do wykreślenia podróży według liczby pasażerów

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT PASSENGER NUMBER VS TRIP COUNTS
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**Wyjście**

![Częstotliwość podróży według liczby pasażerów](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Za pomocą przycisków menu **Tekst** w notesie można wybrać jeden z kilku różnych typów wizualizacji (Tabela, Ciasto, Linia, Obszar lub Pasek). Wykres słupka jest pokazany tutaj.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Wykreślić histogram kwot napiwków i jak wysokość napiwku różni się w zależności od liczby pasażerów i kwot taryfy.
Użyj kwerendy SQL do przykładu danych..

    # SQL SQUERY
    %%sql -q -o sqlResults
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train 
        WHERE passenger_count > 0 
        AND passenger_count < 7
        AND fare_amount > 0 
        AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 
        AND tip_amount < 25


Ta komórka kodu używa kwerendy SQL do utworzenia trzech wykresów danych.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline

    # TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # TIP BY PASSENGER COUNT
    ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount ($) by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
    ax.set_title('Tip amount by Fare amount ($)')
    ax.set_xlabel('Fare Amount')
    ax.set_ylabel('Tip Amount')
    plt.axis([-2, 120, -2, 30])
    plt.show()


**Wyjście:** 

![Dystrybucja kwoty porad](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Kwota napiwku według liczby pasażerów](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Kwota napiwku według taryfy Kwota](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Inżynieria funkcji, transformacja i przygotowanie danych do modelowania
W tej sekcji opisano i zawiera kod dla procedur używanych do przygotowania danych do użycia w modelowaniu ml. Pokazuje, jak wykonać następujące zadania:

* Tworzenie nowej funkcji przez partycjonowanie godzin do pojemników na czas ruchu
* Funkcje indeksowe i na gorąco kodują funkcje kategoryczne
* Tworzenie obiektów punktów oznaczonych etykietą do wprowadzania do funkcji ml
* Tworzenie losowego podpróbkowania danych i dzielenie ich na zestawy szkoleniowe i testowe
* Skalowanie cech
* Pamięć podręczna obiektów w pamięci

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Tworzenie nowej funkcji przez partycjonowanie czasu ruchu do pojemników
Ten kod pokazuje, jak utworzyć nową funkcję, partycjonując czas ruchu do pojemników, a następnie jak buforować wynikową ramkę danych w pamięci. Buforowanie prowadzi do wydłużonego czasu wykonywania, w którym odporne rozproszone zestawy danych (RDD) i ramki danych są używane wielokrotnie. Dlatego buforujemy rddy i ramki danych na kilku etapach w tym instruktażu.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**Wyjście**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Funkcje klasyczne indeksu i jednego skrótu kodowania
W tej sekcji pokazano, jak indeksować lub kodować funkcje kategoryczne do wprowadzania danych do funkcji modelowania. Modelowanie i przewidywanie funkcji MLlib wymagają, aby funkcje z kategorycznymi danymi wejściowymi były indeksowane lub kodowane przed użyciem. 

W zależności od modelu należy indeksować lub kodować je na różne sposoby. Na przykład modele regresji logistycznej i liniowej wymagają kodowania na gorąco, gdzie na przykład obiekt z trzema kategoriami można rozszerzyć na trzy kolumny obiektów, z których każda zawiera 0 lub 1 w zależności od kategorii obserwacji. MLlib zapewnia [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkcji do kodowania na jednym gorąco. Ten koder mapuje kolumnę indeksów etykiet do kolumny wektorów binarnych, z co najwyżej jedną jedną wartością. To kodowanie umożliwia algorytmy, które oczekują, że wartościowe funkcje, takie jak regresja logistyczna, mają być stosowane do funkcji kategorii.

Oto kod do indeksowania i kodowania funkcji kategorycznych:

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
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

    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Wyjście**

Czas wykonania powyżej komórki: 3,14 sekundy

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Tworzenie obiektów punktów oznaczonych etykietą do wprowadzania do funkcji ml
Ta sekcja zawiera kod, który pokazuje, jak indeksować dane tekstowe jako typ danych oznaczonych punkt i jak go zakodować. Transformacja ta przygotowuje dane tekstowe, które mają być używane do szkolenia i testowania regresji logistycznej MLlib i innych modeli klasyfikacji. Obiekty punktowe oznaczone etykietami są odpornymi rozproszonymi zestawami danych (RDD) sformatowanym w sposób, który jest potrzebny jako dane wejściowe przez większość algorytmów ML w MLlib. [Oznaczony punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) jest wektorem lokalnym, gęstym lub rzadkim, skojarzonym z etykietą/odpowiedzią.

Oto kod do indeksowania i kodowania funkcji tekstowych dla klasyfikacji binarnej.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                             line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                   line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Oto kod do kodowania i indeksowania funkcji tekstu kategorii do analizy regresji liniowej.

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Tworzenie losowego podpróbkowania danych i dzielenie ich na zestawy szkoleniowe i testowe
Ten kod tworzy losowe próbkowanie danych (25% jest używany tutaj). Chociaż nie jest to wymagane w tym przykładzie ze względu na rozmiar zestawu danych, możemy zademonstrować, jak można próbki danych w tym miejscu. Wtedy wiesz, jak go używać do własnego problemu, jeśli to konieczne. Gdy próbki są duże, próbkowanie może zaoszczędzić dużo czasu podczas modeli szkoleniowych. Następnie dzielimy próbkę na część szkoleniową (75% tutaj) i część testową (25% tutaj), aby użyć jej w modelowaniu klasyfikacji i regresji.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    from pyspark.sql.functions import rand

    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

    # CACHE TRAIN AND TEST DATA
    trainData.cache()
    testData.cache()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Wyjście**

Czas wykonania powyżej komórki: 0,31 sekundy

### <a name="feature-scaling"></a>Skalowanie cech
Skalowanie operacji, znany również jako normalizacji danych, ubezpiecza, że funkcje z powszechnie wypłaconych wartości nie są podane nadmierne ważenie w funkcji obiektywnej. Kod skalowania operacji używa [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) do skalowania operacji do odchylenia jednostki. Jest dostarczany przez MLlib do użytku w regresji liniowej z Stochastic Gradient Descent (SGD). SGD jest popularnym algorytmem do szkolenia szerokiej gamy innych modeli uczenia maszynowego, takich jak uregulowane regresje lub obsługuje maszyny wektorowe (SVM).   

> [!TIP]
> Firma Microsoft znaleziono LinearRegressionWithSGD algorytm być wrażliwe na skalowanie funkcji.   
> 
> 

Oto kod do skalowania zmiennych do użytku z regularnym liniowym algorytmem SGD.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils

    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Wyjście**

Czas wykonania powyżej komórki: 11,67 sekundy

### <a name="cache-objects-in-memory"></a>Pamięć podręczna obiektów w pamięci
Czas przeznaczony na szkolenie i testowanie algorytmów ml można skrócić, buforując obiekty ramki danych wejściowych używane do klasyfikacji, regresji i skalowane funkcje.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()

    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Wyjście** 

Czas wykonania powyżej komórki: 0,13 sekundy

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Wytypuj, czy wskazówka jest płatna za pomocą modeli klasyfikacji binarnej
W tej sekcji pokazano, jak używać trzech modeli do zadania klasyfikacji binarnej przewidywania, czy wskazówka jest płatna za podróż taksówką. Prezentowane modele to:

* Regresja logistyczna 
* Losowy las
* Zwiększanie gradientu drzew

Każda sekcja kodu budynku modelu jest podzielona na kroki: 

1. **Modelowanie** danych szkoleniowych z jednym zestawem parametrów
2. **Ocena modelu** na zestawie danych testowych z metrykami
3. **Zapisywanie modelu** w obiekcie blob dla przyszłego zużycia

Pokazujemy, jak wykonać krzyżową walidację (CV) z zamiataniem parametrów na dwa sposoby:

1. Przy użyciu **ogólnego** kodu niestandardowego, który może być stosowany do dowolnego algorytmu w MLlib i do dowolnych zestawów parametrów w algorytmie. 
2. Korzystanie z **funkcji potoku pySpark CrossValidator**. CrossValidator ma kilka ograniczeń dla Spark 1.5.0: 
   
   * Nie można zapisać ani utrwalić modeli potoku do przyszłego zużycia.
   * Nie można używać dla każdego parametru w modelu.
   * Nie można używać dla każdego algorytmu MLlib.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Ogólne sprawdzanie poprawności krzyżowej i przesączanie hiperparametru używane z algorytmem regresji logistycznej do klasyfikacji binarnej
Kod w tej sekcji pokazuje, jak trenować, oceniać i zapisywać model regresji logistycznej za pomocą [LBFGS,](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) który przewiduje, czy wskazówka jest płatna za podróż w zestawie danych dotyczących taksówek w Nowym Jorku i taryfy. Model jest szkolony przy użyciu sprawdzania poprawności krzyżowej (CV) i hyperparameter zamiatanie zaimplementowane z kodem niestandardowym, które mogą być stosowane do dowolnego algorytmów uczenia się w MLlib.   

<!-- -->

> [!NOTE]
> Wykonanie tego niestandardowego kodu CV może potrwać kilka minut.

<!-- -->

**Trenuj model regresji logistycznej przy użyciu cv i zamiatania hiperparametru**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from pyspark.mllib.evaluation import BinaryClassificationMetrics

    # CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
    from sklearn.grid_search import ParameterGrid
    grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
    paramGrid = list(ParameterGrid(grid))
    numModels = len(paramGrid)

    # SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
    nFolds = 3;
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    # BEGIN CV WITH PARAMETER SWEEP
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create LabeledPoints from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowOneHotBinary)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowOneHotBinary)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            regt = paramGrid[j]['regType']
            regp = paramGrid[j]['regParam']
            iters = paramGrid[j]['iterations']
            tol = paramGrid[j]['tolerance']
            # Train logistic regression model with hypermarameter set
            model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                      regParam=regp, tolerance = tol, intercept=True)
            predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
            # Use ROC-AUC as accuracy metrics
            validMetrics = BinaryClassificationMetrics(predictionAndLabels)
            metric = validMetrics.areaUnderROC
            metricSum[j] += metric

    avgAcc = metricSum / nFolds;
    bestParam = paramGrid[np.argmax(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    # TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
    logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                                  iterations=bestParam['iterations'], 
                                                  regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                                  intercept=True)


    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitBest.weights))
    print("Intercept: " + str(logitBest.intercept))

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Wyjście**

Współczynniki: [0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, - 0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Przecięcie: -0.0111216486893

Czas wykonania powyżej komórki: 14,43 sekundy

**Oceń model klasyfikacji binarnej za pomocą standardowych metryk**

Kod w tej sekcji pokazuje, jak ocenić model regresji logistycznej względem zestawu danych testowych, w tym wykres krzywej ROC.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT LIBRARIES
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # PREDICT ON TEST DATA WITH BEST/FINAL MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)

    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Wyjście**

Obszar pod PR = 0,985336538462

Powierzchnia pod ROC = 0.983383274312

Statystyki podsumowujące

Dokładność = 0,984174341679

Wycofanie = 0,984174341679

Wynik F1 = 0.984174341679

Czas wykonania powyżej komórki: 2,67 sekundy

**Wykreślić krzywą ROC.**

*PredictionAndLabelsDF* jest zarejestrowany jako tabela, *tmp_results*, w poprzedniej komórce. *tmp_results* może służyć do wykonywania zapytań i wyników wyjściowych do ramki danych sqlResults do drukowania. Oto kod.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Oto kod do prognozowania i działki ROC-krzywej.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES                              
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    #PREDICTIONS
    predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVES
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


**Wyjście**

![Krzywa regresji logistycznej ROC dla podejścia ogólnego](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Utrwalić model w obiekcie blob dla przyszłego zużycia**

Kod w tej sekcji pokazuje, jak zapisać model regresji logistycznej do użycia.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    # PERSIST MODEL
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;

    logitBest.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**Wyjście**

Czas wykonania powyżej komórki: 34,57 sekundy

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Użyj funkcji potoku CrossValidator firmy MLlib z modelem regresji logistycznej (regresja elastyczna)
Kod w tej sekcji pokazuje, jak trenować, oceniać i zapisywać model regresji logistycznej za pomocą [LBFGS,](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) który przewiduje, czy wskazówka jest płatna za podróż w zestawie danych dotyczących taksówek w Nowym Jorku i taryfy. Model jest szkolony przy użyciu sprawdzania poprawności krzyżowej (CV) i zamiatania hiperparametrycznego zaimplementowane za pomocą funkcji potoku MLlib CrossValidator dla CV z parametrami sweep.   

<!-- -->

> [!NOTE]
> Wykonanie tego kodu CV MLlib może potrwać kilka minut.

<!-- -->

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    from sklearn.metrics import roc_curve,auc

    # DEFINE ALGORITHM / MODEL
    lr = LogisticRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build()

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=BinaryClassificationEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    ## PREDICT AND EVALUATE ON TEST DATA-SET

    # USE TEST DATASET FOR PREDICTION
    testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
    test_predictions = cv_model.transform(testDataFrame)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**Wyjście**

Czas wykonania powyżej komórki: 107.98 sekundy

**Wykreślić krzywą ROC.**

*PredictionAndLabelsDF* jest zarejestrowany jako tabela, *tmp_results*, w poprzedniej komórce. *tmp_results* może służyć do wykonywania zapytań i wyników wyjściowych do ramki danych sqlResults do drukowania. Oto kod.

    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Oto kod do wykreślenia krzywej ROC.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
    %%local
    from sklearn.metrics import roc_curve,auc

    # ROC CURVE
    prob = [x["values"][1] for x in sqlResults["probability"]]
    fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    #PLOT
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


**Wyjście**

![Krzywa regresji logistycznej ROC przy użyciu CrossValidator MLliba](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Losowa klasyfikacja lasów
Kod w tej sekcji pokazuje, jak trenować, oceniać i zapisywać losową regresję lasu, która przewiduje, czy wskazówka jest płatna za podróż w zestawie danych o taksówce i taryfie w Nowym Jorku.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Wyjście**

Powierzchnia pod ROC = 0,985336538462

Czas wykonania powyżej komórki: 26,72 sekundy

### <a name="gradient-boosting-trees-classification"></a>Klasyfikacja drzew zwiększających gradient
Kod w tej sekcji pokazuje, jak trenować, oceniać i zapisywać gradientu zwiększenie modelu drzew, który przewiduje, czy wskazówka jest płatna za podróż w nyc taksówki i taryfy zestawu danych.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                    numIterations=10)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # Area under ROC curve
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;

    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Wyjście**

Powierzchnia pod ROC = 0,985336538462

Czas wykonania powyżej komórki: 28,13 sekundy

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Przewidywanie kwoty końcówki za pomocą modeli regresji (nie przy użyciu CV)
W tej sekcji pokazano, jak używać trzech modeli dla zadania regresji: przewidzieć kwotę napiwku zapłaconą za podróż taksówką na podstawie innych funkcji porad. Prezentowane modele to:

* Uregulowana regresja liniowa
* Losowy las
* Zwiększanie gradientu drzew

Modele te zostały opisane we wstępie. Każda sekcja kodu budynku modelu jest podzielona na kroki: 

1. **Modelowanie** danych szkoleniowych z jednym zestawem parametrów
2. **Ocena modelu** na zestawie danych testowych z metrykami
3. **Zapisywanie modelu** w obiekcie blob dla przyszłego zużycia   

<!-- -->

> [!NOTE] 
> Krzyżowe sprawdzanie poprawności nie jest używany z trzech modeli regresji w tej sekcji, ponieważ zostało to pokazane szczegółowo dla modeli regresji logistycznej. Przykład przedstawiający sposób używania CV z siatką elastyczną dla regresji liniowej znajduje się w dodatku do tego tematu.

<!-- -->

<!-- -->

> [!NOTE] 
> Z naszego doświadczenia mogą występować problemy z konwergencją modeli LinearRegressionWithSGD, a parametry muszą być starannie zmieniane/optymalizowane w celu uzyskania prawidłowego modelu. Skalowanie zmiennych znacznie pomaga w zbieżności. Regresja siatki elastycznej, pokazana w dodatku do tego tematu, może być również używana zamiast LinearRegressionWithSGD.

<!-- -->

### <a name="linear-regression-with-sgd"></a>Regresja liniowa z SGD
Kod w tej sekcji pokazuje, jak używać skalowanych funkcji do szkolenia regresji liniowej, która używa stochastic gradientu zejścia (SGD) do optymalizacji i jak oceniać, oceniać i zapisywać model w usłudze Azure Blob Storage (WASB).

> [!TIP]
> Z naszego doświadczenia mogą występować problemy z konwergencją modeli LinearRegressionWithSGD, a parametry muszą być starannie zmieniane/optymalizowane w celu uzyskania prawidłowego modelu. Skalowanie zmiennych znacznie pomaga w zbieżności.
> 
> 

    # LINEAR REGRESSION WITH SGD 

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats

    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))

    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)

    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;

    linearModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Wyjście**

Współczynniki: [0,0141707753435, -0.0252930927087, -0.0231442517137, 0.247070902996, 0.3125444147152, 0.360296120645, 0.01220795566092, -0.00456498588241, -0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632, -0.00289545676449, - 0.00791124681938, 0.54396316518, -0.536293513569, 0.0119076553369, -0.0173039244582, 0.0119632796147, 0.00146764882502]

Przecięcie: 0.854507624459

RMSE = 1,23485131376

R-sqr = 0,597963951127

Czas wykonania powyżej komórki: 38,62 sekundy

### <a name="random-forest-regression"></a>Losowa regresja lasu
Kod w tej sekcji pokazuje, jak trenować, oceniać i zapisywać losowy model lasu, który przewiduje kwotę porad dla danych podróży taksówką w Nowym Jorku.   

<!-- -->

> [!NOTE]
> Sprawdzanie poprawności krzyżowej z parametrami zamiatanie przy użyciu kodu niestandardowego znajduje się w dodatku.

<!-- -->

    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics


    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    # UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Wyjście**

RMSE = 0.931981967875

R-sqr = 0,733445485802

Czas wykonania powyżej komórki: 25,98 sekundy

### <a name="gradient-boosting-trees-regression"></a>Gradient zwiększający regresję drzew
Kod w tej sekcji pokazuje, jak trenować, oceniać i zapisywać gradientu zwiększenie modelu drzew, który przewiduje kwotę porad dla danych podróży taksówką NYC.

**Trenuj i oceniaj**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils

    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

    # EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
    test_predictions= sqlContext.createDataFrame(predictionAndLabels)
    test_predictions_pddf = test_predictions.toPandas()

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Wyjście**

RMSE = 0,928172197114

R-sqr = 0,732680354389

Czas wykonania powyżej komórki: 20,9 sekundy

**Działka**

*tmp_results* jest zarejestrowana jako tabela Hive w poprzedniej komórce. Wyniki z tabeli są dane wyjściowe do *sqlResults* ramki danych do kreślenia. Oto kod

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Oto kod do wykreślenia danych przy użyciu serwera Jupyter.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import numpy as np

    # PLOT
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 15])
    plt.show(ax)

![Rzeczywiste a przewidywane kwoty napiwków](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Dodatek: Dodatkowe zadania regresji przy użyciu sprawdzania poprawności krzyżowej z parametrami wyciągnięcia po ścieżce
Ten dodatek zawiera kod pokazujący, jak wykonać CV przy użyciu elastycznej sieci dla regresji liniowej i jak wykonać CV z parametrem sweep przy użyciu niestandardowego kodu dla losowej regresji lasu.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Sprawdzanie poprawności krzyżowej przy użyciu siatki elastycznej dla regresji liniowej
Kod w tej sekcji pokazuje, jak wykonać krzyżowe sprawdzanie poprawności przy użyciu elastycznej sieci dla regresji liniowej i jak ocenić model względem danych testowych.

    ###  CV USING ELASTIC NET FOR LINEAR REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.regression import LinearRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import RegressionEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

    # DEFINE ALGORITHM/MODEL
    lr = LinearRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build() 

    # DEFINE PIPELINE 
    # SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
    pipeline = Pipeline(stages=[lr])

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=RegressionEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    # EVALUATE MODEL ON TEST SET
    testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

    # MAKE PREDICTIONS ON TEST DOCUMENTS
    # cvModel uses the best model found (lrModel).
    predictionAndLabels = cv_model.transform(testDataFrame)

    # CONVERT TO DF AND SAVE REGISTER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Wyjście**

Czas wykonania powyżej komórki: 161,21 sekundy

**Oceń za pomocą metryki R-SQR**

*tmp_results* jest zarejestrowana jako tabela Hive w poprzedniej komórce. Wyniki z tabeli są dane wyjściowe do *sqlResults* ramki danych do kreślenia. Oto kod

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Oto kod do obliczania R-sqr.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**Wyjście**

R-sqr = 0,619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Sprawdzanie poprawności krzyżowej za pomocą wyciągnięcia po parametrach przy użyciu kodu niestandardowego dla losowej regresji lasu
Kod w tej sekcji pokazuje, jak wykonać krzyżowe sprawdzanie poprawności z parametrem sweep przy użyciu kodu niestandardowego dla losowej regresji lasu i jak ocenić model względem danych testowych.

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    # GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    from sklearn.grid_search import ParameterGrid

    ## CREATE PARAMETER GRID
    grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
    paramGrid = list(ParameterGrid(grid))

    ## SPECIFY LEVELS OF CATEGORICAL VARIBLES
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
    nFolds = 3;
    numModels = len(paramGrid)
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create labeled points from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowIndexingRegression)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowIndexingRegression)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            maxD = paramGrid[j]['maxDepth']
            numT = paramGrid[j]['numTrees']
            # Train logistic regression model with hypermarameter set
            rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=numT, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=maxD, maxBins=32)
            predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
            predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
            # Use ROC-AUC as accuracy metrics
            validMetrics = RegressionMetrics(predictionAndLabels)
            metric = validMetrics.rootMeanSquaredError
            metricSum[j] += metric

    avgAcc = metricSum/nFolds;
    bestParam = paramGrid[np.argmin(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    ## TRAIN FINAL MODL WIHT BEST PARAMETERS
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

    # EVALUATE MODEL ON TEST DATA
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    #PRINT TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Wyjście**

RMSE = 0,906972198262

R-sqr = 0,740751197012

Czas wykonania powyżej komórki: 69,17 sekundy

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Oczyszczanie obiektów z pamięci i drukowanie lokalizacji modelu
Służy `unpersist()` do usuwania obiektów buforowanych w pamięci.

    # UNPERSIST OBJECTS CACHED IN MEMORY

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    trainData.unpersist()
    trainData.unpersist()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()

    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()


**Wyjście**

PythonRDD[122] w: RDD w: PythonRDD.scala: 43

**Ścieżka wyjściowa do plików modelu, które mają być używane w notesie zużycia. ** Aby wykorzystać i zdobyć niezależny zestaw danych, musisz skopiować i wkleić te nazwy plików w "Notesie zużycia".

    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**Wyjście**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>Co dalej?
Teraz, gdy zostały utworzone regresji i klasyfikacji modeli z Spark MlLib, można przystąpić do uczenia się, jak oceniać i oceniać te modele.

**Zużycie modelu:** Aby dowiedzieć się, jak oceniać i oceniać modele klasyfikacji i regresji utworzone w tym temacie, zobacz [Ocenianie i ocenianie modeli uczenia maszynowego utworzonych przez platformę Spark.](spark-model-consumption.md)

