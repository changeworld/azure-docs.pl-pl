---
title: Szkolenie i zarejestruj modele PyTorch
titleSuffix: Azure Machine Learning service
description: W tym artykule pokazano, jak szkolenie i zarejestrować model PyTorch przy użyciu usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: fc80fcde8de3fb2d6dd6f59804f6019b76aa8727
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295592"
---
# <a name="train-and-register-pytorch-models-at-scale-with-azure-machine-learning-service"></a>Szkolenie i zarejestruj PyTorch modeli na skalę przy użyciu usługi Azure Machine Learning

W tym artykule pokazano, jak szkolenie i zarejestrować model PyTorch przy użyciu usługi Azure Machine Learning. Opiera się na [transferu PyTorch firmy learning samouczek](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) opiera się Klasyfikator sieci neuronowej (DNN) dla obrazów Mrówki i odniesieniu.

[PyTorch](https://pytorch.org/) to platforma obliczeniowa typu open-source najczęściej używany do tworzenia głębokich sieciach neuronowych (DNN). Za pomocą usługi Azure Machine Learning można szybko skalować w poziomie zadania szkoleń o całkowicie otwartym kodzie źródłowym, przy użyciu zasobów obliczeniowych w elastycznej chmurze. Można także śledzić swoje przebiegów szkoleniowych, modele wersji wdrażanie modeli i wiele więcej.

Czy tworzysz od podstaw modelu PyTorch korzystamy istniejącego modelu w chmurze, usługi Azure Machine Learning mogą pomóc tworzyć modele gotowe do produkcji.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z tych środowisk:

 - Notesu platformy Azure Machine Learning maszyny Wirtualnej — bez plików do pobrania i instalacji konieczne

    - Wykonaj [Szybki Start oparte na chmurze notesu](quickstart-run-cloud-notebook.md) utworzyć dedykowany serwer wstępnie załadowane z zestawu SDK i przykładowe repozytorium.
    - W folderze samples na serwerze Notes, należy znaleźć notesu ukończonych oraz rozszerzone, przechodząc do tego katalogu: **jak-to-użyj-usługi Azure ml > szkolenia z głębokiego uczenia > train-hyperparameter-tune-deploy-with-pytorch** folder. 
 
 - Serwer notesu programu Jupyter

    - [Zainstaluj aplikację Azure Machine Learning zestawu SDK dla języka Python](setup-create-workspace.md#sdk)
    - [Utwórz plik konfiguracji obszaru roboczego](setup-create-workspace.md#write-a-configuration-file)
    - [Pobieranie plików przykładowych skryptów](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    Możesz również znaleźć ukończone [wersji notesu programu Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) tego przewodnika stronę przykładów usługi GitHub. Notes zawiera rozwinięte sekcje, obejmujące strojenia hiperparametrycznego inteligentnego, wdrażanie modelu i widżetów notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

W tej sekcji konfiguruje eksperymentu szkolenia ładowania pakietów wymaganych języka python, inicjowanie obszar roboczy, tworzenia eksperymentu i przekazywanie danych szkoleniowych i skryptów szkolenia.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj niezbędne biblioteki języka Python.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszaru roboczego usługi Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Udostępnia scentralizowanym miejscem do pracy z wszystkich artefaktów, które można utworzyć. W zestawu SDK języka Python, uzyskujesz dostęp artefaktów obszaru roboczego, tworząc [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) obiektu.

Utwórz obiekt obszaru roboczego z `config.json` pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Tworzenie eksperymentów i folder do przechowywania skryptów szkolenia. W tym przykładzie należy utworzyć eksperyment, o nazwie "pytorch hymenoptera".

```Python
project_folder = './pytorch-hymenoptera'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-hymenoptera'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Pobieranie danych

Zestaw danych składa się z około 120 uczone obrazy każdego Mrówki i odniesieniu, przy użyciu 75 obrazów sprawdzania poprawności dla każdej klasy. Hymenoptera polega na kolejności owadami obejmuje Mrówki i odniesieniu. Pobieranie i wyodrębnianie zestawu danych jako część naszego skryptu szkolenia `pytorch_train.py`.

### <a name="prepare-training-scripts"></a>Przygotowanie skryptów szkolenia

W tym samouczku skrypt szkolenia `pytorch_train.py`, znajduje się już. W praktyce można wykonać dowolny skrypt niestandardowych szkoleń, ponieważ jest i uruchomić go za pomocą usługi Azure Machine Learning.

Przekaż skrypt szkolenia Pytorch, `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Jednak jeśli chcesz użyć śledzenia usługi Azure Machine Learning i możliwości metryk, należy dodać kod małą ilością wewnątrz skrypt szkolenia. Przykładowe metryki śledzenia znajdują się w `pytorch_train.py`.

## <a name="create-a-compute-target"></a>Utworzyć cel obliczenia

Utworzyć obliczeniowego elementu docelowego dla zadania PyTorch systemem. W tym przykładzie należy utworzyć klaster obliczeniowy włączonymi procesorami GPU z usługi Azure Machine Learning.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Aby uzyskać więcej informacji na temat obliczeniowych elementów docelowych, zobacz [co to jest cel obliczenia](concept-compute-target.md) artykułu.

## <a name="create-a-pytorch-estimator"></a>Tworzenie szacowania PyTorch

[PyTorch narzędzie do szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) zapewnia prosty sposób uruchamiania zadania szkolenia PyTorch na obliczeniowego elementu docelowego.

Narzędzie do szacowania PyTorch jest implementowane za pomocą ogólnego [ `estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klasy, która może służyć do obsługi dowolnej platformy. Aby uzyskać więcej informacji na temat szkolenie modeli za pomocą ogólnego narzędzie do szacowania zobacz [uczenia modeli za pomocą usługi Azure Machine Learning przy użyciu narzędzie do szacowania](how-to-train-ml-models.md)

Jeśli dodatkowe narzędzia pip lub conda pakietów, aby uruchomić skrypt szkolenia, może mieć zainstalowanego na Wynikowy obraz platformy docker, przekazując ich nazwiska za pomocą pakietu `pip_packages` i `conda_packages` argumentów.

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

## <a name="submit-a-run"></a>Prześlij przebiegu

[Przebiegu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) zapewnia interfejs do historii uruchamiania podczas uruchamiania zadania, a po jego ukończeniu.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Uruchomienie jest wykonywane, przechodzi on przez następujące etapy:

- **Trwa przygotowywanie**: Obraz platformy docker są tworzone zgodnie ze skonfigurowanym PyTorch narzędzie do szacowania. Obraz, który jest przekazany do obszaru roboczego container registry i nowszych przebiegów w pamięci podręcznej. Dzienniki również są przesyłane strumieniowo do historii uruchamiania i mogą być wyświetlane w celu monitorowania postępu.

- **Skalowanie**: Klaster polegające na skalowanie w górę, jeśli klaster usługi Batch AI wymaga więcej węzłów do wykonania przebiegu nie są obecnie dostępne.

- **Uruchamianie**: Wszystkie skrypty w folderze skryptu są przekazywane do obliczeniowego elementu docelowego, magazyny danych są zainstalowane lub kopiowane i entry_script jest wykonywany. Dane wyjściowe ze strumienia wyjściowego stdout i. / folderu dzienników są przesyłane strumieniowo do historii uruchamiania i może służyć do monitorowania przebiegu.

- **Przetwarzanie końcowe**: . / Wyjścia, folder przebiegu jest kopiowana do historii uruchamiania.

## <a name="register-or-download-a-model"></a>Zarejestruj lub pobrać modelu

Gdy już uczony model, można zarejestrować go do obszaru roboczego. Rejestracja modelu pozwala magazynu i wersji modeli w Twoim obszarze roboczym, aby uprościć [zarządzania i wdrażania modelu](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

Można również pobrać kopię lokalną modelu przy użyciu obiektu przebiegu. W skrypcie szkolenia `pytorch_train.py`, PyTorch Zapisz obiekt będzie się powtarzał modelu do folderu lokalnego (local do obliczeniowego elementu docelowego). Aby pobrać kopię, można użyć obiektu przebiegu.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Rozproszonego szkolenia

[ `PyTorch` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) Narzędzie do szacowania obsługuje również rozproszonego szkolenia w klastrach GPU i CPU. Łatwo można uruchamiać rozproszone zadania PyTorch i usługi Azure Machine Learning będą zarządzać aranżacji dla Ciebie.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) jest typu open source, wszystkie zmniejszyć framework dla rozproszonego szkolenia opracowany przez Uber. Oferuje ona ścieżką łatwe rozproszone zadania PyTorch procesora GPU.

Aby użyć Horovod, określ [ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) dla obiektu `distributed_training` parametr w Konstruktorze PyTorch. Tego parametru gwarantuje zainstalowanie biblioteki Horovod do użycia w skrypcie szkolenia.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod wraz z jego zależnościami zostanie zainstalowany, dzięki czemu można go zaimportować za pomocą skryptu szkolenia `train.py` w następujący sposób:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Eksportowanie do ONNX

Aby zoptymalizować wnioskowania o [środowiska uruchomieniowego ONNX](concept-onnx.md), przekonwertować uczonego modelu PyTorch do formatu ONNX. Wnioskowanie lub oceniania modelu, jest faza użycia wdrożony model do przewidywania najczęściej w danych produkcyjnych. Zobacz [samouczek](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) przykład.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przeszkolonych i zarejestrowany PyTorch modelu w usłudze Azure Machine Learning. Aby dowiedzieć się, jak wdrożyć model, przejdź do naszego artykułu wdrażania modelu.

> [!div class="nextstepaction"]
> [Jak i gdzie umożliwia wdrażanie modeli](how-to-deploy-and-where.md)
* [Śledzenie metryk są uruchamiane podczas szkolenia](how-to-track-experiments.md)
* [Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie uczonego modelu](how-to-deploy-and-where.md)
