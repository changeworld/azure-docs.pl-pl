---
title: Konwertuj kod eksperymentu uczenia maszynowego na kod produkcyjny
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak przekonwertować kod eksperymentalny uczenia maszynowego na kod produkcyjny przy użyciu szablonu kodu MLOpsPython.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 1be97f9754733561111cedcf95fe237b7788583e
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122574"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Samouczek: konwertowanie eksperymentalnego kodu ML na kod produkcyjny

Projekt uczenia maszynowego wymaga eksperymentu, w którym są testowane z użyciem narzędzi agile, takich jak Jupyter Notebook przy użyciu rzeczywistych zestawów danych. Gdy model jest gotowy do produkcji, kod modelu powinien być umieszczony w repozytorium kodu produkcyjnego. W niektórych przypadkach kod modelu musi być konwertowany do skryptów języka Python, które mają zostać umieszczone w repozytorium kodu produkcyjnego. Ten samouczek obejmuje zalecane podejście do eksportowania kodu eksperymentu do skryptów języka Python.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Czysty nieistotny kod
> * Refaktoryzacja Jupyter Notebook kodu do funkcji
> * Tworzenie skryptów języka Python dla zadań pokrewnych
> * Utwórz testy jednostkowe

## <a name="prerequisites"></a>Wymagania wstępne

- Generowanie [szablonu MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) i korzystanie z notesów `experimentation/Diabetes Ridge Regression Training.ipynb` i `experimentation/Diabetes Ridge Regression Scoring.ipynb`. Te notesy są używane jako przykład konwersji z eksperymentów do środowiska produkcyjnego.
- Zainstaluj nbconvert. Postępuj zgodnie z instrukcjami dotyczącymi instalacji w sekcji __Instalowanie nbconvert__ na stronie [instalacji](https://nbconvert.readthedocs.io/en/latest/install.html) .

## <a name="remove-all-nonessential-code"></a>Usuń cały nieistotny kod

Kod zapisany podczas eksperymentowania jest przeznaczony wyłącznie do celów badawczych. W związku z tym pierwszy krok konwersji kodu doświadczalnego na kod produkcyjny polega na usunięciu tego nieistotnego kodu. Usunięcie nieistotnego kodu spowoduje również zwiększenie utrzymania kodu. W tej sekcji usuniemy kod z notesu `experimentation/Diabetes Ridge Regression Training.ipynb`. Instrukcje drukowania kształtu `X` i `y`, a komórka wywołująca `features.describe` jest tylko na potrzeby eksploracji danych i można ją usunąć. Po usunięciu nieistotnego kodu `experimentation/Diabetes Ridge Regression Training.ipynb` powinien wyglądać podobnie do poniższego kodu bez promocji:

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

## <a name="refactor-code-into-functions"></a>Refaktoryzacja kodu do funkcji

Po drugie kod Jupyter musi zostać oddzielony do funkcji. Refaktoryzacja kodu do funkcji sprawia, że testy jednostkowe są łatwiejsze i zwiększają łatwość obsługi kodu. W tej sekcji zostanie Refaktoryzacja:
- Notes uczenia regresji pierścieniowej cukrzycą (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Notes oceniania regresji pierścieniowej cukrzycą (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refaktoryzacja cukrzycą — Notes szkolenia pierścieniowego do funkcji
W `experimentation/Diabetes Ridge Regression Training.ipynb`wykonaj następujące czynności:

1. Utwórz funkcję o nazwie `train_model`, która pobiera parametry `data` i `alpha` i zwraca model. 
1. Skopiuj kod w obszarze nagłówki "model uczenia w zestawie szkoleniowym" i "Zweryfikuj model w zestawie walidacji" w funkcji `train_model`.

Funkcja `train_model` powinna wyglądać podobnie do następującego kodu:

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

Po utworzeniu funkcji `train_model` Zastąp kod w obszarze nagłówki "model uczenia w zestawie szkoleniowym" i "Weryfikuj model w zestawie walidacji" z następującą instrukcją:

```python
reg = train_model(data, alpha)
```

Poprzednia instrukcja wywołuje funkcję `train_model`, przekazując parametry `data` i `alpha` i zwraca model.

W `experimentation/Diabetes Ridge Regression Training.ipynb`wykonaj następujące czynności:

1. Utwórz nową funkcję o nazwie `main`, która nie przyjmuje parametrów i nie zwraca żadnej wartości.
1. Skopiuj kod w obszarze nagłówki "Ładowanie danych", "Podziel dane na zestawy szkoleniowe i walidacje" i "Zapisz model" w funkcji `main`.
1. Skopiuj nowo utworzone wywołanie do `train_model` funkcji `main`.

Funkcja `main` powinna wyglądać podobnie do następującego kodu:

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

Po utworzeniu funkcji `main` Zastąp cały kod pod nagłówkami "Ładowanie danych", "Podziel dane na zestawy szkoleniowe i walidacje" i "Zapisz model" wraz z nowo utworzonym wywołaniem, aby `train_model` z następującą instrukcją:

```python
main()
```

Po refaktoryzacji `experimentation/Diabetes Ridge Regression Training.ipynb` powinien wyglądać podobnie do następującego kodu bez promocji:

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

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refaktoryzacja Cukrzycąa w notesie do funkcji
W `experimentation/Diabetes Ridge Regression Scoring.ipynb`wykonaj następujące czynności:

1. Utwórz nową funkcję o nazwie `init`, która nie przyjmuje parametrów i nie zwraca niczego.
1. Skopiuj kod w nagłówku "Ładowanie modelu" do funkcji `init`.

Funkcja `init` powinna wyglądać podobnie do następującego kodu:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Po utworzeniu funkcji `init` Zastąp cały kod pod nagłówkiem "Ładowanie modelu" pojedynczym wywołaniem do `init` w następujący sposób:

```python
init()
```

W `experimentation/Diabetes Ridge Regression Scoring.ipynb`wykonaj następujące czynności:

1. Utwórz nową funkcję o nazwie `run`, która pobiera `raw_data` i `request_headers` jako parametry i zwraca słownik wyników w następujący sposób:

    ```python
    {"result": result.tolist()}
    ```

1. Skopiuj kod w nagłówkach "przygotowanie danych" i "dane oceny" do funkcji `run`.

    Funkcja `run` powinna wyglądać podobnie do następującego kodu (należy pamiętać, aby usunąć instrukcje ustawiające zmienne `raw_data` i `request_headers`, które będą używane później podczas wywoływania funkcji `run`):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Po utworzeniu funkcji `run` Zastąp cały kod w nagłówkach "przygotowanie danych" i "dane oceny" następującym kodem:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```
Poprzedni kod ustawia zmienne `raw_data` i `request_header`, wywołuje funkcję `run` z `raw_data` i `request_header`i drukuje przewidywania.

Po refaktoryzacji `experimentation/Diabetes Ridge Regression Scoring.ipynb` powinien wyglądać podobnie do następującego kodu bez promocji:

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
Po trzecie, powiązane funkcje muszą zostać scalone z plikami języka Python, aby lepiej ułatwić ponowne użycie kodu. W tej sekcji utworzysz pliki języka Python dla następujących notesów:
- Notes uczenia regresji pierścieniowej cukrzycą (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Notes oceniania regresji pierścieniowej cukrzycą (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Utwórz plik w języku Python dla notesu szkoleniowego dla regresji cukrzycą
Przekonwertuj Notes na skrypt wykonywalny, uruchamiając następującą instrukcję w wierszu polecenia, który używa pakietu nbconvert i ścieżki `experimentation/Diabetes Ridge Regression Training.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Po przekonwertowaniu notesu na `train.py`Usuń wszystkie komentarze. Plik `train.py` powinien wyglądać podobnie do następującego kodu:

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

Plik `train.py` znaleziony w katalogu `diabetes_regression/training` w repozytorium MLOpsPython obsługuje argumenty wiersza polecenia (mianowicie `build_id`, `model_name`i `alpha`). Obsługę argumentów wiersza polecenia można dodać do pliku `train.py`, aby umożliwić obsługę dynamicznych nazw modeli i `alpha` wartości, ale nie jest to konieczne do pomyślnego wykonania kodu.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Utwórz plik Python dla notesu oceniania regresji pierścieniowej cukrzycą
Przekonwertować Notes do skryptu wykonywalnego, uruchamiając następującą instrukcję w wierszu polecenia, który używa pakietu nbconvert i ścieżki `experimentation/Diabetes Ridge Regression Scoring.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Po przekonwertowaniu notesu na `score.py`Usuń wszystkie komentarze. Plik `score.py` powinien wyglądać podobnie do następującego kodu:

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

Funkcja `train_model` wymaga modyfikacji w celu utworzenia wystąpienia modelu zmiennej globalnej, aby była widoczna w całym skrypcie. Dodaj następującą instrukcję na początku funkcji `init`:

```python
global model
```

Po dodaniu poprzedniej instrukcji funkcja `init` powinna wyglądać podobnie do następującego kodu:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Utwórz testy jednostkowe dla każdego pliku języka Python
Czwarte, testy jednostkowe muszą zostać utworzone dla każdego pliku języka Python, co sprawia, że kod jest bardziej niezawodny i łatwiejszy w obsłudze. W tej sekcji utworzysz test jednostkowy dla jednej z funkcji w `train.py`.

`train.py` zawiera dwie funkcje: `train_model` i `main`. Każda funkcja wymaga testu jednostkowego, ale utworzymy tylko jeden test jednostkowy dla funkcji `train_model` przy użyciu platformy Pytest w tym samouczku.  Pytest nie jest jedyną strukturą testów jednostkowych w języku Python, ale jest jednym z najczęściej używanych. Aby uzyskać więcej informacji, odwiedź stronę [Pytest](https://pytest.org).

Test jednostkowy zwykle zawiera trzy główne akcje:
- Rozmieść obiekt — tworzenie i Konfigurowanie niezbędnych obiektów
- Działanie na obiekcie
- Potwierdź, co jest oczekiwane

Typowy warunek dla `train_model` jest `data` i zostanie przeniesiona wartość `alpha`. Oczekiwany wynik polega na tym, że funkcje `Ridge.train` i `Ridge.predict` powinny być wywoływane. Ponieważ metody uczenia maszynowego w usłudze Machine Learning często nie są wykonywane szybko, wywołanie `Ridge.train` zostanie zamakietne. Ponieważ zwracana wartość `Ridge.train` jest obiektem imitacji, zanotujemy również `Ridge.predict`. Test jednostkowy dla `train_model` testowania przekazania `data` i wartości `alpha` z oczekiwanym wynikiem `Ridge.train` i `Ridge.predict` funkcji wywoływanych przy użyciu makietowania, a platforma Pytest powinna wyglądać podobnie do następującego kodu:

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
        mock_ridge_predict = mocker.patch(Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>Używanie własnego modelu z szablonem kodu MLOpsPython
Jeśli wykonano kroki opisane w tym przewodniku, będziesz mieć zestaw skryptów, które są skorelowane ze skryptami szkolenia/oceny/testu dostępnymi w repozytorium MLOpsPython.  Zgodnie ze strukturą wymienioną powyżej, następujące kroki zależą od tego, co jest potrzebne do korzystania z tych plików dla własnego projektu uczenia maszynowego:  

1.  Postępuj zgodnie z przewodnikiem Wprowadzenie
2.  Zastąp kod szkoleniowy
3.  Zastąp kod wyniku
4.  Zaktualizuj kod oceny

### <a name="follow-the-getting-started-guide"></a>Postępuj zgodnie z przewodnikiem Wprowadzenie
Zgodnie z przewodnikiem wprowadzającym konieczne jest posiadanie infrastruktury pomocniczej i potoków do wykonywania MLOpsPython.  Zalecamy wdrożenie kodu MLOpsPython jako-jest przed umieszczeniem w własnym kodzie, aby upewnić się, że struktura i potok działa prawidłowo.  Warto również zapoznać się z strukturą kodu repozytorium.

### <a name="replace-training-code"></a>Zastąp kod szkoleniowy
Zastępowanie kodu używanego do uczenia modelu oraz usuwanie lub zastępowanie odpowiednich testów jednostkowych jest wymagane do działania rozwiązania przy użyciu własnego kodu.  Wykonaj następujące kroki:

1. Zastąp `diabetes_regression\training\train.py`. Ten skrypt pociąga za model lokalnie lub na platformie Azure ML COMPUTE.
1. Usuń lub Zamień testy jednostkowe szkoleniowe Znalezione w `tests/unit/code_test.py`

### <a name="replace-score-code"></a>Zastąp kod oceny
Aby model zapewniał możliwości wnioskowania w czasie rzeczywistym, należy zastąpić kod wyniku. Szablon MLOpsPython korzysta z kodu wynikowego do wdrożenia modelu w celu przeprowadzania oceny w czasie rzeczywistym na ACI, AKS lub aplikacji sieci Web.  Jeśli chcesz zachować ocenianie, Zastąp `diabetes_regression/scoring/score.py`.

### <a name="update-evaluation-code"></a>Zaktualizuj kod oceny
Szablon MLOpsPython używa skryptu evaluate_model do porównania wydajności nowo szkolonego modelu i bieżącego modelu produkcji na podstawie średniego błędu kwadratowego. Jeśli wydajność nowo przeszkolonego modelu jest lepsza niż bieżący model produkcyjny, potoki są kontynuowane. W przeciwnym razie potoki są zatrzymane. Aby zachować ocenę, Zastąp wszystkie wystąpienia `mse` w `diabetes_regression/evaluate/evaluate_model.py` z wybraną metryką. 

Aby pozbyć się oceny, należy ustawić zmienną potoku DevOps `RUN_EVALUATION` w `.pipelines\diabetes_regression-variables` do `false`.

## <a name="next-steps"></a>Następne kroki

Teraz, po zrozumieniu sposobu konwersji z eksperymentu na kod produkcyjny, Skorzystaj z poniższych linków, aby dowiedzieć się, jak monitorować uruchomienia eksperymentów i modele wdrożone jako usługi sieci Web:

> [!div class="nextstepaction"]
> [Monitoruj przebiegi eksperymentów w usłudze Azure ml i metryki](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [Monitoruj i Zbieraj dane z punktów końcowych usługi sieci Web ml](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insight)
