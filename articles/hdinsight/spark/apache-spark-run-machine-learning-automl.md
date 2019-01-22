---
title: Uruchamianie usługi Azure Machine Learning obciążeń przy użyciu zautomatyzowanych usługi machine learning (AutoML) na platformie Apache Spark w usłudze Azure HDInsight
description: Dowiedz się, jak uruchamiać obciążenia usługi Azure Machine Learning za pomocą automatycznych usługi machine learning (AutoML) na platformie Apache Spark w usłudze Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: howto
ms.date: 01/14/2019
ms.openlocfilehash: 58852ee5de97bef043f8c0b67d9e90cdc057912a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440231"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Uruchamianie usługi Azure Machine Learning obciążeń przy użyciu zautomatyzowanych usługi machine learning (AutoML) na platformie Apache Spark w usłudze Azure HDInsight

Usługa Azure Machine Learning to narzędzie do współpracy, obsługiwane metodą przeciągania i upuszczania, które służy do tworzenia, testowania i wdrażania rozwiązań do analizy predykcyjnej na podstawie posiadanych danych. Usługa Azure Machine Learning publikuje modele jako usługi sieci web, które mogą być łatwo używane w niestandardowych aplikacjach albo narzędziach, takich jak program Excel. Automatyczne usługi machine learning (AutoML) ułatwia tworzenie wysokiej jakości usługi machine learning modeli za pomocą inteligentnej automatyzacji i optymalizacji. AutoML decyduje o prawo algorytmu i hyper parametry używane dla typów określonego problemu.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instalowanie usługi Azure Machine Learning w klastrze usługi HDInsight

> [!Note]
> Obszar roboczy usługi uczenie Maszynowe Azure jest obecnie dostępna w następujących regionach: eastus, eastus2 i westcentralus. Klaster HDInsight należy również utworzyć w jednym z tych regionów.

Ogólne samouczki usługi Azure Machine Learning i automatyczne uczenia maszynowego, zobacz [samouczka: Utwórz swoje pierwsze eksperymentalne rozwiązanie do nauki o danych w usłudze Azure Machine Learning Studio](../../machine-learning/studio/create-experiment.md) i [samouczka: Użyj zautomatyzowanej uczenia maszynowego tworzy model regresji](../../machine-learning/service/tutorial-auto-train-models.md).
Aby zainstalować usługę Azure ml w klastrze Azure HDInsight, Uruchom akcję skryptu - [install_aml](https://commonartifacts.blob.core.windows.net/automl/install_aml.sh) — węzły główne i węzły procesu roboczego klastra HDInsight 3.6 Spark 2.3.0 (zalecane). Tę akcję skryptu, można uruchomić jako część procesu tworzenia klastra lub w istniejącym klastrze w witrynie Azure portal.

Aby uzyskać więcej informacji na temat akcji skryptu, przeczytaj [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md). Wraz z instalowania pakietów usługi Azure Machine Learning i zależności, skrypt również pliki do pobrania próbki notesu programu Jupyter (do ścieżki `HdiNotebooks/PySpark` magazynu domyślnego). Ten notes Jupyter pokazuje sposób użycia maszynie automatycznych uczenia klasyfikatora proste klasyfikacji problemu.

> [!Note]
> Pakiety usługi Azure Machine Learning są instalowane w języku Python3 conda środowisko. Zainstalowane notesu programu Jupyter powinien być uruchamiany przy użyciu jądra PySpark3.

## <a name="authentication-for-workspace"></a>Uwierzytelnianie dla obszaru roboczego

Tworzenie obszaru roboczego i przesyłanie eksperymentu wymaga tokenu uwierzytelniania. Ten token może zostać wygenerowany przy użyciu [aplikacji usługi Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). [Użytkownika usługi Azure AD](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) można również wygenerować token uwierzytelniania wymagany, jeśli nie jest włączone uwierzytelnianie wieloskładnikowe na koncie.  

Poniższy fragment kodu tworzy uwierzytelnianie tokenu przy użyciu **aplikacji usługi Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                username = '<Azure AD Application ID>',
                password = '<Azure AD Application Key>'
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

W konfiguracji uczenia automatycznych maszyny właściwość `spark_context` powinna być ustawiona dla pakietu do uruchamiania w trybie rozproszonym. Właściwość `concurrent_iterations`, czyli maksymalną liczbę iteracji, wykonywane równolegle, należy ustawić na liczbę mniejszą niż rdzeni wykonywania dla aplikacji platformy Spark.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na motywacja za uczenia maszynowego automatycznych, zobacz [modele wydania w tempie, używając firmy Microsoft automatyczne usługi machine learning!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/).
* [Projekt AutoML przez firmę Microsoft Research](https://www.microsoft.com/research/project/automl/)