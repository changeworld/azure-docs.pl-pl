---
title: Metryki dzienników podczas przebiegów szkoleniowych
titleSuffix: Azure Machine Learning service
description: Możesz śledzić eksperymenty i monitorować metryki, aby usprawnić proces tworzenia modelu. Dowiedz się, jak dodać rejestrowanie do skryptu szkoleniowego, jak przesłać eksperyment, jak sprawdzić postęp uruchomionego zadania i jak wyświetlić zarejestrowane wyniki przebiegu.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/11/2019
ms.custom: seodec18
ms.openlocfilehash: 7b14ed2c18c1106477e21062afaa4cc8f672c203
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946385"
---
# <a name="log-metrics-during-training-runs"></a>Metryki dzienników podczas przebiegów szkoleniowych

Usprawnij proces tworzenia modelu, śledząc eksperymenty i metryki monitorowania. W tym artykule dowiesz się, jak dodać rejestrowanie do skryptu szkoleniowego, przesłać przebieg eksperymentu, monitorować przebieg i przeglądać wyniki przebiegu Azure Machine Learning usługi.

> [!NOTE]
> Usługa Azure Machine Learning może również rejestrować informacje z innych źródeł podczas szkolenia, takie jak AutoML lub kontener Docker, który uruchamia zadanie szkoleniowe. Te dzienniki nie są udokumentowane. Jeśli wystąpią problemy i skontaktuje się z działem pomocy technicznej firmy Microsoft, mogą oni korzystać z tych dzienników podczas rozwiązywania problemów.

## <a name="list-of-training-metrics"></a>Listy metryk szkolenia 

Następujące metryki można dodać do uruchomienia podczas szkolenia eksperymentu. Aby wyświetlić bardziej szczegółowe listy mogą być śledzone przy uruchomieniu, zobacz [Uruchom dokumentację referencyjną klasę](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Typ| Funkcja języka Python | Uwagi|
|----|:----|:----|
|Wartości skalarne |Funkcja:<br>`run.log(name, value, description='')`<br><br>Przykład:<br>Run.log ("dokładności" 0,95) |Dziennik w liczbowymi lub wartość ciągu do uruchomienia o podanej nazwie. Rejestrowanie metryki Uruchom powoduje, że w przypadku tej metryki, które mają być przechowywane w rekordu uruchomienia w eksperymencie.  Można rejestrować metryki tej samej kilka razy w ramach przebiegu, wynik jest traktowane jako wektor tej metryki.|
|Listy|Funkcja:<br>`run.log_list(name, value, description='')`<br><br>Przykład:<br>Run.log_list ("dokładności" [Update 0.6, 0,7, 0.87]) | Zaloguj się listę wartości przebiegu o podanej nazwie.|
|Wiersz|Funkcja:<br>`run.log_row(name, description=None, **kwargs)`<br>Przykład:<br>Run.log_row ("Y za pośrednictwem X", x = 1, y = 0,4) | Za pomocą *log_row* tworzy metryki z wielu kolumn, zgodnie z opisem w kwargs. Każdy parametr o nazwie generuje kolumna z wartością określoną.  *log_row* można lze volat pouze jednou logowanie dowolnego spójnej kolekcji lub wiele razy w pętli, aby wygenerować pełny spis.|
|Tabela|Funkcja:<br>`run.log_table(name, value, description='')`<br><br>Przykład:<br>Run.log_table ("Y za pośrednictwem X", {"x": [1, 2, 3], "y": [Update 0.6, 0,7 0.89]}) | Zaloguj się do uruchomienia o podanej nazwie obiekt słownika. |
|Obrazy|Funkcja:<br>`run.log_image(name, path=None, plot=None)`<br><br>Przykład:<br>`run.log_image("ROC", plt)` | Zaloguj się obraz rekordu uruchomienia. Użyj log_image do logowania się plik obrazu lub matplotlib wykresu do uruchomienia.  Te obrazy będą widoczne i porównywalny rekordu uruchomienia.|
|Tag przebiegu|Funkcja:<br>`run.tag(key, value=None)`<br><br>Przykład:<br>Run.tag ("wybrane", "yes") | Tag wykonywania za pomocą klucza typu ciąg i wartości opcjonalny ciąg.|
|Przekazywanie pliku lub katalogu|Funkcja:<br>`run.upload_file(name, path_or_stream)`<br> <br> Przykład:<br>Run.upload_file ("best_model.pkl", ". / model.pkl") | Przekazywanie pliku do rekordu uruchomienia. Uruchamia automatyczne Przechwytywanie plików w katalogu określonym produktem wyjściowym wartością domyślną jest ". / wyjścia" dla większości uruchomienia typów.  Użyj upload_file tylko wtedy, gdy konieczne jest przekazanie dodatkowych plików lub nie określono katalogu wyjściowego. Zalecamy dodanie `outputs` nazwę, tak że pobiera przekazany do katalogu danych wyjściowych. Możesz wyświetlić listę wszystkich plików, które są skojarzone z tym uruchomienia rekord przy użyciu o nazwie `run.get_file_names()`|

> [!NOTE]
> Metryki dla wartości skalarnych, wierszy i listy tabel może mieć typ: float, liczba całkowita lub ciąg.

## <a name="start-logging-metrics"></a>Rozpocznij rejestrowanie metryki

Jeśli chcesz śledzić lub monitorowania eksperymentu, należy dodać kod, aby rozpocząć rejestrowanie, gdy prześlesz przebiegu. Poniżej przedstawiono sposoby przesyłania wykonywania wyzwalacza:
* __Run.start_logging__ — Dodawanie funkcji rejestrowania skrypt szkolenia i uruchomić sesję logowania interakcyjnego w określonym eksperymentu. **start_logging** tworzy interakcyjnego wykonywania do użytku w scenariuszach takich jak notesy. Wszystkie metryki, które są rejestrowane w trakcie sesji są dodawane do rekordu uruchomienia w eksperymencie.
* __ScriptRunConfig__ — Dodawanie funkcji rejestrowania skrypt szkolenia i załadować folderu cały skrypt z przebiegiem.  **ScriptRunConfig** jest klasą konfigurowania konfiguracje skrypt jest uruchamiany. Po wybraniu tej opcji można dodać kod monitorowania, aby otrzymywać powiadomienia o zakończeniu lub można pobrać visual widżetu do monitorowania.

## <a name="set-up-the-workspace"></a>Konfigurowanie obszaru roboczego
Przed dodaniem rejestrowania i przesyłanie eksperymentu, należy skonfigurować obszar roboczy.

1. Załaduj obszar roboczy. Aby dowiedzieć się więcej na temat ustawiania konfiguracji obszaru roboczego, zobacz [plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Option 1: Użyj start_logging

**start_logging** tworzy interakcyjnego wykonywania do użytku w scenariuszach takich jak notesy. Wszystkie metryki, które są rejestrowane w trakcie sesji są dodawane do rekordu uruchomienia w eksperymencie.

Poniższy przykład przygotowuje prosty model Ridge skryptu sklearn lokalnie w przypadku lokalnego notesu programu Jupyter. Aby dowiedzieć się więcej na temat przesyłanie eksperymentów w różnych środowiskach, zobacz [Konfigurowanie obliczeniowych elementów docelowych do trenowania modelu za pomocą usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Utwórz skrypt szkoleniowy w lokalnego notesu programu Jupyter. 

   ```python
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
    # Get an experiment object from Azure Machine Learning
    experiment = Experiment(workspace=ws, name="train-within-notebook")
    
    # Create a run object in the experiment
    run =  experiment.start_logging()
    # Log the algorithm parameter alpha to the run
    run.log('alpha', 0.03)
    
    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])
    
    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    run.log('mse', mean_squared_error(data['test']['y'], preds))
    
    # Save the model to the outputs directory for capture
    model_file_name = 'outputs/model.pkl'
    
    joblib.dump(value = regression_model, filename = model_file_name)
    
    # upload the model file explicitly into artifacts 
    run.upload_file(name = model_file_name, path_or_stream = model_file_name)
    
    # Complete the run
    run.complete()
   ```

    Skrypt kończy się ```run.complete()```, która oznacza Uruchom jako ukończone.  Ta funkcja jest zwykle używana w scenariuszach notesu interakcyjnego.

## <a name="option-2-use-scriptrunconfig"></a>Opcja 2: Użyj ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) jest klasą do konfigurowania konfiguracji do uruchamiania skryptów. Po wybraniu tej opcji można dodać kod monitorowania, aby otrzymywać powiadomienia o zakończeniu lub można pobrać visual widżetu do monitorowania.

W tym przykładzie stanowi rozszerzenie podstawowego modelu Ridge skryptu sklearn z góry. Proste parametru odchylenia do czyszczenia odbywa się za pośrednictwem wartości alfa modelu, aby przechwycić metryki i wytrenowane modele w działa w ramach eksperymentu. W przykładzie uruchamiane lokalnie przeciwko środowisku zarządzane przez użytkownika. 

1. Utwórz skrypt `train.py`szkoleniowy.

   ```python
   # train.py

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

2. `train.py` Odwołania`mylib.py` do skryptów, które umożliwiają uzyskanie listy wartości alfa do użycia w modelu pierścieniowym.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Konfigurowanie środowiska lokalnego z zarządzana przez użytkownika.

   ```python
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. Prześlij ```train.py``` skrypt do uruchomienia w środowisku zarządzane przez użytkownika. Ten folder cały skrypt jest przesyłany w celu szkolenia, w tym ```mylib.py``` pliku.

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>Zarządzanie przebiegiem

W artykule [Uruchamianie, monitorowanie i anulowanie szkoleń zostanie](how-to-manage-runs.md) wyróżnione określone Azure Machine Learning przepływy pracy służące do zarządzania eksperymentami.

## <a name="view-run-details"></a>Wyświetl szczegóły przebiegu

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Widżet uruchamiania z Jupyter notesu
Gdy używasz metody **ScriptRunConfig** do przesyłania przebiegów, możesz obserwować postęp przebiegu za pomocą [widżetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Podobnie jak przesyłanie przebiegu, widżet jest asynchroniczny i udostępnia aktualizacje na bieżąco co 10–15 sekund aż do zakończenia zadania.

1. Wyświetl element widget Jupyter podczas oczekiwania na przebieg zakończyć.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Zrzut ekranu aplikacji Jupyter notebook widżetu](./media/how-to-track-experiments/run-details-widget.png)

Możesz również uzyskać link do tego samego ekranu w obszarze roboczym.

```python
print(run.get_portal_url())
```

2. **[Automatyczne uczenia maszynowego przebiegi]**  Dostęp do wykresów z poprzedniego uruchomienia. Zamień `<<experiment_name>>` na odpowiednią nazwę eksperymentu:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widżet notesu programu Jupyter dla automatycznego Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Aby wyświetlić więcej szczegółów, kliknij potok w potoku chcesz eksplorować w tabeli i wykresów będą renderowane w podręcznym w witrynie Azure portal.

### <a name="get-log-results-upon-completion"></a>Pobieranie wyników dziennika po zakończeniu

Model uczenia i monitorowania wystąpić w tle, aby uruchomić inne zadania podczas oczekiwania. Można również poczekać, aż modelu została zakończona szkolenia przed uruchomieniem więcej kodu. Kiedy używasz **ScriptRunConfig**, możesz użyć ```run.wait_for_completion(show_output = True)``` do wyświetlenia po zakończeniu szkolenia modelu. ```show_output``` Flagi zapewnia pełne dane wyjściowe. 


### <a name="query-run-metrics"></a>Uruchomienie metryki zapytania

Możesz wyświetlić metryki uczonego modelu przy użyciu ```run.get_metrics()```. Teraz można uzyskać wszystkie metryki, które zostały zarejestrowane w przykładzie powyżej, aby określić najlepszy model.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal"></a>Wyświetl eksperymentu w witrynie Azure portal

Po zakończeniu eksperymentu, możesz przejść do zarejestrowanych uruchomienie rekordu eksperymentu. Dostęp do historii można uzyskać na dwa sposoby:

* Pobierz adres URL do uruchomienia bezpośrednio ```print(run.get_portal_url())```
* Wyświetl szczegóły przebiegu, przesyłając do nazwa przebiegu (w tym przypadku ```run```). W ten sposób wskazuje nazwę eksperymentu identyfikator, typ, stan, strona szczegółów, łącze do witryny Azure portal oraz link do dokumentacji.

Link do uruchomienia oferuje bezpośrednio do strony szczegółów przebiegu w witrynie Azure portal. Tutaj widać, właściwości, śledzonych metryki, obrazów i wykresów, które są rejestrowane w eksperymencie. W tym przypadku możemy rejestrowane MSE i wartości alfa.

  ![Szczegóły przebiegu w witrynie Azure portal](./media/how-to-track-experiments/run-details-page.png)

Można również wyświetlić wszystkie dane wyjściowe lub dzienniki dla uruchomienia lub Pobierz migawkę eksperymentu, przesłania więc folderu eksperymentu można udostępniać innym osobom.

### <a name="viewing-charts-in-run-details"></a>Wyświetlanie wykresów w szczegóły przebiegu

Istnieją różne sposoby rejestrowania interfejsów API dla różnych typów rekordów metryk podczas przebiegu i wyświetlać je jako wykresy w witrynie Azure portal. 

|Wartość zarejestrowane|Przykładowy kod| Wyświetl w portalu|
|----|----|----|
|Dziennik tablicę wartości liczbowych| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Wykres liniowy pojedynczej zmiennej|
|Logowania jest pojedyncza wartość liczbowa o takiej samej nazwie metryki wielokrotnie używane (jak z poziomu pętli for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Wykres liniowy pojedynczej zmiennej|
|Zaloguj się wielokrotnie wiersz z 2 kolumny liczbowe|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Wykres liniowy z dwoma zmiennymi|
|Tabela dziennika z 2 kolumny liczbowe|`run.log_table(name='Sine Wave', value=sines)`|Wykres liniowy z dwoma zmiennymi|


## <a name="example-notebooks"></a>Przykład notesów
Następujące notesów zademonstrowania koncepcji w tym artykule:
* [How-to-use-azureml/Training/Train-WITHIN-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [How-to-use-azureml/Training/Train-on-Local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [How-to-use-azureml/Training/Logging-API/Logging-API.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

Wypróbuj podjęcie następujących kroków, aby dowiedzieć się, jak używać zestawu SDK usługi Azure Machine Learning dla języka Python:

* Zobacz przykład sposobu rejestrowania najlepszy model i wdrożyć ją w tym samouczku [Wytrenuj model klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md).

* Dowiedz się, jak [uczyć modele PyTorch za pomocą usługi Azure Machine Learning](how-to-train-pytorch.md).
