---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: ab31d45808a8c77c53b895643eec63952201d9e4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800016"
---
Wpisy w `inferenceconfig.json` dokumencie są mapowane na parametry klasy [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON a parametrami metody:

| Jednostka JSON | Parametr metody | Opis |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Ścieżka do pliku lokalnego, który zawiera kod do uruchomienia dla obrazu. |
| `runtime` | `runtime` | Które środowisko uruchomieniowe ma być używane na potrzeby obrazu. Bieżące obsługiwane środowiska uruchomieniowe to `spark-py` i `python`. |
| `condaFile` | `conda_file` | Opcjonalny. Ścieżka do pliku lokalnego, który zawiera definicję środowiska Conda do użycia w obrazie. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Opcjonalny. Ścieżka do pliku lokalnego, który zawiera dodatkowe kroki platformy Docker do uruchomienia podczas konfigurowania obrazu. |
| `sourceDirectory` | `source_directory` | Opcjonalny. Ścieżka do folderów zawierających wszystkie pliki do utworzenia obrazu. |
| `enableGpu` | `enable_gpu` | Opcjonalny. Czy włączyć obsługę procesora GPU w obrazie. Obraz procesora GPU musi być używany w usłudze platformy Azure, takiej jak Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines i Azure Kubernetes Service. Wartość domyślna to false. |
| `baseImage` | `base_image` | Opcjonalny. Obraz niestandardowy, który ma być używany jako obraz podstawowy. Jeśli obraz podstawowy nie zostanie dostarczony, obraz będzie oparty na podanym parametrze środowiska uruchomieniowego. |
| `baseImageRegistry` | `base_image_registry` | Opcjonalny. Rejestr obrazu zawierający obraz podstawowy. |
| `cudaVersion` | `cuda_version` | Opcjonalny. Wersja CUDA do zainstalowania dla obrazów wymagających obsługi procesora GPU. Obraz procesora GPU musi być używany w usłudze platformy Azure, takiej jak Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines i Azure Kubernetes Service. Obsługiwane są wersje 9,0, 9,1 i 10,0. Jeśli ustawiono `enable_gpu`, wartość domyślna to 9,1. |
| `description` | `description` | Opis obrazu. |

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