---
title: Śledzenie eksperymentów i metryk szkolenia — usługa Azure Machine Learning | Dokumentacja firmy Microsoft
description: Za pomocą usługi Azure Machine Learning można śledzić eksperymenty i monitorować metryki, aby ulepszyć proces tworzenia modelu. Dowiedz się, jak dodać rejestrowania skrypt szkolenia, jak przesyłanie eksperymentu, sprawdzanie postępu zadania uruchomione i jak wyświetlać wyniki przebiegu.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: b3e1fd5331b97fc2120819b17f7fbba57dadf7b1
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345054"
---
# <a name="track-experiments-and-training-metrics-in-azure-machine-learning"></a>Śledzenie eksperymentów i szkolenia metryk w usłudze Azure Machine Learning

W usłudze Azure Machine Learning można śledzić eksperymenty i monitorować metryki, aby ulepszyć proces tworzenia modelu. W tym artykule poznasz różne sposoby dodawania rejestrowania skrypt szkolenia, porady przesyłanie eksperymentu za pomocą **start_logging** i **ScriptRunConfig**, jak sprawdzić postęp uruchomione zadania oraz jak wyświetlać wyniki przebiegu. 

>[!NOTE]
> Kod w tym artykule został przetestowany przy użyciu zestawu SDK usługi Azure Machine Learning wersji 0.168 

## <a name="list-of-training-metrics"></a>Listy metryk szkolenia 

Następujące metryki można dodać do uruchomienia podczas szkolenia eksperymentu. Aby wyświetlić bardziej szczegółowe listy mogą być śledzone przy uruchomieniu, zobacz [Dokumentacja referencyjna zestawu SDK](https://docs.microsoft.com/python/api/overview/azure/azure-ml-sdk-overview?view=azure-ml-py).

|Typ| Funkcja języka Python | Przykład | Uwagi|
|----|:----|:----|:----|
|Wartości skalarne | `run.log(name, value, description='')`| `run.log("accuracy", 0.95) ` |Dziennik w liczbowymi lub wartość ciągu do uruchomienia o podanej nazwie. Rejestrowanie metryki Uruchom powoduje, że w przypadku tej metryki, które mają być przechowywane w rekordu uruchomienia w eksperymencie.  Można rejestrować metryki tej samej kilka razy w ramach przebiegu, wynik jest traktowane jako wektor tej metryki.|
|Listy| `run.log_list(name, value, description='')`| `run.log_list("accuracies", [0.6, 0.7, 0.87])` | Zaloguj się listę wartości przebiegu o podanej nazwie.|
|Wiersz| `run.log_row(name, description=None, **kwargs)`| `run.log_row("Y over X", x=1, y=0.4)` | Za pomocą *log_row* tworzy metryki z wielu kolumn, zgodnie z opisem w kwargs. Każdy parametr o nazwie generuje kolumna z wartością określoną.  *log_row* można lze volat pouze jednou logowanie dowolnego spójnej kolekcji lub wiele razy w pętli, aby wygenerować pełny spis.|
|Tabela| `run.log_table(name, value, description='')`| `run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]})` | Zaloguj się do uruchomienia o podanej nazwie obiekt słownika. |
|Obrazy| `run.log_image(name, path=None, plot=None)`| `run.log_image("ROC", plt)` | Zaloguj się obraz rekordu uruchomienia. Użyj log_image do logowania się plik obrazu lub matplotlib wykresu do uruchomienia.  Te obrazy będą widoczne i porównywalny rekordu uruchomienia.|
|Tag przebiegu| `run.tag(key, value=None)`| `run.tag("selected", "yes")` | Tag wykonywania za pomocą klucza typu ciąg i wartości opcjonalny ciąg.|
|Przekazywanie pliku lub katalogu|`run.upload_file(name, path_or_stream)`| Run.upload_file ("best_model.pkl", ". / model.pkl") | Przekazywanie pliku do rekordu uruchomienia. Uruchamia automatyczne Przechwytywanie plików w katalogu określonym produktem wyjściowym wartością domyślną jest ". / wyjścia" dla większości uruchomienia typów.  Użyj upload_file tylko wtedy, gdy konieczne jest przekazanie dodatkowych plików lub nie określono katalogu wyjściowego. Zalecamy dodanie `outputs` nazwę, tak że pobiera przekazany do katalogu danych wyjściowych. Możesz wyświetlić listę wszystkich plików, które są skojarzone z tym uruchomienia rekord przy użyciu o nazwie `run.get_file_names()`|

> [!NOTE]
> Metryki dla wartości skalarnych, wierszy i listy tabel może mieć typ: float, liczba całkowita lub ciąg.

## <a name="log-metrics-for-experiments"></a>Rejestruj metryki dla eksperymentów

Jeśli chcesz śledzić lub monitorowania eksperymentu, należy dodać kod, aby rozpocząć rejestrowanie, gdy prześlesz przebiegu. Poniżej przedstawiono sposoby przesyłania wykonywania wyzwalacza:
* __Run.start_logging__ — Dodawanie funkcji rejestrowania skrypt szkolenia i uruchomić sesję logowania interakcyjnego w określonym eksperymentu. **start_logging** tworzy interakcyjnego wykonywania do użytku w scenariuszach takich jak notesy. Wszystkie metryki, które są rejestrowane w trakcie sesji są dodawane do rekordu uruchomienia w eksperymencie.
* __ScriptRunConfig__ — Dodawanie funkcji rejestrowania skrypt szkolenia i załadować folderu cały skrypt z przebiegiem.  **ScriptRunConfig** jest klasą konfigurowania konfiguracje skrypt jest uruchamiany. Po wybraniu tej opcji można dodać kod monitorowania, aby otrzymywać powiadomienia o zakończeniu lub można pobrać visual widżetu do monitorowania.

## <a name="set-up-the-workspace-and-experiment"></a>Konfigurowanie obszaru roboczego i eksperymentu
Przed dodaniem rejestrowania i przesyłanie eksperymentu, należy skonfigurować obszar roboczy i doświadczenia.

1. Załaduj obszar roboczy. Aby dowiedzieć się więcej na temat ustawiania konfiguracji obszaru roboczego, postępuj zgodnie z [Szybki Start](https://docs.microsoft.com/azure/machine-learning/service/quickstart-get-started).

  ```python
  from azureml.core import Workspace, Run
  import azureml.core
  
  ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```

2. Tworzenie eksperymentu.

  ```python
  from azureml.core import Experiment

  # make up an arbitrary name
  experiment_name = 'train-in-notebook'
  exp = Experiment(workspace_object = ws, name = experiment_name)
  ```
  
## <a name="option-1-use-startlogging"></a>Opcja 1: Użycie start_logging

**start_logging** tworzy interakcyjnego wykonywania do użytku w scenariuszach takich jak notesy. Wszystkie metryki, które są rejestrowane w trakcie sesji są dodawane do rekordu uruchomienia w eksperymencie.

Poniższy przykład przygotowuje prosty model Ridge skryptu sklearn lokalnie w przypadku lokalnego notesu programu Jupyter. Aby dowiedzieć się więcej na temat przesyłanie eksperymentów w różnych środowiskach, zobacz [Konfigurowanie obliczeniowych elementów docelowych do trenowania modelu za pomocą usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Utwórz skrypt szkoleniowy w lokalnego notesu programu Jupyter. 

  ``` python
  # load diabetes dataset, a well-known small dataset that comes with scikit-learn
  from sklearn.datasets import load_diabetes
  from sklearn.linear_model import Ridge
  from sklearn.metrics import mean_squared_error
  from sklearn.model_selection import train_test_split
  from sklearn.externals import joblib

  X, y = load_diabetes(return_X_y = True)
  columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
  X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
  data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
  }
  reg = Ridge(alpha = 0.03)
  reg.fit(data['train']['X'], data['train']['y'])
  preds = reg.predict(data['test']['X'])
  print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
  joblib.dump(value = reg, filename = 'model.pkl');
  ```

2. Dodaj śledzenia eksperymentu przy użyciu zestawu SDK usługi Azure Machine Learning i przekazywanie utrwalonych modelu na uruchomienie rekordu eksperymentu. Poniższy kod dodaje znaczniki, dzienników i przekazuje plik modelu na uruchomienie eksperymentu.

  ```python
  run = Run.start_logging(experiment = exp)
  run.tag("Description","My first run!")
  run.log('alpha', 0.03)
  reg = Ridge(alpha = 0.03)
  reg.fit(data['train']['X'], data['train']['y'])
  preds = reg.predict(data['test']['X'])
  run.log('mse', mean_squared_error(preds, data['test']['y']))
  joblib.dump(value = reg, filename = 'model.pkl')
  # Upload file directly to the outputs folder
  run.upload_file(name = 'outputs/model.pkl', path_or_stream = './model.pkl')

  run.complete()
  ```

Skrypt kończy się ```run.complete()```, która oznacza Uruchom jako ukończone.  Zazwyczaj jest on używany w scenariuszach notesu interakcyjnego.

## <a name="option-2-use-scriptrunconfig"></a>Opcja 2: Użycie ScriptRunConfig

**ScriptRunConfig** jest klasą konfigurowania konfiguracje skrypt jest uruchamiany. Po wybraniu tej opcji można dodać kod monitorowania, aby otrzymywać powiadomienia o zakończeniu lub można pobrać visual widżetu do monitorowania.

W tym przykładzie stanowi rozszerzenie podstawowego modelu Ridge skryptu sklearn z góry. Proste parametru odchylenia do czyszczenia odbywa się za pośrednictwem wartości alfa modelu, aby przechwycić metryki i wytrenowane modele w działa w ramach eksperymentu. W przykładzie uruchamiane lokalnie przeciwko środowisku zarządzane przez użytkownika. 

1. Utwórz skrypt szkolenia. Ta metoda korzysta z ```%%writefile%%``` do pisania kodu szkolenia w folderze skryptu, co ```train.py```.

  ```python
  %%writefile $project_folder/train.py

  import os
  from sklearn.datasets import load_diabetes
  from sklearn.linear_model import Ridge
  from sklearn.metrics import mean_squared_error
  from sklearn.model_selection import train_test_split
  from azureml.core.run import Run
  from sklearn.externals import joblib

  import numpy as np

  #os.makedirs('./outputs', exist_ok = True)

  X, y = load_diabetes(return_X_y = True)

  run = Run.get_context()

  X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
  data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

  # list of numbers from 0.0 to 1.0 with a 0.05 interval
  alphas = mylib.get_alphas()

  for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
  ```

2. ```train.py``` Odwołania do skryptu ```mylib.py```. Ten plik umożliwia pobieranie listy wartości alfa do użycia w modelu ridge.

  ```python
  %%writefile $script_folder/mylib.py
  import numpy as np

  def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
  ```

3. Konfigurowanie środowiska lokalnego z zarządzana przez użytkownika.

  ```python
  from azureml.core.runconfig import RunConfiguration

  # Editing a run configuration property on-fly.
  run_config_user_managed = RunConfiguration()

  run_config_user_managed.environment.python.user_managed_dependencies = True

  # You can choose a specific Python environment by pointing to a Python path 
  #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
  ```

4. Prześlij ```train.py``` skrypt do uruchomienia w środowisku zarządzane przez użytkownika. Ten folder cały skrypt jest przesyłany w celu szkolenia, w tym ```mylib.py``` pliku.

  ```python
  from azureml.core import ScriptRunConfig

  src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_user_managed)
  run = exp.submit(src)
  ```
  
## <a name="view-run-details"></a>Wyświetl szczegóły przebiegu

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Monitorowanie uruchomienia widżetów notesu programu Jupyter
Kiedy używasz **ScriptRunConfig** metodę, aby przesłać działa, możesz obserwować postęp wykonywania za pomocą elementu widget notesu programu Jupyter. Podobnie jak przesyłanie przebiegu, widżet jest asynchroniczny i udostępnia aktualizacje na bieżąco co 10–15 sekund aż do zakończenia zadania.

1. Wyświetl element widget Jupyter podczas oczekiwania na przebieg zakończyć.

  ```python
  from azureml.train.widgets import RunDetails
  RunDetails(run).show()
  ```

  ![Zrzut ekranu aplikacji Jupyter notebook widżetu](./media/how-to-track-experiments/widgets.PNG)

### <a name="get-log-results-upon-completion"></a>Pobieranie wyników dziennika po zakończeniu

Model uczenia i monitorowania wystąpić w tle, aby uruchomić inne zadania podczas oczekiwania. Można również poczekać, aż modelu została zakończona szkolenia przed uruchomieniem więcej kodu. Kiedy używasz **ScriptRunConfig**, możesz użyć ```run.wait_for_completion(show_output = True)``` do wyświetlenia po zakończeniu szkolenia modelu. ```show_output``` Flagi zapewnia pełne dane wyjściowe. 
  
### <a name="query-run-metrics"></a>Uruchomienie metryki zapytania

Możesz wyświetlić metryki uczonego modelu przy użyciu ```run.get_metrics()```. Teraz można uzyskać wszystkie metryki, które zostały zarejestrowane w przykładzie powyżej, aby określić najlepszy model.

## <a name="view-the-experiment-in-the-azure-portal"></a>Wyświetl eksperymentu w witrynie Azure portal

Po zakończeniu eksperymentu, możesz przejść do zarejestrowanych uruchomienie rekordu eksperymentu. Można to zrobić na dwa sposoby:

* Pobierz adres URL do uruchomienia bezpośrednio ```print(run.get_portal_url())```
* Wyświetl szczegóły przebiegu, przesyłając do nazwa przebiegu (w tym przypadku ```run```). To wskazuje nazwę eksperymentu identyfikator, typ, stan, strona szczegółów, łącze do witryny Azure portal oraz link do dokumentacji.

Link do uruchomienia oferuje bezpośrednio do strony szczegółów przebiegu w witrynie Azure portal. Tutaj widać, właściwości, śledzonych metryki, obrazów i wykresów, które są rejestrowane w eksperymencie. W tym przypadku możemy rejestrowane MSE i wartości alfa.

  ![Zrzut ekranu przedstawiający szczegóły przebiegu w witrynie Azure portal](./media/how-to-track-experiments/run-details-page-web.PNG)

Można również wyświetlić wszystkie dane wyjściowe lub dzienniki dla uruchomienia lub Pobierz migawkę eksperymentu, przesłania więc folderu eksperymentu można udostępniać innym osobom.

## <a name="example-notebooks"></a>Przykład notesów
Następujące notesów zademonstrowania koncepcji w tym artykule:
* [01.Getting-Started/01.Train-WITHIN-notebook/01.Train-WITHIN-notebook.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/01.train-within-notebook)
* [01.Getting-Started/02.Train-on-Local/02.Train-on-Local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local)

Pobierz te notesy: [!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

Wypróbuj podjęcie następujących kroków, aby dowiedzieć się, jak używać zestawu SDK usługi Azure Machine Learning dla języka Python:

* Zobacz przykład sposobu rejestrowania najlepszy model i wdrożyć ją w tym samouczku [Wytrenuj model klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md).

* Dowiedz się, jak [uczyć modele PyTorch za pomocą usługi Azure Machine Learning](how-to-train-pytorch.md).
