---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 52689c585e148c18d16ad627570414a8de444fea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926998"
---
Wpisy w `inferenceconfig.json` dokumencie są mapowane na parametry klasy [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON a parametrami metody:

| Jednostka JSON | Parametr metody | Opis |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Ścieżka do pliku lokalnego, który zawiera kod do uruchomienia dla obrazu. |
| `runtime` | `runtime` | Opcjonalny. Które środowisko uruchomieniowe ma być używane na potrzeby obrazu. Bieżące obsługiwane środowiska uruchomieniowe to `spark-py` i `python`. Jeśli ustawiono wartość `environment`, zostanie ona zignorowana. |
| `condaFile` | `conda_file` | Opcjonalny. Ścieżka do pliku lokalnego, który zawiera definicję środowiska Conda do użycia w obrazie.  Jeśli ustawiono wartość `environment`, zostanie ona zignorowana. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Opcjonalny. Ścieżka do pliku lokalnego, który zawiera dodatkowe kroki platformy Docker do uruchomienia podczas konfigurowania obrazu.  Jeśli ustawiono wartość `environment`, zostanie ona zignorowana.|
| `sourceDirectory` | `source_directory` | Opcjonalny. Ścieżka do folderów zawierających wszystkie pliki do utworzenia obrazu. |
| `enableGpu` | `enable_gpu` | Opcjonalny. Czy włączyć obsługę procesora GPU w obrazie. Obraz procesora GPU musi być używany w usłudze platformy Azure, takiej jak Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines i Azure Kubernetes Service. Wartość domyślna to False. Jeśli ustawiono wartość `environment`, zostanie ona zignorowana.|
| `baseImage` | `base_image` | Opcjonalny. Obraz niestandardowy, który ma być używany jako obraz podstawowy. Jeśli obraz podstawowy nie zostanie dostarczony, obraz będzie oparty na podanym parametrze środowiska uruchomieniowego. Jeśli ustawiono wartość `environment`, zostanie ona zignorowana. |
| `baseImageRegistry` | `base_image_registry` | Opcjonalny. Rejestr obrazu zawierający obraz podstawowy. Jeśli ustawiono wartość `environment`, zostanie ona zignorowana.|
| `cudaVersion` | `cuda_version` | Opcjonalny. Wersja CUDA do zainstalowania dla obrazów wymagających obsługi procesora GPU. Obraz procesora GPU musi być używany w usłudze platformy Azure, takiej jak Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines i Azure Kubernetes Service. Obsługiwane są wersje 9,0, 9,1 i 10,0. Jeśli ustawiono `enable_gpu`, wartość domyślna to 9,1. Jeśli ustawiono wartość `environment`, zostanie ona zignorowana. |
| `description` | `description` | Opis obrazu. Jeśli ustawiono wartość `environment`, zostanie ona zignorowana.  |
| `environment` | `environment` | Opcjonalny.  [Środowisko](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)Azure Machine Learning.|

Poniższy kod JSON to Przykładowa konfiguracja wnioskowania do użycia z interfejsem wiersza polecenia:

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```

Poniższy kod JSON to Przykładowa konfiguracja wnioskowania, która używa istniejącego [środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning z określoną wersją do użycia z interfejsem wiersza polecenia:

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": "1"
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```

Poniższy kod JSON to Przykładowa konfiguracja wnioskowania, która używa istniejącego [środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning z najnowszą wersją do użycia z interfejsem wiersza polecenia:

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": null
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```
