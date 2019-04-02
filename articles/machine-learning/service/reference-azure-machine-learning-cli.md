---
title: Rozszerzenie interfejsu wiersza polecenia do uczenia maszynowego
titleSuffix: Azure Machine Learning service
description: Dowiedz się więcej o rozszerzenie interfejsu wiersza polecenia usługi Azure Machine Learning dla wiersza polecenia platformy Azure. Wiersza polecenia platformy Azure jest narzędziem wiersza polecenia dla wielu platform, która umożliwia pracę z zasobami w chmurze platformy Azure. Rozszerzenie usługi Machine Learning umożliwia pracę z usługą Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 2504ca9cb785529a9eab321c2521db46390632b7
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793207"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Na użytek rozszerzenie interfejsu wiersza polecenia usługi Azure Machine Learning

Interfejsu wiersza polecenia usługi Azure Machine Learning to rozszerzenie [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), interfejs wiersza polecenia dla wielu platform na platformie Azure. To rozszerzenie zawiera polecenia służące do pracy z usługą Azure Machine Learning z poziomu wiersza polecenia. Umożliwia tworzenia skryptów automatyzujących przepływy pracy uczenia maszynowego. Na przykład można utworzyć skrypty, które wykonują następujące czynności:

+ Uruchamianie eksperymentów w celu tworzenia modeli uczenia maszynowego

+ Zarejestruj modele uczenia maszynowego do użycia przez klientów

+ Pakowanie, wdrażanie i śledzenia w cyklu życia modeli usługi machine learning

Interfejs wiersza polecenia nie jest zamiennikiem dla zestawu SDK usługi Azure Machine Learning. To narzędzie służy do uzupełniające zoptymalizowane pod kątem obsługi wysoce sparametryzowanych zadań, takich jak:

* Tworzenie zasobów obliczeniowych

* przesyłanie eksperymentu sparametryzowane

* Rejestracja modelu

* Tworzenie obrazu

* Wdrażanie usługi

## <a name="prerequisites"></a>Wymagania wstępne


* Aby korzystać z interfejsu wiersza polecenia, musi mieć subskrypcję platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* [Wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalowanie rozszerzenia

Aby zainstalować rozszerzenie interfejsu wiersza polecenia Machine Learning, użyj następującego polecenia:

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-1.0.10-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

Po wyświetleniu monitu wybierz `y` można zainstalować rozszerzenia.

Aby sprawdzić, czy rozszerzenie zostało zainstalowane, użyj następującego polecenia, aby wyświetlić listę określonych ML podpoleceń polecenia:

```azurecli-interactive
az ml -h
```

> [!TIP]
> Aby zaktualizować rozszerzenie, należy najpierw __Usuń__ , a następnie __zainstalować__ go. Spowoduje to zainstalowanie najnowszej wersji.

## <a name="remove-the-extension"></a>Usuń rozszerzenie

Aby usunąć rozszerzenie interfejsu wiersza polecenia, użyj następującego polecenia:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Zarządzanie zasobami

Poniższe polecenia pokazują, jak zarządzać zasoby używane przez usługi Azure Machine Learning przy użyciu interfejsu wiersza polecenia.


+ Utwórz obszar roboczy usługi Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -n myworkspace -g myresourcegroup
    ```

+ Ustaw domyślnego obszaru roboczego:

    ```azurecli-interactive
    az configure --defaults aml_workspace=myworkspace group=myresourcegroup
    ```
    
+ Dołącz klastra AKS

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a name="experiments"></a>Eksperymenty

Poniższe polecenia pokazują, jak pracować z eksperymentów przy użyciu interfejsu wiersza polecenia:

* Dołączanie projektu (Uruchom konfigurację) przed przesłaniem eksperymentu:

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* Rozpocznij przebieg eksperymentu. Korzystając z tego polecenia, należy określić nazwę pliku runconfig, który zawiera konfiguracji uruchamiania. Obliczeniowego elementu docelowego używa konfiguracji uruchamiania do utworzenia środowiska do szkolenia modelu. W tym przykładzie konfiguracji uruchamiania jest ładowany z `./aml_config/myrunconfig.runconfig` pliku.

    ```azurecli-interactive
    az ml run submit -c myrunconfig train.py
    ```

    Aby uzyskać więcej informacji na temat pliku runconfig, zobacz [RunConfig](#runconfig) sekcji.

* Wyświetlanie listy przesłanych eksperymentów:

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>Rejestracja modelu, tworzenie obrazu i wdrożenie

Poniższe polecenia pokazują, jak rejestrowanie uczonego modelu, a następnie wdrożyć go jako usługę produkcyjną:

+ Zarejestruj model przy użyciu usługi Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Utwórz obraz, który zawiera Twoje modelu uczenia maszynowego i zależności: 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Wdrażanie obrazu obliczeniowego elementu docelowego:

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```

## <a id="runconfig"></a> Plik Runconfig

Konfigurację uruchomieniową służy do konfigurowania środowiska szkolenia, używane do trenowania modelu. Tę konfigurację można tworzyć przy użyciu zestawu SDK w pamięci lub może być załadowany z pliku runconfig.

Plik runconfig jest dokument tekst, który opisuje konfiguracji w środowisku szkoleniowym. Na przykład wyświetla nazwę skrypt szkolenia i plik, który zawiera zależności conda, potrzebne do nauczenia modelu.

Interfejsu wiersza polecenia usługi Azure Machine Learning tworzy dwie domyślne `.runconfig` plików o nazwie `docker.runconfig` i `local.runconfig` po dołączeniu projekt za pomocą `az ml project attach` polecenia. 

Jeśli masz kod, który tworzy za pomocą konfiguracji uruchamiania [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) klasy, można użyć `save()` metodę, aby zachować jego `.runconfig` pliku.

Oto przykład zawartości `.runconfig` pliku:

```text
# The script to run.
script: train.py
# The arguments to the script file.
arguments: []
# The name of the compute target to use for this run.
target: local
# Framework to execute inside. Allowed values are "Python" ,  "PySpark", "CNTK",  "TensorFlow", and "PyTorch".
framework: PySpark
# Communicator for the given framework. Allowed values are "None" ,  "ParameterServer", "OpenMpi", and "IntelMpi".
communicator: None
# Automatically prepare the run environment as part of the run itself.
autoPrepareEnvironment: true
# Maximum allowed duration for the run.
maxRunDurationSeconds:
# Number of nodes to use for running job.
nodeCount: 1
# Environment details.
environment:
# Environment variables set for the run.
  environmentVariables:
    EXAMPLE_ENV_VAR: EXAMPLE_VALUE
# Python details
  python:
# user_managed_dependencies=True indicates that the environmentwill be user managed. False indicates that AzureML willmanage the user environment.
    userManagedDependencies: false
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
  docker:
# Set True to perform this run inside a Docker container.
    enabled: true
# Base image used for Docker-based runs.
    baseImage: mcr.microsoft.com/azureml/base:0.2.4
# Set False if necessary to work around shared volume bugs.
    sharedVolumes: true
# Run with NVidia Docker extension to support GPUs.
    gpuSupport: false
# Extra arguments to the Docker run command.
    arguments: []
# Image registry that contains the base image.
    baseImageRegistry:
# DNS name or IP address of azure container registry(ACR)
      address:
# The username for ACR
      username:
# The password for ACR
      password:
# Spark details
  spark:
# List of spark repositories.
    repositories:
    - https://mmlspark.azureedge.net/maven
    packages:
    - group: com.microsoft.ml.spark
      artifact: mmlspark_2.11
      version: '0.12'
    precachePackages: true
# Databricks details
  databricks:
# List of maven libraries.
    mavenLibraries: []
# List of PyPi libraries
    pypiLibraries: []
# List of RCran libraries
    rcranLibraries: []
# List of JAR libraries
    jarLibraries: []
# List of Egg libraries
    eggLibraries: []
# History details.
history:
# Enable history tracking -- this allows status, logs, metrics, and outputs
# to be collected for a run.
  outputCollection: true
# whether to take snapshots for history.
  snapshotProject: true
# Spark configuration details.
spark:
  configuration:
    spark.app.name: Azure ML Experiment
    spark.yarn.maxAppAttempts: 1
# HDI details.
hdi:
# Yarn deploy mode. Options are cluster and client.
  yarnDeployMode: cluster
# Tensorflow details.
tensorflow:
# The number of worker tasks.
  workerCount: 1
# The number of parameter server tasks.
  parameterServerCount: 1
# Mpi details.
mpi:
# When using MPI, number of processes per node.
  processCountPerNode: 1
# data reference configuration details
dataReferences: {}
# Project share datastore reference.
sourceDirectoryDataStore:
# AmlCompute details.
amlcompute:
# VM size of the Cluster to be created.Allowed values are Azure vm sizes. The list of vm sizes is available in 'https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs
  vmSize:
# VM priority of the Cluster to be created.Allowed values are "dedicated" , "lowpriority".
  vmPriority:
# A bool that indicates if the cluster has to be retained after job completion.
  retainCluster: false
# Name of the cluster to be created. If not specified, runId will be used as cluster name.
  name:
# Maximum number of nodes in the AmlCompute cluster to be created. Minimum number of nodes will always be set to 0.
  clusterMaxNodeCount: 1
```
