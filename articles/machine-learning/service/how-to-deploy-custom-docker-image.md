---
title: Wdrażanie modeli przy użyciu niestandardowego obrazu platformy Docker
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać niestandardowego obrazu platformy Docker podczas wdrażania modeli usług Azure Machine Learning. Podczas wdrażania przeszkolonego modelu obraz platformy Docker jest tworzony w celu hostowania obrazu, serwera sieci Web i innych składników wymaganych do uruchomienia usługi. Gdy usługa Azure Machine Learning udostępnia obraz domyślny, można również użyć własnego obrazu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/11/2019
ms.openlocfilehash: 0025f488f6a9b0af4e05a8bdf3ddf36c089d4d9f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856123"
---
# <a name="deploy-a-model-using-a-custom-docker-image"></a>Wdrażanie modelu przy użyciu niestandardowego obrazu platformy Docker

Dowiedz się, jak używać niestandardowego obrazu platformy Docker podczas wdrażania przeszkolonych modeli przy użyciu usługi Azure Machine Learning.

Po wdrożeniu przeszkolonego modelu do usługi sieci Web lub urządzenia IoT Edge zostanie utworzony obraz platformy Docker. Ten obraz zawiera model, środowisko Conda i zasoby, które są konieczne do korzystania z modelu. Zawiera również serwer sieci Web do obsługi żądań przychodzących, które są wdrażane jako usługa sieci Web, oraz składniki niezbędne do pracy z usługą Azure IoT Hub.

Usługa Azure Machine Learning udostępnia domyślny obraz platformy Docker, dzięki czemu nie trzeba martwić się o tworzenie go. Możesz również użyć niestandardowego obrazu, który tworzysz jako _obraz podstawowy_. Obraz podstawowy jest używany jako punkt wyjścia podczas tworzenia obrazu dla wdrożenia. Udostępnia on podstawowy system operacyjny i składniki. Następnie proces wdrażania dodaje do obrazu dodatkowe składniki, takie jak model, środowisko Conda i inne zasoby, przed jego wdrożeniem.

Zazwyczaj tworzysz obraz niestandardowy, gdy chcesz kontrolować wersje składników lub zaoszczędzić czas podczas wdrażania. Na przykład możesz chcieć przeprowadzić standaryzację dla określonej wersji języka Python, Conda lub innego składnika. Możesz również zainstalować oprogramowanie wymagane przez model, w którym proces instalacji zajmuje dużo czasu. Zainstalowanie oprogramowania podczas tworzenia obrazu podstawowego oznacza, że nie trzeba go instalować dla każdego wdrożenia.

> [!IMPORTANT]
> Podczas wdrażania modelu nie można przesłonić składników podstawowych, takich jak serwer sieci Web lub składniki IoT Edge. Te składniki zapewniają znane środowisko robocze, które jest testowane i obsługiwane przez firmę Microsoft.

> [!WARNING]
> Firma Microsoft może nie pomóc w rozwiązywaniu problemów spowodowanych przez niestandardowy obraz. Jeśli wystąpią problemy, może zostać wyświetlony monit o użycie obrazu domyślnego lub jednego z obrazów firmy Microsoft, aby sprawdzić, czy problem jest specyficzny dla Twojego obrazu.

Ten dokument jest podzielony na dwie sekcje:

* Tworzenie obrazu niestandardowego: Zawiera informacje dla administratorów i DevOps na temat tworzenia obrazu niestandardowego i konfigurowania uwierzytelniania do Azure Container Registry przy użyciu interfejsu wiersza polecenia platformy Azure i interfejsu wiersza polecenia Machine Learning.
* Użyj obrazu niestandardowego: Program udostępnia informacje osobom zajmującym się danymi i DevOps/MLOps na korzystanie z obrazów niestandardowych podczas wdrażania przeszkolonego modelu z zestawu SDK języka Python lub interfejsu wiersza polecenia.

## <a name="prerequisites"></a>Wymagania wstępne

* Grupa robocza usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie obszaru roboczego](how-to-manage-workspace.md) .
* [Zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* [Wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Rozszerzenie interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md).
* [Azure Container Registry](/azure/container-registry) lub innych rejestrów platformy Docker, które są dostępne w Internecie.
* W procedurach przedstawionych w tym dokumencie założono, że wiesz już, jak utworzyć i użyć obiektu __konfiguracji wnioskowania__ w ramach wdrażania modelu. Aby uzyskać więcej informacji, zobacz sekcję "Przygotowanie do wdrożenia" w temacie [gdzie można wdrożyć i jak to zrobić](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-image"></a>Tworzenie obrazu niestandardowego

Informacje w tej sekcji założono, że używasz Azure Container Registry do przechowywania obrazów platformy Docker. Podczas planowania tworzenia obrazów niestandardowych dla usługi Azure Machine Learning należy użyć poniższej listy kontrolnej:

* Czy używasz Azure Container Registry utworzonego dla obszaru roboczego usługi Azure Machine Learning lub autonomicznej Azure Container Registry?

    W przypadku używania obrazów przechowywanych w __rejestrze kontenerów dla obszaru roboczego__nie trzeba uwierzytelniać się w rejestrze. Uwierzytelnianie jest obsługiwane przez obszar roboczy.

    > [!WARNING]
    > Usługa Azure Container Rzegistry dla Twojego obszaru roboczego jest __tworzona podczas pierwszego uczenia lub wdrożenia modelu__ przy użyciu obszaru roboczego. Jeśli utworzono nowy obszar roboczy, ale nie został przeszkolony ani utworzony model, w obszarze roboczym nie będą dostępne żadne Azure Container Registry.

    Aby uzyskać informacje na temat pobierania nazwy Azure Container Registry dla obszaru roboczego, zobacz sekcję [pobieranie nazwy rejestru kontenerów](#getname) w tym artykule.

    W przypadku używania obrazów przechowywanych w __autonomicznym rejestrze kontenerów__należy skonfigurować jednostkę usługi, która ma co najmniej dostęp do odczytu. Następnie podaj identyfikator jednostki usługi (username) i hasło dla każdej osoby, która używa obrazów z rejestru. Wyjątek polega na tym, że rejestr kontenerów jest publicznie dostępny.

    Aby uzyskać informacje na temat tworzenia prywatnego Azure Container Registry, zobacz [Tworzenie prywatnego rejestru kontenerów](/azure/container-registry/container-registry-get-started-azure-cli).

    Aby uzyskać informacje na temat używania nazw głównych usługi z Azure Container Registry, zobacz [Azure Container Registry Authentication z](/azure/container-registry/container-registry-auth-service-principal)jednostkami usługi.

* Informacje o Azure Container Registry i obrazie: Podaj nazwę obrazu dla każdej osoby, która musi go używać. Na przykład obraz o nazwie `myimage`przechowywane w rejestrze o nazwie `myregistry`jest przywoływany w `myregistry.azurecr.io/myimage` przypadku użycia obrazu do wdrożenia modelu

* Wymagania dotyczące obrazu: Usługa Azure Machine Learning obsługuje tylko obrazy platformy Docker, które udostępniają następujące oprogramowanie:

    * Ubuntu 16,04 lub nowszy.
    * Conda 4.5. # lub nowszej.
    * Python 3.5. # lub 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Pobierz informacje o rejestrze kontenera

W tej sekcji dowiesz się, jak uzyskać nazwę Azure Container Registry dla obszaru roboczego usługi Azure Machine Learning.

> [!WARNING]
> Azure Container Registry dla obszaru roboczego jest __tworzony podczas pierwszego uczenia lub wdrożenia modelu__ przy użyciu obszaru roboczego. Jeśli utworzono nowy obszar roboczy, ale nie został przeszkolony ani utworzony model, w obszarze roboczym nie będą dostępne żadne Azure Container Registry.

Jeśli modele zostały już przeszkolone lub wdrożone przy użyciu usługi Azure Machine Learning, dla Twojego obszaru roboczego utworzono rejestr kontenerów. Aby znaleźć nazwę tego rejestru kontenerów, wykonaj następujące czynności:

1. Otwórz nową powłokę lub wiersz polecenia i użyj następującego polecenia, aby uwierzytelnić się w ramach subskrypcji platformy Azure:

    ```azurecli-interactive
    az login
    ```

    Postępuj zgodnie z monitami, aby uwierzytelnić się w subskrypcji.

2. Użyj poniższego polecenia, aby wyświetlić listę rejestrów kontenerów dla obszaru roboczego. Zamień `<myworkspace>` na nazwę obszaru roboczego usługi Azure Machine Learning. Zamień `<resourcegroup>` na grupę zasobów platformy Azure, która zawiera obszar roboczy:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    Zwracane informacje są podobne do następujących:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>` Wartość to nazwa Azure Container Registry obszaru roboczego.

### <a name="build-a-custom-image"></a>Tworzenie obrazu niestandardowego

Kroki opisane w tej sekcji przedstawiają Tworzenie niestandardowego obrazu platformy Docker w Azure Container Registry.

1. Utwórz nowy plik tekstowy o nazwie `Dockerfile`i użyj następującego tekstu jako zawartości:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Z poziomu powłoki lub wiersza polecenia Użyj następujących poleceń, aby uwierzytelnić się w Azure Container Registry. `<registry_name>` Zastąp ciąg nazwą rejestru kontenera, w którym ma być przechowywany obraz:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Aby przekazać pliku dockerfile i skompilować go, użyj następującego polecenia. Zamień `<registry_name>` na nazwę rejestru kontenera, w którym ma być przechowywany obraz:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    W trakcie procesu kompilacji informacje są przesyłane strumieniowo z powrotem do wiersza polecenia. Jeśli kompilacja zakończy się pomyślnie, zostanie wyświetlony komunikat podobny do następującego:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Aby uzyskać więcej informacji na temat tworzenia obrazów przy użyciu Azure Container Registry, zobacz [Kompilowanie i uruchamianie obrazu kontenera przy użyciu Azure Container Registry zadań](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Aby uzyskać więcej informacji na temat przekazywania istniejących obrazów do Azure Container Registry, zobacz [wypychanie pierwszego obrazu do prywatnego rejestru kontenerów platformy Docker](/azure/container-registry/container-registry-get-started-docker-cli).

## <a name="use-a-custom-image"></a>Korzystanie z obrazu niestandardowego

Aby użyć obrazu niestandardowego, potrzebne są następujące informacje:

* __Nazwa obrazu__. Na przykład `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` jest ścieżką do podstawowego obrazu platformy Docker dostarczonego przez firmę Microsoft.
* Jeśli obraz znajduje się w __repozytorium prywatnym__, potrzebne są następujące informacje:

    * __Adres__rejestru. Na przykład `myregistry.azureecr.io`.
    * __Nazwa__ główna usługi i __hasło__ , które mają dostęp do odczytu do rejestru.

    Jeśli nie masz tych informacji, porozmawiaj z administratorem, aby uzyskać Azure Container Registry, który zawiera obraz.

### <a name="publicly-available-images"></a>Publicznie dostępne obrazy

Firma Microsoft udostępnia kilka obrazów platformy Docker w publicznie dostępnym repozytorium, które może być używane z krokami z tej sekcji:

| Image | Opis |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Podstawowa ilustracja dotycząca usługi Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | Zawiera środowisko uruchomieniowe ONNX. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | Zawiera składniki środowiska uruchomieniowego ONNX i CUDA. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | Zawiera środowisko uruchomieniowe ONNX i TensorRT. |

> [!TIP]
> Ponieważ te obrazy są publicznie dostępne, nie trzeba podawać adresu, nazwy użytkownika ani hasła podczas ich używania.

> [!IMPORTANT]
> Obrazy firmy Microsoft korzystające z CUDA lub TensorRT muszą być używane tylko w przypadku usług Microsoft Azure Services.

> [!TIP]
>__Jeśli model jest szkolony na Azure Machine Learning obliczeń__przy użyciu __wersji 1.0.22 lub nowszej__ Azure Machine Learning SDK, obraz jest tworzony podczas uczenia. Aby odnaleźć nazwę tego obrazu, użyj `run.properties["AzureML.DerivedImageName"]`. Poniższy przykład ilustruje sposób użycia tego obrazu:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Korzystanie z obrazu z zestawem SDK Azure Machine Learning

Aby użyć obrazu niestandardowego, ustaw `base_image` Właściwość [obiektu konfiguracji wnioskowania](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) na adres obrazu:

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

Ten format działa dla obu obrazów przechowywanych w Azure Container Registry dla Twojego obszaru roboczego i rejestrów kontenerów, które są publicznie dostępne. Na przykład poniższy kod używa domyślnego obrazu dostarczonego przez firmę Microsoft:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Aby użyć obrazu z __prywatnego rejestru kontenerów__ , który nie znajduje się w obszarze roboczym, należy określić adres repozytorium oraz nazwę użytkownika i hasło:

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

### <a name="use-an-image-with-the-machine-learning-cli"></a>Korzystanie z obrazu za pomocą interfejsu wiersza polecenia Machine Learning

> [!IMPORTANT]
> Obecnie interfejs wiersza polecenia Machine Learning może używać obrazów z Azure Container Registry dla obszaru roboczego lub publicznie dostępnych repozytoriów. Nie można używać obrazów z autonomicznych rejestrów prywatnych.

Podczas wdrażania modelu przy użyciu interfejsu wiersza polecenia Machine Learning można dostarczyć plik konfiguracyjny wnioskowania, który odwołuje się do obrazu niestandardowego. Poniższy dokument JSON pokazuje, jak odwoływać się do obrazu w rejestrze kontenera publicznego:

```json
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "infenv.yml",
   "extraDockerfileSteps": null,
   "sourceDirectory": null,
   "enableGpu": false,
   "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
   "baseImageRegistry": "mcr.microsoft.com"
}
```

Ten plik jest używany z `az ml model deploy` poleceniem. Ten `--ic` parametr służy do określania pliku konfiguracyjnego wnioskowania.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Aby uzyskać więcej informacji na temat wdrażania modelu przy użyciu interfejsu wiersza polecenia ML, zobacz sekcję "Rejestracja modelu, profilowanie i wdrażanie" w artykule [rozszerzenie interfejsu wiersza polecenia dla usługi Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o tym [, gdzie wdrożyć i jak to zrobić](how-to-deploy-and-where.md).
* Dowiedz się [, jak uczenie i wdrażanie modeli uczenia maszynowego przy użyciu Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).
