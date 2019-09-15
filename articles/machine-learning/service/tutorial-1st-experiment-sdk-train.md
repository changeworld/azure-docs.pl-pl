---
title: 'Samouczek: Uczenie swojego pierwszego modelu ML'
titleSuffix: Azure Machine Learning
description: W ramach tego samouczka nauczysz się podstawowe wzorce projektowe w Azure Machine Learning i uczenie prostego modelu uczenia scikitego na podstawie zestawu danych cukrzycą.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/03/2019
ms.openlocfilehash: b5d3a687adc8ecefcf581f7eda3b9e13d1973c62
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004034"
---
# <a name="tutorial-train-your-first-ml-model"></a>Samouczek: Uczenie swojego pierwszego modelu ML

Ten samouczek jest **drugą częścią dwuczęściowej serii samouczków**. W poprzednim samouczku [utworzono obszar roboczy i wybrano środowisko programistyczne](tutorial-1st-experiment-sdk-setup.md). W ramach tego samouczka nauczysz się podstawowe wzorce projektowe w Azure Machine Learning i uczenie prostego modelu uczenia scikitego na podstawie zestawu danych cukrzycą. Po ukończeniu tego samouczka będziesz mieć praktyczną wiedzę na temat zestawu SDK w celu skalowania w górę w celu opracowywania bardziej złożonych eksperymentów i przepływów pracy.

W tym samouczku nauczysz się wykonywać następujące zadania:

> [!div class="checklist"]
> * Łączenie obszaru roboczego i tworzenie eksperymentu
> * Ładowanie danych i uczenie modeli scikit — uczenie się
> * Wyświetlanie wyników szkoleniowych w portalu
> * Pobieranie najlepszego modelu

## <a name="prerequisites"></a>Wymagania wstępne

Jedynym wymaganiem wstępnym jest uruchomienie części jednego z tego samouczka, [Ustawienia środowiska i obszaru roboczego](tutorial-1st-experiment-sdk-setup.md).

W tej części samouczka uruchamiasz kod w notesie `tutorials/tutorial-1st-experiment-sdk-train.ipynb` przykładowym Jupyter otwartym na końcu części pierwszej. Ten artykuł zawiera ten sam kod, który znajduje się w notesie.

## <a name="launch-jupyter-web-interface"></a>Uruchom interfejs sieci Web Jupyter

1. Na stronie obszaru roboczego w Azure Portal wybierz pozycję **maszyny wirtualne Notes** po lewej stronie.

1. W kolumnie **URI** w ramach tego samouczka wybierz pozycję **Jupyter** .

    ![Uruchom serwer notesu Jupyter](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

   Link uruchamia serwer notesu i otwiera stronę sieci Web notesu Jupyter na nowej karcie przeglądarki.  Ten link będzie działał tylko dla osoby, która tworzy maszynę wirtualną. Każdy użytkownik obszaru roboczego musi utworzyć własną maszynę wirtualną.

1. Na stronie Jupyter notesu wybierz górny folder NazwaFolderu, który ma swoją nazwę użytkownika.  

   Ten folder istnieje na [koncie magazynu](concept-workspace.md#resources) obszaru roboczego, a nie na maszynie wirtualnej notesu.  Po usunięciu maszyny wirtualnej notesu nadal będzie można zachować całą swoją służbę.  Po utworzeniu nowej maszyny wirtualnej notesu zostanie ona załadowana w tym samym folderze. Jeśli udostępnisz obszar roboczy innym osobom, zobaczysz Twój folder i zobaczysz ich.

1. Otwórz podkatalog, a następnie Otwórz Notes `tutorials/tutorial-1st-experiment-sdk-train.ipynb`Jupyter, a **nie** `.yml` plik o tej samej nazwie. `samples-*` 

## <a name="connect-workspace-and-create-experiment"></a>Połącz obszar roboczy i Utwórz eksperyment

> [!Important]
> Pozostała część tego artykułu zawiera tę samą zawartość, która jest wyświetlana w notesie.  
>
> Przełącz się do notesu Jupyter teraz, jeśli chcesz czytać wraz z uruchamianiem kodu. 
> Aby uruchomić pojedynczą komórkę kodu w notesie, kliknij komórkę kod i naciśnij **klawisze SHIFT + ENTER**. Lub Uruchom cały Notes, wybierając pozycję **komórka > Uruchom wszystkie** z górnego menu.

Zaimportuj `config.json`klasęi Załaduj informacje o subskrypcji z pliku przy użyciu funkcji `from_config().` ta funkcja szuka domyślnego pliku JSON w bieżącym katalogu, ale można także określić parametr ścieżki, aby wskazywał na plik. `Workspace` przy `from_config(path="your/file/path")`użyciu. W przypadku serwera notesu w chmurze plik jest automatycznie w katalogu głównym.

Jeśli Poniższy kod pyta o dodatkowe uwierzytelnianie, wystarczy wkleić link w przeglądarce i wprowadzić token uwierzytelniania.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Teraz Utwórz eksperyment w obszarze roboczym. Eksperyment jest innym podstawnym zasobem w chmurze, który reprezentuje kolekcję prób (poszczególne modele są uruchamiane). W tym samouczku użyjesz eksperymentu, aby utworzyć przebiegi i śledzić szkolenia modeli w Azure Portal. Parametry obejmują odwołanie do obszaru roboczego i nazwę ciągu dla eksperymentu.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Ładowanie danych i przygotowanie do szkolenia

W tym samouczku używany jest zestaw danych cukrzycą, który jest wstępnie znormalizowanym zestawem danych zawartym w scikit. Ten zestaw danych używa funkcji, takich jak wiek, płeć i BMI, do przewidywania postępu choroby cukrzycą. Załaduj dane z `load_diabetes()` funkcji statycznej i Podziel ją na zestawy szkoleniowe i testowe przy `train_test_split()`użyciu. Ta funkcja dzieli dane, aby model miał niewidoczne dane, które są używane do testowania po szkoleniu.


```python
from sklearn.datasets import load_diabetes
from sklearn.model_selection import train_test_split

X, y = load_diabetes(return_X_y = True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Szkolenie modelu

Uczenie prostego modelu uczenia się w scikit można łatwo wykonać lokalnie w przypadku szkolenia na małą skalę, ale w przypadku szkolenia wielu iteracji z dziesiątami z różnych permutacji funkcji i ustawień parametrów, można łatwo utracić śledzenie modeli, które zostały przeszkolone i jak przeszkolone. Poniższy Wzorzec projektowy pokazuje, jak korzystać z zestawu SDK, aby łatwo śledzić szkolenia w chmurze.

Utwórz skrypt, który pociąga za siebie modele pierścieniowe w pętli za pomocą różnych wartości alfanumerycznych parametru.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

Powyższy kod wykonuje następujące czynności:

1. Dla każdej wartości parametru alfa w `alphas` tablicy zostanie utworzony nowy przebieg w ramach eksperymentu. Wartość alfa jest zarejestrowana w celu rozróżnienia między każdym przebiegiem.
1. W każdym przebiegu model pierścieniowy jest skonkretyzowany, szkolony i używany do uruchamiania prognoz. Element główny-średni-kwadratowy jest obliczany dla wartości rzeczywistych i przewidywanych, a następnie rejestrowanych w ramach uruchomienia. W tym momencie przebieg ma metadane dołączone zarówno do wartości alfa, jak i dokładności RMSE.
1. Następnie model każdego przebiegu jest serializowany i przekazywany do przebiegu. Pozwala to na pobranie pliku modelu z przebiegu w portalu.
1. Po zakończeniu każdej iteracji przebieg jest wykonywany przez wywołanie `run.complete()`.

Po zakończeniu szkolenia Wywołaj `experiment` zmienną, aby pobrać link do eksperymentu w portalu.

```python
experiment
```

<table style="width:100%"><tr><th>Name</th><th>Obszar roboczy</th><th>Strona raportu</th><th>Strona docs</th></tr><tr><td>cukrzycą — eksperyment</td><td>Nazwa Twojego obszaru roboczego</td><td>Link do witryny Azure Portal</td><td>Link do dokumentacji</td></tr></table>

## <a name="view-training-results-in-portal"></a>Wyświetlanie wyników szkolenia w portalu

Kliknięcie **linku do witryny Azure Portal** spowoduje przejście do strony głównej eksperymentu. W tym miejscu zobaczysz wszystkie uruchomienia indywidualne w eksperymentie. Wszystkie wartości zarejestrowane przez użytkownika (`alpha_value` i `rmse`w tym przypadku) stają się polami dla każdego przebiegu, a także stają się dostępne dla wykresów i kafelków w górnej części strony eksperymentu. Aby dodać zarejestrowanej metrykę do wykresu lub kafelka, umieść kursor nad nim, kliknij przycisk Edytuj i Znajdź metrykę zarejestrowaną niestandardowo.

Gdy szkolenia modeli są przeprowadzane na dużą skalę i tysiące różnych uruchomień, ta strona ułatwia przeglądanie każdego modelu, który jest przeszkolony, a w odróżnieniu od czasu, w jaki zostały one przeszkolone, oraz sposobu zmiany unikatowych metryk w czasie.

![Strona głównej eksperymentu w portalu](./media/tutorial-quickstart/experiment-main.png)

Kliknięcie linku z numerem uruchomienia w `RUN NUMBER` kolumnie spowoduje przejście do strony dla każdego pojedynczego uruchomienia. Na **karcie domyślne znajdują** się szczegółowe informacje dotyczące poszczególnych przebiegów. Przejdź do karty dane **wyjściowe** i zobaczysz `.pkl` plik dla modelu, który został przekazany do przebiegu podczas każdej iteracji szkoleniowej. W tym miejscu możesz pobrać plik modelu, zamiast konieczności ręcznego ponownego uczenia go.

![Strona szczegóły uruchamiania w portalu](./media/tutorial-quickstart/model-download.png)

## <a name="get-the-best-model"></a>Uzyskaj najlepszy model

Oprócz możliwości pobierania plików modelu z eksperymentu w portalu można również pobrać je programowo. Poniższy kod wykonuje iterację w każdym przebiegu eksperymentu i uzyskuje dostęp do zarejestrowanych metryk uruchomienia i szczegółów uruchomienia (które zawierają run_id). Pozwala to na śledzenie najlepszego przebiegu, w tym przypadku przebieg z najniższym średnim elementem głównym-kwadratowym.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

Użyj najlepszego identyfikatora przebiegu, aby pobrać pojedynczy przebieg przy użyciu `Run` konstruktora wraz z obiektem eksperymentu. Następnie Wywołaj `get_file_names()` , aby zobaczyć wszystkie pliki dostępne do pobrania z tego przebiegu. W takim przypadku przekazano tylko jeden plik dla każdego przebiegu podczas szkolenia.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Wywołaj `download()` obiekt Run, określając nazwę pliku modelu do pobrania. Domyślnie ta funkcja jest pobierana do bieżącego katalogu.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Nie wykonuj tej sekcji, jeśli planujesz Uruchamianie innych samouczków Azure Machine Learning.

### <a name="stop-the-notebook-vm"></a>Zatrzymaj maszynę wirtualną notesu

Jeśli używasz serwera notesu w chmurze, Zatrzymaj maszynę wirtualną, gdy nie używasz jej do obniżenia kosztów.

1. W obszarze roboczym wybierz pozycję **maszyny wirtualne Notes**.

   ![Zatrzymaj serwer maszyn wirtualnych](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. Z listy wybierz maszynę wirtualną.

1. Wybierz pozycję **Zatrzymaj**.

1. Gdy wszystko będzie gotowe do korzystania z serwera, wybierz pozycję **Uruchom**.

### <a name="delete-everything"></a>Usuń wszystko

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące zadania:

> [!div class="checklist"]
> * Nawiązano połączenie z obszarem roboczym i utworzono eksperyment
> * Ładowane dane i przeszkolone modele uczenia scikitego
> * Oglądane wyniki szkolenia w portalu i pobrane modele

[Wdróż model](tutorial-deploy-models-with-aml.md) za pomocą usługi Azure Machine Learning.
Dowiedz się, jak opracowywać zautomatyzowane eksperymenty [uczenia maszynowego](tutorial-auto-train-models.md) .
