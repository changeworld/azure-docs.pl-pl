---
title: Zaawansowane Eksplorowanie danych i modelowanie za pomocą platformy Spark — zespołu danych dla celów naukowych
description: Użyj platformy HDInsight Spark eksploracji danych i uczenia binarne modeli klasyfikacji i regresji za pomocą optymalizacji krzyżowego sprawdzania poprawności i hiperparametrycznego.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 02/15/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 5f6145e581393d874871d214515a660f987d1d7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60253430"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Zaawansowane eksplorowanie i modelowanie danych za pomocą platformy Spark

W tym instruktażu wykorzystano HDInsight Spark eksploracji danych i szkolenie klasyfikacji binarnej i modele regresji, przy użyciu krzyżowego sprawdzania poprawności i optymalizacji hiperparametrycznego odnośnie do przykładu NYC taksówki podróży i klasie 2013 zestawu danych. Przeprowadza użytkownika przez kroki [danych dla celów naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, przechowywanie danych i modele przy użyciu klastra usługi HDInsight Spark dla obiektów blob platformy Azure i związanych z przetwarzaniem. Proces analizuje i wizualizuje dane zaimportowane z usługi Azure Blob Storage i następnie przygotowuje dane w celu tworzenia modeli predykcyjnych. Python został użyty do kodu rozwiązania i wyświetlić odpowiednie wykresy. Te modele są kompilacji przy użyciu zestawu narzędzi Biblioteka MLlib platformy Spark w celu binarnej klasyfikacji i regresji zadań modelowania. 

* **Klasyfikacji binarnej** zadania jest prognozowanie, czy porady zostało opłacone podróż. 
* **Regresji** zadania jest prognozowanie, kwotę napiwku na podstawie innych funkcji porada. 

Kroki modelowania również zawierać kod, przedstawiający sposób uczenie, ocenę i Zapisz każdego typu modelu. Omówiono także niektóre z tych samych podstaw jako [Eksplorowanie i modelowanie za pomocą platformy Spark danych](spark-data-exploration-modeling.md) tematu. Jednak go jest bardziej "Zaawansowane" w tym również za pomocą krzyżowego sprawdzania poprawności hiperparametrycznego sprawdzaniu to w opracowywaniu optymalnie dokładnych modeli klasyfikacji i regresji. 

**Krzyżowa Weryfikacja (CV)** to technika, która ocenia, jak dobrze uogólnia model skonfigurowanych pod kątem w znanego zestawu danych przewidzenia funkcje zestawów danych, na którym ją nie przeprowadzono.  Najczęstszą implementacją używane w tym miejscu jest podzielić zestawu danych złożeń K i następnie uczenia modelu w sposób okrężny we wszystkich oprócz jednego złożeń. Możliwości modelu do prognozowania dokładnie, gdy testowane w odniesieniu do niezależnego zestawu danych w tym zwijania nie są używane do nauczenia modelu jest oceniane.

**Optymalizacja Hiperparametrycznego** problemu wybierania zestaw hiperparametrów dla algorytmu uczenia, zazwyczaj z celem miary wydajności tego algorytmu o zestaw danych, niezależnie od optymalizacji. **Hiperparametrów** są wartościami, które musi być określona poza procedury szkolenie modelu. Założenia dotyczące tych wartości może wpłynąć na elastyczność i dokładność modeli. Drzewa decyzyjne mają hiperparametrów, na przykład, takich jak żądany głębi i liczba pozostawia w drzewie. Obsługa wektor maszyny (SVMs) wymagają, aby ustawienie błędu klasyfikacji spadek czasu trwania umowy. 

Typowym sposobem wykonania hiperparametrycznego optymalizacji używany w tym miejscu jest wyszukiwanie siatki lub **parametrów**. Obejmuje to wykonuje kompleksowe przeszukiwanie wartości określony podzbiór miejsca hiperparametrycznego dla algorytmu uczenia. Krzyżowego sprawdzania poprawności można podać Metryka wydajności, aby posortować optymalne wyniki generowane przez algorytm wyszukiwania siatki. CV używane z pomaga polegających na usuwaniu hiperparametrycznego limit problemów, takich jak overfitting model, aby dane szkoleniowe, dzięki czemu model zachowuje zdolności do zastosowania do ogólne zestawu danych, z którego został wyodrębniony dane szkoleniowe.

Modeli, których używamy obejmują regresji logistycznej liniowego i liniowa, losowych lasów i gradientu wzmocnionego drzewa:

* [Regresja liniowa z SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) model regresji liniowej, który używa metody stochastycznego spadku gradientu (SGD) i do optymalizacji i funkcji skalowania do prognozowania kwoty Porada płatne. 
* [Regresji logistycznej przy użyciu LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) lub regresji "logit" jest model regresji, który można użyć, gdy zmienna zależnych od ustawień lokalnych jest podzielone na kategorie celu klasyfikacji danych. LBFGS jest algorytm optymalizacji quasi Newton — która przybliża algorytm Broyden — Fletcher — Goldfarb — Shanno (BFGS) przy użyciu ograniczona ilość pamięci komputera i która jest powszechnie używana w usłudze machine learning.
* [Losowe lasów](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) są decyzyjne drzewa decyzyjne.  Łączą wiele drzewa decyzyjne, aby zmniejszyć ryzyko overfitting. Losowe lasach są używane do regresji i klasyfikacji i mogą obsługiwać funkcje podzielonych na kategorie i można rozszerzyć na ustawienie klasyfikacji wieloklasowej. One skalowanie funkcja nie jest wymagane i są w stanie przechwytywania nieliniowość i są wyposażone w interakcje. Losowe lasy są jednymi z najbardziej popularnych modeli, które w funkcji klasyfikacji i regresji uczenia maszynowego.
* [Gradient wzmocnione drzewa](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) są decyzyjne drzewa decyzyjne. GBTs uczenie drzew decyzyjnych interakcyjnie, aby zminimalizować funkcję utraty. GBTs służą do regresji i klasyfikacji mogą obsługiwać funkcje podzielonych na kategorie, funkcja skalowanie nie jest wymagane i są w stanie przechwytywania nieliniowość i interakcje są wyposażone w. One można również w ustawieniu kontra klasyfikacji.

Modelowanie za pomocą stałych Nietrwałych i Hiperparametrycznego przykłady przedstawiono odchylenia problemu klasyfikacji binarnej. Przykłady prostsze (bez parametru wrzucając) są prezentowane w tematem głównym dla zadań regresji. Jednak w dodatku, weryfikacja za pomocą elastycznych net regresji liniowej i stałych Nietrwałych za pomocą parametru odchylenia przy użyciu regresji losowe lasu są także przedstawione. **Elastyczne netto** jest metodą regresji umorzyć dopasowanie modele regresji liniowej, który liniowo łączy P1 i P2 metryki jak karnymi z [lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) i [ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization)metody.   

> [!NOTE]
> Mimo że toolkit Biblioteka MLlib platformy Spark jest przeznaczona do pracy na dużych zestawach danych, stosunkowo małą próbkę (OK. 30 Mb w wierszach 170K, około 0,1% oryginalnego zestawu danych NYC) jest używany tutaj jako udogodnienie. Ćwiczenie podane tutaj działa wydajnie (w ciągu około 10 minut) w klastrze usługi HDInsight przy użyciu 2 węzłów procesu roboczego. Ten sam kod, za pomocą drobnych modyfikacji, może służyć do przetwarzania większych-zestawów danych przy użyciu odpowiednie modyfikacje dla buforowania danych w pamięci i zmienianie rozmiaru klastra.
> 
> 

## <a name="setup-spark-clusters-and-notebooks"></a>Konfiguracja: Klastry Spark i notesy
Kroki instalacji i kodu w tym przewodniku dla podano przy użyciu HDInsight Spark 1.6. Ale notesów programu Jupyter znajdują się w przypadku klastrów HDInsight Spark 1.6 i platformy Spark w wersji 2.0. Opis notesów i łącza do nich znajdują się w [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierające je. Ponadto kod w tym miejscu w notesach połączonej jest ogólny i powinna działać w dowolnym klastrze Spark. Jeśli nie używasz platformy HDInsight Spark, konfiguracja klastra i czynności administracyjne mogą nieznacznie różnić się od przedstawionego w tym miejscu. Dla wygody Oto łącza do notesów programu Jupyter, platformy Spark 1.6 i można uruchomić jądra pyspark serwera notesu Jupyter w wersji 2.0:

### <a name="spark-16-notebooks"></a>Notesy platformy Spark 1.6

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Zawiera tematy w notesie #1 i opracowywania modelu strojenia hiperparametrycznego i krzyżowego sprawdzania poprawności.

### <a name="spark-20-notebooks"></a>Notesy platformy Spark w wersji 2.0

[Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Ten plik zawiera informacje dotyczące sposobu wykonywania eksploracji danych, modelowania i ocenianie w klastrach platformy Spark w wersji 2.0.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**DANE WYJŚCIOWE**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Importuj biblioteki
Zaimportuj wymagane biblioteki z następującym kodem:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Ustawienie wstępne kontekstu aparatu Spark i poleceń magicznych PySpark
Jądra PySpark, które są dostarczane z notesów Jupyter mają wstępnie kontekstu. Dlatego nie należy ustawić Spark lub tworzenia gałęzi w kontekstach jawnie, przed rozpoczęciem pracy z aplikacją. Tych kontekstach są domyślnie dostępne. Tych kontekstach są następujące:

* SC - dla platformy Spark 
* sqlContext - programu Hive

Jądra PySpark zawiera kilka wstępnie zdefiniowanych "poleceń magicznych", które są specjalne polecenia, które można wywoływać za pomocą %%. Istnieją dwa polecenia, które są używane w tych przykładach kodu.

* **%% lokalnego** Określa, że kod w kolejnych wierszy jest wykonywana lokalnie. Kod musi być prawidłowy kod języka Python.
* **%% sql -o \<nazwa zmiennej >** wykonuje zapytanie programu Hive względem sqlContext. Jeśli parametr -o zostanie przekazana, wynik kwerendy są utrwalane w %% kontekstu Python lokalnego jako Pandas DataFrame.

Aby więcej informacji na temat jądra notesów programu Jupyter i wstępnie zdefiniowanego "magics", zapewniają one, zobacz [jądra, które są dostępne dla notesów programu Jupyter przy użyciu platformy Spark HDInsight w systemie Linux klastrów HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Pozyskiwanie danych z publicznego obiektu blob:
Pierwszym krokiem w procesie nauki o danych jest pozyskiwanie danych do analizy ze źródeł, w którym znajduje się on do eksploracji danych i środowisko modelowania. To środowisko jest platforma Spark w tym przewodniku. Ta sekcja zawiera kod, aby wykonać szereg zadań:

* próbka danych, aby modelować pozyskiwania
* Przeczytaj w zestawie danych wejściowych (przechowywany jako plik tsv)
* Formatowanie i czyszczenie danych
* Tworzenie i buforować obiekty (danych lub ramki danych) w pamięci
* Zarejestruj go jako tabeli tymczasowej w kontekście SQL.

Poniżej przedstawiono kod w celu pozyskiwania danych.

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


**DANE WYJŚCIOWE**

Czas wykonywania powyżej komórki: 276.62 sekund

## <a name="data-exploration--visualization"></a>Eksplorowanie danych i wizualizacja
Po wprowadzeniu danych do platformy Spark jest następnym krokiem w procesie nauki o danych, aby lepiej zrozumieć dane za pośrednictwem eksploracji i wizualizacji. W tej sekcji możemy przeanalizować dane taksówek za pomocą zapytań SQL i wykreślania docelowych zmiennych i potencjalnego funkcje kontroli. W szczególności firma Microsoft przedstawianie częstotliwość liczby pasażerów w podróży taksówek, częstotliwość Porada kwoty i jak porady różnią się zależnie od kwotę płatności i typu.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Histogram częstotliwości liczby pasażerów w próbce rund taksówek wykreślania
Ten kod i kolejne fragmenty umożliwia magicznym wyrażeniem SQL zapytania próbki i magic lokalnych danych.

* **Magicznym wyrażeniem SQL (`%%sql`)** jądra HDInsight PySpark obsługuje zapytania HiveQL łatwe wbudowane względem sqlContext. (-O nazwa_zmiennej) argument będzie się powtarzał wyniki kwerendy SQL jako ramkę danych Pandas na serwerze programu Jupyter. Oznacza to, że jest on dostępny w trybie lokalnym.
*  **`%%local` Magic** służy do uruchamiania kodu lokalnie na serwerze programu Jupyter, który jest węzłem głównym klastra HDInsight. Zazwyczaj można użyć `%%local` magic po `%%sql -o` magic jest używany do uruchamiania kwerendy. Parametr -o będzie utrwalanie danych wyjściowych zapytania SQL lokalnie. A następnie `%%local` magic wyzwala kolejny zbiór fragmentów kodu w celu uruchomienia lokalnie dane wyjściowe zapytań SQL, który lokalnie trwały. Dane wyjściowe są automatycznie wizualizowane po uruchomieniu kodu.

To zapytanie pobiera rund według liczby pasażerów. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Ten kod tworzy lokalnej ramce danych na podstawie wyników zapytania i drukuje dane. `%%local` Magic tworzy lokalnej ramce danych, `sqlResults`, który może służyć do kreślenia z matplotlib. 

> [!NOTE]
> Ta magic PySpark jest używana wiele razy, w tym przewodniku. W przypadku dużych ilości danych, powinny być przykładowe Utwórz ramkę danych który można umieścić w pamięci lokalnej.
> 
> 

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Oto kod do wykreślenia w podróży, liczby pasażerów

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

**DANE WYJŚCIOWE**

![Częstotliwość rund według liczby pasażerów](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Możesz wybrać spośród kilku różnych typów wizualizacji (tabeli, kołowego, linii, obszaru lub pasku) przy użyciu **typu** przycisków menu w notesie. Wykres słupkowy jest tu ukazywany.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Przedstawianie histogram Porada kwoty i jak ilość Porada zależy od kwoty turystycznej i liczba pasażerów.
Korzystać z zapytania SQL do przykładowych danych...

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


Tej komórki kodu używa zapytania SQL do tworzenia trzech powierzchni danych.

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


**DANE WYJŚCIOWE:** 

![Porada dystrybucji kwota](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Porada kwota według liczby pasażerów](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Porada kwotę w klasie kwota](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Są wyposażone w inżynierii, przekształcania i danych przygotowania do modelowania
W tej sekcji opisano i zawiera kod dla procedur w celu przygotowywania danych do użycia w modelowaniu uczenia Maszynowego. Widoczny jest sposób wykonywania następujących zadań:

* Utwórz nową funkcję, dzieląc godzin do pojemników ruchu w czasie
* Indeksowanie i na gorąco kodowanie funkcji podzielonych na kategorie
* Tworzenie obiektów etykietą punktu wejścia do funkcji uczenia Maszynowego
* Tworzenie losowego podrzędnych pobierania próbek danych i podziel go na szkolenie i testowanie zestawów
* Skalowanie funkcji
* Obiekty w pamięci podręcznej w pamięci

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Utwórz nową funkcję, dzieląc razy ruchu do pojemników
Ten kod pokazuje, jak utworzyć nową funkcję, dzieląc razy ruchu do pojemników, a następnie jak wynikowe ramki danych w pamięci w pamięci podręcznej. Buforowanie w wyniku krótszy czas wykonywania gdzie odpornych rozproszonych zestawów danych (danych) i ramki danych są używane wielokrotnie. Tak możemy pamięci podręcznej zestawów danych oraz ramki danych na wiele etapów w ramach tego przewodnika.

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

**DANE WYJŚCIOWE**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Indeksowanie i hot jeden kodowanie funkcji podzielonych na kategorie
W tej sekcji przedstawiono sposób indeksu lub zakodować kategorii funkcji dla danych wejściowych do funkcji modelowania. Modelowania i przewidywanie funkcje MLlib wymaga, aby funkcji, korzystając z kategorii danych wejściowych być indeksowane zakodowane przed użyciem. 

W zależności od modelu musisz indeksu lub Zakoduj je na różne sposoby. Na przykład Logistic i regresji liniowej modele wymagają hot jeden kodowania, gdy, na przykład funkcji z trzech kategorii można rozszerzyć do trzech kolumnach funkcji, z każdego zawierających 0 lub 1 w zależności od kategorii wystąpienia wartości. Udostępnia MLlib [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkcji hot jeden kodowania. Ten koder mapuje kolumny indeksów etykiety z kolumną wektorów binarnych z co najwyżej jeden — wartość typu single. To kodowanie umożliwia algorytmy, które oczekują liczbowe ważnych funkcji, takich jak regresji logistycznej, mają być stosowane do kategorii funkcje.

Poniżej przedstawiono kod, aby zaindeksować i kodowanie funkcji podzielonych na kategorie:

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


**DANE WYJŚCIOWE**

Czas wykonywania powyżej komórki: 3.14 sekund

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Tworzenie obiektów etykietą punktu wejścia do funkcji uczenia Maszynowego
Ta sekcja zawiera kod, który pokazuje, jak indeksowanie danych podzielonych na kategorie tekstu jako typ etykietą punktu danych oraz jak do zakodowania. Wykonanie tych kroków przygotowuje go do użycia w celu nauczenia i przetestowania regresji logistycznej MLlib i innych modeli klasyfikacji. Obiekty oznaczone punktu są odporne rozproszone zestawy danych (RDD) sformatowany w sposób, które są potrzebne najbardziej algorytmów uczenia Maszynowego w MLlib jako dane wejściowe. A [etykietą punktu](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) lokalnego wektor gęstą lub rozrzedzony, skojarzony z etykiety/odpowiedzi.

Poniżej przedstawiono kod, aby zaindeksować i kodowanie tekstu funkcji Klasyfikacja binarna.

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
Ten kod tworzy losowego próbkowanie danych (25% służy w tym miejscu). Chociaż nie jest wymagane w tym przykładzie ze względu na rozmiar zestawu danych, pokażemy, jak można przykładowe dane, w tym miejscu. Następnie wiesz, jak używać własnego problemu, w razie potrzeby. W przypadku dużych przykłady to można zapisać znaczną ilość czasu podczas szkolenia modeli. Następnie możemy podzielić próbki część szkolenia (w tym miejscu 75%) i testowania część (25% tutaj) do użycia w funkcji klasyfikacji i regresji modelowania.

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

**DANE WYJŚCIOWE**

Czas wykonywania powyżej komórki: 0.31 sekund

### <a name="feature-scaling"></a>Skalowanie funkcji
Funkcja skalowania, nazywana również normalizacji danych ubezpieczycielom, że funkcje o wartościach powszechnie rozchodów są nie udzieliła nadmierne porównać w celu funkcji. Kod funkcji skalowania używa [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) te funkcje, aby odchylenie jednostki skalowania. Jest ona udostępniana przed MLlib do użycia w regresji liniowej z stochastycznego gradientu zejścia (SGD). SGD to popularne algorytm szkoleniowe szerokiej gamy innych modeli, takie jak umorzyć regresji lub pomocy technicznej wektor maszyny (SVM) uczenia maszynowego.   

> [!TIP]
> Wykryto algorytm LinearRegressionWithSGD być wrażliwa funkcji skalowania.   
> 
> 

Poniżej przedstawiono kod w celu skalowania zmienne do użytku z programem regularized liniowego algorytmu SGD.

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

**DANE WYJŚCIOWE**

Czas wykonywania powyżej komórki: 11.67 sekund

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

**DANE WYJŚCIOWE** 

Czas wykonywania powyżej komórki: 0,13 sekund

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Przewidywania, czy porady zostało opłacone przy użyciu modeli klasyfikacji binarnej
W tej sekcji przedstawiono sposób użycia trzech modeli w przypadku zadanie klasyfikacji binarnej Prognozowanie czy Porada zostało opłacone podróży taksówek. Modele prezentowane są następujące:

* Regresja logistyczna 
* Losowe lasu
* Gradient zwiększenie drzew

Każdy model tworzenia sekcji kodu zostanie podzielona na kroki: 

1. **Szkolenie modelu** danych za pomocą jeden zestaw parametrów
2. **Model oceny** na testowego zestawu danych za pomocą metryk
3. **Zapisywanie modelu** w obiekcie blob do przyszłego użytku

Pokazujemy, jak to zrobić krzyżowego sprawdzania poprawności (CV) z parametrem sprawdzaniu na dwa sposoby:

1. Za pomocą **ogólny** kodu niestandardowego, które mogą być stosowane do dowolnego algorytmu w MLlib i każdego parametru ustawia w algorytmie. 
2. Za pomocą **pySpark CrossValidator potoku funkcji**. Należy zwrócić uwagę na to, że CrossValidator ma kilka ograniczeń dla platformy Spark 1.5.0: 
   
   * Modele potok nie może być zapisany/zachowywane do użytku w przyszłości.
   * Nie można używać dla każdego parametru w modelu.
   * Nie można używać dla każdego algorytmu MLlib.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Ogólny krzyżowego sprawdzania poprawności i zaczynają hiperparametrycznego korzystania z algorytmu regresji logistycznej dla klasyfikacji binarnej
Kod w tej sekcji pokazano, jak uczenie, ocenę i Zapisz model regresji logistycznej przy użyciu [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) który prognozuje czy Porada czy płatna komunikacji dwustronnej w NYC taksówek podróży i klasie zestawu danych. Model jest uczony przy użyciu krzyżowego sprawdzania poprawności (CV) i zaczynają hiperparametrycznego implementowane za pomocą kodu niestandardowego, który można zastosować do dowolnego z algorytmów uczenia w MLlib.   

> [!NOTE]
> Wykonanie tego niestandardowego kodu CV może potrwać kilka minut.
> 
> 

**Uczenia modelu regresji logistycznej przy użyciu stałych Nietrwałych i zaczynają hiperparametrycznego**

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


**DANE WYJŚCIOWE**

Coefficients: [0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Intercept:-0.0111216486893

Czas wykonywania powyżej komórki: 14.43 sekund

**Model klasyfikacji binarnej przy użyciu standardowych metryk oceny**

Kod w tej sekcji pokazano, jak do oceny względem test-zestawu danych, w tym wykres WIELOKLASOWA krzywa model regresji logistycznej.

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


**DANE WYJŚCIOWE**

Obszar, w ramach żądania Ściągnięcia = 0.985336538462

Obszar pod ROC = 0.983383274312

Podsumowanie statystyk

Precyzja = 0.984174341679

Odwołaj = 0.984174341679

F1 Ocena = 0.984174341679

Czas wykonywania powyżej komórki: 2.67 sekund

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


**DANE WYJŚCIOWE**

![Regresja logistyczna krzywej ROC, ogólne podejście](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Utrwalanie modelu w obiekcie blob do przyszłego użytku**

Kod w tej sekcji pokazano, jak można zapisać modelu regresji logistycznej do użycia.

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


**DANE WYJŚCIOWE**

Czas wykonywania powyżej komórki: 34.57 sekund

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Funkcja firmy MLlib CrossValidator potoku z modelu regresji logistycznej (Regresja elastyczne)
Kod w tej sekcji pokazano, jak uczenie, ocenę i Zapisz model regresji logistycznej przy użyciu [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) który prognozuje czy Porada czy płatna komunikacji dwustronnej w NYC taksówek podróży i klasie zestawu danych. Model jest uczony przy użyciu krzyżowego sprawdzania poprawności (CV) i zaczynają hiperparametrycznego implementowane za pomocą funkcji potoku MLlib CrossValidator dla CV, za pomocą parametrów.   

> [!NOTE]
> Wykonanie tego kodu MLlib CV może potrwać kilka minut.
> 
> 

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

**DANE WYJŚCIOWE**

Czas wykonywania powyżej komórki: 107.98 sekund

**Przedstawianie WIELOKLASOWA krzywa.**

*PredictionAndLabelsDF* jest zarejestrowany jako tabelę *tmp_results*, w poprzedniej komórki. *tmp_results* może służyć zapytania i dane wyjściowe wyniki do ramki danych sqlResults do kreślenia. Oto kod.

    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Poniżej przedstawiono kod, do wykreślenia krzywej ROC.

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


**DANE WYJŚCIOWE**

![Krzywej ROC regresji logistycznej przy użyciu MLlib firmy CrossValidator](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Klasyfikacja losowe lasu
Kod w tej sekcji przedstawiono sposób uczenie, ocenę i Zapisz regresji losowe lasu, który prognozuje czy Porada czy płatna komunikacji dwustronnej w NYC taksówek podróży i klasie zestawu danych.

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


**DANE WYJŚCIOWE**

Obszar pod ROC = 0.985336538462

Czas wykonywania powyżej komórki: 26.72 sekund

### <a name="gradient-boosting-trees-classification"></a>Gradient zwiększenie klasyfikacji drzew
Kod w tej sekcji przedstawiono sposób uczenie, ocenę i Zapisz gradientu zwiększenie drzewa modelu do prognozowania czy Porada czy płatna komunikacji dwustronnej w podróży taksówek NYC i taryfy zestawu danych.

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

**DANE WYJŚCIOWE**

Obszar pod ROC = 0.985336538462

Czas wykonywania powyżej komórki: 28.13 sekund

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Prognozowanie kwota Porada za pomocą modele regresji (bez użycia CV)
W tej sekcji przedstawiono sposób użycia, trzy modele zadania regresji: przewidywanie Porada uiszczone komunikacji dwustronnej taksówek, w oparciu o inne funkcje porada. Modele prezentowane są następujące:

* Umorzyć regresji liniowej
* Losowe lasu
* Gradient zwiększenie drzew

Modele te zostały opisane we wstępie. Każdy model tworzenia sekcji kodu zostanie podzielona na kroki: 

1. **Szkolenie modelu** danych za pomocą jeden zestaw parametrów
2. **Model oceny** na testowego zestawu danych za pomocą metryk
3. **Zapisywanie modelu** w obiekcie blob do przyszłego użytku   

> UWAGA PLATFORMY AZURE: Krzyżowa Weryfikacja nie jest używana z modelami trzy regresji, w tej sekcji, ponieważ zostało to pokazane szczegółowo dla modele regresji logistycznej. Przykład przedstawiający sposób użycia stałych Nietrwałych elastycznej NET regresji liniowej znajduje się w dodatku w tym temacie.
> 
> UWAGA PLATFORMY AZURE: W naszych doświadczeń wynika mogą występować problemy ze zbieżności LinearRegressionWithSGD modeli i parametry muszą być zmienione/zoptymalizowaną dokładnie w celu uzyskania prawidłowego modelu. Skalowanie zmiennych znacznie ułatwić realizację zbieżności. Zamiast LinearRegressionWithSGD można także elastycznej netto regresji, przedstawiony w dodatku do tego tematu.
> 
> 

### <a name="linear-regression-with-sgd"></a>Regresja liniowa z SGD
W kodzie w tej sekcji pokazano, jak używać funkcji skalowane do nauczenia regresji liniowej, który używa stochastycznego spadku gradientu (wstecznej Propagacji) na potrzeby optymalizacji i jak ocena, oceny i zapisywanie modelu w usłudze Azure Blob Storage (WASB).

> [!TIP]
> W naszych doświadczeń wynika mogą występować problemy ze zbieżności LinearRegressionWithSGD modeli i parametry muszą być zmienione/zoptymalizowaną dokładnie w celu uzyskania prawidłowego modelu. Skalowanie zmiennych znacznie ułatwić realizację zbieżności.
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

**DANE WYJŚCIOWE**

Coefficients: [0.0141707753435, -0.0252930927087, -0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092, -0.00456498588241, -0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632, -0.00289545676449, -0.00791124681938, 0.54396316518, -0.536293513569, 0.0119076553369, -0.0173039244582, 0.0119632796147, 0.00146764882502]

Przechwytywanie: 0.854507624459

RMSE = 1.23485131376

R-sqr = 0.597963951127

Czas wykonywania powyżej komórki: 38.62 sekund

### <a name="random-forest-regression"></a>Regresja Random lasu
Kod w tej sekcji przedstawiono sposób uczenie, ocenę i Zapisz model lasu losowego, który prognozuje kwotę wskazówka dla danych podróży taksówek NYC.   

> [!NOTE]
> Krzyżowa Weryfikacja za pomocą parametru sprawdzaniu za pomocą kodu niestandardowego znajduje się w dodatku.
> 
> 

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

**DANE WYJŚCIOWE**

RMSE = 0.931981967875

R sqr = 0.733445485802

Czas wykonywania powyżej komórki: 25.98 sekund

### <a name="gradient-boosting-trees-regression"></a>Zwiększenie regresji drzew gradientu
Kod w tej sekcji przedstawiono sposób uczenie, ocenę i Zapisz gradientu zwiększenie drzewa modelu do prognozowania kwotę wskazówka dla danych podróży NYC taksówek.

**Nauczanie i ocena**

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


**DANE WYJŚCIOWE**

RMSE = 0.928172197114

R-sqr = 0.732680354389

Czas wykonywania powyżej komórki: 20.9 sekund

**Wykres**

*tmp_results* jest zarejestrowany jako tabeli programu Hive w poprzedniej komórki. Wyniki z tabeli są danymi wyjściowymi przekazywanymi do *sqlResults* ramki danych do kreślenia. W tym miejscu znajduje się kod

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Poniżej przedstawiono kod, do wykreślenia danych za pomocą serwera programu Jupyter.

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

![Vs przewidzieć — Porada kwoty rzeczywiste](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Dodatek: Zadania dodatkowe regresji przy użyciu parametrów symulacji krzyżowego sprawdzania poprawności
Ten dodatek zawiera kod, przedstawiający, jak to zrobić przy użyciu elastycznych net regresji liniowej stałych Nietrwałych i jak to zrobić CV za pomocą czyszczenia parametrów przy użyciu niestandardowego kodu dla lasu losowe regresji.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Krzyżowe sprawdzanie poprawności przy użyciu elastycznej netto dla regresji liniowej
W kodzie w tej sekcji pokazano, jak krzyżowe sprawdzanie poprawności przy użyciu elastycznych net regresji liniowej i ocenianie modelu dla danych testowych.

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


**DANE WYJŚCIOWE**

Czas wykonywania powyżej komórki: 161.21 sekund

**R SQR metryki oceny**

*tmp_results* jest zarejestrowany jako tabeli programu Hive w poprzedniej komórki. Wyniki z tabeli są danymi wyjściowymi przekazywanymi do *sqlResults* ramki danych do kreślenia. W tym miejscu znajduje się kod

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Poniżej przedstawiono kod, aby obliczyć R sqr.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**DANE WYJŚCIOWE**

R sqr = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Krzyżowe sprawdzanie poprawności za pomocą czyszczenia parametrów przy użyciu niestandardowego kodu dla lasu losowe regresji
Kod w tej sekcji przedstawiono sposób krzyżowe sprawdzanie poprawności przy użyciu parametrów dla regresji losowe lasu za pomocą kodu niestandardowego i ocena modelu dla danych testowych.

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


**DANE WYJŚCIOWE**

RMSE = 0.906972198262

R sqr = 0.740751197012

Czas wykonywania powyżej komórki: 69.17 sekund

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Czyszczenie obiektów z pamięci i lokalizacje modelu drukowania
Użyj `unpersist()` można usunąć obiektów w pamięci podręcznej.

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


**DANE WYJŚCIOWE**

PythonRDD [122] w RDD na PythonRDD.scala: 43

** Ścieżka wydruku plików modelu ma być używany w notesie zużycia. ** W celu umożliwienia użycia i ocenianie niezależnie od zestawu danych, musisz skopiować i wkleić te nazwy plików w notesie"zużycie".

    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**DANE WYJŚCIOWE**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>Co dalej?
Teraz, modele regresji i klasyfikacji została utworzona przy użyciu MlLib platformy Spark, jesteś gotowy dowiedzieć się, jak ocena i ocenić te modele.

**Użycie modelu:** Aby dowiedzieć się, jak ocena i oceniać modele klasyfikacji i regresji, utworzone w tym temacie, zobacz [wynik i ocena modeli uczenia maszynowego utworzonych na platformie Spark](spark-model-consumption.md).

