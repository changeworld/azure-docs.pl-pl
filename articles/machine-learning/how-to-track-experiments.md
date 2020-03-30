---
title: Rejestrowanie eksperymentów ml & metryki
titleSuffix: Azure Machine Learning
description: Monitoruj eksperymenty usługi Azure ML i monitoruj metryki uruchamiania, aby usprawnić proces tworzenia modelu. Dodaj rejestrowanie do skryptu szkolenia i wyświetl zarejestrowane wyniki przebiegu.  Użyj pliku run.log, pliku Run.start_logging lub ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: seodec18
ms.openlocfilehash: 0c77e9d0aa4f44f33b1345a6021fc0378459ee85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79296969"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Monitorowanie przebiegów eksperymentu usługi Azure ML i metryk
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Usprawnij proces tworzenia modelu, śledząc eksperymenty i monitorując metryki przebiegu. W tym artykule dowiesz się, jak dodać kod rejestrowania do skryptu szkoleniowego, przesłać przebieg eksperymentu, monitorować uruchomione i sprawdzić wyniki w usłudze Azure Machine Learning.

> [!NOTE]
> Usługa Azure Machine Learning może również rejestrować informacje z innych źródeł podczas szkolenia, takich jak automatyczne przebiegi uczenia maszynowego lub kontener platformy Docker, który uruchamia zadanie szkoleniowe. Te dzienniki nie są udokumentowane. Jeśli wystąpią problemy i skontaktuj się z pomocą techniczną firmy Microsoft, mogą one być w stanie korzystać z tych dzienników podczas rozwiązywania problemów.

> [!TIP]
> Informacje zawarte w tym dokumencie są przeznaczone przede wszystkim dla analityków danych i deweloperów, którzy chcą monitorować proces szkolenia modelu. Jeśli jesteś administratorem zainteresowanym monitorowaniem użycia zasobów i zdarzeń z usługi Azure Machine Learning, takich jak przydziały, ukończone przebiegi szkoleniowe lub ukończone wdrożenia modelu, zobacz [Monitorowanie usługi Azure Machine Learning.](monitor-azure-machine-learning.md)

## <a name="available-metrics-to-track"></a>Dostępne metryki do śledzenia

Następujące metryki można dodać do uruchomienia podczas szkolenia eksperymentu. Aby wyświetlić bardziej szczegółową listę tego, co można śledzić w przebiegu, zobacz [dokumentację odwołania do klasy Uruchom](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Typ| Python, funkcja | Uwagi|
|----|:----|:----|
|Wartości skalarne |Funkcja:<br>`run.log(name, value, description='')`<br><br>Przykład:<br>run.log("dokładność", 0,95) |Zarejestruj wartość numeryczną lub ciąg do uruchomienia o podanej nazwie. Rejestrowanie metryki do uruchomienia powoduje, że metryka mają być przechowywane w rekordzie uruchomienia w eksperymencie.  Można rejestrować tę samą metrykę wiele razy w ramach uruchomienia, wynik jest uważany za wektor tej metryki.|
|Listy|Funkcja:<br>`run.log_list(name, value, description='')`<br><br>Przykład:<br>run.log_list("dokładność", [0.6, 0.7, 0.87]) | Zarejestruj listę wartości do uruchomienia pod o podanej nazwie.|
|Wiersz|Funkcja:<br>`run.log_row(name, description=None, **kwargs)`<br>Przykład:<br>run.log_row("Y ponad X", x=1, y=0,4) | Za pomocą *log_row* tworzy metrykę z wieloma kolumnami, jak opisano w kwargs. Każdy nazwany parametr generuje kolumnę o określonej wartości.  *log_row* można wywołać raz, aby zarejestrować dowolną krotę lub wiele razy w pętli, aby wygenerować pełną tabelę.|
|Tabela|Funkcja:<br>`run.log_table(name, value, description='')`<br><br>Przykład:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Zaloguj obiekt słownika do uruchomienia o podanej nazwie. |
|Obrazy|Funkcja:<br>`run.log_image(name, path=None, plot=None)`<br><br>Przykład:<br>`run.log_image("ROC", plot=plt)` | Rejestrowanie obrazu w rekordzie biegu. Użyj log_image, aby zarejestrować plik obrazu lub wykres matplotlib do uruchomienia.  Te obrazy będą widoczne i porównywalne w rekordzie biegu.|
|Oznaczanie biegu|Funkcja:<br>`run.tag(key, value=None)`<br><br>Przykład:<br>run.tag("zaznaczone", "tak") | Oznacz bieg za pomocą klucza ciągu i opcjonalnej wartości ciągu.|
|Przekazywanie pliku lub katalogu|Funkcja:<br>`run.upload_file(name, path_or_stream)`<br> <br> Przykład:<br>run.upload_file("best_model.pkl", "./model.pkl") | Przekaż plik do rekordu uruchomienia. Uruchamia automatycznie przechwytywać plik w określonym katalogu wyjściowym, który domyślnie "./outputs" dla większości typów uruchamiania.  Należy używać upload_file tylko wtedy, gdy konieczne jest przekazanie dodatkowych plików lub nie określono katalogu wyjściowego. Sugerujemy `outputs` dodanie do nazwy, tak aby został przekazany do katalogu outputs. Można wyświetlić listę wszystkich plików, które są skojarzone z tym rekordem run przez wywołane`run.get_file_names()`|

> [!NOTE]
> Metryki skalarne, listy, wiersze i tabele mogą mieć typ: float, integer lub string.

## <a name="choose-a-logging-option"></a>Wybieranie opcji rejestrowania

Jeśli chcesz śledzić lub monitorować eksperyment, musisz dodać kod, aby rozpocząć rejestrowanie podczas przesyłania przebiegu. Poniżej przedstawiono sposoby wyzwalania przesłania przebiegu:
* __Run.start_logging__ — dodaj funkcje rejestrowania do skryptu szkoleniowego i rozpocznij interaktywną sesję rejestrowania w określonym eksperymencie. **start_logging** tworzy interaktywne uruchomienie do użycia w scenariuszach, takich jak notesy. Wszystkie metryki, które są rejestrowane podczas sesji są dodawane do rekordu uruchomienia w eksperymencie.
* __ScriptRunConfig__ — dodaj funkcje rejestrowania do skryptu szkoleniowego i załaduj cały folder skryptu z uruchomieniem.  **ScriptRunConfig** jest klasą konfigurowania konfiguracji dla uruchamianych skryptów. Za pomocą tej opcji można dodać kod monitorowania, aby otrzymywać powiadomienia o zakończeniu lub uzyskać widżet wizualny do monitorowania.

## <a name="set-up-the-workspace"></a>Konfigurowanie obszaru roboczego
Przed dodaniem rejestrowania i przesyłania eksperymentu należy skonfigurować obszar roboczy.

1. Załaduj obszar roboczy. Aby dowiedzieć się więcej o ustawianiu konfiguracji obszaru roboczego, zobacz [plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_ws)]


## <a name="option-1-use-start_logging"></a>Opcja 1: Użyj start_logging

**start_logging** tworzy interaktywne uruchomienie do użycia w scenariuszach, takich jak notesy. Wszystkie metryki, które są rejestrowane podczas sesji są dodawane do rekordu uruchomienia w eksperymencie.

Poniższy przykład trenuje prosty model sklearn Ridge lokalnie w lokalnym notebooku Jupyter. Aby dowiedzieć się więcej na temat przesyłania eksperymentów do różnych środowisk, zobacz [Konfigurowanie celów obliczeniowych dla szkoleń dotyczących modelu za pomocą usługi Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets)

### <a name="load-the-data"></a>Ładowanie danych

W tym przykładzie użyto zestawu danych cukrzyca, dobrze znany mały zestaw danych, który pochodzi z scikit-learn. Ta komórka ładuje zestaw danych i dzieli go na losowe zestawy szkoleniowe i testowe.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_data)]

### <a name="add-tracking"></a>Dodawanie śledzenia
Dodaj śledzenie eksperymentu przy użyciu sdk usługi Azure Machine Learning i przekaż utrwalony model do rekordu przebiegu eksperymentu. Poniższy kod dodaje tagi, dzienniki i przekazuje plik modelu do uruchomienia eksperymentu.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=create_experiment)]

Skrypt kończy ```run.complete()```się , co oznacza, że przebieg jest zakończony.  Ta funkcja jest zwykle używana w scenariuszach notesu interaktywnego.

## <a name="option-2-use-scriptrunconfig"></a>Opcja 2: Użyj ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) jest klasą konfigurowania konfiguracji dla uruchamianych skryptów. Za pomocą tej opcji można dodać kod monitorowania, aby otrzymywać powiadomienia o zakończeniu lub uzyskać widżet wizualny do monitorowania.

W tym przykładzie rozwija się na podstawowy model sklearn Ridge z góry. Wykonuje proste parametry zamiatać do przeciągnięcia wartości alfa modelu do przechwytywania metryki i przeszkolonych modeli w działach w ramach eksperymentu. Przykład działa lokalnie względem środowiska zarządzanego przez użytkownika. 

1. Tworzenie skryptu `train.py`szkoleniowego .

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. Odwołania `train.py` do `mylib.py` skryptu, który pozwala uzyskać listę wartości alfa do użycia w modelu grzbietu.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. Konfigurowanie środowiska lokalnego zarządzanego przez użytkownika.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=user_managed_env)]


4. Prześlij skrypt do ```train.py``` uruchomienia w środowisku zarządzanym przez użytkownika. Ten cały folder skryptu jest przesyłany do szkolenia, łącznie z plikiem. ```mylib.py```

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]




## <a name="manage-a-run"></a>Zarządzanie przebiegiem

W artykule [Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych](how-to-manage-runs.md) przedstawiono określone przepływy pracy usługi Azure Machine Learning dotyczące zarządzania eksperymentami.

## <a name="view-run-details"></a>Wyświetlanie szczegółów przebiegu

### <a name="view-activequeued-runs-from-the-browser"></a>Wyświetlanie aktywnych/kolejkowane przebiegi z przeglądarki

Obiekty docelowe obliczeń używane do szkolenia modeli są zasobem udostępnionym. W związku z tym mogą mieć wiele uruchomień w kolejce lub aktywnych w danym czasie. Aby wyświetlić przebiegi określonego obiektu docelowego obliczeń z przeglądarki, należy wykonać następujące czynności:

1. W [studiu usługi Azure Machine Learning](https://ml.azure.com/)wybierz obszar roboczy, a następnie wybierz pozycję __Oblicz__ z lewej strony.

1. Wybierz __klastry szkoleniowe,__ aby wyświetlić listę celów obliczeniowych używanych do szkolenia. Następnie wybierz klaster.

    ![Wybieranie klastra szkoleniowego](./media/how-to-track-experiments/select-training-compute.png)

1. Wybierz __pozycję Przebiegi__. Zostanie wyświetlona lista uruchomień korzystających z tego klastra. Aby wyświetlić szczegóły określonego przebiegu, użyj łącza w kolumnie __Uruchom.__ Aby wyświetlić szczegóły eksperymentu, użyj łącza w kolumnie __Eksperyment.__

    ![Wybieranie przebiegów dla klastra szkoleniowego](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Przebieg może zawierać przebiegi podrzędne, więc jedno zadanie szkoleniowe może spowodować wiele wpisów.

Po zakończeniu biegu nie jest już wyświetlany na tej stronie. Aby wyświetlić informacje o __ukończonych biegach,__ odwiedź sekcję Eksperymenty w studiu i wybierz eksperyment i uruchom. Aby uzyskać więcej informacji, zobacz sekcję [metryki uruchamiania kwerendy.](#queryrunmetrics)

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Monitorowanie działa z widżetem notebooka Jupyter
Korzystając z metody **ScriptRunConfig** do przesyłania przebiegów, można obserwować postęp biegu za pomocą [widżetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Podobnie jak przesyłanie przebiegu, widżet jest asynchroniczny i udostępnia aktualizacje na bieżąco co 10–15 sekund aż do zakończenia zadania.

1. Wyświetl widżet Jupytera, czekając na zakończenie biegu.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Zrzut ekranu przedstawiający widżet notebooka Jupyter](./media/how-to-track-experiments/run-details-widget.png)

   Można również uzyskać łącze do tego samego ekranu w obszarze roboczym.

   ```python
   print(run.get_portal_url())
   ```

2. **[Dla zautomatyzowanych przebiegów uczenia maszynowego]** Aby uzyskać dostęp do wykresów z poprzedniego uruchomienia. Zastąp `<<experiment_name>>` odpowiednią nazwą eksperymentu:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widżet notebooka Jupyter do automatycznego uczenia maszynowego](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Aby wyświetlić dalsze szczegóły potoku kliknij potoku, który chcesz zbadać w tabeli, a wykresy będą renderowane w wyskakującym okienku z usługi Azure Machine Learning studio.

### <a name="get-log-results-upon-completion"></a>Pobieranie wyników dziennika po zakończeniu

Szkolenie modelu i monitorowania występują w tle, dzięki czemu można uruchomić inne zadania podczas oczekiwania. Można również poczekać, aż model zakończy szkolenie przed uruchomieniem więcej kodu. Korzystając z **programu ScriptRunConfig,** można użyć ```run.wait_for_completion(show_output = True)``` do pokazania, kiedy szkolenie modelu jest zakończone. Flaga ```show_output``` daje pełne dane wyjściowe. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Metryki uruchamiania kwerendy

Można wyświetlić metryki przeszkolonego modelu ```run.get_metrics()```za pomocą programu . Teraz można uzyskać wszystkie metryki, które zostały zarejestrowane w powyższym przykładzie, aby określić najlepszy model.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Wyświetlanie eksperymentu w obszarze roboczym w [studio usługi Azure Machine Learning](https://ml.azure.com)

Po zakończeniu eksperymentu można przejść do rekordu przebiegu zarejestrowanego eksperymentu. Dostęp do historii można uzyskać w [studiu usługi Azure Machine Learning.](https://ml.azure.com)

Przejdź do karty Eksperymenty i wybierz eksperyment. Nastąpi przesunięty do pulpitu nawigacyjnego uruchamiania eksperymentu, gdzie można zobaczyć śledzone metryki i wykresy, które są rejestrowane dla każdego uruchomienia. W takim przypadku zarejestrowaliśmy MSE i wartości alfa.

  ![Uruchamianie szczegółów w studio usługi Azure Machine Learning](./media/how-to-track-experiments/experiment-dashboard.png)

Można przejść do określonego uruchomienia, aby wyświetlić jego dane wyjściowe lub dzienniki, lub pobrać migawkę przesłanego eksperymentu, aby udostępnić folder eksperymentu innym osobom.

### <a name="viewing-charts-in-run-details"></a>Wyświetlanie wykresów w szczegółach uruchomienia

Istnieją różne sposoby używania interfejsów API rejestrowania do rejestrowania różnych typów metryk podczas uruchamiania i wyświetlania ich jako wykresów w studio usługi Azure Machine Learning.

|Zarejestrowana wartość|Przykładowy kod| Zobacz w portalu|
|----|----|----|
|Rejestrowanie tablicy wartości liczbowych| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|wykres liniowy z jedną zmienną|
|Rejestrowanie pojedynczej wartości liczbowej o tej samej nazwie metryki wielokrotnie używanej (np. z poziomu pętli for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Wykres liniowy z jedną zmienną|
|Logowanie wiersza z 2 kolumnami numerycznymi wielokrotnie|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Wykres liniowy z dwiema zmiennymi|
|Tabela dzienników z 2 kolumnami numerycznymi|`run.log_table(name='Sine Wave', value=sines)`|Wykres liniowy z dwiema zmiennymi|


## <a name="example-notebooks"></a>Przykładowe notesy
Następujące notesy zademonstrować pojęcia w tym artykule:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

Skorzystaj z poniższych następnych kroków, aby dowiedzieć się, jak używać zestawu SDK usługi Azure Machine Learning dla języka Python:

* Zobacz przykład jak zarejestrować najlepszy model i wdrożyć go w samouczku [Szkolenie modelu klasyfikacji obrazów za pomocą usługi Azure Machine Learning.](tutorial-train-models-with-aml.md)

* Dowiedz się, jak [szkolić modele pytorch za pomocą usługi Azure Machine Learning.](how-to-train-pytorch.md)
