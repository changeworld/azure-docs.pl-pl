---
title: Analiza danych przy użyciu platformy Spark w usłudze Azure HDInsight — zespołu danych dla celów naukowych
description: Zestaw narzędzi Biblioteka MLlib platformy Spark oferuje znaczne usługi machine learning modelowanie się środowisku rozproszonym HDInsight.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 932587afcffcb3b1a259a02a98c648e938e99931
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60256405"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Omówienie używania platformy Spark w usłudze Azure HDInsight do analizy danych

Tym zestawie tematów pokazuje, jak używać platformy HDInsight Spark do wykonania typowych zadań do nauki o danych, takich jak pozyskiwanie danych, technicznego opracowywania funkcji, modelowania i ocenę modelu. Dane używane jest przykładem 2013 NYC taksówek podróży i klasie zestawu danych. Modele utworzone obejmują regresji logistycznej liniowego i liniowa w, losowych lasów i gradientu wzmocnionego drzewa. W tematach opisano również sposób przechowywania tych modeli w usłudze Azure blob storage (WASB) oraz ocena i oceny wydajności predykcyjne. Obejmuje bardziej zaawansowanych tematów, jak modeli może być uczony przy użyciu zaczynają krzyżowego sprawdzania poprawności i parametrów. Ten ogólny temat odwołuje się również do tematów opisujących, jak skonfigurować klaster Spark, które należy wykonać czynności opisane w wskazówki podane.

## <a name="spark-and-mllib"></a>Platforma Spark oraz MLlib
[Platforma Spark](https://spark.apache.org/) to platforma przetwarzania równoległego typu open source, która obsługuje w pamięci przetwarzanie w celu zwiększania wydajności aplikacji do analizy danych big data. Aparat przetwarzania Spark zaprojektowano pod kątem szybkości, łatwości użycia i zaawansowanych możliwości analitycznych. Możliwości obliczeń rozproszonych w pamięci platforma Spark, że dobry wybór w przypadku algorytmów iteracyjnych używanych używane w machine learning i obliczeniach na grafach. [Biblioteka MLlib](https://spark.apache.org/mllib/) to biblioteka uczenia maszynowego skalowalne do platforma Spark, które powoduje konsolidatorze modelowanie to środowisku rozproszonym.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) to Azure hostowanej Oferta typu open source platformy Spark. Obejmuje również obsługę **notesów Jupyter PySpark** w klastrze Spark, która może uruchomić interakcyjnych zapytań Spark SQL do przekształcania, filtrowania i wizualizowanie danych przechowywanych obiektów blob platformy Azure (WASB). PySpark jest interfejsu API języka Python dla platformy Spark. Fragmenty kodu, do zapewniania rozwiązań, które pokazują odpowiednie wykresy w celu wizualizacji danych, w tym miejscu są uruchamiane w notesy Jupyter notebook zainstalowane w klastrach platformy Spark. Kroki modelowania w tych tematach zawierają kod, który pokazuje, jak uczenie, ocenę, Zapisz i zużywać każdy rodzaj modelu.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Konfiguracja: Klastry Spark i notesy Jupyter
Kroki instalacji i kodu w tym przewodniku dla podano przy użyciu HDInsight Spark 1.6. Ale notesów programu Jupyter znajdują się w przypadku klastrów HDInsight Spark 1.6 i platformy Spark w wersji 2.0. Opis notesów i łącza do nich znajdują się w [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierające je. Ponadto kod w tym miejscu w notesach połączonej jest ogólny i powinna działać w dowolnym klastrze Spark. Jeśli nie używasz platformy HDInsight Spark, konfiguracja klastra i czynności administracyjne mogą nieznacznie różnić się od przedstawionego w tym miejscu. Dla wygody Oto łącza do notesów programu Jupyter, platformy Spark 1.6 (do uruchomienia jądra pySpark, serwera aplikacji Jupyter Notebook) i platformy Spark w wersji 2.0 (do uruchomienia w jądrze pySpark3 serwera aplikacji Jupyter Notebook):

### <a name="spark-16-notebooks"></a>Notesy platformy Spark 1.6
Te notesy to będą uruchamiane w jądra pySpark serwer notesu Jupyter.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Zawiera informacje dotyczące sposobu wykonywania eksploracji danych, modelowania i ocenianie z kilku różnych algorytmów.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Zawiera tematy w notesie #1 i opracowywania modelu strojenia hiperparametrycznego i krzyżowego sprawdzania poprawności.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): Pokazuje, jak operacjonalizować model zapisanej przy użyciu języka Python w klastrach HDInsight.

### <a name="spark-20-notebooks"></a>Notesy platformy Spark w wersji 2.0
Te notesy to będą uruchamiane w jądra pySpark3 serwer notesu Jupyter.

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
> Notesy platformy Spark 2.0 na NYC taksówek i linie lotnicze lotu opóźnienie-zestawów danych może potrwać 10 minut lub dłużej (w zależności od rozmiaru klastra usługi HDI). Pierwszy notesu na powyższej liście przedstawiono wiele aspektów eksplorację, wizualizację i szkolenie modelu uczenia Maszynowego w notesie, który zajmuje mniej czasu do uruchomienia z próbkowana w dół NYC zestaw danych, w którym zostały wstępnie dołączonym do plików taksówek i klasie: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Ten notes, trwa znacznie krótszy czas zakończenia (2-3 minuty) i może być przeznaczona punkt początkowy do szybkiego analizowania kodu, który udostępniliśmy dla platformy Spark w wersji 2.0.

<!-- -->

Aby uzyskać wskazówki dotyczące operacjonalizacji modeli Spark 2.0 i użycie modelu do oceniania, zobacz [dokumentu Spark 1.6 zużycia](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) przykład konspekt kroki wymagane. Na temat platformy Spark w wersji 2.0, należy skorzystać z tej, Zastąp plik kodu języka Python za pomocą [ten plik](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Wymagania wstępne

Poniższe procedury odnoszą się do platformy Spark 1.6. Dla wersji platformy Spark w wersji 2.0 korzystanie z notesów opisem i powiązane z wcześniej.

1. Wymagana jest subskrypcja platformy Azure. Jeśli nie masz już jeden, zobacz [uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. Potrzebujesz klastra Spark 1.6 do przeprowadzenia tego instruktażu. Aby go utworzyć, zobacz instrukcje podane w [wprowadzenie: tworzenie platformy Apache Spark w usłudze Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Typ klastra i wersja jest określony z **wybierz typ klastra** menu.

![Konfigurowanie klastra](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Dla tematu, który pokazuje, jak używać języka Scala, a nie języka Python w celu wykonania zadań w procesie nauki o danych end-to-end, zobacz [do nauki o danych z platformą Spark na platformie Azure przy użyciu języka Scala](scala-walkthrough.md).
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>Dane taksówek 2013 NYC
Dane podróży taksówek NYC około 20 GB plików skompresowanych wartości rozdzielanych przecinkami (CSV) (~ 48 GB nieskompresowane), zawierających ponad milion 173 poszczególnych podróży i opłaty opłacony każdego podróży. Każdy rekord podróży obejmuje pobranie się i dropoff lokalizacji i czasu, hack anonimowe (sterownika) numer licencji i numer Medalionu (unikatowy identyfikator dla taksówek). Dane obejmuje wszystkie podróży w roku 2013 i znajduje się w następujących dwóch zestawów danych w każdym miesiącu:

1. Pliki CSV "trip_data" zawierają podróży uzyskać szczegółowe informacje, takie jak liczba osób, podnieś i wskazuje dropoff, przełączył czas trwania i długość podróży. Poniżej przedstawiono kilka przykładowych rekordów:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Pliki CSV "trip_fare" zawierają szczegółowe informacje o opłatę za każdym razem, takich jak typ płatności, kwota taryfy, opłata za opcję i podatków, porady i drogi i łącznej kwoty zapłacone. Poniżej przedstawiono kilka przykładowych rekordów:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Firma Microsoft ma próbkę 0,1% tych plików i połączonych wyzwolenie\_danych i podróży\_taryfy CVS pliki w jednym zestawie danych do użycia jako wejściowego zestawu danych w ramach tego przewodnika. Unikatowy klucz, aby dołączyć podróży\_danych i podróży\_taryfy składa się z pól: Medalionu hakowanie\_licencji oraz pobrania\_daty/godziny. Każdy rekord zestaw danych zawiera następujące atrybuty, które reprezentuje podróż taksówek NYC:

| Pole | Krótki opis |
| --- | --- |
| Medalionu |Anonimowe taksówek Medalionu (taksówek Unikatowy identyfikator) |
| hack_license |Anonimowe numer licencji Hackney powrót karetki |
| vendor_id |Identyfikator dostawcy taksówek |
| rate_code |Współczynnik taksówek NYC turystycznej |
| store_and_fwd_flag |Store i flagi do przodu |
| pickup_datetime |Wybierz datę i godzinę |
| dropoff_datetime |Dropoff daty i godziny |
| pickup_hour |Wybierz godzinę |
| pickup_week |Wybierz tydzień roku |
| Dzień tygodnia |Dzień tygodnia (zakresu 1-7) |
| passenger_count |Liczba osób w podróży taksówek |
| trip_time_in_secs |Czas podróży w ciągu kilku sekund |
| trip_distance |Dystans podróży w milach |
| pickup_longitude |Podnieś długości geograficznej |
| pickup_latitude |Wybierz szerokość geograficzna |
| dropoff_longitude |Dropoff longitude |
| dropoff_latitude |Szerokość geograficzna Dropoff |
| direct_distance |Bezpośrednie odległość między pobrania się i lokalizacje dropoff |
| payment_type |Typ płatności (środki pieniężne, karta kredytowa itp.) |
| fare_amount |Kwota taryfy w |
| Opłata za opcję |Opłata za opcję |
| mta_tax |Podatek MTA |
| tip_amount |Porada kwota |
| tolls_amount |Kwota drogi |
| total_amount |Łączna kwota |
| Przechylony |Przechylony (0/1 dla nie lub tak) |
| tip_class |Porada klasy (0: $0, 1: $0-5, 2: $6 – 10, 3: $11 – 20, 4: > 20 USD) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Wykonanie kodu z notesu Jupyter w klastrze Spark
Można uruchomić notesu Jupyter w witrynie Azure portal. Znajdź klastra Spark na pulpicie nawigacyjnym i kliknij go, aby wprowadzić strony zarządzania dla klastra. Aby otworzyć Notes skojarzone z klastra Spark kliknij pozycję **pulpity nawigacyjne klastra** -> **notesu programu Jupyter** .

![Pulpity nawigacyjne klastra](./media/spark-overview/spark-jupyter-on-portal.png)

Możesz również przejść do ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** na dostęp do notesów programu Jupyter. Zastąp CLUSTERNAME część tego adresu URL, nazwą własnego klastra. Konieczne jest hasło do konta administratora dostęp do notesów.

![Przeglądaj notesów programu Jupyter](./media/spark-overview/spark-jupyter-notebook.png)

Wybierz PySpark, aby wyświetlić katalog, który zawiera kilka przykładów wstępnie spakowanej notesów, które używają interfejsu API PySpark. Notesy, które zawierają przykłady kodu dla tego zestawu tematu platformy Spark są dostępne pod adresem [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Możesz przekazać notesów bezpośrednio z [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) na serwer notesu Jupyter w klastrze Spark. Na stronie głównej Twojej Jupyter, kliknij **przekazywanie** przycisku w prawej części ekranu. Spowoduje to otwarcie Eksploratora plików. W tym miejscu możesz wkleić adres URL usługi GitHub (nieprzetworzonej zawartości) w notesie, a następnie kliknij **Otwórz**.

Nazwa pliku zostanie wyświetlony na liście plików programu Jupyter, z **przekazywanie** ponownie przycisk. Kliknij tutaj, **przekazywanie** przycisku. Teraz zaimportowano notesu. Powtórz te kroki, aby przekazywanie notesów z tego przewodnika.

> [!TIP]
> Możesz kliknąć prawym przyciskiem myszy linki w przeglądarce i wybierz **Kopiuj Link** można pobrać pierwotne URL zawartości usługi GitHub. Ten adres URL można wkleić w okno dialogowe przekazywanie Jupyter Eksploratora plików.
> 
> 

Teraz możesz wykonywać następujące czynności:

* Aby wyświetlić kod, należy kliknąć notesu.
* Wykonaj każda komórka, naciskając klawisz **wprowadź SHIFT**.
* Uruchom cały notes, klikając **komórki** -> **Uruchom**.
* Użyj automatycznego wizualizacji zapytań.

> [!TIP]
> Jądra PySpark automatycznie wizualizuje dane wyjściowe zapytań SQL (HiveQL). Masz możliwość dokonania wyboru spośród kilku różnych typów wizualizacji (tabeli, kołowego, linii, obszaru lub pasku) przy użyciu **typu** przycisków menu w notesie:
>
>

![Regresja logistyczna krzywej ROC, ogólne podejście](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Co dalej?
Teraz, gdy są konfigurowane przy użyciu klastra usługi HDInsight Spark i zostały przekazane z notesów Jupyter, można przystąpić do pracy z tematami, które odnoszą się do trzech notesów PySpark. Pokazują, jak eksplorować dane, a następnie tworzenie i korzystanie z modeli. Notes Eksplorowanie i modelowanie zaawansowanych danych pokazuje, jak do uwzględnienia krzyżowa Weryfikacja, parametrów sprawdzaniu, a model oceny.

**Eksplorowanie i modelowanie danych za pomocą platformy Spark:** Zapoznaj się z zestawu danych i tworzenie, wynik i ocenić maszyny modeli uczenia dzięki pracy za pośrednictwem [Tworzenie binarnego modeli klasyfikacji i regresji dla danych z zestawem narzędzi Biblioteka MLlib platformy Spark](spark-data-exploration-modeling.md) tematu.

**Użycie modelu:** Aby dowiedzieć się, jak na ocenianie modeli klasyfikacji i regresji, utworzone w tym temacie, zobacz [wynik i ocena modeli uczenia maszynowego utworzonych na platformie Spark](spark-model-consumption.md).

**Krzyżowa Weryfikacja i zaczynają hiperparametrycznego**: Zobacz [zaawansowane Eksplorowanie i modelowanie za pomocą platformy Spark danych](spark-advanced-data-exploration-modeling.md) w sposób modeli może być uczony przy użyciu zaczynają krzyżowego sprawdzania poprawności i parametrów

