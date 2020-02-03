---
title: Analiza danych przy użyciu platformy Spark w usłudze Azure HDInsight — zespołu danych dla celów naukowych
description: Zestaw narzędzi Biblioteka MLlib platformy Spark oferuje znaczne usługi machine learning modelowanie się środowisku rozproszonym HDInsight.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718518"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Omówienie używania platformy Spark w usłudze Azure HDInsight do analizy danych

Tym zestawie tematów pokazuje, jak używać platformy HDInsight Spark do wykonania typowych zadań do nauki o danych, takich jak pozyskiwanie danych, technicznego opracowywania funkcji, modelowania i ocenę modelu. Dane używane jest przykładem 2013 NYC taksówek podróży i klasie zestawu danych. Modele utworzone obejmują regresji logistycznej liniowego i liniowa w, losowych lasów i gradientu wzmocnionego drzewa. W tematach opisano również sposób przechowywania tych modeli w usłudze Azure blob storage (WASB) oraz ocena i oceny wydajności predykcyjne. Obejmuje bardziej zaawansowanych tematów, jak modeli może być uczony przy użyciu zaczynają krzyżowego sprawdzania poprawności i parametrów. Ten ogólny temat odwołuje się również do tematów opisujących, jak skonfigurować klaster Spark, które należy wykonać czynności opisane w wskazówki podane.

## <a name="spark-and-mllib"></a>Platforma Spark oraz MLlib
[Platforma Spark](https://spark.apache.org/) to platforma przetwarzania równoległego typu open source, która obsługuje przetwarzanie w pamięci w celu zwiększenia wydajności aplikacji do analizy danych Big Data. Aparat przetwarzania Spark zaprojektowano pod kątem szybkości, łatwości użycia i zaawansowanych możliwości analitycznych. Możliwości obliczeń rozproszonych w pamięci platforma Spark, że dobry wybór w przypadku algorytmów iteracyjnych używanych używane w machine learning i obliczeniach na grafach. [MLlib](https://spark.apache.org/mllib/) to skalowalna biblioteka uczenia maszynowego platformy Spark, która zapewnia możliwości modelowania algorytmów w tym środowisku rozproszonym.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) to hostowana w systemie Azure oferta platformy Spark typu open source. Obejmuje również obsługę **notesów Jupyter PySpark** w klastrze Spark, które mogą uruchamiać zapytania Spark SQL Interactive na potrzeby przekształcania, filtrowania i wizualizacji danych przechowywanych w obiektach Blob platformy Azure (WASB). PySpark jest interfejsu API języka Python dla platformy Spark. Fragmenty kodu, do zapewniania rozwiązań, które pokazują odpowiednie wykresy w celu wizualizacji danych, w tym miejscu są uruchamiane w notesy Jupyter notebook zainstalowane w klastrach platformy Spark. Kroki modelowania w tych tematach zawierają kod, który pokazuje, jak uczenie, ocenę, Zapisz i zużywać każdy rodzaj modelu.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Instalacji: Klastry Spark i notesy Jupyter
Kroki instalacji i kodu w tym przewodniku dla podano przy użyciu HDInsight Spark 1.6. Ale notesów programu Jupyter znajdują się w przypadku klastrów HDInsight Spark 1.6 i platformy Spark w wersji 2.0. Opis notesów i linków do nich znajduje się w [README.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierającego je. Ponadto kod w tym miejscu w notesach połączonej jest ogólny i powinna działać w dowolnym klastrze Spark. Jeśli nie używasz platformy HDInsight Spark, konfiguracja klastra i czynności administracyjne mogą nieznacznie różnić się od przedstawionego w tym miejscu. Dla wygody poniżej przedstawiono linki do notesów Jupyter dla platformy Spark 1,6 (do uruchomienia w jądrze pySpark serwera Jupyter Notebook) i platformy Spark 2,0 (do uruchomienia w jądrze pySpark3 serwera Jupyter Notebook):

### <a name="spark-16-notebooks"></a>Notesy platformy Spark 1.6
Te notesy to będą uruchamiane w jądra pySpark serwer notesu Jupyter.

- [pySpark-Machine-Learning-Data-nauka-Spark-Data-Eksploracja-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): zawiera informacje na temat przeprowadzania eksploracji danych, modelowania i oceniania przy użyciu kilku różnych algorytmów.
- [pySpark-Machine-Learning-Data-nauka-Spark-Advanced-Data-Eksploracja-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): zawiera tematy w notesie #1 i projektowanie modeli przy użyciu strojenia parametrów i krzyżowego sprawdzania poprawności.
- [pySpark-Machine-Learning-Data-nauka-Spark-model-zużycie. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): pokazuje, jak operacjonalizować zapisany model przy użyciu języka Python w klastrach usługi HDInsight.

### <a name="spark-20-notebooks"></a>Notesy platformy Spark w wersji 2.0
Te notesy to będą uruchamiane w jądra pySpark3 serwer notesu Jupyter.

- [Spark 2.0-pySpark3-Machine-Learning-Data-nauka-Spark-Advanced-Data-Eksploracja — Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): ten plik zawiera informacje na temat sposobu przeprowadzania eksploracji danych, modelowania i oceniania w klastrach platformy Spark 2,0 przy użyciu usługi NYC Data podróży i [opłat.](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data) Ten notes może być dobry punkt wyjścia do szybkiego analizowania kodu, który udostępniliśmy dla platformy Spark w wersji 2.0. Aby uzyskać bardziej szczegółowy notesu analizuje dane taksówek NYC, zobacz następnego notesu na tej liście. Zapoznaj się z uwagami poniżej tej listy, która porównuje te notesy.
- [Spark 2.0-pySpark3_NYC_Taxi_Tip_Regression. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): w tym pliku pokazano, jak wykonywać operacje przetwarzanie danych (Spark SQL i Dataframe), eksploracja, modelowanie i ocenianie przy użyciu danych o podróży i opłatach za pomocą usługi NYC, które zostały opisane [tutaj](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark 2.0-pySpark3_Airline_Departure_Delay_Classification. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): w tym pliku pokazano, jak wykonywać operacje przetwarzanie danych (Spark SQL i Dataframe), eksploracja, modelowanie i ocenianie przy użyciu dobrze znanego zestawu danych wyjściowych w czasie od 2011 do 2012. Zestaw danych linii lotniczych jest zintegrowany z danymi o pogodzie lotniska (na przykład windspeed, temperatura, Wysokość itp.) przed modelem, dzięki czemu funkcje pogodowe mogą zostać uwzględnione w modelu.

<!-- -->

> [!NOTE]
> Linie lotnicze zestaw danych został dodany do notesów Spark 2.0, aby lepiej zilustrować używania algorytmów klasyfikacji. Zobacz poniższe linki do informacji na temat linii lotniczych na czas wyjścia zestawu danych i zestaw danych o pogodzie:
> 
> - Dane wychodzące z czasu transportu lotniczego: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Dane pogodowe lotniska: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> Notesy platformy Spark 2.0 na NYC taksówek i linie lotnicze lotu opóźnienie-zestawów danych może potrwać 10 minut lub dłużej (w zależności od rozmiaru klastra usługi HDI). Pierwszy Notes na powyższej liście przedstawia wiele aspektów uczenia się, wizualizacji i modelu ML w notesie, który zajmuje mniej czasu na uruchamianie z zestawem danych NYC z próbkami, w którym zostały wstępnie dołączone pliki z taksówką i taryfą: [Spark 2.0-pySpark3-Machine-Learning-Data-nauka-Spark-Advanced-Data-Eksploracja-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Ten Notes zajmuje dużo czasu na zakończenie (2-3 min) i może być dobrym punktem wyjścia do szybkiego eksplorowania kodu dostarczonego przez nas 2,0.

<!-- -->

Aby uzyskać wskazówki dotyczące operacjonalizacji modelu i użycia modelu platformy Spark 2,0 na potrzeby oceniania, zapoznaj się z [dokumentem dotyczącym platformy spark 1,6](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) , aby zapoznać się z przykładem. Aby użyć tego przykładu na platformie Spark 2,0, Zastąp plik kodu Python [tym plikiem](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Wymagania wstępne

Poniższe procedury odnoszą się do platformy Spark 1.6. Dla wersji platformy Spark w wersji 2.0 korzystanie z notesów opisem i powiązane z wcześniej.

1. Wymagana jest subskrypcja platformy Azure. Jeśli jeszcze tego nie masz, zobacz artykuł [Pobieranie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. Aby ukończyć ten przewodnik, potrzebny jest klaster Spark 1,6. Aby go utworzyć, zobacz instrukcje podane w artykule [wprowadzenie: tworzenie Apache Spark w usłudze Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Typ i wersja klastra są określone z menu **Wybierz typ klastra** .

![Konfigurowanie klastra](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Aby uzyskać informacje o tym, jak używać Scala zamiast języka Python do wykonywania zadań związanych z kompleksowym procesem nauki o danych, zobacz [naukę danych przy użyciu narzędzia Scala with Spark na platformie Azure](scala-walkthrough.md).
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>Dane taksówek 2013 NYC
Dane podróży taksówek NYC około 20 GB plików skompresowanych wartości rozdzielanych przecinkami (CSV) (~ 48 GB nieskompresowane), zawierających ponad milion 173 poszczególnych podróży i opłaty opłacony każdego podróży. Każdy rekord podróży obejmuje odbiór i dropoff lokalizacji i czasu, hack anonimowe (sterownika) numer licencji i numer Medalionu (unikatowy identyfikator dla taksówek). Dane obejmuje wszystkie podróży w roku 2013 i znajduje się w następujących dwóch zestawów danych w każdym miesiącu:

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

Pobrano 0,1% próbek tych plików i dołączono je do podróży\_danych i wyjazdu\_do jednego zestawu danych, który będzie używany jako zestaw danych wejściowych dla tego przewodnika. Unikatowy klucz do przyłączenia do podróży\_Data i podróż\_opłaty są złożone z pól: Medallion, hakerzy\_licencję i pobranie\_DateTime. Każdy rekord zestaw danych zawiera następujące atrybuty, które reprezentuje podróż taksówek NYC:

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
| direct_distance |Bezpośrednia odległość między lokalizacjami odbioru i Dropoff |
| payment_type |Typ płatności (gotówka, karta kredytowa itp.) |
| fare_amount |Kwota taryfy w |
| Opłata za opcję |Opłata za opcję |
| mta_tax |Podatek transportowy MTA |
| tip_amount |Porada kwota |
| tolls_amount |Kwota drogi |
| total_amount |Łączna kwota |
| Przechylony |Przechylony (0/1 dla nie lub tak) |
| tip_class |Porada klasy (0: $0, 1: $0-5, 2: $6 – 10, 3: $11 – 20, 4: > 20 USD) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Wykonanie kodu z notesu Jupyter w klastrze Spark
Można uruchomić notesu Jupyter w witrynie Azure portal. Znajdź klastra Spark na pulpicie nawigacyjnym i kliknij go, aby wprowadzić strony zarządzania dla klastra. Aby otworzyć Notes skojarzony z klastrem Spark, kliknij pozycję **pulpity nawigacyjne klastra** -> **Jupyter Notebook**.

![Pulpity nawigacyjne klastra](./media/spark-overview/spark-jupyter-on-portal.png)

Możesz również przejść do ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** , aby uzyskać dostęp do notesów Jupyter. Zastąp CLUSTERNAME część tego adresu URL, nazwą własnego klastra. Konieczne jest hasło do konta administratora dostęp do notesów.

![Przeglądaj notesów programu Jupyter](./media/spark-overview/spark-jupyter-notebook.png)

Wybierz pozycję PySpark, aby wyświetlić katalog zawierający kilka przykładów wstępnie spakowanych notesów korzystających z interfejsu API PySpark. Notesy zawierające przykłady kodu dla tego zestawu platformy Spark są dostępne w witrynie [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Notesy można przekazać bezpośrednio z witryny [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) do serwera notesu Jupyter w klastrze Spark. Na stronie głównej Jupyter kliknij przycisk **Przekaż** znajdujący się w prawej części ekranu. Spowoduje to otwarcie Eksploratora plików. W tym miejscu możesz wkleić adres URL witryny GitHub (nieprzetworzona zawartość) notesu i kliknąć pozycję **Otwórz**.

Na liście plików Jupyter zostanie wyświetlona nazwa pliku z przyciskiem **Przekaż** . Kliknij ten przycisk **Przekaż** . Teraz zaimportowano notesu. Powtórz te kroki, aby przekazywanie notesów z tego przewodnika.

> [!TIP]
> Możesz kliknąć prawym przyciskiem myszy linki w przeglądarce i wybrać pozycję **Kopiuj link** , aby uzyskać adres URL nieprzetworzonej zawartości usługi GitHub. Ten adres URL można wkleić w okno dialogowe przekazywanie Jupyter Eksploratora plików.
> 
> 

Teraz możesz wykonywać następujące czynności:

* Aby wyświetlić kod, należy kliknąć notesu.
* Wykonaj każdą komórkę, naciskając **klawisze SHIFT-ENTER**.
* Uruchom cały Notes, klikając **komórkę** -> **Run**.
* Użyj automatycznego wizualizacji zapytań.

> [!TIP]
> Jądra PySpark automatycznie wizualizuje dane wyjściowe zapytań SQL (HiveQL). Można wybrać różne typy wizualizacji (tabela, wykres kołowy, linia, obszar lub Słupek) za pomocą przycisków menu **Typ** w notesie:
>
>

![Regresja logistyczna krzywej ROC, ogólne podejście](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Co dalej?
Teraz, gdy są konfigurowane przy użyciu klastra usługi HDInsight Spark i zostały przekazane z notesów Jupyter, można przystąpić do pracy z tematami, które odnoszą się do trzech notesów PySpark. Pokazują, jak eksplorować dane, a następnie tworzenie i korzystanie z modeli. Notes Eksplorowanie i modelowanie zaawansowanych danych pokazuje, jak do uwzględnienia krzyżowa Weryfikacja, parametrów sprawdzaniu, a model oceny.

**Eksplorowanie i modelowanie danych za pomocą platformy Spark:** Eksplorowanie zestawu danych i tworzenie, Ocena i Ocena modeli uczenia maszynowego przez pracę w temacie [Create Binary Modeling and regresji dla danych za pomocą narzędzia Spark MLlib Toolkit](spark-data-exploration-modeling.md) .

**Użycie modelu:** Aby dowiedzieć się, jak oszacować modele klasyfikacji i regresji utworzone w tym temacie, zobacz [ocenę i ocenę modeli uczenia maszynowego opartych na platformie Spark](spark-model-consumption.md).

Czyszczenie z zastosowaniem **krzyżowym i**przeszukiwaniem: zobacz [Zaawansowane Eksploracja i modelowanie danych za pomocą platformy Spark](spark-advanced-data-exploration-modeling.md) , w jaki sposób można przeszkoleć modele przy użyciu weryfikacji krzyżowej i funkcji Hyper-Parameter.

