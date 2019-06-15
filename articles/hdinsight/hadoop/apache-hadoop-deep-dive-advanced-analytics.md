---
title: Szczegółowe informacje — advanced analytics — Azure HDInsight
description: Dowiedz się, jak Zaawansowana analiza używa algorytmów do przetwarzania danych big data.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: ac0edf2de4337154b665b8f3898134a7c2fd1f4c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64712395"
---
# <a name="deep-dive---advanced-analytics"></a>Szczegółowe informacje — zaawansowane analizy

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Co to jest zaawansowany analytics dla usługi HDInsight?

HDInsight zapewnia możliwość uzyskania wartościowy wgląd w informacje z dużych ilości strukturą i bez struktury i szybko zmieniających danych. Analiza zaawansowana polega korzystanie ze architektury wysoce skalowalną statystyczny modeli uczenia maszynowego i inteligentne pulpity nawigacyjne, aby udostępnić istotnych informacji. Uczenie maszynowe, lub *analizy predykcyjnej*, używa algorytmów, które zidentyfikować i ucz się od relacji w danych w celu tworzenia prognoz i przewodnik decyzje.

## <a name="advanced-analytics-process"></a>Proces zaawansowanej analizy

![Process](./media/apache-hadoop-deep-dive-advanced-analytics/process.png)

Po zidentyfikowaliśmy problem biznesowy i zostały uruchomione, zbierania i przetwarzania danych, należy utworzyć model, który reprezentuje pytanie chcesz przewidzieć. Model użyje jednego lub więcej algorytmów uczenia maszynowego Zmień typ prognozowania, który najlepiej spełnia Twoje potrzeby biznesowe.  Większość danych należy używać do nauczenia modelu, z użyciem usług rest używany w celu przetestowania lub ewaluacji go. 

Po utworzeniu, obciążenia, testowanie i ocenę modelu, następnym krokiem jest, aby wdrożyć model, dzięki czemu rozpocznie się, podając odpowiedzi na pytania. Ostatnim krokiem jest monitorowanie wydajności modelu i dostrojenie jej, zgodnie z potrzebami. 

## <a name="common-types-of-algorithms"></a>Popularne typy algorytmów

Zaawansowanych rozwiązań analizy zapewniają zestaw algorytmów uczenia maszynowego. Poniżej przedstawiono podsumowanie kategorii algorytmów i skojarzone typowych przypadków użycia biznesowego.

![W usłudze Machine Learning przypadki użycia](./media/apache-hadoop-deep-dive-advanced-analytics/ml-use-cases.png)

Wraz z wybraniu najlepszego dopasowania algorytmy, należy wziąć pod uwagę, czy należy podać dane do trenowania. Algorytmów uczenia maszynowego są podzielone na następujące kategorie:

* W trybie nadzorowanym — algorytm musi odbywać się w zestawie danych oznaczonych przed zapewnia wyniki
* Częściowo nadzorowanych — algorytm może zostać rozszerzony o dodatkowe obiekty docelowe za pomocą interakcyjnych zapytań przez instruktora, które nie były dostępne podczas początkowego etapu szkolenia
* Nienadzorowane — algorytm nie wymaga szkolenia danych 
* Wzmocnienie — algorytm używa oprogramowania agentów do określenia zachowania idealne rozwiązanie w określonym kontekście (często używane w związane z robotyką)


| Algorytm kategorii| Użycie | Typ nauki | Algorytmy |
| --- | --- | --- | -- |
| Klasyfikacja | Klasyfikowanie osób lub urządzeń do grup | W trybie nadzorowanym | Drzewa decyzyjne, regresji logistycznej, sieci neuronowych |
| Klastrowanie | Dzielenie zestaw przykładów na jednorodnego grup | Nienadzorowane | K-średnich klastrowania |
| Wzorzec wykrywania | Identyfikowanie częste powiązania danych | Nienadzorowane | Reguły kojarzenia |
| Regresji | Przewidywanie wyników liczbowych | W trybie nadzorowanym | Regresja liniowa sieci neuronowych |
| Wzmocnienie | Określ zachowanie optymalne dla robotów | Wzmocnienie | Monte Carlo Simulations, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Uczenia maszynowego w HDInsight

HDInsight ma kilka usługi machine learning opcje przepływ pracy zaawansowanej analizy:

* Uczenie maszynowe i Apache Spark
* R i usługi ML
* Usługi Azure Machine Learning i Apache Hive
* Platforma Apache Spark i uczenie głębokie

### <a name="machine-learning-and-apache-spark"></a>Uczenie maszynowe i Apache Spark


[HDInsight Spark](../spark/apache-spark-overview.md) to oferta hostowanymi na platformie Azure z [platformy Apache Spark](https://spark.apache.org/), ujednolicone, typu open source, platforma równoległego przetwarzania danych, która używa przetwarzanie w pamięci, aby zwiększyć możliwości analizy danych Big Data. Aparat przetwarzania Spark zaprojektowano pod kątem szybkości, łatwości użycia i zaawansowanych możliwości analitycznych. Możliwości obliczeń rozproszonych w pamięci platforma Spark, że dobry wybór w przypadku algorytmów iteracyjnych używanych używane w machine learning i obliczeniach na grafach. 


Istnieją trzy biblioteki uczenia skalowalne maszyny, które przenieść funkcje modelowania konsolidatorze na tym środowisku rozproszonym:

* [**Biblioteka MLlib** ](https://spark.apache.org/docs/latest/ml-guide.html) -MLlib zawiera oryginalny interfejs API, zbudowany na podstawie danych platformy Spark.
* [**SparkML** ](https://spark.apache.org/docs/1.2.2/ml-guide.html) -SparkML jest nowszy pakiet, który udostępnia interfejs API wyższego poziomu utworzonych na szczycie elementy Dataframe platformy Spark do konstruowania potokach uczenia Maszynowego.
* [**MMLSpark** ](https://github.com/Azure/mmlspark) — Microsoft Machine Learning library dla platformy Apache Spark (MMLSpark) umożliwia analitykom danych bardziej wydajnej pracy na platformie Spark, zwiększenia szybkości eksperymentowania i uczenie maszynowe najnowocześniejsze technik, w tym uczenia głębokiego, w bardzo dużych zestawów danych. Biblioteka MMLSpark upraszcza typowe zadania modelowania przeznaczone do budowania modeli w PySpark. 

### <a name="r-and-ml-services"></a>R i usługi ML

Jako część HDInsight, możesz utworzyć klaster usługi HDInsight przy użyciu [usługi ML](../r-server/r-server-overview.md) gotowe do użycia z ogromnych zestawów danych i modelami. Ta nowa funkcja umożliwia analitykom danych i Statystyków za pomocą znajomego interfejsu języka R, która może być skalowana na żądanie za pośrednictwem HDInsight, bez konieczności konfigurowania klastra oraz konserwacji.

### <a name="azure-machine-learning-and-apache-hive"></a>Usługi Azure Machine Learning i Apache Hive

[Usługa Azure Machine Learning Studio](https://studio.azureml.net/) udostępnia narzędzia do analizy predykcyjnej w modelu, a także w pełni zarządzana usługa umożliwia wdrażanie modeli predykcyjnych w postaci gotowych do użycia usług sieci web. Usługa Azure Machine Learning udostępnia narzędzia do tworzenia kompletnych rozwiązań analizy predykcyjnej w chmurze szybkie tworzenie, testowanie, operacjonalizacja i zarządzanie nimi modeli predykcyjnych. Wybierz z dużej biblioteki algorytmów, używać programu studio opartego na sieci web przeznaczone do budowania modeli i łatwy sposób wdrażania modelu w postaci usługi sieci web.

### <a name="apache-spark-and-deep-learning"></a>Platforma Apache Spark i uczenie głębokie

[Uczenie głębokie](https://www.microsoft.com/research/group/dltc/) to gałąź usługi machine learning, która używa *głębokich sieciach neuronowych* (sieci), ZAINSPIROWANE procesom mózgu ludzi. Wielu pracowników naukowo-badawczych Zobacz uczenia głębokiego jako obietnic podejście dla sztucznej inteligencji. Kilka przykładów uczenia głębokiego są tłumaczy mowy, systemy rozpoznawania obrazów i logikę maszyny. Aby pomóc w dojściu pracę w uczenia głębokiego, firma Microsoft opracowała bezpłatny, łatwy w użyciu, open source [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/). Zestaw narzędzi jest często używana przez szerokiej gamy produktów firmy Microsoft, firm na całym świecie z konieczności wdrażania uczenia głębokiego na dużą skalę i studentów zainteresowani najnowsze algorytmy i techniki. 

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scenariusz — obrazy wynik w celu zidentyfikowania wzorców w rozwój miast

Omówmy teraz przykładem maszynie zaawansowane funkcje analityczne uczenia potoku przy użyciu HDInsight.

W tym scenariuszu, zobaczysz, jak jednokierunkowe sieci Dnn generowany w ramach uczenia głębokiego firmy Microsoft Cognitive Toolkit (CNTK), może być udało do oceniania kolekcje duży obraz przechowywany na koncie usługi Azure Blob Storage przy użyciu PySpark w klastrze usługi HDInsight Spark. To podejście jest stosowany do wspólnego DNN przypadek użycia, Klasyfikacja obrazów z powietrza i może służyć do identyfikowania najnowszych wzorce w rozwój miast.  Model klasyfikacji wstępnie przeszkolonych obraz zostanie użyty. Model, który jest wstępnie przeszkolonych na [dataset CIFAR 10](https://www.cs.toronto.edu/~kriz/cifar.html) i została zastosowana do 10 000 obrazów wstrzymana.

W tym scenariuszu zaawansowane funkcje analityczne, istnieją trzy główne zadania:

1. Utworzenie klastra usługi Azure HDInsight Hadoop z dystrybucji platformy Apache Spark 2.1.0. 
2. Uruchom niestandardowy skrypt, aby zainstalować Microsoft Cognitive Toolkit we wszystkich węzłach klastra usługi Azure HDInsight Spark. 
3. Przekazywanie notesu programu Jupyter gotowych do klastra usługi HDInsight Spark do zastosowania uczonego Microsoft Cognitive Toolkit głębokie uczenie modelu do plików w konto usługi Azure Blob Storage przy użyciu interfejsu API Python platformy Spark (PySpark). 

W tym przykładzie użyto zestawu, skompilowane i rozproszonych Alex Krizhevsky, Vinod Nair i Geoffrey Hinton obrazów CIFAR 10. Zestaw danych CIFAR 10 zawiera 60 000 32 × 32 kolor obrazów należących do 10 wzajemnie wykluczających się klasy:

![Obrazy](./media/apache-hadoop-deep-dive-advanced-analytics/ml-images.png)

Aby uzyskać szczegółowe informacje na temat zestawu danych, zobacz Alex Krizhevsky [Learning wiele warstw z funkcji z niewielki rozmiar obrazów](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Zestaw danych została poddana partycjonowaniu na zestaw szkoleniowy, 50 000 obrazów i zestaw testów 10 000 obrazów. Pierwszy zestaw został użyty do uczenia modelu dwadzieścia — warstwa głębokiego splotowe sieci pozostają resztkowe (siecią ResNet), przy użyciu zestawu narzędzi Microsoft Cognitive Toolkit, postępując zgodnie z [w tym samouczku](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) z repozytorium GitHub usługi Cognitive Toolkit. Pozostałych 10 000 obrazów były używane do testowania dokładności modelu. Jest to, gdzie przetwarzania rozproszonego właśnie: zadanie przetwarzania wstępnego i oceniania obrazów jest wysoce równoległego. Zapisane uczonego modelu w kasie użyliśmy:

* PySpark do dystrybucji obrazów i uczonego modelu z węzłami procesu roboczego klastra.
* Python wstępnego przetwarzania obrazów w każdym węźle klastra platformy HDInsight Spark.
* Zestaw narzędzi cognitive Toolkit do załadowania modelu a wynik wstępnie przetworzonych obrazów w każdym węźle.
* Notesy Jupyter w celu uruchomienia skryptu PySpark agregowanie wyników, a następnie użyj [Matplotlib](https://matplotlib.org/) wizualizacji wydajności modelu.

Całe przetwarzanie wstępne/ocenianie 10 000 obrazów trwa mniej niż minutę w klastrze z 4 węzłów procesu roboczego. Model przewiduje dokładnie etykiet ~ 9,100 (91%) obrazy. Macierz pomyłek przedstawia najbardziej typowe błędy klasyfikacji. Na przykład pokazuje macierzy, przechowywaniu psy jako koty i na odwrót występowanie ponad często dla innych par etykiety.

![Wyniki](./media/apache-hadoop-deep-dive-advanced-analytics/ml-results.png)

### <a name="try-it-out"></a>Wypróbuj!

Postępuj zgodnie z [w tym samouczku](../spark/apache-spark-microsoft-cognitive-toolkit.md) implementacji tego rozwiązania end-to-end: Konfigurowanie klastra usługi HDInsight Spark, zainstaluj zestaw narzędzi Cognitive Toolkit i uruchamianie notesu programu Jupyter, która ocenia 10 000 CIFAR obrazów.

## <a name="next-steps"></a>Kolejne kroki

Apache Hive, jak i usługi Azure Machine Learning

* [Apache Hive i usługi Azure Machine Learning end-to-end](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Przy użyciu klastra usługi Azure HDInsight Hadoop na zestawie danych 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Platforma Apache Spark oraz MLLib

* [Uczenie maszynowe przy użyciu platformy Apache Spark w HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Platforma Apache Spark w usłudze Machine Learning: Use Apache Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Apache Spark w HDInsight do przewidywania wyników kontroli żywności](../spark/apache-spark-machine-learning-mllib-ipython.md)

Uczenie głębokie, Cognitive Toolkit i innych

* [Do nauki o danych maszyny wirtualnej platformy Azure](../../machine-learning/data-science-virtual-machine/overview.md)
* [Wprowadzenie do H2O.ai w usłudze Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
