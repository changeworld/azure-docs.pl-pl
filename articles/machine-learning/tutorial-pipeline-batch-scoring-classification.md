---
title: 'Samouczek: Potoki ml do oceniania partii'
titleSuffix: Azure Machine Learning
description: W tym samouczku tworzysz potok uczenia maszynowego do wykonywania oceniania partii na modelu klasyfikacji obrazu. Usługa Azure Machine Learning umożliwia skupienie się na uczeniu maszynowym zamiast na infrastrukturze i automatyzacji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: laobri
ms.date: 03/11/2020
ms.openlocfilehash: 1ccd7a7f33c6ee5cab8b7173d8eb93365b6cb587
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472224"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Samouczek: Tworzenie potoku usługi Azure Machine Learning do oceniania wsadowego

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak utworzyć potok w usłudze Azure Machine Learning, aby uruchomić zadanie oceniania wsadowego. Potoki uczenia maszynowego optymalizują przepływ pracy z szybkością, przenośnością i ponownym użyciem, dzięki czemu można skupić się na uczeniu maszynowym zamiast na infrastrukturze i automatyzacji. Po utworzeniu i opublikowaniu potoku można skonfigurować punkt końcowy REST, którego można użyć do wyzwolenia potoku z dowolnej biblioteki HTTP na dowolnej platformie. 

W przykładzie użyto wstępnie przeszkolonego modelu sieci [neuronowej Inception-V3](https://arxiv.org/abs/1512.00567) zaimplementowanego w Tensorflow do klasyfikowania obrazów bez etykiety. [Dowiedz się więcej o potokach uczenia maszynowego](concept-ml-pipelines.md).

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Konfigurowanie obszaru roboczego 
> * Pobieranie i przechowywanie przykładowych danych
> * Tworzenie obiektów zestawu danych do pobierania i wyprowadzania danych
> * Pobieranie, przygotowywanie i rejestrowanie modelu w obszarze roboczym
> * Aprowizuj obiekty docelowe obliczeń i tworzenie skryptu oceniania
> * Użyj `ParallelRunStep` klasy do oceniania partii asynchronii
> * Tworzenie, uruchamianie i publikowanie potoku
> * Włączanie punktu końcowego REST dla potoku

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz jeszcze obszaru roboczego usługi Azure Machine Learning lub maszyny wirtualnej usługi notesu, wypełnij [część 1 samouczka konfiguracji](tutorial-1st-experiment-sdk-setup.md).
* Po zakończeniu samouczka instalacji użyj tego samego serwera notesu, aby otworzyć notes *tutoriale/uczenie maszynowe-potoki zaawansowane/samouczek-pipeline-batch-scoring-classification.ipynb.*

Jeśli chcesz uruchomić samouczek konfiguracji we własnym [środowisku lokalnym,](how-to-configure-environment.md#local)możesz uzyskać dostęp do samouczka na [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Uruchom, `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps pandas requests` aby uzyskać wymagane pakiety.

## <a name="configure-workspace-and-create-a-datastore"></a>Konfigurowanie obszaru roboczego i tworzenie magazynu danych

Utwórz obiekt obszaru roboczego z istniejącego obszaru roboczego usługi Azure Machine Learning.

- [Obszar roboczy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) to klasa, która akceptuje informacje o subskrypcji i zasobach platformy Azure. Obszar roboczy tworzy również zasób w chmurze, którego można używać do monitorowania i śledzenia przebiegów modelu. 
- `Workspace.from_config()`odczytuje `config.json` plik, a następnie ładuje szczegóły `ws`uwierzytelniania do obiektu o nazwie . Obiekt `ws` jest używany w kodzie w tym samouczku.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

## <a name="create-a-datastore-for-sample-images"></a>Tworzenie magazynu danych dla przykładowych obrazów

Na `pipelinedata` koncie pobierz imagenet oceny próbnika `sampledata` danych publicznych z kontenera obiektu blob publicznych. Wywołanie, `register_azure_blob_container()` aby udostępnić dane do `images_datastore`obszaru roboczego pod nazwą . Następnie ustaw domyślny magazyn danych obszaru roboczego jako wyjściowy magazyn danych. Użyj magazynu danych wyjściowych, aby uzyskać dane wyjściowe w potoku.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>Tworzenie obiektów zestawu danych

Podczas tworzenia potoków `Dataset` obiekty są używane do odczytywania `PipelineData` danych z magazynów danych obszaru roboczego, a obiekty są używane do przesyłania danych pośrednich między krokami potoku.

> [!Important]
> Przykład oceniania partii w tym samouczku używa tylko jednego kroku potoku. W przypadkach użycia, które mają wiele kroków, typowy przepływ będzie zawierać następujące kroki:
>
> 1. Obiekty `Dataset` są używane jako *dane wejściowe* do pobierania nieprzetworzonych danych, wykonywania niektórych transformacji, a następnie *wyprowadzania* `PipelineData` obiektu.
>
> 2. Użyj `PipelineData` *obiektu wyjściowego* w poprzednim kroku jako *obiektu wejściowego*. Powtórz go w kolejnych krokach.

W tym scenariuszu `Dataset` można utworzyć obiekty, które odpowiadają katalogów magazynu danych dla obrazów wejściowych i etykiet klasyfikacji (wartości testu y). Należy również `PipelineData` utworzyć obiekt dla danych wyjściowych oceniania partii.

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/*.txt"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

Następnie zarejestruj zestawy danych w obszarze roboczym.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>Pobieranie i rejestrowanie modelu

Pobierz wstępnie przeszkolony model Tensorflow, aby użyć go do oceniania partii w potoku. Najpierw utwórz katalog lokalny, w którym jest przechowywany model. Następnie pobierz i wyodrębnij model.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Następnie zarejestruj model w obszarze roboczym, dzięki czemu można łatwo pobrać model w procesie potoku. W `register()` funkcji statycznej `model_name` parametr jest kluczem używanym do lokalizowania modelu w całym sdk.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Tworzenie i dołączanie zdalnego celu obliczeniowego

Potoki uczenia maszynowego nie mogą być uruchamiane lokalnie, więc można uruchomić je na zasobach w chmurze lub *zdalnych celach obliczeniowych.* Zdalny obiekt docelowy obliczeń to wirtualne środowisko obliczeniowe wielokrotnego użytkowania, w którym można uruchamiać eksperymenty i przepływy pracy uczenia maszynowego. 

Uruchom następujący kod, aby utworzyć obiekt [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) docelowy z włączoną funkcją GPU, a następnie dołączyć go do obszaru roboczego. Aby uzyskać więcej informacji na temat celów obliczeniowych, zobacz [artykuł koncepcyjny](https://docs.microsoft.com/azure/machine-learning/concept-compute-target).


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Pisanie skryptu oceniania

Aby wykonać punktację, utwórz `batch_scoring.py`skrypt oceniania wsadowego o nazwie , a następnie zapisz go w bieżącym katalogu. Skrypt pobiera obrazy wejściowe, stosuje model klasyfikacji, a następnie wyprowadza prognoz do pliku wyników.

Skrypt `batch_scoring.py` przyjmuje następujące parametry, które są `ParallelRunStep` przekazywane z utworzonego później utworu:

- `--model_name`: Nazwa używanego modelu.
- `--labels_name`: Nazwa pliku, `Dataset` który `labels.txt` zawiera plik.

Infrastruktura potoku `ArgumentParser` używa klasy do przekazywania parametrów do kroków potoku. Na przykład w poniższym kodzie `--model_name` pierwszy argument otrzymuje `model_name`identyfikator właściwości . W `init()` funkcji, `Model.get_model_path(args.model_name)` służy do uzyskania dostępu do tej właściwości.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict():
    label = []
    proto_as_ascii_lines = tf.gfile.GFile("labels.txt").readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_name', dest="labels_name", required=True)
    args, _ = parser.parse_known_args()

    workspace = Run.get_context(allow_offline=False).experiment.workspace
    label_ds = Dataset.get_by_name(workspace=workspace, name=args.labels_name)
    label_ds.download(target_path='.', overwrite=True)

    label_dict = get_class_label_dict()
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> Potok w tym samouczku ma tylko jeden krok i zapisuje dane wyjściowe do pliku. W przypadku potoków wieloetapowych można również zdefiniować `ArgumentParser` katalog do zapisywania danych wyjściowych dla danych wejściowych do kolejnych kroków. Na przykład przekazywania danych między wieloma `ArgumentParser` krokami potoku przy użyciu wzorca projektu zobacz [notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-the-pipeline"></a>Zbuduj potok

Przed uruchomieniem potoku utwórz obiekt, który definiuje środowisko Języka `batch_scoring.py` Python i tworzy zależności, których wymaga skrypt. Wymagana główna zależność to Tensorflow, ale `azureml-defaults` można również zainstalować dla procesów w tle. Utwórz `RunConfiguration` obiekt przy użyciu zależności. Należy również określić obsługę platformy Docker i Docker-GPU.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>Tworzenie konfiguracji w celu zawijania skryptu

Utwórz krok potoku przy użyciu skryptu, konfiguracji środowiska i parametrów. Określ cel obliczeniowy, który został już dołączony do obszaru roboczego.

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>Tworzenie kroku potoku

Krok potoku jest obiektem, który hermetyzuje wszystko, czego potrzebujesz do uruchomienia potoku, w tym:

* Ustawienia środowiska i zależności
* Zasób obliczeniowy do uruchomienia potoku na
* Dane wejściowe i wyjściowe oraz wszelkie parametry niestandardowe
* Odwołanie do skryptu lub logiki SDK do uruchomienia podczas kroku

Wiele klas dziedziczy [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py)z klasy nadrzędnej . Można wybrać klasy, aby użyć określonych struktur lub stosy do tworzenia kroku. W tym przykładzie `ParallelRunStep` klasy można użyć do zdefiniowania logiki kroku przy użyciu niestandardowego skryptu Języka Python. Jeśli argument do skryptu jest dane wejściowe do kroku lub dane wyjściowe kroku, `arguments` argument musi być `input` zdefiniowany *zarówno* w tablicy, *jak i* w lub `output` lub parametru, odpowiednio. 

W scenariuszach, w których istnieje więcej niż `outputs` jeden krok, odwołanie do obiektu w tablicy staje się dostępne jako *dane wejściowe* dla następnego kroku potoku.

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

batch_score_step = ParallelRunStep(
    name="parallel-step-test",
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    models=[model],
    arguments=["--model_name", "inception",
               "--labels_name", "label_ds"],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

Aby uzyskać listę wszystkich klas, których można użyć dla różnych typów kroków, zobacz [pakiet kroków](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

## <a name="submit-the-pipeline"></a>Prześlij potok

Teraz uruchom rurociąg. Najpierw utwórz `Pipeline` obiekt przy użyciu odwołania do obszaru roboczego i utworzonego kroku potoku. Parametr `steps` jest tablicą kroków. W takim przypadku istnieje tylko jeden krok do oceniania partii. Aby utworzyć potoki, które mają wiele kroków, należy umieścić kroki w kolejności w tej tablicy.

Następnie użyj `Experiment.submit()` funkcji, aby przesłać potok do wykonania. Należy również określić `param_batch_size`parametr niestandardowy . Funkcja `wait_for_completion` wyprowadza dzienniki podczas procesu kompilacji potoku. Można użyć dzienników, aby zobaczyć bieżący postęp.

> [!IMPORTANT]
> Pierwsze uruchomienie rurociągu trwa około *15 minut.* Wszystkie zależności muszą zostać pobrane, tworzony jest obraz platformy Docker, a środowisko Języka Python jest aprowizowana i tworzona. Ponowne uruchomienie potoku zajmuje znacznie mniej czasu, ponieważ te zasoby są ponownie ponownie, a nie tworzone. Jednak całkowity czas wykonywania potoku zależy od obciążenia skryptów i procesów, które są uruchomione w każdym kroku potoku.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Pobieranie i przeglądanie danych wyjściowych

Uruchom następujący kod, aby pobrać plik wyjściowy, który jest tworzony ze skryptu. `batch_scoring.py` Następnie zapoznaj się z wynikami punktacji.

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publikowanie i uruchamianie z punktu końcowego REST

Uruchom następujący kod, aby opublikować potok w obszarze roboczym. W obszarze roboczym w studio usługi Azure Machine Learning można wyświetlić metadane dla potoku, w tym historię uruchamiania i czas trwania. Potok można również uruchomić ręcznie ze studia.

Publikowanie potoku umożliwia punkt końcowy REST, którego można użyć do uruchomienia potoku z dowolnej biblioteki HTTP na dowolnej platformie.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Aby uruchomić potok z punktu końcowego REST, należy OAuth2 nagłówek uwierzytelniania typu okaziciela. W poniższym przykładzie użyto uwierzytelniania interaktywnego (do celów ilustracyjnych), ale w większości scenariuszy produkcyjnych wymagających uwierzytelniania automatycznego lub bezgłowego należy używać uwierzytelniania głównego usługi zgodnie [z opisem w tym artykule.](how-to-setup-authentication.md)

Uwierzytelnianie jednostkowe usługi polega na utworzeniu *rejestracji aplikacji* w *usłudze Azure Active Directory*. Najpierw wygenerujesz klucz tajny klienta, a następnie udzielisz dostępu do *roli* głównej usługi do obszaru roboczego uczenia maszynowego. Użyj [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) klasy do zarządzania przepływem uwierzytelniania. 

Zarówno [`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py) `ServicePrincipalAuthentication` i `AbstractAuthentication`dziedziczą po . W obu przypadkach [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py#get-authentication-header--) należy użyć funkcji w taki sam sposób, aby pobrać nagłówek:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Pobierz adres URL `endpoint` REST z właściwości opublikowanego obiektu potoku. Adres URL REST można również znaleźć w obszarze roboczym w studio usługi Azure Machine Learning. 

Tworzenie żądania HTTP POST do punktu końcowego. Określ nagłówek uwierzytelniania w żądaniu. Dodaj obiekt ładunku JSON, który ma nazwę eksperymentu i parametr rozmiaru partii. Jak wspomniano wcześniej w `param_batch_size` samouczku, `batch_scoring.py` jest przekazywana do `PipelineParameter` skryptu, ponieważ zdefiniowano go jako obiekt w konfiguracji kroku.

Złożyć żądanie, aby wyzwolić uruchomić. Dołącz kod, `Id` aby uzyskać dostęp do klucza ze słownika odpowiedzi, aby uzyskać wartość identyfikatora uruchomienia.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Użyj identyfikatora uruchomienia, aby monitorować stan nowego uruchomienia. Nowy bieg trwa kolejne 10-15 min, aby zakończyć. 

Nowy przebieg będzie wyglądać podobnie do potoku, który uruchomiono wcześniej w samouczku. Można wybrać opcję nieuchołkania pełnego wyjścia.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Nie należy ukończyć tej sekcji, jeśli planujesz uruchomić inne samouczki usługi Azure Machine Learning.

### <a name="stop-the-compute-instance"></a>Zatrzymywać wystąpienie obliczeń

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Usuń wszystko

Jeśli nie planujesz używać utworzonych zasobów, usuń je, aby uniknąć naliczania opłat.

1. W witrynie Azure portal w menu po lewej stronie wybierz pozycję **Grupy zasobów**.
1. Na liście grup zasobów wybierz utworzoną grupę zasobów.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. Wpisz nazwę grupy zasobów. Następnie wybierz pozycję **Usuń**.

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku potoków uczenia maszynowego wykonaliśmy następujące zadania:

> [!div class="checklist"]
> * Skonsliowany potok z zależnościami środowiska do uruchamiania na zdalnym zasobie obliczeniowym procesora GPU.
> * Utworzono skrypt oceniania do uruchamiania prognoz wsadowych przy użyciu wstępnie przeszkolonego modelu Tensorflow.
> * Opublikowano potok i włączył go do uruchomienia z punktu końcowego REST.

Aby uzyskać więcej przykładów tworzenia potoków przy użyciu sdk uczenia maszynowego, zobacz [repozytorium notesu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
