---
title: Nowości — zaawansowane analizy - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zaawansowana analityka używa algorytmów do przetwarzania danych big data.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: f121a1f95eed04bf8086c03a5d4406c092dc9445
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400677"
---
# <a name="deep-dive---advanced-analytics"></a>Nowości — zaawansowane analizy

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Co to jest zaawansowane analizy dla usługi HDInsight?

Usługa HDInsight zapewnia możliwość uzyskania cenny wgląd w duże ilości strukturalnych, bez struktury i szybko danych. Zaawansowana analityka to korzystanie ze skalowalnej architektur, statystyczne machine learning modeli i inteligentnego pulpitów nawigacyjnych, które zapewniają wgląd w istotne dane. Learning, komputera lub *analizy predykcyjnej*, używa algorytmów, które identyfikują i Dowiedz się od relacji w danych do tworzenia prognoz i przewodnik swoje decyzje dotyczące.

## <a name="advanced-analytics-process"></a>Proces zaawansowana analityka

![Proces](./media/apache-hadoop-deep-dive-advanced-analytics/process.png)

Po wyłaniają się problem biznesowy i uruchomiony zbierania i przetwarzania danych, należy utworzyć model, który reprezentuje pytanie chcesz przewidzieć. Model użyje co najmniej jeden algorytmów uczenia maszynowego aby typ prognozowania, który będzie najlepiej dostosowane do potrzeb biznesowych.  Większość danych należy używać do nauczenia modelu, z resztą używane w celu przetestowania lub ewaluacji go. 

Po utworzeniu, obciążenia, testowania i oceny modelu, następnym krokiem jest wdrażania modelu, dzięki czemu zaczyna się dostarczanie odpowiedzi na pytania. Ostatnim krokiem jest monitorować wydajność, modelu i dostrajania go w razie potrzeby. 

## <a name="common-types-of-algorithms"></a>Popularne typy algorytmów

Zaawansowana analityka rozwiązań zapewnia zbiór algorytmów uczenia maszynowego. Poniżej przedstawiono podsumowanie kategorii algorytmów i skojarzone typowe przypadki użycia biznesowych.

![Przypadki użycia uczenia maszynowego](./media/apache-hadoop-deep-dive-advanced-analytics/ml-use-cases.png)

Wraz z wybranie najlepszego dopasowania algorytmy, należy wziąć pod uwagę, czy należy podać dane do szkolenia. Algorytmów uczenia maszynowego są podzielone na następujące kategorie:

* Nadzorowane — algorytm musi być szkoleni w zestawie danych oznaczonych przed może udostępnić wyników
* Częściowo nadzorowane — algorytm może zostać rozszerzony o dodatkowe elementy docelowe za pośrednictwem interaktywnego kwerendy trainer, które nie były dostępne w początkowym etapie szkolenia
* Nienadzorowane - algorytmu nie wymaga szkolenia danych 
* Wzmocnienie — algorytm używa oprogramowania agentów do określenia zachowania idealne w określonym kontekście (często używane w robotics)


| Algorytm kategorii| Użycie | Learning typu | Algorytmy |
| --- | --- | --- | -- |
| Klasyfikacja | Klasyfikowanie osoby lub elementy w grupach | Nadzorowane | Drzewa decyzyjne, Regresja logistyczna, sieci neuronowe |
| Klastrowanie | Dzielenie zestaw przykłady na jednorodnego grup | Nienadzorowane | K-średnich klastra |
| Wzorzec wykrywania | Zidentyfikuj częste powiązania danych | Nienadzorowane | Skojarzenie zasad |
| Regresja | Przewidywanie wyników numeryczne | Nadzorowane | Regresja liniowa, sieci neuronowe |
| Wzmocnienie | Określ zachowanie optymalne dla robotów | Wzmocnienie | Symulacje Monte Carlo DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Machine learning w usłudze HDInsight

HDInsight ma kilka machine learning opcje zaawansowane metody analizy przepływu pracy:

* [Uczenie maszynowe i Spark](#machine-learning-and-spark)
* [Serwer R i r](#r-and-r-server)
* [Uczenie maszynowe Azure i Hive](#azure-machine-learning-and-hive)
* [Platforma Spark i uczenie się bezpośrednich](#spark-and-deep-learning)

### <a name="machine-learning-and-spark"></a>Uczenie maszynowe i Spark

[HDInsight Spark](../spark/apache-spark-overview.md) jest oferowany hostowanymi na platformie Azure z [Spark](http://spark.apache.org/), ujednoliconego typu open source, platforma równoległego przetwarzania danych, która korzysta z przetwarzania w pamięci w celu zwiększania analizy danych Big Data. Aparat przetwarzania Spark zaprojektowano pod kątem szybkości, łatwości użycia i zaawansowanych możliwości analitycznych. Możliwości rozproszone obliczenia w pamięci platforma Spark stanowić dobry wybór w przypadku algorytmów iteracyjnych w machine learning i obliczeniach na grafach. 

Istnieją trzy biblioteki learning maszyny skalowalne, zapewniające możliwości algorytmicznego modelowania w tym środowisku rozproszonym:

* [**MLlib** ](https://spark.apache.org/docs/latest/ml-guide.html) -MLlib zawiera oryginalnego interfejsu API, rozszerzający Spark RDDs.
* [**SparkML** ](https://spark.apache.org/docs/1.2.2/ml-guide.html) -SparkML jest nowsza pakietu, który udostępnia interfejs API wyższego poziomu, rozszerzający Spark DataFrames tworzenia ML potoków do.
* [**MMLSpark** ](https://github.com/Azure/mmlspark) - Microsoft Machine Learning biblioteki dla Apache Spark (MMLSpark) umożliwia analityków danych bardziej wydajnej pracy na Spark, zwiększenia szybkości eksperymenty i uczenie maszynowe najnowocześniejsze technik, w tym uczenie głębokie, na bardzo dużych zestawów danych. Biblioteka MMLSpark upraszcza typowe zadania modelowania budowania modeli w PySpark. 

### <a name="r-and-r-server"></a>Serwer R i r

W ramach usługi HDInsight, można utworzyć klastra usługi HDInsight [R Server](../r-server/r-server-overview.md) gotowe do użycia z duże zestawy danych i modele. Ta nowa funkcja zapewnia chi z interfejsem R znanych można skalować i analityków danych na żądanie za pośrednictwem usługi HDInsight, bez potrzeby Konfiguracja klastra i konserwacji.

### <a name="azure-machine-learning-and-hive"></a>Uczenie maszynowe Azure i Hive

[Azure Machine Learning Studio](https://studio.azureml.net/) udostępnia narzędzia do analizy predykcyjnej modelu, a także pełni zarządzana usługa umożliwia wdrażanie modeli predykcyjnych w postaci gotowych do użycia usług sieci web. Usługa Azure Machine Learning zapewnia narzędzia do tworzenia kompletnych rozwiązań analizy predykcyjnej w chmurze szybkie tworzenie, testowanie, operacjonalizacji i zarządzanie nimi modeli predykcyjnych. Wybierz od dużej biblioteki algorytmów, użycia studia opartych na sieci web w celu utworzenia modeli i łatwy sposób wdrażania modelu w postaci usługi sieci web.

### <a name="spark-and-deep-learning"></a>Platforma Spark i uczenie się bezpośrednich

[Głębokie learning](https://www.microsoft.com/research/group/dltc/) jest gałęzią uczenia maszynowego, która używa *sieci neuronowe głębokość* (DNNs), inspirowana przez procesom z inteligencji człowieka. Wiele pracowników naukowo-badawczych Zobacz głębokie learning jako obietnicy podejście związane z analizą sztucznego. Przykłady głębokie learning są tłumaczy język rozmowy, systemy rozpoznawania obrazu i rozsądkiem maszyny. Aby poprawić pracę w uczeniu głębokie, firma Microsoft opracowała bezpłatne, łatwy w użyciu, open source [kognitywnych zestaw narzędzi firmy Microsoft](https://www.microsoft.com/cognitive-toolkit/). Zestaw narzędzi jest często używana przez cały szereg produktów firmy Microsoft, firm na świecie potrzebuje do wdrożenia głębokiego uczenie na dużą skalę i studentów zainteresowana najnowsze algorytmy i techniki. 

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scenariusz — obrazy wynik, aby zidentyfikować wzorce w rozwój miast

Załóżmy Przejrzyj przykładem maszyny zaawansowana analityka uczenia potoku za pomocą usługi HDInsight.

W tym scenariuszu zobaczysz, jak DNNs utworzonego w ramach głębokiego learning Microsoft obiektu kognitywnych Toolkit (CNTK), można operationalized wyników kolekcji duży obraz przechowywany w konta magazynu obiektów Blob Azure przy użyciu PySpark w klastrze Spark w usłudze HDInsight. Takie podejście jest stosowany do wspólnej DNN przypadek użycia, klasyfikacji satelitarnej obrazu i może służyć do określania ostatnie schematów rozwój miast.  Zostanie użyty obraz wstępnie uczonego modelu klasyfikacji. Model jest wstępnie przeszkolone na [dataset CIFAR 10](https://www.cs.toronto.edu/~kriz/cifar.html) i zastosowano do 10 000 obrazów wstrzymana.

Istnieją trzy kluczowe zadania w tym scenariuszu zaawansowane metody analizy:

1. Tworzenie klastra usługi Azure HDInsight Hadoop z dystrybucji Apache Spark 2.1.0. 
2. Uruchomienie skryptu niestandardowego do zainstalowania kognitywnych zestaw narzędzi firmy Microsoft na wszystkich węzłach klastra usługi Azure HDInsight Spark. 
3. Przekaż wbudowanych notesu Jupyter do klastra Spark w usłudze HDInsight do zastosowania przeszkolone kognitywnych zestaw narzędzi firmy Microsoft głębokie uczenie modelu do plików na koncie magazynu obiektów Blob Azure przy użyciu interfejsu API języka Python Spark (PySpark). 

W tym przykładzie użyto zestawu skompilowanego i rozproszone Alexowi Krizhevsky, Vinod Nair i Geoffrey Hinton obrazów CIFAR 10. Zestaw danych CIFAR 10 zawiera 60 000 32 × 32 kolor obrazy należące do klasy wykluczają się wzajemnie 10:

![Obrazy](./media/apache-hadoop-deep-dive-advanced-analytics/ml-images.png)

Aby uzyskać więcej informacji dotyczących zestawu danych, zobacz Alexowi Krizhevsky [Learning wielu warstw funkcji z niewielki rozmiar obrazów](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Zestaw danych został podzielony na partycje do zestawu szkoleniowego, 50 000 obrazów i zbiór obrazy 10 000. Pierwszy zestaw został użyty do uczenia modelu dwadzieścia warstwy głębokiego convolutional pozostałych sieci (ResNet), używając następującego kognitywnych zestaw narzędzi firmy Microsoft [w tym samouczku](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) z repozytorium Toolkit GitHub kognitywnych. Pozostałe obrazy 10 000 były używane do testowania dokładności modelu. Jest to, gdzie wejścia przetwarzania rozproszonego play: zadanie przetwarzania wstępnego i oceniania obrazów jest wysoce działania równoległego. Zapisane uczonego modelu w ręcznie użyliśmy:

* PySpark do dystrybucji obrazów i uczonego modelu do klastra węzłów procesu roboczego.
* Python wstępnie przetworzyć obrazach w każdym węźle klastra Spark w usłudze HDInsight.
* Kognitywnych Toolkit można załadować modelu i wyniku wstępnie przetworzonych obrazów w każdym węźle.
* Notesów Jupyter do uruchamiania skryptu PySpark agregacji wyniki i użyj [Matplotlib](https://matplotlib.org/) do wizualizacji wydajności modelu.

Cały przetwarzania wstępnego/oceniania 10 000 obrazów ma mniej niż minutę w klastrze z 4 węzłami procesów roboczych. Model przewiduje dokładnie etykiet ~ 9,100 obrazów (91%). Macierz pomyłek przedstawiono typowe błędy klasyfikacji. Na przykład macierzy pokazuje, że przechowywaniu psów jako kotów i na odwrót występuje ponad często innych par etykiety.

![Wyniki](./media/apache-hadoop-deep-dive-advanced-analytics/ml-results.png)

### <a name="try-it-out"></a>Wypróbuj

Postępuj zgodnie z [w tym samouczku](../spark/apache-spark-microsoft-cognitive-toolkit.md) implementacji tego rozwiązania end-to-end: Konfiguracja klastra Spark w usłudze HDInsight, zainstaluj kognitywnych zestawu narzędzi, a następnie uruchom notesu Jupyter, który wyników obrazów CIFAR 10 000.

## <a name="next-steps"></a>Kolejne kroki

Hive i uczenie maszynowe Azure

* [Hive i usługi Azure Machine Learning end-to-end](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [W zestawie 1 TB danych przy użyciu klastra usługi Azure HDInsight Hadoop](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Platforma Spark i MLLib

* [Machine learning z platformy Spark w usłudze HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury w budynku z użyciem danych HVAC](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](../spark/apache-spark-machine-learning-mllib-ipython.md)

Głębokie uczenia, kognitywnych zestaw narzędzi i innych

* [Obraz embarrassingly równoległych klasyfikacji, za pomocą zestawu narzędzi kognitywnych i TensorFlow na Azure HDInsight Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
* [Połączenie analiz danych maszyny wirtualnej platformy Azure](../../machine-learning/data-science-virtual-machine/overview.md)
* [Wprowadzenie H2O.ai w usłudze Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
