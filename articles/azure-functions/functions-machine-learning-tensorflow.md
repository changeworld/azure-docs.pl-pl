---
title: Korzystanie z języka Python i TensorFlow w Azure Functions, aby przeprowadzić wnioskowanie dotyczące uczenia maszynowego
description: W tym samouczku pokazano, jak zastosować modele uczenia maszynowego w programie TensorFlow w Azure Functions
author: anthonychu
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: e7c4e1bbd23d43d5e11ab8b26c3d4e1215b4946b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230503"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Samouczek: stosowanie modeli uczenia maszynowego w Azure Functions przy użyciu języka Python i TensorFlow

W tym artykule pokazano, jak Azure Functions umożliwia korzystanie z języka Python i TensorFlow z modelem uczenia maszynowego w celu klasyfikowania obrazu na podstawie jego zawartości.

Z tego samouczka dowiesz się, jak wykonać następujące czynności: 

> [!div class="checklist"]
> * Inicjowanie środowiska lokalnego na potrzeby opracowywania Azure Functions w języku Python
> * Importowanie niestandardowego modelu uczenia maszynowego TensorFlow do aplikacji funkcji
> * Tworzenie bezserwerowego interfejsu API HTTP w celu przewidywania, czy zdjęcie zawiera pies lub kot
> * Korzystanie z interfejsu API z aplikacji sieci Web

![Zrzut ekranu ukończonego projektu](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne 

Aby utworzyć Azure Functions w języku Python, należy zainstalować kilka narzędzi.

- [Python 3,6](https://www.python.org/downloads/release/python-360/)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Edytor kodu, taki jak program [Visual Studio Code](https://code.visualstudio.com/)

## <a name="clone-the-tutorial-repository"></a>Klonowanie repozytorium samouczka

Aby rozpocząć, Otwórz Terminal i Sklonuj następujące repozytorium za pomocą usługi git:

```console
git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
cd functions-python-tensorflow-tutorial
```

Repozytorium zawiera kilka folderów.

- *Rozpocznij*: to jest folder roboczy samouczka
- *koniec*: jest to końcowy wynik i pełna implementacja dla odwołania
- *zasoby*: zawiera model uczenia maszynowego i biblioteki pomocnika
- *fronton*: Witryna sieci Web, która wywołuje aplikację funkcji

## <a name="create-and-activate-a-python-virtual-environment"></a>Tworzenie i aktywowanie środowiska wirtualnego języka Python

Azure Functions wymaga języka Python 3.6. x. Utworzysz środowisko wirtualne, aby upewnić się, że jest używana wymagana wersja języka Python.

Zmień bieżący katalog roboczy na folder *Start* . Następnie utwórz i aktywuj środowisko wirtualne o nazwie *. venv*. W zależności od instalacji języka Python polecenia służące do tworzenia środowiska wirtualnego języka Python 3,6 mogą się różnić od poniższych instrukcji.

#### <a name="linux-and-macos"></a>Linux i macOS:

```bash
cd start
python3.6 -m venv .venv
source .venv/bin/activate
```

#### <a name="windows"></a>W systemie Windows:

```powershell
cd start
py -3.6 -m venv .venv
.venv\scripts\activate
```

Monit dotyczący terminalu jest teraz poprzedzony `(.venv)`, co oznacza, że środowisko wirtualne zostało pomyślnie uaktywnione. Upewnij się, że `python` w środowisku wirtualnym jest naprawdę Python 3.6. x.

```console
python --version
```

> [!NOTE]
> W pozostałej części tego samouczka uruchamiasz polecenia w środowisku wirtualnym. Jeśli musisz ponownie aktywować środowisko wirtualne w terminalu, wykonaj odpowiednie polecenie Activate dla danego systemu operacyjnego.

## <a name="create-an-azure-functions-project"></a>Tworzenie projektu usługi Azure Functions

W folderze *Start* Użyj Azure Functions Core Tools, aby zainicjować aplikację funkcji języka Python.

```console
func init --worker-runtime python
```

Aplikacja funkcji może zawierać co najmniej jedną Azure Functions. Otwórz folder *Start* w edytorze i przejrzyj jego zawartość.

- [*Local. Settings. JSON*](functions-run-local.md#local-settings-file): zawiera ustawienia aplikacji używane na potrzeby lokalnego tworzenia
- plik [*host. JSON*](functions-host-json.md): zawiera ustawienia dla Azure Functions hosta i rozszerzeń
- [*Requirements. txt*](functions-reference-python.md#package-management): zawiera pakiety języka Python wymagane przez tę aplikację

## <a name="create-an-http-function"></a>Tworzenie funkcji HTTP

Aplikacja wymaga pojedynczego punktu końcowego interfejsu API protokołu HTTP, który pobiera adres URL obrazu jako dane wejściowe i zwraca prognozę, czy obraz zawiera pies lub kot.

W terminalu Użyj Azure Functions Core Tools do tworzenia szkieletu nowej funkcji HTTP o nazwie *klasyfikowanie*.

```console
func new --language python --template HttpTrigger --name classify
```

Tworzony jest nowy folder o nazwie *klasyfikowanie* zawierający dwa pliki.

- *\_\_init\_\_. PR*: plik dla funkcji Main
- *Function. JSON*: plik opisujący wyzwalacz funkcji i jego powiązania wejściowe i wyjściowe

### <a name="run-the-function"></a>Uruchom funkcję

Uruchom aplikację funkcji w terminalu ze aktywowanym środowiskiem wirtualnym języka Python.

```console
func start
```

Otwórz przeglądarkę i przejdź do poniższego adresu URL. Funkcja powinna zostać wykonana i zwrócić *Hello Azure!*

```
http://localhost:7071/api/classify?name=Azure
```

Użyj `Ctrl-C`, aby zatrzymać aplikację funkcji.

## <a name="import-the-tensorflow-model"></a>Importowanie modelu TensorFlow

Będziesz używać wstępnie skompilowanego modelu TensorFlow, który został przeszkolony i wyeksportowany z usługi Azure Custom Vision Service.

> [!NOTE]
> Jeśli chcesz utworzyć własny program za pomocą warstwy Bezpłatna Custom Vision Service, możesz postępować zgodnie z [instrukcjami w repozytorium przykładowego projektu](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md).

Model składa się z dwóch plików w *< REPOSITORY_ROOT > folderze/Resources/model* : *model. PB* i *Label. txt*. Skopiuj je do folderu funkcji *klasyfikowanie* .

#### <a name="linux-and-macos"></a>Linux i macOS:

```bash
cp ../resources/model/* classify
```

#### <a name="windows"></a>W systemie Windows:

```powershell
copy ..\resources\model\* classify
```

Upewnij się, że w powyższym poleceniu uwzględniono \*. Upewnij się, że *klasyfikowanie* zawiera teraz pliki o nazwach *model. PB* i *Label. txt*.

## <a name="add-the-helper-functions-and-dependencies"></a>Dodawanie funkcji i zależności pomocnika

Niektóre funkcje pomocnika na potrzeby przygotowania obrazu wejściowego i tworzenia prognoz przy użyciu TensorFlow znajdują się w pliku o nazwie *Predict.py* w folderze *resources* . Skopiuj ten plik do folderu funkcji *klasyfikowanie* .

#### <a name="linux-and-macos"></a>Linux i macOS:

```bash
cp ../resources/predict.py classify
```

#### <a name="windows"></a>W systemie Windows:

```powershell
copy ..\resources\predict.py classify
```

Upewnij się, że *klasyfikowanie* zawiera teraz plik o nazwie *Predict.py*.

### <a name="install-dependencies"></a>Instalowanie zależności

Biblioteka pomocników ma pewne zależności, które należy zainstalować. Otwórz plik *Start/Requirements. txt* w edytorze i Dodaj do niego następujące zależności.

```txt
tensorflow==1.14
Pillow
requests
```

Zapisz plik.

W terminalu ze aktywowanym środowiskiem wirtualnym Uruchom następujące polecenie w folderze *Start* , aby zainstalować zależności. Ukończenie niektórych kroków instalacyjnych może potrwać kilka minut.

```console
pip install --no-cache-dir -r requirements.txt
```

### <a name="caching-the-model-in-global-variables"></a>Buforowanie modelu w zmiennych globalnych

W edytorze Otwórz *Predict.py* i przyjrzyj się funkcji `_initialize` w górnej części pliku. Należy zauważyć, że model TensorFlow jest ładowany z dysku podczas pierwszego wykonywania funkcji i zapisywania w zmiennych globalnych. Ładowanie z dysku jest pomijane w kolejnych wykonaniach funkcji `_initialize`. Buforowanie modelu w pamięci dzięki tej metodzie przyspiesza późniejsze przewidywania.

Aby uzyskać więcej informacji na temat zmiennych globalnych, zapoznaj się z [przewodnikiem dla deweloperów w języku Python Azure Functions](functions-reference-python.md#global-variables).

## <a name="update-function-to-run-predictions"></a>Funkcja aktualizacji do uruchamiania prognoz

Otwórz przystawkę */\_\_init\_\_. PR* w edytorze. Zaimportuj bibliotekę *predykcyjną* dodaną do tego samego folderu wcześniej. Dodaj następujące instrukcje `import` poniżej innych importów znajdujących się już w pliku.

```python
import json
from .predict import predict_image_from_url
```

Zastąp kod szablonu funkcji następującym.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    image_url = req.params.get('img')
    results = predict_image_from_url(image_url)

    headers = {
        "Content-type": "application/json",
        "Access-Control-Allow-Origin": "*"
    }
    return func.HttpResponse(json.dumps(results), headers = headers)
```

Upewnij się zapisać zmiany.

Ta funkcja odbiera adres URL obrazu w parametrze ciągu zapytania o nazwie `img`. Wywołuje `predict_image_from_url` z biblioteki pomocnika, która pobiera obraz i zwraca prognozę przy użyciu modelu TensorFlow. Funkcja zwraca odpowiedź HTTP z wynikami.

Ponieważ punkt końcowy HTTP jest wywoływany przez stronę sieci Web hostowaną w innej domenie, odpowiedź HTTP zawiera nagłówek `Access-Control-Allow-Origin`, aby spełnić wymagania dotyczące udostępniania zasobów między źródłami (CORS) w przeglądarce.

> [!NOTE]
> W aplikacji produkcyjnej Zmień `*` na konkretną Źródło strony sieci Web, aby zwiększyć bezpieczeństwo.

### <a name="run-the-function-app"></a>Uruchamianie aplikacji funkcji

Upewnij się, że środowisko wirtualne języka Python jest nadal aktywowane i uruchom aplikację funkcji przy użyciu następującego polecenia.

```console
func start
```

W przeglądarce Otwórz ten adres URL, który wywołuje funkcję przy użyciu adresu URL zdjęcia Cat. Upewnij się, że zwracany jest prawidłowy wynik przewidywania.

```
http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
```

Pozostaw uruchomioną aplikację funkcji.

### <a name="run-the-web-app"></a>Uruchamianie aplikacji internetowej

W folderze *frontonu* znajduje się prosta aplikacja internetowa korzystająca z interfejsu API protokołu HTTP w aplikacji funkcji.

Otwórz *oddzielny* Terminal i przejdź do folderu *frontonu* . Uruchom serwer HTTP przy użyciu języka Python 3,6.

#### <a name="linux-and-macos"></a>Linux i macOS:

```bash
cd <FRONT_END_FOLDER>
python3.6 -m http.server
```

#### <a name="windows"></a>W systemie Windows:

```powershell
cd <FRONT_END_FOLDER>
py -3.6  -m http.server
```

W przeglądarce przejdź do adresu URL serwera HTTP, który jest wyświetlany w terminalu. Powinna zostać wyświetlona aplikacja sieci Web. Wprowadź jeden z następujących adresów URL zdjęć do pola tekstowego. Możesz również użyć adresu URL publicznie dostępnego Zdjęcia Cat lub Dog.

- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`

Po kliknięciu przycisku Prześlij aplikacja funkcji jest wywoływana, a na stronie zostanie wyświetlony wynik.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
W całości z tego samouczka działa lokalnie na komputerze, dlatego nie ma żadnych zasobów ani usług platformy Azure do oczyszczenia.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób kompilowania i dostosowywania interfejsu API protokołu HTTP przy użyciu Azure Functions do tworzenia prognoz przy użyciu modelu TensorFlow. Wiesz również, jak wywołać interfejs API z aplikacji sieci Web.

Można użyć technik z tego samouczka, aby utworzyć interfejsy API dowolnej złożoności, a wszystko to uruchomione w modelu obliczeniowym bez serwera udostępnianym przez Azure Functions.

Aby wdrożyć aplikację funkcji na platformie Azure, użyj [Azure Functions Core Tools](./functions-run-local.md#publish) lub [Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).

> [!div class="nextstepaction"]
> [Przewodnik dewelopera w języku Python Azure Functions](./functions-reference-python.md)
