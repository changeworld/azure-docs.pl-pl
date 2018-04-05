---
title: Co to jest usługa Azure Machine Learning? | Microsoft Docs
description: Omówienie usług Eksperymentowanie i Zarządzanie modelami w usłudze Azure Machine Learning — zintegrowanych, kompleksowych rozwiązań do nauki o danych, przeznaczonych dla profesjonalnych analityków zajmujących się opracowywaniem, testowaniem i wdrażaniem zaawansowanych aplikacji analitycznych na skalę chmury.
services: machine-learning
author: haining
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: overview
ms.date: 03/28/2018
ms.openlocfilehash: 1c4826959bb92e2e515867261094b1154a3c805c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="machine-learning-solutions-from-microsoft"></a>Rozwiązania uczenia maszynowego firmy Microsoft

Oprócz usługi [Azure Machine Learning](overview-what-is-azure-ml.md) firma Microsoft udostępnia wiele różnych opcji umożliwiających tworzenie i wdrażania modeli uczenia maszynowego oraz zarządzanie nimi. 
* SQL Server Machine Learning Services
* Serwer Microsoft Machine Learning
* Usługi Azure Machine Learning
* Azure Machine Learning Studio
* Maszyna wirtualna do analizy danych
* Biblioteka MLLib platformy Spark w usłudze HDInsight
* Usługa szkolenia sztucznej inteligencji usługi Batch
* Zestaw narzędzi usług Microsoft Cognitive
* Usługi Microsoft Cognitive Services


## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning Services
[Usługi Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) pozwalają uruchamiać, uczyć i wdrażać modele uczenia maszynowego przy użyciu języka R lub Python. Można używać danych znajdujących się w sieci lokalnej lub w bazach danych programu SQL Server. 

Usługi Microsoft Machine Learning służą do uczenia lub wdrażania modeli lokalnych lub znajdujących się w programie Microsoft SQL Server. Modele utworzone przy użyciu usług Machine Learning można wdrażać przy użyciu usługi Zarządzanie modelami w usłudze Azure Machine Learning. 

## <a name="microsoft-machine-learning-server"></a>Serwer Microsoft Machine Learning 
Przeznaczony dla przedsiębiorstw [serwer Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) umożliwia hostowanie równoległych i rozproszonych obciążeń procesów języków R i Python oraz zarządzanie nimi. Serwer Microsoft Machine Learning działa w systemach Linux i Windows oraz na platformach Hadoop i Apache Spark. Dostępny jest również w usłudze [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Udostępnia on aparat wykonywania dla rozwiązań utworzonych za pomocą [pakietów usługi Microsoft Machine Learning](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) i rozszerza możliwości korzystania z języków open source R i Python dzięki obsłudze następujących scenariuszy:

- Analizy o wysokiej wydajności
- Analizy statystyczne
- Uczenie maszynowe
- Bardzo duże zestawy danych

Funkcje o wartości dodanej są udostępniane za pośrednictwem zastrzeżonych pakietów, instalowanych razem z serwerem. Programowanie odbywa się w środowiskach IDE, takich jak [R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/) i [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/).

Serwer Microsoft Machine Learning umożliwia:

- Tworzenie i wdrażanie na serwerze modeli opracowanych za pomocą języków R i Python
- Masowe rozpowszechnianie szkoleń opracowanych za pomocą języków R i Python w klastrach usługi Hadoop lub Spark

## <a name="data-science-virtual-machine"></a>Maszyna wirtualna do analizy danych
[Maszyna wirtualna do analizy danych](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) to niestandardowy obraz maszyny wirtualnej znajdujący się w chmurze Microsoft Azure, przeznaczony do wykonywania analiz danych. Zawiera ona wiele wstępnie zainstalowanych i skonfigurowanych, popularnych narzędzi do analizy danych, które pozwalają szybko rozpocząć tworzenie inteligentnych, zaawansowanych aplikacji analitycznych. Jest dostępna w systemach Windows Server i Linux. Oferujemy wersje maszyny wirtualnej dla systemów Windows Server 2016 i Windows Server 2012. Wersje dla systemu Linux są dostępne w systemie Ubuntu 16.04 LTS i w dystrybucjach OpenLogic 7.2 CentOS. 

Maszyna wirtualna do analizy danych pozwala uruchamiać lub hostować zadania w jednym węźle. Można również jej użyć, gdy trzeba zdalnie skalować w górę przetwarzanie na jednej maszynie. Zarówno usługa Eksperymentowanie w usłudze Azure Machine Learning, jak i usługa Zarządzanie modelami w usłudze Azure Machine Learning obsługują maszynę wirtualną do analizy danych jako obiekt docelowy. 

## <a name="spark-mllib-in-hdinsight"></a>Biblioteka MLLib platformy Spark w usłudze HDInsight
[Biblioteka MLLib platformy Spark w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) umożliwia tworzenie modeli w ramach zadań platformy Spark wykonywanych na danych big data. Platforma Spark pozwala łatwo przekształcać i przygotowywać dane, a następnie skalować w poziomie tworzenie modelu w jednym zadaniu. Modele utworzone za pomocą biblioteki MLLib platformy Spark można wdrażać i monitorować oraz zarządzać nimi za pośrednictwem usługi Zarządzanie modelami w usłudze Azure Machine Learning. Wysyłanie przebiegów szkoleniowych i zarządzanie nimi może odbywać się za pomocą usługi Eksperymentowanie w usłudze Azure Machine Learning. Platformy Spark można również używać do skalowania w poziomie zadań przygotowywania danych utworzonych w środowisku roboczym usługi Machine Learning. 

Platforma Spark pozwala skalować w poziomie przetwarzanie danych i tworzyć modele w ramach potoku danych. Zadania Spark można tworzyć w językach Scala, Java, Python i R. 

## <a name="batch-ai-training"></a>Szkolenie sztucznej inteligencji usługi Batch 
[Szkolenie sztucznej inteligencji usługi Azure Batch](https://aka.ms/batchaitraining) ułatwia przeprowadzanie eksperymentów równoległych z modelami sztucznej inteligencji za pomocą dowolnej architektury, a następnie szkolenie tych modeli na dużą skalę na procesorach GPU znajdujących się w klastrze. Wystarczy, że opiszesz wymagania zadania i konfigurację do uruchomienia, a my zajmiemy się resztą. 

Szkolenie sztucznej inteligencji usługi Batch pozwala skalować w poziomie zadania uczenia głębokiego na procesorach GPU znajdujących się w klastrze. Obsługiwane są następujące platformy:

- Zestaw narzędzi usług Cognitive
- Caffe
- Chainer
- TensorFlow

Zarządzania modelami w usłudze Azure Machine Learning można użyć do pobrania modeli z modułu szkolenia sztucznej inteligencji usługi Batch w celu ich wdrożenia i monitorowania oraz zarządzania nimi.  Szkolenie sztucznej inteligencji usługi Batch zostanie w przyszłości zintegrowane z usługą Eksperymentowanie w usłudze Azure Machine Learning. 

## <a name="microsoft-cognitive-toolkit"></a>Zestaw narzędzi usług Microsoft Cognitive
Ujednolicony [zestaw narzędzi usług Microsoft Cognitive](https://www.microsoft.com/en-us/cognitive-toolkit/) do uczenia głębokiego umożliwia opisywanie sieci neuronowych jako etapów obliczeniowych na grafie skierowanym. Węzły liści na grafie skierowanym reprezentują wartości wejściowe lub parametry sieci, a inne węzły przedstawiają operacje na macierzach danych wejściowych. Zestaw narzędzi usług Cognitive pozwala łatwo realizować i łączyć popularne typy modeli, takie jak jednokierunkowe sieci DNN, sieci splotowe CNN oraz sieci rekurencyjne RNN i LSTM. Implementuje on algorytm uczenia stochastycznego spadku gradientu (wstecznej propagacji błędu) z automatycznym różnicowaniem i przetwarzaniem równoległym na różnych procesorach GPU i serwerach.

Zestaw narzędzi usług Cognitive pozwala tworzyć modele korzystające z uczenia głębokiego.  Można go używać razem z dowolną z wcześniej wymienionych usług.

## <a name="microsoft-cognitive-services"></a>Usługi Microsoft Cognitive Services
Usługi Microsoft Cognitive Services to zestaw 30 interfejsów API, które pozwalają tworzyć aplikacje korzystające z naturalnych metod komunikacji. Te interfejsy API pozwalają aplikacjom dostrzegać, słyszeć, wypowiadać, rozumieć i interpretować potrzeby użytkowników — wystarczy tylko kilka wierszy kodu. Można łatwo dodawać do aplikacji inteligentne funkcje, takie jak: 

- Wykrywanie emocji i tonacji
- Rozpoznawanie obrazów i mowy
- Rozumienia języka
- Wiedza i wyszukiwanie

Usług Microsoft Cognitive Services można używać do opracowywania aplikacji przeznaczonych dla różnych urządzeń i platform. Interfejsy API są stale udoskonalane i łatwe do skonfigurowania. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z omówieniem usługi [Azure Machine Learning](overview-what-is-azure-ml.md).