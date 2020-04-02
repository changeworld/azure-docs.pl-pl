---
title: 'Samouczek: Trenuj swój pierwszy model usługi Azure ML w języku Python'
titleSuffix: Azure Machine Learning
description: W tym samouczku dowiesz się o podstawowych wzorcach projektowania w usłudze Azure Machine Learning i trenujesz prosty model scikit-learn na podstawie zestawu danych cukrzycy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: c8f259d2d4df46470a042c3f65ac1b8e1f66b1dd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546056"
---
# <a name="tutorial-train-your-first-ml-model"></a>Samouczek: Trenuj swój pierwszy model ML

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ten samouczek jest **drugą częścią dwuczęściowej serii samouczków**. W poprzednim samouczku [utworzono obszar roboczy i wybrano środowisko programistyczne](tutorial-1st-experiment-sdk-setup.md). W tym samouczku dowiesz się o podstawowych wzorcach projektowania w usłudze Azure Machine Learning i trenujesz prosty model scikit-learn na podstawie zestawu danych cukrzycy. Po ukończeniu tego samouczka będziesz mieć praktyczną wiedzę na temat zestawu SDK, aby skalować się w górę, aby opracować bardziej złożone eksperymenty i przepływy pracy.

W tym samouczku nauczysz się wykonywać następujące zadania:

> [!div class="checklist"]
> * Łączenie obszaru roboczego i tworzenie eksperymentu
> * Załaduj dane i trenuj modele scikit-learn
> * Wyświetlanie wyników treningu w studiu
> * Pobieranie najlepszego modelu

## <a name="prerequisites"></a>Wymagania wstępne

Jedynym warunkiem jest uruchomienie części 1 tego samouczka, [środowiska instalacji i obszaru roboczego](tutorial-1st-experiment-sdk-setup.md).

W tej części samouczka można uruchomić kod w przykładzie tutoriale notesu *Jupyter/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb* otwarty na końcu części pierwszej. W tym artykule o wiele więcej niż w notesie.

## <a name="open-the-notebook"></a>Otwieranie notesu

1. Zaloguj się do [studia usługi Azure Machine Learning](https://ml.azure.com/).

1. Otwórz **tutorial-1st-experiment-sdk-train.ipynb** w folderze, jak pokazano w [części 1](tutorial-1st-experiment-sdk-setup.md#open).


> [!Warning]
> **Nie** należy tworzyć *nowego* notebooka w interfejsie Jupyter! *Samouczki notesu/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb* zawiera **cały kod i dane potrzebne** do tego samouczka.

## <a name="connect-workspace-and-create-experiment"></a>Łączenie obszaru roboczego i tworzenie eksperymentu

> [!Important]
> Pozostała część tego artykułu zawiera tę samą zawartość, co w notesie.  
>
> Przełącz się do notebooka Jupyter teraz, jeśli chcesz przeczytać wzdłuż podczas uruchamiania kodu. 
> Aby uruchomić pojedynczą komórkę kodu w notesie, kliknij komórkę kodu i naciśnij **klawisze Shift+Enter**. Możesz też uruchomić cały notes, wybierając pozycję **Uruchom wszystko** z górnego paska narzędzi.

Zaimportuj `Workspace` klasę i załaduj informacje o subskrypcji z pliku `config.json` za pomocą funkcji `from_config().` Domyślnie wyszukuje plik JSON `from_config(path="your/file/path")`w bieżącym katalogu, ale można również określić parametr ścieżki, który ma wskazywać plik za pomocą programu . Na serwerze notesu w chmurze plik jest automatycznie w katalogu głównym.

Jeśli poniższy kod prosi o dodatkowe uwierzytelnienie, po prostu wklej łącze w przeglądarce i wprowadź token uwierzytelniania.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Teraz utwórz eksperyment w obszarze roboczym. Eksperyment to kolejny podstawowy zasób w chmurze, który reprezentuje zbiór prób (poszczególne przebiegi modelu). W tym samouczku służysz do tworzenia przebiegów i śledzenia szkolenia modelu w studiu usługi Azure Machine Learning. Parametry obejmują odwołanie do obszaru roboczego i nazwę ciągu eksperymentu.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Załaduj dane i przygotuj się do treningu

W tym samouczku używasz zestawu danych cukrzyca, który używa funkcji, takich jak wiek, płeć i BMI do przewidywania progresji choroby cukrzycy. Załaduj dane z klasy [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) i `train_test_split()`podziel je na zestawy szkoleniowe i testowe przy użyciu . Ta funkcja segreguje dane, więc model ma niewidoczne dane do użycia do testowania po szkoleniu.


```python
from azureml.opendatasets import Diabetes
from sklearn.model_selection import train_test_split

x_df = Diabetes.get_tabular_dataset().to_pandas_dataframe().dropna()
y_df = x_df.pop("Y")

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Szkolenie modelu

Szkolenie prostego modelu scikit-learn można łatwo wykonać lokalnie do szkolenia na małą skalę, ale podczas szkolenia wielu iteracji z dziesiątkami różnych permutacji funkcji i ustawień hiperparametru, łatwo jest stracić informacje o tym, jakie modele zostały przeszkolone i jak je przeszkoliłeś. Poniższy wzorzec projektu pokazuje, jak wykorzystać sdk, aby łatwo śledzić szkolenia w chmurze.

Tworzenie skryptu, który trenuje modele grzbietu w pętli przez różne wartości alfa hiperparametryczne.


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

1. Dla każdej wartości hiperparametrycznej alfa w `alphas` tablicy w ramach eksperymentu tworzony jest nowy przebieg. Wartość alfa jest rejestrowana w celu rozróżnienia między każdym przebiegiem.
1. W każdym uruchomieniu model Ridge jest tworzone, trenowane i używane do uruchamiania prognoz. Błąd typu root-mean-squared jest obliczany dla wartości rzeczywistych i przewidywanych, a następnie rejestrowany w przebiegu. W tym momencie run ma metadane dołączone zarówno dla wartości alfa i dokładności rmse.
1. Następnie model dla każdego uruchomienia jest serializowany i przekazany do uruchomienia. Dzięki temu można pobrać plik modelu z uruchomienia w studio.
1. Na końcu każdej iteracji przebieg jest `run.complete()`uzupełniony przez wywołanie .

Po zakończeniu szkolenia, wywołać zmienną, `experiment` aby pobrać link do eksperymentu w studio.

```python
experiment
```

<table style="width:100%"><tr><th>Nazwa</th><th>Workspace</th><th>Strona raportu</th><th>Strona dokumenty</th></tr><tr><td>eksperyment cukrzycy</td><td>nazwa obszaru roboczego</td><td>Łącze do studia usługi Azure Machine Learning</td><td>Łącze do dokumentacji</td></tr></table>

## <a name="view-training-results-in-studio"></a>Wyświetlanie wyników szkolenia w studio

Po **łączu do usługi Azure Machine Learning studio** przeniesie Cię do głównej strony eksperymentu. Tutaj widzisz wszystkie poszczególne biegi w eksperymencie. Wszystkie niestandardowe wartości`alpha_value` rejestrowane `rmse`(i , w tym przypadku) stają się polami dla każdego uruchomienia, a także stają się dostępne dla wykresów i kafelków u góry strony eksperymentu. Aby dodać zarejestrowaną metrykę do wykresu lub kafelka, umieść nad nią wskaźnik myszy, kliknij przycisk edycji i znajdź metrykę zarejestrowaną na zamówienie.

Podczas szkolenia modeli na dużą skalę w setkach i tysiącach oddzielnych przebiegów, ta strona ułatwia zobaczenie każdego wyszkolonego modelu, w szczególności sposobu ich przeszkolenia i zmiany unikatowych wskaźników w czasie.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/experiment-main.png" alt-text="Strona głównego eksperymentu w studiu.":::


Wybierz łącze numeru `RUN NUMBER` uruchomienia w kolumnie, aby wyświetlić stronę dla pojedynczego uruchomienia. Domyślna karta **Szczegóły** zawiera bardziej szczegółowe informacje o każdym uruchomieniu. Przejdź do **danych wyjściowych + dzienniki** `.pkl` kartę, a zobaczysz plik dla modelu, który został przekazany do uruchomienia podczas każdej iteracji szkolenia. W tym miejscu można pobrać plik modelu, zamiast przeszkolić go ręcznie.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/model-download.png" alt-text="Uruchom stronę szczegółów w studio.":::

## <a name="get-the-best-model"></a>Uzyskaj najlepszy model

Oprócz możliwości pobierania plików modelu z eksperymentu w studio, można je również pobrać programowo. Poniższy kod iteruje za pośrednictwem każdego uruchomienia w eksperymencie i uzyskuje dostęp zarówno do metryk przebiegu rejestrowane i szczegóły uruchomienia (który zawiera run_id). To śledzi najlepsze uruchomienie, w tym przypadku uruchomić z najniższym root-mean-squared-error.

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

Użyj identyfikatora najlepszego uruchomienia, aby pobrać `Run` poszczególne uruchomienia przy użyciu konstruktora wraz z obiektem eksperymentu. Następnie `get_file_names()` wywołać, aby wyświetlić wszystkie pliki dostępne do pobrania z tego uruchomienia. W takim przypadku przesłano tylko jeden plik dla każdego uruchomienia podczas treningu.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Wywołanie `download()` obiektu uruchamiania, określając nazwę pliku modelu do pobrania. Domyślnie ta funkcja jest pobierana do bieżącego katalogu.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Nie należy ukończyć tej sekcji, jeśli planujesz uruchomienie innych samouczków usługi Azure Machine Learning.

### <a name="stop-the-compute-instance"></a>Zatrzymywać wystąpienie obliczeń

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Usuń wszystko

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonaliśmy następujące zadania:

> [!div class="checklist"]
> * Połączenie obszaru roboczego i utworzenie eksperymentu
> * Załadowane dane i wyszkolone modele scikit-learn
> * Oglądane wyniki szkolenia w studio i pobrane modele

[Wdróż model](tutorial-deploy-models-with-aml.md) za pomocą usługi Azure Machine Learning.
Dowiedz się, jak opracować zautomatyzowane eksperymenty [uczenia maszynowego.](tutorial-auto-train-models.md)
