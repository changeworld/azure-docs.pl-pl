---
title: 'Samouczek: Potoki Azure ML do oceniania partii'
titleSuffix: Azure Machine Learning
description: Utwórz potok ML do uruchamiania oceniania wsadowego na modelu klasyfikacji obrazów. Potoki usługi Machine Learning optymalizują przepływ pracy przy użyciu szybkości, przenośności i ponownego wykorzystania, dzięki czemu możesz skupić się na wiedzy, uczeniu maszynowym, a nie na infrastrukturze i automatyzacji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/05/2019
ms.openlocfilehash: aa280ea71735ce7c4000951e5c11de2913aaebe8
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001614"
---
# <a name="use-azure-machine-learning-pipelines-for-batch-scoring"></a>Korzystanie z potoków Azure Machine Learning na potrzeby oceniania partii

W tym samouczku użyjesz potoków Azure Machine Learning do uruchamiania oceniania partii lub wnioskowania, zadania. To przykładowe zadanie służy do klasyfikowania obrazów bez etykiet, korzystając z wstępnie [nauczonego](https://arxiv.org/abs/1512.00567) modelu splotowych neuronowych Network Tensorflow. Po skompilowaniu i opublikowaniu potoku należy skonfigurować punkt końcowy REST, aby można było wyzwolić potok z dowolnej biblioteki HTTP na dowolnej platformie.

Potoki usługi Machine Learning optymalizują przepływ pracy przy użyciu szybkości, przenośności i ponownego wykorzystania, dzięki czemu możesz skupić się na wiedzy, uczeniu maszynowym, a nie na infrastrukturze i automatyzacji. [Dowiedz się więcej o potokach ml](concept-ml-pipelines.md).

W tym samouczku przedstawiono następujące zadania:

> [!div class="checklist"]
> * Konfigurowanie obszaru roboczego i Pobieranie przykładowych danych
> * Tworzenie obiektów danych do pobierania i wyprowadzania danych
> * Pobieranie, przygotowywanie i rejestrowanie modelu w obszarze roboczym
> * Udostępnianie elementów docelowych obliczeń i Tworzenie skryptu oceniania
> * Kompilowanie, uruchamianie i publikowanie potoku
> * Włączanie punktu końcowego REST dla potoku

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [część 1 samouczka Instalatora](tutorial-1st-experiment-sdk-setup.md) , jeśli nie masz jeszcze obszaru roboczego Azure Machine Learning lub maszyny wirtualnej notesu.
* Po zakończeniu pracy z samouczkiem Instalatora Otwórz Notes **samouczków/tutorial-Pipeline-Batch-Scoring-Classification. ipynb** przy użyciu tego samego serwera notesu.

Ten samouczek jest również dostępny w witrynie [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) , jeśli chcesz uruchomić go w [środowisku lokalnym](how-to-configure-environment.md#local). Uruchom `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` , aby pobrać wymagane pakiety.

## <a name="configure-workspace-and-create-datastore"></a>Konfigurowanie obszaru roboczego i tworzenie magazynu danych

Utwórz obiekt obszaru roboczego z istniejącego obszaru roboczego Azure Machine Learning. 
+ [Obszar roboczy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) to Klasa, która akceptuje informacje o subskrypcji i zasobach platformy Azure. Tworzy ona również zasób w chmurze służący do monitorowania i śledzenia przebiegów modelu. 

+ `Workspace.from_config()`odczytuje plik **config. JSON** i ładuje szczegóły uwierzytelniania do obiektu o nazwie `ws`. Obiekt `ws` jest używany w kodzie w tym samouczku.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Tworzenie magazynu danych dla przykładowych obrazów

Pobierz przykład danych z publicznej wersji ewaluacyjnej ImageNet z publicznego kontenera `sampledata` obiektów BLOB na `pipelinedata`koncie. Wywołanie `register_azure_blob_container()` sprawia, że dane są dostępne dla obszaru roboczego pod `images_datastore`nazwą. Następnie określ domyślny magazyn danych obszaru roboczego jako wynikowy magazyn danych, który będzie używany do oceny wyników w potoku.

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

Podczas kompilowania potoków `DataReference` obiekty są używane do odczytywania danych z magazynów elementów obszaru roboczego `PipelineData` , a obiekty są używane do przesyłania danych pośrednich między etapami potoku.

> [!Important]
> Ten przykład wsadowego oceniania wykorzystuje tylko jeden krok potoku, ale w przypadku użycia z wieloma krokami typowy przepływ obejmuje:
>
> 1. Używanie `DataReference` obiektów jako danych **wejściowych** do pobierania nieprzetworzonych, wykonywania niektórych transformacji, `PipelineData` a następnie **wyprowadzania** obiektu.
>
> 2. Użyj **obiektu wyjściowego** poprzedniego kroku `PipelineData` jako *obiektu wejściowego*, powtarzanego dla kolejnych kroków.

W tym scenariuszu utworzysz `DataReference` obiekty odpowiadające katalogom magazynu danych zarówno dla obrazów wejściowych, jak i etykiet klasyfikacji (wartości y-test). Tworzony jest również `PipelineData` obiekt dla danych wyjściowych oceniania partii.

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

Pobierz wstępnie szkolony model Tensorflow, aby używać go do oceniania partii w potoku. Najpierw Utwórz katalog lokalny, w którym przechowujesz model, a następnie pobierz go i Wyodrębnij.

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

Teraz zarejestrujesz model w obszarze roboczym, który pozwala łatwo pobrać go w procesie potoku. `register()` W funkcji`model_name` statycznej parametr jest kluczem używanym do lokalizowania modelu w całym zestawie SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-remote-compute-target"></a>Tworzenie i dołączanie zdalnego celu obliczeń

Ponieważ potoki ML nie mogą być uruchamiane lokalnie, należy je uruchomić w zasobach w chmurze. Nazywamy je jako zdalnymi obiektami docelowymi obliczeń, które są wirtualnymi środowiskami obliczeniowymi wielokrotnego użytku, w których uruchamiane są eksperymenty i przepływy pracy w ML. Uruchom następujący kod, aby utworzyć obiekt docelowy z obsługą [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) procesora GPU i dołączyć go do obszaru roboczego. Więcej informacji na temat obiektów docelowych obliczeń można znaleźć w [artykule koncepcyjnym](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target) .


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

Aby wykonać ocenianie, należy utworzyć skrypt `batch_scoring.py`oceniania partii i zapisać go w bieżącym katalogu. Skrypt pobiera obrazy wejściowe, stosuje model klasyfikacji i wyprowadza przewidywania do pliku wyników.

Skrypt `batch_scoring.py` przyjmuje następujące parametry, które są przenoszone z kroku potoku utworzonego w dalszej części tego samouczka:

- `--model_name`: nazwa używanego modelu
- `--label_dir`: katalog zawierający `labels.txt` plik 
- `--dataset_path`: katalog zawierający obrazy wejściowe
- `--output_dir`: skrypt uruchomi model na danych i wyjściowych `results-label.txt` w tym katalogu
- `--batch_size`: rozmiar wsadu używany podczas uruchamiania modelu

Infrastruktura potoków używa `ArgumentParser` klasy do przekazywania parametrów do kroków potoku. Na przykład, w kodzie poniżej pierwszego argumentu `--model_name` jest przyznany identyfikator `model_name`właściwości. W funkcji Ta właściwość jest dostępna przy użyciu `Model.get_model_path(args.model_name)`. `main()`


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
> Potok w tym samouczku ma tylko jeden krok i zapisuje dane wyjściowe do pliku, ale w przypadku potoków wieloetapowych można również `ArgumentParser` zdefiniować katalog do zapisywania danych wyjściowych do kolejnych kroków. Zapoznaj się z [notesem](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) , aby uzyskać przykład przekazywania danych między wieloma krokami potoku przy użyciu `ArgumentParser` wzorca projektowego.

## <a name="build-and-run-the-pipeline"></a>Kompilowanie i uruchamianie potoku

Przed uruchomieniem potoku należy utworzyć obiekt definiujący środowisko i zależności, które są potrzebne przez skrypt `batch_scoring.py`. Główna zależność wymagana to Tensorflow, ale można również zainstalować `azureml-defaults` procesy w tle z zestawu SDK. `RunConfiguration` Utwórz obiekt przy użyciu zależności, a także określ obsługę platformy Docker i procesora GPU.

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

Zdefiniuj parametr niestandardowy dla potoku, aby kontrolować rozmiar wsadu. Po opublikowaniu i udostępnieniu potoku za pośrednictwem punktu końcowego REST wszystkie skonfigurowane parametry są również uwidaczniane i można je określić w ładunku JSON podczas ponownej uruchomienia potoku przy użyciu żądania HTTP.

`PipelineParameter` Utwórz obiekt, aby włączyć to zachowanie, i zdefiniuj nazwę i wartość domyślną.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>Tworzenie etap potoku

Krok potoku to obiekt, który hermetyzuje wszystko, czego potrzebujesz do uruchamiania potoków, w tym:

* Ustawienia środowiska i zależności
* zasób obliczeniowy, na którym ma zostać uruchomiony potok
* dane wejściowe i wyjściowe oraz dowolne parametry niestandardowe
* odwołanie do skryptu lub zestawu SDK logiki do uruchomienia w ramach tego kroku

Istnieje wiele klas, które dziedziczą z klasy [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) nadrzędnej, aby ułatwić tworzenie kroków przy użyciu określonych platform i stosów. W tym przykładzie użyto [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) klasy do zdefiniowania logiki kroku przy użyciu niestandardowego skryptu języka Python. Należy pamiętać, że jeśli argument skryptu jest danymi wejściowymi do kroku lub danych wyjściowych kroku, musi być zdefiniowany **zarówno** `arguments` w tablicy, **jak** i odpowiednio w `input` parametrze lub. `output` 

Odwołanie do obiektu w `outputs` tablicy jest dostępne jako **dane wejściowe** dla kolejnego kroku potoku, w przypadku scenariuszy, w których istnieje więcej niż jeden krok.

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

Aby uzyskać listę wszystkich klas dla różnych typów etapów, zobacz [pakiet kroków](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

### <a name="run-the-pipeline"></a>Uruchamianie potoku

Teraz można uruchomić potok. Najpierw Utwórz `Pipeline` obiekt z odwołaniem do obszaru roboczego i utworzonym etapem potoku. `steps` Parametr jest tablicą kroków, a w tym przypadku jest tylko jeden krok dla oceniania partii. Aby kompilować potoki z wieloma krokami, należy umieścić kroki w kolejności w tej tablicy.

Następnie użyj funkcji `Experiment.submit()` , aby przesłać potok do wykonania. Należy również określić parametr `param_batch_size`niestandardowy. `wait_for_completion` Funkcja będzie wyprowadzać dzienniki podczas procesu kompilacji potoku, co umożliwia wyświetlenie bieżącego postępu.

> [!IMPORTANT]
> Pierwsze uruchomienie potoku trwa około **15 minut**, ponieważ wszystkie zależności muszą zostać pobrane, tworzony jest obraz platformy Docker, a środowisko języka Python jest inicjowane/tworzone. Ponowne uruchomienie tego procesu zajmuje znacznie mniej czasu, ponieważ zasoby są ponownie używane. Jednak łączny czas wykonywania zależy od obciążenia skryptów i procesów uruchomionych w każdym kroku potoku.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Pobierz i Przejrzyj dane wyjściowe

Uruchom następujący kod, aby pobrać plik wyjściowy utworzony ze `batch_scoring.py` skryptu, a następnie przejrzyj wyniki oceniania.

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
      <td>Rhodesian ridgeback</td>
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

## <a name="publish-and-run-from-rest-endpoint"></a>Publikowanie i uruchamianie z punktu końcowego REST

Uruchom następujący kod, aby opublikować potok w obszarze roboczym. W obszarze roboczym portalu można zobaczyć metadane dla potoku, w tym historię uruchamiania i czasy trwania. Potok można również uruchomić ręcznie z poziomu portalu.

Ponadto opublikowanie potoku umożliwia punktowi końcowego REST ponowne uruchomienie potoku z dowolnej biblioteki HTTP na dowolnej platformie.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Aby uruchomić potok z punktu końcowego REST, należy najpierw uzyskać nagłówek uwierzytelniania typu "OAuth2". W tym przykładzie użyto interaktywnego uwierzytelniania na potrzeby ilustracji, ale w przypadku większości scenariuszy produkcyjnych wymagających uwierzytelniania automatycznego lub bezobsługowego należy użyć uwierzytelniania z użyciem zasady usługi zgodnie [z opisem w tym notesie](https://aka.ms/pl-restep-auth).

Uwierzytelnianie zasad usługi polega na utworzeniu **rejestracji aplikacji** w **Azure Active Directory**, wygenerowaniu klucza tajnego klienta, a następnie przyznaniu **roli** głównej usługi dostępu do obszaru roboczego uczenia maszynowego. Następnie można użyć [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) klasy do zarządzania przepływem uwierzytelniania. 

Oba `InteractiveLoginAuthentication` `AbstractAuthentication`i `ServicePrincipalAuthentication` dziedziczą z`get_authentication_header()` , a w obu przypadkach używasz funkcji w taki sam sposób, aby można było pobrać nagłówek.

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Pobierz adres URL REST z `endpoint` właściwości opublikowanego obiektu potoku. Adres URL REST można również znaleźć w obszarze roboczym portalu. Utwórz żądanie HTTP POST w punkcie końcowym, określając nagłówek uwierzytelniania. Dodatkowo Dodaj obiekt ładunku JSON z nazwą eksperymentu i parametrem rozmiaru partii. Przypomnienie `param_batch_size` jest przesyłane `batch_scoring.py` do skryptu, ponieważ zostało zdefiniowane jako `PipelineParameter` obiekt w konfiguracji kroku.

Wykonaj żądanie, aby wyzwolić uruchomienie. Uzyskaj dostęp do klucza z słownika odpowiedzi, aby uzyskać wartość identyfikatora uruchomienia. `Id`

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Użyj identyfikatora uruchomienia, aby monitorować stan nowego przebiegu. Spowoduje to ponowienie 10-15 min i będzie wyglądać podobnie do poprzedniego przebiegu potoku, dlatego jeśli nie trzeba zobaczyć innego uruchomienia potoku, możesz pominąć oglądanie pełnych danych wyjściowych.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Nie wykonuj tej sekcji, jeśli planujesz Uruchamianie innych samouczków Azure Machine Learning.

### <a name="stop-the-notebook-vm"></a>Zatrzymaj maszynę wirtualną notesu

Jeśli używasz serwera notesu w chmurze, Zatrzymaj maszynę wirtualną, gdy nie używasz jej do obniżenia kosztów.

1. W obszarze roboczym wybierz pozycję **maszyny wirtualne Notes**.
1. Z listy wybierz maszynę wirtualną.
1. Wybierz pozycję **Zatrzymaj**.
1. Gdy wszystko będzie gotowe do korzystania z serwera, wybierz pozycję **Uruchom**.

### <a name="delete-everything"></a>Usuń wszystko

Jeśli nie planujesz korzystać z utworzonych zasobów, usuń je, aby nie nawiązać żadnych opłat.

1. W witrynie Azure Portal na końcu z lewej strony wybierz pozycję **Grupy zasobów**.
1. Wybierz utworzoną grupę zasobów z listy.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. Wpisz nazwę grupy zasobów. Następnie wybierz pozycję **Usuń**.

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku potoki uczenia maszynowego zostały wykonane następujące zadania:

> [!div class="checklist"]
> * Skompilowany potok z zależnościami środowiska do uruchamiania na zdalnym zasobie obliczeniowym procesora GPU
> * Utworzono skrypt oceniania służący do uruchamiania prognoz wsadowych ze wstępnie szkolonym modelem Tensorflow
> * Opublikowano potok i włączył go do uruchamiania z punktu końcowego REST

Zapoznaj się z [repozytorium notesu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines) , aby uzyskać dodatkowe przykłady kompilowania potoków przy użyciu zestawu SDK usługi Machine Learning.
