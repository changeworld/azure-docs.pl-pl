---
title: Integracja z usługą git dla Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób Azure Machine Learning integruje się z lokalnym repozytorium git.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 10/11/2019
ms.openlocfilehash: db96663ef3d901546e1b32362a9eb9c9ae09dd21
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377514"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integracja z usługą git dla Azure Machine Learning

[Git](https://git-scm.com/) to popularny system kontroli wersji, który umożliwia udostępnianie projektów i współpracę z nimi. W przypadku przesłania zadania szkoleniowego do Azure Machine Learning, jeśli pliki szkoleniowe są przechowywane w lokalnym repozytorium git, informacje o repozytorium są śledzone w ramach procesu szkoleniowego.

Ponieważ Azure Machine Learning śledzi informacje z lokalnego repozytorium git, nie jest ono powiązane z żadnym określonym centralnym repozytorium. Repozytorium można sklonować z witryny GitHub, GitLab, BitBucket, Azure DevOps lub dowolnej innej usługi zgodnej z Git.

## <a name="how-does-git-integration-work"></a>Jak działa integracja z usługą git?

Po przesłaniu szkolenia z zestawu SDK języka Python lub interfejsu wiersza polecenia Machine Learning pliki potrzebne do uczenia modelu są przekazywane do obszaru roboczego. Jeśli `git` polecenie jest dostępne w środowisku deweloperskim, proces przekazywania używa go do sprawdzenia, czy pliki są przechowywane w repozytorium git. Jeśli tak, to w ramach przebiegu szkoleniowego zostanie również przekazane informacje z repozytorium git. Te informacje są przechowywane w następujących właściwościach przebiegu szkoleniowego:

| Właściwość | Opis |
| ----- | ----- |
| `azureml.git.repository_uri` | Identyfikator URI, z którego zostało Sklonowane repozytorium. |
| `mlflow.source.git.repoURL` | Identyfikator URI, z którego zostało Sklonowane repozytorium. |
| `azureml.git.branch` | Aktywna gałąź podczas przesyłania przebiegu. |
| `mlflow.source.git.branch` | Aktywna gałąź podczas przesyłania przebiegu. |
| `azureml.git.commit` | Skrót zatwierdzenia kodu, który został przesłany dla uruchomienia. |
| `mlflow.source.git.commit` | Skrót zatwierdzenia kodu, który został przesłany dla uruchomienia. |
| `azureml.git.dirty` | `True`, jeśli zatwierdzenie jest zanieczyszczone; w przeciwnym razie `false`. |

Te informacje są wysyłane do przebiegów korzystających z szacowania, potoku uczenia maszynowego lub uruchamiania skryptów.

Jeśli pliki szkoleniowe nie znajdują się w repozytorium Git w środowisku deweloperskim lub polecenie `git` nie jest dostępne, nie są śledzone żadne informacje związane z usługą git.

## <a name="view-the-logged-information"></a>Wyświetlanie zarejestrowanych informacji

Informacje dotyczące usługi git są przechowywane we właściwościach przebiegu szkoleniowego. Te informacje można wyświetlić przy użyciu Azure Portal, zestawu Python SDK i interfejsu wiersza polecenia. 

### <a name="azure-portal"></a>Azure Portal

1. Na [Azure Portal](https://portal.azure.com)wybierz swój obszar roboczy.
1. Wybierz pozycję __eksperymenty__, a następnie wybierz jedno z eksperymentów.
1. Wybierz jeden z przebiegów z kolumny __numer uruchomienia__ .
1. Wybierz pozycję __dzienniki__, a następnie rozwiń pozycje __dzienniki__ i __Azure__ . Wybierz łącze zaczynające się od __### @ no__t-2azure__.

Zarejestrowane informacje zawierają tekst podobny do następującego:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Zestaw SDK dla języka Python

Po przesłaniu przebiegu szkoleniowego zwracany jest obiekt [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) . Atrybut `properties` tego obiektu zawiera zarejestrowane informacje usługi git. Na przykład poniższy kod pobiera skrót zatwierdzenia:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>Interfejs CLI

Polecenie interfejsu wiersza polecenia `az ml run` może służyć do pobierania właściwości z przebiegu. Na przykład następujące polecenie zwraca właściwości dla ostatniego uruchomienia w eksperymentie o nazwie `train-on-amlcompute`:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją [AZ ml Run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) Reference.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z przewodnikiem dotyczącym uczenia się z Azure Machine Learning w Visual Studio Code, zobacz [Samouczek: uczenie modeli przy użyciu Azure Machine Learning](tutorial-train-models-with-aml.md).
* Aby zapoznać się z przewodnikiem dotyczącym sposobu edytowania, uruchamiania i debugowania kodu lokalnie, zobacz [samouczek Python Hello-World](https://code.visualstudio.com/docs/Python/Python-tutorial).
