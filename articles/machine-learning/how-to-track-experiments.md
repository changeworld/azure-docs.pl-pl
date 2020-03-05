---
title: Rejestruj eksperymenty w ML & metryki
titleSuffix: Azure Machine Learning
description: Monitoruj eksperymenty dotyczące platformy Azure ML i monitoruj metryki uruchamiania, aby usprawnić proces tworzenia modelu. Dodaj rejestrowanie do skryptu szkoleniowego i Wyświetl zarejestrowane wyniki przebiegu.  Użyj Run. log, Run. start_logging lub ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: e6b2f73540a0af7ed9c12469406a77d1bed8a2b4
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268459"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Monitoruj uruchomienia eksperymentów i metryki usługi Azure ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Usprawnij proces tworzenia modelu, śledząc eksperymenty i monitorowane metryki. W tym artykule dowiesz się, jak dodać kod rejestrowania do skryptu szkoleniowego, przesłać przebieg eksperymentu, monitorować ten przebieg i sprawdzić wyniki w Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning mogą także rejestrować informacje z innych źródeł podczas szkoleń, takich jak automatyczne uruchomienia uczenia maszynowego lub kontener platformy Docker, który uruchamia zadanie szkoleniowe. Te dzienniki nie są udokumentowane. Jeśli wystąpią problemy i skontaktuje się z działem pomocy technicznej firmy Microsoft, mogą oni korzystać z tych dzienników podczas rozwiązywania problemów.

> [!TIP]
> Informacje przedstawione w tym dokumencie są przeznaczone głównie dla analityków danych i deweloperów, którzy chcą monitorować proces szkolenia modelu. Jeśli jesteś administratorem zainteresowani monitorowaniem użycia zasobów i zdarzeń z usługi Azure Machine Learning, takich jak przydziały, ukończone przebiegi szkoleniowe lub wdrożone wdrożenia modelu, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Dostępne metryki do śledzenia

Następujące metryki można dodać do uruchomienia podczas szkolenia eksperymentu. Aby wyświetlić bardziej szczegółową listę elementów, które można śledzić w przebiegu, zobacz [dokumentację dotyczącą przebiegu klasy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Typ| Funkcja języka Python | Uwagi|
|----|:----|:----|
|Wartości skalarne |Funkcja:<br>`run.log(name, value, description='')`<br><br>Przykład:<br>Run.log ("dokładności" 0,95) |Dziennik w liczbowymi lub wartość ciągu do uruchomienia o podanej nazwie. Rejestrowanie metryki Uruchom powoduje, że w przypadku tej metryki, które mają być przechowywane w rekordu uruchomienia w eksperymencie.  Można rejestrować metryki tej samej kilka razy w ramach przebiegu, wynik jest traktowane jako wektor tej metryki.|
|Listy|Funkcja:<br>`run.log_list(name, value, description='')`<br><br>Przykład:<br>Run.log_list ("dokładności" [Update 0.6, 0,7, 0.87]) | Zaloguj się listę wartości przebiegu o podanej nazwie.|
|wiersz|Funkcja:<br>`run.log_row(name, description=None, **kwargs)`<br>Przykład:<br>Run.log_row ("Y za pośrednictwem X", x = 1, y = 0,4) | Za pomocą *log_row* tworzy metrykę z wieloma kolumnami, zgodnie z opisem w kwargs. Każdy parametr o nazwie generuje kolumna z wartością określoną.  *log_row* można wywołać raz, aby zarejestrować dowolną krotkę lub wiele razy w pętli, aby wygenerować kompletną tabelę.|
|Tabela|Funkcja:<br>`run.log_table(name, value, description='')`<br><br>Przykład:<br>Run.log_table ("Y za pośrednictwem X", {"x": [1, 2, 3], "y": [Update 0.6, 0,7 0.89]}) | Zaloguj się do uruchomienia o podanej nazwie obiekt słownika. |
|Obrazy|Funkcja:<br>`run.log_image(name, path=None, plot=None)`<br><br>Przykład:<br>`run.log_image("ROC", plot=plt)` | Zaloguj się obraz rekordu uruchomienia. Użyj log_image do logowania się plik obrazu lub matplotlib wykresu do uruchomienia.  Te obrazy będą widoczne i porównywalny rekordu uruchomienia.|
|Tag przebiegu|Funkcja:<br>`run.tag(key, value=None)`<br><br>Przykład:<br>Run.tag ("wybrane", "yes") | Tag wykonywania za pomocą klucza typu ciąg i wartości opcjonalny ciąg.|
|Przekazywanie pliku lub katalogu|Funkcja:<br>`run.upload_file(name, path_or_stream)`<br> <br> Przykład:<br>Run.upload_file ("best_model.pkl", ". / model.pkl") | Przekazywanie pliku do rekordu uruchomienia. Uruchamia automatyczne Przechwytywanie plików w katalogu określonym produktem wyjściowym wartością domyślną jest ". / wyjścia" dla większości uruchomienia typów.  Użyj upload_file tylko wtedy, gdy konieczne jest przekazanie dodatkowych plików lub nie określono katalogu wyjściowego. Zalecamy dodanie `outputs` do nazwy, aby można było przekazać ją do katalogu danych wyjściowych. Wszystkie pliki, które są skojarzone z tym rekordem uruchamiania, można wyświetlić według nazwy `run.get_file_names()`|

> [!NOTE]
> Metryki dla wartości skalarnych, wierszy i listy tabel może mieć typ: float, liczba całkowita lub ciąg.

## <a name="choose-a-logging-option"></a>Wybierz opcję rejestrowania

Jeśli chcesz śledzić lub monitorowania eksperymentu, należy dodać kod, aby rozpocząć rejestrowanie, gdy prześlesz przebiegu. Poniżej przedstawiono sposoby przesyłania wykonywania wyzwalacza:
* __Uruchom. start_logging__ — Dodaj funkcje rejestrowania do skryptu szkoleniowego i Rozpocznij interaktywną sesję rejestrowania w określonym doświadczeniu. **start_logging** tworzy interaktywny przebieg do użycia w scenariuszach takich jak notesy. Wszystkie metryki, które są rejestrowane w trakcie sesji są dodawane do rekordu uruchomienia w eksperymencie.
* __ScriptRunConfig__ — Dodaj funkcje rejestrowania do skryptu szkoleniowego i Załaduj cały folder skryptu z przebiegiem.  **ScriptRunConfig** jest klasą do konfigurowania konfiguracji do uruchamiania skryptów. Po wybraniu tej opcji można dodać kod monitorowania, aby otrzymywać powiadomienia o zakończeniu lub można pobrać visual widżetu do monitorowania.

## <a name="set-up-the-workspace"></a>Konfigurowanie obszaru roboczego
Przed dodaniem rejestrowania i przesyłanie eksperymentu, należy skonfigurować obszar roboczy.

1. Załaduj obszar roboczy. Aby dowiedzieć się więcej na temat ustawiania konfiguracji obszaru roboczego, zobacz [plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Opcja 1: Użycie start_logging

**start_logging** tworzy interaktywny przebieg do użycia w scenariuszach takich jak notesy. Wszystkie metryki, które są rejestrowane w trakcie sesji są dodawane do rekordu uruchomienia w eksperymencie.

Poniższy przykład przygotowuje prosty model Ridge skryptu sklearn lokalnie w przypadku lokalnego notesu programu Jupyter. Aby dowiedzieć się więcej o przesyłaniu eksperymentów do różnych środowisk, zobacz [Konfigurowanie celów obliczeniowych na potrzeby szkolenia modeli przy użyciu Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets).

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

2. Dodaj śledzenie eksperymentu przy użyciu zestawu SDK Azure Machine Learning i przekaż utrwalony model do rekordu przebiegu eksperymentu. Poniższy kod dodaje znaczniki, dzienników i przekazuje plik modelu na uruchomienie eksperymentu.

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

    Skrypt kończy się ```run.complete()```, co oznacza, że przebieg jest zakończony.  Ta funkcja jest zwykle używana w scenariuszach notesu interakcyjnego.

## <a name="option-2-use-scriptrunconfig"></a>Opcja 2: Użycie ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) jest klasą do konfigurowania konfiguracji do uruchamiania skryptów. Po wybraniu tej opcji można dodać kod monitorowania, aby otrzymywać powiadomienia o zakończeniu lub można pobrać visual widżetu do monitorowania.

W tym przykładzie stanowi rozszerzenie podstawowego modelu Ridge skryptu sklearn z góry. Proste parametru odchylenia do czyszczenia odbywa się za pośrednictwem wartości alfa modelu, aby przechwycić metryki i wytrenowane modele w działa w ramach eksperymentu. W przykładzie uruchamiane lokalnie przeciwko środowisku zarządzane przez użytkownika. 

1. Utwórz skrypt szkoleniowy `train.py`.

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

2. `train.py` skrypt odwołuje się `mylib.py`, co umożliwia uzyskanie listy wartości alfa do użycia w modelu pierścienia.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Konfigurowanie środowiska lokalnego z zarządzana przez użytkownika.

   ```python
   from azureml.core.environment import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. Prześlij skrypt ```train.py``` do uruchomienia w środowisku zarządzanym przez użytkownika. Ten cały folder skryptu jest przesyłany do szkoleń, w tym pliku ```mylib.py```.

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

### <a name="view-activequeued-runs-from-the-browser"></a>Wyświetl aktywne/kolejkowane uruchomienia z przeglądarki

Cele obliczeniowe używane do uczenia modeli są zasobem udostępnionym. W związku z tym mogą mieć wiele uruchomionych w kolejce lub aktywnych w danym momencie. Aby zobaczyć przebiegi dla określonego elementu docelowego obliczeń z przeglądarki, wykonaj następujące czynności:

1. W [Azure Machine Learning Studio](https://ml.azure.com/)wybierz swój obszar roboczy, a następnie wybierz pozycję __obliczenia__ z lewej strony.

1. Wybierz pozycję __klastry szkoleniowe__ , aby wyświetlić listę celów obliczeniowych używanych na potrzeby szkolenia. Następnie wybierz klaster.

    ![Wybierz klaster szkoleniowy](./media/how-to-track-experiments/select-training-compute.png)

1. Wybierz pozycję __uruchomienia__. Zostanie wyświetlona lista przebiegów korzystających z tego klastra. Aby wyświetlić szczegóły określonego uruchomienia, Użyj linku w kolumnie __Run__ . Aby wyświetlić szczegóły eksperymentu, Użyj linku w kolumnie __eksperymenty__ .

    ![Wybierz przebiegi dla klastra szkoleniowego](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Przebieg może zawierać przebiegi podrzędne, więc jedno zadanie szkoleniowe może spowodować powstanie wielu wpisów.

Po zakończeniu przebiegu nie jest on już wyświetlany na tej stronie. Aby wyświetlić informacje o zakończonych przebiegach, odwiedź sekcję __eksperymenty__ w programie Studio i wybierz eksperyment i Uruchom. Aby uzyskać więcej informacji, zobacz sekcję [kwerendy przebiegu zapytania](#queryrunmetrics) .

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

2. **[Dla zautomatyzowanych przebiegów uczenia maszynowego]** Aby uzyskać dostęp do wykresów z poprzedniego przebiegu. Zastąp `<<experiment_name>>` odpowiednimi nazwami eksperymentu:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widżet notesu programu Jupyter dla automatycznego Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Aby wyświetlić dalsze szczegóły potoku, kliknij potok, który chcesz zbadać w tabeli, a wykresy będą renderowane w wyskakującym okienku z Azure Machine Learning Studio.

### <a name="get-log-results-upon-completion"></a>Pobieranie wyników dziennika po zakończeniu

Model uczenia i monitorowania wystąpić w tle, aby uruchomić inne zadania podczas oczekiwania. Można również poczekać, aż modelu została zakończona szkolenia przed uruchomieniem więcej kodu. Gdy korzystasz z **ScriptRunConfig**, możesz użyć ```run.wait_for_completion(show_output = True)```, aby pokazać, kiedy szkolenie modelu zostało zakończone. Flaga ```show_output``` zapewnia pełne dane wyjściowe. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Uruchomienie metryki zapytania

Możesz wyświetlić metryki modelu przeszkolonego za pomocą ```run.get_metrics()```. Teraz można uzyskać wszystkie metryki, które zostały zarejestrowane w przykładzie powyżej, aby określić najlepszy model.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Wyświetlanie eksperymentu w obszarze roboczym w programie [Azure Machine Learning Studio](https://ml.azure.com)

Po zakończeniu eksperymentu, możesz przejść do zarejestrowanych uruchomienie rekordu eksperymentu. Możesz uzyskać dostęp do historii z programu [Azure Machine Learning Studio](https://ml.azure.com).

Przejdź do karty eksperymenty i wybierz swój eksperyment. Nastąpi przełączenie do pulpitu nawigacyjnego uruchamiania eksperymentu, w którym można zobaczyć śledzone metryki i wykresy, które są rejestrowane dla każdego przebiegu. W tym przypadku możemy rejestrowane MSE i wartości alfa.

  ![Szczegóły uruchamiania w programie Azure Machine Learning Studio](./media/how-to-track-experiments/experiment-dashboard.png)

Możesz przejść do określonego przebiegu w celu wyświetlenia jego danych wyjściowych lub dzienników lub pobrać migawkę podanego eksperymentu, aby umożliwić udostępnienie folderu eksperymentu innym osobom.

### <a name="viewing-charts-in-run-details"></a>Wyświetlanie wykresów w szczegóły przebiegu

Istnieją różne sposoby używania interfejsów API rejestrowania do rejestrowania różnych typów metryk podczas przebiegu i wyświetlania ich jako wykresów w programie Azure Machine Learning Studio.

|Wartość zarejestrowane|Przykładowy kod| Wyświetl w portalu|
|----|----|----|
|Dziennik tablicę wartości liczbowych| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Wykres liniowy pojedynczej zmiennej|
|Logowania jest pojedyncza wartość liczbowa o takiej samej nazwie metryki wielokrotnie używane (jak z poziomu pętli for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Wykres liniowy pojedynczej zmiennej|
|Zaloguj się wielokrotnie wiersz z 2 kolumny liczbowe|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Wykres liniowy z dwoma zmiennymi|
|Tabela dziennika z 2 kolumny liczbowe|`run.log_table(name='Sine Wave', value=sines)`|Wykres liniowy z dwoma zmiennymi|


## <a name="example-notebooks"></a>Przykład notesów
Następujące notesów zademonstrowania koncepcji w tym artykule:
* [jak to zrobić — Azure/szkolenia/uczenie w obrębie notesu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [Jak używać — Azure/szkolenia/uczenie lokalne](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [Jak korzystać z programu — usługi Azure/śledzenia i monitorowania — eksperymenty/rejestrowanie — interfejs API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

Skorzystaj z poniższych następnych kroków, aby dowiedzieć się, jak używać zestawu SDK usługi Azure Machine Learning dla języka Python:

* Zapoznaj się z przykładem sposobu rejestracji najlepszego modelu i wdrożenia go w samouczku, [uczenie modelu klasyfikacji obrazów przy użyciu Azure Machine Learning](tutorial-train-models-with-aml.md).

* Dowiedz się, jak [uczenie modeli PyTorch za pomocą Azure Machine Learning](how-to-train-pytorch.md).
