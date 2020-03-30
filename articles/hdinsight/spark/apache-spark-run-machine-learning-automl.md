---
title: Uruchamianie obciążeń usługi Azure Machine Learning w programie Apache Spark w programie HDInsight
description: Dowiedz się, jak uruchamiać obciążenia usługi Azure Machine Learning za pomocą automatycznego uczenia maszynowego (AutoML) w programie Apache Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 6fc0d4cfe29e0fb189c44b307576bd08d2da8a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638888"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Uruchamianie obciążeń usługi Azure Machine Learning za pomocą zautomatyzowanego uczenia maszynowego w programie Apache Spark w usłudze HDInsight

Usługa Azure Machine Learning upraszcza i przyspiesza tworzenie, szkolenie i wdrażanie modeli uczenia maszynowego. W zautomatyzowanym uczeniu maszynowym (AutoML) możesz zacząć od danych szkoleniowych, które mają zdefiniowaną funkcję docelową, a następnie iterować za pomocą kombinacji algorytmów i opcji, aby automatycznie wybrać najlepszy model danych na podstawie wyników szkolenia. Usługa HDInsight umożliwia klientom aprowizowanie klastrów za pomocą setek węzłów. Automl uruchomiony w programie Spark w klastrze HDInsight umożliwia użytkownikom korzystanie z pojemności obliczeniowej w tych węzłach do uruchamiania zadań szkoleniowych w sposób skalowany w poziomie i równokolniejszego uruchamiania wielu zadań szkoleniowych. Dzięki temu użytkownicy mogą uruchamiać eksperymenty AutoML podczas udostępniania obliczeń innym obciążeniom dużych zbiorów danych.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instalowanie usługi Azure Machine Learning w klastrze usługi HDInsight

Aby uzyskać ogólne samouczki automatycznego uczenia maszynowego, zobacz [Samouczek: Tworzenie modelu regresji za pomocą zautomatyzowanego uczenia maszynowego](../../machine-learning/tutorial-auto-train-models.md).
Wszystkie nowe klastry HDInsight-Spark są fabrycznie zainstalowane z zestawem SDK azureml-automl.

> [!Note]
> Pakiety usługi Azure Machine Learning są instalowane w środowisku conda języka Python3. Zainstalowany notebook Jupyter powinien być uruchamiany przy użyciu jądra PySpark3.

Do korzystania z automl można również używać notesów Zeppelin.

> [!Note]
> Zeppelin ma [znany problem,](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) w którym PySpark3 nie wybiera odpowiedniej wersji Pythona. Proszę użyć udokumentowanego obejście.

## <a name="authentication-for-workspace"></a>Uwierzytelnianie dla obszaru roboczego

Tworzenie obszaru roboczego i przesyłanie eksperymentu wymagają tokenu uwierzytelniania. Ten token można wygenerować przy użyciu [aplikacji usługi Azure AD.](../../active-directory/develop/app-objects-and-service-principals.md) [Użytkownik usługi Azure AD](/azure/python/python-sdk-azure-authenticate) może również służyć do generowania wymaganego tokenu uwierzytelniania, jeśli uwierzytelnianie wieloskładnikowe nie jest włączone na koncie.  

Poniższy fragment kodu tworzy token uwierzytelniania przy użyciu **aplikacji usługi Azure AD.**

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

Poniższy fragment kodu tworzy token uwierzytelniania przy użyciu **użytkownika usługi Azure AD.**

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Ładowanie zestawu danych

Zautomatyzowane uczenie maszynowe w programie Spark wykorzystuje **przepływy danych,** które są leniwie oceniane, niezmienne operacje na danych.  Przepływ danych można załadować zestaw danych z obiektu blob z publicznego dostępu do odczytu lub z adresu URL obiektu blob z tokenem sygnatury dostępu współdzielonego.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Można również zarejestrować magazyn danych w obszarze roboczym przy użyciu rejestracji jednorazowej.

## <a name="experiment-submission"></a>Przesyłanie eksperymentu

W [konfiguracji automatycznego uczenia maszynowego](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)właściwość `spark_context` powinna być ustawiona dla pakietu do uruchomienia w trybie rozproszonym. Właściwość `concurrent_iterations`, która jest maksymalną liczbą iteracji wykonywanych równolegle, powinna być ustawiona na liczbę mniejszą niż rdzenie executora dla aplikacji Spark.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat motywacji automatycznego uczenia maszynowego, zobacz [Zwalnianie modeli w tempie przy użyciu zautomatyzowanego uczenia maszynowego firmy Microsoft!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Aby uzyskać więcej informacji na temat korzystania z funkcji zautomatyzowanego uczenia maszynowego usługi Azure ML, zobacz [Nowe funkcje automatycznego uczenia maszynowego w usłudze Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Projekt AutoML firmy Microsoft Research](https://www.microsoft.com/research/project/automl/)
