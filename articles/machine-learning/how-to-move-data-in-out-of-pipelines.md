---
title: Dane wejściowe i wyjściowe z potoków ml
titleSuffix: Azure Machine Learning
description: Przygotowywanie, używanie i generowanie danych w potokach usługi Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 3dd1a82bf7fad1f201f5c0f52af944ef44a3fdf9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879767"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Przenoszenie danych do i między krokami potoku ml (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dane mają kluczowe znaczenie dla potoków uczenia maszynowego. Ten artykuł zawiera kod do importowania, przekształcania i przenoszenia danych między krokami w potoku usługi Azure Machine Learning. Aby uzyskać omówienie działania danych w usłudze Azure Machine Learning, zobacz [Dostęp do danych w usługach magazynu platformy Azure.](how-to-access-data.md) Aby uzyskać korzyści i strukturę potoków usługi Azure Machine Learning, zobacz [Co to są potoki usługi Azure Machine Learning?](concept-ml-pipelines.md)

W tym artykule dowiesz się, jak:

- Używanie `Dataset` obiektów do wcześniej istniejących danych
- Dostęp do danych w ramach kroków
- Dzielenie `Dataset` danych na podzbiory, takie jak podzbiory szkoleń i sprawdzania poprawności
- Tworzenie `PipelineData` obiektów w celu przesyłania danych do następnego kroku potoku
- Używanie `PipelineData` obiektów jako danych wejściowych do kroków potoku
- Tworzenie `Dataset` nowych `PipelineData` obiektów z chcesz upierać

## <a name="prerequisites"></a>Wymagania wstępne

Będą potrzebne:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree).

- Zestaw [SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)lub dostęp do studia usługi Azure Machine [Learning](https://ml.azure.com/).

- Obszar roboczy usługi Azure Machine Learning.
  
  Utwórz [obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md) lub użyj istniejącego za pośrednictwem zestawu SDK języka Python. Zaimportuj `Workspace` i `Datastore` klasy i załadować informacje subskrypcji z pliku `config.json` przy użyciu funkcji `from_config()`. Ta funkcja domyślnie wyszukuje plik JSON w bieżącym katalogu, ale można również `from_config(path="your/file/path")`określić parametr ścieżki wskazujący plik za pomocą programu .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Niektóre istniejące dane. W tym artykule pokrótce przedstawiono użycie [kontenera obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Opcjonalnie: istniejący potok uczenia maszynowego, taki jak opisany w [obszarze Tworzenie i uruchamianie potoków uczenia maszynowego za pomocą narzędzia Azure Machine Learning SDK.](how-to-create-your-first-pipeline.md)

## <a name="use-dataset-objects-for-pre-existing-data"></a>Używanie `Dataset` obiektów do wcześniej istniejących danych 

Preferowanym sposobem pozyskiwania danych do potoku jest użycie obiektu [Zestawu danych.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) `Dataset`obiekty reprezentują trwałe dane dostępne w całym obszarze roboczym.

Istnieje wiele sposobów tworzenia `Dataset` i rejestrowania obiektów. Tabelaryczne zestawy danych są dla rozdzielonych danych dostępnych w jednym lub więcej plikach. Zestawy danych plików są przeznaczone dla danych binarnych (takich jak obrazy) lub danych, które będą analizowane. Najprostszym programowym sposobem `Dataset` tworzenia obiektów jest użycie istniejących obiektów blob w magazynie obszaru roboczego lub publicznych adresach URL:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Aby uzyskać więcej opcji tworzenia zestawów danych z różnymi opcjami i z różnych źródeł, rejestrowania ich i przeglądania ich w interfejsie użytkownika usługi Azure Machine Learning, dowiedzieć się, jak rozmiar danych współdziała z pojemnością obliczeniową i ich przechowywania wersji, zobacz [Tworzenie zestawów danych usługi Azure Machine Learning.](how-to-create-register-datasets.md) 

### <a name="pass-datasets-to-your-script"></a>Przekazywanie zestawów danych do skryptu

Aby przekazać ścieżkę zestawu danych do skryptu, `Dataset` `as_named_input()` użyj metody obiektu. Wynikowy `DatasetConsumptionConfig` obiekt można przekazać do skryptu jako argument lub, `inputs` za pomocą argumentu do skryptu potoku, można pobrać zestaw danych za pomocą programu `Run.get_context().input_datasets[]`.

Po utworzeniu nazwanego wejścia można wybrać jego `as_mount()` tryb `as_download()`dostępu: lub . Jeśli skrypt przetwarza wszystkie pliki w zestawie danych, a dysk w zasobie obliczeniowym jest wystarczająco duży dla zestawu danych, lepszym wyborem jest tryb dostępu do pobierania. Tryb dostępu do pobierania pozwoli uniknąć narzutu przesyłania strumieniowego danych w czasie wykonywania. Jeśli skrypt uzyskuje dostęp do podzbioru zestawu danych lub jest zbyt duży dla obliczeń, użyj trybu dostępu do instalacji. Aby uzyskać więcej informacji, przeczytaj [artykuł Góra vs. Pobierz](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Aby przekazać zestaw danych do kroku potoku:

1. Użyj `TabularDataset.as_named_inputs()` `FileDataset.as_named_input()` lub (nie "s" na `DatasetConsumptionConfig` końcu), aby utworzyć obiekt
1. Użyj `as_mount()` `as_download()` lub ustaw tryb dostępu
1. Przekazywanie zestawów danych do kroków `arguments` potoku `inputs` przy użyciu lub argumentu

Poniższy fragment kodu pokazuje wspólny wzorzec łączenia `PythonScriptStep` tych kroków w konstruktorze: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

Można również użyć metod, takich jak `random_split()` i `take_sample()` utworzyć wiele danych wejściowych lub zmniejszyć ilość danych przekazanych do kroku potoku:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Dostęp do zestawów danych w skrypcie

Nazwane dane wejściowe do skryptu kroku potoku są dostępne jako słownik w `Run` obiekcie. Pobierz aktywny `Run` obiekt `Run.get_context()` za pomocą obiektu aktywnego, a następnie `input_datasets`pobierz słownik nazwanych danych wejściowych za pomocą programu . `DatasetConsumptionConfig` Jeśli obiekt został przekazany `arguments` przy użyciu `inputs` argumentu, a `ArgParser` nie argumentu, dostęp do danych przy użyciu kodu. Obie techniki przedstawiono w poniższym urywek.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

Przekazana wartość będzie ścieżką do plików zestawu danych.

Istnieje również możliwość bezpośredniego dostępu `Dataset` do zarejestrowanego. Ponieważ zarejestrowane zestawy danych są trwałe i współużytkowane w obszarze roboczym, można je pobrać bezpośrednio:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>Zastosowanie `PipelineData` do danych pośrednich

Podczas `Dataset` gdy obiekty reprezentują dane trwałe, [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) obiekty są używane do danych tymczasowych, który jest wyprowadzany z kroków potoku. Ponieważ żywotność obiektu jest `PipelineData` dłuższa niż pojedynczy krok potoku, można zdefiniować je w skrypcie definicji potoku. Podczas tworzenia `PipelineData` obiektu należy podać nazwę i magazyn danych, w którym będą znajdować się dane. Przekaż `PipelineData` obiekt do swoich `PythonScriptStep` argumentów, `arguments` używając `outputs` _zarówno_ argumentów:

```python
default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)
```

Obiekt można utworzyć `PipelineData` w trybie dostępu, który zapewnia natychmiastowe przekazywanie. W takim przypadku podczas `PipelineData`tworzenia , `upload_mode` `"upload"` ustaw do `output_path_on_compute` i użyj argumentu, aby określić ścieżkę, do której będziesz zapisywać dane:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Użyj `PipelineData` jako wyników etapu szkolenia

W obrębie potoku `PythonScriptStep`można pobrać dostępne ścieżki wyjściowe przy użyciu argumentów programu. Jeśli ten krok jest pierwszy i zainisalizuje dane wyjściowe, należy utworzyć katalog w określonej ścieżce. Następnie możesz napisać dowolne pliki, które `PipelineData`chcesz mieć w pliku .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Jeśli `PipelineData` utworzono `is_directory` swój argument `True`ustawiony na , to `os.makedirs()` wystarczy, aby po prostu wykonać połączenie, a następnie będzie można napisać, co chcesz pliki do ścieżki. Aby uzyskać więcej informacji, zobacz [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) dokumentacji referencyjnej.

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Odczytywane `PipelineData` jako dane wejściowe do kroków innych niż początkowe

Po początkowym kroku potoku zapisuje niektóre dane do `PipelineData` ścieżki i staje się wyjściem tego kroku początkowego, może służyć jako dane wejściowe do późniejszego kroku:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

Wartość `PipelineData` danych wejściowych jest ścieżką do poprzedniego wyjścia. Jeśli, jak pokazano wcześniej, pierwszy krok napisał pojedynczy plik, zużywając go może wyglądać: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Konwertowanie `PipelineData` obiektów na `Dataset`s

Jeśli chcesz udostępnić ją `PipelineData` na dłużej niż czas trwania `as_dataset()` biegu, użyj jego `Dataset`funkcji, aby przekonwertować go na plik . Następnie można zarejestrować `Dataset`, co czyni go obywatelem pierwszej klasy w swojej przestrzeni roboczej. Ponieważ `PipelineData` obiekt będzie miał inną ścieżkę za każdym razem, gdy `create_new_version` potok `True` jest uruchamiany, zdecydowanie zaleca się ustawienie podczas rejestrowania utworzonego `Dataset` z `PipelineData` obiektu.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zestawu danych usługi Azure machine learning](how-to-create-register-datasets.md)
* [Tworzenie i uruchamianie potoków uczenia maszynowego za pomocą zestawu SDK usługi Azure Machine Learning](how-to-create-your-first-pipeline.md)
