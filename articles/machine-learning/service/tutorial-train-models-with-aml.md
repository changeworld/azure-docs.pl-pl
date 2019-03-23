---
title: 'Samouczek dotyczący klasyfikacji obrazów: Szkolenie modeli'
titleSuffix: Azure Machine Learning service
description: W tym samouczku pokazano, jak za pomocą usługi Azure Machine Learning przeprowadzić uczenie modelu klasyfikacji obrazów, używając biblioteki scikit-learn w notesie Jupyter języka Python. Ten samouczek jest pierwszą częścią dwuczęściowej serii.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 01/28/2019
ms.custom: seodec18
ms.openlocfilehash: e7617aec2739daa4f84bcecab060ae0f8e28fabe
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361595"
---
# <a name="tutorial-train-an-image-classification-model-with-azure-machine-learning-service"></a>Samouczek: Uczenie modelu klasyfikacji obrazów za pomocą usługi Azure Machine Learning

W tym samouczku przeprowadzisz szkolenie modelu uczenia maszynowego na zdalnych zasobach obliczeniowych. Zastosujesz przepływ pracy uczenia i wdrażania dla usługi Azure Machine Learning (wersja zapoznawcza) w notesie Jupyter języka Python.  Następnie możesz użyć notesu jako szablonu do uczenia własnego modelu uczenia maszynowego z użyciem własnych danych. Ten samouczek jest **pierwszą częścią dwuczęściowej serii**.  

Ten samouczek uczy prostej regresji logistycznej przy użyciu zestawu danych [MNIST](http://yann.lecun.com/exdb/mnist/), biblioteki [scikit-learn](https://scikit-learn.org) oraz usługi Azure Machine Learning. MNIST jest popularnym zestawem danych składającym się z 70 000 obrazów w skali szarości. Każdy obraz ma rozmiar 28 x 28 pikseli i przedstawia odręcznie napisaną cyfrę z zakresu od 0 do 9. Celem jest utworzenie klasyfikatora wieloklasowego do identyfikacji cyfry reprezentowanej przez dany obraz. 

Dowiedz się, jak wykonać następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie środowiska projektowego.
> * Uzyskiwanie dostępu do danych i badanie ich.
> * Uczenie modelu regresji logistycznej proste w zdalnym klastrze.
> * Przeglądanie wyników uczenia i rejestrowanie najlepszego modelu.

Tego, jak wybrać i wdrożyć model, dowiesz się z [drugiej części tego samouczka](tutorial-deploy-models-with-aml.md). 

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

>[!NOTE]
> Kod w tym artykule został przetestowany przy użyciu zestawu Azure Machine Learning SDK w wersji 1.0.8.

## <a name="prerequisites"></a>Wymagania wstępne

Przejdź do sekcji [Konfigurowanie środowiska projektowego](#start), aby zapoznać się z instrukcjami dotyczącymi notesu, lub skorzystaj z poniższych instrukcji, aby pobrać notes i uruchomić go w usłudze Azure Notebooks lub na swoim serwerze notesów.  Do uruchomienia notesu potrzebne są następujące elementy:

* Serwer notesów Python 3.6 z zainstalowanym następującym oprogramowaniem:
    * Zestaw SDK usługi Azure Machine Learning dla języka Python
    * `matplotlib` i `scikit-learn`
* Notes samouczka i plik utils.py
* Obszar roboczy uczenia maszynowego 
* Plik konfiguracji obszaru roboczego w tym samym katalogu co notes 

Wszystkie te wymagania wstępne można spełnić, korzystając z jednej z poniższych sekcji.
 
* Korzystanie z usługi [Azure Notebooks](#azure) 
* Korzystanie z [własnego serwera notesów](#server)

### <a name="azure"></a>Korzystanie z usługi Azure Notebooks: bezpłatne notesy Jupyter Notebook w chmurze

Rozpoczęcie pracy z usługą Azure Notebooks jest bardzo proste. [Zestaw Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk) został już zainstalowany i skonfigurowany w usłudze [Azure Notebooks](https://notebooks.azure.com/). Instalacja i przyszłe aktualizacje są automatycznie zarządzane za pomocą usług platformy Azure.

Po wykonaniu poniższych czynności uruchom notes **tutorials/img-classification-part1-training.ipynb** w projekcie **Wprowadzenie**.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]


### <a name="server"></a>Korzystanie z własnego serwera notesów Jupyter Notebook

Wykonaj te kroki, aby utworzyć lokalny serwer notesów Jupyter Notebook na komputerze. 

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

 Po wykonaniu czynności uruchom notes **tutorials/img-classification-part1-training.ipynb**.

## <a name="start"></a>Konfigurowanie środowiska projektowego

Cała konfiguracja dla prac programistycznych może zostać wykonana w notesie języka Python. Konfiguracja obejmuje następujące czynności:

* Importowanie pakietów języka Python.
* Nawiązywanie połączenia z obszarem roboczym, aby komputer lokalny mógł komunikować się z zasobami zdalnymi.
* Tworzenie eksperymentu do śledzenia wszystkich przebiegów.
* Tworzenie zdalnego docelowego zasobu obliczeniowego na potrzeby uczenia.

### <a name="import-packages"></a>Importowanie pakietów

Zaimportuj pakiety języka Python, które są potrzebne w tej sesji. Wyświetl również wersję zestawu Azure Machine Learning SDK:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Łączenie z obszarem roboczym

Utwórz obiekt obszaru roboczego na podstawie istniejącego obszaru roboczego. Metoda `Workspace.from_config()` odczytuje plik **config.json** i ładuje szczegóły do obiektu o nazwie `ws`:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, ws.location, sep = '\t')
```

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Utwórz eksperyment do śledzenia przebiegów w Twoim obszarze roboczym. Obszar roboczy może zawierać wiele eksperymentów: 

```python
experiment_name = 'sklearn-mnist'

from azureml.core import Experiment
exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-resource"></a>Tworzenie lub dołączanie istniejącego zasobu obliczeniowego

Za pomocą usługi zarządzanej Azure Machine Learning Compute analitycy danych mogą szkolić modele uczenia maszynowego w klastrach maszyn wirtualnych platformy Azure. Przykłady obejmują maszyny wirtualne z obsługą procesorów GPU. W tym samouczku utworzysz usługę Azure Machine Learning Compute jako środowisko uczenia. Poniższy kod utworzy za Ciebie klastry obliczeniowe, jeśli nie istnieją one jeszcze w Twoim obszarze roboczym.

 **Tworzenie klastrów obliczeniowych zajmuje około pięciu minut.** Jeśli klastry obliczeniowe znajdują się już w obszarze roboczym, kod skorzysta z nich i pominie proces tworzenia.


```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Teraz masz pakiety i zasoby obliczeniowe niezbędne do przeprowadzenia uczenia modelu w chmurze. 

## <a name="explore-data"></a>Eksplorowanie danych

Zanim nauczysz model, musisz zrozumieć dane używane na potrzeby uczenia. Musisz też skopiować dane do chmury, aby były one dostępne dla Twojego środowiska uczenia w chmurze. W tej sekcji dowiesz się, jak wykonać następujące czynności:

* Pobieranie zestawu danych MNIST
* Wyświetlanie przykładowych obrazów
* Przekazywanie danych do chmury

### <a name="download-the-mnist-dataset"></a>Pobieranie zestawu danych MNIST

Pobierz zestaw danych MNIST i zapisz pliki lokalnie w katalogu `data`. Zostaną pobrane obrazy i etykiety zarówno na potrzeby uczenia, jak i testowania:


```python
import urllib.request
import os

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename=os.path.join(data_folder, 'train-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename=os.path.join(data_folder, 'train-labels.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename=os.path.join(data_folder, 'test-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename=os.path.join(data_folder, 'test-labels.gz'))
```
Zostaną wyświetlone dane wyjściowe podobne do tych: ```('./data/test-labels.gz', <http.client.HTTPMessage at 0x7f40864c77b8>)```

### <a name="display-some-sample-images"></a>Wyświetlanie przykładowych obrazów

Załaduj pliki skompresowane do tablic `numpy`. Następnie użyj `matplotlib` do wykreślenia 30 losowych obrazów z zestawu danych wraz z ich etykietami nad nimi. Ten krok wymaga funkcji `load_data` uwzględnionej w pliku `util.py`. Ten plik znajduje się w folderze przykładu. Upewnij się, że znajduje się on w tym samym folderze co ten notes. Funkcja `load_data` po prostu analizuje skompresowane pliki i przetwarza je w tablice numpy:



```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize = (16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Zostanie wyświetlona losowa próbka obrazów:

![Losowa próbka obrazów](./media/tutorial-train-models-with-aml/digits.png)

Teraz wiesz już, jak wyglądają te obrazy i jakie są oczekiwane wyniki przewidywania.

### <a name="upload-data-to-the-cloud"></a>Przekazywanie danych do chmury

Teraz umożliw zdalny dostęp do danych, przekazując je ze swojego komputera lokalnego na platformę Azure. Będą one wtedy dostępne dla zdalnego uczenia. Magazyn danych to wygodna konstrukcja skojarzona z Twoim obszarem roboczym służąca do przekazywania/pobierania danych. Umożliwia także interakcję z nimi z poziomu zdalnych docelowych zasobów obliczeniowych. Jest on wspierany przez konto usługi Azure Blob Storage.

Pliki MNIST są przekazywane do katalogu o nazwie `mnist` w folderze głównym magazynu danych:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir=data_folder, target_path='mnist', overwrite=True, show_progress=True)
```
Masz teraz wszystko, czego potrzebujesz, aby rozpocząć uczenie modelu. 


## <a name="train-on-a-remote-cluster"></a>Uczenie w klastrze zdalnym

Na potrzeby tego samouczka prześlij zadanie do skonfigurowanego wcześniej zdalnego klastra uczenia.  W celu przesłania zadania wykonywane są następujące czynności:
* Tworzenie katalogu
* Tworzenie skryptu uczenia
* Tworzenie obiektu narzędzia do szacowania
* Przesyłanie zadania 

### <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz katalog w celu dostarczenia niezbędnego kodu ze swojego komputera do zasobu zdalnego.

```python
import os
script_folder  = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Tworzenie skryptu uczenia

Aby przesłać zadanie do klastra, najpierw utwórz skrypt uczenia. Uruchom poniższy kod, aby utworzyć skrypt uczenia o nazwie `train.py` w katalogu, który został właśnie utworzony.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Zwróć uwagę, jak skrypt pobiera dane i zapisuje modele:

+ Skrypt uczenia odczytuje argument, aby znaleźć katalog zawierający dane. Podczas późniejszego przesyłania zadania wskażesz magazyn danych dla tego argumentu: `parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')`.

+ Skrypt uczenia zapisuje model w katalogu o nazwie **outputs**: <br/>
`joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`.<br/>
Dowolne pliki zapisane w tym katalogu są automatycznie przekazywane do Twojego obszaru roboczego. W dalszej części samouczka z poziomu tego katalogu uzyskasz dostęp do swojego modelu.
Plik `utils.py` jest wywoływany przez skrypt uczenia, aby prawidłowo załadować zestaw danych. Skopiuj ten skrypt do folderu skryptów, aby był dostępny wraz ze skryptem uczenia zasobu zdalnego.


```python
import shutil
shutil.copy('utils.py', script_folder)
```


### <a name="create-an-estimator"></a>Tworzenie narzędzia do szacowania

Obiekt narzędzia do szacowania służy do przesyłania przebiegu. Utwórz narzędzie do szacowania, uruchamiając następujący kod, aby zdefiniować następujące elementy:

* Nazwa obiektu narzędzia do szacowania, `est`.
* Katalog zawierający Twoje skrypty. Wszystkie pliki w tym katalogu są przekazywane do węzłów klastra w celu wykonania. 
* Docelowy zasób obliczeniowy. W tym przypadku użyjesz utworzonego klastra obliczeniowego usługi Azure Machine Learning.
* Nazwa skryptu uczenia, **train.py**.
* Wymagane parametry skryptu uczenia. 
* Pakiety języka Python wymagane na potrzeby uczenia.

W tym samouczku elementem docelowym jest usługa AmlCompute. Wszystkie pliki w folderze skryptów są przekazywane do węzłów klastra w celu uruchomienia. Folder danych **data_folder** ustawiono w celu używania magazynu danych — `ds.path('mnist').as_mount()`:

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--regularization': 0.8
}

est = Estimator(source_directory=script_folder,
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py',
                conda_packages=['scikit-learn'])
```


### <a name="submit-the-job-to-the-cluster"></a>Przesyłanie zadania do klastra

Uruchom eksperyment, przesyłając obiekt narzędzia do szacowania:

```python
run = exp.submit(config=est)
run
```

Ponieważ wywołanie jest asynchroniczne, zwraca ono stan **Przygotowywanie** lub **Uruchomiono** zaraz po uruchomieniu zadania.

## <a name="monitor-a-remote-run"></a>Monitorowanie zdalnego przebiegu

Łącznie pierwszy przebieg trwa **około 10 minut**. Jednak podczas kolejnych przebiegów, o ile zależności skryptu nie ulegną zmianie, jest ponownie używany ten sam obraz. Dlatego czas uruchamiania kontenera jest znacznie krótszy.

Co się dzieje podczas oczekiwania:

- **Tworzenie obrazu**: tworzony jest obraz platformy Docker zgodny ze środowiskiem Python określonym przez narzędzie do szacowania. Obraz jest przekazywany do obszaru roboczego. Tworzenie obrazu i jego przekazywanie trwa **około pięciu minut**. 

  Ten etap jest wykonywany tylko raz dla każdego środowiska Python, ponieważ kontener jest buforowany dla kolejnych przebiegów. Podczas tworzenia obrazu dzienniki są przesyłane strumieniowo do historii uruchamiania. Postęp tworzenia obrazu możesz monitorować przy użyciu tych dzienników.

- **Skalowanie**: jeśli klaster zdalny wymaga więcej węzłów do wykonania przebiegu niż jest obecnie dostępnych, dodatkowe węzły są dodawane automatycznie. Skalowanie zazwyczaj trwa **około pięciu minut.**

- **Uruchamianie**: na tym etapie niezbędne skrypty i pliki są wysyłane do docelowego zasobu obliczeniowego. Następnie magazyny danych są instalowane lub kopiowane. Następnie uruchamiany jest skrypt **entry_script**. Podczas działania zadania dane z wyjścia **stdout** i katalogu **./logs** są przesyłane strumieniowo do historii uruchamiania. Postęp przebiegu możesz monitorować przy użyciu tych dzienników.

- **Przetwarzanie końcowe**: katalog **./outputs** przebiegu jest kopiowany do historii uruchamiania w Twoim obszarze roboczym, więc możesz uzyskać dostęp do tych wyników.


Postęp działającego zadania możesz sprawdzić na kilka sposobów. W tym samouczku jest używany widżet Jupyter oraz metoda `wait_for_completion`. 

### <a name="jupyter-widget"></a>Widżet Jupyter

Obserwuj postęp przebiegu za pomocą widżetu Jupyter. Podobnie jak przesyłanie przebiegu, widżet jest asynchroniczny i udostępnia aktualizacje na bieżąco co 10–15 sekund aż do zakończenia zadania:


```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

Ten zrzut ekranu przedstawia widżet wyświetlany na końcu uczenia:

![Widżet notesu](./media/tutorial-train-models-with-aml/widget.png)

Jeśli musisz anulować przebieg, możesz wykonać [te instrukcje](https://aka.ms/aml-docs-cancel-run).

### <a name="get-log-results-upon-completion"></a>Pobieranie wyników dziennika po zakończeniu

Uczenie i monitorowanie modelu odbywa się w tle. Poczekaj na zakończenie uczenia modelu przed uruchomieniem dalszego kodu. Za pomocą metody `wait_for_completion` można wyświetlić informację o zakończeniu trenowania modelu: 


```python
run.wait_for_completion(show_output=False) # specify True for a verbose log
```

### <a name="display-run-results"></a>Wyświetlanie wyników przebiegu

Teraz masz nauczony model w klastrze zdalnym. Pobierz dokładność modelu:

```python
print(run.get_metrics())
```
Dane wyjściowe pokazują, że model zdalny ma dokładność 0,9204:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

W następnym samouczku bardziej szczegółowo poznasz ten model.

## <a name="register-model"></a>Rejestrowanie modelu

W ramach ostatniego kroku skryptu uczenia plik `outputs/sklearn_mnist_model.pkl` został zapisany w katalogu o nazwie `outputs` na maszynie wirtualnej klastra, gdzie zadanie jest uruchamiane. Katalog `outputs` jest katalogiem specjalnym, ponieważ cała jego zawartość jest automatycznie przekazywana do Twojego obszaru roboczego. Ta zawartość jest widoczna w rekordzie przebiegu eksperymentu w ramach Twojego obszaru roboczego. W związku z tym plik modelu jest teraz również dostępny w Twoim obszarze roboczym.

Możesz zobaczyć pliki skojarzone z tym przebiegiem:

```python
print(run.get_file_names())
```

Zarejestruj model w obszarze roboczym, aby umożliwić sobie (lub innym współpracownikom) późniejsze badanie i wdrażanie tego modelu oraz wykonywanie zapytań względem niego:

```python
# register model 
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep = '\t')
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Usunąć możesz również sam klaster obliczeniowy usługi Azure Machine Learning. Jednak ponieważ włączona jest funkcja skalowania automatycznego i minimalna wielkość klastra jest równa zero, dla tego konkretnego zasobu nie będą naliczane dodatkowe opłaty za obliczenia, gdy nie jest on używany:


```python
# optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku usługi Azure Machine Learning za pomocą języka Python wykonano następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie środowiska projektowego.
> * Uzyskiwanie dostępu do danych i badanie ich.
> * Szkolenie wielu modeli w klastrze zdalnym przy użyciu popularnej biblioteki uczenia maszynowego scikit-learn
> * Przeglądanie szczegółów uczenia i rejestrowanie najlepszego modelu.

Wszystko jest już gotowe do wdrożenia tego zarejestrowanego modelu zgodnie z instrukcjami w następnej części serii samouczków:

> [!div class="nextstepaction"]
> [Samouczek 2 — wdrażanie modeli](tutorial-deploy-models-with-aml.md)
