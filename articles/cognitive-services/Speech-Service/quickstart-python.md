---
title: 'Szybki start: rozpoznawanie mowy, Python — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Korzystając z tego przewodnika, możesz utworzyć aplikację konsolową zamieniającą mowę na tekst przy użyciu zestawu SDK usługi Mowa dla języka Python. Następnie za pomocą mikrofonu komputera będzie możliwa transkrypcja mowy na tekst w czasie rzeczywistym.
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: chlandsi
ms.openlocfilehash: 1c7287b919c46ead4f961aff769da5c8bf68cc6f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226574"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>Szybki start: rozpoznawanie mowy przy użyciu zestawu SDK usługi Mowa dla języka Python

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule pokazano, jak korzystać z usługi rozpoznawania mowy za pośrednictwem zestawu Speech SDK dla języka Python. Przedstawia on, jak rozpoznawać mowę w danych wejściowych z mikrofonu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy zapoznaj się z poniższą listą wymagań wstępnych:

* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
* [Język Python 3.5 (64-bitowy)](https://www.python.org/downloads/) lub nowsza wersja.
* Pakiet zestawu Speech SDK dla języka Python jest dostępny dla systemu Windows (x64), komputerów Mac (macOS X w wersji 10.12 lub nowszej) i systemu Linux (Ubuntu 16.04 lub 18.04 na platformie x64).
* W systemie Ubuntu uruchom następujące polecenia, aby zainstalować wymagane pakiety:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

* W systemie Windows potrzebny jest też składnik [Microsoft Visual C++ Redistributable for Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) dla danej platformy.

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Pakiet zestawu Speech SDK usługi Cognitive Services dla języka Python możesz zainstalować z witryny [PyPI](https://pypi.org/) przy użyciu tego polecenia w wierszu polecenia:

```sh
pip install azure-cognitiveservices-speech
```

Bieżąca wersja zestawu Speech SDK usługi Cognitive Services to `1.2.0`.

## <a name="support-and-updates"></a>Pomoc techniczna i aktualizacje

Aktualizacje pakietu zestawu Speech SDK dla języka Python będą rozpowszechniane za pośrednictwem witryny PyPI i ogłaszane na stronie [informacji o wersji](./releasenotes.md).
Gdy będzie dostępna nowa wersja, możesz przeprowadzić aktualizację do niej, używając polecenia `pip install --upgrade azure-cognitiveservices-speech`.
Możesz ustalić, która wersja jest obecnie zainstalowana, sprawdzając zmienną `azure.cognitiveservices.speech.__version__`.

W przypadku problemów lub braku funkcji zapoznaj się z naszą [stroną pomocy technicznej](./support.md).

## <a name="create-a-python-application-using-the-speech-sdk"></a>Tworzenie aplikacji języka Python za pomocą zestawu Speech SDK

### <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Możesz skopiować [kod](#quickstart-code) z tego przewodnika Szybki start do pliku źródłowego `quickstart.py` i uruchomić go w środowisku IDE lub w konsoli

```sh
python quickstart.py
```

lub możesz pobrać ten samouczek Szybki start jako notes programu [Jupyter](https://jupyter.org) z [repozytorium przykładów mowy usług Cognitive Services](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) i uruchomić go jako notes.

### <a name="sample-code"></a>Przykładowy kod

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalowanie i używanie zestawu SDK usługi Mowa za pomocą programu Visual Studio Code

1. [Pobierz](https://www.python.org/downloads/) i zainstaluj 64-bitową wersję (3.5 lub nowszą) języka Python na komputerze.
1. [Pobierz](https://code.visualstudio.com/Download) i zainstaluj program Visual Studio Code.
1. Otwórz program Visual Studio Code i zainstaluj rozszerzenie języka Python, wybierając pozycję **File** > **Preferences** > **Extensions** (Plik > Preferencje > Rozszerzenia) z menu i wyszukując hasło „Python”.
   ![Instalowanie rozszerzenia języka Python](media/sdk/qs-python-vscode-python-extension.png)
1. Utwórz folder do przechowywania projektu, na przykład za pomocą Eksploratora Windows.
1. W programie Visual Studio Code kliknij ikonę **File** (Plik), a następnie otwórz utworzony folder.
   ![Otwieranie folderu](media/sdk/qs-python-vscode-python-open-folder.png)
1. Utwórz nowy plik źródłowy języka Python `speechsdk.py`, klikając ikonę nowego pliku.
   ![Tworzenie pliku](media/sdk/qs-python-vscode-python-newfile.png)
1. Skopiuj, wklej i zapisz [kod języka Python](#quickstart-code) w nowo utworzonym pliku.
1. Wstaw informacje dotyczące subskrypcji usługi rozpoznawania mowy.
1. Jeśli został już wybrany interpreter języka Python, zostanie wyświetlony po lewej stronie paska stanu u dołu okna.
   W przeciwnym razie możesz wyświetlić listę dostępnych interpreterów języka Python, otwierając **paletę poleceń** (`Ctrl+Shift+P`), wpisując **Python: Select Interpreter** (Python: wybierz interpreter) i wybierając odpowiedni interpreter.
1. Jeśli pakiet zestawu Speech SDK dla języka Python nie jest jeszcze zainstalowany dla wybranego interpretera języka Python, można to łatwo zrobić z poziomu programu Visual Studio Code.
   Aby zainstalować pakiet zestawu SDK usługi Mowa, otwórz terminal, ponownie wyświetlając paletę poleceń (`Ctrl+Shift+P`) i wpisując **Terminal: Create New Integrated Terminal** (Terminal: utwórz nowy zintegrowany terminal).
   W wyświetlonym terminalu wprowadź polecenie `python -m pip install azure-cognitiveservices-speech` lub polecenie odpowiednie dla swojego systemu.
1. Aby uruchomić przykładowy kod, kliknij prawym przyciskiem myszy w dowolnym miejscu w edytorze, a następnie wybierz pozycję **Run Python File in Terminal** (Uruchom plik języka Python w terminalu).
   Po otrzymaniu monitu powiedz kilka słów. Transkrypcja tekstu powinna zostać wyświetlona po chwili.
   ![Uruchamianie przykładu](media/sdk/qs-python-vscode-python-run.png)

Jeśli masz problemy z wykonaniem tych instrukcji, zapoznaj się z obszerniejszym [samouczkiem języka Python dla programu Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla języka Python w serwisie GitHub](https://aka.ms/csspeech/samples)
