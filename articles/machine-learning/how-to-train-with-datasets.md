---
title: Trenuj za pomocą zestawów danych azureml
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać zestawów danych w szkoleniach
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.openlocfilehash: 401383f2d483836bf725051810d78167869f7b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283500"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Trenuj z zestawami danych w usłudze Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się dwa sposoby korzystania z [zestawów danych usługi Azure Machine Learning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) w zdalnym treningu eksperymentu działa bez martwienia się o parametry połączenia lub ścieżki danych.

- Opcja 1: Jeśli masz dane strukturalne, utwórz zestaw danych tabelaryczne i użyj go bezpośrednio w skrypcie treningowym.

- Opcja 2: Jeśli masz nieustrukturyzowane dane, utwórz zestaw danych filedata i zainstaluj lub pobierz pliki do zdalnego obliczeń w celu szkolenia.

Zestawy danych usługi Azure Machine Learning zapewniają bezproblemową integrację z produktami szkoleniowymi usługi Azure Machine Learning, takimi jak [ScriptRun,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py) [Estymator,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) i [Potoki usługi Azure Machine Learning.](how-to-create-your-first-pipeline.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby tworzyć i trenować z zestawami danych, potrzebujesz:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* [Obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md).

* [Zainstalowano zestaw SDK usługi Azure Machine Learning dla języka Python,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)który zawiera pakiet zestawów danych azureml.

> [!Note]
> Niektóre klasy zestawu danych mają zależności w pakiecie [azureml-dataprep.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) Dla użytkowników systemu Linux te klasy są obsługiwane tylko w następujących dystrybucjach: Red Hat Enterprise Linux, Ubuntu, Fedora i CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>Opcja 1: Używanie zestawów danych bezpośrednio w skryptach szkoleniowych

W tym przykładzie można utworzyć [Zestaw danych tabelaryjskich](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) i `estimator` używać go jako bezpośrednie dane wejściowe do obiektu do szkolenia. 

### <a name="create-a-tabulardataset"></a>Tworzenie zestawu tabelaryczne dane

Poniższy kod tworzy niezarejestrowany zestaw danych tabelaryczne z adresu URL sieci Web. Można również tworzyć zestawy danych z plików lokalnych lub ścieżek w magazynach danych. Dowiedz się więcej o [tworzeniu zestawów danych](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>Dostęp do zestawu danych wejściowych w skrypcie treningowym

Obiekty TabularDataset umożliwiają załadowanie danych do pandas lub spark DataFrame, dzięki czemu można pracować ze znanymi bibliotekami przygotowującymi dane i szkoleniowymi. Aby wykorzystać tę funkcję, można przekazać Zestaw danych tabelaryjskich jako dane wejściowe w konfiguracji szkolenia, a następnie pobrać go w skrypcie.

Aby to zrobić, należy uzyskać [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) dostęp do zestawu danych [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) wejściowych za pośrednictwem obiektu w skrypcie szkolenia i użyć metody. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>Konfigurowanie estymatora

Obiekt [estymatora](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) jest używany do przesyłania przebiegu eksperymentu. Usługa Azure Machine Learning ma wstępnie skonfigurowane estymatory dla wspólnych struktur uczenia maszynowego, a także ogólny estymator.

Ten kod tworzy ogólny obiekt `est`estymatora, który określa

* Katalog skryptów dla skryptów. Wszystkie pliki w tym katalogu są przekazywane do węzłów klastra w celu wykonania.
* Skrypt szkoleniowy, *train_titanic.py*.
* Wejściowy zestaw danych `titanic`dla szkolenia, . `as_named_input()`jest wymagane, aby wejściowy zestaw danych mógł odwoływać się do przypisanej nazwy w skrypcie treningowym. 
* Cel obliczeniowy dla eksperymentu.
* Definicja środowiska dla eksperymentu.

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```


## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Opcja 2: Instalowanie plików w zdalnym celu obliczeniowym

Jeśli chcesz udostępnić pliki danych w celu szkolenia, użyj [zestawu danych FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) do instalowania lub pobierania plików, o których się odnosi.

### <a name="mount-vs-download"></a>Góra a pobieranie

Instalowanie lub pobieranie plików dowolnego formatu są obsługiwane dla zestawów danych utworzonych z magazynu obiektów Blob platformy Azure, plików Azure, usługi Azure Data Lake Storage Gen1, usługi Azure Data Lake Storage Gen2, bazy danych SQL Azure i bazy danych platformy Azure dla postgreSQL. 

Podczas instalowania zestawu danych należy dołączyć pliki, do których odwołuje się zestaw danych, do katalogu (punkt instalacji) i udostępnić go w pliku docelowym obliczeń. Montaż jest obsługiwany dla obliczeń opartych na systemie Linux, w tym usługi Azure Machine Learning Compute, maszyn wirtualnych i usługi HDInsight. Po pobraniu zestawu danych wszystkie pliki, do których odwołuje się zestaw danych, zostaną pobrane do obiektu docelowego obliczeń. Pobieranie jest obsługiwane dla wszystkich typów obliczeń. 

Jeśli skrypt przetwarza wszystkie pliki, do których odwołuje się zestaw danych, a dysk obliczeniowy może pasować do pełnego zestawu danych, pobieranie jest zalecane, aby uniknąć narzutu związane z przesyłaniem strumieniowym danych z usług magazynu. Jeśli rozmiar danych przekracza rozmiar dysku obliczeniowego, pobieranie nie jest możliwe. W tym scenariuszu zaleca się montaż, ponieważ tylko pliki danych używane przez skrypt są ładowane w czasie przetwarzania.

Poniższy kod `dataset` jest instalowany do katalogu tymczasowego`mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

### <a name="create-a-filedataset"></a>Tworzenie zestawu danych plików

Poniższy przykład tworzy niezarejestrowany zestaw danych filedata z adresów URL sieci Web. Dowiedz się więcej o [tworzeniu zestawów danych](https://aka.ms/azureml/howto/createdatasets) z innych źródeł.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-estimator"></a>Konfigurowanie estymatora

Oprócz przekazywania zestawu `inputs` danych za pośrednictwem parametru w estymatorze, można również przekazać zestaw danych i `script_params` uzyskać ścieżkę danych (punkt montażu) w skrypcie szkolenia za pomocą argumentów. W ten sposób można zachować skrypt szkolenia niezależne od azureml-sdk. Innymi słowy będzie można użyć tego samego skryptu szkoleniowego do debugowania lokalnego i zdalnego szkolenia na dowolnej platformie w chmurze.

Obiekt estymatora [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) służy do przesyłania przebiegu do eksperymentów scikit-learn. Dowiedz się więcej o szkoleniu z [estymatorem SKlearn](how-to-train-scikit-learn.md).

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>Pobieranie danych w skrypcie treningowym

Po przesłaniu uruchomienia pliki danych, `mnist` o których mowa w zestawie danych, zostaną zainstalowane w celu obliczenia obiektu docelowego. Poniższy kod pokazuje, jak pobrać dane w skrypcie.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="notebook-examples"></a>Przykłady notesów

[Notesy zestawu danych](https://aka.ms/dataset-tutorial) demonstrują i rozwijają pojęcia w tym artykule.

## <a name="next-steps"></a>Następne kroki

* [Automatyczne szkolenie modeli uczenia maszynowego](how-to-auto-train-remote.md) za pomocą zestawów danych tabelaryczne

* [Szkolenie modeli klasyfikacji obrazów](https://aka.ms/filedataset-samplenotebook) za pomocą zestawów plików

* [Trenuj z zestawami danych przy użyciu potoków](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)
