---
title: Szkolenie i zarejestruj modele Chainer
titleSuffix: Azure Machine Learning service
description: W tym artykule pokazano, jak szkolenie i zarejestrować model Chainer przy użyciu usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 06/15/2019
ms.openlocfilehash: 8ecefccbdf5f02652e935858b6ae8fb4cdfde640
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840034"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning-service"></a>Szkolenie i zarejestruj Chainer modeli na skalę przy użyciu usługi Azure Machine Learning

W tym artykule pokazano, jak szkolenie i zarejestrować model Chainer przy użyciu usługi Azure Machine Learning. Używa ona popularnej [zestawu danych mnist ręcznie ZAPISANYCH](http://yann.lecun.com/exdb/mnist/) do klasyfikowania cyfr pisma odręcznego za pomocą sieci neuronowej (DNN) utworzonych przy użyciu [biblioteki Chainer Python](https://Chainer.org) uruchomione w górnej części [numpy](https://www.numpy.org/).

Chainer jest ogólny interfejs API umożliwiający uruchomienie na górze innych popularnych struktur DNN można Uproszczenie projektowania aplikacji w sieci neuronowych. Za pomocą usługi Azure Machine Learning można szybko skalować w poziomie zadań szkoleniowych przy użyciu zasobów obliczeniowych w elastycznej chmurze. Można także śledzić swoje przebiegów szkoleniowych, modele wersji wdrażanie modeli i wiele więcej.

Czy tworzysz od podstaw modelu Chainer korzystamy istniejącego modelu w chmurze, usługi Azure Machine Learning mogą pomóc tworzyć modele gotowe do produkcji.

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z tych środowisk:

- Notesu platformy Azure Machine Learning maszyny Wirtualnej — bez plików do pobrania i instalacji konieczne

    - Wykonaj [Szybki Start oparte na chmurze notesu](quickstart-run-cloud-notebook.md) utworzyć dedykowany serwer wstępnie załadowane z zestawu SDK i przykładowe repozytorium.
    - W folderze samples na serwerze Notes, Znajdź notesu ukończone i plików w **how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer** folderu.  Notes zawiera rozwinięte sekcje, obejmujące strojenia hiperparametrycznego inteligentnego, wdrażanie modelu i widżetów notesu.

- Serwer notesu programu Jupyter

    - [Zainstaluj aplikację Azure Machine Learning zestawu SDK dla języka Python](setup-create-workspace.md#sdk)
    - [Utwórz plik konfiguracji obszaru roboczego](setup-create-workspace.md#write-a-configuration-file)
    - Pobierz przykładowy plik skryptu [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py)
     - Możesz również znaleźć ukończone [wersji notesu programu Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) tego przewodnika stronę przykładów usługi GitHub. Notes zawiera rozwinięte sekcje, obejmujące strojenia hiperparametrycznego inteligentnego, wdrażanie modelu i widżetów notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

W tej sekcji konfiguruje eksperymentu szkolenia ładowania pakietów wymaganych języka python, inicjowanie obszar roboczy, tworzenia eksperymentu i przekazywanie danych szkoleniowych i skryptów szkolenia.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj azureml.core wyświetlania ad biblioteki Python numer wersji.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszaru roboczego usługi Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Udostępnia scentralizowanym miejscem do pracy z wszystkich artefaktów, które można utworzyć. W zestawu SDK języka Python, uzyskujesz dostęp artefaktów obszaru roboczego, tworząc [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) obiektu.

Utwórz obiekt obszaru roboczego z `config.json` pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Utwórz katalog projektu
Utwórz katalog, który będzie zawierać niezbędny kod z komputera lokalnego, że będziesz potrzebować dostępu do zdalnego zasobu. Obejmuje to skrypt szkolenia i wszelkie dodatkowe pliki, od którego zależy skrypt szkolenia.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Przygotuj skrypt szkoleniowy

W tym samouczku skrypt szkoleniowy **chainer_mnist.py** poprawiał dla Ciebie. W praktyce powinien mieć możliwość wykonaj dowolny skrypt niestandardowy szkolenia i uruchom go z usługi uczenie Maszynowe Azure bez konieczności modyfikowania kodu.

Aby korzystać z usługi Azure ML funkcje śledzenia i metryki, będą musieli dodać niewielką ilość kodu usługi Azure ML wewnątrz skrypt szkolenia.  Skrypt szkoleniowy **chainer_mnist.py** pokazuje, jak zalogować się do usługi Azure ML uruchomić niektóre metryki. Aby to zrobić, możesz uzyskać dostęp usługi Azure ML `Run` obiektu w skrypcie.

Skopiuj skrypt szkolenia **chainer_mnist.py** w katalogu projektu.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Tworzenie eksperymentów i folder do przechowywania skryptów szkolenia. W tym przykładzie należy utworzyć eksperyment, o nazwie "chainer-mnist ręcznie zapisanych".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Utworzyć lub uzyskać obliczeniowego elementu docelowego

Konieczne będzie [obliczeniowego elementu docelowego](concept-compute-target.md) do trenowania modelu. W tym samouczku użyjesz środowiska obliczeniowego usługi Azure ML, zarządzane (AmlCompute) zasobu obliczeniowego zdalnego szkolenia.

**Tworzenie AmlCompute zajmuje około 5 minut**. Jeśli AmlCompute o tej nazwie jest już w obszarze roboczym, ten kod pozwoli na pominięcie procesu tworzenia.  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

Aby uzyskać więcej informacji na temat obliczeniowych elementów docelowych, zobacz [co to jest cel obliczenia](concept-compute-target.md) artykułu.

## <a name="create-a-chainer-estimator"></a>Tworzenie szacowania Chainer

[Chainer narzędzie do szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) zapewnia prosty sposób uruchamiania zadań szkoleniowych Chainer na obliczeniowego elementu docelowego.

Narzędzie do szacowania Chainer jest implementowane za pomocą ogólnego [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klasy, która może służyć do obsługi dowolnej platformy. Aby uzyskać więcej informacji na temat szkolenie modeli za pomocą ogólnego narzędzie do szacowania zobacz [uczenia modeli za pomocą usługi Azure Machine Learning przy użyciu narzędzie do szacowania](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>Prześlij przebiegu

[Przebiegu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) zapewnia interfejs do historii uruchamiania podczas uruchamiania zadania, a po jego ukończeniu.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Uruchomienie jest wykonywane, przechodzi on przez następujące etapy:

- **Trwa przygotowywanie**: Obraz platformy docker są tworzone zgodnie ze skonfigurowanym Chainer narzędzie do szacowania. Obraz, który jest przekazany do obszaru roboczego container registry i nowszych przebiegów w pamięci podręcznej. Dzienniki również są przesyłane strumieniowo do historii uruchamiania i mogą być wyświetlane w celu monitorowania postępu.

- **Skalowanie**: Klaster polegające na skalowanie w górę, jeśli klaster usługi Batch AI wymaga więcej węzłów do wykonania przebiegu nie są obecnie dostępne.

- **Uruchamianie**: Wszystkie skrypty w folderze skryptu są przekazywane do obliczeniowego elementu docelowego, magazyny danych są zainstalowane lub kopiowane i entry_script jest wykonywany. Dane wyjściowe ze strumienia wyjściowego stdout i. / folderu dzienników są przesyłane strumieniowo do historii uruchamiania i może służyć do monitorowania przebiegu.

- **Przetwarzanie końcowe**: . / Wyjścia, folder przebiegu jest kopiowana do historii uruchamiania.

## <a name="save-and-register-the-model"></a>Zapisz i zarejestrować model

Gdy już uczony model, można zapisać i zarejestruj go do obszaru roboczego. Rejestracja modelu pozwala magazynu i wersji modeli w Twoim obszarze roboczym, aby uprościć [zarządzania i wdrażania modelu](concept-model-management-and-deployment.md).

Dodaj następujący kod do skryptu szkolenia, **chainer_mnist.py**, aby zapisać modelu. 

``` Python
    serializers.save_npz(os.path.join(args.output_dir, 'model.npz'), model)
```

Zarejestruj model do swojego obszaru roboczego z następującym kodem.

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```



## <a name="next-steps"></a>Kolejne kroki

W tym artykule szkolenia modelu Chainer w usłudze Azure Machine Learning. 

* Aby dowiedzieć się, jak wdrożyć model, przejdź do naszego [modelu wdrażania](how-to-deploy-and-where.md) artykułu.

* [Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md)

* [Śledzenie metryk są uruchamiane podczas szkolenia](how-to-track-experiments.md)
