---
title: Szkolenie modeli PyTorch za pomocą usługi Azure Machine Learning
description: Dowiedz się, jak uruchamiać jednym węzłem i rozproszonego szkolenia modeli PyTorch z PyTorch narzędzie do szacowania
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: e569b63f676fb750bcbab88dda6cda39156d41f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977036"
---
# <a name="how-to-train-pytorch-models"></a>Sposób trenowania modeli PyTorch

W przypadku szkolenia sieci neuronowej (DNN) przy użyciu PyTorch, Azure Machine Learning zapewnia klasy PyTorch niestandardowej estymatora. Narzędzie do szacowania zestawu Azure SDK PyTorch można łatwo przesłać zadania szkolenia PyTorch uruchamiany jednym węzłem i rozproszonych obliczeń platformy Azure.

## <a name="single-node-training"></a>Szkolenie z jednym węzłem
Szkolenie przy użyciu narzędzie do szacowania PyTorch jest podobne do [podstawowa narzędzie do szacowania](how-to-train-ml-models.md), więc najpierw przeczytać artykuł porad i upewnij się, zrozumieniu pojęć wprowadzonych w.
  
Aby uruchomić zadanie PyTorch, utworzyć `PyTorch` obiektu. Powinna już utworzono usługi [obliczeniowego elementu docelowego](how-to-set-up-training-targets.md#batch) obiektu `compute_target` i [datastore](how-to-access-data.md) obiektu `ds`.

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
* `source_directory`: Katalog lokalny, który zawiera wszystkie wymagane na potrzeby zadania szkolenia kodu. Ten folder skopiowane z komputera lokalnego do zdalnego obliczeń
* `script_params`: Określanie argumentów wiersza polecenia do skryptu szkolenia słownik `entry_script`, w postaci < argument wiersza polecenia, wartość > par
* `compute_target`: Zdalne zasoby obliczeniowe uruchamianego skrypt szkolenia, w tym przypadku [usługi Batch AI](how-to-set-up-training-targets.md#batch) klastra
* `entry_script`Filepath (względem `source_directory`) skryptu szkolenia, należy uruchomić na zdalne zasoby obliczeniowe. Ten plik i wszelkie dodatkowe pliki, od których zależy, powinny się znajdować w tym folderze
* `conda_packages`: Lista pakietów języka Python, aby ją zainstalować za pomocą narzędzia conda, wymagane przez skrypt szkolenia.
Konstruktor ma inny parametr o nazwie `pip_packages` używanego do dowolnego pakietu pip, wymagane
* `use_gpu`: Ustaw tę flagę `True` wykorzystanie procesora GPU do trenowania. Wartość domyślna to `False`

Ponieważ używasz narzędzie do szacowania PyTorch kontenera, użyty do trenowania będą domyślnie zawierają PyTorch pakietu i powiązane zależności niezbędne do szkolenia z zakresu procesorów oraz procesorów GPU.

Następnie prześlij zadanie PyTorch:
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>Rozproszonego szkolenia
Narzędzie do szacowania PyTorch umożliwia także szkolenie modeli na dużą skalę w klastrach GPU i CPU maszyn wirtualnych platformy Azure. Łatwo można uruchomić rozproszonego szkolenia PyTorch kilka wywołań interfejsu API, natomiast usługa Azure Machine Learning będą zarządzać w tle, infrastruktury i aranżacji potrzebne do wykonania tych obciążeń.

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

Powyższy kod udostępnia następujące nowe parametry do konstruktora PyTorch:
* `node_count`: Liczba węzłów na potrzeby zadania szkolenia. Wartością domyślną tego argumentu `1`
* `process_count_per_node`: Liczba procesów (lub "pracowników przetwarzających"), aby uruchomić w każdym węźle. Wartością domyślną tego argumentu `1`
* `distributed_backend`: Zaplecze do uruchamiania rozproszonych szkoleń, która estymatora oferuje za pośrednictwem MPI. Domyślnie ten argument `None`. Jeśli chcesz przeprowadzić szkolenia równoległego lub rozproszonej (np. `node_count`> 1 lub `process_count_per_node`> 1 lub obie) przy użyciu MPI (i Horovod), należy ustawić `distributed_backend='mpi'`. Implementacja MPI używane przez usługi Azure Machine Learning jest [Otwórz MPI](https://www.open-mpi.org/).

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
Samouczek dotyczący szkolenia PyTorch jednym węzłem zobacz:
* `training/01.train-tune-deploy-pytorch/01.train-tune-deploy-pytorch.ipynb`

Samouczek dotyczący rozproszonego PyTorch z Horovod zobacz:
* `training/02.distributed-pytorch-with-horovod/02.distributed-pytorch-with-horovod.ipynb`

Pobierz te notesy:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki
* [Śledzenie metryk są uruchamiane podczas szkolenia](how-to-track-experiments.md)
* [Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie uczonego modelu](how-to-deploy-and-where.md)
