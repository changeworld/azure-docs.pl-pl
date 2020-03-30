---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159419"
---
Wpisy w `inferenceconfig.json` dokumencie są mapowane na parametry klasy [InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON i parametry metody:

| Jednostka JSON | Parametr Metody | Opis |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Ścieżka do pliku lokalnego, który zawiera kod do uruchomienia dla obrazu. |
| `sourceDirectory` | `source_directory` | Element opcjonalny. Ścieżka do folderów zawierających wszystkie pliki w celu utworzenia obrazu, co ułatwia dostęp do wszystkich plików w tym folderze lub podfolderze. Cały folder z komputera lokalnego można przekazać jako zależności dla usługi sieci Web. Uwaga: ścieżki entry_script, conda_file i extra_docker_file_steps są ścieżkami względnymi do ścieżki source_directory. |
| `environment` | `environment` | Element opcjonalny.  [Środowisko](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)usługi Azure Machine Learning .|

W pliku konfiguracyjnym wnioskowania można dołączyć pełne specyfikacje [środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) usługi Azure Machine Learning. Jeśli to środowisko nie istnieje w obszarze roboczym, usługa Azure Machine Learning utworzy go. W przeciwnym razie usługa Azure Machine Learning zaktualizuje środowisko, jeśli to konieczne. Przykładem jest następujący JSON:

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

Można również użyć istniejącego [środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) usługi Azure Machine Learning w oddzielnych parametrów interfejsu wiersza polecenia i usunąć klucz "środowisko" z pliku konfiguracji wnioskowania. Użyj -e dla nazwy środowiska i --ev dla wersji środowiska. Jeśli nie określisz --ev, zostanie użyta najnowsza wersja. Oto przykład pliku konfiguracyjnego wnioskowania:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Poniższe polecenie pokazuje, jak wdrożyć model przy użyciu poprzedniego pliku konfiguracji wnioskowania (o nazwie myInferenceConfig.json). 

Używa również najnowszej wersji istniejącego [środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) usługi Azure Machine Learning (o nazwie AzureML-Minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
