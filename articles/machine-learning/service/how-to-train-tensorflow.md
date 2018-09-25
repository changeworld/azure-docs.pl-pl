---
title: Szkolenia TensorFlow modeli za pomocą usługi Azure Machine Learning
description: Dowiedz się, jak uruchamiać jednym węzłem i rozproszonego szkolenia TensorFlow modeli z TensorFlow narzędzie do szacowania
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: ba43593e90b78aaa0083faf4f8162a7663c0ad47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974225"
---
# <a name="how-to-train-tensorflow-models"></a>Jak szkolenia TensorFlow modeli

W przypadku szkolenia sieci neuronowej (DNN) przy użyciu TensorFlow, Azure Machine Learning zapewnia niestandardowej klasy TensorFlow estymatora. Narzędzie do szacowania zestawu Azure SDK TensorFlow (nie można być conflated z [ `tf.estimator.Estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator) klasy) umożliwia łatwe przesyłanie zadania szkolenia TensorFlow uruchamiany jednym węzłem i rozproszonych obliczeń platformy Azure.

## <a name="single-node-training"></a>Szkolenie z jednym węzłem
Szkolenie przy użyciu narzędzie do szacowania TensorFlow jest podobne do [podstawowa narzędzie do szacowania](how-to-train-ml-models.md), więc najpierw przeczytać artykuł porad i upewnij się, zrozumieniu pojęć wprowadzonych w.
  
Aby uruchomić zadanie TensorFlow, utworzyć `TensorFlow` obiektu. Powinna już utworzono swoje [obliczeniowego elementu docelowego](how-to-set-up-training-targets.md#batch) obiektu `compute_target`.

```Python
from azureml.train.dnn import TensorFlow

script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py',
                    conda_packages=['scikit-learn'],
                    use_gpu=True)
```

W tym miejscu możemy określić następujące parametry do konstruktora TensorFlow:
* `source_directory`: Katalog lokalny, który zawiera wszystkie wymagane na potrzeby zadania szkolenia kodu. Ten folder skopiowane z komputera lokalnego do zdalnego obliczeń
* `script_params`: Określanie argumentów wiersza polecenia do skryptu szkolenia słownik `entry_script`, w postaci < argument wiersza polecenia, wartość > par
* `compute_target`: Zdalne zasoby obliczeniowe uruchamianego skrypt szkolenia, w tym przypadku [usługi Batch AI](how-to-set-up-training-targets.md#batch) klastra
* `entry_script`Filepath (względem `source_directory`) skryptu szkolenia, należy uruchomić na zdalne zasoby obliczeniowe. Ten plik i wszelkie dodatkowe pliki, od których zależy, powinny się znajdować w tym folderze
* `conda_packages`: Lista pakietów języka Python, aby ją zainstalować za pomocą narzędzia conda, wymagane przez skrypt szkolenia. W takim przypadku używa skrypt szkoleniowy `sklearn` do ładowania danych, zatem Określ tego pakietu do zainstalowania.  
Konstruktor ma inny parametr o nazwie `pip_packages` używanego do dowolnego pakietu pip, wymagane
* `use_gpu`: Ustaw tę flagę `True` wykorzystanie procesora GPU do trenowania. Wartość domyślna to `False`.

Ponieważ używasz narzędzie do szacowania TensorFlow, domyślnie zostanie kontenera, użyty na potrzeby szkolenia TensorFlow pakietu i powiązane zależności niezbędne do szkolenia z zakresu procesorów oraz procesorów GPU.

Następnie należy przesłać zadania TensorFlow:
```Python
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Rozproszonego szkolenia
Narzędzie do szacowania TensorFlow umożliwia także szkolenie modeli na dużą skalę w klastrach GPU i CPU maszyn wirtualnych platformy Azure. Łatwo można uruchomić rozproszonego szkolenia TensorFlow kilka wywołań interfejsu API, natomiast usługa Azure Machine Learning będą zarządzać w tle, infrastruktury i aranżacji potrzebne do wykonania tych obciążeń.

Usługa Azure Machine Learning obsługuje dwie metody rozproszonego szkolenia w TensorFlow:
1. Na podstawie MPI rozproszonego szkolenia przy użyciu [Horovod](https://github.com/uber/horovod) framework
2. natywne [rozproszonych TensorFlow](https://www.tensorflow.org/deploy/distributed) za pomocą parametru metody serwera

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) to architektura typu open-source pierścień allreduce dla rozproszonego szkolenia opracowany przez Uber.

Aby uruchomić TensorFlow rozproszonego przy użyciu Horovod framework, Utwórz obiekt TensorFlow w następujący sposób:

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi',
                    use_gpu=True)
```

Powyższy kod udostępnia następujące nowe parametry do konstruktora TensorFlow:
* `node_count`: Liczba węzłów na potrzeby zadania szkolenia. Wartością domyślną tego argumentu `1`
* `process_count_per_node`: Liczba procesów (lub "pracowników przetwarzających"), aby uruchomić w każdym węźle. Wartością domyślną tego argumentu `1`
* `distributed_backend`: Zaplecze do uruchamiania rozproszonych szkoleń, która estymatora oferuje za pośrednictwem MPI. Domyślnie ten argument `None`. Jeśli chcesz przeprowadzić szkolenia równoległego lub rozproszonej (np. `node_count`> 1 lub `process_count_per_node`> 1 lub obie) przy użyciu MPI (i Horovod), należy ustawić `distributed_backend='mpi'`. Implementacja MPI używane przez usługi Azure Machine Learning jest [Otwórz MPI](https://www.open-mpi.org/).

Powyższy przykład zostaną uruchomione rozproszonego szkolenia dwóch pracowników jednego procesu roboczego w każdym węźle.

Horovod wraz z jego zależnościami zostanie zainstalowany, dzięki czemu można po prostu zaimportować go w skrypcie szkolenia `train.py` w następujący sposób:
```Python
import tensorflow as tf
import horovod
```

Na koniec można przesłać zadania TensorFlow:
```Python
run = exp.submit(tf_est)
```

### <a name="parameter-server"></a>Serwer parametrów
Można również uruchomić [native rozproszonych TensorFlow](https://www.tensorflow.org/deploy/distributed), parametr modelem serwera, który używa. W przypadku tej metody szkolenie w klastrze serwerów parametru i procesów roboczych. Procesy robocze obliczania gradientów podczas szkolenia, gdy serwery parametr agregacji gradientów.

Konstrukcja obiektu TensorFlow:
```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps',
                    use_gpu=True)
```

Należy zwrócić uwagę na następujące parametry do konstruktora TensorFlow w powyższym kodzie:
* `worker_count`: Liczba procesów roboczych. Wartością domyślną tego argumentu `1`
* `parameter_server_count`: Liczba serwerów parametrów. Wartością domyślną tego argumentu `1`
* `distributed_backend`: Wewnętrznej bazy danych do użycia dla rozproszonego szkolenia. Domyślnie ten argument `None`. Aby to zrobić rozproszonego szkolenia za pośrednictwem parametrów serwera, musisz ustawić `distributed_backend='ps'`

#### <a name="note-on-tfconfig"></a>Uwaga dotycząca `TF_CONFIG`
Należy również adresów sieciowych i portów klastra na potrzeby [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), dzięki czemu usługa Azure Machine Learning ustawia `TF_CONFIG` zmienną środowiskową dla Ciebie.

`TF_CONFIG` Zmienna środowiskowa jest ciąg JSON. Oto przykład zmiennej serwera parametru:
```
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Jeśli używasz TensorFlow użytkownika wysokiego poziomu [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) interfejsu API, TensorFlow będzie analizować to `TF_CONFIG` Specyfikacja zmienną i kompilacji klastra dla Ciebie. 

Jeśli zamiast tego używasz TensorFlow na niższym poziomie podstawowe interfejsy API szkolenia, należy przeanalizować `TF_CONFIG` zmienną i kompilacji `tf.train.ClusterSpec` samodzielnie w kodzie szkolenia. W [w tym przykładzie](https://aka.ms/aml-notebook-tf-ps), dlatego w, należy wykonać **skrypt szkolenia** w następujący sposób:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

Po zakończeniu pisania skryptu szkolenia i tworzenia obiektu TensorFlow, można przesłać zadania szkolenia:
```Python
run = exp.submit(tf_est)
```

## <a name="examples"></a>Przykłady
Samouczek dotyczący szkolenia TensorFlow jednym węzłem zobacz:
* `training/03.train-tune-deploy-tensorflow/03.train-tune-deploy-tensorflow.ipynb`

Samouczek dotyczący rozproszonego TensorFlow z Horovod zobacz:
* `training/04.distributed-tensorflow-with-horovod/04.distributed-tensorflow-with-horovod.ipynb`

Samouczek dotyczący natywnych TensorFlow rozproszonej zobacz:
* `training/05.distributed-tensorflow-with-parameter-server/05.distributed-tensorflow-with-parameter-server.ipynb`

Pobierz te notesy:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki
* [Śledzenie metryk są uruchamiane podczas szkolenia](how-to-track-experiments.md)
* [Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie uczonego modelu](how-to-deploy-and-where.md)
