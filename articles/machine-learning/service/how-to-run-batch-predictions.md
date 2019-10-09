---
title: Uruchamianie prognoz wsadowych dotyczących dużych ilości danych przy użyciu potoków
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wykonywać przewidywania wsadowe asynchronicznie w przypadku dużych ilości danych przy użyciu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 07/12/2019
ms.openlocfilehash: 910974eac6a67c9c9fe68c502f2876ef68bb94eb
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028522"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-pipelines"></a>Uruchamianie prognoz wsadowych w dużych zestawach danych przy użyciu potoków Azure Machine Learning

W tym artykule dowiesz się, jak wykonać prognozowanie dużej ilości danych asynchronicznie przy użyciu potoków ML z Azure Machine Learning.

Funkcja prognozowanie wsadowe (lub ocenianie partii) zapewnia ekonomiczną ocenę i niezrównaną przepływność dla aplikacji asynchronicznych. Potoki przewidywania wsadowe mogą być skalowane w celu przeprowadzenia wnioskowania na terabajty danych produkcyjnych. Funkcja przewidywania wsadowe jest zoptymalizowana pod kątem wysokiej przepływności, nieprawidłowych prognoz i zapamiętania dla dużej ilości danych.

>[!TIP]
> Jeśli system wymaga przetwarzania o małym opóźnieniu (w celu szybkiego przetworzenia pojedynczego dokumentu lub małego zestawu dokumentów), należy użyć [oceny czasu rzeczywistego](how-to-consume-web-service.md) zamiast przewidywania wsadowego.

W poniższych krokach utworzysz [potok uczenia maszynowego](concept-ml-pipelines.md) w celu zarejestrowania wstępnie przeszkolonego modelu przetwarzania obrazów ([od rozpoczęcia do V3](https://arxiv.org/abs/1512.00567)). Następnie korzystasz z wstępnie nauczonego modelu, aby wykonywać wsadowe oceny na obrazach dostępnych na koncie usługi Azure Blob Storage. Obrazy używane do oceniania są obrazami nieoznaczonymi z zestawu danych [ImageNet](http://image-net.org/) .

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

- Skonfiguruj środowisko programistyczne, aby zainstalować zestaw SDK Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska deweloperskiego dla Azure Machine Learning](how-to-configure-environment.md).

- Utwórz obszar roboczy Azure Machine Learning, w którym będą przechowywane wszystkie zasoby potoku. Możesz użyć poniższego kodu lub aby uzyskać więcej opcji, zobacz [Tworzenie pliku konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).

  ```python
  from azureml.core import Workspace
  ws = Workspace.create(name = '<workspace-name>',
                        subscription_id = '<subscription-id>',
                        resource_group = '<resource-group>',
                        location = '<workspace_region>',
                        exist_ok = True
                        )
  ```

## <a name="set-up-machine-learning-resources"></a>Konfigurowanie zasobów uczenia maszynowego

Poniższe kroki umożliwiają skonfigurowanie zasobów potrzebnych do uruchomienia potoku:

- Uzyskaj dostęp do magazynu danych, który ma już przedstawiony model, etykiety wejściowe i obrazy do oceny (jest to już skonfigurowane dla Ciebie).
- Skonfiguruj magazyn danych, aby przechowywać dane wyjściowe.
- Skonfiguruj wartość @ no__t-0 @ no__t-1objects, aby wskazywała dane w poprzednich magazynach danych.
- Skonfiguruj maszyny obliczeniowe lub klastry, w których zostaną uruchomione kroki potoku.

### <a name="access-the-datastores"></a>Dostęp do magazynów danych

Najpierw uzyskaj dostęp do magazynu danych z modelem, etykietami i obrazami.

Użyj publicznego kontenera obiektów BLOB o nazwie *SampleData*w koncie *pipelinedata* , które zawiera obrazy z zestawu oceny ImageNet. Nazwa magazynu danych dla tego kontenera publicznego to *images_datastore*. Zarejestruj ten magazyn danych za pomocą swojego obszaru roboczego:

```python
from azureml.core import Datastore

account_name = "pipelinedata"
datastore_name = "images_datastore"
container_name = "sampledata"

batchscore_blob = Datastore.register_azure_blob_container(ws,
                                                          datastore_name=datastore_name,
                                                          container_name=container_name,
                                                          account_name=account_name,
                                                          overwrite=True)
```

Następnie skonfiguruj, aby użyć domyślnego magazynu danych dla danych wyjściowych.

Podczas tworzenia obszaru roboczego [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)@no__t 1And [magazynu obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) are dołączonego do obszaru roboczego domyślnie. Azure Files jest domyślnym magazynem danych dla obszaru roboczego, ale można również użyć magazynu obiektów BLOB jako magazynu danych. Aby uzyskać więcej informacji, zobacz [Opcje usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>Konfigurowanie odwołań do danych

Teraz odwołują się do danych w potoku jako dane wejściowe kroków potoku.

Źródło danych w potoku jest reprezentowane przez element [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) object. @ No__t-0 @ no__t-1object wskazuje dane, które znajdują się w lub są dostępne z, magazynu danych. Potrzebujesz `DataReference` @ no__t-1objects dla katalogu używanego na potrzeby obrazów wejściowych, katalogu, w którym jest przechowywany wstępnie przemieszczony model, katalog dla etykiet i katalog wyjściowy.

```python
from azureml.data.data_reference import DataReference

input_images = DataReference(datastore=batchscore_blob,
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download")

model_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_model",
                          path_on_datastore="batchscoring/models",
                          mode="download")

label_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download")

output_dir = PipelineData(name="scores",
                          datastore=def_data_store,
                          output_path_on_compute="batchscoring/results")
```

### <a name="set-up-compute-target"></a>Skonfiguruj cel obliczeń

W Azure Machine Learning, *obliczenia* (lub *element docelowy obliczeń*) odnoszą się do maszyn lub klastrów wykonujących kroki obliczeniowe w potoku uczenia maszynowego. Na przykład można utworzyć Azure Machine Learning obliczeń z klasą [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29?view=azure-ml-py) .

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

compute_name = "gpucluster"
compute_min_nodes = 0
compute_max_nodes = 4
vm_size = "STANDARD_NC6"

if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target. just use it. ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(
        vm_size=vm_size,  # NC6 is GPU-enabled
        vm_priority='lowpriority',  # optional
        min_nodes=compute_min_nodes,
        max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws,
                                          compute_name,
                                          provisioning_config)

    compute_target.wait_for_completion(
        show_output=True,
        min_node_count=None,
        timeout_in_minutes=20)
```

## <a name="prepare-the-model"></a>Przygotuj model

Aby można było korzystać z preszkolenego modelu, należy pobrać model i zarejestrować go w obszarze roboczym.

### <a name="download-the-pretrained-model"></a>Pobieranie modelu przedniego

Pobierz wstępnie przemieszczony model przetwarzania obrazów (InceptionV3) z <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz>. Następnie wyodrębnij go do podfolderu `models`.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url = "http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>Rejestrowanie modelu

Poniżej przedstawiono sposób rejestrowania modelu:

```python
import shutil
from azureml.core.model import Model

# register downloaded model
model = Model.register(
    model_path="models/inception_v3.ckpt",
    model_name="inception",  # This is the name of the registered model
    tags={'pretrained': "inception"},
    description="Imagenet trained tensorflow inception",
    workspace=ws)
```

## <a name="write-your-scoring-script"></a>Napisz swój skrypt oceniania

>[!Warning]
>Poniższy kod jest tylko przykładem zawartym w [batch_score. PR](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py) używanym przez [przykładowy Notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb). Musisz utworzyć własny skrypt oceniania dla Twojego scenariusza.

Skrypt `batch_score.py` pobiera obrazy wejściowe w *dataset_path*, modele wstępnie nauczenia w *model_dir* i wyprowadza *Results-Label. txt* do *OUTPUT_DIR*.

```python
# Snippets from a sample scoring script
# Refer to the accompanying batch-scoring Notebook
# https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb
# for the implementation script

# Get labels
def get_class_label_dict(label_file):
  label = []
  proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
  for l in proto_as_ascii_lines:
    label.append(l.rstrip())
  return label

class DataIterator:
  # Definition of the DataIterator here

def main(_):
    # Refer to batch-scoring Notebook for implementation.
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)

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

        # copy the file to artifacts
        shutil.copy(out_filename, "./outputs/")
```

## <a name="build-and-run-the-batch-scoring-pipeline"></a>Kompilowanie i uruchamianie potoku oceniania partii

### <a name="prepare-the-run-environment"></a>Przygotuj środowisko uruchomieniowe

Określ zależności Conda dla skryptu. Ten obiekt będzie potrzebny później, podczas tworzenia kroku potoku.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

cd = CondaDependencies.create(
    pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>Określ parametr potoku

Utwórz parametr potoku za pomocą [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) object z wartością domyślną.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(
    name="param_batch_size",
    default_value=20)
```

### <a name="create-the-pipeline-step"></a>Tworzenie kroku potoku

Utwórz krok potoku przy użyciu skryptu, konfiguracji środowiska i parametrów. Określ miejsce docelowe obliczeń, które zostało już dołączone do obszaru roboczego jako element docelowy wykonywania skryptu. Użyj [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) , aby utworzyć krok potoku.

```python
from azureml.pipeline.steps import PythonScriptStep
inception_model_name = "inception_v3.ckpt"

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_score.py",
    arguments=["--dataset_path", input_images,
               "--model_name", "inception",
               "--label_dir", label_dir,
               "--output_dir", output_dir,
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config,
    source_directory=scripts_folder
```

### <a name="run-the-pipeline"></a>Uruchamianie potoku

Teraz uruchamiaj potok i badaj utworzone dane wyjściowe. Dane wyjściowe mają wynik odpowiadający każdemu obrazowi wejściowemu.

```python
import pandas as pd
from azureml.pipeline.core import Pipeline

# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(
    pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>Publikowanie potoku

Po spełnieniu wyniku przebiegu należy opublikować potok, aby można było uruchomić go z innymi wartościami wejściowymi później. Po opublikowaniu potoku otrzymujesz punkt końcowy REST. Ten punkt końcowy akceptuje wywoływanie potoku za pomocą zestawu parametrów, które zostały już dołączone przy użyciu [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py).

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring",
    description="Batch scoring using Inception v3 model",
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>Ponowne uruchamianie potoku przy użyciu punktu końcowego REST

Aby ponownie uruchomić potok, musisz mieć Azure Active Directory token nagłówka uwierzytelniania, zgodnie z opisem w [klasie AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>Następne kroki

Aby zobaczyć, jak to działa, wypróbuj Notes oceniania partii w usłudze [GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)lub przejdź do [centrum architektury platformy Azure](/azure/architecture/reference-architectures/ai/batch-scoring-python) , aby wyświetlić przykładową architekturę rozwiązania.
