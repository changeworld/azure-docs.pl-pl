---
title: Trenuj modele PyTorch do głębokiego uczenia się
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać skrypty szkoleniowe pyTorch w skali przedsiębiorstwa przy użyciu klasy estymatora PyTorch usługi Azure Machine Learning.  Przykładowe skrypty klasyfikują obrazy kurczaka i indyka w celu zbudowania sieci neuronowej uczenia głębokiego na podstawie samouczka uczenia się transferu PyTorcha.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 136ee197271fc659497c169e27a6399c3940c19e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834863"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Trenuj modele uczenia głębokiego Pytorch na dużą skalę dzięki usłudze Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak uruchomić skrypty szkoleniowe [pyTorch](https://pytorch.org/) w skali przedsiębiorstwa przy użyciu klasy [estymatora PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) usługi Azure Machine Learning.  

Przykładowe skrypty w tym artykule są używane do klasyfikowania obrazów kurczaka i indyka do budowania głębokiej sieci neuronowej uczenia się w oparciu o [samouczek](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)pyTorch's transfer uczenia . 

Niezależnie od tego, czy szkolisz model PyTorch uczenia głębokiego od podstaw, czy wprowadzasz istniejący model do chmury, możesz użyć usługi Azure Machine Learning do skalowania w poziomie zadań szkoleniowych typu open source przy użyciu elastycznych zasobów obliczeniowych w chmurze. Za pomocą usługi Azure Machine Learning można tworzyć, wdrażać, wersje i monitorować modele klasy produkcyjnej. 

Dowiedz się więcej o [uczeniu głębokim i uczeniu maszynowym.](concept-deep-learning-vs-machine-learning.md)

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w jednym z tych środowisk:

- Wystąpienie obliczeń usługi Azure Machine Learning — nie jest wymagane pobieranie ani instalacja

    - Ukończ [samouczek: Środowisko instalacji i obszar roboczy,](tutorial-1st-experiment-sdk-setup.md) aby utworzyć serwer dedykowany notebooka wstępnie załadowany za pomocą zestawu SDK i przykładowego repozytorium.
    - W przykładowym folderze uczenia głębokiego na serwerze notesu znajdź ukończony i rozwinięty notes, przechodząc do tego katalogu: **how-to-use-azureml > training-with-deep-learning > train-hyperparameter-tune-deploy-with-pytorch** folder. 
 
 - Twój własny serwer notebooka Jupyter

    - [Zainstaluj zestaw SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Tworzenie pliku konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    - [Pobieranie przykładowych plików skryptów](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Ukończoną wersję tego przewodnika można również znaleźć ukończoną wersję tego przewodnika [w usłudze](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) GitHub. Notes zawiera rozszerzone sekcje obejmujące inteligentne dostrajanie hiperparametryczne, wdrażanie modelu i widżety notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

W tej sekcji konfiguruje eksperyment szkoleniowy, ładując wymagane pakiety języka Python, inicjując obszar roboczy, tworząc eksperyment i przekazując dane szkoleniowe i skrypty szkoleniowe.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj niezbędne biblioteki Języka Python.

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

[Obszar roboczy usługi Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia scentralizowane miejsce do pracy ze wszystkimi artefaktami, które tworzysz. W zestaw SDK języka Python można uzyskać dostęp do [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) artefaktów obszaru roboczego, tworząc obiekt.

Utwórz obiekt obszaru `config.json` roboczego z pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Tworzenie eksperymentu głębokiego uczenia się

Utwórz eksperyment i folder do przechowywania skryptów szkoleniowych. W tym przykładzie utwórz eksperyment o nazwie "pytorch-ptaki".

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Pobieranie danych

Zestaw danych składa się z około 120 obrazów szkoleniowych dla indyków i kurcząt, z 100 obrazami walidacji dla każdej klasy. Pobierzemy i wyodrębnimy zestaw danych w `pytorch_train.py`ramach naszego skryptu szkoleniowego. Obrazy są podzbiorem [zestawu danych Open Images v5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Przygotowywanie skryptów szkoleniowych

W tym samouczku skrypt `pytorch_train.py`szkolenia, , jest już pod warunkiem. W praktyce można wziąć dowolny niestandardowy skrypt szkoleniowy, jak jest i uruchomić go za pomocą usługi Azure Machine Learning.

Prześlij skrypt szkoleniowy `pytorch_train.py`Pytorcha, .

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Jeśli jednak chcesz korzystać z funkcji śledzenia i metryk usługi Azure Machine Learning, musisz dodać mały kod ilości wewnątrz skryptu szkoleniowego. Przykłady śledzenia danych można znaleźć `pytorch_train.py`w pliku .

## <a name="create-a-compute-target"></a>Tworzenie celu obliczeniowego

Utwórz cel obliczeniowy dla zadania PyTorch do uruchomienia. W tym przykładzie utwórz klaster obliczeniowy usługi Azure Machine Learning z obsługą procesora GPU.

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

Aby uzyskać więcej informacji na temat obiektów docelowych obliczeń, zobacz, co jest artykuł [docelowy obliczeń.](concept-compute-target.md)

## <a name="create-a-pytorch-estimator"></a>Tworzenie estymatora PyTorch

[Estymator PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) zapewnia prosty sposób na rozpoczęcie zadania szkoleniowego PyTorch na celu obliczeniowym.

Estymator PyTorch jest implementowany [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) za pośrednictwem klasy ogólnej, która może służyć do obsługi dowolnej struktury. Aby uzyskać więcej informacji na temat modeli szkoleniowych przy użyciu ogólnego estymatora, zobacz [modele pociągów z usługą Azure Machine Learning przy użyciu estymatora](how-to-train-ml-models.md)

Jeśli skrypt szkolenia wymaga dodatkowych pakietów pip lub conda do uruchomienia, można mieć pakiety zainstalowane `pip_packages` `conda_packages` na wynikowy obraz docker, przekazując ich nazwy przez argumenty i.

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

Aby uzyskać więcej informacji na temat dostosowywania środowiska języka Python, zobacz [Tworzenie środowisk szkoleniowych i wdrożeniowych oraz zarządzanie nimi.](how-to-use-environments.md)

## <a name="submit-a-run"></a>Prześlij bieg

[Run Obiekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) udostępnia interfejs do historii uruchamiania, gdy zadanie jest uruchomiony i po jego zakończeniu.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

W miarę wykonywania przebiegu przechodzi przez następujące etapy:

- **Przygotowanie:** Obraz docker jest tworzony zgodnie z estymatorem PyTorch. Obraz jest przekazyany do rejestru kontenerów obszaru roboczego i buforowany dla późniejszych uruchomień. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i można je przeglądać w celu monitorowania postępu.

- **Skalowanie:** Klaster próbuje skalować w górę, jeśli klaster AI partii wymaga więcej węzłów do wykonania uruchomienia niż są obecnie dostępne.

- **Uruchomione**: Wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są montowane lub kopiowane, a entry_script jest wykonywana. Wyjścia z folderu stdout i ./logs są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Post-Processing:** Folder ./outputs przebiegu jest kopiowany do historii uruchamiania.

## <a name="register-or-download-a-model"></a>Zarejestruj lub pobierz model

Po przeszkoleniu modelu można go zarejestrować w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie i przechowywanie modeli w obszarze roboczym w celu uproszczenia [zarządzania modelami i wdrażania.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> Model, który właśnie zarejestrowałeś, jest wdrażany dokładnie w taki sam sposób, jak każdy inny zarejestrowany model w usłudze Azure Machine Learning, niezależnie od tego, który estymator został użyty do szkolenia. Instrukcje wdrażania zawiera sekcję dotyczącą rejestrowania modeli, ale można pominąć bezpośrednio do [tworzenia miejsca docelowego obliczeń](how-to-deploy-and-where.md#choose-a-compute-target) dla wdrożenia, ponieważ masz już zarejestrowany model.

Można również pobrać lokalną kopię modelu przy użyciu Run obiektu. W skrypcie `pytorch_train.py`szkolenia obiekt zapisywania PyTorch utrzymuje model w folderze lokalnym (lokalnym do obiektu docelowego obliczeń). Można użyć Uruchom obiektu, aby pobrać kopię.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Trenowanie rozproszone

Estymator [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) obsługuje również szkolenia rozproszone w klastrach procesorów CPU i GPU. Można łatwo uruchomić rozproszonych zadań PyTorch i usługi Azure Machine Learning będzie zarządzać aranżacji dla Ciebie.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) jest open-source, wszystkie zmniejszyć ramy dla rozproszonych szkoleń opracowanych przez Uber. Oferuje łatwą ścieżkę do rozproszonych zadań GPU PyTorch.

Aby użyć Horovod, [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) należy `distributed_training` określić obiekt dla parametru w konstruktorze PyTorch. Ten parametr gwarantuje, że biblioteka Horovod jest zainstalowana do użycia w skrypcie treningowym.


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
Horovod i jego zależności zostaną zainstalowane dla Ciebie, dzięki czemu `train.py` można zaimportować go w skrypcie szkolenia w następujący sposób:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Eksport do ONNX

Aby zoptymalizować wnioskowanie za pomocą [środowiska uruchomieniowego ONNX,](concept-onnx.md)przekonwertuj przeszkolony model PyTorch na format ONNX. Wnioskowanie lub ocenianie modelu jest fazą, w której wdrożony model jest używany do przewidywania, najczęściej na danych produkcyjnych. Zobacz [przykład samouczka.](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)

## <a name="next-steps"></a>Następne kroki

W tym artykule przeszkolono i zarejestrowałeś głęboką sieć neuronową uczenia głębokiego przy użyciu pyTorch w usłudze Azure Machine Learning. Aby dowiedzieć się, jak wdrożyć model, przejdź do naszego artykułu wdrażania modelu.

> [!div class="nextstepaction"]
> [Jak i gdzie wdrożyć modele](how-to-deploy-and-where.md)
* [Śledzenie danych o przebiegu podczas treningu](how-to-track-experiments.md)
* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie przeszkolonego modelu](how-to-deploy-and-where.md)
* [Architektura referencyjna dla rozproszonych szkoleń głębokiego uczenia na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

