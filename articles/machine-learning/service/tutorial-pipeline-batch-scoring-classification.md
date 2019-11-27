---
title: 'Samouczek: potoki ML do oceniania partii'
titleSuffix: Azure Machine Learning
description: W tym samouczku utworzysz potok uczenia maszynowego służący do uruchamiania oceny wsadowej na modelu klasyfikacji obrazów w Azure Machine Learning. Potoki uczenia maszynowego optymalizują przepływ pracy z szybkością, przenośnością i wielokrotnym użyciem, dzięki czemu możesz skupić się na wiedzy na temat uczenia maszynowego — zamiast infrastruktury i automatyzacji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 11/04/2019
ms.openlocfilehash: 11d57031405939e37afade9a9452c5d3a8b9e1e4
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483296"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Samouczek: Tworzenie potoku Azure Machine Learning na potrzeby oceniania partii

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym samouczku użyjesz potoku w Azure Machine Learning, aby uruchomić zadanie oceniania partii. W przykładzie zastosowano [model splotowych neuronowych](https://arxiv.org/abs/1512.00567) sieci Tensorflow, który umożliwia klasyfikowanie obrazów bez etykiet. Po skompilowaniu i opublikowaniu potoku należy skonfigurować punkt końcowy REST, za pomocą którego można wyzwolić potok z dowolnej biblioteki HTTP na dowolnej platformie.

Potoki uczenia maszynowego optymalizują przepływ pracy z szybkością, przenośnością i wielokrotnym użyciem, dzięki czemu możesz skupić się na wiedzy na temat uczenia maszynowego — zamiast infrastruktury i automatyzacji. [Dowiedz się więcej o potokach uczenia maszynowego](concept-ml-pipelines.md).

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Konfigurowanie obszaru roboczego i Pobieranie przykładowych danych
> * Tworzenie obiektów danych do pobierania i wyprowadzania danych
> * Pobieranie, przygotowywanie i rejestrowanie modelu w obszarze roboczym
> * Udostępnianie elementów docelowych obliczeń i Tworzenie skryptu oceniania
> * Kompilowanie, uruchamianie i publikowanie potoku
> * Włączanie punktu końcowego REST dla potoku

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz jeszcze Azure Machine Learning obszaru roboczego lub notesu maszyny wirtualnej, Ukończ [część 1 samouczka instalacji](tutorial-1st-experiment-sdk-setup.md).
* Po zakończeniu pracy z samouczkiem Instalatora Użyj tego samego serwera notesu, aby otworzyć Notes */tutorial-Pipeline-Batch-Scoring-Classification. ipynb* .

Jeśli chcesz uruchomić samouczek instalacji we własnym [środowisku lokalnym](how-to-configure-environment.md#local), możesz uzyskać dostęp do samouczka w witrynie [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Uruchom `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests`, aby uzyskać wymagane pakiety.

## <a name="configure-workspace-and-create-a-datastore"></a>Konfigurowanie obszaru roboczego i tworzenie magazynu danych

Utwórz obiekt obszaru roboczego z istniejącego obszaru roboczego Azure Machine Learning.

- [Obszar roboczy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) to Klasa, która akceptuje informacje o subskrypcji i zasobach platformy Azure. Obszar roboczy tworzy również zasób w chmurze, którego można użyć do monitorowania i śledzenia przebiegów modelu. 
- `Workspace.from_config()` odczytuje plik `config.json`, a następnie ładuje szczegóły uwierzytelniania do obiektu o nazwie `ws`. Obiekt `ws` jest używany w kodzie w tym samouczku.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Tworzenie magazynu danych dla przykładowych obrazów

Na koncie `pipelinedata` z `sampledata` publicznego kontenera obiektów BLOB Pobierz dane z publicznej oceny ImageNet. Wywołaj `register_azure_blob_container()`, aby udostępnić dane dla obszaru roboczego pod nazwą `images_datastore`. Następnie należy ustawić domyślny magazyn danych obszaru roboczego jako wyjściowy. Użyj wyjściowego magazynu danych do oceny danych wyjściowych w potoku.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-data-objects"></a>Tworzenie obiektów danych

Podczas kompilowania potoku obiekt `DataReference` odczytuje dane z magazynu danych obszaru roboczego. Obiekt `PipelineData` przesyła dane pośrednie między etapami potoku.

> [!Important]
> Przykład wsadowego oceniania w tym samouczku używa tylko jednego kroku potoku. W przypadku użycia, które mają wiele kroków, typowy przepływ obejmuje następujące kroki:
>
> 1. Użyj `DataReference` obiektów jako *danych wejściowych* , aby pobrać dane pierwotne, wykonać transformację, a następnie *wyprowadzić* `PipelineData` obiektu.
>
> 2. Użyj `PipelineData` *obiektu wyjściowego* w poprzednim kroku jako *obiektu wejściowego*. Powtórz tę czynność dla kolejnych kroków.

W tym scenariuszu utworzysz obiekty `DataReference`, które odpowiadają katalogom magazynu danych zarówno dla obrazów wejściowych, jak i etykiet klasyfikacji (wartości y-test). Utworzysz również obiekt `PipelineData` dla danych wyjściowych oceniania wsadowego.

```python
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download"
                            )

label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download"                          
                         )

output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

## <a name="download-and-register-the-model"></a>Pobieranie i rejestrowanie modelu

Pobierz Tensorflow model, aby użyć go do oceniania partii w potoku. Najpierw Utwórz katalog lokalny, w którym jest przechowywany model. Następnie pobierz i Wyodrębnij model.

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

Następnie zarejestruj model w obszarze roboczym, aby można było łatwo pobrać model w procesie potoku. W `register()` statycznej funkcji parametr `model_name` jest kluczem używanym do lokalizowania modelu w całym zestawie SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Tworzenie i dołączanie zdalnego celu obliczeń

Potoki usługi Machine Learning nie mogą być uruchamiane lokalnie, dlatego można je uruchamiać w zasobach w chmurze lub *zdalnych celach obliczeniowych*. Zdalny obiekt docelowy obliczeń to wirtualne środowisko obliczeniowe wielokrotnego użytku, w którym można uruchamiać eksperymenty i przepływy pracy uczenia maszynowego. 

Uruchom następujący kod, aby utworzyć obiekt docelowy [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) z włączoną obsługą procesora GPU, a następnie dołącz go do obszaru roboczego. Aby uzyskać więcej informacji na temat obiektów docelowych obliczeń, zobacz [artykuł koncepcyjny](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target).


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

## <a name="write-a-scoring-script"></a>Napisz skrypt oceniania

Aby wykonać ocenianie, Utwórz skrypt oceniania partii o nazwie `batch_scoring.py`, a następnie zapisz go w bieżącym katalogu. Skrypt pobiera obrazy wejściowe, stosuje model klasyfikacji, a następnie wyprowadza przewidywania do pliku wyników.

Skrypt `batch_scoring.py` przyjmuje następujące parametry, które są przesyłane z kroku potoku utworzonego w dalszej części tego samouczka:

- `--model_name`: nazwa używanego modelu.
- `--label_dir`: katalog, w którym znajduje się plik `labels.txt`.
- `--dataset_path`: katalog zawierający obrazy wejściowe.
- `--output_dir`: katalog wyjściowy dla pliku `results-label.txt` po uruchomieniu skryptu uruchamia model na danych.
- `--batch_size`: rozmiar wsadu używany w uruchomionym modelu.

Infrastruktura potoku używa klasy `ArgumentParser` do przekazywania parametrów do kroków potoku. Na przykład, w poniższym kodzie, pierwszy argument `--model_name` jest przyznany identyfikatorowi właściwości `model_name`. W funkcji `main()` `Model.get_model_path(args.model_name)` służy do uzyskiwania dostępu do tej właściwości.


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
from azureml.core.model import Model

slim = tf.contrib.slim

parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
parser.add_argument('--model_name', dest="model_name", required=True)
parser.add_argument('--label_dir', dest="label_dir", required=True)
parser.add_argument('--dataset_path', dest="dataset_path", required=True)
parser.add_argument('--output_dir', dest="output_dir", required=True)
parser.add_argument('--batch_size', dest="batch_size", type=int, required=True)

args = parser.parse_args()

image_size = 299
num_channel = 3

# create output directory if it does not exist
os.makedirs(args.output_dir, exist_ok=True)


def get_class_label_dict(label_file):
    label = []
    proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


class DataIterator:
    def __init__(self, data_dir):
        self.file_paths = []
        image_list = os.listdir(data_dir)
        self.file_paths = [data_dir + '/' + file_name.rstrip() for file_name in image_list]
        self.labels = [1 for file_name in self.file_paths]

    @property
    def size(self):
        return len(self.labels)

    def input_pipeline(self, batch_size):
        images_tensor = tf.convert_to_tensor(self.file_paths, dtype=tf.string)
        labels_tensor = tf.convert_to_tensor(self.labels, dtype=tf.int64)
        input_queue = tf.train.slice_input_producer([images_tensor, labels_tensor], shuffle=False)
        labels = input_queue[1]
        images_content = tf.read_file(input_queue[0])

        image_reader = tf.image.decode_jpeg(images_content, channels=num_channel, name="jpeg_reader")
        float_caster = tf.cast(image_reader, tf.float32)
        new_size = tf.constant([image_size, image_size], dtype=tf.int32)
        images = tf.image.resize_images(float_caster, new_size)
        images = tf.divide(tf.subtract(images, [0]), [255])

        image_batch, label_batch = tf.train.batch([images, labels], batch_size=batch_size, capacity=5 * batch_size)
        return image_batch


def main(_):
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)
    count = 0

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)

    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                  num_classes=classes_num,
                                                  is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")
        with open(out_filename, "w") as result_file:
            i = 0
            while count < total_size and not coord.should_stop():
                test_images_batch = sess.run(test_images)
                file_names_batch = test_feeder.file_paths[i * args.batch_size:
                                                          min(test_feeder.size, (i + 1) * args.batch_size)]
                results = sess.run(probabilities, feed_dict={input_images: test_images_batch})
                new_add = min(args.batch_size, total_size - count)
                count += new_add
                i += 1
                for j in range(new_add):
                    result_file.write(os.path.basename(file_names_batch[j]) + ": " + label_dict[results[j]] + "\n")
                result_file.flush()
            coord.request_stop()
            coord.join(threads)

        shutil.copy(out_filename, "./outputs/")

if __name__ == "__main__":
    tf.app.run()

```

> [!TIP]
> Potok w tym samouczku ma tylko jeden krok i zapisuje dane wyjściowe do pliku. W przypadku potoków wieloetapowych należy użyć `ArgumentParser`, aby zdefiniować katalog do zapisywania danych wyjściowych do kolejnych kroków. Przykład przekazywania danych między wieloma krokami potoku przy użyciu wzorca projektowego `ArgumentParser` można znaleźć w [notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-and-run-the-pipeline"></a>Kompilowanie i uruchamianie potoku

Przed uruchomieniem potoku Utwórz obiekt, który definiuje środowisko Python i tworzy zależności wymagane przez skrypt `batch_scoring.py`. Główna zależność wymagana to Tensorflow, ale można również zainstalować `azureml-defaults` z zestawu SDK dla procesów w tle. Utwórz obiekt `RunConfiguration` przy użyciu zależności. Należy również określić obsługę platformy Docker i platformy Docker-GPU.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import CondaDependencies, RunConfiguration

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])

amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="parameterize-the-pipeline"></a>Sparametryzuj potoku

Zdefiniuj parametr niestandardowy dla potoku, aby kontrolować rozmiar wsadu. Gdy potok zostanie opublikowany i ujawniony za pośrednictwem punktu końcowego REST, wszystkie skonfigurowane parametry są również uwidocznione. Po ponownym uruchomieniu potoku za pomocą żądania HTTP można określić parametry niestandardowe w ładunku JSON.

Utwórz obiekt `PipelineParameter`, aby włączyć to zachowanie oraz zdefiniować nazwę i wartość domyślną.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>Tworzenie etap potoku

Krok potoku to obiekt, który hermetyzuje wszystko, czego potrzebujesz do uruchomienia potoku, w tym:

* Ustawienia środowiska i zależności
* Zasób obliczeniowy, na którym ma zostać uruchomiony potok
* Dane wejściowe i wyjściowe oraz dowolne parametry niestandardowe
* Odwołanie do skryptu lub logiki zestawu SDK do uruchomienia w ramach tego kroku

Wiele klas dziedziczy z klasy nadrzędnej [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py). Możesz wybrać klasy do użycia określonych platform lub stosów, aby skompilować krok. W tym przykładzie użyto klasy [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) do zdefiniowania logiki krokowej przy użyciu niestandardowego skryptu języka Python. Jeśli argument skryptu jest wejściem do kroku lub wyjściem kroku, argument musi być zdefiniowany *zarówno* w tablicy `arguments`, *jak i* odpowiednio w `input` lub `output`. 

W scenariuszach, w których występuje więcej niż jeden krok, odwołanie do obiektu w tablicy `outputs` jest dostępne jako *dane wejściowe* dla kolejnego kroku potoku.

```python
from azureml.pipeline.steps import PythonScriptStep

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_scoring.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config
)
```

Aby zapoznać się z listą wszystkich klas, których można użyć dla różnych typów etapów, zobacz [pakiet kroków](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

### <a name="run-the-pipeline"></a>Uruchamianie potoku

Teraz uruchom potok. Najpierw Utwórz obiekt `Pipeline` przy użyciu odwołania do obszaru roboczego i utworzonego etapu potoku. Parametr `steps` jest tablicą kroków. W takim przypadku istnieje tylko jeden krok oceniania partii. Aby utworzyć potoki, które mają wiele kroków, należy umieścić kroki w kolejności w tej tablicy.

Następnie użyj funkcji `Experiment.submit()`, aby przesłać potok do wykonania. Należy również określić parametr niestandardowy `param_batch_size`. Funkcja `wait_for_completion` wyprowadza dzienniki podczas procesu kompilacji potoku. Możesz użyć dzienników, aby zobaczyć bieżący postęp.

> [!IMPORTANT]
> Pierwsze uruchomienie potoku trwa około *15 minut*. Wszystkie zależności muszą zostać pobrane, zostanie utworzony obraz platformy Docker, a środowisko Python jest inicjowane i tworzone. Ponowne uruchomienie potoku trwa znacznie mniej czasu, ponieważ te zasoby są ponownie używane zamiast tworzenia. Jednak łączny czas wykonywania potoku zależy od obciążenia skryptów i procesów uruchomionych w każdym kroku potoku.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Pobierz i Przejrzyj dane wyjściowe

Uruchom następujący kod, aby pobrać plik wyjściowy, który został utworzony na podstawie skryptu `batch_scoring.py`. Następnie przejrzyj wyniki oceniania.

```python
import pandas as pd

step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Nazwa pliku</th>
      <th>Prognozy</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>ILSVRC2012_val_00000102. JPEG</td>
      <td>Rhodesian Ridgeback</td>
    </tr>
    <tr>
      <td>1</td>
      <td>ILSVRC2012_val_00000103. JPEG</td>
      <td>tripod</td>
    </tr>
    <tr>
      <td>2</td>
      <td>ILSVRC2012_val_00000104. JPEG</td>
      <td>Klawiatura z maszyną do pisania</td>
    </tr>
    <tr>
      <td>3</td>
      <td>ILSVRC2012_val_00000105. JPEG</td>
      <td>jedwab Terrier</td>
    </tr>
    <tr>
      <td>4</td>
      <td>ILSVRC2012_val_00000106. JPEG</td>
      <td>Windsor krawat</td>
    </tr>
    <tr>
      <td>5</td>
      <td>ILSVRC2012_val_00000107. JPEG</td>
      <td>zbiór plonów</td>
    </tr>
    <tr>
      <td>6</td>
      <td>ILSVRC2012_val_00000108. JPEG</td>
      <td>violin</td>
    </tr>
    <tr>
      <td>7</td>
      <td>ILSVRC2012_val_00000109. JPEG</td>
      <td>loudspeaker</td>
    </tr>
    <tr>
      <td>8</td>
      <td>ILSVRC2012_val_00000110. JPEG</td>
      <td>fartuch</td>
    </tr>
    <tr>
      <td>9</td>
      <td>ILSVRC2012_val_00000111. JPEG</td>
      <td>Homar amerykański</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publikowanie i uruchamianie z punktu końcowego REST

Uruchom następujący kod, aby opublikować potok w obszarze roboczym. W obszarze roboczym programu Azure Machine Learning Studio można zobaczyć metadane dla potoku, w tym historię uruchamiania i czas trwania. Potok można również uruchomić ręcznie z poziomu programu Studio.

Opublikowanie potoku umożliwia punkt końcowy REST, którego można użyć do uruchomienia potoku z dowolnej biblioteki HTTP na dowolnej platformie.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Aby można było uruchomić potok z punktu końcowego REST, potrzebny jest nagłówek uwierzytelniania typu "OAuth2". W poniższym przykładzie użyto uwierzytelniania interaktywnego (na przykład), ale w przypadku większości scenariuszy produkcyjnych, które wymagają automatycznego lub bezobsługowego uwierzytelniania, należy użyć uwierzytelniania nazwy głównej usługi zgodnie [z opisem w tym notesie](https://aka.ms/pl-restep-auth).

Uwierzytelnianie jednostki usługi polega na utworzeniu *rejestracji aplikacji* w *Azure Active Directory*. Najpierw należy wygenerować klucz tajny klienta, a następnie przyznać roli głównej usługi *dostęp* do obszaru roboczego uczenia maszynowego. Użyj klasy [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) , aby zarządzać przepływem uwierzytelniania. 

Zarówno [`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py) , jak i `ServicePrincipalAuthentication` dziedziczą z `AbstractAuthentication`. W obu przypadkach Użyj funkcji [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py#get-authentication-header--) w taki sam sposób, aby pobrać nagłówek:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Pobierz adres URL REST z właściwości `endpoint` opublikowanego obiektu potoku. Możesz również znaleźć adres URL REST w obszarze roboczym w programie Azure Machine Learning Studio. 

Kompiluj żądanie HTTP POST do punktu końcowego. Określ w żądaniu nagłówek uwierzytelniania. Dodaj obiekt ładunku JSON, który ma nazwę eksperymentu i parametr rozmiaru partii. Jak wspomniano wcześniej w samouczku, `param_batch_size` jest przenoszona do skryptu `batch_scoring.py`, ponieważ został on zdefiniowany jako obiekt `PipelineParameter` w konfiguracji kroku.

Wykonaj żądanie, aby wyzwolić uruchomienie. Dołącz kod, aby uzyskać dostęp do klucza `Id` ze słownika odpowiedzi, aby uzyskać wartość identyfikatora uruchomienia.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Użyj identyfikatora uruchomienia, aby monitorować stan nowego przebiegu. Nowy przebieg zajmie jeszcze 10-15 min. 

Nowy przebieg będzie wyglądał podobnie do potoku, który został uruchomiony wcześniej w samouczku. Możesz zrezygnować z wyświetlania pełnych danych wyjściowych.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Nie Dokończ tej sekcji, jeśli planujesz uruchamiać inne samouczki Azure Machine Learning.

### <a name="stop-the-notebook-vm"></a>Zatrzymaj maszynę wirtualną notesu

[!INCLUDE [aml-stop-server](../../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Usuń wszystko

Jeśli nie planujesz używać utworzonych zasobów, usuń je, aby uniknąć naliczania opłat.

1. W Azure Portal w menu po lewej stronie wybierz pozycję **grupy zasobów**.
1. Z listy grup zasobów wybierz utworzoną grupę zasobów.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. Wpisz nazwę grupy zasobów. Następnie wybierz pozycję **Usuń**.

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku potoki uczenia maszynowego zostały wykonane następujące zadania:

> [!div class="checklist"]
> * Utworzono potok z zależnościami środowiska do uruchomienia na zdalnym zasobie obliczeniowym procesora GPU.
> * Utworzono skrypt oceniania służący do uruchamiania prognoz wsadowych przy użyciu przedniego Tensorflow modelu.
> * Opublikowano potok i włączył go do uruchamiania z punktu końcowego REST.

Aby uzyskać więcej przykładów tworzenia potoków przy użyciu zestawu SDK usługi Machine Learning, zobacz [repozytorium notesu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
