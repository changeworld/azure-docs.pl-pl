---
title: Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać niestandardowego obrazu platformy Docker, wdrażając swoje modele usługi Azure Machine Learning. W przypadku wdrażania trenowanego modelu, obraz platformy Docker jest tworzony hosta obrazu, serwer sieci web i inne składniki potrzebne do uruchomienia usługi. Usługi Azure Machine Learning zapewnia domyślnego obrazu dla Ciebie, jednocześnie można również użyć własnego obrazu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/05/2019
ms.openlocfilehash: 02bc8835ddb163d81f389e13b21b88adca55cb2f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082627"
---
# <a name="deploy-a-model-using-a-custom-docker-image"></a>Wdrażanie modelu przy użyciu niestandardowego obrazu platformy Docker

Dowiedz się, jak używać niestandardowego obrazu platformy Docker, wdrażając przeszkolone modele przy użyciu usługi Azure Machine Learning.

Jeśli wdrażasz uczonego modelu do usługi sieci web lub urządzenie usługi IoT Edge jest tworzony obraz platformy Docker. Ten obraz zawiera model, środowiska conda i zasobów potrzebnych do korzystania z modelu. Zawiera ona także serwer sieci web do obsługi przychodzących żądań, po wdrożeniu usługi sieci web i składniki potrzebne do pracy z usługą Azure IoT Hub.

Usługa Azure Machine Learning udostępnia domyślny obraz platformy Docker, więc nie trzeba już martwić się o tworzeniu. Można również użyć niestandardowego obrazu, która jest tworzona jako _obrazu podstawowego_. Obraz podstawowy jest używany jako punktu wyjścia podczas tworzenia obrazu dla wdrożenia. Zapewnia bazowym systemem operacyjnym i składników. Proces wdrażania następnie dodaje dodatkowe składniki, takie jak model, środowiska conda i inne zasoby do obrazu przed jego wdrożeniem.

Zazwyczaj można utworzyć niestandardowy obraz, gdy użytkownik chce kontrolować wersje składników lub oszczędzić czas podczas wdrażania. Na przykład możesz chcieć standaryzuj określoną wersję języka Python, Conda lub inny składnik. Można także zainstalować oprogramowanie jest wymagane przez model, w którym zajmuje dużo czasu, przez proces instalacji. Instalowanie oprogramowania, podczas tworzenia obrazu podstawowego oznacza, że nie trzeba go zainstalować w każdym wdrożeniu.

> [!IMPORTANT]
> W przypadku wdrażania modelu, nie można zastąpić podstawowe składniki, takie jak serwer sieci web lub składniki usługi IoT Edge. Te składniki zapewniają znanego środowiska pracy, który jest testowany i obsługiwane przez firmę Microsoft.

> [!WARNING]
> Microsoft nie można pomóc w rozwiązywaniu problemów powodowanych przez obraz niestandardowy. Jeśli napotkasz problemy, może się prośba o użyć domyślnego obrazu lub jeden z obrazów, które firma Microsoft udostępnia, aby sprawdzić, czy problem jest specyficzny dla obrazu.

Ten dokument jest podzielony na dwie sekcje:

* Tworzenie obrazu niestandardowego: Zawiera informacje na temat administratorów i metodyki DevOps na tworzenie niestandardowego obrazu i konfigurowania uwierzytelniania usługi Azure Container Registry przy użyciu wiersza polecenia platformy Azure i interfejsu wiersza polecenia Machine Learning.
* Używanie niestandardowego obrazu: Informacje do operacji deweloperskich/MLOps i analityków danych na temat używania obrazów niestandardowych podczas wdrażania uczonego modelu z zestawu SDK języka Python lub interfejsu wiersza polecenia ML.

## <a name="prerequisites"></a>Wymagania wstępne

* Usługa Azure Machine Learning service grupy roboczej. Aby uzyskać więcej informacji, zobacz [Utwórz obszar roboczy](setup-create-workspace.md) artykułu.
* Aplikację Azure Machine Learning zestawu SDK. Aby uzyskać więcej informacji, zobacz sekcję zestawu SDK języka Python [Utwórz obszar roboczy](setup-create-workspace.md#sdk) artykułu.
* [Wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Usługi Azure Container Registry](/azure/container-registry) lub innych rejestru platformy Docker, który jest dostępny w Internecie.
* Kroki opisane w tym dokumencie przyjęto założenie, że czytelnik zna tworzenia i używania __konfiguracji wnioskowania__ obiektu jako część wdrożenia modelu. Aby uzyskać więcej informacji, zobacz sekcję "Przygotowanie do wdrożenia" [miejsca wdrożenia i w jaki sposób](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-image"></a>Tworzenie obrazu niestandardowego

Informacje przedstawione w tej sekcji założono, że używasz usługi Azure Container Registry do przechowywania obrazów platformy Docker. Użyj poniższej listy kontrolnej, podczas planowania, aby utworzyć niestandardowe obrazy dla usługi Azure Machine Learning:

* Będziesz używać usługi Azure Container Registry utworzone dla obszaru roboczego usługi Azure Machine Learning lub autonomicznego usługi Azure Container Registry?

    Jeśli przy użyciu obrazów przechowywanych w __rejestru kontenerów do obszaru roboczego__, nie trzeba do uwierzytelniania w rejestrze. Uwierzytelnianie jest obsługiwane przez obszar roboczy.

    > [!TIP]
    > Rejestr kontenerów do obszaru roboczego jest tworzony po raz pierwszy uczenia lub wdrażania modelu korzystanie z obszaru roboczego. Jeśli został utworzony nowy obszar roboczy, ale nie skonfigurowanych pod kątem lub utworzono model, nie usługi Azure Container Registry będzie istnieć dla obszaru roboczego.

    Aby uzyskać informacje dotyczące pobierania nazwę obszaru roboczego usługi Azure Container Registry, zobacz [nazwy rejestru kontenerów Get](#getname) dalszej części tego artykułu.

    Podczas używania obrazów przechowywanych w __autonomicznego kontenera rejestru__, musisz skonfigurować jednostki usługi, która ma co najmniej do odczytu dostępu. Możesz dostarczać identyfikator jednostki usługi (nazwa użytkownika) i hasło dla każdego, kto korzysta z obrazów z rejestru. Wyjątkiem jest, jeśli można utworzyć rejestr kontenerów dostępny publicznie.

    Aby uzyskać informacje na temat tworzenia prywatnego rejestru kontenerów platformy Azure, zobacz [Tworzenie prywatnego rejestru kontenerów](/azure/container-registry/container-registery-get-started-azure-cli).

    Aby uzyskać informacje na temat używania jednostek usług za pomocą usługi Azure Container Registry, zobacz [uwierzytelniania usługi Azure Container Registry za pomocą jednostki usługi](/azure/container-registry/container-registry-auth-service-principal).

* Azure Container Registry i obraz informacji: Podaj nazwę obrazu dla każdego, kto należy z niej korzystać. Na przykład obraz o nazwie `myimage`, przechowywanych w rejestrze, o nazwie `myregistry`, jest określany jako `myregistry.azurecr.io/myimage` w przypadku używania obrazu dla modelu wdrażania

* Wymagania dotyczące obrazu: Usługa Azure Machine Learning obsługuje wyłącznie obrazy platformy Docker, które zapewniają następujące oprogramowanie:

    * Ubuntu 16.04 lub nowszej.
    * Conda 4.5. # lub nowszej.
    * Język Python 3.5. # lub 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Pobieranie informacji z rejestru kontenerów

W tej sekcji Dowiedz się, jak uzyskać nazwę rejestru kontenerów platformy Azure dla Twojego obszaru roboczego usługi Azure Machine Learning.

> [!TIP]
> Rejestr kontenerów do obszaru roboczego jest tworzony po raz pierwszy uczenia lub wdrażania modelu korzystanie z obszaru roboczego. Jeśli został utworzony nowy obszar roboczy, ale nie skonfigurowanych pod kątem lub utworzono model, nie usługi Azure Container Registry będzie istnieć dla obszaru roboczego.

Jeśli została już skonfigurowanych pod kątem lub wdrożyć modeli za pomocą usługi Azure Machine Learning, rejestr kontenera zostało utworzone dla obszaru roboczego. Aby znaleźć nazwę tego rejestru kontenerów, użyj następujących kroków:

1. Otwórz nowy powłoki lub wiersza polecenia i użyj następującego polecenia do uwierzytelniania w Twojej subskrypcji platformy Azure:

    ```azurecli-interactive
    az login
    ```

    Postępuj zgodnie z monitami, aby uwierzytelnić się do subskrypcji.

2. Użyj następującego polecenia, aby wyświetlić listę rejestru kontenerów do obszaru roboczego. Zastąp `<myworkspace>` przy użyciu nazwy swojego obszaru roboczego usługi Azure Machine Learning. Zastąp `<resourcegroup>` z grupą zasobów platformy Azure, która zawiera obszar roboczy:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    Zwracane informacje dotyczą jest podobny do następującego tekstu:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>` Wartość nazwę obszaru roboczego usługi Azure Container Registry.

### <a name="build-a-custom-image"></a>Kompilowanie obrazu niestandardowego

Kroki opisane w tym przewodniku sekcji Tworzenie niestandardowego obrazu platformy Docker w usługi Azure Container Registry.

1. Utwórz nowy plik tekstowy o nazwie `Dockerfile`i skorzystaj z poniższego tekstu jako zawartość:

    ```text
    FROM ubuntu:16.04

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-4.5.12-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y python=3.6 && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Z powłoki lub wiersza polecenia należy użyć następującego do uwierzytelniania w usłudze Azure Container Registry. Zastąp `<registry_name>` nazwą rejestru kontenerów, które mają być przechowywane obrazu w:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Aby przekazać plik Dockerfile i skompiluj go, użyj następującego polecenia. Zastąp `<registry_name>` nazwą rejestru kontenerów, które mają być przechowywane obrazu w:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    Podczas procesu kompilacji informacje są przesyłane strumieniowo można z powrotem do wiersza polecenia. Jeśli kompilacja zakończy się pomyślnie, pojawi się komunikat podobny do następującego tekstu:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Aby uzyskać więcej informacji dotyczących tworzenia obrazów za pomocą usługi Azure Container Registry, zobacz [Skompiluj i uruchom obraz kontenera przy użyciu zadań rejestru kontenera platformy Azure](/docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli.md)

Aby uzyskać więcej informacji na temat przekazywania istniejących obrazów do usługi Azure Container Registry, zobacz [Wypchnij swój pierwszy obraz do prywatnego rejestru kontenerów platformy Docker](/azure/container-registry/container-registry-get-started-docker-cli.md).

## <a name="use-a-custom-image"></a>Korzystanie z obrazu niestandardowego

Aby użyć niestandardowego obrazu, potrzebne są następujące informacje:

* __Nazwa obrazu__. Na przykład `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` jest ścieżka do podstawowego obrazu platformy Docker, obsługiwane przez firmę Microsoft.
* Jeśli obraz, który znajduje się w __w repozytorium prywatnym__, potrzebne są następujące informacje:

    * Rejestr __adres__. Na przykład `myregistry.azureecr.io`.
    * Jednostki usługi __username__ i __hasło__ , ma dostęp do odczytu do rejestru.

    Jeśli nie masz tych informacji, Porozmawiaj z administratorem usługi Azure Container Registry, który zawiera obraz.

### <a name="publicly-available-images"></a>Publicznie dostępne obrazy

Firma Microsoft udostępnia kilka obrazów platformy docker na dostępny publicznie repozytorium, które mogą być używane z instrukcjami w tej sekcji:

| Image | Opis |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Podstawowy obraz dla usługi Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | Zawiera środowisko uruchomieniowe ONNX. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | Zawiera środowisko uruchomieniowe ONNX i składniki architektury CUDA. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | Zawiera środowisko uruchomieniowe ONNX i TensorRT. |

> [!TIP]
> Ponieważ te obrazy są publicznie dostępne, nie należy podać adres, nazwa użytkownika lub hasło podczas korzystania z nich.

> [!IMPORTANT]
> Obrazy firmy Microsoft, korzystających z architektury CUDA lub TensorRT muszą być używane na usług platformy Microsoft Azure tylko.

> [!TIP]
>__Jeśli model jest uczony w obliczeniowego usługi Azure Machine Learning__przy użyciu __wersji 1.0.22 lub nowszej__ zestawu Azure SDK Learning maszyny, obraz jest tworzony podczas szkolenia. Aby odnaleźć nazwy tego obrazu, należy użyć `run.properties["AzureML.DerivedImageName"]`. Poniższy przykład pokazuje sposób użycia następująco:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Obraz za pomocą zestawu SDK usługi Azure Machine Learning

Aby użyć obrazu niestandardowego, ustaw `base_image` właściwość [obiekt konfiguracji wnioskowania](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) adres obrazu:

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

Ten format działa w przypadku obu obrazów przechowywanych w usłudze Azure Container Registry dla Twojego obszaru roboczego i kontener rejestrów, które są dostępne publicznie. Na przykład w poniższym kodzie użyto domyślnego obrazu obsługiwane przez firmę Microsoft:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Aby użyć obrazu z __prywatnego rejestru kontenerów__ nie jest w obszarze roboczym, należy określić adres repozytorium i nazwę użytkownika i hasło:

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

### <a name="use-an-image-with-the-machine-learning-cli"></a>Obraz za pomocą interfejsu wiersza polecenia Machine Learning

> [!IMPORTANT]
> Obecnie interfejsu wiersza polecenia Machine Learning mogą używać obrazów z rejestru kontenerów platformy Azure dla obszaru roboczego lub publicznie repozytoria. Nie można go używać obrazów z autonomicznego prywatnych rejestrów.

W przypadku wdrażania modelu przy użyciu interfejsu wiersza polecenia Machine Learning, należy podać plik konfiguracji wnioskowania, który odwołuje się do niestandardowego obrazu. Poniższy dokument JSON pokazuje, jak odwoływać się do obrazu w rejestrze publicznego kontenera:

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

Ten plik jest używany z `az ml model deploy` polecenia. `--ic` Parametr jest używany do określenia pliku konfiguracji jest wnioskowania.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Aby uzyskać więcej informacji na temat wdrażania modelu przy użyciu interfejsu wiersza polecenia ML, zobacz sekcję "rejestracji modelu, profilowanie i wdrożenie" [rozszerzenie interfejsu wiersza polecenia usługi Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) artykułu.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [miejsca wdrożenia i w jaki sposób](how-to-deploy-and-where.md).
* Dowiedz się, jak [szkolenie i wdrażania modeli uczenia maszynowego przy użyciu potoków Azure](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).