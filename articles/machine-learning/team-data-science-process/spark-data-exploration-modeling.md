---
title: Eksploracja i modelowanie danych za pomocą platformy Spark — proces nauki o danych zespołowych
description: Prezentuje możliwości eksploracji i modelowania danych zestawu narzędzi Spark MLlib na platformie Azure.
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
ms.openlocfilehash: 208f176ca942fb382ff2ed81d872602f7229b0a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718637"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Eksplorowanie i modelowanie danych za pomocą platformy Spark

W tym instruktażu używa hdinsight Spark do eksploracji danych i klasyfikacji binarnej i regresji zadania modelowania na próbkę podróży taksówką NYC i taryfy 2013 zestawu danych.  Przeprowadzi Cię przez kroki [procesu nauki o danych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, przy użyciu klastra platformy SPARK USŁUGI HDInsight do przetwarzania i obiektów blob platformy Azure do przechowywania danych i modeli. Proces eksploruje i wizualizuje dane wprowadzone z obiektu blob usługi Azure Storage, a następnie przygotowuje dane do tworzenia modeli predykcyjnych. Te modele są budowane przy użyciu zestawu narzędzi Spark MLlib do wykonywania zadań klasyfikacji binarnej i modelowania regresji.

* Zadaniem **klasyfikacji binarnej** jest przewidywanie, czy napiwek jest opłacany za podróż. 
* Zadaniem **regresji** jest przewidywanie ilości końcówki na podstawie innych funkcji porad. 

Modele, których używamy obejmują regresję logistyczną i liniową, losowe lasy i drzewa wzmocnione gradientem:

* [Regresja liniowa z SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) jest modelem regresji liniowej, który używa metody Stochastic Gradient Descent (SGD) oraz optymalizacji i skalowania operacji w celu przewidywania kwot końcówki zapłaconych. 
* [Regresja logistyczna z LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) lub regresją "logit" jest modelem regresji, który może być używany, gdy zmienna zależna jest kategoryczna do klasyfikacji danych. LBFGS to algorytm optymalizacji quasi-Newtona, który przybliża algorytm Broyden-Fletcher-Goldfarb-Shanno (BFGS) przy użyciu ograniczonej ilości pamięci komputera i jest szeroko stosowany w uczeniu maszynowym.
* [Losowe lasy](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) to zespoły drzew decyzyjnych.  Łączą one wiele drzew decyzyjnych, aby zmniejszyć ryzyko overfitting. Lasy losowe są używane do regresji i klasyfikacji i mogą obsługiwać funkcje kategoryczne i mogą być rozszerzone do ustawienia klasyfikacji wieloklasowej. Nie wymagają one skalowania operacji i są w stanie przechwytywać nieliniowe i interakcje funkcji. Lasy losowe są jednym z najbardziej udanych modeli uczenia maszynowego dla klasyfikacji i regresji.
* [Gradient wzmocnione drzewa](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) są zespoły drzew decyzyjnych. GBTS drzewa decyzyjne pociągu iteracyjne, aby zminimalizować funkcję utraty. GBTS jest używany do regresji i klasyfikacji i może obsługiwać funkcje kategoryczne, nie wymagają skalowania operacji i są w stanie przechwytywać nieliniowe i interakcje funkcji. Mogą być również używane w ustawieniach klasyfikacji wieloklasowej.

Kroki modelowania zawierają również kod pokazujący, jak trenować, oceniać i zapisywać każdy typ modelu. Python został użyty do zakodować rozwiązanie i pokazać odpowiednie wykresy.   

> [!NOTE]
> Chociaż zestaw narzędzi Spark MLlib jest przeznaczony do pracy na dużych zestawach danych, stosunkowo mała próbka (~30 Mb przy użyciu wierszy 170K, około 0,1% oryginalnego zestawu danych NYC) jest używana tutaj dla wygody. Ćwiczenie podane w tym miejscu działa wydajnie (w około 10 minut) w klastrze HDInsight z 2 węzłami procesu roboczego. Ten sam kod, z niewielkimi modyfikacjami, może służyć do przetwarzania większych zestawów danych, z odpowiednimi modyfikacjami buforowania danych w pamięci i zmiany rozmiaru klastra.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebujesz konta platformy Azure i klastra usługi Spark 1.6 (lub Spark 2.0). Zobacz [omówienie nauki o danych przy użyciu platformy Spark w usłudze Azure HDInsight,](spark-overview.md) aby uzyskać instrukcje dotyczące spełniania tych wymagań. Ten temat zawiera również opis nyc 2013 Taxi dane używane tutaj i instrukcje dotyczące wykonywania kodu z notesu Jupyter w klastrze Platformy Spark. 

## <a name="spark-clusters-and-notebooks"></a>Klastry i notesy platformy Spark
Kroki konfiguracji i kod są podane w tym instruktażu do korzystania z programu HDInsight Spark 1.6. Ale notebooki Jupyter są dostępne zarówno dla klastrów HDInsight Spark 1.6, jak i Spark 2.0. Opis notesów i łącza do nich znajdują się w [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierającego je. Ponadto kod w tym miejscu i w połączonych notesach jest ogólny i powinien działać w dowolnym klastrze platformy Spark. Jeśli nie używasz programu HDInsight Spark, kroki konfiguracji klastra i zarządzania klastra mogą się nieznacznie różnić od czynności przedstawionych w tym miejscu. Dla wygody, oto linki do notebooków Jupyter dla Spark 1.6 (do uruchomienia w jądrze pySpark serwera notebooków Jupyter) i Spark 2.0 (do uruchomienia w jądrze pySpark3 serwera notebooka Jupyter):

### <a name="spark-16-notebooks"></a>Notesy Spark 1.6

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Zawiera informacje na temat sposobu eksploracji danych, modelowania i oceniania za pomocą kilku różnych algorytmów.

### <a name="spark-20-notebooks"></a>Notesy Spark 2.0
Zadania regresji i klasyfikacji, które są implementowane przy użyciu klastra Platformy Spark 2.0 znajdują się w oddzielnych notesach, a notes klasyfikacji używa innego zestawu danych:

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Ten plik zawiera informacje na temat przeprowadzania eksploracji danych, modelowania i oceniania w klastrach Spark 2.0 przy użyciu podróży taksówką NYC i zestawu danych taryfy opisanych [tutaj](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Ten notes może być dobrym punktem wyjścia do szybkiego eksplorowania kodu, który podali dla platformy Spark 2.0. Aby uzyskać bardziej szczegółowy notes analizuje dane taksówki NYC, zobacz następny notes na tej liście. Zobacz notatki po tej liście, która porównuje te notesy. 
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Ten plik pokazuje, jak wykonywać wrangling danych (Spark SQL i operacji dataframe), eksploracji, modelowania i punktacji przy użyciu podróży taksówką NYC i taryfy data-set opisane [tutaj](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Ten plik pokazuje, jak wykonywać wrangling danych (Spark SQL i operacji dataframe), eksploracji, modelowania i oceniania przy użyciu dobrze znanego zestawu danych odlotów linii lotniczych na czas z 2011 i 2012. Przed modelingiem zintegrowaliśmy zestaw danych linii lotniczych z danymi pogodowymi na lotnisku (na przykład prędkością wiatru, temperaturą, wysokością itp.), dzięki czemu te funkcje pogodowe mogą być uwzględnione w modelu.

<!-- -->

> [!NOTE]
> Zestaw danych linii lotniczych został dodany do notesów Platformy Spark 2.0, aby lepiej zilustrować użycie algorytmów klasyfikacji. Zobacz następujące łącza, aby uzyskać informacje na temat zestawu danych o wyjeździe linii lotniczych na czas i zestawu danych pogodowych:
> 
> - Dane dotyczące odlotu linii lotniczych na czas:[https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Dane pogodowe na lotnisku:[https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 

<!-- -->

<!-- -->

> [!NOTE]
> Notesy Spark 2.0 na zestawach danych o opóźnieniach lotów w Nowym Jorku i opóźnieniu lotu linii lotniczych mogą trwać co najmniej 10 minut (w zależności od rozmiaru klastra HDI). Pierwszy notes na powyższej liście pokazuje wiele aspektów eksploracji danych, wizualizacji i szkolenia modelu ML w notesie, który zajmuje mniej czasu, aby uruchomić z pobranym próbką zestawu danych NYC, w którym pliki taksówek i taryf zostały wstępnie połączone: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) Ten notebook zajmuje znacznie krótszy czas, aby zakończyć (2-3 min) i może być dobrym punktem wyjścia do szybkiego zbadania kodu, który dostarczyliśmy dla Spark 2.0. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
> Poniższe opisy są związane z używaniem platformy Spark 1.6. W przypadku wersji platformy Spark 2.0 należy używać notesów opisanych i połączonych powyżej. 

<!-- -->

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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Importowanie bibliotek
Konfiguracja wymaga również importowania niezbędnych bibliotek. Ustaw kontekst iskry i zaimportuj niezbędne biblioteki za pomocą następującego kodu:

    # IMPORT LIBRARIES
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

Aby uzyskać więcej informacji na temat jąder notebooka Jupyter i wstępnie zdefiniowanych "magii", zobacz [Jądra dostępne dla notebooków Jupyter z klastrami HDInsight Spark Linux w programie HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Pozyskiwania danych z publicznego obiektu blob
Pierwszym krokiem w procesie nauki o danych jest pozyskiwanie danych do analizy ze źródeł, gdzie znajduje się w środowisku eksploracji i modelowania danych. Środowisko jest Spark w tym instruktażu. Ta sekcja zawiera kod do wykonania serii zadań:

* połknąć próbkę danych do modelowania
* odczytu w wejściowym zestawie danych (przechowywanym jako plik tsv)
* formatowanie i czyszczenie danych
* tworzenie i buforowanie obiektów (RDD lub ramek danych) w pamięci
* zarejestrować go jako temp-table w kontekście SQL.

Oto kod pozyskiwania danych.

    # INGEST DATA

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


    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**Wyjście:**

Czas wykonania powyżej komórki: 51,72 sekundy

## <a name="data-exploration--visualization"></a>Eksploracja danych & wizualizacja
Po wprowadzeniu danych do platformy Spark następnym krokiem w procesie nauki o danych jest uzyskanie głębszego zrozumienia danych poprzez eksplorację i wizualizację. W tej sekcji analizujemy dane taksówek przy użyciu zapytań SQL i wykreślić zmienne docelowe i funkcje prospektywne do kontroli wizualnej. W szczególności kreślimy częstotliwość liczby pasażerów w podróżach taksówką, częstotliwość kwot napiwków oraz to, jak wskazówki różnią się w zależności od kwoty płatności i typu.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Wykreślić histogram częstotliwości liczby pasażerów w próbie przejazdów taksówką
Ten kod i kolejne fragmenty kodu używają magii SQL do wykonywania zapytań o przykładową i lokalną magię w celu wykreślenia danych.

* **Magia`%%sql`SQL ( )** Jądro HDInsight PySpark obsługuje łatwe wbudowane zapytania HiveQL względem sqlContext. Argument (-o VARIABLE_NAME) utrzymuje dane wyjściowe kwerendy SQL jako Pandas DataFrame na serwerze Jupyter. To ustawienie powoduje udostępnienie danych wyjściowych w trybie lokalnym.
* ** `%%local` Magia** służy do uruchamiania kodu lokalnie na serwerze Jupyter, który jest headnode klastra HDInsight. Zazwyczaj używasz `%%local` magii w `%%sql` połączeniu z magią z parametrem -o. Parametr -o będzie zachowywać dane wyjściowe kwerendy SQL lokalnie, a następnie %%local magic wyzwoli następny zestaw fragmentu kodu, aby uruchomić lokalnie względem danych wyjściowych zapytań SQL, które są utrwalone lokalnie

Dane wyjściowe są automatycznie wizualizowane po uruchomieniu kodu.

Ta kwerenda pobiera przejazdy według liczby pasażerów. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Ten kod tworzy lokalną ramkę danych z danych wyjściowych kwerendy i kreśli dane. Magia `%%local` tworzy lokalną ramkę `sqlResults`danych, która może być używana do drukowania za pomocą matplotlib. 

> [!NOTE]
> Ta magia PySpark jest używana wiele razy w tym instruktażu. Jeśli ilość danych jest duża, należy próbkować, aby utworzyć ramkę danych, która może zmieścić się w pamięci lokalnej.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Oto kod do wykreślenia podróży według liczby pasażerów

    # PLOT PASSENGER NUMBER VS. TRIP COUNTS
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**Wyjście:**

![Częstotliwość podróży według liczby pasażerów](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Za pomocą przycisków menu **Tekst** w notesie można wybrać jeden z kilku różnych typów wizualizacji (Tabela, Ciasto, Linia, Obszar lub Pasek). Wykres słupka jest pokazany tutaj.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Wykreślić histogram kwot napiwków i jak wysokość napiwku różni się w zależności od liczby pasażerów i kwot taryfy.
Użyj kwerendy SQL do przykładu danych.

    #PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

    # HIVEQL QUERY AGAINST THE sqlContext
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

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 100, -2, 20])
    plt.show()


**Wyjście:** 

![Dystrybucja kwoty porad](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Kwota napiwku według liczby pasażerów](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Kwota napiwku według kwoty taryfy](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Inżynieria funkcji, transformacja i przygotowanie danych do modelowania
W tej sekcji opisano i zawiera kod dla procedur używanych do przygotowania danych do użycia w modelowaniu ml. Pokazuje, jak wykonać następujące zadania:

* Tworzenie nowej funkcji przez godziny binningu w zasobniku czasu ruchu
* Indeksowanie i kodowanie funkcji kategorycznych
* Tworzenie obiektów punktów oznaczonych etykietą do wprowadzania do funkcji ml
* Tworzenie losowego podpróbkowania danych i dzielenie ich na zestawy szkoleniowe i testowe
* Skalowanie cech
* Pamięć podręczna obiektów w pamięci

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Tworzenie nowej funkcji przez godziny binningu w zasobniku czasu ruchu
Ten kod pokazuje, jak utworzyć nową funkcję przez binning godzin w zasobników czasu ruchu, a następnie jak buforować wynikową ramkę danych w pamięci. Gdzie resilient rozproszonych zestawów danych (RDD) i ramek danych są używane wielokrotnie, buforowanie prowadzi do wydłużonego czasu wykonywania. W związku z tym możemy pamięci podręcznej RDD i ramek danych na kilku etapach w instruktażu. 

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

**Wyjście:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indeksowanie i kodowanie funkcji kategorycznych w celu wprowadzania danych do funkcji modelowania
W tej sekcji pokazano, jak indeksować lub kodować funkcje kategoryczne do wprowadzania danych do funkcji modelowania. Modelowanie i przewidywanie funkcji MLlib wymagają funkcji z kategorycznych danych wejściowych, które mają być indeksowane lub kodowane przed użyciem. W zależności od modelu należy indeksować lub kodować je na różne sposoby:  

* **Modelowanie oparte na drzewach** wymaga zakodowania kategorii jako wartości liczbowych (na przykład funkcja z trzema kategoriami może być zakodowana z 0, 1, 2). Algorytm ten jest dostarczany przez funkcję [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) firmy MLlib. Ta funkcja koduje kolumnę ciąg etykiet do kolumny indeksów etykiet, które są uporządkowane według częstotliwości etykiet. Chociaż indeksowane z wartościami liczbowymi do obsługi danych wejściowych i danych, algorytmy oparte na drzewie można określić, aby traktować je odpowiednio jako kategorie. 
* **Modele regresji logistycznej i liniowej** wymagają kodowania na gorąco, gdzie na przykład obiekt z trzema kategoriami można rozszerzyć na trzy kolumny obiektów, z których każda zawiera 0 lub 1 w zależności od kategorii obserwacji. MLlib zapewnia [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkcji do kodowania na jednym gorąco. Ten koder mapuje kolumnę indeksów etykiet do kolumny wektorów binarnych, z co najwyżej jedną jedną wartością. To kodowanie umożliwia algorytmy, które oczekują, że wartościowe funkcje, takie jak regresja logistyczna, mają być stosowane do funkcji kategorii.

Oto kod do indeksowania i kodowania funkcji kategorycznych:

    # INDEX AND ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES    
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

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

**Wyjście:**

Czas wykonania powyżej komórki: 1,28 sekundy

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Tworzenie obiektów punktów oznaczonych etykietą do wprowadzania do funkcji ml
Ta sekcja zawiera kod, który pokazuje, jak indeksować dane tekstowe jako typ danych oznaczonych punkt i zakodować go tak, aby można było go używać do szkolenia i testowania regresji logistycznej MLlib i innych modeli klasyfikacji. Obiekty punktowe oznaczone etykietami są odpornymi rozproszonymi zestawami danych (RDD) sformatowanym w sposób, który jest potrzebny jako dane wejściowe przez większość algorytmów ML w MLlib. [Oznaczony punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) jest wektorem lokalnym, gęstym lub rzadkim, skojarzonym z etykietą/odpowiedzią.  

Ta sekcja zawiera kod, który pokazuje, jak indeksować dane tekstowe jako typ danych [oznaczonych punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) i zakodować go tak, aby można było go używać do szkolenia i testowania regresji logistycznej MLlib i innych modeli klasyfikacji. Obiekty punktowe oznaczone etykietami to odporne rozproszone zestawy danych (RDD) składające się z etykiety (zmiennej docelowej/odpowiedzi) i wektora funkcji. Ten format jest potrzebny jako dane wejściowe przez wiele algorytmów ML w MLlib.

Oto kod do indeksowania i kodowania funkcji tekstowych dla klasyfikacji binarnej.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
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
Ten kod tworzy losowe próbkowanie danych (25% jest używany tutaj). Chociaż nie jest to wymagane w tym przykładzie ze względu na rozmiar zestawu danych, możemy zademonstrować, jak można spróbować tutaj, dzięki czemu wiesz, jak go używać do własnego problemu, gdy jest to potrzebne. Gdy próbki są duże, próbkowanie może zaoszczędzić dużo czasu podczas modeli szkoleniowych. Następnie dzielimy próbkę na część szkoleniową (75% tutaj) i część testową (25% tutaj), aby użyć jej w modelowaniu klasyfikacji i regresji.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.sql.functions import rand

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

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

**Wyjście:**

Czas wykonania powyżej komórki: 0,24 sekundy

### <a name="feature-scaling"></a>Skalowanie cech
Skalowanie operacji, znany również jako normalizacji danych, ubezpiecza, że funkcje z powszechnie wypłaconych wartości nie są podane nadmierne ważenie w funkcji obiektywnej. Kod skalowania operacji używa [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) do skalowania operacji do odchylenia jednostki. Jest dostarczany przez MLlib do użytku w regresji liniowej z Stochastic Gradient Descent (SGD), popularny algorytm do szkolenia szerokiej gamy innych modeli uczenia maszynowego, takich jak uregulowane regresje lub obsługuje maszyny wektorowe (SVM).

> [!NOTE]
> Firma Microsoft znaleziono LinearRegressionWithSGD algorytm być wrażliwe na skalowanie funkcji.
> 
> 

Oto kod do skalowania zmiennych do użytku z regularnym liniowym algorytmem SGD.

    # FEATURE SCALING

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

**Wyjście:**

Czas wykonania powyżej komórki: 13,17 sekundy

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

**Wyjście:** 

Czas wykonania powyżej komórki: 0,15 sekundy

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Wytypuj, czy wskazówka jest płatna za pomocą modeli klasyfikacji binarnej
W tej sekcji pokazano, jak używać trzech modeli do zadania klasyfikacji binarnej przewidywania, czy wskazówka jest płatna za podróż taksówką. Prezentowane modele to:

* Uregulowana regresja logistyczna 
* Losowy model lasu
* Zwiększanie gradientu drzew

Każda sekcja kodu budynku modelu jest podzielona na kroki: 

1. **Modelowanie** danych szkoleniowych z jednym zestawem parametrów
2. **Ocena modelu** na zestawie danych testowych z metrykami
3. **Zapisywanie modelu** w obiekcie blob dla przyszłego zużycia

### <a name="classification-using-logistic-regression"></a>Klasyfikacja przy użyciu regresji logistycznej
Kod w tej sekcji pokazuje, jak trenować, oceniać i zapisywać model regresji logistycznej za pomocą [LBFGS,](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) który przewiduje, czy wskazówka jest płatna za podróż w zestawie danych dotyczących taksówek w Nowym Jorku i taryfy.

**Trenuj model regresji logistycznej przy użyciu cv i zamiatania hiperparametru**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics


    # CREATE MODEL WITH ONE SET OF PARAMETERS
    logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                                   regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                                   tolerance=0.0001, validateData=True, numClasses=2)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitModel.weights))
    print("Intercept: " + str(logitModel.intercept))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Wyjście:** 

Współczynniki: [0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, - 0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Przecięcie: -0.0111216486893

Czas wykonania powyżej komórki: 14,43 sekundy

**Oceń model klasyfikacji binarnej za pomocą standardowych metryk**

    #EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # PREDICT ON TEST DATA WITH MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))

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


    ## SAVE MODEL WITH DATE-STAMP
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    logitModel.save(sc, dirfilename);

    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**Wyjście:** 

Obszar pod PR = 0,985297691373

Powierzchnia pod ROC = 0,983714670256

Statystyki podsumowujące

Dokładność = 0.984304060189

Wycofanie = 0.984304060189

Wynik F1 = 0.984304060189

Czas wykonania powyżej komórki: 57,61 sekundy

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

    # MAKE PREDICTIONS
    predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVE
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


**Wyjście:**

![Regresja logistyczna ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Losowa klasyfikacja lasów
Kod w tej sekcji pokazuje, jak trenować, oceniać i zapisywać losowy model lasu, który przewiduje, czy wskazówka jest płatna za podróż w zestawie danych dotyczących taksówek w Nowym Jorku i taryfy.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

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
    ## UN-COMMENT IF YOU WANT TO PRINT TREES
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

**Wyjście:**

Powierzchnia pod ROC = 0,985297691373

Czas wykonania powyżej komórki: 31,09 sekundy

### <a name="gradient-boosting-trees-classification"></a>Klasyfikacja drzew zwiększających gradient
Kod w tej sekcji pokazuje, jak trenować, oceniać i zapisywać gradientu zwiększenie modelu drzew, który przewiduje, czy wskazówka jest płatna za podróż w nyc taksówki i taryfy zestawu danych.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
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


**Wyjście:**

Powierzchnia pod ROC = 0,985297691373

Czas wykonania powyżej komórki: 19,76 sekundy

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Przewidywanie kwot napiwków dla przejazdów taksówką z modelami regresji
W tej sekcji pokazano, jak używać trzech modeli dla zadania regresji przewidywania kwoty napiwku zapłaconego za podróż taksówką na podstawie innych funkcji porad. Prezentowane modele to:

* Uregulowana regresja liniowa
* Losowy las
* Zwiększanie gradientu drzew

Modele te zostały opisane we wstępie. Każda sekcja kodu budynku modelu jest podzielona na kroki: 

1. **Modelowanie** danych szkoleniowych z jednym zestawem parametrów
2. **Ocena modelu** na zestawie danych testowych z metrykami
3. **Zapisywanie modelu** w obiekcie blob dla przyszłego zużycia

### <a name="linear-regression-with-sgd"></a>Regresja liniowa z SGD
Kod w tej sekcji pokazuje, jak używać skalowanych funkcji do szkolenia regresji liniowej, która używa stochastic gradientu zejścia (SGD) do optymalizacji i jak oceniać, oceniać i zapisywać model w usłudze Azure Blob Storage (WASB).

> [!TIP]
> Z naszego doświadczenia mogą występować problemy z konwergencją modeli LinearRegressionWithSGD, a parametry muszą być starannie zmieniane/optymalizowane w celu uzyskania prawidłowego modelu. Skalowanie zmiennych znacznie pomaga w zbieżności. 
> 
> 

    #PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

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

    # PRINT TEST METRICS
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;

    linearModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Wyjście:**

Współczynniki: [0,00457675809917, -0,0226314167349, -0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981, -0.000987181489428, -0.0888306617845, 0.0569376211553, 0.11551955171, 0.149250164995, - 0.00990211159703, -0.00637410344522, 0.545083566179, -0.536756072402, 0.0105762393099, -0.0130117577055, 0.0129304737772, -0.00171065945959]

Przecięcie: 0.853872718283

RMSE = 1,24190115863

R-sqr = 0,608017146081

Czas wykonania powyżej komórki: 58,42 sekundy

### <a name="random-forest-regression"></a>Losowa regresja lasu
Kod w tej sekcji pokazuje, jak trenować, oceniać i zapisywać losową regresję lasu, która przewiduje kwotę porad dla danych podróży taksówką w Nowym Jorku.

    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics


    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    ## PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
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

**Wyjście:**

RMSE = 0.891209218139

R-sqr = 0,759661334921

Czas wykonania powyżej komórki: 49,21 sekundy

### <a name="gradient-boosting-trees-regression"></a>Gradient zwiększający regresję drzew
Kod w tej sekcji pokazuje, jak trenować, oceniać i zapisywać gradientu zwiększenie modelu drzew, który przewiduje kwotę porad dla danych podróży taksówką NYC.

**Trenuj i oceniaj**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils

    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

    ## EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)

    # CONVERT RESULTS TO DF AND REGISTER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Wyjście:**

RMSE = 0,908473148639

R-sqr = 0,753835096681

Czas wykonania powyżej komórki: 34,52 sekundy

**Działka**

*tmp_results* jest zarejestrowana jako tabela Hive w poprzedniej komórce. Wyniki z tabeli są dane wyjściowe do *sqlResults* ramki danych do kreślenia. Oto kod

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Oto kod do wykreślenia danych przy użyciu serwera Jupyter.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    import numpy as np

    # PLOT 
    ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
    plt.axis([-1, 20, -1, 20])
    plt.show(ax)


**Wyjście:**

![Rzeczywiste a przewidywane kwoty napiwków](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Oczyszczanie obiektów z pamięci
Służy `unpersist()` do usuwania obiektów buforowanych w pamięci.

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()

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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Rejestrowanie lokalizacji przechowywania modeli do użycia i oceniania
Aby korzystać z niezależnego zestawu danych opisanego w temacie [Wyniki i oceniać modele uczenia maszynowego utworzone przez platformę Spark,](spark-model-consumption.md) należy skopiować i wkleić te nazwy plików zawierające zapisane modele utworzone w notesie Usługi Jupyter. Oto kod do drukowania ścieżek do plików modelu, które są potrzebne tam.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**Wyjście**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>Co dalej?
Teraz, gdy zostały utworzone regresji i klasyfikacji modeli z Spark MlLib, można przystąpić do uczenia się, jak oceniać i oceniać te modele. Zaawansowane eksploracji danych i modelowania notebooka zanurza się głębiej w tym krzyżowe walidacji, hyper-parametr sweeping i oceny modelu. 

**Zużycie modelu:** Aby dowiedzieć się, jak oceniać i oceniać modele klasyfikacji i regresji utworzone w tym temacie, zobacz [Ocenianie i ocenianie modeli uczenia maszynowego utworzonych przez platformę Spark.](spark-model-consumption.md)

**Krzyżowe walidacji i hyperparameter zamiatanie**: Zobacz [zaawansowane eksploracji danych i modelowania z Spark,](spark-advanced-data-exploration-modeling.md) w jaki sposób modele mogą być trenowane przy użyciu krzyżowej walidacji i hyper-parametr sweeping

