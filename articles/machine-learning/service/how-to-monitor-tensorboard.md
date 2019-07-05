---
title: Wizualizacja doświadczenia z usługą TensorBoard i Azure Machine Learning
description: Uruchamianie narzędzia TensorBoard, aby zwizualizować historie uruchomienie eksperymentu i zidentyfikować potencjalne obszary hiperparametrycznego dostrajanie i ponownego trenowania.
services: machine-learning
author: maxluk
ms.author: maxluk
ms.reviewer: nibaccam
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: babd4cdf8b7ed9e04b4bd975d840688b27439c4f
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560852"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Wizualizuj przebiegów eksperymentu i metryk przy użyciu narzędzia TensorBoard i Azure Machine Learning

W tym artykule dowiesz się, jak wyświetlić przebiegów eksperymentu i metryki w przy użyciu narzędzia TensorBoard [ `tensorboard` pakietu](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) zestawu SDK usługi głównej usługi Azure Machine Learning. Po zostały zbadane przebiegów eksperymentu, można lepiej dostosować i ponowne trenowanie modeli uczenia maszynowego.

[Narzędzia TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) to pakiet aplikacji sieci web do inspekcji i opis swojej struktury eksperyment i wydajności.

Jak uruchomić narzędzia TensorBoard z eksperymentów w usłudze Azure Machine Learning, zależy od typu eksperymentu:
+ Jeśli eksperymentu natywnie generuje pliki dziennika, które są do wykorzystania przez narzędzia TensorBoard, takich jak PyTorch, Chainer i TensorFlow eksperymentów, a następnie możesz [bezpośrednio uruchomić narzędzia TensorBoard](#direct) z eksperymentu historii uruchamiania. 

+ Eksperymenty, które natywnie nie udostępniają pliki w użyciu narzędzia TensorBoard, takie jak Scikit-learn lub usługi Azure Machine Learning eksperymentów, można użyć [ `export_to_tensorboard()` metoda](#export) do eksportowania uruchomień jako Dzienniki narzędzia TensorBoard i uruchom Narzędzia TensorBoard z tego miejsca. 

## <a name="prerequisites"></a>Wymagania wstępne

* Aby uruchomić narzędzia TensorBoard i widok historie uruchamiania eksperymentu, eksperymenty konieczne zostało wcześniej włączone rejestrowanie, aby śledzić swoje metryki i wydajności.  

* Kod w tych instrukcjach mogą być uruchamiane w jednym z następujących środowiskach: 

    * Notesu platformy Azure Machine Learning maszyny Wirtualnej — bez plików do pobrania i instalacji konieczne

        * Wykonaj [Szybki Start oparte na chmurze notesu](quickstart-run-cloud-notebook.md#create-notebook) utworzyć dedykowany serwer wstępnie załadowane z zestawu SDK i przykładowe repozytorium.

        * W folderze samples na serwerze Notes, Znajdź dwie ukończone i rozszerzona notesów, przechodząc do tego katalogu: **jak-to-użyj-usługi Azure ml > szkolenia z głębokiego uczenia**.
        * export-Run-history-to-Run-history.ipynb
        * tensorboard.ipynb

    * Twój własny serwer notesu Juptyer
      * Użyj [Tworzenie artykułu w obszarze roboczym](setup-create-workspace.md) do
          * [Zainstaluj zestaw SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) z `tensorboard` dodatkowe
          * Tworzenie obszaru roboczego i jego pliku konfiguracji (config.json)
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Opcja 1: Bezpośrednio Widok historii uruchamiania w aplikacji TensorBoard

Ta opcja działa w przypadku eksperymentów, czy natywnie dane wyjściowe pliki dzienników są w użyciu przez narzędzia TensorBoard, takich jak PyTorch, Chainer i TensorFlow experiments. Jeśli to nie przypadek eksperymentu, użyj [ `export_to_tensorboard()` metoda](#export) zamiast tego.

Poniższy przykład kodu używa [mnist ręcznie ZAPISANYCH wersji demonstracyjnej eksperymentu](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) z repozytorium TensorFlow firmy w celu zdalnego obliczeń obliczeniowego usługi Azure Machine Learning. Następnie uczenie nasz model przy użyciu niestandardowego zestawu SDK [TensorFlow narzędzie do szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), a następnie uruchom narzędzia TensorBoard względem tego eksperymentu TensorFlow, czyli eksperyment, który natywnie generuje pliki zdarzeń narzędzia TensorBoard.

### <a name="set-experiment-name-and-create-project-folder"></a>Ustaw nazwę eksperymentu, a następnie utwórz folder projektu

W tym miejscu użyjemy nazwy eksperymentu i tworzenie folderu. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Pobierz program code eksperymentu pokaz TensorFlow

Repozytorium firmy TensorFlow ma pokaz mnist ręcznie ZAPISANYCH z rozbudowaną instrumentacji aplikacji TensorBoard. Firma Microsoft nie obsługują ani nie muszą, zmiany kodu w tej wersji demonstracyjnej dla niego do pracy z usługą Azure Machine Learning. W poniższym kodzie możemy pobrać kod mnist ręcznie ZAPISANYCH i zapisz go w naszej eksperymentu nowo utworzony folder.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
W całym pliku kodu mnist ręcznie ZAPISANYCH, mnist_with_summaries.py, zwróć uwagę, że istnieją wiersze to wywołanie `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` itp. Te grupy metod, log oraz tag kluczowe metryki eksperymenty w historii uruchamiania. `tf.summary.FileWriter()` Jest szczególnie ważne, to szereguje dane z metryk rejestrowane eksperymentu umożliwia TensorBoard generować wizualizacje zniżki w stosunku do nich.

 ### <a name="configure-experiment"></a>Konfigurowanie eksperymentu

Poniższa możemy skonfigurować nasz eksperyment i skonfigurować katalogów dla dzienników i danych. Te dzienniki będą przekazywane do usługi artefaktu TensorBoard później uzyskuje dostęp do.

>[!Note]
> W tym przykładzie TensorFlow należy zainstalować TensorFlow na komputerze lokalnym. Ponadto moduł TensorBoard (czyli jeden dołączone TensorFlow) muszą być dostępne dla jądra ten notes się komputera lokalnego, co działa TensorBoard.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Tworzenie klastra dla swojego eksperymentu
Możemy utworzyć klaster AmlCompute, w tym eksperymencie, jednak w dowolnym środowisku można tworzyć eksperymenty i nadal można uruchomić narzędzia TensorBoard względem eksperymentu, historii uruchamiania. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>Prześlij przebieg z TensorFlow narzędzie do szacowania

Narzędzie do szacowania TensorFlow zapewnia prosty sposób uruchamiania zadania szkolenia TensorFlow na obliczeniowego elementu docelowego. Jest ona wdrożona za pomocą ogólnego [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klasy, która może służyć do obsługi dowolnej platformy. Aby uzyskać więcej informacji na temat szkolenie modeli za pomocą ogólnego narzędzie do szacowania zobacz [uczenia modeli za pomocą usługi Azure Machine Learning przy użyciu narzędzie do szacowania](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Uruchamianie narzędzia TensorBoard

Można uruchomić narzędzia TensorBoard, podczas przebieg lub po jej zakończeniu. W następującym, możemy utworzyć wystąpienie obiektu TensorBoard, `tb`, przyjmuje uruchamianie eksperymentu historii załadowany w `run`i następnie uruchamia TensorBoard za pomocą `start()` metody. 
  
[Konstruktor TensorBoard](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) pobiera tablicę przebiegów, więc upewnij się i przekaż go w postaci pojedynczego elementu tablicy.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Opcja 2: Eksportowanie historii jako dziennik, aby wyświetlać w aplikacji TensorBoard

Poniższy kod konfiguruje eksperymentów przykładowych, rozpoczyna się proces rejestrowania, korzystając z usługi Azure Machine Learning API historii uruchamiania i eksportuje eksperymentu, historii uruchamiania w dzienniki eksploatacyjnych przez narzędzia TensorBoard wizualizacji. 

### <a name="set-up-experiment"></a>Konfigurowanie eksperymentu

Poniższy kod ustawia nowy eksperyment i nazwy katalogu uruchamiania `root_run`. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

W tym miejscu możemy załadować zestawu danych choroby — wbudowanych małych zestawu danych, który jest dostarczany z scikit-Dowiedz się, a następnie podzielić ją na test i szkolenia zestawów.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Uruchom eksperyment i rejestrowanie metryki

Dla tego kodu, możemy uczenia modelu regresji liniowej i dziennika kluczowych metryk, które współczynnika alfa `alpha` oznaczają kwadratów błędów i `mse`, w historii uruchamiania.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Eksportowanie przebiegów, aby TensorBoard

Za pomocą zestawu SDK [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) metody, firma Microsoft może wyeksportować historię przebiegów naszych eksperymentu usługi Azure machine learning do dzienników aplikacji TensorBoard, dzięki czemu będziemy mogli wyświetlać je za pomocą narzędzia TensorBoard.  

W poniższym kodzie, możemy utworzyć folder `logdir` w naszym bieżącego katalogu roboczego. Ten folder jest, gdzie zostaną wyeksportowane nasz eksperyment historii uruchamiania i dzienniki z `root_run` , a następnie oznacz uruchamianą jako ukończone. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 Można również wyeksportować danego uruchomienia do narzędzia TensorBoard, określając nazwę przebiegu  `export_to_tensorboard(run_name, logdir)`

Uruchamianie i zatrzymywanie raz TensorBoard naszej historii uruchamiania w tym eksperymencie są eksportowane, możemy uruchomić TensorBoard z [start()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) metody. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Gdy wszystko będzie gotowe, upewnij się, że wywołanie [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) metody obiektu narzędzia TensorBoard. W przeciwnym razie TensorBoard będą nadal działać, dopóki nie zostanie zamknięta jądra w notesie. 

```python
tb.stop()
```

## <a name="next-steps"></a>Kolejne kroki

W tym porad, utworzyliśmy dwa doświadczenia i przedstawiono sposób uruchamiania narzędzia TensorBoard przed ich uruchomień do identyfikacji obszarów potencjalnych dostosowywania i ponownego trenowania. 

* Jeśli jesteś zadowolony z modelu, przejdź do naszego [sposób wdrażania modelu](how-to-deploy-and-where.md) artykułu. 
* Dowiedz się więcej o [strojenia hiperparametrycznego](how-to-tune-hyperparameters.md). 
