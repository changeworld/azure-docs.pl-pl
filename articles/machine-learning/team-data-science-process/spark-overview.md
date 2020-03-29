---
title: Data science przy użyciu platformy Spark on Azure HDInsight — proces nauki o danych zespołowych
description: Zestaw narzędzi Spark MLlib zapewnia znaczne możliwości modelowania uczenia maszynowego w rozproszonym środowisku HDInsight.
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
ms.openlocfilehash: 63148b99e65a5ccc49d54d4ae6c58adebc72c6d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718518"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Omówienie nauki o danych przy użyciu platformy Spark w usłudze Azure HDInsight

Ten zestaw tematów pokazuje, jak używać programu HDInsight Spark do wykonywania typowych zadań do nauki o danych, takich jak pozyskiwania danych, inżynierii funkcji, modelowania i oceny modelu. Wykorzystane dane są próbką zestawu danych o taksówce i taryfie 2013 NYC. Zbudowane modele obejmują regresję logistyczną i liniową, losowe lasy i drzewa pobudzone gradientem. Tematy pokazują również, jak przechowywać te modele w magazynie obiektów blob platformy Azure (WASB) i jak oceniać i oceniać ich wydajność predykcyjną. Bardziej zaawansowane tematy obejmują sposób, w jaki modele mogą być szkolone przy użyciu krzyżowej weryfikacji i hiperparametrycznego zamiatania. W tym temacie przeglądu odwołuje się również do tematów, które opisują sposób konfigurowania klastra platformy Spark, który jest potrzebny do wykonania kroków w przewodnikach.

## <a name="spark-and-mllib"></a>Iskra i MLlib
[Spark](https://spark.apache.org/) to struktura przetwarzania równoległego typu open source, która obsługuje przetwarzanie w pamięci, aby zwiększyć wydajność aplikacji analitycznych dużych zbiorów danych. Silnik przetwarzania Spark został stworzony z myślą o szybkości, łatwości obsługi i zaawansowanej analizie. Rozproszone możliwości obliczeniowe firmy Spark w pamięci sprawiają, że jest to dobry wybór dla algorytmów iteracyjnych używanych w uczeniu maszynowym i obliczeniach wykresów. [MLlib](https://spark.apache.org/mllib/) to skalowalna biblioteka uczenia maszynowego platformy Spark, która zapewnia możliwości modelowania algorytmicznego w tym rozproszonym środowisku.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HdInsight Spark](../../hdinsight/spark/apache-spark-overview.md) to hostowana na platformie Azure oferta platformy Spark typu open source. Obejmuje również obsługę **notesów Jupyter PySpark** w klastrze Platformy Spark, które mogą uruchamiać interaktywne zapytania programu Spark SQL do przekształcania, filtrowania i wizualizacji danych przechowywanych w obiektach blob platformy Azure (WASB). PySpark jest interfejsem API języka Python dla platformy Spark. Fragmenty kodu, które zapewniają rozwiązania i pokazują odpowiednie wykresy do wizualizacji danych w tym miejscu uruchomić w notesach Jupyter zainstalowanych w klastrach platformy Spark. Kroki modelowania w tych tematach zawierają kod, który pokazuje, jak trenować, oceniać, zapisywać i używać każdego typu modelu.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Konfiguracja: klastry platformy Spark i notesy Jupyter
Kroki konfiguracji i kod są podane w tym instruktażu do korzystania z programu HDInsight Spark 1.6. Ale notebooki Jupyter są dostępne zarówno dla klastrów HDInsight Spark 1.6, jak i Spark 2.0. Opis notesów i łącza do nich znajdują się w [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierającego je. Ponadto kod w tym miejscu i w połączonych notesach jest ogólny i powinien działać w dowolnym klastrze platformy Spark. Jeśli nie używasz programu HDInsight Spark, kroki konfiguracji klastra i zarządzania klastra mogą się nieznacznie różnić od czynności przedstawionych w tym miejscu. Dla wygody, oto linki do notebooków Jupyter dla Spark 1.6 (do uruchomienia w jądrze pySpark serwera notebooków Jupyter) i Spark 2.0 (do uruchomienia w jądrze pySpark3 serwera notebooka Jupyter):

### <a name="spark-16-notebooks"></a>Notesy Spark 1.6
Te notesy mają być uruchamiane w jądrze pySpark serwera notebooków Jupyter.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Zawiera informacje na temat sposobu eksploracji danych, modelowania i oceniania za pomocą kilku różnych algorytmów.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Zawiera tematy w #1 notebooków i rozwoju modelu przy użyciu dostrajania hiperparametrycznego i krzyżowej weryfikacji.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb)Pokazuje, jak operacjonalizacji zapisanego modelu przy użyciu języka Python w klastrach HDInsight.

### <a name="spark-20-notebooks"></a>Notesy Spark 2.0
Te notebooki mają być uruchamiane w jądrze pySpark3 serwera notebooków Jupyter.

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
> Notesy Spark 2.0 na zestawach danych o opóźnieniach lotów w Nowym Jorku i opóźnieniu lotu linii lotniczych mogą trwać co najmniej 10 minut (w zależności od rozmiaru klastra HDI). Pierwszy notes na powyższej liście pokazuje wiele aspektów eksploracji danych, wizualizacji i szkolenia modelu ML w notebooku, który zajmuje mniej czasu, aby uruchomić z pobranym próbką zestawu danych NYC, w którym pliki taksówek i taryf zostały wstępnie połączone: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Ten notes zajmuje znacznie krótszy czas( 2-3 min) i może być dobrym punktem wyjścia do szybkiego zbadania kodu, który udostępniliśmy dla platformy Spark 2.0.

<!-- -->

Aby uzyskać wskazówki dotyczące operacjonalizacji modelu platformy Spark 2.0 i zużycia modelu do oceniania, zobacz [dokument platformy Spark 1.6 dotyczący zużycia,](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) na przykład przedstawiający wymagane kroki. Aby użyć tego przykładu na Spark 2.0, zastąp plik kodu Języka Python [tym plikiem](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Wymagania wstępne

Poniższe procedury są związane z spark 1.6. W przypadku wersji Spark 2.0 użyj notesów opisanych i połączonych z wcześniej.

1. Wymagana jest subskrypcja platformy Azure. Jeśli jeszcze go nie masz, zobacz [Pobierz bezpłatną wersję próbną platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. Aby ukończyć ten instruktajalnia, potrzebny jest klaster platformy Spark 1.6. Aby go utworzyć, zobacz instrukcje podane w [obszarze Wprowadzenie: utwórz program Apache Spark w usłudze Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Typ i wersja klastra są określone w menu **Wybierz typ klastra.**

![Konfigurowanie klastra](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> W temacie, który pokazuje, jak używać Scala zamiast Python do wykonywania zadań dla kompleksowego procesu nauki o danych, zobacz [nauki o danych przy użyciu Scala z iskrą na platformie Azure.](scala-walkthrough.md)
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>Dane dotyczące taksówek w Nowym Jorku 2013
Dane NYC Taxi Trip to około 20 GB skompresowanych plików wartości oddzielonych przecinkami (CSV) (~48 GB nieskompresowanych), obejmujących ponad 173 miliony pojedynczych podróży i opłaty płacone za każdą podróż. Każdy rekord podróży zawiera miejsce odbioru i nadsyłania i czas, anonimowy hack (kierowca) numer licencji i medalion (taxi's unique id) numer. Dane obejmują wszystkie podróże w 2013 r. i są dostępne w następujących dwóch zestawach danych dla każdego miesiąca:

1. Pliki CSV "trip_data" zawierają szczegóły podróży, takie jak liczba pasażerów, punkty odbioru i wysiadania, czas trwania podróży i długość podróży. Oto kilka przykładowych rekordów:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Pliki CSV "trip_fare" zawierają szczegółowe informacje o taryfie zapłaconej za każdą podróż, takie jak rodzaj płatności, kwota taryfy, dopłaty i podatki, napiwki i opłaty drogowe oraz całkowita zapłacona kwota. Oto kilka przykładowych rekordów:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Pobraliśmy próbkę 0,1% tych plików i\_połączyliśmy\_dane podróży i usługi podróży plików CVS do jednego zestawu danych do wykorzystania jako wejściowego zestawu danych dla tego przewodnika. Unikalny klucz do\_przyłączenia\_się do danych podróży i taryfy podróży\_składa\_się z pól: medalion, licencja hack i data odbioru. Każdy rekord zestawu danych zawiera następujące atrybuty reprezentujące podróż taksówką NYC:

| Pole | Krótki opis |
| --- | --- |
| Medalion |Zanonimizowane medalion taxi (unikalny identyfikator taksówki) |
| hack_license |Anonimowy numer licencji na przewóz hackney |
| vendor_id |Identyfikator dostawcy taksówki |
| rate_code |Nyc taxi rate of fare |
| store_and_fwd_flag |Przechowywanie i przesyłanie dalej flagi |
| pickup_datetime |Data odbioru & czas |
| dropoff_datetime |Data nadsyłania & czas |
| pickup_hour |Godzina odbioru |
| pickup_week |Odbiór tygodnia roku |
| Rozkład |Dzień tygodnia (zakres 1-7) |
| passenger_count |Liczba pasażerów w podróży taksówką |
| trip_time_in_secs |Czas podróży w sekundach |
| trip_distance |Odległość podróży przebytą milami |
| pickup_longitude |Długość geograficzna |
| pickup_latitude |Podnieś szerokość geograficzną |
| dropoff_longitude |Długość geograficzna dropoff |
| dropoff_latitude |Szerokość szerokości nadsyłania |
| direct_distance |Bezpośrednia odległość między miejscami odbioru i wysiadania |
| payment_type |Rodzaj płatności (gotówka, karta kredytowa itp.) |
| fare_amount |Kwota taryfy w |
| Dopłaty |Dopłaty |
| mta_tax |Podatek transportowy MTA Metro |
| tip_amount |Kwota napiwku |
| tolls_amount |Wysokość opłat drogowych |
| total_amount |Suma |
| Wyrzucenia |Końcówka (0/1 dla nie lub tak) |
| tip_class |Klasa porad (0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Wykonywanie kodu z notesu Jupyter w klastrze Spark
Notes Jupyter można uruchomić z witryny Azure portal. Znajdź klaster platformy Spark na pulpicie nawigacyjnym i kliknij ją, aby wejść na stronę zarządzania klastra. Aby otworzyć notes skojarzony z klastrem Spark, kliknij pozycję **Pulpit nawigacyjny klastra** -> **Notes jupyter .**

![Pulpity nawigacyjne klastra](./media/spark-overview/spark-jupyter-on-portal.png)

Możesz również przejść ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** do notesów Jupyter. Zastąp część CLUSTERNAME tego adresu URL nazwą własnego klastra. Aby uzyskać dostęp do notesów, potrzebujesz hasła do konta administratora.

![Przeglądaj notebooki Jupyter](./media/spark-overview/spark-jupyter-notebook.png)

Wybierz PySpark, aby wyświetlić katalog zawierający kilka przykładów wstępnie spakowanych notesów korzystających z interfejsu API PySpark. Notesy zawierające przykłady kodu dla tego pakietu tematu Spark są dostępne w [usłudze GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Notesy można przekazać bezpośrednio z [usługi GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) do serwera notebooków Jupyter w klastrze platformy Spark. Na stronie głównej urządzenia Jupyter kliknij przycisk **Prześlij** po prawej stronie ekranu. Otwiera eksploratora plików. W tym miejscu można wkleić adres URL usługi GitHub (zawartość nieprzetworzona) notesu i kliknąć przycisk **Otwórz**.

Na liście plików Jupyter ponownie zobaczysz nazwę pliku z przyciskiem **Przekaż.** Kliknij ten **przycisk Przesyłania.** Teraz zaimportowałeś notes. Powtórz te kroki, aby przekazać inne notesy z tego przewodnika.

> [!TIP]
> Możesz kliknąć prawym przyciskiem myszy łącza w przeglądarce i wybrać **pozycję Kopiuj łącze,** aby uzyskać adres URL zawartości pierwotnej GitHub. Ten adres URL można wkleić do okna dialogowego Eksploratora plików Przekazywania jupytera.
> 
> 

Co możesz teraz zrobić:

* Zobacz kod, klikając notes.
* Wykonaj każdą komórkę, naciskając **klawisz SHIFT-ENTER**.
* Uruchom cały notes, klikając **pozycję Cell** -> **Run**.
* Użyj automatycznej wizualizacji zapytań.

> [!TIP]
> Jądro PySpark automatycznie wizualizuje dane wyjściowe zapytań SQL (HiveQL). Za pomocą przycisków menu **Tekst** w notesie można wybrać jedną z kilku różnych typów wizualizacji (Tabela, Ciasto, Linia, Obszar lub Pasek):
>
>

![Krzywa regresji logistycznej ROC dla podejścia ogólnego](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Co dalej?
Teraz, gdy są skonfigurowane z klastrem HDInsight Spark i przesłane notesy Jupyter, jesteś gotowy do pracy przez tematy, które odpowiadają trzech notesów PySpark. Pokazują one, jak eksplorować dane, a następnie jak tworzyć i używać modeli. Zaawansowany notes eksploracji i modelowania danych pokazuje, jak uwzględnić krzyżowe sprawdzanie poprawności, tworzenie hiperparametrów i ocenę modelu.

**Eksploracja i modelowanie danych za pomocą platformy Spark:** Eksploruj zestaw danych i tworzenie, ocena i oceny modeli uczenia maszynowego, pracując za pośrednictwem [Tworzenie klasyfikacji binarnej i modeli regresji dla danych z Spark MLlib toolkit tematu.](spark-data-exploration-modeling.md)

**Zużycie modelu:** Aby dowiedzieć się, jak ocenić modele klasyfikacji i regresji utworzone w tym temacie, zobacz [Ocenianie i ocenianie modeli uczenia maszynowego utworzonych przez platformę Spark.](spark-model-consumption.md)

**Krzyżowe walidacji i hyperparameter zamiatanie**: Zobacz [zaawansowane eksploracji danych i modelowania z Spark,](spark-advanced-data-exploration-modeling.md) w jaki sposób modele mogą być trenowane przy użyciu krzyżowej walidacji i hyper-parametr sweeping

