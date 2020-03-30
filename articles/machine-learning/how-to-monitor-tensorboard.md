---
title: Wizualizowanie eksperymentów za pomocą rozwiązania TensorBoard
titleSuffix: Azure Machine Learning
description: Uruchom TensorBoard, aby wizualizować historie przebiegu eksperymentów i identyfikować potencjalne obszary dostrajania i ponownego trenowania hiperparametryczne.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: b6b7e47acdbc5bd059e17e512731bd09c8580798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78195383"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Wizualizuj przebiegi eksperymentów i metryki za pomocą tensorboard i usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak wyświetlić przebiegi eksperymentu i metryki w TensorBoard przy użyciu [ `tensorboard` pakietu](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) w głównym pakietu Azure Machine Learning SDK. Po sprawdzeniu przebiegów eksperymentu można lepiej dostroić i przekwalifikować modele uczenia maszynowego.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) to zestaw aplikacji internetowych do sprawdzania i zrozumienia struktury eksperymentów i wydajności.

Sposób uruchamiania tensorboardu za pomocą eksperymentów usługi Azure Machine Learning zależy od typu eksperymentu:
+ Jeśli eksperyment natywnie wyprowadza pliki dziennika, które są używane przez TensorBoard, takich jak PyTorch, Chainer i TensorFlow eksperymentów, a następnie można [uruchomić TensorBoard bezpośrednio](#direct) z historii uruchamiania eksperymentu. 

+ W przypadku eksperymentów, które nie natywnie wyprowadzają plików materiałów eksploatacyjnych TensorBoard, takich jak Scikit-learn lub eksperymenty usługi Azure Machine Learning, użyj [ `export_to_tensorboard()` tej metody,](#export) aby wyeksportować historie uruchamiania jako dzienniki TensorBoard i uruchomić tensorboard stamtąd. 

> [!TIP]
> Informacje zawarte w tym dokumencie są przeznaczone przede wszystkim dla analityków danych i deweloperów, którzy chcą monitorować proces szkolenia modelu. Jeśli jesteś administratorem zainteresowanym monitorowaniem użycia zasobów i zdarzeń z usługi Azure Machine Learning, takich jak przydziały, ukończone przebiegi szkoleniowe lub ukończone wdrożenia modelu, zobacz [Monitorowanie usługi Azure Machine Learning.](monitor-azure-machine-learning.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Aby uruchomić TensorBoard i wyświetlić historię przebiegu eksperymentu, eksperymenty muszą mieć wcześniej włączone rejestrowanie, aby śledzić jego metryki i wydajność.  

* Kod w tym dokumencie można uruchomić w jednym z następujących środowisk: 

    * Wystąpienie obliczeń usługi Azure Machine Learning — nie jest wymagane pobieranie ani instalacja

        * Ukończ [samouczek: Środowisko instalacji i obszar roboczy,](tutorial-1st-experiment-sdk-setup.md) aby utworzyć serwer dedykowany notebooka wstępnie załadowany za pomocą zestawu SDK i przykładowego repozytorium.

        * W folderze przykłady na serwerze notesu znajdź dwa ukończone i rozszerzone notesy, przechodząc do następujących katalogów:
            * **how-to-use-azureml > szkolenia z głębokim uczeniem > export-run-history-to-tensorboard > export-run-history-to-tensorboard.ipynb**

            * **how-to-use-azureml > eksperymentów track-and-monitor > tensorboard.ipynb**

    * Twój własny serwer notebooków Juptyer
       * [Zainstaluj zestaw SDK usługi](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) `tensorboard` Azure Machine Learning z dodatkowymi
        * [Utwórz obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md).  
        * [Tworzenie pliku konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Opcja 1: Bezpośrednie wyświetlanie historii przebiegu w TensorBoard

Ta opcja działa w przypadku eksperymentów, które natywnie wyprowadza pliki dziennika materiałów eksploatacyjnych przez TensorBoard, takich jak PyTorch, Chainer i TensorFlow eksperymentów. Jeśli nie jest to przypadek eksperymentu, należy użyć [ `export_to_tensorboard()` metody](#export) zamiast tego.

Poniższy przykładowy kod używa [eksperymentu demo MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) z repozytorium TensorFlow w zdalnym miejscu docelowym obliczeń, Azure Machine Learning Compute. Następnie szkolimy nasz model za pomocą niestandardowego [estymatora TensorFlow,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)a następnie uruchamiamy TensorBoard przed tym eksperymentem TensorFlow, czyli eksperymentem, który natywnie wyprowadza pliki zdarzeń TensorBoard.

### <a name="set-experiment-name-and-create-project-folder"></a>Ustawianie nazwy eksperymentu i tworzenie folderu projektu

Tutaj nazywamy eksperyment i tworzymy jego folder. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Pobierz kod eksperymentu demo TensorFlow

Repozytorium TensorFlow ma demo MNIST z obszernym instrumentacją TensorBoard. Nie zmieniamy ani nie musimy zmieniać żadnego kodu tego demo, aby działał z usługą Azure Machine Learning. W poniższym kodzie pobieramy kod MNIST i zapisujemy go w naszym nowo utworzonym folderze eksperymentu.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
W całym pliku kodu MNIST, mnist_with_summaries.py, zwróć uwagę, `tf.summary.scalar()` `tf.summary.histogram()`że `tf.summary.FileWriter()` istnieją linie, które wywołują itp. Te metody grupują, logują i tagują kluczowe metryki eksperymentów w historii uruchamiania. `tf.summary.FileWriter()` Jest to szczególnie ważne, ponieważ serializuje dane z metryk zarejestrowanego eksperymentu, co pozwala tensorboard do generowania wizualizacji z nich.

 ### <a name="configure-experiment"></a>Konfigurowanie eksperymentu

Poniżej konfigurujemy nasz eksperyment i konfigurujemy katalogi dla dzienników i danych. Te dzienniki zostaną przekazane do usługi Artifact Service, do której TensorBoard uzyskuje dostęp później.

>[!Note]
> W tym przykładzie TensorFlow należy zainstalować TensorFlow na komputerze lokalnym. Ponadto moduł TensorBoard (czyli ten dołączony do TensorFlow) musi być dostępny dla jądra tego notebooka, ponieważ komputer lokalny jest tym, co uruchamia TensorBoard.

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

### <a name="create-a-cluster-for-your-experiment"></a>Tworzenie klastra dla eksperymentu
Tworzymy klaster AmlCompute dla tego eksperymentu, jednak eksperymenty mogą być tworzone w dowolnym środowisku i nadal można uruchomić TensorBoard względem historii uruchamiania eksperymentu. 

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

### <a name="submit-run-with-tensorflow-estimator"></a>Prześlij przebieg z estymatorem TensorFlow

Estymator TensorFlow zapewnia prosty sposób uruchamiania zadania szkoleniowego TensorFlow na celu obliczeniowym. Jest implementowany za pośrednictwem [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klasy ogólnej, która może służyć do obsługi dowolnej struktury. Aby uzyskać więcej informacji na temat modeli szkoleniowych przy użyciu ogólnego estymatora, zobacz [modele pociągów z usługą Azure Machine Learning przy użyciu estymatora](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Uruchom TensorBoard

Możesz uruchomić TensorBoard podczas biegu lub po jego zakończeniu. Poniżej utworzymy tensorBoard wystąpienie `tb`obiektu, który pobiera historię `run`uruchamiania eksperymentu załadowany `start()` w , a następnie uruchamia TensorBoard z metodą. 
  
[Konstruktor TensorBoard](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) przyjmuje tablicę przebiegów, więc upewnij się i przekaż go jako tablicę jednoelementową.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 W tym przykładzie użyto tensorflow, TensorBoard może być równie łatwo używany w modelach PyTorch lub Chainer. TensorFlow musi być dostępny na komputerze z systemem TensorBoard, ale nie jest konieczne na komputerze wykonującym obliczenia PyTorch lub Chainer. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Opcja 2: Eksportowanie historii jako dziennika do wyświetlenia w TensorBoard

Poniższy kod konfiguruje przykładowy eksperyment, rozpoczyna proces rejestrowania przy użyciu interfejsów API historii uruchamiania usługi Azure Machine Learning i eksportuje historię uruchamiania eksperymentu do dzienników materiałów eksploatacyjnych przez TensorBoard do wizualizacji. 

### <a name="set-up-experiment"></a>Konfigurowanie eksperymentu

Poniższy kod konfiguruje nowy eksperyment i `root_run`nazywa katalog uruchamiania . 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Tutaj ładujemy zestaw danych cukrzycy - wbudowany mały zestaw danych, który jest dostarczany z scikit-learn, i dzielimy go na zestawy testowe i szkoleniowe.

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

### <a name="run-experiment-and-log-metrics"></a>Uruchamianie danych eksperymentu i dziennika

Dla tego kodu możemy szkolić model regresji liniowej i dziennika kluczowych `alpha`metryki, współczynnik `mse`alfa i średni błąd kwadratu, w historii uruchamiania.

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

### <a name="export-runs-to-tensorboard"></a>Eksport działa do TensorBoard

Dzięki metodzie [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) narzędzia SDK możemy wyeksportować historię uruchamiania naszego eksperymentu uczenia maszynowego platformy Azure do dzienników TensorBoard, dzięki czemu możemy je wyświetlać za pośrednictwem tensorboardu.  

W poniższym kodzie tworzymy folder `logdir` w naszym bieżącym katalogu roboczym. Ten folder jest, gdzie będziemy eksportować naszą `root_run` historię przebiegu eksperymentu i dzienniki z a następnie oznaczyć, że uruchomić jako zakończone. 

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
 Można również wyeksportować określone uruchomienie do TensorBoard, określając nazwę przebiegu`export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Uruchamianie i zatrzymywać tensorboard
Po wyeksportowanie naszej historii uruchamiania dla tego eksperymentu, możemy uruchomić TensorBoard z metodą [start().](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Po zakończeniu, upewnij się, aby [wywołać stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) metoda TensorBoard obiektu. W przeciwnym razie TensorBoard będzie nadal działać, dopóki nie zamkniesz jądra notesu. 

```python
tb.stop()
```

## <a name="next-steps"></a>Następne kroki

W tym how-to you, stworzył dwa eksperymenty i dowiedział się, jak uruchomić TensorBoard w ich historii uruchamiania w celu zidentyfikowania obszarów potencjalnego dostrajania i przekwalifikowania. 

* Jeśli jesteś zadowolony z modelu, przejdź do naszego [jak wdrożyć](how-to-deploy-and-where.md) artykuł modelu. 
* Dowiedz się więcej o [dostrajaniu hiperparametryczne.](how-to-tune-hyperparameters.md) 
