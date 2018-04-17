---
title: Inne machine learning produktów firmy Microsoft — usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Oprócz Azure Machine Learning istnieją różne opcje w firmie Microsoft na tworzenie, wdrażanie i zarządzanie nimi z modeli uczenia maszynowego.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/11/2018
ms.openlocfilehash: 189f19a3a3f588df8844a552abda73827eb45191
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="other-machine-learning-products-and-services-from-microsoft"></a>Inne machine learning produktów i usług firmy Microsoft

Oprócz usługi [Azure Machine Learning](overview-what-is-azure-ml.md) firma Microsoft udostępnia wiele różnych opcji umożliwiających tworzenie i wdrażania modeli uczenia maszynowego oraz zarządzanie nimi. 
* SQL Server Machine Learning Services
* Serwer Microsoft Machine Learning
* Maszyna wirtualna nauki danych Azure
* Biblioteka MLLib platformy Spark w usłudze HDInsight
* Usługa szkolenia sztucznej inteligencji usługi Batch
* Kognitywnych zestaw narzędzi firmy Microsoft (CNTK)
* Kognitywnych usług platformy Azure


## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning Services
[Usług SQL Server Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) pozwala uruchamiać, uczenie i wdrażanie modeli uczenia komputera przy użyciu R lub Python. Można używać danych znajdujących się w sieci lokalnej lub w bazach danych programu SQL Server. 

Usługi Microsoft Machine Learning służą do uczenia lub wdrażania modeli lokalnych lub znajdujących się w programie Microsoft SQL Server. Modele utworzone przy użyciu usług Machine Learning można wdrażać przy użyciu usługi Zarządzanie modelami w usłudze Azure Machine Learning. 

## <a name="microsoft-machine-learning-server"></a>Serwer Microsoft Machine Learning 
Przeznaczony dla przedsiębiorstw [serwer Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) umożliwia hostowanie równoległych i rozproszonych obciążeń procesów języków R i Python oraz zarządzanie nimi. Serwer Microsoft Machine Learning działa w systemach Linux i Windows oraz na platformach Hadoop i Apache Spark. Dostępny jest również w usłudze [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Zapewnia aparat wykonywania dla rozwiązań utworzonych za pomocą [pakiety uczenia maszynowego Microsoft](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package), i rozszerza R i Python open source z obsługą w następujących scenariuszach:

- Analizy o wysokiej wydajności
- Analizy statystyczne
- Uczenie maszynowe
- Bardzo duże zestawy danych

Dodatkowe funkcje jest zapewniana za pomocą zastrzeżonych pakiety, które zainstalować na serwerze. Programowanie odbywa się w środowiskach IDE, takich jak [R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/) i [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/).

Serwer Microsoft Machine Learning umożliwia:

- Tworzenie i wdrażanie na serwerze modeli opracowanych za pomocą języków R i Python
- Masowe rozpowszechnianie szkoleń opracowanych za pomocą języków R i Python w klastrach usługi Hadoop lub Spark

## <a name="azure-data-science-virtual-machine"></a>Maszyna wirtualna nauki danych Azure
[Maszyny wirtualnej nauki danych](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) to środowisko dostosowane maszyny wirtualnej w chmurze Microsoft Azure opracowane pod kątem wykonywania analizy danych. Zawiera ona wiele wstępnie zainstalowanych i skonfigurowanych, popularnych narzędzi do analizy danych, które pozwalają szybko rozpocząć tworzenie inteligentnych, zaawansowanych aplikacji analitycznych. Maszyna wirtualna nauki danych jest dostępne w wersjach systemu Windows Server 2016 i 2012 i w Maszynę wirtualną systemu Linux na Ubuntu 16.04 LTS i na podstawie OpenLogic CentOS 7.4 dystrybucji. 

Maszyna wirtualna nauki danych należy używać wtedy, gdy konieczne do uruchamiania lub hosta zadań w jednym węźle. Można również jej użyć, gdy trzeba zdalnie skalować w górę przetwarzanie na jednej maszynie. Zarówno usługa Eksperymentowanie w usłudze Azure Machine Learning, jak i usługa Zarządzanie modelami w usłudze Azure Machine Learning obsługują maszynę wirtualną do analizy danych jako obiekt docelowy. 

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

## <a name="microsoft-cognitive-toolkit-cntk"></a>Kognitywnych zestaw narzędzi firmy Microsoft (CNTK)
Ujednolicony [zestaw narzędzi usług Microsoft Cognitive](https://www.microsoft.com/en-us/cognitive-toolkit/) do uczenia głębokiego umożliwia opisywanie sieci neuronowych jako etapów obliczeniowych na grafie skierowanym. Węzły liści na grafie skierowanym reprezentują wartości wejściowe lub parametry sieci, a inne węzły przedstawiają operacje na macierzach danych wejściowych. Zestaw narzędzi usług Cognitive pozwala łatwo realizować i łączyć popularne typy modeli, takie jak jednokierunkowe sieci DNN, sieci splotowe CNN oraz sieci rekurencyjne RNN i LSTM. Implementuje on algorytm uczenia stochastycznego spadku gradientu (wstecznej propagacji błędu) z automatycznym różnicowaniem i przetwarzaniem równoległym na różnych procesorach GPU i serwerach.

Zestaw narzędzi usług Cognitive pozwala tworzyć modele korzystające z uczenia głębokiego.  Można go używać razem z dowolną z wcześniej wymienionych usług.

## <a name="azure-cognitive-services"></a>Kognitywnych usług platformy Azure
[Usług Azure kognitywnych](https://docs.microsoft.com/en-us/azure/#pivot=products&panel=ai) to zbiór około 30 interfejsów API, które pozwalają tworzyć aplikacje korzystające z fizyczną metod komunikacji. Te interfejsy API umożliwiają Zobacz, usłyszeć, głosu, zrozumieć i interpretować potrzeb użytkownika przy użyciu kilku wierszy kodu aplikacji. Można łatwo dodawać do aplikacji inteligentne funkcje, takie jak: 

- Wykrywanie emocji i tonacji
- Rozpoznawanie obrazów i mowy
- Opis języka (LUIS)
- Wiedza i wyszukiwanie

Kognitywnych usług może służyć do opracowywania aplikacji między urządzeniami i platformami. Interfejsy API są stale udoskonalane i łatwe do skonfigurowania. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z omówieniem usługi [Azure Machine Learning](overview-what-is-azure-ml.md).