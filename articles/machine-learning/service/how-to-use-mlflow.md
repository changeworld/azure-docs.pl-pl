---
title: MLflow za pomocą usługi Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak rejestrować metryki i artefaktów przy użyciu biblioteki MLflow do usługi Azure Machine Learning
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: d0bc4620d0c55d6e94a3d99c39ab405dab2743e5
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461648"
---
# <a name="use-mlflow-with-azure-machine-learning-service-preview"></a>MLflow za pomocą usługi Azure Machine Learning (wersja zapoznawcza)

W tym artykule przedstawiono sposób używania firmy MLflow śledzenia identyfikatora URI i rejestrowanie interfejsu API, zbiorczo znany także jako MLflow śledzenia, za pomocą usługi Azure Machine Learning do śledzenia i rejestrować swoich metryk eksperyment i artefakty w swojej [usługi Azure Machine Learning obszar roboczy usługi](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace). Jeśli używasz już MLflow śledzenia dla eksperymentów, obszar roboczy zawiera scentralizowanego, bezpieczne i skalowalne lokalizację do zapisania swoich metryk szkolenia i modeli.

[MLflow](https://www.mlflow.org) to biblioteka typu open source do zarządzania cyklem życia eksperymentów uczenia maszynowego. [Śledzenie MLFlow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) jest składnikiem MLflow dzienników, która śledzi szkolenia uruchomić metryk i artefaktów do modelu, czy eksperymenty są uruchamiane lokalnie, na maszynie wirtualnej lub na zdalnym klastrze obliczeniowym.
![mlflow przy użyciu usługi azure machine learning diagramu](media/how-to-use-mlflow/mlflow-diagram.png)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Porównaj klientów MLflow i Azure Machine Learning

 Poniższa tabela zawiera podsumowanie różnych klientów używających usługi Azure Machine Learning i możliwości ich odpowiednich funkcji.

 Śledzenie MLflow oferuje rejestrowanie metryki i artefaktu funkcje magazynu, które tylko są dostępne za pośrednictwem [zestawu SDK usługi Azure Machine Learning Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

| | Śledzenie MLflow | Azure Machine Learning <br> Zestaw SDK dla języka Python |  Azure Machine Learning <br> Interfejs wiersza polecenia | Azure Portal|
|-|-|-|-|-|
| Zarządzanie obszaru roboczego |   | ✓ |  ✓ | ✓  |
| Magazyny danych użycia  |   | ✓ |  ✓ |    |
| Rejestruj metryki      | ✓ | ✓ |    |    |
| Przekaż artefakty | ✓ | ✓ |    |    |
| Wyświetlanie metryk     | ✓ | ✓ | ✓  | ✓ |
| Zarządzanie obliczeniami   |   | ✓ | ✓  | ✓ |
| Wdrażanie modeli    |   | ✓ |   ✓ | ✓ |

## <a name="prerequisites"></a>Wymagania wstępne

* [Zainstaluj MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Instalowanie zestawu SDK języka Python usługi Azure Machine Learning na komputerze lokalnym i tworzenie obszaru roboczego usługi Azure Machine Learning](setup-create-workspace.md#sdk). Zestaw SDK udostępnia łączność dla MLflow dostępu do Twojego obszaru roboczego.

## <a name="track-local-runs"></a>Śledzenie lokalnych przebiegów

Zainstaluj `azureml-contrib-run` pakiet w celu użycia MLflow śledzenia za pomocą usługi Azure Machine Learning na eksperymenty lokalnie uruchomić w aplikacji Jupyter Notebook albo edytora kodu.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>Przestrzeń nazw azureml.contrib zmienia się często, gdy będziemy pracować nad usprawnienia świadczonej usługi. Jako takie wszystko w tej przestrzeni nazw powinny być uważane za wersję zapoznawczą i nie są w pełni obsługiwane przez firmę Microsoft.

Importuj `mlflow` i [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) klasy, aby uzyskać dostęp MLflow przez śledzenie identyfikatora URI i konfigurowanie obszaru roboczego.

W poniższym kodzie `get_mlflow_tracking_uri()` metoda przypisuje unikatowy śledzenia adresu URI do obszaru roboczego `ws`, i `set_tracking_uri()` wskazuje MLflow śledzenia identyfikatora URI do tego adresu.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Śledzenie identyfikatora URI jest nieprawidłowy nawet przez godzinę lub mniej. Jeśli skrypt ponownie po pewnym czasie bezczynności, użyj get_mlflow_tracking_uri interfejsu API, aby pobrać nowy identyfikator URI.

Ustaw nazwę eksperymentu MLflow z `set_experiment()` i rozpocząć uruchamianie za pomocą szkolenia `start_run()`. Następnie użyj `log_metric()` do aktywowania interfejs API rejestrowania MLflow i rozpocząć rejestrowanie szkolenia uruchomić metryk.

```Python
experiment_name = "experiment_with_mlflow"
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Śledź zdalnych przebiegów

Zdalnych przebiegów pozwalają uczyć swoje modele na bardziej zaawansowanych obliczeń, takich jak maszyny wirtualne obsługujące procesor GPU lub obliczeniowego usługi Machine Learning klastrów. Zobacz [Konfigurowanie obliczeniowych elementów docelowych do trenowania modelu](how-to-set-up-training-targets.md) Aby dowiedzieć się więcej na temat opcji obliczeniowej.

Konfigurowanie obliczeń i uruchamiania treningowe z [ `Environment` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) klasy. Obejmują `mlflow` i `azure-contrib-run` narzędzia pip pakiety w środowisku firmy [ `CondaDependencies` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) sekcji. Następnie konstruowania [ `ScriptRunConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) za pomocą zdalnego zasobów obliczeniowych jako obliczeniowego elementu docelowego.

```Python

from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = "my_workspace",
                 name = "my_experiment")

mlflow_env = Environment(name="mlflow-env")

cd = CondaDependencies.create(pip_packages = ["mlflow", "azureml-contrib-run"])

mlflow_env.python.conda_dependencies=cd

src = ScriptRunConfig(source_directory="./my_script_location", script="my_training_script.py")

src.run_config.target = "my-remote-compute-compute"
src.run_config.environment = mlflow_env
```

W skrypcie szkolenia, należy zaimportować `mlflow` MLflow rejestrowania interfejsów API i Rozpocznij rejestrowanie wykonywania metryk.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric("example", 1.23)
```

Z mocą obliczeniową i szkoleń, Uruchom konfigurację, użyj `Experiment.submit("train.py")` metodę, aby przesłać przebiegu. To automatycznie ustawia MLflow śledzenia identyfikatora URI i kieruje rejestrowania MLflow obszaru roboczego.

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Wyświetl metryki i artefaktów w obszarze roboczym

Metryki i artefakty MLflow logowania są przechowywane w obszarze roboczym na [witryny Azure portal](https://portal.azure.com). Aby je wyświetlić ilekroć, przejdź do obszaru roboczego, a następnie znajdź eksperyment według nazwy.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie planujesz użyć zarejestrowanych metryk i artefakty w obszarze roboczym, usuwać je pojedynczo jest obecnie niedostępna. Zamiast tego należy usunąć grupę zasobów, która zawiera konta magazynu i w obszarze roboczym, więc nie powodują naliczania żadnych opłat:

1. W witrynie Azure Portal na końcu z lewej strony wybierz pozycję **Grupy zasobów**.

   ![Usuwanie w witrynie Azure Portal](media/how-to-use-mlflow/delete-resources.png)

1. Wybierz utworzoną grupę zasobów z listy.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. Wpisz nazwę grupy zasobów. Następnie wybierz pozycję **Usuń**.

## <a name="example-notebooks"></a>Przykład notesów

[MLflow za pomocą notesów usługi Azure ML](https://aka.ms/azureml-mlflow-examples) pokazuje koncepcji w tym artykule.

## <a name="next-steps"></a>Kolejne kroki

* [Jak wdrożyć model](how-to-deploy-and-where.md).
