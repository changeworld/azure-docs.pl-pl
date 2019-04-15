---
title: Eksplorowanie danych i modelowanie za pomocą platformy Spark — zespołu danych dla celów naukowych
description: Prezentuje możliwości Eksplorowanie i modelowanie danych zestawu narzędzi Biblioteka MLlib platformy Spark na platformie Azure.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 03/15/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: acc701431afa458efd0768fb3d6898fd1920e333
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528088"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Eksplorowanie i modelowanie danych za pomocą platformy Spark

W tym przewodniku używa platformy HDInsight Spark do wykonywania eksploracji danych i binarnej klasyfikacji i regresji, modelowanie zadania, na przykład NYC taksówki podróży, jak i klasie 2013 zestawu danych.  Przeprowadza użytkownika przez kroki [danych dla celów naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, przechowywanie danych i modele przy użyciu klastra usługi HDInsight Spark dla obiektów blob platformy Azure i związanych z przetwarzaniem. Proces analizuje i wizualizuje dane zaimportowane z usługi Azure Blob Storage i następnie przygotowuje dane w celu tworzenia modeli predykcyjnych. Te modele są kompilacji przy użyciu zestawu narzędzi Biblioteka MLlib platformy Spark w celu binarnej klasyfikacji i regresji zadań modelowania.

* **Klasyfikacji binarnej** zadania jest prognozowanie, czy porady zostało opłacone podróż. 
* **Regresji** zadania jest prognozowanie, kwotę napiwku na podstawie innych funkcji porada. 

Modeli, których używamy obejmują regresji logistycznej liniowego i liniowa, losowych lasów i gradientu wzmocnionego drzewa:

* [Regresja liniowa z SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) model regresji liniowej, który używa metody stochastycznego spadku gradientu (SGD) i do optymalizacji i funkcji skalowania do prognozowania kwoty Porada płatne. 
* [Regresji logistycznej przy użyciu LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) lub regresji "logit" jest model regresji, który można użyć, gdy zmienna zależnych od ustawień lokalnych jest podzielone na kategorie celu klasyfikacji danych. LBFGS jest algorytm optymalizacji quasi Newton — która przybliża algorytm Broyden — Fletcher — Goldfarb — Shanno (BFGS) przy użyciu ograniczona ilość pamięci komputera i która jest powszechnie używana w usłudze machine learning.
* [Losowe lasów](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) są decyzyjne drzewa decyzyjne.  Łączą wiele drzewa decyzyjne, aby zmniejszyć ryzyko overfitting. Losowe lasach są używane do regresji i klasyfikacji i mogą obsługiwać funkcje podzielonych na kategorie i można rozszerzyć na ustawienie klasyfikacji wieloklasowej. One skalowanie funkcja nie jest wymagane i są w stanie przechwytywania nieliniowość i są wyposażone w interakcje. Losowe lasy są jednymi z najbardziej popularnych modeli, które w funkcji klasyfikacji i regresji uczenia maszynowego.
* [Gradient wzmocnione drzewa](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) są decyzyjne drzewa decyzyjne. GBTs uczenie drzew decyzyjnych interakcyjnie, aby zminimalizować funkcję utraty. GBTs służą do regresji i klasyfikacji mogą obsługiwać funkcje podzielonych na kategorie, funkcja skalowanie nie jest wymagane i są w stanie przechwytywania nieliniowość i interakcje są wyposażone w. One można również w ustawieniu kontra klasyfikacji.

Kroki modelowania również zawierać kod, przedstawiający sposób uczenie, ocenę i Zapisz każdego typu modelu. Python został użyty do kodu rozwiązania i wyświetlić odpowiednie wykresy.   

> [!NOTE]
> Mimo że toolkit Biblioteka MLlib platformy Spark jest przeznaczona do pracy na dużych zestawach danych, stosunkowo małą próbkę (OK. 30 Mb w wierszach 170K, około 0,1% oryginalnego zestawu danych NYC) jest używany tutaj jako udogodnienie. Ćwiczenie podane tutaj działa wydajnie (w ciągu około 10 minut) w klastrze usługi HDInsight przy użyciu 2 węzłów procesu roboczego. Ten sam kod, za pomocą drobnych modyfikacji, może służyć do przetwarzania większych-zestawów danych przy użyciu odpowiednie modyfikacje dla buforowania danych w pamięci i zmienianie rozmiaru klastra.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Potrzebujesz konta platformy Azure i platformy Spark 1.6 (lub Spark 2.0) klastra HDInsight w celu przeprowadzenia tego instruktażu. Zobacz [Omówienie programu do nauki o danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md) w jaki sposób spełnić te wymagania. Ten temat zawiera również opis dane taksówek 2013 NYC użyty tutaj i instrukcje dotyczące sposobu wykonania kodu z notesu Jupyter w klastrze Spark. 

## <a name="spark-clusters-and-notebooks"></a>Klastry Spark i notesy
Kroki instalacji i kodu w tym przewodniku dla podano przy użyciu HDInsight Spark 1.6. Ale notesów programu Jupyter znajdują się w przypadku klastrów HDInsight Spark 1.6 i platformy Spark w wersji 2.0. Opis notesów i łącza do nich znajdują się w [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierające je. Ponadto kod w tym miejscu w notesach połączonej jest ogólny i powinna działać w dowolnym klastrze Spark. Jeśli nie używasz platformy HDInsight Spark, konfiguracja klastra i czynności administracyjne mogą nieznacznie różnić się od przedstawionego w tym miejscu. Dla wygody Oto łącza do notesów programu Jupyter, platformy Spark 1.6 (do uruchomienia jądra pySpark, serwera aplikacji Jupyter Notebook) i platformy Spark w wersji 2.0 (do uruchomienia w jądrze pySpark3 serwera aplikacji Jupyter Notebook):

### <a name="spark-16-notebooks"></a>Notesy platformy Spark 1.6

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Zawiera informacje dotyczące sposobu wykonywania eksploracji danych, modelowania i ocenianie z kilku różnych algorytmów.

### <a name="spark-20-notebooks"></a>Notesy platformy Spark w wersji 2.0
Zadania regresji i klasyfikacji, które są implementowane przy użyciu klastra Spark 2.0 znajdują się w oddzielnych notesów i notesu klasyfikacji korzysta z innego zestawu danych:

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Ten plik zawiera informacje na temat wykonywania eksploracji danych, modelowania, oraz do oceniania platformy Spark w wersji 2.0 klastrów za pomocą taksówek NYC podróży i klasie zestawu danych, opisano [tutaj](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Ten notes może być dobry punkt wyjścia do szybkiego analizowania kodu, który udostępniliśmy dla platformy Spark w wersji 2.0. Aby uzyskać bardziej szczegółowy notesu analizuje dane taksówek NYC, zobacz następnego notesu na tej liście. Zobacz uwagi na końcu tej listy pozwalające porównać te notesy. 
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Ten plik pokazuje sposób wykonania danych inteligencji (operacje Spark SQL i dataframe) eksploracji, modelowania i oceniania przy użyciu taksówek NYC podróży i klasie zestawu danych, opisano [tutaj](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Ten plik pokazuje, jak przeprowadzić danych inteligencji (operacje Spark SQL i dataframe) eksploracji, modelowania i oceniania przy użyciu dobrze znanych linie lotnicze w czasie wyjścia zestawu danych z 2011 i 2012. Zintegrowaliśmy linii lotniczych zestawu danych z danymi pogody Kuwejcie (np. prędkość wiatru, temperatury, wysokość itp.) przed modelowaniu, dzięki czemu te funkcje pogody mogły zostać uwzględnione w modelu.

<!-- -->

> [!NOTE]
> Linie lotnicze zestaw danych został dodany do notesów Spark 2.0, aby lepiej zilustrować używania algorytmów klasyfikacji. Zobacz poniższe linki do informacji na temat linii lotniczych na czas wyjścia zestawu danych i zestaw danych o pogodzie:
> 
> - Dane na czas wyjścia linii lotniczych: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Dane o pogodzie w Kuwejcie: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 

<!-- -->

<!-- -->

> [!NOTE]
> Notesy platformy Spark 2.0 na NYC taksówek i linie lotnicze lotu opóźnienie-zestawów danych może potrwać 10 minut lub dłużej (w zależności od rozmiaru klastra usługi HDI). Pierwszy notesu na powyższej liście przedstawiono wiele aspektów eksplorację, wizualizację i szkolenie modelu uczenia Maszynowego w notesie, który zajmuje mniej czasu do uruchomienia z próbkowana w dół NYC zestaw danych, w którym zostały wstępnie dołączonym do plików taksówek i klasie: [Spark2.0-pySpark3-Machine-Learning-Data-Science-Spark-Advanced-Data-exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) ten notes, trwa znacznie krótszy czas zakończenia (2-3 minuty) i może być dobry punkt początkowy dla szybkie analizowanie kodu zostały zamieszczone dla platformy Spark w wersji 2.0. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
> Poniższe opisy są powiązane z użyciem aparatu Spark 1.6. Dla wersji platformy Spark w wersji 2.0 Użyj notesów opisane i linki umieszczono powyżej. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Instalacji: lokalizacje przechowywania, biblioteki i wstępnie zdefiniowane kontekstu aparatu Spark
Platforma Spark jest możliwość odczytu i zapisu do usługi Azure Blob Storage (znany także jako WASB). Dlatego żadnych istniejących danych przechowywanych mogą być przetwarzane przy użyciu platformy Spark i najlepszych wyników ponownie przechowywane w WASB.

Aby zapisać modeli lub pliki w WASB, ścieżka musi być określona poprawnie. Kontener domyślny, dołączony do klastra Spark można się odwoływać przy użyciu ścieżki rozpoczynającej się od: "wasb: / / /". Odwołują się inne lokalizacje "wasb: / /".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Ustaw ścieżki katalogu lokalizacje przechowywania w WASB
Poniższy przykład kodu Określa lokalizację danych do odczytu i ścieżkę dla katalogu magazynu modelu, które są zapisywane dane wyjściowe modelu:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Importuj biblioteki
Konfigurowanie wymaga również importowania wymagane biblioteki. Ustaw kontekst aparatu spark i zaimportuj wymagane biblioteki z następującym kodem:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Ustawienie wstępne kontekstu aparatu Spark i poleceń magicznych PySpark
Jądra PySpark, które są dostarczane z notesów Jupyter mają wstępnie kontekstu. Dlatego nie należy ustawić Spark lub tworzenia gałęzi w kontekstach jawnie, przed rozpoczęciem pracy z aplikacją. Tych kontekstach są domyślnie dostępne. Tych kontekstach są następujące:

* SC - dla platformy Spark 
* sqlContext - programu Hive

Jądra PySpark zawiera kilka wstępnie zdefiniowanych "poleceń magicznych", które są specjalne polecenia, które można wywoływać za pomocą %%. Istnieją dwa polecenia, które są używane w tych przykładach kodu.

* **%% lokalnego** Określa, że kod w kolejnych wierszy jest wykonywana lokalnie. Kod musi być prawidłowy kod języka Python.
* **%% sql -o \<nazwa zmiennej >** wykonuje zapytanie programu Hive względem sqlContext. Jeśli parametr -o zostanie przekazana, wynik kwerendy są utrwalane w %% kontekstu Python lokalnego jako Pandas DataFrame.

Aby więcej informacji na temat jądra notesów programu Jupyter i wstępnie zdefiniowanego "magics", zapewniają one, zobacz [jądra, które są dostępne dla notesów programu Jupyter przy użyciu platformy Spark HDInsight w systemie Linux klastrów HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Pozyskiwanie danych z publicznego obiektu blob
Pierwszym krokiem w procesie nauki o danych jest pozyskiwanie danych do analizy ze źródeł gdzie jest znajduje się w środowisku Eksplorowanie i modelowanie danych. Środowisko jest platforma Spark w tym przewodniku. Ta sekcja zawiera kod, aby wykonać szereg zadań:

* próbka danych, aby modelować pozyskiwania
* Przeczytaj w zestawie danych wejściowych (przechowywany jako plik tsv)
* Formatowanie i czyszczenie danych
* Tworzenie i buforować obiekty (danych lub ramki danych) w pamięci
* Zarejestruj go jako tabeli tymczasowej w kontekście SQL.

Poniżej przedstawiono kod w celu pozyskiwania danych.

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

Czas wykonywania powyżej komórki: 51.72 sekund

## <a name="data-exploration--visualization"></a>Eksplorowanie danych i wizualizacja
Po wprowadzeniu danych do platformy Spark jest następnym krokiem w procesie nauki o danych, aby lepiej zrozumieć dane za pośrednictwem eksploracji i wizualizacji. W tej sekcji możemy przeanalizować dane taksówek za pomocą zapytań SQL i wykreślania docelowych zmiennych i potencjalnego funkcje kontroli. W szczególności firma Microsoft przedstawianie częstotliwość liczby pasażerów w podróży taksówek, częstotliwość Porada kwoty i jak porady różnią się zależnie od kwotę płatności i typu.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Histogram częstotliwości liczby pasażerów w próbce rund taksówek wykreślania
Ten kod i kolejne fragmenty umożliwia magicznym wyrażeniem SQL zapytania próbki i magic lokalnych danych.

* **Magicznym wyrażeniem SQL (`%%sql`)** jądra HDInsight PySpark obsługuje zapytania HiveQL łatwe wbudowane względem sqlContext. (-O nazwa_zmiennej) argument będzie się powtarzał wyniki kwerendy SQL jako ramkę danych Pandas na serwerze programu Jupyter. Oznacza to, że jest on dostępny w trybie lokalnym.
*  **`%%local` Magic** służy do uruchamiania kodu lokalnie na serwerze programu Jupyter, który jest węzłem głównym klastra HDInsight. Zazwyczaj można użyć `%%local` magic w połączeniu z `%%sql` magic z parametrem -o. Parametr -o będzie utrwalanie danych wyjściowych, zapytania SQL lokalnie i następnie %% lokalnego magic powodowało kolejny zbiór fragment kodu w celu uruchomienia lokalnie dane wyjściowe zapytań SQL, który jest trwały lokalnie

Dane wyjściowe są automatycznie wizualizowane po uruchomieniu kodu.

To zapytanie pobiera rund według liczby pasażerów. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Ten kod tworzy lokalnej ramce danych na podstawie wyników zapytania i drukuje dane. `%%local` Magic tworzy lokalnej ramce danych, `sqlResults`, który może służyć do kreślenia z matplotlib. 

> [!NOTE]
> Ta magic PySpark jest używana wiele razy, w tym przewodniku. W przypadku dużych ilości danych, powinny być przykładowe Utwórz ramkę danych który można umieścić w pamięci lokalnej.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Oto kod do wykreślenia w podróży, liczby pasażerów

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

![Częstotliwość podróży według liczby pasażerów](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Możesz wybrać spośród kilku różnych typów wizualizacji (tabeli, kołowego, linii, obszaru lub pasku) przy użyciu **typu** przycisków menu w notesie. Wykres słupkowy jest tu ukazywany.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Przedstawianie histogram Porada kwoty i jak ilość Porada zależy od kwoty turystycznej i liczba pasażerów.
Użyj zapytania SQL do przykładowych danych.

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


Tej komórki kodu używa zapytania SQL do tworzenia trzech powierzchni danych.

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

![Porada dystrybucji kwota](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Porada kwota według liczby pasażerów](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Porada kwota według ilości klasie](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Są wyposażone w inżynierii, przekształcania i danych przygotowania do modelowania
W tej sekcji opisano i zawiera kod dla procedur w celu przygotowywania danych do użycia w modelowaniu uczenia Maszynowego. Widoczny jest sposób wykonywania następujących zadań:

* Utwórz nową funkcję według godzin pakowania do przedziałów czasu ruchu
* Indeksowanie i kodowanie funkcji podzielonych na kategorie
* Tworzenie obiektów etykietą punktu wejścia do funkcji uczenia Maszynowego
* Tworzenie losowego podrzędnych pobierania próbek danych i podziel go na szkolenie i testowanie zestawów
* Skalowanie funkcji
* Obiekty w pamięci podręcznej w pamięci

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Utwórz nową funkcję według godzin pakowania do przedziałów czasu ruchu
Ten kod pokazuje, jak utworzyć nową funkcję według godzin pakowania w przedziały czasu ruchu, a następnie jak wynikowe ramki danych w pamięci w pamięci podręcznej. W przypadku, gdy odpornych rozproszonych zestawów danych (danych) i ramki danych są używane wielokrotnie, buforowanie prowadzi do ulepszone czasu wykonania. W związku z tym firma Microsoft w pamięci podręcznej zestawów danych i ramki danych w kilku etapach w instruktażu. 

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

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indeksowanie i kodowanie funkcji podzielonych na kategorie na dane wejściowe do funkcji modelowania
W tej sekcji przedstawiono sposób indeksu lub zakodować kategorii funkcji dla danych wejściowych do funkcji modelowania. Modelowania i przewidywanie funkcje MLlib wymagają funkcji z podzielonych na kategorie danych wejściowych, które mają być indeksowane lub zakodowane przed użyciem. W zależności od modelu musisz indeksu lub Zakoduj je na różne sposoby:  

* **Oparta na drzewie modelowania** wymaga kategorii do zakodowania jako wartości liczbowych (na przykład funkcji z trzech kategorii mogą być zakodowane przy użyciu 0, 1, 2). To są dostarczane przez firmy MLlib [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) funkcji. Ta funkcja koduje kolumną z ciągami etykiet z kolumną indeksami etykiety są uporządkowane według częstotliwości etykiety. Mimo że indeksowane, za pomocą wartości liczbowe dla danych wejściowych i obsługi danych, algorytmy oparta na drzewie można określić je traktować odpowiednio jako kategorie. 
* **Modeli logistyczną i regresji liniowej** wymagają hot jeden kodowania, where, na przykład funkcji z trzech kategorii można rozszerzyć do trzech kolumnach funkcji, z każdego zawierających 0 lub 1 w zależności od kategorii wystąpienia wartości. Udostępnia MLlib [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkcji hot jeden kodowania. Ten koder mapuje kolumny indeksów etykiety z kolumną wektorów binarnych z co najwyżej jeden — wartość typu single. To kodowanie umożliwia algorytmy, które oczekują liczbowe ważnych funkcji, takich jak regresji logistycznej, mają być stosowane do kategorii funkcje.

Poniżej przedstawiono kod, aby zaindeksować i kodowanie funkcji podzielonych na kategorie:

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

Czas wykonywania powyżej komórki: 1,28 sekund

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Tworzenie obiektów etykietą punktu wejścia do funkcji uczenia Maszynowego
Ta sekcja zawiera kod, który pokazuje, jak indeksowanie danych podzielonych na kategorie tekstu jako typ danych oznaczonych punktu i Zakoduj je, dzięki czemu może służyć do nauczenia i przetestowania regresji logistycznej MLlib i innych modeli klasyfikacji. Obiekty oznaczone punktu są odporne rozproszone zestawy danych (RDD) sformatowany w sposób, które są potrzebne najbardziej algorytmów uczenia Maszynowego w MLlib jako dane wejściowe. A [etykietą punktu](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) lokalnego wektor gęstą lub rozrzedzony, skojarzony z etykiety/odpowiedzi.  

Ta sekcja zawiera kod, który pokazuje, jak i indeksowanie danych podzielonych na kategorie tekstu jako [etykietą punktu](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) typ danych i Zakoduj je, dzięki czemu może służyć do nauczenia i przetestowania regresji logistycznej MLlib i innych modeli klasyfikacji. Obiekty oznaczone punktu są odporne rozproszone zestawów danych (RDD) składające się z etykiety (zmienna docelowego/odpowiedzi) i funkcja wektora. Ten format jest potrzebny jako dane wejściowe przez wiele algorytmów uczenia Maszynowego w MLlib.

Poniżej przedstawiono kod, aby zaindeksować i kodowanie tekstu funkcji Klasyfikacja binarna.

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


Poniżej przedstawiono kod w celu kodowania i indeksu funkcji podzielonych na kategorie tekstu analizę regresji liniowej.

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


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Tworzenie losowego podrzędnych pobierania próbek danych i podziel go na szkolenie i testowanie zestawów
Ten kod tworzy losowego próbkowanie danych (25% służy w tym miejscu). Chociaż nie jest wymagane w tym przykładzie ze względu na rozmiar zestawu danych, pokażemy, jak próbkę można pobrać tutaj aby wiedzieć, jak własne problem w razie potrzeby. W przypadku dużych przykłady to można zapisać znaczną ilość czasu podczas szkolenia modeli. Następnie możemy podzielić próbki część szkolenia (w tym miejscu 75%) i testowania część (25% tutaj) do użycia w funkcji klasyfikacji i regresji modelowania.

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

Czas wykonywania powyżej komórki: 0,24 sekund

### <a name="feature-scaling"></a>Skalowanie funkcji
Funkcja skalowania, nazywana również normalizacji danych ubezpieczycielom, że funkcje o wartościach powszechnie rozchodów są nie udzieliła nadmierne porównać w celu funkcji. Kod funkcji skalowania używa [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) te funkcje, aby odchylenie jednostki skalowania. Jest ona udostępniana przed MLlib do użycia w regresji liniowej z stochastycznego gradientu zejścia (SGD), popularnych algorytmów do trenowania szerokiej gamy innych modeli, takie jak umorzyć regresji lub pomocy technicznej wektor maszyny (SVM) uczenia maszynowego.

> [!NOTE]
> Wykryto algorytm LinearRegressionWithSGD być wrażliwa funkcji skalowania.
> 
> 

Poniżej przedstawiono kod w celu skalowania zmienne do użytku z programem regularized liniowego algorytmu SGD.

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

Czas wykonywania powyżej komórki: 13.17 sekund

### <a name="cache-objects-in-memory"></a>Obiekty w pamięci podręcznej w pamięci
Można zmniejszyć czas potrzebny na szkolenie i testowanie algorytmów uczenia Maszynowego, buforując ramki danych wejściowych, obiekty używane do klasyfikacji, regresji i skalowania funkcje.

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

Czas wykonywania powyżej komórki: 0,15 sekund

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Przewidywania, czy porady zostało opłacone przy użyciu modeli klasyfikacji binarnej
W tej sekcji przedstawiono sposób użycia trzech modeli w przypadku zadanie klasyfikacji binarnej Prognozowanie czy Porada zostało opłacone podróży taksówek. Modele prezentowane są następujące:

* Umorzyć regresji logistycznej 
* Model lasu losowe
* Gradient zwiększenie drzew

Każdy model tworzenia sekcji kodu zostanie podzielona na kroki: 

1. **Szkolenie modelu** danych za pomocą jeden zestaw parametrów
2. **Model oceny** na testowego zestawu danych za pomocą metryk
3. **Zapisywanie modelu** w obiekcie blob do przyszłego użytku

### <a name="classification-using-logistic-regression"></a>Klasyfikacja za pomocą regresji logistycznej
Kod w tej sekcji pokazano, jak uczenie, ocenę i Zapisz model regresji logistycznej przy użyciu [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) który prognozuje czy Porada czy płatna komunikacji dwustronnej w NYC taksówek podróży i klasie zestawu danych.

**Uczenia modelu regresji logistycznej przy użyciu stałych Nietrwałych i zaczynają hiperparametrycznego**

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

Czas wykonywania powyżej komórki: 14.43 sekund

**Model klasyfikacji binarnej przy użyciu standardowych metryk oceny**

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

Obszar, w ramach żądania Ściągnięcia = 0.985297691373

Obszar pod ROC = 0.983714670256

Podsumowanie statystyk

Precyzja = 0.984304060189

Odwołaj = 0.984304060189

F1 Ocena = 0.984304060189

Czas wykonywania powyżej komórki: 57.61 sekund

**Przedstawianie WIELOKLASOWA krzywa.**

*PredictionAndLabelsDF* jest zarejestrowany jako tabelę *tmp_results*, w poprzedniej komórki. *tmp_results* może służyć zapytania i dane wyjściowe wyniki do ramki danych sqlResults do kreślenia. Oto kod.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Poniżej przedstawiono kod, aby tworzyć prognozy i wykreślić krzywej ROC.

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
Kod w tej sekcji przedstawiono sposób uczenie, ocenę i Zapisz modelu losowe lasu, który czy Porada czy płatna komunikacji dwustronnej w NYC taksówek podróży i klasie zestawu danych.

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

Obszar pod ROC = 0.985297691373

Czas wykonywania powyżej komórki: 31.09 sekund

### <a name="gradient-boosting-trees-classification"></a>Gradient zwiększenie klasyfikacji drzew
Kod w tej sekcji przedstawiono sposób uczenie, ocenę i Zapisz gradientu zwiększenie drzewa modelu do prognozowania czy Porada czy płatna komunikacji dwustronnej w podróży taksówek NYC i taryfy zestawu danych.

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

Obszar pod ROC = 0.985297691373

Czas wykonywania powyżej komórki: 19.76 sekund

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Przewidywanie Porada kwoty rund taksówek za pomocą modele regresji
W tej sekcji przedstawiono, jak płatne użycie trzy modele dla zadania regresji prognozowania kwotę napiwku komunikacji dwustronnej taksówek, w oparciu o inne funkcje porada. Modele prezentowane są następujące:

* Umorzyć regresji liniowej
* Losowe lasu
* Gradient zwiększenie drzew

Modele te zostały opisane we wstępie. Każdy model tworzenia sekcji kodu zostanie podzielona na kroki: 

1. **Szkolenie modelu** danych za pomocą jeden zestaw parametrów
2. **Model oceny** na testowego zestawu danych za pomocą metryk
3. **Zapisywanie modelu** w obiekcie blob do przyszłego użytku

### <a name="linear-regression-with-sgd"></a>Regresja liniowa z SGD
W kodzie w tej sekcji pokazano, jak używać funkcji skalowane do nauczenia regresji liniowej, który używa stochastycznego spadku gradientu (wstecznej Propagacji) na potrzeby optymalizacji i jak ocena, oceny i zapisywanie modelu w usłudze Azure Blob Storage (WASB).

> [!TIP]
> W naszych doświadczeń wynika mogą występować problemy ze zbieżności LinearRegressionWithSGD modeli i parametry muszą być zmienione/zoptymalizowaną dokładnie w celu uzyskania prawidłowego modelu. Skalowanie zmiennych znacznie ułatwić realizację zbieżności. 
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

Przechwytywanie: 0.853872718283

RMSE = 1.24190115863

R sqr = 0.608017146081

Czas wykonywania powyżej komórki: 58.42 sekund

### <a name="random-forest-regression"></a>Regresja Random lasu
Kod w tej sekcji przedstawiono sposób uczenie, ocenę i Zapisz regresji losowe lasu, który prognozuje kwotę wskazówka dla danych podróży NYC taksówek.

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

Czas wykonywania powyżej komórki: 49.21 sekund

### <a name="gradient-boosting-trees-regression"></a>Zwiększenie regresji drzew gradientu
Kod w tej sekcji przedstawiono sposób uczenie, ocenę i Zapisz gradientu zwiększenie drzewa modelu do prognozowania kwotę wskazówka dla danych podróży NYC taksówek.

**Nauczanie i ocena**

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

**DANE WYJŚCIOWE:**

RMSE = 0.908473148639

R sqr = 0.753835096681

Czas wykonywania powyżej komórki: 34.52 sekund

**Wykres**

*tmp_results* jest zarejestrowany jako tabeli programu Hive w poprzedniej komórki. Wyniki z tabeli są danymi wyjściowymi przekazywanymi do *sqlResults* ramki danych do kreślenia. W tym miejscu znajduje się kod

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Poniżej przedstawiono kod, do wykreślenia danych za pomocą serwera programu Jupyter.

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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Lokalizacje przechowywania rekordu zużycia i oceniania modeli
Używanie i oceniać zestaw niezależnych opisanego w [wynik i ocena modeli uczenia maszynowego utworzonych na platformie Spark](spark-model-consumption.md) tematu, należy skopiować i wkleić te nazwy plików zawierające zapisane modele utworzone w tym miejscu do użycia Notes Jupyter. Poniżej przedstawiono kod, aby wydrukować ścieżki do plików modeli, których potrzebujesz.

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
Teraz, modele regresji i klasyfikacji została utworzona przy użyciu MlLib platformy Spark, jesteś gotowy dowiedzieć się, jak ocena i ocenić te modele. Zaawansowane Eksplorowanie danych i modelowania notesu omawia bardziej krzyżowa Weryfikacja, parametrów sprawdzaniu, w tym i ocenę modelu. 

**Użycie modelu:** Aby dowiedzieć się, jak ocena i oceniać modele klasyfikacji i regresji, utworzone w tym temacie, zobacz [wynik i ocena modeli uczenia maszynowego utworzonych na platformie Spark](spark-model-consumption.md).

**Krzyżowa Weryfikacja i zaczynają hiperparametrycznego**: Zobacz [zaawansowane Eksplorowanie i modelowanie za pomocą platformy Spark danych](spark-advanced-data-exploration-modeling.md) w sposób modeli może być uczony przy użyciu zaczynają krzyżowego sprawdzania poprawności i parametrów

