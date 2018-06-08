---
title: Omówienie nauki danych przy użyciu platformy Spark w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Zestaw narzędzi platformy Spark MLlib łączy uczenia maszynowego znaczne modelowanie się środowisku rozproszonym usługi HDInsight.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: a4e1de99-a554-4240-9647-2c6d669593c8
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: f15596bbd8a9b8bab96eeae738b63df934620648
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838497"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Omówienie nauki danych przy użyciu platformy Spark w usłudze Azure HDInsight
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Ten pakiet tematy przedstawia sposób użycia Spark w usłudze HDInsight do wykonywania typowych zadań analizy danych, takich jak wprowadzanie danych, funkcja engineering modelowania i oceny modelu. Dane używane jest przykładowe 2013 NYC taksówki podróży i taryfy zestawu danych. Modele wbudowane obejmują Regresja logistyczna i liniowych, losowe lasów i gradientu boosted drzewa. W tematach opisano również do przechowywania tych modeli w magazynie obiektów blob platformy Azure (WASB) oraz sposobu wynik i ocena wydajności predykcyjnej. Bardziej zaawansowanych tematów dotyczących opisano, jak można modeli uczenia przy użyciu kominów krzyżowego sprawdzania poprawności i parametru funkcji hyper. Ten temat odwołuje się również do tematów opisujących sposób konfigurowania klastra Spark, które należy wykonać czynności opisane w wskazówki podane. 

## <a name="spark-and-mllib"></a>Platforma Spark i MLlib
[Platforma Spark](http://spark.apache.org/) jest przetwarzanie platforma przetwarzania równoległego open source, który obsługuje w pamięci w celu zwiększania wydajności aplikacji do analizy danych big data. Aparat przetwarzania Spark zaprojektowano pod kątem szybkości, łatwości użycia i zaawansowanych możliwości analitycznych. Możliwości rozproszone obliczenia w pamięci platforma Spark stanowić dobry wybór w przypadku algorytmów iteracyjnych w machine learning i obliczeniach na grafach. [MLlib](http://spark.apache.org/mllib/) jest platforma Spark skalowalne maszyny learning biblioteki wprowadzający algorytmicznego modelowanie tej środowisku rozproszonym. 

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) jest platformy Azure hostowanej oferty open source platformy Spark. Obejmuje również obsługę **notesów Jupyter PySpark** w klastrze Spark, który można uruchomić interakcyjnych zapytań Spark SQL Przekształcanie, filtrowania i wizualizacja danych przechowywanych w obiektach blob Azure (WASB). PySpark to interfejs API języka Python dla platformy Spark. Wstawki kodu dostarczające rozwiązań i Pokaż odpowiednich powierzchni do wizualizacji danych w tym miejscu Uruchom w notesach Jupyter zainstalowany w klastrze Spark. Kroki modelowania w tych tematach zawiera kod, który pokazuje, jak uczenia, ocenić, zapisywania i korzystać z każdego typu modelu. 

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Instalatora: Klastry Spark i notesów Jupyter
Kroki instalacji i kodu są dostępne w tym przewodniku dla przy użyciu wersji 1.6 HDInsight Spark. Ale notesów Jupyter znajdują się w przypadku klastrów HDInsight Spark 1.6 jak Spark 2.0. Opis notesów i łącza do nich znajdują się w [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierające je. Ponadto kod w tym miejscu w notesach połączony jest rodzajowy i powinny działać na dowolnym klastra Spark. Jeśli nie używasz Spark w usłudze HDInsight, konfiguracja klastra i czynności administracyjne mogą być nieco inne niż to, co przedstawiono w tym miejscu. Dla wygody Oto łącza do notesu Jupyter w klastrze Spark w wersji 1.6 (do uruchomienia jądra pySpark serwera notesu Jupyter) i 2.0 Spark (do uruchomienia jądra pySpark3 serwera notesu Jupyter):

### <a name="spark-16-notebooks"></a>Notesów Spark w wersji 1.6
Te komputery przenośne są uruchamiane w jądra pySpark serwera notesu Jupyter.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): zawiera informacje na temat sposobu wykonywania Eksploracja danych, modelowania i oceniania z kilku różnych algorytmów.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): zawiera tematy w notesie #1 i tworzenia modelu przy użyciu hyperparameter dostrajanie i krzyżowego sprawdzania poprawności.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): pokazano, jak operacjonalizacji zapisany model przy użyciu języka Python w klastrach usługi HDInsight.

### <a name="spark-20-notebooks"></a>Notesów Spark 2.0
Te komputery przenośne są uruchamiane w pySpark3 jądra serwera notesu Jupyter.

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

Wskazówki dotyczące operationalization model Spark 2.0 i zużycia modelu do oceniania, zobacz [Spark 1.6 dokumentu o zużyciu](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) przykład zwijania kroki wymagane. Aby użyć tej funkcji na Spark 2.0, Zastąp plik kodu Python z [ten plik](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Wymagania wstępne
Poniższe procedury dotyczą Spark 1.6. W wersji Spark 2.0 Użyj notesów opisane i połączone z wcześniej. 

1 użytkownik musi mieć subskrypcję platformy Azure. Jeśli nie masz już jeden, zobacz [Azure Pobierz bezpłatną wersję próbną](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. należy klastra Spark w wersji 1.6, w tym przewodniku. Aby go utworzyć, zobacz instrukcje podane w [wprowadzenie: tworzenie Apache Spark w usłudze Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Typ klastra i wersji określonego z **wybierz typ klastra** menu. 

![Konfigurowanie klastra](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Na temat, który przedstawia sposób użycia Scala zamiast Python do wykonywania zadań w procesie nauki danych na całej trasie, zobacz [nauki danych przy użyciu języka Scala z łącznikiem Spark on Azure](scala-walkthrough.md).
> 
> 

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

## <a name="the-nyc-2013-taxi-data"></a>Dane NYC taksówki 2013
Dane podróży taksówki NYC to około 20 GB skompresowanego wartości rozdzielanych przecinkami (CSV) plików (~ 48 GB nieskompresowane), składającej się z więcej niż 173 milionów poszczególnych podróży i opłaty płatnej dla każdej podróży. Każdy rekord podróży obejmuje odbioru i przyjmowania lokalizacji i czasu, hack anonimowe (sterownik) numer licencji i numer Medalionu (taksówki jego unikatowy identyfikator). Dane obejmuje wszystkie rund w roku 2013 i jest dostępne w następujących dwóch zestawów danych dla każdego miesiąca:

1. Pliki CSV "trip_data" zawierają szczegóły podróży, takie jak liczba pasażerów, podnieś i dropoff punktów, rzeczy przed wyjazdem czas trwania i długości podróży. Poniżej przedstawiono kilka przykładowych rekordów:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Pliki CSV "trip_fare" zawierają szczegółowe informacje o klasie za każdym razem, takie jak typ płatności, kwota taryfy, przeciążenia i podatków, porady i przejazd i sumy płatnej. Poniżej przedstawiono kilka przykładowych rekordów:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Firma Microsoft ma próbkę 0,1% tych plików i połączonych podróż\_danych i podróży\_taryfy CVS plików do jednego zestawu danych do użycia jako danych wejściowych dla tego przewodnika. Unikatowy klucz do przyłączenia podróży\_danych i podróży\_taryfy składa się z pola: Medalionu, hack\_licencji i pobrania\_daty/godziny. Każdy rekord zestaw danych zawiera następujące atrybuty reprezentujący podróży taksówki NYC:

| Pole | Krótki opis |
| --- | --- |
| Medalionu |Anonimowe taksówki Medalionu (taksówki Unikatowy identyfikator) |
| hack_license |Numer licencji karetki Hackney anonimowe |
| vendor_id |Identyfikator dostawcy taksówki |
| rate_code |Szybkość taksówki NYC Taryfy |
| store_and_fwd_flag |Magazyn i flagi do przodu |
| pickup_datetime |Wybierz datę i godzinę |
| dropoff_datetime |Dropoff wartość daty i godziny |
| pickup_hour |Wybierz godzinę |
| pickup_week |Wybierz tydzień roku |
| dzień tygodnia |Dzień tygodnia (zakresu 1-7) |
| passenger_count |Liczba osób w podróży taksówki |
| trip_time_in_secs |Podróży czas w sekundach |
| trip_distance |Dystans podróży w milach |
| pickup_longitude |Wybierz długość geograficzna |
| pickup_latitude |Podnieś współrzędnych |
| dropoff_longitude |Dropoff longitude |
| dropoff_latitude |Współrzędne Dropoff |
| direct_distance |Bezpośrednie odległość między pobrania w górę i lokalizacje dropoff |
| payment_type |Typ płatności (urzędów certyfikacji, karta kredytowa itp.) |
| fare_amount |Kwota taryfy w |
| Przeciążenia |Przeciążenia |
| mta_tax |Podatek MTA |
| tip_amount |Porada kwota |
| tolls_amount |Kwota przejazd |
| total_amount |Suma |
| Przechylony |Przechylony (0/1 dla nie lub tak) |
| tip_class |Porada klasy (0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Wykonanie kodu z notesu Jupyter w klastrze Spark
Można uruchomić notesu Jupyter z portalu Azure. Znajdź klastra Spark na pulpicie nawigacyjnym i kliknij go, aby wprowadzić strony zarządzania dla klastra. Aby otworzyć notesu skojarzony z klastrem Spark, kliknij przycisk **pulpitów nawigacyjnych klastrów** -> **notesu Jupyter** .

![Pulpity nawigacyjne klastra](./media/spark-overview/spark-jupyter-on-portal.png)

Możesz również przejść do ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** do notesów Jupyter. Zamień na nazwę własnego klastra NAZWAKLASTRA częścią tego adresu URL. Potrzebne hasło do konta administratora dostęp do notesów.

![Przeglądaj notesów Jupyter](./media/spark-overview/spark-jupyter-notebook.png)

Wybierz PySpark, aby wyświetlić katalog, który zawiera kilka przykładów opakowanych notebooki, które korzystają z interfejsu API PySpark. Notesy, które zawierają przykłady kodu dla tego zestawu Spark tematu są dostępne pod adresem [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Możesz przekazać notesów bezpośrednio z [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) serwerowi notesu Jupyter w klastrze Spark. Na stronie głównej programu Jupyter kliknij **przekazać** przycisk w prawej części ekranu. Spowoduje to otwarcie Eksploratora plików. W tym miejscu można wkleić adres URL GitHub (nieprzetworzonej zawartości) notesu i kliknij przycisk **Otwórz**. 

Nazwa pliku zostanie wyświetlony na liście plików Jupyter z **przekazać** przycisk ponownie. Kliknij tutaj, **przekazać** przycisku. Teraz zaimportowano notesu. Powtórz te kroki, aby przekazać komputery przenośne z tego przewodnika.

> [!TIP]
> Możesz kliknąć prawym przyciskiem myszy łączy przeglądarki i wybierz **Kopiuj Link** uzyskanie github nieprzetworzonej zawartości adresu URL. Ten adres URL można wkleić do okno dialogowe przekazywania Jupyter Eksploratora plików.
> 
> 

Teraz możesz:

* Aby wyświetlić kod, należy kliknąć notesu.
* Wykonanie każdej komórki naciskając **wprowadź SHIFT**.
* Uruchom cały notes klikając **komórki** -> **Uruchom**.
* Użyj automatycznego wizualizacji zapytań.

> [!TIP]
> Jądro PySpark automatycznie wizualizuje wynik zapytania SQL (HiveQL). Podano opcję, aby wybrać spośród kilku różnych typów wizualizacji (tabeli, kołowego, wiersz, obszar lub pasek) przy użyciu **typu** przycisków menu w notesie:
> 
> 

![Regresja logistyczna krzywą ROC dla metody ogólne](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Co dalej?
Teraz, gdy są skonfigurowane z klastra Spark w usłudze HDInsight i zostały przekazane z notesów Jupyter, można przystąpić do pracy z tematami, które odpowiadają w notesach trzy PySpark. Przedstawiają sposób eksplorować dane, a następnie tworzenie i korzystanie z modeli. Zaawansowane danych notesu eksploracji i modelowanie pokazuje, jak do uwzględnienia krzyżowego sprawdzania poprawności, funkcji hyper parametr profilach, a model oceny. 

**Eksploracja danych i modelowanie z Spark:** Eksploruj zestawu danych i tworzenie, wynik i ocenić maszyny modeli uczenia przez pracy nad [utworzyć binarne klasyfikacji i regresji modeli danych z Spark MLlib zestaw narzędzi](spark-data-exploration-modeling.md) tematu.

**Model zużycia:** informacje na temat wynik modele klasyfikacji i regresji, utworzone w tym temacie, zobacz [wynik i ocena modele uczenia wbudowane Spark maszyny](spark-model-consumption.md).

**Krzyżowe sprawdzanie poprawności i kominów hyperparameter**: zobacz [zaawansowane Eksploracja danych i modelowania z Spark](spark-advanced-data-exploration-modeling.md) na jak modeli można uczony przy użyciu kominów krzyżowego sprawdzania poprawności i parametru funkcji hyper

