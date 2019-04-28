---
title: Przegląd usług Machine learning — Azure HDInsight
description: W tym artykule opisano opcje w HDInsight uczenia maszynowego.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: hrasheed
ms.openlocfilehash: ca69424ecbddc068fdaf8bb5839a647701ee27fe
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766727"
---
# <a name="machine-learning-on-hdinsight"></a>Uczenia maszynowego w HDInsight

HDInsight umożliwia uczenie maszynowe przy użyciu danych big data, umożliwiając uzyskanie wartościowy wgląd w informacje z dużych ilości (petabajtów, a nawet eksabajtów) strukturą i bez struktury i szybko zmieniających danych. Istnieje kilka usługi machine learning opcje w HDInsight:  SparkML i Apache, MLlib platformy Spark, R, Apache Hive i Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML oraz MLlib

[HDInsight Spark](spark/apache-spark-overview.md) to oferta hostowanymi na platformie Azure z [platformy Apache Spark](https://spark.apache.org/), ujednolicone, typu open source, w ramach równoległego przetwarzania danych obsługuje przetwarzanie w pamięci, aby zwiększyć możliwości analizy danych big data. Aparat przetwarzania Spark zaprojektowano pod kątem szybkości, łatwości użycia i zaawansowanych możliwości analitycznych. Możliwości obliczeń rozproszonych w pamięci platforma Spark, że dobry wybór w przypadku algorytmów iteracyjnych używanych używane w machine learning i obliczeniach na grafach. Istnieją dwie biblioteki uczenia skalowalne maszyny, które ożywiają funkcje modelowania konsolidatorze do tego środowiska rozproszonych: Biblioteka MLlib i SparkML. Biblioteka MLlib zawiera oryginalny interfejs API, zbudowany na podstawie danych. SparkML jest nowszy pakiet, który udostępnia interfejs API wyższego poziomu utworzonych na szczycie elementy Dataframe tworzenia potoków uczenia Maszynowego. SparkML nie obsługuje jeszcze wszystkie funkcje MLlib, ale zastępuje jako standardowa platforma Spark usługi machine learning biblioteki MLlib.

Biblioteka Microsoft Machine Learning dla platformy Apache Spark jest [MMLSpark](https://github.com/Azure/mmlspark). Ta biblioteka została zaprojektowana do być analitykom danych wydajniejsze na platformie Spark, zwiększenia szybkości eksperymentowania i korzystać z technik uczenia maszynowego najnowocześniejsze metody, w tym uczenia głębokiego, w bardzo dużych zestawów danych. MMLSpark zapewnia warstwę interfejsach niskiego poziomu SparkML firmy podczas tworzenia skalowalnych modeli uczenia Maszynowego, takich jak ciągi indeksowania coercing — dane w układzie oczekiwany przez maszynę algorytmów uczenia i złożenia wektorów funkcji. Biblioteka MMLSpark upraszcza tych i innych typowych zadań przeznaczone do budowania modeli w PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) jest aktualnie najbardziej popularnych języków programowania statystyczne na całym świecie. Jest typu open source narzędzia do wizualizacji danych ze społecznością użytkowników ponad 2,5 mln i powiększania. Z jego prężnie działającą bazy użytkowników i ponad 8000 dodanych przez współautorów pakiety języka R jest prawdopodobnie wyborem dla wielu firmach, którzy potrzebują usługi machine learning. Gotowe do użycia z ogromnych zestawów danych i modeli usługi uczenie Maszynowe, można utworzyć klaster usługi HDInsight. Ta funkcja umożliwia analitykom danych i Statystyków za pomocą znajomego interfejsu języka R, która może być skalowana na żądanie za pośrednictwem HDInsight, bez konieczności konfigurowania klastra oraz konserwacji.

![Szkolenia dla prognoz dzięki programowi R server](./media/hdinsight-machine-learning-overview/r-training.png)

Węzeł krawędzi klastra zapewnia wygodne miejsce do łączenia z klastrem i do uruchamiania skryptów języka R.  Istnieje również możliwość uruchamianie skryptów języka R na węzłach klastra za pomocą programu ScaleR w usłudze Hadoop Mapreduce lub konteksty wystąpień obliczeniowych platformy Spark.

Przy użyciu usługi uczenie Maszynowe na HDInsight przy użyciu platformy Spark można zrównoleglić szkolenia w węzłach klastra za pomocą kontekstu obliczeniowego aparatu Spark. Uruchamianie skryptów języka R bezpośrednio w węźle brzegowym, przy użyciu wszystkich dostępnych rdzeni w sposób równoległy, zgodnie z potrzebami. Alternatywnie można uruchomić kod z węzłem krawędzi, otwiera Konferencję przetwarzania, które jest dystrybuowane między wszystkie węzły w klastrze. Usługi uczenie Maszynowe na HDInsight przy użyciu platformy Spark umożliwia także przekształcają funkcji z pakietów języka R typu open source, w razie potrzeby.

## <a name="azure-machine-learning-and-apache-hive"></a>Usługi Azure Machine Learning i Apache Hive

Usługa Azure Machine Learning udostępnia narzędzia do analizy predykcyjnej w modelu, a także w pełni zarządzana usługa, służących do wdrażania modeli predykcyjnych w postaci gotowych do użycia usług sieci web. Usługa Azure Machine Learning to rozwiązanie pełną analizy predykcyjnej w chmurze, która umożliwia tworzenie, testowanie, operacjonalizacja i zarządzanie modeli predykcyjnych. Wybierz z dużej biblioteki algorytmów, używać programu studio opartego na sieci web przeznaczone do budowania modeli i łatwy sposób wdrażania modelu w postaci usługi sieci web.

![Podejmowanie advanced analytics, które są dostępne dla platformy Hadoop za pomocą usługi Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

Tworzenie funkcji danych w usłudze HDInsight Hadoop dla klastra przy użyciu [zapytań programu Hive](../machine-learning/team-data-science-process/create-features-hive.md). *Inżynieria funkcji* próbuje zwiększają możliwości predykcyjnych algorytmów uczenia, tworząc funkcje z nieprzetworzone dane, które ułatwiają proces uczenia. Uruchamianie zapytań HiveQL z usługi Azure Machine Learning studio, a dostęp do danych przetworzonych w gałęzi i przechowywane w magazynie obiektów blob za pomocą [modułu importu danych](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Zestaw narzędzi usług Microsoft Cognitive

[Uczenie głębokie](https://www.microsoft.com/en-us/research/group/dltc/) to gałąź usługi machine learning korzysta z sieci neuronowych przez procesom mózgu ludzi. Wielu pracowników naukowo-badawczych Zobacz uczenia głębokiego jako obietnic podejście związane z poprawianiem sztucznej inteligencji. Przykłady uczenia głębokiego to tłumaczy mowy, systemy rozpoznawania obrazów i logikę maszyny.

Aby pomóc w dojściu pracę w uczenia głębokiego, firma Microsoft opracowała bezpłatny, łatwy w użyciu, open source [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/). Ten zestaw narzędzi jest używany przez szerokiej gamy produktów firmy Microsoft, firm na całym świecie z konieczności wdrażania uczenia głębokiego na dużą skalę i studentów zainteresowani najnowsze algorytmy i techniki.

## <a name="see-also"></a>Zobacz także

### <a name="scenarios"></a>Scenariusze

* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do przewidywania wyników kontroli żywności](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Generowanie rekomendacji filmów za pomocą Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive, jak i usługi Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive i usługi Azure Machine Learning end-to-end](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Uczenie maszynowe przy użyciu platformy Apache Spark w HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Zasoby uczenia głębokiego

* [Użyj Microsoft Cognitive Toolkit głębokie uczenie modelu przy użyciu klastra usługi HDInsight Spark](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Używanie platformy Caffe na platformie Azure HDInsight Spark na potrzeby rozproszonej uczenia głębokiego](spark/apache-spark-deep-learning-caffe.md)
* [Platformy sztucznej Inteligencji i uczenia głębokiego na maszynę wirtualną do nauki o danych (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-deep-learning-ai-frameworks.md)
