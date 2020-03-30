---
title: Integracja git dla usługi Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak usługa Azure Machine Learning integruje się z lokalnym repozytorium Git. Podczas przesyłania przebiegu szkolenia z katalogu lokalnego, który jest repozytorium Git, informacje o repozytorium, gałęzi i bieżącego zatwierdzenia są śledzone w ramach uruchomienia.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402820"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integracja git dla usługi Azure Machine Learning

[Git](https://git-scm.com/) to popularny system kontroli wersji, który pozwala udostępniać i współpracować nad swoimi projektami. 

Usługa Azure Machine Learning w pełni obsługuje repozytoria Git do śledzenia pracy — repozytoria można klonować bezpośrednio w systemie plików udostępnionego obszaru roboczego, używać git na lokalnej stacji roboczej lub używać git z potoku ciągłej integracji/ciągłego wdrażania.

Podczas przesyłania zadania do usługi Azure Machine Learning, jeśli pliki źródłowe są przechowywane w lokalnym repozytorium git, informacje o repozytorium są śledzone w ramach procesu szkoleniowego.

Ponieważ usługa Azure Machine Learning śledzi informacje z lokalnego repozytorium git, nie jest powiązana z żadnym określonym centralnym repozytorium. Twoje repozytorium może być sklonowane z GitHub, GitLab, Bitbucket, Azure DevOps lub dowolnej innej usługi zgodnej z git.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Klonowanie repozytoriów Git do systemu plików obszaru roboczego
Usługa Azure Machine Learning udostępnia udostępniony system plików dla wszystkich użytkowników w obszarze roboczym.
Aby sklonować repozytorium Git w tym udziale plików, zaleca się utworzenie wystąpienia obliczeniowego & otwarcia terminala.
Po otwarciu terminala masz dostęp do pełnego klienta Git i możesz klonować i pracować z Git za pośrednictwem git cli.

Zaleca się sklonowanie repozytorium w katalogu użytkowników, aby inne osoby nie dokonywywykonywają kolizji bezpośrednio w gałęzi działającej.

Możesz sklonować dowolne repozytorium Git, które możesz uwierzytelnić (GitHub, Azure Repos, BitBucket itp.)

Aby uzyskać przewodnik, jak korzystać z Git CLI, przeczytaj [tutaj](https://guides.github.com/introduction/git-handbook/).

## <a name="track-code-that-comes-from-git-repositories"></a>Kod śledzenia pochodzący z repozytoriów Git

Po przesłaniu przebiegu szkolenia z zestawie wiersza polecenia interfejsu użytkownika języka Python lub uczenia maszynowego pliki potrzebne do uczenia modelu są przekazywane do obszaru roboczego. Jeśli `git` polecenie jest dostępne w środowisku programistycznym, proces przekazywania używa go do sprawdzenia, czy pliki są przechowywane w repozytorium git. Jeśli tak, to informacje z repozytorium git są również przesyłane w ramach przebiegu szkoleniowego. Te informacje są przechowywane w następujących właściwościach dla przebiegu szkolenia:

| Właściwość | Polecenie Git używane do uzyskania wartości | Opis |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Identyfikator URI, z których sklonowano repozytorium. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Identyfikator URI, z których sklonowano repozytorium. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Aktywna gałąź podczas przesyłania przebiegu. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Aktywna gałąź podczas przesyłania przebiegu. |
| `azureml.git.commit` | `git rev-parse HEAD` | Skrót zatwierdzenia kodu, który został przesłany do uruchomienia. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Skrót zatwierdzenia kodu, który został przesłany do uruchomienia. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, jeśli gałąź/commit jest zabrudzona; w `false`przeciwnym razie , . |

Te informacje są wysyłane do uruchomień, które używają estymatora, potoku uczenia maszynowego lub uruchomienia skryptu.

Jeśli pliki szkoleniowe nie znajdują się w repozytorium git `git` w środowisku programistycznym lub polecenie nie jest dostępne, żadne informacje związane z git nie są śledzone.

> [!TIP]
> Aby sprawdzić, czy polecenie git jest dostępne w środowisku programistycznym, otwórz sesję powłoki, wiersz polecenia, program PowerShell lub inny interfejs wiersza polecenia i wpisz następujące polecenie:
>
> ```
> git --version
> ```
>
> Jeśli jest zainstalowany i w ścieżce, `git version 2.4.1`otrzymasz odpowiedź podobną do . Aby uzyskać więcej informacji na temat instalowania git w środowisku programistycznym, zobacz [witrynę Git](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Wyświetlanie zarejestrowanych informacji

Informacje git są przechowywane we właściwościach dla przebiegu szkolenia. Można wyświetlić te informacje za pomocą witryny Azure portal, zestaw SDK języka Python i interfejsu wiersza polecenia. 

### <a name="azure-portal"></a>Portal Azure

1. Z [witryny Azure portal](https://portal.azure.com)wybierz swój obszar roboczy.
1. Wybierz __pozycję Eksperymenty__, a następnie wybierz jeden z eksperymentów.
1. Wybierz jeden z przebiegów z kolumny __URUCHOM NUMER.__
1. Wybierz __pozycję Dzienniki__, a następnie rozwiń dzienniki i __wpisy__ __azureml.__ Wybierz łącze, __ ### \___ które zaczyna się od platformy Azure .

    ![Wpis ###_azure w portalu](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

Zarejestrowane informacje zawierają tekst podobny do następującego JSON:

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

Po przesłaniu przebiegu szkolenia, [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) obiekt jest zwracany. Atrybut `properties` tego obiektu zawiera zarejestrowane informacje git. Na przykład następujący kod pobiera skrót zatwierdzania:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>Interfejs wiersza polecenia

Polecenie `az ml run` CLI może służyć do pobierania właściwości z uruchomienia. Na przykład następujące polecenie zwraca właściwości ostatniego uruchomienia w `train-on-amlcompute`eksperymencie o nazwie:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [az ml run.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest)

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie i używanie obiektów docelowych obliczeń do szkolenia modelu](how-to-set-up-training-targets.md)
