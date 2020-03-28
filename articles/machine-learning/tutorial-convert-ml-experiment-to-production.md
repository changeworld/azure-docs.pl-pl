---
title: Konwertowanie kodu eksperymentu uczenia maszynowego na kod produkcyjny
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak konwertować kod eksperymentalny uczenia maszynowego do kodu produkcyjnego przy użyciu szablonu kodu MLOpsPython.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/13/2020
ms.openlocfilehash: f40c2b5f7134458b3f8cb492652bebf14388634c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477140"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Samouczek: Konwertowanie kodu eksperymentalnego ML na kod produkcyjny

Projekt uczenia maszynowego wymaga eksperymentowania, w którym hipotezy są testowane za pomocą narzędzi agile, takich jak Notebook Jupyter przy użyciu rzeczywistych zestawów danych. Gdy model jest gotowy do produkcji, kod modelu powinny być umieszczone w repozytorium kodu produkcyjnego. W niektórych przypadkach kod modelu musi zostać przekonwertowany na skrypty języka Python, które zostaną umieszczone w repozytorium kodu produkcyjnego. W tym samouczku opisano zalecane podejście do eksportowania kodu eksperymentów do skryptów języka Python.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Czysty kod nieistotny
> * Refaktoryzator Kodu notebooka Jupyter do funkcji
> * Tworzenie skryptów języka Python dla powiązanych zadań
> * Tworzenie testów jednostkowych

## <a name="prerequisites"></a>Wymagania wstępne

- Wygeneruj [szablon MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) i użyj `experimentation/Diabetes Ridge Regression Training.ipynb` notesów. `experimentation/Diabetes Ridge Regression Scoring.ipynb` Te notesy są używane jako przykład konwersji z eksperymentów do produkcji. Możesz znaleźć te notesy w [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation).
- Zainstaluj nbconvert. Postępuj zgodnie z instrukcjami instalacji w sekcji __Instalowanie nbconvert__ na stronie [Instalacja.](https://nbconvert.readthedocs.io/en/latest/install.html)

## <a name="remove-all-nonessential-code"></a>Usuń wszystkie nieistotne kodu

Niektóre kody napisane podczas eksperymentów jest przeznaczony tylko do celów badawczych. W związku z tym pierwszym krokiem do konwersji kodu eksperymentalnego do kodu produkcyjnego jest usunięcie tego nieistotnego kodu. Usunięcie nieistotne kod będzie również kod bardziej chywalne. W tej sekcji usuniesz `experimentation/Diabetes Ridge Regression Training.ipynb` kod z notesu. Instrukcje drukowania `X` kształtu i `y` `features.describe` wywoływania komórek są tylko do eksploracji danych i mogą zostać usunięte. Po usunięciu nieistotnego kodu, `experimentation/Diabetes Ridge Regression Training.ipynb` powinien wyglądać jak następujący kod bez znaczników:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib

X, y = load_diabetes(return_X_y=True)

X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

alpha = 0.5

reg = Ridge(alpha=alpha)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg.predict(data["test"]["X"])
print("mse", mean_squared_error(preds, data["test"]["y"]))

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Refaktoryzator kodu do funkcji

Po drugie kod Jupyter musi być refaktoryzowana do funkcji. Refaktoryzowanie kodu do funkcji sprawia, że testowanie jednostek łatwiejsze i sprawia, że kod bardziej do utrzymania. W tej sekcji będziesz refaktoryzator:

- Notatnik treningu regresji Diabetes Ridge(`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Notatnik oceniania regresji Diabetes Ridge(`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Regenerora Diabetes Ridge Recydywa Szkolenia notebooka do funkcji

W `experimentation/Diabetes Ridge Regression Training.ipynb`, wykonaj następujące kroki:

1. Utwórz funkcję `train_model`o nazwie `data` , `alpha` która przyjmuje parametry i zwraca model.
1. Skopiuj kod pod nagłówkami "Train Model on Training Set" `train_model` i "Validate Model on Validation Set" do funkcji.

Funkcja `train_model` powinna wyglądać następująco:

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

Po `train_model` utworzeniu funkcji zastąp kod pod nagłówkami "Train Model on Training Set" i "Validate Model on Validation Set" następującą instrukcją:

```python
reg = train_model(data, alpha)
```

Poprzednia instrukcja `train_model` wywołuje `data` funkcję `alpha` przekazywania i parametrów i zwraca model.

W `experimentation/Diabetes Ridge Regression Training.ipynb`, wykonaj następujące kroki:

1. Utwórz nową `main`funkcję o nazwie , która nie przyjmuje żadnych parametrów i nic nie zwraca.
1. Skopiuj kod pod nagłówkami "Załaduj dane", "Podziel dane `main` na zestawy szkoleniowe i walidacyjne" i "Zapisz model" do funkcji.
1. Skopiuj `train_model` nowo `main` utworzone wywołanie do funkcji.

Funkcja `main` powinna wyglądać następująco:

```python
def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)
```

Po `main` utworzeniu funkcji zastąp cały kod pod nagłówkami "Załaduj dane", "Podziel dane na zestawy `train_model` szkoleniowe i sprawdzania poprawności" i "Zapisz model" wraz z nowo utworzonym wywołaniem następującą instrukcją:

```python
main()
```

Po refaktoryzacji, `experimentation/Diabetes Ridge Regression Training.ipynb` powinien wyglądać następującego kodu bez znaczników:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

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

Konwertowanie notesu na skrypt wykonywalny, uruchamiając następującą instrukcję w `experimentation/Diabetes Ridge Regression Training.ipynb`wierszu polecenia, która używa pakietu nbconvert i ścieżki :

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Po przekonwertowaniu `train.py`notesu na program usuń wszystkie komentarze. Plik `train.py` powinien wyglądać następująco:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():
    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

Plik `train.py` znaleziony w `diabetes_regression/training` katalogu w repozytorium MLOpsPython obsługuje argumenty wiersza `model_name`polecenia `alpha`(a mianowicie `build_id`, , i ). Obsługa argumentów wiersza polecenia można `train.py` dodać do pliku `alpha` do obsługi dynamicznych nazw modelu i wartości, ale nie jest konieczne, aby kod został pomyślnie wykonany.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Tworzenie pliku Python dla notesu Oceniania regresji grzbietu cukrzycy

Zakryj notes do skryptu wykonywalnego, uruchamiając następującą instrukcję w wierszu polecenia, która używa pakietu nbconvert i `experimentation/Diabetes Ridge Regression Scoring.ipynb`ścieżki:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Po przekonwertowaniu `score.py`notesu na program usuń wszystkie komentarze. Plik `score.py` powinien wyglądać następująco:

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

Funkcja `train_model` wymaga modyfikacji, aby utworzyć wystąpienie modelu zmiennej globalnej, tak aby był widoczny w całym skrypcie. Dodaj następującą instrukcję na `init` początku funkcji:

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

Po czwarte, testy jednostkowe muszą być tworzone dla każdego pliku języka Python, co sprawia, że kod jest bardziej niezawodny i łatwiejszy do utrzymania. W tej sekcji zostaniesz utwórz test jednostkowy dla `train.py`jednej z funkcji w .

`train.py`zawiera dwie `train_model` funkcje: i `main`. Każda funkcja wymaga testu jednostkowego, ale utworzymy tylko `train_model` test jednostkowy dla jednej funkcji przy użyciu pytest framework w tym samouczku. Pytest nie jest jedyną platformą testowania jednostek Języka Python, ale jest jedną z najczęściej używanych. Aby uzyskać więcej informacji, odwiedź [Pytest](https://pytest.org).

Test jednostkowy zwykle zawiera trzy główne czynności:

- Rozmieszczanie obiektu — tworzenie i konfigurowanie niezbędnych obiektów
- Działaj na przedmiot
- Potwierdzanie oczekiwanych

Typowym warunkiem `train_model` jest, `data` `alpha` kiedy i wartość są przekazywane. Oczekiwany wynik jest, że `Ridge.train` i `Ridge.predict` funkcje powinny być wywoływane. Ponieważ metody szkolenia uczenia maszynowego często nie `Ridge.train` są szybko działające, wywołanie będzie szydził. Ponieważ zwracana `Ridge.train` wartość jest obiektem szyderczym, będziemy również szydzić `Ridge.predict`. Test jednostkowy `train_model` do `data` testowania `alpha` przekazywania i wartość `Ridge.train` `Ridge.predict` z oczekiwanym wynikiem i funkcje wywoływane przy użyciu mocking i Pytest framework powinien wyglądać następującego kodu:

```python
import pytest
from code.training.train import train_model

class TestTrain:

    @staticmethod
    def test_train_model(mocker):
        # Arrange
        test_data = {"train": {"X": [[1, 2, 3]], "y": [0]},
                     "test": {"X": [[4, 5, 6]], "y": [0]}}
        test_alpha = 0.5
        mock_ridge_fit = mocker.patch('Ridge.fit')
        mock_ridge_predict = mocker.patch('Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>Użyj własnego modelu z szablonem kodu MLOpsPython

Jeśli zostały następujące kroki w tym przewodniku, będziesz mieć zestaw skryptów, które są skorelowane z train/score/test skryptów dostępnych w repozytorium MLOpsPython.  Zgodnie z wyżej wymienioną strukturą następujące kroki będą przebiegać przez to, co jest potrzebne do użycia tych plików dla własnego projektu uczenia maszynowego:

1. Postępuj zgodnie z przewodnikiem MLOpsPython [Wprowadzenie](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md)
2. Postępuj zgodnie z instrukcjami mlopsPython [bootstrap,](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) aby utworzyć punkt początkowy projektu
3. Wymień kod szkolenia
4. Zastąp kod wyników
5. Aktualizowanie kodu oceny

### <a name="follow-the-getting-started-guide"></a>Postępuj zgodnie z przewodnikiem wprowadzenie
Po [wprowadzenie](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) przewodnika jest konieczne, aby infrastruktury pomocniczej i potoków do wykonania MLOpsPython.

### <a name="follow-the-bootstrap-instructions"></a>Postępuj zgodnie z instrukcjami Bootstrap

[Przewodnik po repozytorium Bootstrap firmy MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) pomoże Ci szybko przygotować repozytorium dla projektu.

**Uwaga:** Ponieważ skrypt bootstrap zmieni nazwę folderu diabetes_regression na nazwę wybranego projektu, będziemy odnosić `[project name]` się do projektu tak, jak wtedy, gdy ścieżki są zaangażowane.

### <a name="replace-training-code"></a>Zamień kod szkolenia

Zastąpienie kodu używanego do szkolenia modelu i usunięcie lub zastąpienie odpowiednich testów jednostkowych jest wymagane dla rozwiązania do działania z własnym kodem. Wykonaj następujące kroki w szczególności:

1. Zamień `[project name]/training/train.py`. Ten skrypt trenuje model lokalnie lub na podstawie obliczeń usługi Azure ML.
1. Usuń lub wymień testy jednostkowe szkolenia znalezione w`[project name]/training/test_train.py`

### <a name="replace-score-code"></a>Zamień kod wyniku

Dla modelu, aby zapewnić możliwości wnioskowania w czasie rzeczywistym, kod wynik musi zostać zastąpiony. Szablon MLOpsPython używa kodu wynik do wdrażania modelu do oceniania w czasie rzeczywistym w aplikacjach ACI, AKS lub sieci Web. Jeśli chcesz zachować punktację, zamień `[project name]/scoring/score.py`.

### <a name="update-evaluation-code"></a>Kod oceny aktualizacji

Szablon MLOpsPython używa skryptu evaluate_model do porównania wydajności nowo przeszkolonego modelu i bieżącego modelu produkcyjnego opartego na błędzie Mean Squared Error. Jeśli wydajność nowo przeszkolonego modelu jest lepsza niż bieżący model produkcyjny, potoki będą kontynuowane. W przeciwnym razie potoki są anulowane. Aby zachować ocenę, zastąp `mse` wszystkie wystąpienia w `[project name]/evaluate/evaluate_model.py` metryki, które chcesz.

Aby pozbyć się oceny, ustaw zmienną `RUN_EVALUATION` `.pipelines/[project name]-variables-template.yml` potoku DevOps w . `false`

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zrozumiesz, jak przekonwertować z eksperymentu na kod produkcyjny, skorzystaj z następujących łączy, aby dowiedzieć się, jak monitorować przebiegi eksperymentu i modele wdrożone jako usługi sieci web:

> [!div class="nextstepaction"]
> [Monitorowanie przebiegów eksperymentu usługi Azure ML i metryk](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [Monitoruj i zbieraj dane z punktów końcowych usługi sieci Web ml](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
