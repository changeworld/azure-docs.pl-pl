---
title: Szkolenie modeli za pomocą TensorFlow & Keras
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak uruchamiać jednym węzłem i rozproszonego szkolenia TensorFlow i Keras modele z aplikacjom TensorFlow i Keras
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 78db7d21774750892c831ac220244c54594b78f3
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548358"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Szkolenia TensorFlow i Keras modeli za pomocą usługi Azure Machine Learning

Szkolenia sieci neuronowej (DNN) przy użyciu TensorFlow, Azure Machine Learning zapewnia niestandardowego `TensorFlow` klasy `Estimator`. Zestaw Azure SDK [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) narzędzie do szacowania (nie można być conflated z [ `tf.estimator.Estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator) klasy) umożliwia łatwe przesyłanie zadania szkolenia TensorFlow jednym węzłem i rozproszonych działa na platformie Azure zasobów obliczeniowych.

## <a name="single-node-training"></a>Szkolenie z jednym węzłem
Szkolenie z `TensorFlow` narzędzie do szacowania jest podobne do [podstawowy `Estimator` ](how-to-train-ml-models.md), więc najpierw przeczytać artykuł porad i upewnij się, zrozumieniu pojęć wprowadzonych w.
  
Aby uruchomić zadanie TensorFlow, utworzyć `TensorFlow` obiektu. Powinna już utworzono swoje [obliczeniowego elementu docelowego](how-to-set-up-training-targets.md#amlcompute) obiektu `compute_target`.

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
                    conda_packages=['scikit-learn'], # in case you need scikit-learn in train.py
                    use_gpu=True)
```

W tym miejscu możemy określić następujące parametry do konstruktora TensorFlow:

Parametr | Opis
--|--
`source_directory` | Katalog lokalny, który zawiera wszystkie wymagane na potrzeby zadania szkolenia kodu. Ten folder skopiowane z komputera lokalnego do zdalnego obliczeń
`script_params` | Określanie argumentów wiersza polecenia do skryptu szkolenia słownika `entry_script`, w postaci < argument wiersza polecenia, wartość > pary.  Aby określić pełne flagi w `script_params`, użyj `<command-line argument, "">`.
`compute_target` | Zdalne obliczeniowego elementu docelowego, uruchamianego skrypt szkolenia, w tym przypadku Azure obliczeniowego usługi Machine Learning ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) klastra
`entry_script` | FilePath (względem `source_directory`) skryptu szkolenia, należy uruchomić na zdalne zasoby obliczeniowe. Ten plik i wszelkie dodatkowe pliki, od których zależy, powinny się znajdować w tym folderze
`conda_packages` | Lista pakietów języka Python, aby ją zainstalować za pomocą narzędzia conda, wymagane przez skrypt szkolenia. W takim przypadku używa skrypt szkoleniowy `sklearn` do ładowania danych, zatem Określ tego pakietu do zainstalowania.  Konstruktor ma inny parametr o nazwie `pip_packages` używanego do dowolnego pakietu pip, wymagane
`use_gpu` | Ustaw tę flagę `True` wykorzystanie procesora GPU do trenowania. Wartość domyślna to `False`.

Ponieważ używasz narzędzie do szacowania TensorFlow, domyślnie zostanie kontenera, użyty na potrzeby szkolenia TensorFlow pakietu i powiązane zależności niezbędne do szkolenia z zakresu procesorów oraz procesorów GPU.

Następnie należy przesłać zadania TensorFlow:
```Python
run = exp.submit(tf_est)
```

## <a name="keras-support"></a>Obsługa biblioteki Keras
[Biblioteki Keras](https://keras.io/) to popularne API Python wysokiego poziomu DNN, który obsługuje TensorFlow, CNTK lub Theano jako zaplecza. Jeśli używasz TensorFlow jako wewnętrznej bazy danych, łatwo umożliwia narzędzie do szacowania TensFlow uczenia modelu biblioteki Keras. Oto przykład estymatora TensorFlow, za pomocą biblioteki Keras dodawanych do niego:

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py',
                       pip_packages=['keras'], # just add keras through pip
                       use_gpu=True)
```
Powyższe Konstruktor narzędzie do szacowania TensorFlow powoduje, że usługa Azure Machine Learning do zainstalowania Keras przy użyciu narzędzia pip do środowiska wykonawczego. I `keras_train.py` można następnie zaimportować interfejs API biblioteki Keras, aby uczenia modelu biblioteki Keras. Aby uzyskać kompletny przykład, zapoznaj się z [ten notes Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb).

## <a name="distributed-training"></a>Rozproszonego szkolenia
Narzędzie do szacowania TensorFlow umożliwia także szkolenie modeli na dużą skalę w klastrach GPU i CPU maszyn wirtualnych platformy Azure. Łatwo można uruchomić rozproszonego szkolenia TensorFlow kilka wywołań interfejsu API, natomiast usługa Azure Machine Learning będą zarządzać w tle, infrastruktury i aranżacji potrzebne do wykonania tych obciążeń.

Usługa Azure Machine Learning obsługuje dwie metody rozproszonego szkolenia w TensorFlow:
* Na podstawie MPI rozproszonego szkolenia przy użyciu [Horovod](https://github.com/uber/horovod) framework
* natywne [rozproszonych TensorFlow](https://www.tensorflow.org/deploy/distributed) za pomocą parametru metody serwera

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

Parametr | Opis | Domyślne
--|--|--
`node_count` | Liczba węzłów na potrzeby zadania szkolenia. | `1`
`process_count_per_node` | Liczba procesów (lub "pracowników przetwarzających"), aby uruchomić w każdym węźle.|`1`
`distributed_backend` | Zaplecze dla uruchamiania rozproszonych, szkolenia, która estymatora oferuje za pośrednictwem MPI. Jeśli chcesz przeprowadzić szkolenia równoległego lub rozproszonej (na przykład `node_count`> 1 lub `process_count_per_node`> 1 lub oba) przy użyciu MPI (i Horovod), należy ustawić `distributed_backend='mpi'`. Implementacja MPI używane przez usługi Azure Machine Learning jest [Otwórz MPI](https://www.open-mpi.org/). | `None`

Powyższy przykład zostaną uruchomione rozproszonego szkolenia dwóch pracowników jednego procesu roboczego w każdym węźle.

Horovod wraz z jego zależnościami zostanie zainstalowany, dzięki czemu można go zaimportować za pomocą skryptu szkolenia `train.py` w następujący sposób:

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

Parametr | Opis | Domyślne
--|--|--
`worker_count` | Liczba procesów roboczych. | `1`
`parameter_server_count` | Liczba serwerów parametrów. | `1`
`distributed_backend` | Zaplecze dla rozproszonego szkolenia. Rozproszonego szkolenia za pośrednictwem parametrów serwera, ustaw `distributed_backend='ps'` | `None`

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

Jeśli używasz TensorFlow na wysokim poziomie [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) interfejsu API, TensorFlow będzie analizować to `TF_CONFIG` Specyfikacja zmienną i kompilacji klastra dla Ciebie. 

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

Zapoznaj się z różnymi [notesów na rozproszonej uczenia głębokiego w witrynie Github](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki
* [Śledzenie metryk są uruchamiane podczas szkolenia](how-to-track-experiments.md)
* [Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie uczonego modelu](how-to-deploy-and-where.md)
