---
title: Jak używać uczenia maszynowego MMLSpark | Dokumentacja firmy Microsoft
description: Przewodnik dotyczący sposobu korzystanie z biblioteki MMLSpark w usłudze Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: 654b2559518cd52978153310fbb1e89a91838a8a
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649418"
---
# <a name="how-to-use-microsoft-machine-learning-library-for-apache-spark"></a>Jak używać usługi Microsoft Machine Learning Library dla platformy Apache Spark

## <a name="introduction"></a>Wprowadzenie

[Biblioteka uczenia usługi Microsoft Machine dla platformy Apache Spark](https://github.com/Azure/mmlspark) (MMLSpark) zawiera narzędzia, które pozwalają na łatwe tworzenie modeli uczenia maszynowego skalowalne do dużych zestawów danych. Obejmuje integrację SparkML potoków przy użyciu [Microsoft Cognitive Toolkit ](https://github.com/Microsoft/CNTK) i [OpenCV](http://www.opencv.org/), dzięki któremu można: 
 * Ruch przychodzący i dane obrazu przetwarzania wstępnego
 * Cechowanie obrazów i tekstu przy użyciu wstępnie przeszkolonych modeli uczenia głębokiego
 * Szkolenie i ocenianie modeli klasyfikacji i regresji przy użyciu niejawnego cechowania.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków w tym przewodniku, musisz:
- [Instalowanie aplikacji Azure Machine Learning Workbench](../service/quickstart-installation.md)
- [Konfigurowanie klastra usługi HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)

## <a name="run-your-experiment-in-docker-container"></a>Uruchamianie eksperymentu w kontenerze platformy Docker

Usługi Azure Machine Learning Workbench jest skonfigurowany do używania MMLSpark, podczas uruchamiania eksperymentów w kontenerze platformy Docker, lokalnie lub na zdalnej maszynie Wirtualnej. Ta funkcja pozwala na łatwe debugowanie i eksperymentować z modelami PySpark przed uruchomieniem je na dużą skalę na klastrze. 

Aby rozpocząć pracę, przy użyciu przykładu, Utwórz nowy projekt, a następnie wybierz przykładu z galerii "MMLSpark w treści dla dorosłych spisu". Wybierz "Docker" kontekstu obliczeniowego na pulpicie nawigacyjnym projektu, a następnie kliknij pozycję "Uruchom". Usługa Azure Machine Learning Workbench tworzy kontener platformy Docker, aby uruchomić PySpark program, a następnie uruchamia program.

Po zakończeniu uruchomienia wyniki można wyświetlić w widoku historii uruchamiania w aplikacji Azure Machine Learning Workbench.

## <a name="install-mmlspark-on-azure-hdinsight-spark-cluster"></a>Zainstaluj MMLSpark w klastrze usługi Azure HDInsight Spark.

Aby wykonać to i następujący krok, trzeba wcześniej [Utwórz klaster usługi HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql).

Domyślnie program Azure Machine Learning Workbench instaluje pakiet MMLSpark w klastrze, po uruchomieniu eksperymentu. Można kontrolować to zachowanie i zainstalować inne pakiety platformy Spark, edytując plik o nazwie _aml_config/spark_dependencies.yml_ w folderze projektu.

```
# Spark configuration properties.
configuration:
  "spark.app.name": "Azure ML Experiment"
  "spark.yarn.maxAppAttempts": 1

repositories:
  - "https://mmlspark.azureedge.net/maven"
packages:
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.9.9"
```

Możesz także zainstalować MMLSpark bezpośrednio na użyciu klastra platformy HDInsight Spark [akcji skryptu](https://github.com/Azure/mmlspark#hdinsight).

## <a name="set-up-azure-hdinsight-spark-cluster-as-compute-target"></a>Konfigurowanie klastra Spark usługi Azure HDInsight, jako cel obliczenia

Otwórz okno interfejsu wiersza polecenia z aplikacji Azure Machine Learning Workbench, przechodząc do Menu "File", a następnie kliknij przycisk "Otwórz wiersz polecenia"

W oknie interfejsu wiersza polecenia Uruchom następujące polecenia:

```
az ml computetarget attach cluster --name <myhdi> --address <myhdi-ssh.azurehdinsight.net> --username <sshusername> --password <sshpwd> 
```

```
az ml experiment prepare -c <myhdi>
```

Teraz klastra jest dostępna, ponieważ obliczeniowego elementu docelowego projektu.

## <a name="run-experiment-on-azure-hdinsight-spark-cluster"></a>Uruchom eksperyment w klastrze usługi HDInsight Spark.

Wróć do pulpitu nawigacyjnego projekt przykładu "MMLSpark w treści dla dorosłych spisu". Wybierz klaster jako obliczeniowego elementu docelowego, a następnie kliknij przycisk Uruchom.

Usługa Azure Machine Learning Workbench przesyła zadanie spark w klastrze. Można monitorować postęp i wyświetlić wyniki w widoku historii uruchamiania.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje na temat Biblioteka MMLSpark i przykłady, zobacz [repozytorium MMLSpark GitHub](https://github.com/Azure/mmlspark)

*Apache®, Apache Spark i Spark® są zastrzeżonymi znakami towarowymi lub znakami towarowymi Apache Software Foundation w Stanach Zjednoczonych i/lub innych krajach.*
