---
title: Głębokie nurkowanie - zaawansowana analityka — Usługa Azure HDInsight
description: Dowiedz się, jak zaawansowana analityka wykorzystuje algorytmy do przetwarzania dużych zbiorów danych w usłudze Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 6fc6ea9ca0ce3bf82f027b964db94df50f07f2bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646527"
---
# <a name="deep-dive---advanced-analytics"></a>Głębokie nurkowanie - zaawansowana analityka

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Co to jest zaawansowana analiza hdinsight?

Usługa HDInsight umożliwia uzyskanie cennych informacji z dużych ilości danych strukturalnych, nieustrukturyzowanych i szybko poruszających się. Zaawansowana analiza to wykorzystanie wysoce skalowalnych architektur, modeli uczenia statystycznego i uczenia maszynowego oraz inteligentnych pulpitów nawigacyjnych, aby zapewnić ci znaczące informacje. Uczenie maszynowe lub *analiza predykcyjna*wykorzystuje algorytmy identyfikujące i wyciągające wnioski z relacji w danych do przewidywania i kierowania decyzjami.

## <a name="advanced-analytics-process"></a>Zaawansowany proces analizy

![Zaawansowany przepływ procesów analitycznych](./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png)

Po zidentyfikowaniu problemu biznesowego i rozpoczęciu zbierania i przetwarzania danych należy utworzyć model reprezentujący pytanie, które chcesz przewidzieć. Model użyje co najmniej jednego algorytmu uczenia maszynowego, aby typ prognozowania, który najlepiej odpowiada Twoim potrzebom biznesowym.  Większość danych powinny być używane do szkolenia modelu, a reszta służy do testowania lub oceny.

Po utworzeniu, załadowaniu, przetestowaniu i ocenie modelu następnym krokiem jest wdrożenie modelu, tak aby rozpoczynał dostarczanie odpowiedzi na pytania. Ostatnim krokiem jest monitorowanie wydajności modelu i dostrojenie go w razie potrzeby.

## <a name="common-types-of-algorithms"></a>Typowe typy algorytmów

Zaawansowane rozwiązania analityczne zapewniają zestaw algorytmów uczenia maszynowego. Oto podsumowanie kategorii algorytmów i powiązanych typowych przypadków użycia biznesowego.

![Podsumowania kategorii uczenia maszynowego](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png)

Wraz z wyborem najlepiej dopasowanych algorytmów należy rozważyć, czy musisz podać dane do szkolenia. Algorytmy uczenia maszynowego są podzielone na następujące kategorie:

* Nadzorowany — algorytm musi być przeszkolony na podstawie zestawu danych oznaczonych etykietą, zanim będzie mógł dostarczyć wyników
* Półnadzorowany - algorytm może być rozszerzony o dodatkowe cele poprzez interaktywne zapytanie trenera, które nie były dostępne podczas początkowego etapu szkolenia
* Bez nadzoru - algorytm nie wymaga danych szkoleniowych
* Wzmocnienie - algorytm wykorzystuje agentów oprogramowania do określenia idealnego zachowania w określonym kontekście (często używanym w robotyce)

| Kategoria algorytmu| Użycie | Typ uczenia się | Algorytmy |
| --- | --- | --- | -- |
| Klasyfikacja | Klasyfikowanie osób lub rzeczy do grup | Nadzorowane | Drzewa decyzyjne, regresja logistyczna, sieci neuronowe |
| Klastrowanie | Dzielenie zestawu przykładów na jednorodne grupy | Bez nadzoru | K oznacza klastrowanie |
| Wykrywanie wzorków | Identyfikowanie częstych skojarzeń w danych | Bez nadzoru | Association rules |
| Regresja | Przewidywanie wyników liczbowych | Nadzorowane | Regresja liniowa, sieci neuronowe |
| Wzmocnienie (Reinforcement) | Określanie optymalnego zachowania robotów | Wzmocnienie (Reinforcement) | Symulacje Monte Carlo, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Uczenie maszynowe w programie HDInsight

HdInsight ma kilka opcji uczenia maszynowego dla zaawansowanego przepływu pracy analizy:

* Uczenie maszynowe i iskra Apache
* Usługi R i ML
* Usługa Azure Machine Learning i gałąź Apache
* Apache Spark i głębokie uczenie się

### <a name="machine-learning-and-apache-spark"></a>Uczenie maszynowe i iskra Apache

[HDInsight Spark](../spark/apache-spark-overview.md) to hostowana na platformie Azure oferta [Apache Spark](https://spark.apache.org/), ujednoliconej, równoległej struktury przetwarzania danych typu open source, która wykorzystuje przetwarzanie w pamięci w celu zwiększenia analizy dużych zbiorów danych. Silnik przetwarzania Spark został stworzony z myślą o szybkości, łatwości obsługi i zaawansowanej analizie. Rozproszone możliwości obliczeniowe firmy Spark w pamięci sprawiają, że jest to dobry wybór dla algorytmów iteracyjnych używanych w uczeniu maszynowym i obliczeniach wykresów.

Istnieją trzy skalowalne biblioteki uczenia maszynowego, które zapewniają możliwości modelowania algorytmicznego w tym rozproszonym środowisku:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) - MLlib zawiera oryginalny interfejs API zbudowany na podstawie spark RDDs.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html) — SparkML to nowszy pakiet, który zapewnia interfejs API wyższego poziomu utworzony na podstawie ram danych platformy Spark do konstruowania potoków ml.
* [**MMLSpark**](https://github.com/Azure/mmlspark) — biblioteka uczenia maszynowego firmy Microsoft dla platformy Apache Spark (MMLSpark) została zaprojektowana w taki sposób, aby naukowcy zajmujący się danymi wydajniejsi w programie Spark, zwiększyć szybkość eksperymentów i wykorzystać najnowocześniejsze techniki uczenia maszynowego, w tym uczenie głębokie, na bardzo dużych zestawach danych. Biblioteka MMLSpark upraszcza typowe zadania modelowania do tworzenia modeli w pyspark.

### <a name="r-and-ml-services"></a>Usługi R i ML

W ramach usługi HDInsight można utworzyć klaster HDInsight z [usługami ML](../r-server/r-server-overview.md) gotowymi do użycia z ogromnymi zestawami danych i modelami. Ta nowa funkcja zapewnia analityków danych i statystyków ze znanym interfejsem języka R, który można skalować na żądanie za pośrednictwem usługi HDInsight, bez konieczności konfigurowania i konserwacji klastra.

### <a name="azure-machine-learning-and-apache-hive"></a>Usługa Azure Machine Learning i gałąź Apache

[Usługa Azure Machine Learning Studio (klasyczna)](https://studio.azureml.net/) udostępnia narzędzia do modelowania analizy predykcyjnej oraz w pełni zarządzaną usługę, której można użyć do wdrażania modeli predykcyjnych jako gotowych do użycia usług sieci web. Usługa Azure Machine Learning udostępnia narzędzia do tworzenia kompletnych rozwiązań do analizy predykcyjnej w chmurze w celu szybkiego tworzenia, testowania, operacjonalizacji i zarządzania modelami predykcyjnymi. Wybierz z dużej biblioteki algorytmów, użyj studio oparte na sieci Web do tworzenia modeli i łatwo wdrożyć model jako usługę sieci web.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark i głębokie uczenie się

[Głębokie uczenie się](https://www.microsoft.com/research/group/dltc/) jest gałęzią uczenia maszynowego, która wykorzystuje *głębokie sieci neuronowe* (DNN), inspirowane biologicznymi procesami ludzkiego mózgu. Wielu badaczy postrzega głębokie uczenie się jako obiecujące podejście do sztucznej inteligencji. Niektóre przykłady uczenia głębokiego to tłumacze języka mówionego, systemy rozpoznawania obrazów i rozumowanie maszynowe. Aby pomóc w rozwijaniu własnej pracy w zakresie głębokiego uczenia się, firma Microsoft opracowała bezpłatny, łatwy w użyciu zestaw [narzędzi Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)typu open source. Zestaw narzędzi jest szeroko stosowany przez szeroką gamę produktów firmy Microsoft, przez firmy na całym świecie, które muszą wdrażać głębokie uczenie na dużą skalę, oraz przez studentów zainteresowanych najnowszymi algorytmami i technikami.

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scenariusz - Ocena obrazów do identyfikacji wzorców w rozwoju miast

Przejrzyjmy przykład potoku zaawansowanego uczenia maszynowego analizy przy użyciu usługi HDInsight.

W tym scenariuszu zobaczysz, jak nazwy DNN produkowane w ramach uczenia głębokiego, Cognitive Toolkit firmy Microsoft (CNTK), można zużyć do oceniania dużych kolekcji obrazów przechowywanych na koncie usługi Azure Blob Storage przy użyciu pyspark w klastrze platformy SPARK usługi HDInsight. Podejście to jest stosowane do wspólnego przypadku użycia DNN, klasyfikacji zdjęć lotniczych i może być wykorzystane do identyfikacji ostatnich wzorców w rozwoju miast.  Użyjesz wstępnie przeszkolonego modelu klasyfikacji obrazów. Model jest wstępnie przeszkolony w [zestawie danych CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html) i został zastosowany do 10 000 wstrzymanych obrazów.

W tym scenariuszu zaawansowanej analizy istnieją trzy kluczowe zadania:

1. Utwórz klaster usługi Azure HDInsight Hadoop z dystrybucją Apache Spark 2.1.0.
2. Uruchom skrypt niestandardowy, aby zainstalować zestaw narzędzi Microsoft Cognitive Toolkit we wszystkich węzłach klastra platformy Azure HDInsight Spark.
3. Przekaż wstępnie utworzony notes Jupyter do klastra platformy SPARK usługi HDInsight, aby zastosować przeszkolony model uczenia głębokiego microsoft Cognitive Toolkit do plików na koncie usługi Azure Blob Storage przy użyciu interfejsu API Języka Spark Python (PySpark).

W tym przykładzie użyto zestawu obrazów CIFAR-10 skompilowanego i dystrybuowanego przez Alexa Krizhevsky'ego, Vinoda Naira i Geoffreya Hintona. Zestaw danych CIFAR-10 zawiera 60 000 kolorowych obrazów 32×32 należących do 10 wzajemnie wykluczanych klas:

![Przykładowe obrazy uczenia maszynowego](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png)

Aby uzyskać więcej informacji na temat zestawu danych, zobacz Alex Krizhevsky's [Learning Multiple Layers of Features from Tiny Images](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Zestaw danych został podzielony na zestaw szkoleniowy 50 000 obrazów i zestaw testowy 10 000 obrazów. Pierwszy zestaw został użyty do szkolenia dwudziestoligowarstwowego modelu splotowej sieci resztkowej (ResNet) przy użyciu zestawu narzędzi Microsoft Cognitive Toolkit, wykonując [ten samouczek](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) z repozytorium Cognitive Toolkit GitHub. Pozostałe 10 000 obrazów zostało wykorzystanych do przetestowania dokładności modelu. W tym miejscu w grę wchodzi przetwarzanie rozproszone: zadanie wstępnego przetwarzania i oceniania obrazów jest wysoce równoległe. Z zapisanym wyszkolonym modelem w ręku użyliśmy:

* PySpark do dystrybucji obrazów i przeszkolony model do węzłów procesu roboczego klastra.
* Python do wstępnego przetwarzania obrazów w każdym węźle klastra platformy SPARK usługi HDInsight.
* Cognitive Toolkit, aby załadować model i zdobyć wstępnie przetworzone obrazy w każdym węźle.
* Notesy Jupyter do uruchamiania skryptu PySpark, agregowania wyników i [używania matplotlib](https://matplotlib.org/) do wizualizacji wydajności modelu.

Całe przetwarzanie wstępne/ocenianie 10 000 obrazów zajmuje mniej niż jedną minutę w klastrze z 4 węzłami procesu roboczego. Model dokładnie przewiduje etykiety ~9100 (91%) Obrazów. Macierz pomyłek ilustruje najczęstsze błędy klasyfikacji. Na przykład matryca pokazuje, że błędne oznaczenie psów jako kotów i odwrotnie występuje częściej niż w przypadku innych par etykiet.

![Wykres wyników uczenia maszynowego](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png)

### <a name="try-it-out"></a>Wypróbuj!

Wykonaj [ten samouczek,](../spark/apache-spark-microsoft-cognitive-toolkit.md) aby zaimplementować to rozwiązanie end-to-end: skonfiguruj klaster platformy SPARK usługi HDInsight, zainstaluj zestaw narzędzi Cognitive Toolkit i uruchom notes Jupyter, który ocenia 10 000 obrazów CIFAR.

## <a name="next-steps"></a>Następne kroki

Ule Apache i uczenie maszynowe platformy Azure

* [Apache Hive i azure machine learning end-to-end](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Korzystanie z klastra programu Azure HDInsight Hadoop w zestawie danych o pojemności 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark i MLLib

* [Uczenie maszynowe z Apache Spark na HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark z uczeniem maszynowym: Użyj apache Spark w HDInsight do analizowania temperatury budynku przy użyciu danych HVAC](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z uczeniem maszynowym: użyj platformy Spark Apache w hdinsight, aby przewidzieć wyniki inspekcji żywności](../spark/apache-spark-machine-learning-mllib-ipython.md)

Deep Learning, Cognitive Toolkit i inne

* [Maszyna wirtualna platformy Azure do nauki o danych](../../machine-learning/data-science-virtual-machine/overview.md)
* [Przedstawiamy H2O.ai w usłudze Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
