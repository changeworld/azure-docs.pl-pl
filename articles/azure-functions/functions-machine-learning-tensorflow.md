---
title: Korzystanie z języka Python i TensorFlow do uczenia maszynowego na platformie Azure
description: Użyj Python, TensorFlow i usługi Azure Functions z modelu uczenia maszynowego do klasyfikowania obrazu na podstawie jego zawartości.
author: anthonychu
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: c64d87b2430cc1d733a67bbc1e803590a37b1714
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78190776"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Samouczek: Stosowanie modeli uczenia maszynowego w usłudze Azure Functions za pomocą języka Python i TensorFlow

W tym artykule dowiesz się, jak używać python, TensorFlow i usługi Azure Functions z modelu uczenia maszynowego do klasyfikowania obrazu na podstawie jego zawartości. Ponieważ wszystkie działają lokalnie i nie utworzyć żadnych zasobów platformy Azure w chmurze, nie ma żadnych kosztów, aby zakończyć ten samouczek.

> [!div class="checklist"]
> * Inicjowanie środowiska lokalnego do tworzenia usług Azure Functions w języku Python.
> * Zaimportuj niestandardowy model uczenia maszynowego TensorFlow do aplikacji funkcji.
> * Tworzenie bezserwerowego interfejsu API HTTP w celu sklasyfikowania obrazu jako zawierającego psa lub kota.
> * Korzystanie z interfejsu API z aplikacji sieci web.

## <a name="prerequisites"></a>Wymagania wstępne 

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4](https://www.python.org/downloads/release/python-374/). (Python 3.7.4 i Python 3.6.x są weryfikowane za pomocą usługi Azure Functions; Python 3.8 i nowsze wersje nie są jeszcze obsługiwane.)
- [Podstawowe narzędzia funkcji platformy Azure](functions-run-local.md#install-the-azure-functions-core-tools)
- Edytor kodu, taki jak program [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Sprawdzanie pod warunkiem

1. W oknie terminala lub `func --version` polecenia uruchom, aby sprawdzić, czy podstawowe narzędzia usługi Azure Functions są w wersji 2.7.1846 lub nowszej.
1. Uruchom `python --version` (Linux/ MacOS) lub `py --version` (Windows), aby sprawdzić raporty wersji Pythona 3.7.x.

## <a name="clone-the-tutorial-repository"></a>Klonowanie repozytorium samouczków

1. W oknie terminala lub polecenia sklonuj następujące repozytorium za pomocą gita:

    ```
    git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
    ```

1. Przejdź do folderu i sprawdź jego zawartość.

    ```
    cd functions-python-tensorflow-tutorial
    ```

    - *start* jest folder roboczy dla samouczka.
    - *koniec* jest ostatecznym wynikiem i pełną implementacją dla twojego odniesienia.
    - *zawiera* model uczenia maszynowego i biblioteki pomocnicze.
    - *frontend* to witryna sieci Web, która wywołuje aplikację funkcji.
    
## <a name="create-and-activate-a-python-virtual-environment"></a>Tworzenie i aktywowanie środowiska wirtualnego języka Python

Przejdź do folderu *startowego* i uruchom następujące polecenia, `.venv`aby utworzyć i aktywować środowisko wirtualne o nazwie . Pamiętaj, aby używać języka Python 3.7, który jest obsługiwany przez usługi Azure Functions.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
cd start
```

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Jeśli Python nie zainstalował pakietu venv w dystrybucji systemu Linux, uruchom następujące polecenie:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
cd start
```

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
```

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Wszystkie kolejne polecenia można uruchomić w tym aktywowanym środowisku wirtualnym. (Aby wyjść ze środowiska `deactivate`wirtualnego, uruchom .)


## <a name="create-a-local-functions-project"></a>Tworzenie projektu funkcji lokalnych

W usłudze Azure Functions projekt funkcji jest kontenerem dla co najmniej jednej pojedynczej funkcji, która odpowiada na określony wyzwalacz. Wszystkie funkcje w projekcie współużytkują te same konfiguracje lokalne i hostingowe. W tej sekcji utworzysz projekt funkcji, który zawiera `classify` jedną funkcję standardową o nazwie, która zapewnia punkt końcowy HTTP. Dodaj bardziej szczegółowy kod w dalszej sekcji.

1. W folderze *start* użyj narzędzia Podstawowe usługi Azure Functions, aby zainicjować aplikację funkcji Języka Python:

    ```
    func init --worker-runtime python
    ```

    Po zainicjowaniu folder *startowy* zawiera różne pliki dla projektu, w tym pliki konfiguracji o nazwie [local.settings.json](functions-run-local.md#local-settings-file) i [host.json](functions-host-json.md). Ponieważ *local.settings.json* może zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *gitignore.*

    > [!TIP]
    > Ponieważ projekt funkcji jest powiązany z określonym środowiskom run, wszystkie funkcje w projekcie muszą być zapisywane w tym samym języku.

1. Dodaj funkcję do projektu za pomocą następującego `--name` polecenia, gdzie argument jest `--template` unikatową nazwą funkcji, a argument określa wyzwalacz funkcji. `func new`utworzyć podfolder pasujący do nazwy funkcji zawierającej plik kodu odpowiedni dla wybranego języka projektu i plik konfiguracyjny o nazwie *function.json*.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    To polecenie tworzy folder pasujący do nazwy funkcji, *klasyfikuj*. W tym folderze znajdują się dwa pliki: * \_ \_init\_\_.py*, który zawiera kod funkcji, i *function.json*, który opisuje wyzwalacz funkcji i jej powiązania wejściowe i wyjściowe. Aby uzyskać szczegółowe informacje na temat zawartości tych plików, zobacz [Sprawdzanie zawartości pliku](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#optional-examine-the-file-contents) w przewodniku Szybki start języka Python.


## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

1. Uruchom tę funkcję, uruchamiając lokalnego hosta środowiska uruchomieniowego usługi Azure Functions w folderze *startowym:*

    ```
    func start
    ```
    
1. Gdy zobaczysz `classify` punkt końcowy pojawiają się w danych ```http://localhost:7071/api/classify?name=Azure```wyjściowych, przejdź do adresu URL, . Komunikat "Hello Azure!" powinny pojawić się na wyjściu.

1. Użyj **klawisza Ctrl**-**C,** aby zatrzymać hosta.


## <a name="import-the-tensorflow-model-and-add-helper-code"></a>Importowanie modelu TensorFlow i dodawanie kodu pomocnika

Aby zmodyfikować `classify` funkcję, aby sklasyfikować obraz na podstawie jego zawartości, należy użyć wstępnie utworzonego modelu TensorFlow, który został przeszkolony i wyeksportowany z usługi Azure Custom Vision Service. Model, który znajduje się w folderze *zasobów* próbki sklonowane wcześniej, klasyfikuje obraz na podstawie tego, czy zawiera psa lub kota. Następnie należy dodać kod pomocniczy i zależności do projektu.

> [!TIP]
> Jeśli chcesz utworzyć własny model przy użyciu bezpłatnej warstwy usługi Custom Vision Service, postępuj zgodnie z instrukcjami w [przykładowym repozytorium projektu](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md).

1. W folderze *start uruchom* następujące polecenie, aby skopiować pliki modelu do folderu *klasyfikacji.* Pamiętaj, aby `\*` uwzględnić w poleceniu. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    cp ../resources/model/* classify
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    copy ..\resources\model\* classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\model\* classify
    ```
    
    ---
    
1. Sprawdź, czy folder *klasyfikacji* zawiera pliki o nazwach *model.pb* i *labels.txt*. Jeśli nie, sprawdź, czy polecenie zostało posuń w folderze *startowym.*

1. W folderze *start uruchom* następujące polecenie, aby skopiować plik z kodem pomocnika do folderu *klasyfikacji:*

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    cp ../resources/predict.py classify
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    copy ..\resources\predict.py classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\predict.py classify
    ```
    
    ---

1. Sprawdź, czy folder *klasyfikacji* zawiera teraz plik o nazwie *predict.py*.

1. Otwórz *przycisk start/requirements.txt* w edytorze tekstu i dodaj następujące zależności wymagane przez kod pomocnika:

    ```txt
    tensorflow==1.14
    Pillow
    requests
    ```
    
1. Zapisz *requirements.txt*.

1. Zainstaluj zależności, uruchamiając następujące polecenie w folderze *startowym.* Instalacja może potrwać kilka minut, w tym czasie można kontynuować modyfikowanie funkcji w następnej sekcji.

    ```
    pip install --no-cache-dir -r requirements.txt
    ```
    
    W systemie Windows może wystąpić błąd "Nie można zainstalować pakiety z powodu EnvironmentError: [Errno 2] Brak takiego pliku lub katalogu:" następuje długa ścieżka do pliku, takiego jak *sharded_mutable_dense_hashtable.cpython-37.pyc*. Zazwyczaj ten błąd występuje, ponieważ głębokość ścieżki folderu staje się zbyt długa. W takim przypadku ustaw `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` klucz `1` rejestru, aby włączyć długie ścieżki. Alternatywnie sprawdź, gdzie jest zainstalowany interpreter języka Python. Jeśli ta lokalizacja ma długą ścieżkę, spróbuj ponownie zainstalować w folderze z krótszą ścieżką.

> [!TIP]
> Podczas wywoływania *predict.py* do pierwszego przewidywania, funkcja `_initialize` o nazwie ładuje model TensorFlow z dysku i buforuje go w zmiennych globalnych. To buforowanie przyspiesza kolejne prognozy. Aby uzyskać więcej informacji na temat używania zmiennych globalnych, zapoznaj się z [przewodnikiem dla deweloperów języka Azure Functions Python](functions-reference-python.md#global-variables).

## <a name="update-the-function-to-run-predictions"></a>Aktualizowanie funkcji w celu uruchamiania prognoz

1. Otwórz *classify/\_\_\_\_init .py* w edytorze tekstu i `import` dodaj następujące wiersze po istniejących instrukcjach, aby zaimportować standardową bibliotekę JSON i pomocników *przewidywania:*

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Zastąp całą `main` zawartość funkcji następującym kodem:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="8-19":::

    Ta funkcja odbiera adres URL obrazu `img`w parametrze ciągu zapytania o nazwie . Następnie wywołuje `predict_image_from_url` z biblioteki pomocnika, aby pobrać i sklasyfikować obraz przy użyciu modelu TensorFlow. Następnie funkcja zwraca odpowiedź HTTP z wynikami. 

    > [!IMPORTANT]
    > Ponieważ ten punkt końcowy HTTP jest wywoływany przez stronę sieci `Access-Control-Allow-Origin` web hostowane w innej domenie, odpowiedź zawiera nagłówek, aby spełnić wymagania przeglądarki Cross-Origin Resource Sharing (CORS).
    >
    > W aplikacji produkcyjnej `*` zmień na określone pochodzenie strony sieci web, aby zwiększyć bezpieczeństwo.

1. Zapisz zmiany, a następnie zakładając, że zależności zakończyły instalację, uruchom ponownie lokalnego hosta funkcji za pomocą programu `func start`. Pamiętaj, aby uruchomić hosta w folderze *startowym* z włączonym środowiskiem wirtualnym. W przeciwnym razie rozpocznie się host, ale podczas wywoływania funkcji zostaną wyświetlone błędy.

    ```
    func start
    ```

1. W przeglądarce otwórz następujący adres URL, aby wywołać funkcję z adresem URL obrazu kota i potwierdzić, że zwrócony JSON klasyfikuje obraz jako kat.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
    ```
    
1. Zachowaj hosta uruchomionego, ponieważ używasz go w następnym kroku. 

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>Uruchom lokalny fronton aplikacji sieci Web, aby przetestować funkcję

Aby przetestować wywoływanie punktu końcowego funkcji z innej aplikacji sieci web, istnieje prosta aplikacja w folderze *frontonu* repozytorium.

1. Otwórz nowy terminal lub wiersz polecenia i aktywuj środowisko wirtualne (jak opisano wcześniej w obszarze [Utwórz i aktywuj środowisko wirtualne Języka Python).](#create-and-activate-a-python-virtual-environment)

1. Przejdź do folderu *frontonu* repozytorium.

1. Uruchom serwer HTTP w języku Python:

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash 
    python -m http.server
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. W przeglądarce przejdź `localhost:8000`do , a następnie wprowadź jeden z następujących adresów URL zdjęć w skrzynce tekstowej lub użyj adresu URL dowolnego publicznie dostępnego obrazu.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`
    
1. Wybierz **przycisk Prześlij,** aby wywołać punkt końcowy funkcji, aby sklasyfikować obraz.

    ![Zrzut ekranu przedstawiający gotowy projekt](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

    Jeśli przeglądarka zgłasza błąd podczas przesyłania adresu URL obrazu, sprawdź terminal, w którym uruchamiasz aplikację funkcji. Jeśli zostanie wyświetlony błąd w stylu "Brak modułu znaleziono 'PIL'", być może aplikacja funkcji została uruchomiona w *folderze start* bez uprzedniego aktywowania środowiska wirtualnego utworzonego wcześniej. Jeśli nadal widzisz błędy, `pip install -r requirements.txt` uruchom ponownie z włączonym środowiskiem wirtualnym i poszukaj błędów.

> [!NOTE]
> Model zawsze klasyfikuje zawartość obrazu jako kota lub psa, niezależnie od tego, czy obraz zawiera albo, domyślnie do psa. Obrazy tygrysów i panter, na przykład, zazwyczaj klasyfikują się jako kot, ale obrazy słoni, marchwi lub samolotów klasyfikują się jako pies.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Ponieważ cały ten samouczek działa lokalnie na komputerze, nie ma żadnych zasobów platformy Azure lub usług do czyszczenia.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak skompilować i dostosować punkt końcowy interfejsu HTTP interfejsu API za pomocą usługi Azure Functions, aby klasyfikować obrazy przy użyciu modelu TensorFlow. Można również dowiedzieć się, jak wywołać interfejs API z aplikacji sieci web. Techniki w tym samouczku można użyć do tworzenia interfejsów API dowolnej złożoności, a wszystko to podczas uruchamiania na modelu obliczeniowym bez użycia serwera udostępniane przez usługę Azure Functions.

> [!div class="nextstepaction"]
> [Wdrażanie funkcji w usłudze Azure Functions przy użyciu przewodnika interfejsu wiersza polecenia platformy Azure](./functions-run-local.md#publish)

Zobacz też:

- [Wdrażanie funkcji na platformie Azure przy użyciu programu Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Przewodnik dla deweloperów usługi Azure Functions Python](./functions-reference-python.md)
