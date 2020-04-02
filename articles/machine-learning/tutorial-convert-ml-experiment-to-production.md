---
title: Konwertowanie kodu eksperymentu uczenia maszynowego na kod produkcyjny
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak konwertować kod eksperymentalny uczenia maszynowego do kodu produkcyjnego przy użyciu szablonu kodu MLOpsPython.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/13/2020
ms.openlocfilehash: e3c9b16ae3d2b06ec19ecd29d15762a065c0c1ae
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521440"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Samouczek: Konwertowanie kodu eksperymentalnego ML na kod produkcyjny

Projekt uczenia maszynowego wymaga eksperymentowania, w którym hipotezy są testowane za pomocą narzędzi agile, takich jak Notebook Jupyter przy użyciu rzeczywistych zestawów danych. Gdy model jest gotowy do produkcji, kod modelu powinny być umieszczone w repozytorium kodu produkcyjnego. W niektórych przypadkach kod modelu musi zostać przekonwertowany na skrypty języka Python, które zostaną umieszczone w repozytorium kodu produkcyjnego. W tym samouczku opisano zalecane podejście do eksportowania kodu eksperymentów do skryptów języka Python.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Czysty kod nieistotny
> * Refaktoryzator Kodu notebooka Jupyter do funkcji
> * Tworzenie skryptów języka Python dla powiązanych zadań
> * Tworzenie testów jednostkowych

## <a name="prerequisites"></a>Wymagania wstępne

- Wygeneruj [szablon MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) i użyj `experimentation/Diabetes Ridge Regression Training.ipynb` notesów. `experimentation/Diabetes Ridge Regression Scoring.ipynb` Te notesy są używane jako przykład konwersji z eksperymentów do produkcji. Możesz znaleźć te notesy w [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation).
- Zainstaluj system `nbconvert`. Postępuj zgodnie z instrukcjami instalacji w sekcji __Instalowanie nbconvert__ na stronie [Instalacja.](https://nbconvert.readthedocs.io/en/latest/install.html)

## <a name="remove-all-nonessential-code"></a>Usuń wszystkie nieistotne kodu

Niektóre kody napisane podczas eksperymentów jest przeznaczony tylko do celów badawczych. W związku z tym pierwszym krokiem do konwersji kodu eksperymentalnego do kodu produkcyjnego jest usunięcie tego nieistotnego kodu. Usunięcie nieistotne kod będzie również kod bardziej chywalne. W tej sekcji usuniesz `experimentation/Diabetes Ridge Regression Training.ipynb` kod z notesu. Instrukcje drukowania `X` kształtu i `y` `features.describe` wywoływania komórek są tylko do eksploracji danych i mogą zostać usunięte. Po usunięciu nieistotnego kodu, `experimentation/Diabetes Ridge Regression Training.ipynb` powinien wyglądać jak następujący kod bez znaczników:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Refaktoryzator kodu do funkcji

Po drugie kod Jupyter musi być refaktoryzowana do funkcji. Refaktoryzowanie kodu do funkcji sprawia, że testowanie jednostek łatwiejsze i sprawia, że kod bardziej do utrzymania. W tej sekcji będziesz refaktoryzator:

- Notatnik treningu regresji Diabetes Ridge(`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Notatnik oceniania regresji Diabetes Ridge(`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Regenerora Diabetes Ridge Recydywa Szkolenia notebooka do funkcji

W `experimentation/Diabetes Ridge Regression Training.ipynb`, wykonaj następujące kroki:

1. Utwórz funkcję `split_data` wywoływaną do dzielenia ramki danych na dane testowe i kolejowe. Funkcja powinna wziąć ramkę dataframe `df` jako parametr i zwrócić `train` słownik `test`zawierający klucze i .

    Przenieś kod w obszarze *Podziel dane do szkolenia i sprawdzania poprawności ustawia* nagłówek do `split_data` funkcji i zmodyfikować go, aby zwrócić `data` obiekt.

1. Utwórz funkcję `train_model`o nazwie `data` , `args` która przyjmuje parametry i zwraca przeszkolony model.

    Przenieś kod pod nagłówkiem *Model szkolenia na zestaw szkolenia* do `train_model` funkcji i zmodyfikować go, aby zwrócić `reg_model` obiekt. Usuń `args` słownik, wartości będą pochodzić `args` z parametru.

1. Utwórz funkcję `get_model_metrics`o nazwie `reg_model` `data`, która przyjmuje parametry i , i ocenia model następnie zwraca słownik metryk dla przeszkolonego modelu.

    Przenieś kod w obszarze *Sprawdź poprawność modelu na sprawdzanie poprawności ustaw* nagłówek do `get_model_metrics` funkcji i zmodyfikować go, aby zwrócić `metrics` obiekt.

Trzy funkcje powinny być następujące:

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

Nadal `experimentation/Diabetes Ridge Regression Training.ipynb`w , wykonaj następujące kroki:

1. Utwórz nową `main`funkcję o nazwie , która nie przyjmuje żadnych parametrów i nic nie zwraca.
1. Przenieś kod pod nagłówkiem "Załaduj dane" do `main` funkcji.
1. Dodaj wywołania nowo zapisanych funkcji do `main` funkcji:
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. Przenieś kod pod nagłówkiem "Zapisz `main` model" do funkcji.

Funkcja `main` powinna wyglądać następująco:

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

Na tym etapie nie powinno być żadnego kodu pozostałego w notesie, który nie jest w funkcji, inne niż instrukcje importu w pierwszej komórce.

Dodaj instrukcję, `main` która wywołuje funkcję.

```python
main()
```

Po refaktoryzacji, `experimentation/Diabetes Ridge Regression Training.ipynb` powinien wyglądać następującego kodu bez znaczników:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Regenerora Diabetes Ridge — notatnik punktacji regresji do funkcji

W `experimentation/Diabetes Ridge Regression Scoring.ipynb`, wykonaj następujące kroki:

1. Utwórz nową `init`funkcję o nazwie , która nie przyjmuje żadnych parametrów i nic nie zwraca.
1. Skopiuj kod pod nagłówkiem `init` "Załaduj model" do funkcji.

Funkcja `init` powinna wyglądać następująco:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Po `init` utworzeniu funkcji zastąp cały kod pod nagłówkiem "Załaduj model" jednym wywołaniem `init` w następujący sposób:

```python
init()
```

W `experimentation/Diabetes Ridge Regression Scoring.ipynb`, wykonaj następujące kroki:

1. Utwórz nową `run`funkcję o `raw_data` `request_headers` nazwie , która przyjmuje i jako parametry i zwraca słownik wyników w następujący sposób:

    ```python
    {"result": result.tolist()}
    ```

1. Skopiuj kod pod nagłówkami "Przygotuj dane" `run` i "Dane wynikowe" do funkcji.

    Funkcja `run` powinna wyglądać jak następujący kod (Pamiętaj, aby usunąć `raw_data` `request_headers`instrukcje, które ustawiają zmienne i , które będą używane później, gdy `run` funkcja jest wywoływana):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Po `run` utworzeniu funkcji zastąp cały kod w nagłówkach "Przygotuj dane" i "Dane wynikowe" następującym kodem:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Poprzedni kod ustawia `raw_data` zmienne `request_header`i `run` , `raw_data` `request_header`wywołuje funkcję z i , i drukuje prognoz.

Po refaktoryzacji, `experimentation/Diabetes Ridge Regression Scoring.ipynb` powinien wyglądać następującego kodu bez znaczników:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Łączenie powiązanych funkcji w plikach języka Python

Po trzecie, powiązane funkcje muszą zostać scalone z plikami języka Python, aby lepiej pomóc w ponownym użyciem kodu. W tej sekcji będziesz tworzyć pliki Języka Python dla następujących notesów:

- Notatnik treningu regresji Diabetes Ridge(`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Notatnik oceniania regresji Diabetes Ridge(`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Tworzenie pliku Python dla notesu szkolenia regresji diabetes Ridge

Konwertowanie notesu na skrypt wykonywalny, uruchamiając `nbconvert` następującą instrukcję w wierszu polecenia, która używa pakietu i ścieżki `experimentation/Diabetes Ridge Regression Training.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Po przekonwertowaniu `train.py`notesu na usunięcie niechcianych komentarzy. Zastąp `main()` wywołanie na końcu pliku wywołaniem warunkowym, takim jak następujący kod:

```python
if __name__ == '__main__':
    main()
```

Plik `train.py` powinien wyglądać następująco:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

`train.py`można teraz wywołać z terminala, uruchamiając `python train.py`plik .
Funkcje `train.py` z mogą być również wywoływane z innych plików.

Plik `train_aml.py` znaleziony w `diabetes_regression/training` katalogu w repozytorium MLOpsPython wywołuje `train.py` funkcje zdefiniowane w kontekście uruchomienia eksperymentu usługi Azure Machine Learning. Funkcje mogą być również wywoływane w testach jednostkowych, omówione w dalszej części tego przewodnika.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Tworzenie pliku Python dla notesu Oceniania regresji grzbietu cukrzycy

Zakryj notes do skryptu wykonywalnego, uruchamiając następującą instrukcję w wierszu polecenia, która używa `nbconvert` pakietu i `experimentation/Diabetes Ridge Regression Scoring.ipynb`ścieżki:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Po przekonwertowaniu `score.py`notesu na usunięcie niechcianych komentarzy. Plik `score.py` powinien wyglądać następująco:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

Zmienna musi być globalna, `model` tak aby była widoczna w całym skrypcie. Dodaj następującą instrukcję na `init` początku funkcji:

```python
global model
```

Po dodaniu poprzedniej `init` instrukcji funkcja powinna wyglądać następująco:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Tworzenie testów jednostkowych dla każdego pliku języka Python

Po czwarte, utwórz testy jednostkowe dla funkcji języka Python. Testy jednostkowe chronią kod przed regresjami funkcjonalnymi i ułatwiają jego konserwację. W tej sekcji będziesz tworzyć testy jednostkowe dla `train.py`funkcji w .

`train.py`zawiera wiele funkcji, ale utworzymy tylko jeden `train_model` test jednostkowy dla funkcji przy użyciu pytest framework w tym samouczku. Pytest nie jest jedyną platformą testowania jednostek Języka Python, ale jest jedną z najczęściej używanych. Aby uzyskać więcej informacji, odwiedź [Pytest](https://pytest.org).

Test jednostkowy zwykle zawiera trzy główne czynności:

- Rozmieszczanie obiektu — tworzenie i konfigurowanie niezbędnych obiektów
- Działaj na przedmiot
- Potwierdzanie oczekiwanych

Test jednostkowy `train_model` wywoła niektóre zakodowane dane i argumenty `train_model` i sprawdź poprawność, która działała zgodnie z oczekiwaniami przy użyciu wynikowego modelu uczonego, aby przewidzieć i porównując tę prognozę z oczekiwaną wartością.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zrozumiesz, jak przekonwertować z eksperymentu na kod produkcyjny, zobacz następujące łącza, aby uzyskać więcej informacji i następne kroki:

+ [MLOpsPython:](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md)Tworzenie potoku ciągłej integracji/ciągłego wdrażania w celu uczenia, oceny i wdrażania własnego modelu przy użyciu potoków platformy Azure i usługi Azure Machine Learning
+ [Monitorowanie przebiegów eksperymentu usługi Azure ML i metryk](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
+ [Monitorowanie i zbieranie danych z punktów końcowych usługi sieci web ml](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
