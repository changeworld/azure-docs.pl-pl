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
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0d5751ab96dc6b44229e2b18b832a570930058ca
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442354"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Szkolenia TensorFlow i Keras modeli za pomocą usługi Azure Machine Learning

Można łatwo uruchamiać zadania szkolenia TensorFlow, mocy obliczeniowej platformy Azure przy użyciu [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) narzędzie do szacowania klasy w zestawie SDK Azure Machine Learning. `TensorFlow` Narzędzie do szacowania Określa, że usługa Azure Machine Learning, aby uruchomić zadanie w kontenerze włączono TensorFlow szkolenia głębokie sieci neuronowych (DNN).

`TensorFlow` Narzędzie do szacowania zapewnia także warstwy abstrakcji przez wykonanie, co oznacza, że można łatwo konfigurować sparametryzowane uruchomień w różnych obliczeniowych elementów docelowych, bez zmiany skryptów szkolenia.

## <a name="get-started"></a>Rozpoczęcie pracy

Ponieważ `TensorFlow` narzędzie do szacowania klasa jest podobna do podstawy [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py), firma Microsoft zaleca pierwszy odczytu [podstawowy artykule narzędzie do szacowania](how-to-train-ml-models.md) można zapoznać się z pojęciami nadrzędna.

Aby rozpocząć pracę z usługą Azure Machine Learning [ukończenie tego przewodnika Szybki Start](quickstart-run-cloud-notebook.md). Po zakończeniu będziesz mieć [obszaru roboczego usługi Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) i wszystkie nasze [przykładowy notesów](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml) tym w zakresie sieci szkolenia TensorFlow i biblioteki Keras.

## <a name="single-node-training"></a>Szkolenie z jednym węzłem

Aby uruchomić zadanie TensorFlow, utworzyć [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) obiektu i przesłać go jako eksperyment.

Poniższy kod tworzy szacowania TensorFlow i przesyła je jako eksperyment. Skrypt szkoleniowy `train.py` zostanie uruchomiona przy użyciu parametrów skryptu. Zadanie będzie uruchamiane na obsługujące procesor GPU [obliczeniowego elementu docelowego](how-to-set-up-training-targets.md)i scikit-Dowiedz się, będzie można zainstalować jako zależność `train.py`.

```Python
from azureml.train.dnn import TensorFlow

# training script parameters passed as command-line arguments
script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

# TensorFlow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    conda_packages=['scikit-learn'],
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Rozproszonego szkolenia

[ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Narzędzie do szacowania obsługuje również rozproszonego szkolenia w klastrach GPU i CPU. Łatwo można uruchamiać rozproszone zadania TensorFlow i usługi Azure Machine Learning będą zarządzać infrastrukturą i organizowania dla siebie.

Usługa Azure Machine Learning obsługuje dwie metody rozproszonego szkolenia w TensorFlow:

* [Na podstawie MPI](https://www.open-mpi.org/) rozproszonego szkolenia przy użyciu [Horovod](https://github.com/uber/horovod) framework
* Natywne [rozproszonych TensorFlow](https://www.tensorflow.org/deploy/distributed) przy użyciu parametru metody serwera

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) to architektura typu open source dla rozproszonego szkolenia opracowany przez Uber. Oferuje ona ścieżką łatwe rozproszone zadania TensorFlow procesora GPU.

Poniższy przykład uruchamia zadanie rozproszonego szkolenia Horovod przy użyciu dwóch procesów roboczych rozproszone między dwoma węzłami.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi', # specifies Horovod backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

Horovod i jego zależności zostaną zainstalowane, dzięki czemu można go zaimportować za pomocą skryptu szkolenia.

```Python
import tensorflow as tf
import horovod
```

### <a name="parameter-server"></a>Serwer parametrów

Można również uruchomić [native rozproszonych TensorFlow](https://www.tensorflow.org/deploy/distributed), parametr modelem serwera, który używa. W przypadku tej metody szkolenie w klastrze serwerów parametru i procesów roboczych. Procesy robocze obliczania gradientów podczas szkolenia, gdy serwery parametr agregacji gradientów.

Poniższy przykład działa zadanie rozproszonego szkolenia, używając parametru metody serwera przy użyciu czterech pracowników rozproszone między dwoma węzłami.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps', # specifies parameter server backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>Uwaga dotycząca `TF_CONFIG`

Należy także adresów sieciowych i portów klastra na potrzeby [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), dzięki czemu usługa Azure Machine Learning ustawia `TF_CONFIG` zmienną środowiskową dla Ciebie.

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

Na wysokim poziomie firmy TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) interfejsu API, TensorFlow będzie analizować to `TF_CONFIG` Specyfikacja zmienną i kompilacji klastra dla Ciebie.

Firmy TensorFlow niższego poziomu podstawowe interfejsy API, szkolenia, można przeanalizować `TF_CONFIG` zmienną i kompilacji `tf.train.ClusterSpec` w kodzie szkolenia. W [w tym przykładzie](https://aka.ms/aml-notebook-tf-ps), dlatego w, należy wykonać **skrypt szkolenia** w następujący sposób:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="keras-support"></a>Obsługa biblioteki Keras

[Biblioteki Keras](https://keras.io/) jest zestawem popularnych wysokiego poziomu DNN Python interfejsu API obsługującego TensorFlow, CNTK i Theano jako zaplecza. Jeśli używasz TensorFlow jako wewnętrznej bazy danych, dodając Keras jest proste i polega na tym `pip_package` parametr konstruktora.

Poniższy przykład tworzy [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) narzędzie do szacowania i przesyła je jako eksperyment. Narzędzie do szacowania uruchamia skrypt szkolenia Keras `keras_train.py`. Zadanie będzie uruchamiane na obsługujące procesor gpu [obliczeniowego elementu docelowego](how-to-set-up-training-targets.md) przy użyciu biblioteki Keras zainstalowany jako zależności za pomocą narzędzia pip.

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py', # relative path to your TensorFlow job
                       pip_packages=['keras'], # add keras through pip
                       use_gpu=True)
```

## <a name="export-to-onnx"></a>Eksportowanie do ONNX

Można pobrać zoptymalizowane wnioskowania o [środowiska uruchomieniowego ONNX](concept-onnx.md), uczonego modelu TensorFlow można przekonwertować do formatu ONNX. Zobacz [przykład](https://github.com/onnx/tensorflow-onnx/blob/master/examples/call_coverter_via_python.py).

## <a name="examples"></a>Przykłady

Można znaleźć przykłady kodu pracy jednym węzłem i rozproszonych wykonań TensorFlow, przy użyciu różnych platform w [naszej stronie GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning).

## <a name="next-steps"></a>Kolejne kroki

* [Śledzenie metryk są uruchamiane podczas szkolenia](how-to-track-experiments.md)
* [Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie uczonego modelu](how-to-deploy-and-where.md)
