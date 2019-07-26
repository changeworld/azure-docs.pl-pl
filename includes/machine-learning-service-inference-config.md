---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 03b40b1f334816f08d7e169f8ff78bdb7c06c4de
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348493"
---
Wpisy w `inferenceconfig.json` dokumencie są mapowane na parametry klasy [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON a parametrami metody:

| Jednostka JSON | Parametr metody | Opis |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Ścieżka do pliku lokalnego, który zawiera kod do uruchomienia dla obrazu. |
| `runtime` | `runtime` | Które środowisko uruchomieniowe ma być używane na potrzeby obrazu. Bieżące obsługiwane środowiska uruchomieniowe to "Spark-pr" i "Python". |
| `condaFile` | `conda_file` | Opcjonalny. Ścieżka do pliku lokalnego zawierającego definicję środowiska Conda do użycia w obrazie. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Opcjonalna. Ścieżka do pliku lokalnego zawierającego dodatkowe kroki platformy Docker do uruchomienia podczas konfigurowania obrazu. |
| `sourceDirectory` | `source_directory` | Opcjonalny. Ścieżka do folderów zawierających wszystkie pliki do utworzenia obrazu. |
| `enableGpu` | `enable_gpu` | Opcjonalna. Czy włączyć obsługę procesora GPU w obrazie. Obraz procesora GPU musi być używany w usłudze Microsoft Azure. Na przykład Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines i Azure Kubernetes Service. Wartością domyślną jest false. |
| `baseImage` | `base_image` | Opcjonalna. Obraz niestandardowy, który będzie używany jako obraz podstawowy. Jeśli obraz podstawowy nie zostanie określony, zostanie użyty obraz podstawowy na podstawie danego parametru środowiska uruchomieniowego. |
| `baseImageRegistry` | `base_image_registry` | Opcjonalny. Rejestr obrazu zawierający obraz podstawowy. |
| `cudaVersion` | `cuda_version` | Opcjonalna. Wersja CUDA do zainstalowania dla obrazów wymagających obsługi procesora GPU. Obraz procesora GPU musi być używany w usługach Microsoft Azure. Na przykład Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines i Azure Kubernetes Service. Obsługiwane są wersje 9,0, 9,1 i 10,0. Jeśli ustawiono wartość "enable_gpu", wartością domyślną jest "9,1". |
| `description` | `description` |  Opis tego obrazu. |

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