---
title: Jak używać MMLSpark Machine Learning | Dokumentacja firmy Microsoft
description: Przewodnik dotyczący sposobu korzystania z usługi Azure Machine Learning MMLSpark biblioteki.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: 3c9bcce67bb802a8596416b55ef61a51ab2900bd
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831534"
---
# <a name="how-to-use-microsoft-machine-learning-library-for-apache-spark"></a>Jak używać Microsoft Machine Learning biblioteki platformy Apache Spark

## <a name="introduction"></a>Wprowadzenie

[Machine Learning biblioteki platformy Apache Spark](https://github.com/Azure/mmlspark) (MMLSpark) zawiera narzędzia, które umożliwiają łatwe tworzenie modeli uczenia skalowalne maszyny dla dużych zestawów danych. Zawiera on integracji potoków SparkML z [kognitywnych zestaw narzędzi firmy Microsoft ](https://github.com/Microsoft/CNTK) i [OpenCV](http://www.opencv.org/), dzięki któremu można: 
 * Transfer danych przychodzących i dane wstępnego przetwarzania obrazu
 * Featurize obrazy i tekst przy użyciu wstępnie przeszkolone bezpośrednich modeli uczenia
 * Szkolenie i modele klasyfikacji i regresji przy użyciu niejawnego featurization wynik.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych ten przewodnik, musisz:
- [Zainstaluj Azure Machine Learning Workbench](../service/quickstart-installation.md)
- [Konfigurowanie klastra Spark w usłudze HDInsight Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)

## <a name="run-your-experiment-in-docker-container"></a>Uruchamianie eksperymentu w kontenerze Docker

Twoje Workbench Learning maszyny Azure jest skonfigurowany do używania MMLSpark, po uruchomieniu eksperymenty w kontenerze Docker lokalnego lub zdalnego maszyny Wirtualnej. Ta funkcja umożliwia łatwe debugowanie i eksperymentować modeli PySpark, przed uruchomieniem je na skali w klastrze. 

Aby rozpocząć korzystanie z przykładem, Utwórz nowy projekt i wybierz przykład galerii "MMLSpark na dla dorosłych spisu". Wybierz "Docker" kontekstu obliczeń na pulpicie nawigacyjnym projektu, a następnie kliknij przycisk "Uruchom". Azure Machine Learning Workbench tworzy kontener Docker, aby uruchomić PySpark program, a następnie wykonuje program.

Po zakończeniu uruchom można wyświetlić wyniki w widoku Historia uruchomień Azure Machine Learning Workbench.

## <a name="install-mmlspark-on-azure-hdinsight-spark-cluster"></a>Zainstaluj MMLSpark w klastrze Spark w usłudze HDInsight Azure.

Aby wykonać to i następujący krok, musisz najpierw [Tworzenie klastra Spark w usłudze HDInsight Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql).

Domyślnie program Azure Machine Learning Workbench instaluje pakiet MMLSpark w klastrze, uruchomienie eksperymentu. Można kontrolować to zachowanie i zainstalować inne pakiety Spark, edytując plik o nazwie _aml_config/spark_dependencies.yml_ w folderze projektu.

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

Można także zainstalować MMLSpark bezpośrednio na użyciu klastra Spark w usłudze HDInsight [akcji skryptu](https://github.com/Azure/mmlspark#hdinsight).

## <a name="set-up-azure-hdinsight-spark-cluster-as-compute-target"></a>Konfigurowanie usługi Azure HDInsight Spark klastra jako cel obliczeniowe

Otwórz okno interfejsu wiersza polecenia z usługi Azure Machine Learning Workbench, przechodząc do Menu "File" i kliknij przycisk "Otwórz okno wiersza polecenia"

W oknie interfejsu wiersza polecenia Uruchom następujące polecenia:

```
az ml computetarget attach cluster --name <myhdi> --address <myhdi-ssh.azurehdinsight.net> --username <sshusername> --password <sshpwd> 
```

```
az ml experiment prepare -c <myhdi>
```

Teraz klastra jest dostępne, ponieważ obliczeniowe celu dla projektu.

## <a name="run-experiment-on-azure-hdinsight-spark-cluster"></a>Uruchom eksperyment w klastrze usługi Azure HDInsight Spark.

Wróć do pulpitu nawigacyjnego projektu przykładu "MMLSpark na dla dorosłych spisu". Wybierz klaster jako element docelowy obliczeń, a następnie kliknij przycisk Uruchom.

Azure Machine Learning Workbench przesyła zadanie spark dla klastra. Możesz monitorować postęp i wyświetlić wyniki w widoku historii wykonywania.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje na temat MMLSpark biblioteki i przykłady, zobacz [repozytorium MMLSpark GitHub](https://github.com/Azure/mmlspark)

*Apache®, Apache Spark i Spark® są zastrzeżonymi znakami towarowymi lub znakami towarowymi Foundation oprogramowania Apache w Stanach Zjednoczonych i/lub innych krajach.*
