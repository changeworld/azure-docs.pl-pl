---
title: Uruchamianie usługi Azure Machine Learning obciążeń przy użyciu zautomatyzowanych usługi machine learning (AutoML) na platformie Apache Spark w usłudze Azure HDInsight
description: Dowiedz się, jak uruchamiać obciążenia usługi Azure Machine Learning za pomocą automatycznych usługi machine learning (AutoML) na platformie Apache Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 5135de0fc87af227073f96c653d928ace1a50fd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64917053"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Uruchamianie usługi Azure Machine Learning obciążeń przy użyciu zautomatyzowanych usługi machine learning (AutoML) na platformie Apache Spark w usłudze Azure HDInsight

Usługę Azure Machine Learning upraszcza i przyspiesza kompilowanie, szkolenia i wdrażania modeli uczenia maszynowego. Automatyczne dotyczącym uczenia maszynowego (AutoML) służy do rozpoczynać dane szkoleniowe, funkcją zdefiniowanego celu i następnie iterację kombinacje algorytmów i wybrane funkcje, aby automatycznie wybrać najlepszy model dla swoich danych, w oparciu o wyniki szkolenia. HDInsight umożliwia klientom inicjować obsługę klastrów za pomocą nawet kilkuset węzłów. AutoML działający na platformie Spark w klastrze usługi HDInsight pozwala użytkownikom na używanie możliwości obliczeniowych na tych węzłach, uruchamiać zadania szkoleń w sposób skalowalny w poziomie, a także do równoległego uruchamiania wielu zadań szkoleniowych. Pozwala to użytkownikom na uruchamianie eksperymentów AutoML, ale współużytkować zasoby obliczeniowe przy użyciu ich obciążeń dużymi ilościami danych.
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instalowanie usługi Azure Machine Learning w klastrze usługi HDInsight

Ogólne samouczki automatycznych machine Learning, są dostępne [samouczka: Użyj zautomatyzowanej uczenia maszynowego tworzy model regresji](../../machine-learning/service/tutorial-auto-train-models.md).
Wstępnie zainstalowane przy użyciu zestawu SDK usługi Azure ml AutoML pochodzą wszystkie nowe klastry HDInsight Spark. Możesz rozpocząć pracę z AutoML na HDInsight przy użyciu tego [przykładowej aplikacji Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi). Ten notes Jupyter pokazuje sposób użycia maszynie automatycznych uczenia klasyfikatora proste klasyfikacji problemu.

> [!Note]
> Pakiety usługi Azure Machine Learning są instalowane w języku Python3 conda środowisko. Zainstalowane notesu programu Jupyter powinien być uruchamiany przy użyciu jądra PySpark3.

Za korzystanie z notesów Zeppelin można też użyć AutoML także.

> [!Note]
> Ma Zeppelin [znany problem](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) gdzie PySpark3 nie wybrać właściwą wersję języka Python. Użyj udokumentowanego obejścia.

## <a name="authentication-for-workspace"></a>Uwierzytelnianie dla obszaru roboczego

Tworzenie obszaru roboczego i przesyłanie eksperymentu wymaga tokenu uwierzytelniania. Ten token może zostać wygenerowany przy użyciu [aplikacji usługi Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). [Użytkownika usługi Azure AD](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) można również wygenerować token uwierzytelniania wymagany, jeśli nie jest włączone uwierzytelnianie wieloskładnikowe na koncie.  

Poniższy fragment kodu tworzy uwierzytelnianie tokenu przy użyciu **aplikacji usługi Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                service_principal_id = '<Azure AD Application ID>',
                service_principal_password = '<Azure AD Application Key>'
                )
```
Poniższy fragment kodu tworzy uwierzytelnianie tokenu przy użyciu **użytkownika usługi Azure AD**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com','my_smart_password')
```

## <a name="loading-dataset"></a>Trwa ładowanie zestawu danych

Automatyczne machine learning na Spark korzysta **przepływów danych**, które są niezmienne opóźnieniem ocenianą operacji na danych.  Przepływ danych można załadować zestawu danych z obiektu blob przy użyciu publicznego dostępu do odczytu lub adresu URL obiektu blob przy użyciu tokenu sygnatury dostępu Współdzielonego.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Należy również zarejestrować magazynu danych z obszarem roboczym przy użyciu jednorazowego rejestracji.

## <a name="experiment-submission"></a>Przesyłanie eksperymentu

W [konfiguracji uczenia automatycznych maszyny](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig), właściwość `spark_context` powinna być ustawiona dla pakietu do uruchamiania w trybie rozproszonym. Właściwość `concurrent_iterations`, czyli maksymalną liczbę iteracji, wykonywane równolegle, należy ustawić na liczbę mniejszą niż rdzeni wykonywania dla aplikacji platformy Spark.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na motywacja za uczenia maszynowego automatycznych, zobacz [modele wydania w tempie, używając firmy Microsoft automatyczne usługi machine learning!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Aby uzyskać więcej informacji na temat korzystania z możliwości usług ML automatycznych uczenie Maszynowe Azure, zobacz [nowe zautomatyzowane funkcje uczenia maszynowego w usłudze Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Projekt AutoML przez firmę Microsoft Research](https://www.microsoft.com/research/project/automl/)
