---
title: Eksploracja danych i modelowanie z Spark | Dokumentacja firmy Microsoft
description: Ilustrację Funkcje eksploracji i modelowanie danych zestawu narzędzi platformy Spark MLlib na platformie Azure.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: b989b918-5ba5-4696-b8d0-76ae510a23f4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: deguhath
ms.openlocfilehash: d505d432897eb7f4e792b45a95ec91ce1802eeb8
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="data-exploration-and-modeling-with-spark"></a>Eksplorowanie i modelowanie danych za pomocą platformy Spark
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

W tym przewodniku zastosowano Spark w usłudze HDInsight w celu Eksploracja danych i binarne klasyfikacji i regresji modelowania zadania, na przykład NYC taksówki podróży i taryfy 2013 zestawu danych.  Przeprowadzi Cię przez kroki [proces nauki danych](http://aka.ms/datascienceprocess)end-to- end, przy użyciu klastra Spark w usłudze HDInsight do przetwarzania i Azure BLOB do przechowywania danych i modelu. Proces Eksploruje wizualizuje dane pochodzące z obiektu Blob magazynu Azure i następnie przygotowuje danych do tworzenia modeli predykcyjnych. Te modele są kompilowane przy użyciu zestawu narzędzi platformy Spark MLlib celu binarne klasyfikacji i regresji zadań modelowania.

* **Klasyfikacji binarnej** zadania jest określenie, czy etykietki ma być stosowany do podróży. 
* **Regresji** zadania jest określenie ilość Porada na podstawie innych funkcji poradę. 

Modele używanych obejmują Regresja logistyczna i liniowych, losowe lasów i gradientu boosted drzew:

* [Regresji liniowej z SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) model regresji liniowej, który używa metody stochastycznego spadku gradientu (SGD) i dla optymalizacji i funkcja skalowania do prognozowania kwoty Porada płatnej. 
* [Regresja logistyczna z LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) lub regresji "logit" to model regresji, który może być używana podczas zależnych od zmiennej jest podzielone na kategorie do klasyfikacji danych. LBFGS to algorytm optymalizacji quasi Newton — która przybliża algorytmu Broyden — Fletcher — Goldfarb — Shanno (BFGS) przy użyciu ograniczoną ilość pamięci komputera i który jest powszechnie używany w uczeniu maszynowym.
* [Losowe lasów](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) są komplety drzewa decyzyjnego.  Łączą wiele drzew decyzyjnych, aby zmniejszyć ryzyko overfitting. Losowe lasach są używane do regresji i klasyfikacji i może obsługiwać funkcje podzielone na kategorie i może zostać rozszerzony do ustawienia wieloklasowej klasyfikacji. One nie wymagają funkcji skalowania i są w stanie przechwytywania nieliniowość i interakcje funkcji. Losowe lasach są jednym z modeli dla funkcji klasyfikacji i regresji uczenia maszynowego najbardziej popularnych.
* [Gradient boosted drzew](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) są komplety drzewa decyzyjnego. GBTs uczenie drzew decyzyjnych wielokrotnie powtarzane, aby zminimalizować funkcję utraty. GBTs służą do regresji i klasyfikacji można obsługiwać funkcji podzielone na kategorie, nie wymagają funkcji skalowania i są w stanie przechwytywania nieliniowość i interakcje funkcji. Ich można również w ustawieniu multiklasa klasyfikacji.

Kroki modelowania również zawierać kod, przedstawiający sposób uczenia, oceny i zapisać każdego typu modelu. Python został użyty do kodu rozwiązania i wyświetlania odpowiednich powierzchni.   

> [!NOTE]
> Chociaż toolkit Spark MLlib jest przeznaczona do pracy w dużych zestawów danych, przykładowe stosunkowo mały (~ 30 Mb przy użyciu 170K wierszy, około 0,1% oryginalnego zestawu danych NYC) jest używana w tym miejscu dla wygody. Ćwiczenie podane tutaj działa wydajnie (w około 10 minut) w klastrze usługi HDInsight z 2 węzłów procesu roboczego. Ten sam kod z drobne zmiany można przetworzyć większych-zestawów danych z odpowiednie modyfikacje dla pamięci podręcznej danych w pamięci i zmieniania rozmiaru klastra.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Potrzebujesz konta platformy Azure i Spark 1.6 (lub Spark 2.0) klastra usługi HDInsight w tym przewodniku. Zobacz [przegląd danych nauki używania platformy Spark w usłudze Azure HDInsight](spark-overview.md) instrukcje na temat sposobu spełniają tych wymagań. Ten temat zawiera również opis taksówki 2013 NYC danych używany w tym miejscu i instrukcje dotyczące sposobu wykonania kodu z notesu Jupyter w klastrze Spark. 

## <a name="spark-clusters-and-notebooks"></a>Klastry Spark i notebooki
Kroki instalacji i kodu są dostępne w tym przewodniku dla przy użyciu wersji 1.6 HDInsight Spark. Ale notesów Jupyter znajdują się w przypadku klastrów HDInsight Spark 1.6 jak Spark 2.0. Opis notesów i łącza do nich znajdują się w [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierające je. Ponadto kod w tym miejscu w notesach połączony jest rodzajowy i powinny działać na dowolnym klastra Spark. Jeśli nie używasz Spark w usłudze HDInsight, konfiguracja klastra i czynności administracyjne mogą być nieco inne niż to, co przedstawiono w tym miejscu. Dla wygody Oto łącza do notesu Jupyter w klastrze Spark w wersji 1.6 (do uruchomienia jądra pySpark serwera notesu Jupyter) i 2.0 Spark (do uruchomienia jądra pySpark3 serwera notesu Jupyter):

### <a name="spark-16-notebooks"></a>Notesów Spark w wersji 1.6

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): zawiera informacje na temat sposobu wykonywania Eksploracja danych, modelowania i oceniania z kilku różnych algorytmów.

### <a name="spark-20-notebooks"></a>Notesów Spark 2.0
Zadania regresji i klasyfikacji, które są implementowane przy użyciu klastra Spark 2.0 znajdują się w oddzielnych notesów i notesu klasyfikacji korzysta z innego zestawu danych:

- [Spark2.0-pySpark3-Machine-Learning-Data-Science-Spark-Advanced-Data-exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): ten plik zawiera informacje na temat sposobu wykonywania Eksploracja danych, modelowania, i oceniania w Spark 2.0 klastrów za pomocą taksówki NYC podróży i zestaw danych taryfy opisane [tutaj](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Ten notes może być dobry punkt wyjścia do eksplorowania szybko kod, który firma Microsoft umieściła 2.0 Spark. Dla bardziej szczegółowe notesu analizuje dane taksówki NYC, zobacz notesu dalej na tej liście. Zobacz uwagi na końcu tej listy pozwalające porównać te komputery przenośne. 
- [Spark2.0 pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): ten plik pokazano, jak wykonać danych wrangling (operacje Spark SQL i dataframe) eksploracji, modelowania i oceniania przy użyciu taksówki NYC podróży i taryfy zestawu danych opisane [tutaj ](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0 pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): ten plik pokazano, jak wykonać danych wrangling (operacje Spark SQL i dataframe) eksploracji, modelowania i oceniania przy użyciu znanych wyjścia w czasie linii lotniczych zestaw danych z 2011 i 2012. Zintegrowane firma Microsoft linii lotniczych zestawu danych z danymi pogody lotniska (np. prędkość wiatru, temperatury, wysokość itp.) przed modelowania, więc te funkcje pogody można dołączyć do modelu.

<!-- -->

> [!NOTE]
> Zestaw danych linii lotniczych został dodany do notesów Spark 2.0, aby lepiej zilustrować użyciem algorytmów klasyfikacji. Zobacz następujące linki do informacji na temat linii lotniczych na czas wyjścia zestawu danych i pogody dataset:

>- Dane na czas wyjścia linii lotniczych: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Dane pogody lotnisku: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
Notesy Spark 2.0 na NYC taksówkami i linii lotniczych transmitowane opóźnienie-zestawów danych może potrwać 10 minut lub dłużej (w zależności od wielkości klastra HDI). Pierwszy notesu na powyższej liście zawiera wiele aspektów Eksploracja danych, wizualizacji i ML szkolenia w notesie, który zajmuje mniej czasu do uruchomienia z próbkowany dół NYC zestaw danych, w którym pliki taksówkami i taryfy zostały wstępnie dołączonego do modelu: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) tego notesu przyjmuje znacznie krótszy czas na zakończenie (2 – 3 min) i może być bardzo punkt początkowy dla szybko Eksploracja kodu, firma Microsoft umieściła 2.0 Spark. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
Poniższe opisy są powiązane z użyciem Spark 1.6. W wersjach Spark 2.0 Użyj notesów opisane i linki umieszczono powyżej. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Instalatora: lokalizacje magazynu, biblioteki i wstępnie zdefiniowane kontekstu Spark
Platforma Spark jest możliwość odczytu i zapisu do obiektu Blob magazynu Azure (znanej także jako WASB). Dlatego żadnych istniejących danych przechowywanych mogą być przetwarzane przy użyciu platformy Spark i ponownie przechowywane w WASB wyniki.

Aby zapisać modele lub pliki w WASB, ścieżka musi prawidłowo określone. Domyślny kontener dołączony do klastra Spark można odwoływać się przy użyciu ścieżki rozpoczynającej się od: "wasb: / / /". Odwołuje się inne lokalizacje "wasb: / /".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Ustawianie ścieżki katalogu dla lokalizacji przechowywania w WASB
Poniższy przykład kodu Określa lokalizację danych do odczytu i ścieżkę do katalogu magazynu modelu, w którym jest zapisany modelu danych wyjściowych:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Importuj biblioteki
Konfigurowanie wymaga również importowanie wymagane biblioteki. Ustaw kontekst spark i zaimportuj wymagane biblioteki z następującym kodem:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Wstępnie zdefiniowane kontekstu Spark i poleceń magicznych PySpark
Jądra PySpark, które są dostarczane z notesów Jupyter ma wstępnie zdefiniowane kontekstu. Dlatego nie trzeba ustawić Spark lub tworzenia kontekstów Hive jawnie, przed rozpoczęciem pracy z aplikacją. Konteksty te są dostępne dla Ciebie domyślnie. Konteksty te są:

* sc - platformy Spark 
* Element sqlContext - gałęzi

Jądro PySpark zawiera kilka wstępnie zdefiniowanych "poleceń magicznych", które są specjalne polecenia, które można wywoływać z %%. Istnieją dwa polecenia, które są używane w tych przykładach kodu.

* **%% lokalnego** Określa, że kod w kolejnych wierszy jest wykonywana lokalnie. Kod musi być prawidłowy kod języka Python.
* **%% sql -o <variable name>**  wykonuje zapytanie Hive względem element sqlContext. Jeśli parametr -o zostanie przekazany, wynik kwerendy jest utrwalona w %% lokalny kontekst Python jako Pandas DataFrame.

Dla więcej informacji na temat jądra notesów Jupyter i wstępnie zdefiniowane "magics" który zapewniają, zobacz [jądra dostępne dla notesu Jupyter klastrze HDInsight Spark w systemie Linux klastrów HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Wprowadzanie danych z obiektu blob publiczny
Pierwszym krokiem w procesie nauki danych jest pozyskiwanie danych do analizy ze źródeł gdzie jest znajduje się w środowisku eksploracji i modelowanie danych. Środowisko jest Spark, w tym przewodniku. Ta sekcja zawiera kod, aby wykonać szereg zadań:

* Przykładowe dane do należy modelować pozyskiwania
* Przeczytaj w zestawie danych wejściowych (przechowywane jako plik .tsv)
* Formatowanie i czyszczenie danych
* Tworzenie i buforowania obiektów (RDDs lub ramek danych) w pamięci
* Zarejestruj go w postaci tabeli tymczasowej w kontekście SQL.

Oto kod wprowadzanie danych.

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

**DANE WYJŚCIOWE:**

Czas wykonywania nad komórką: 51.72 sekund

## <a name="data-exploration--visualization"></a>Eksploracja danych i wizualizacji
Po wprowadzeniu danych w Spark, następnym krokiem w procesie nauki danych jest lepiej zrozumieć dane za pośrednictwem eksploracji i wizualizacji. W tej sekcji możemy sprawdzanie danych taksówki za pomocą zapytań SQL i wykreślenia docelowych zmiennych i potencjalnego funkcje kontroli visual. W szczególności firma Microsoft wykreślenia częstotliwość liczby osób w podróży taksówki, częstotliwość kwoty Porada i jak porady różnią się zależnie od ilości płatności i typu.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Histogram częstotliwości liczba osób w próbce taksówki rund do wykreślenia
Ten kod i kolejne wstawki umożliwia SQL magic próbki oraz lokalnego magic danych zapytania.

* **Magiczna SQL (`%%sql`)** jądra HDInsight PySpark obsługuje zapytania HiveQL łatwe wbudowanego przed element sqlContext. (-O nazwa_zmiennej) argument będzie się powtarzał wyniki kwerendy SQL jako DataFrame Pandas, na serwerze Jupyter. Oznacza to, że jest on dostępny w trybie lokalnym.
* **`%%local` Magic** służy do uruchomienia kodu lokalnie na serwerze Jupyter jest headnode klastra usługi HDInsight. Zazwyczaj `%%local` magic w połączeniu z `%%sql` magic z parametrem -o. Parametru -o czy zachować dane wyjściowe kwerendy SQL lokalnie, a następnie %% magic lokalnego spowoduje wywołanie następnego zestawu fragment kodu w celu uruchomienia lokalnie wynik zapytania SQL jest trwały lokalnie

Dane wyjściowe automatyczna wizualizacja po uruchomieniu kodu.

To zapytanie pobiera rund według liczby osób. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Ten kod tworzy lokalne ramki danych z wyników kwerendy i zawiera dane. `%%local` Magic tworzy lokalne ramki danych, `sqlResults`, które mogą służyć do kreślenia z matplotlib. 

> [!NOTE]
> Ta magic PySpark jest używana wiele razy w tym przewodniku. W przypadku dużych ilości danych, powinny przykładowe tworzenia danych ramki, który można umieścić w lokalnej pamięci.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Oto kod do wykreślenia rund przez liczenie osób

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

**DANE WYJŚCIOWE:**

![Częstotliwość podróży według liczby osób](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Można dokonać wyboru spośród kilku różnych typów wizualizacji (tabeli, kołowego, wiersz, obszar lub pasek) przy użyciu **typu** przycisków menu notesu. Wykres słupkowy jest wyświetlany w tym miejscu.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Wykreślenia histogram kwot porady i jak kwota Porada jest zależna od ilości taryfy i liczba osób.
Użyj zapytanie SQL do przykładowych danych.

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


Ta komórka kod używa zapytania SQL w celu utworzenia trzy powierzchni danych.

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


**DANE WYJŚCIOWE:** 

![Porada kwota dystrybucji](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Porada kwota według liczby osób](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Porada kwota kwotę Taryfy](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Funkcja przygotowanie zespołu inżynieryjnego, przekształcania i danych do modelowania
Tej części opisano i przedstawiono kod dla procedur w celu przygotowania do użycia w ML modelowania danych. Widoczny jest sposób wykonywania następujących zadań:

* Utwórz nową funkcję o binning godziny do ruchu przedziałów czasu
* Indeks i kodowania funkcji podzielone na kategorie
* Tworzenie obiektów etykietą punktu na dane wejściowe do funkcji ML
* Tworzenie losowego podrzędne pobierania próbek danych i podziel go do celów szkoleniowych i testów zestawów
* Skalowanie funkcji
* Obiektów w pamięci podręcznej w pamięci

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Utwórz nową funkcję o binning godziny do ruchu przedziałów czasu
Ten kod przedstawia sposób tworzenia nowej funkcji według godzin binning do ruchu przedziałów czasu, a następnie jak buforować wynikowe ramki danych w pamięci. W przypadku, gdy odporność rozproszonych zestawów danych (RDDs) i ramek danych są używane wielokrotnie, buforowanie prowadzi do ulepszone czasu wykonywania. W związku z tym możemy pamięci podręcznej RDDs i ramek danych w kilku etapach przewodnika. 

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

**DANE WYJŚCIOWE:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indeks i kodowania podzielone na kategorie funkcje dla danych wejściowych do modelowania funkcji
W tej sekcji przedstawiono sposób indeksu lub zakodować podzielone na kategorie funkcje na dane wejściowe do funkcji modelowania. Modelowania i prognozowania z kategorii danych wejściowych indeksowanego lub zakodowane przed użyciem funkcji wymagają funkcji MLlib. W zależności od modelu musisz indeksu lub zakodować je na różne sposoby:  

* **Oparta na drzewie modelowania** wymaga kategorie, które mają być kodowane jako wartości liczbowe (na przykład funkcja z trzech kategorii może być zakodowane za pomocą 0, 1, 2). To są dostarczane przez firmy MLlib [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) funkcji. Ta funkcja koduje kolumny ciąg etykiet do kolumny indeksów etykiety, uporządkowanych według częstotliwości etykiety. Mimo że zaindeksowane wartości liczbowe wprowadzania i obsługi danych, można określić algorytmy oparta na drzewie odpowiednio traktując je jako kategorie. 
* **Modele logistyczna i regresji liniowej** wymagają dynamicznego jeden kodowania, where, na przykład funkcja z trzech kategorii można rozszerzyć do trzy kolumny funkcji, z każdym zawierających 0 lub 1 w zależności od rodzaju obserwacji. Udostępnia MLlib [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkcji dynamicznego jednego kodowania. Ten koder mapuje kolumny indeksów etykiety z kolumną wektorów binarnego z co najwyżej jedną co wartość. Ten typ kodowania umożliwia algorytmy, które oczekują numeryczny ważnych funkcji, takich jak Regresja logistyczna ma zostać zastosowany do funkcji podzielone na kategorie.

Oto kod do indeksu i kodowania funkcji podzielone na kategorie:

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

**DANE WYJŚCIOWE:**

Czas wykonywania nad komórką: 1,28 sekund

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Tworzenie obiektów etykietą punktu na dane wejściowe do funkcji ML
Ta sekcja zawiera kod, który pokazuje, jak indeksować dane tekstowe podzielone na kategorie jako typ etykietą punktu danych i kodować je, dzięki czemu może służyć do nauczenia i przetestowania Regresja logistyczna MLlib i inne modele klasyfikacji. Obiekty etykietą punktu są odporne rozproszonych zestawów danych (RDD) sformatowany w sposób, który jest potrzebny jako danych wejściowych przez większość algorytmów uczenia Maszynowego w MLlib. A [etykietą punktu](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) lokalnego wektora, zawierające gęsto lub rozrzedzony, skojarzony z etykiety/odpowiedź.  

Ta sekcja zawiera kod, który pokazuje, jak dane podzielone na kategorie tekstu jako indeksu [etykietą punktu](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) typ danych i kodować je, dzięki czemu może służyć do nauczenia i przetestowania Regresja logistyczna MLlib i inne modele klasyfikacji. Obiekty etykietą punktu są odporne rozproszonych zestawów danych (RDD) składające się z etykiety (docelowy/odpowiedzi zmienna) i funkcja wektora. Ten format jest potrzebny jako dane wejściowe wiele algorytmów uczenia Maszynowego w MLlib.

Oto kod do indeksu i kodowania tekstu funkcje klasyfikacji binarnej.

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

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Oto kod do kodowania i indeksu funkcje podzielone na kategorie tekstu do analizy regresji liniowej.

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


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Tworzenie losowego podrzędne pobierania próbek danych i podziel go do celów szkoleniowych i testów zestawów
Ten kod tworzy losowe pobierania próbek danych (25% służy w tym miejscu). Chociaż nie jest wymagana w tym przykładzie ze względu na rozmiar zestawu danych, przedstawiony sposób próbkę można pobrać tutaj, aby wiedzieć, jak użyć jej do własnych problem w razie potrzeby. W przypadku dużych przykłady to zapisanie długiego czasu podczas szkolenia modeli. Obok próbki możemy podzielić na części szkolenia (w tym miejscu 75%) i testowania część (25% tutaj) do użycia w klasyfikacji i regresji modelowania.

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

**DANE WYJŚCIOWE:**

Czas wykonywania nad komórką: 0,24 sekund

### <a name="feature-scaling"></a>Skalowanie funkcji
Skalowanie funkcji normalizacji danych, nazywany również temu, że funkcji z wartościami powszechnie rozchodów są nie podany nadmiernego porównać w celu funkcji. Kod dla funkcji skalowania używa [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) funkcje do wariancji jednostki skalowania. Do użytku w regresji liniowej z stochastycznego gradientu spadku (SGD), popularnych Algorytm uczenia szeroką gamę innych modeli, takie jak umorzyć regresji lub pomocy technicznej wektor maszyny (SVM) uczenia maszynowego są dostarczane przez MLlib.

> [!NOTE]
> Znaleziono uwzględniać funkcji skalowania algorytm LinearRegressionWithSGD.
> 
> 

Oto kod służący do skalowania zmienne do użytku z algorytmu regularized SGD liniowej.

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

**DANE WYJŚCIOWE:**

Czas wykonywania nad komórką: 13.17 sekund

### <a name="cache-objects-in-memory"></a>Obiektów w pamięci podręcznej w pamięci
Czas potrzebny do celów szkoleniowych i testów algorytmów uczenia Maszynowego można zmniejszyć przez buforowanie ramki danych wejściowych obiekty używane do klasyfikacji i regresji i funkcje skalowania.

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

**DANE WYJŚCIOWE:** 

Czas wykonywania nad komórką: 0,15 sekund

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Przewidywanie, czy z modelami klasyfikacji binarnej otrzymuje porady
W tej sekcji przedstawiono sposób użycia trzech modeli dla zadanie klasyfikacji binarnej przewidywania czy poradę ma być stosowany w podróży taksówki. Modele prezentowane są:

* Regresja logistyczna umorzyć 
* Model lasu losowych
* Gradientu zwiększania wyniku drzewa

Każdy model budowania sekcji kodu jest podzielony na kroki: 

1. **Model uczenia** danych za pomocą jednego zestawu parametrów
2. **Model oceny** na testowego zestawu danych o metryki
3. **Zapisywanie modelu** w obiekcie blob do użytku w przyszłości

### <a name="classification-using-logistic-regression"></a>Regresja logistyczna klasyfikacji
Kod w tej sekcji przedstawiono sposób uczenia, oceny i Zapisz model Regresja logistyczna z [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) który prognozuje czy Porada jest płatnej w podróży w NYC taksówki podróży i taryfy zestawu danych.

**Uczenie modelu Regresja logistyczna przy użyciu CV i kominów hyperparameter**

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


**DANE WYJŚCIOWE:** 

Coefficients: [0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Intercept:-0.0111216486893

Czas wykonywania nad komórką: 14.43 sekund

**Ocena modelu klasyfikacji binarnej o standardowych metryki**

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

**DANE WYJŚCIOWE:** 

Obszarze PR = 0.985297691373

Obszarze ROC = 0.983714670256

Podsumowanie statystyk

Dokładność = 0.984304060189

Odwołaj = 0.984304060189

F1 Wynik = 0.984304060189

Czas wykonywania nad komórką: 57.61 sekund

**Wykreślić krzywą ROC.**

*PredictionAndLabelsDF* jest zarejestrowany jako tabelę, *tmp_results*, w poprzedniej komórki. *tmp_results* może służyć zapytania i wyniki dane wyjściowe do ramki danych sqlResults do kreślenia. Oto kod.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Oto kod służący do tworzenia prognoz i wykreślić krzywą ROC.

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


**DANE WYJŚCIOWE:**

![Regresja logistyczna ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Klasyfikacja losowe lasu
Kod w tej sekcji przedstawiono sposób uczenia, oceny i zapisać modelu losowe lasu, który wskazuje, czy etykietki jest płatnej w podróży w NYC taksówki podróży i taryfy zestawu danych.

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

**DANE WYJŚCIOWE:**

Obszarze ROC = 0.985297691373

Czas wykonywania nad komórką: 31.09 sekund

### <a name="gradient-boosting-trees-classification"></a>Gradientu zwiększania wyniku klasyfikacji drzewa.
Kod w tej sekcji przedstawiono sposób uczenia, ocenić i Zapisz gradientu zwiększania wyniku modelu drzewa, który wskazuje, czy w podróży w podróży taksówki NYC otrzymuje porady i taryfy zestawu danych.

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


**DANE WYJŚCIOWE:**

Obszarze ROC = 0.985297691373

Czas wykonywania nad komórką: 19.76 sekund

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Porada kwoty rund taksówki z modelami regresji prognozowania
W tej sekcji przedstawiono, jak trzy modele użycia dla zadania regresji przewidywania ilość poradę płatnej w podróży taksówki na podstawie innych funkcji poradę. Modele prezentowane są:

* Umorzyć regresji liniowej
* Losowe lasu
* Gradientu zwiększania wyniku drzewa

Te modele zostały opisane w wprowadzenie. Każdy model budowania sekcji kodu jest podzielony na kroki: 

1. **Model uczenia** danych za pomocą jednego zestawu parametrów
2. **Model oceny** na testowego zestawu danych o metryki
3. **Zapisywanie modelu** w obiekcie blob do użytku w przyszłości

### <a name="linear-regression-with-sgd"></a>Regresji liniowej z SGD
Kod w tej sekcji pokazano, jak używać funkcji skalowany w celu przeszkolenia regresji liniowej używającego optymalizacji stochastycznego spadku gradientu (SGD) oraz jak wynik, oceny i zapisać modelu w usłudze Azure Blob Storage (WASB).

> [!TIP]
> Wiemy z doświadczenia mogą występować problemy z zbieżności LinearRegressionWithSGD modeli, a parametry muszą być zmienione/zoptymalizowaną dokładnie w celu uzyskania prawidłowego modelu. Skalowanie zmiennych znacznie ułatwia zbieżności. 
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

**DANE WYJŚCIOWE:**

Coefficients: [0.00457675809917, -0.0226314167349, -0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981, -0.000987181489428, -0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995, -0.00990211159703, -0.00637410344522, 0.545083566179, -0.536756072402, 0.0105762393099, -0.0130117577055, 0.0129304737772, -0.00171065945959]

Przechwycić: 0.853872718283

RMSE = 1.24190115863

R sqr = 0.608017146081

Czas wykonywania nad komórką: 58.42 sekund

### <a name="random-forest-regression"></a>Regresja losowe lasu
Kod w tej sekcji przedstawiono sposób uczenia, oceny i Zapisz regresji losowe lasu, który prognozuje Porada ilość danych NYC taksówki podróży.

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

**DANE WYJŚCIOWE:**

RMSE = 0.891209218139

R sqr = 0.759661334921

Czas wykonywania nad komórką: 49.21 sekund

### <a name="gradient-boosting-trees-regression"></a>Gradientu zwiększania wyniku regresji drzewa.
Kod w tej sekcji przedstawiono sposób uczenia, oceny i Zapisz gradientu zwiększania wyniku modelu drzewa, który prognozuje Porada ilość danych NYC taksówki podróży.

**Nauczanie i Ewaluacja**

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

    # CONVER RESULTS TO DF AND REGISER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**DANE WYJŚCIOWE:**

RMSE = 0.908473148639

R sqr = 0.753835096681

Czas wykonywania nad komórką: 34.52 sekund

**Kreślenia**

*tmp_results* jest zarejestrowany jako tabeli programu Hive w poprzedniej komórki. Wyniki z tabeli są dane wyjściowe do *sqlResults* ramki danych do kreślenia. Oto kod

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Oto kod do wykreślenia danych za pomocą serwera Jupyter.

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


**DANE WYJŚCIOWE:**

![Vs przewidzieć — Porada kwoty rzeczywiste](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Czyszczenie obiektów z pamięci
Użyj `unpersist()` można usunąć obiektów w pamięci podręcznej.

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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Lokalizacje przechowywania rekordu modeli konsumenckich i oceniania
Do wykorzystania i wynik niezależne zestawu danych opisanego w [wynik i ocena modeli uczenia maszynowego wbudowane Spark](spark-model-consumption.md) tematu, należy skopiować i wkleić te nazwy pliku zawierającego zapisany modeli utworzonych w tym miejscu do notesu Jupyter zużycia. Oto kod, aby wydrukować ścieżki do plików modelu, który należy.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**DANE WYJŚCIOWE**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>Co dalej?
Teraz, po utworzeniu modele regresji i klasyfikacji z Spark MlLib, możesz przystąpić do Dowiedz się, jak wynik i oceny tych modeli. Eksploracja danych zaawansowane i modelowanie notesu dives głębiej w tym krzyżowego sprawdzania poprawności, funkcji hyper parametr profilach i model oceny. 

**Model zużycia:** informacje na temat uczenie i Ewaluacja modeli klasyfikacji i regresji utworzone w tym temacie, zobacz [wynik i ocena modele uczenia wbudowane Spark maszyny](spark-model-consumption.md).

**Krzyżowe sprawdzanie poprawności i kominów hyperparameter**: zobacz [zaawansowane Eksploracja danych i modelowania z Spark](spark-advanced-data-exploration-modeling.md) na jak modeli można uczony przy użyciu kominów krzyżowego sprawdzania poprawności i parametru funkcji hyper

