---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 6970732f53ffa99849e20f279c8bdf4160c30a0a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845148"
---
Wpisy w `inferenceconfig.json` dokumencie są mapowane na parametry klasy [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON a parametrami metody:

| Jednostka JSON | Parametr metody | Opis |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Ścieżka do pliku lokalnego, który zawiera kod do uruchomienia dla obrazu. |
| `runtime` | `runtime` | Element opcjonalny. Które środowisko uruchomieniowe ma być używane na potrzeby obrazu. Obsługiwane środowiska uruchomieniowe to `spark-py` i `python`. Jeśli ustawiono `environment`, ten wpis zostanie zignorowany. |
| `condaFile` | `conda_file` | Element opcjonalny. Ścieżka do pliku lokalnego, który zawiera definicję środowiska Conda do użycia w obrazie.  Jeśli ustawiono `environment`, ten wpis zostanie zignorowany. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Element opcjonalny. Ścieżka do pliku lokalnego, który zawiera dodatkowe kroki platformy Docker do uruchomienia podczas konfigurowania obrazu.  Jeśli ustawiono `environment`, ten wpis zostanie zignorowany.|
| `sourceDirectory` | `source_directory` | Element opcjonalny. Ścieżka do folderów zawierających wszystkie pliki do utworzenia obrazu, co ułatwia dostęp do dowolnych plików w tym folderze lub podfolderze. Można przekazać cały folder z komputera lokalnego jako zależności usługi sieci Web. Uwaga: ścieżki entry_script, conda_file i extra_docker_file_steps są ścieżkami względnymi do ścieżki source_directory. |
| `enableGpu` | `enable_gpu` | Element opcjonalny. Czy włączyć obsługę procesora GPU w obrazie. Obraz procesora GPU musi być używany w usłudze platformy Azure, np. Azure Container Instances. Na przykład Azure Machine Learning COMPUTE, Azure Virtual Machines i Azure Kubernetes Service. Wartość domyślna to false. Jeśli ustawiono `environment`, ten wpis zostanie zignorowany.|
| `baseImage` | `base_image` | Element opcjonalny. Obraz niestandardowy, który ma być używany jako obraz podstawowy. Jeśli obraz podstawowy nie zostanie dostarczony, obraz będzie oparty na podanym parametrze środowiska uruchomieniowego. Jeśli ustawiono `environment`, ten wpis zostanie zignorowany. |
| `baseImageRegistry` | `base_image_registry` | Element opcjonalny. Rejestr obrazu zawierający obraz podstawowy. Jeśli ustawiono `environment`, ten wpis zostanie zignorowany.|
| `cudaVersion` | `cuda_version` | Element opcjonalny. Wersja CUDA do zainstalowania dla obrazów wymagających obsługi procesora GPU. Obraz procesora GPU musi być używany w usłudze platformy Azure. Na przykład Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines i Azure Kubernetes Service. Obsługiwane są wersje 9,0, 9,1 i 10,0. Jeśli ustawiono `enable_gpu`, wartość domyślna to 9,1. Jeśli ustawiono `environment`, ten wpis zostanie zignorowany. |
| `description` | `description` | Opis obrazu. Jeśli ustawiono `environment`, ten wpis zostanie zignorowany.  |
| `environment` | `environment` | Element opcjonalny.  [Środowisko](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)Azure Machine Learning.|

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

W pliku konfiguracyjnym wnioskowania można uwzględnić pełne specyfikacje [środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning. Jeśli to środowisko nie istnieje w obszarze roboczym, Azure Machine Learning utworzy je. W przeciwnym razie Azure Machine Learning zaktualizuje środowisko w razie potrzeby. Poniższy kod JSON jest przykładem:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Można również użyć istniejącego [środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning w oddzielnych parametrach interfejsu wiersza polecenia i usunąć klucz "Environment" z pliku konfiguracyjnego wnioskowania. Użyj-e dla nazwy środowiska i--EV dla wersji środowiska. Jeśli nie określisz wartość--EV, zostanie użyta Najnowsza wersja. Oto przykład pliku konfiguracyjnego wnioskowania:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Następujące polecenie pokazuje, jak wdrożyć model przy użyciu poprzedniego pliku konfiguracyjnego wnioskowania (o nazwie myInferenceConfig. JSON). 

Używa również najnowszej wersji istniejącego [środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning (o nazwie "Azure-minimum").

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
