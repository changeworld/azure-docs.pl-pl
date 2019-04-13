---
title: Szkolenie modeli za pomocą PyTorch
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak uruchamiać jednym węzłem i rozproszonego szkolenia modeli PyTorch z PyTorch narzędzie do szacowania
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 9ae7795381f036bb819ce24554d8cea94ceb5552
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548555"
---
# <a name="train-pytorch-models-with-azure-machine-learning-service"></a>Szkolenie modeli PyTorch za pomocą usługi Azure Machine Learning

Za pomocą PyTorch szkolenia sieci neuronowej (DNN), usługi Azure Machine Learning zapewnia niestandardowego [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) klasy `Estimator`. Zestaw Azure SDK `PyTorch` narzędzie do szacowania umożliwia łatwe przesyłanie zadania szkolenia PyTorch uruchamiany jednym węzłem i rozproszonych obliczeń platformy Azure.

## <a name="single-node-training"></a>Szkolenie z jednym węzłem
Szkolenie z `PyTorch` narzędzie do szacowania jest podobne do [podstawowy `Estimator` ](how-to-train-ml-models.md), więc najpierw przeczytać artykuł porad i upewnij się, zrozumieniu pojęć wprowadzonych w.
  
Aby uruchomić zadanie PyTorch, utworzyć `PyTorch` obiektu. Powinna już utworzono usługi [obliczeniowego elementu docelowego](how-to-set-up-training-targets.md#amlcompute) obiektu `compute_target` i [datastore](how-to-access-data.md) obiektu `ds`.

```Python
from azureml.train.dnn import PyTorch

script_params = {
    '--data_dir': ds
}

pt_est = PyTorch(source_directory='./my-pytorch-proj',
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='train.py',
                 use_gpu=True)
```

W tym miejscu możemy określić następujące parametry do konstruktora PyTorch:

Parametr | Opis
--|--
`source_directory` |  Katalog lokalny, który zawiera wszystkie wymagane na potrzeby zadania szkolenia kodu. Ten folder skopiowane z komputera lokalnego do zdalnego obliczeń
`script_params` |  Określanie argumentów wiersza polecenia do skryptu szkolenia słownika `entry_script`, w postaci < argument wiersza polecenia, wartość > pary.  Aby określić pełne flagi w `script_params`, użyj `<command-line argument, "">`.
`compute_target` |  Zdalne obliczeniowego elementu docelowego, uruchamianego skrypt szkolenia, w tym przypadku Azure obliczeniowego usługi Machine Learning ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) klastra
`entry_script` |  FilePath (względem `source_directory`) skryptu szkolenia, należy uruchomić na zdalne zasoby obliczeniowe. Ten plik i wszelkie dodatkowe pliki, od których zależy, powinny się znajdować w tym folderze
`conda_packages` |  Lista pakietów języka Python, aby ją zainstalować za pomocą narzędzia conda, wymagane przez skrypt szkolenia. Konstruktor ma inny parametr o nazwie `pip_packages` używanego do dowolnego pakietu pip, wymagane
`use_gpu` |  Ustaw tę flagę `True` wykorzystanie procesora GPU do trenowania. Wartość domyślna to `False`

Ponieważ używasz `PyTorch` narzędzie do szacowania, będzie zawierać kontenera, użyty na potrzeby szkolenia, pakiet PyTorch i powiązane zależności niezbędne do szkolenia z zakresu procesorów oraz procesorów GPU.

Następnie prześlij zadanie PyTorch:
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>Rozproszonego szkolenia
`PyTorch` Narzędzie do szacowania pozwala również na szkolenie modeli na dużą skalę w klastrach GPU i CPU maszyn wirtualnych platformy Azure. Łatwo można uruchomić rozproszonego szkolenia PyTorch kilka wywołań interfejsu API, natomiast usługa Azure Machine Learning będą zarządzać w tle, infrastruktury i aranżacji potrzebne do wykonania tych obciążeń.

Usługa Azure Machine Learning obsługuje obecnie na podstawie MPI dla rozproszonego szkolenia w PyTorch przy użyciu Horovod framework.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) to architektura typu open-source pierścień allreduce dla rozproszonego szkolenia opracowany przez Uber.

Aby uruchomić PyTorch rozproszonego przy użyciu Horovod framework, Utwórz obiekt PyTorch w następujący sposób:

```Python
from azureml.train.dnn import PyTorch

pt_est = PyTorch(source_directory='./my-pytorch-project',
                 script_params={},
                 compute_target=compute_target,
                 entry_script='train.py',
                 node_count=2,
                 process_count_per_node=1,
                 distributed_backend='mpi',
                 use_gpu=True)
```

Ten kod udostępnia następujące nowe parametry do konstruktora PyTorch:

Parametr | Opis | Domyślne
--|--|--
`node_count` |  Liczba węzłów na potrzeby zadania szkolenia. | `1`
`process_count_per_node` |  Liczba procesów (lub "pracowników przetwarzających"), aby uruchomić w każdym węźle. | `1`
`distributed_backend` |  Zaplecze dla uruchamiania rozproszonych, szkolenia, która estymatora oferuje za pośrednictwem MPI.  Przeprowadzenie szkolenia równoległego lub rozproszonej (np. `node_count`> 1 lub `process_count_per_node`> 1 lub oba) przy użyciu MPI (i Horovod), należy ustawić `distributed_backend='mpi'`. Implementacja MPI używane przez usługi Azure Machine Learning jest [Otwórz MPI](https://www.open-mpi.org/). | `None`

Powyższy przykład zostaną uruchomione rozproszonego szkolenia dwóch pracowników jednego procesu roboczego w każdym węźle.

Horovod wraz z jego zależnościami zostanie zainstalowany, dzięki czemu można po prostu zaimportować go w skrypcie szkolenia `train.py` w następujący sposób:
```Python
import torch
import horovod
```

Na koniec przesyłania zadania PyTorch rozproszonych:
```Python
run = exp.submit(pt_est)
```

## <a name="examples"></a>Przykłady

Notesy w rozproszonej uczenia głębokiego zobacz:
* [How-to-use-azureml/Training-with-deep-Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki
* [Śledzenie metryk są uruchamiane podczas szkolenia](how-to-track-experiments.md)
* [Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie uczonego modelu](how-to-deploy-and-where.md)
