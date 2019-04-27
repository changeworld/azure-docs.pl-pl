---
title: 'Szybki start: rozpoznawanie mowy, Python — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Korzystając z tego przewodnika, możesz utworzyć aplikację konsolową zamieniającą mowę na tekst przy użyciu zestawu SDK usługi Mowa dla języka Python. Następnie za pomocą mikrofonu komputera będzie możliwa transkrypcja mowy na tekst w czasie rzeczywistym.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: chlandsi
ms.openlocfilehash: 1464d1ca8fcf1ad5a955d69aa76caec9aa568dad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621312"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>Szybki start: rozpoznawanie mowy przy użyciu zestawu SDK usługi Mowa dla języka Python

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule przedstawiono sposób korzystania z usług rozpoznawania mowy, mowy zestawu SDK dla języka Python. Przedstawia on, jak rozpoznawać mowę w danych wejściowych z mikrofonu.

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji platformy Azure dla usług przetwarzania mowy. [Uzyskaj bezpłatnie](get-started.md).
* [Środowisko Python 3.5 lub nowsze](https://www.python.org/downloads/).
* Pakiet zestawu Speech SDK dla języka Python jest dostępny dla tych systemów operacyjnych:
    * Windows: x64 i x86.
    * Mac: macOS X w wersji 10.12 lub nowszej.
    * W systemie Linux: Ubuntu 16.04 lub 18.04 w wersji x64.
* W systemie Ubuntu uruchom następujące polecenia, aby zainstalować wymagane pakiety:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libasound2 wget
  ```

* W systemie Windows potrzebny jest też składnik [Microsoft Visual C++ Redistributable for Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) dla danej platformy.

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

To polecenie instaluje pakiet języka Python z witryny [PyPI](https://pypi.org/) dla zestawu Speech SDK:

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Pomoc techniczna i aktualizacje

Aktualizacje pakietu zestawu Speech SDK dla języka Python będą rozpowszechniane za pośrednictwem witryny PyPI i ogłaszane w [informacjach o wersji](./releasenotes.md).
Gdy będzie dostępna nowa wersja, możesz przeprowadzić aktualizację do niej, używając polecenia `pip install --upgrade azure-cognitiveservices-speech`.
Ustal, która wersja jest obecnie zainstalowana, sprawdzając zmienną `azure.cognitiveservices.speech.__version__`.

W przypadku problemu lub braku funkcji zobacz [opcje pomocy i obsługi technicznej](./support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Tworzenie aplikacji języka Python za pomocą zestawu Speech SDK

### <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Możesz skopiować [kod przykładowy](#sample-code) z tego przewodnika Szybki start do pliku źródłowego `quickstart.py` i uruchomić go w środowisku IDE lub w konsoli:

```sh
python quickstart.py
```

Ewentualnie możesz pobrać ten samouczek Szybki start jako notes programu [Jupyter](https://jupyter.org) z [repozytorium przykładów zestawu Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) i uruchomić go jako notes.

### <a name="sample-code"></a>Przykładowy kod

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalowanie i używanie zestawu SDK usługi Mowa za pomocą programu Visual Studio Code

1. Pobierz i zainstaluj na komputerze 64-bitową wersję 3.5 lub nowszą środowiska [Python](https://www.python.org/downloads/).
1. Pobierz i zainstaluj program [Visual Studio Code](https://code.visualstudio.com/Download).
1. Otwórz program Visual Studio Code i zainstaluj rozszerzenie języka Python. Wybierz z menu pozycję **Plik** > **Preferencje** > **Rozszerzenia**. Wyszukaj pozycję **Python**.

   ![Instalowanie rozszerzenia języka Python](media/sdk/qs-python-vscode-python-extension.png)

1. Utwórz folder do przechowywania projektu. Na przykład za pomocą Eksploratora Windows.
1. W programie Visual Studio Code wybierz ikonę **Plik**. Następnie otwórz utworzony przez siebie folder.

   ![Otwieranie folderu](media/sdk/qs-python-vscode-python-open-folder.png)

1. Utwórz nowy plik źródłowy języka Python `speechsdk.py`, wybierając ikonę nowego pliku.

   ![Tworzenie pliku](media/sdk/qs-python-vscode-python-newfile.png)

1. Skopiuj, wklej i zapisz [kod języka Python](#sample-code) w nowo utworzonym pliku.
1. Wstaw informacje o subskrypcji usług przetwarzania mowy.
1. Jeśli został wybrany interpreter języka Python, zostanie wyświetlony po lewej stronie paska stanu u dołu okna.
   W przeciwnym razie możesz wyświetlić listę dostępnych interpreterów języka Python. Otwórz paletę poleceń (Ctrl+Shift+P) i wprowadź tekst **Python: Select Interpreter** (Python: wybierz interpreter). Wybierz odpowiedni.
1. Możesz zainstalować pakiet języka Python zestawu Speech SDK z poziomu programu Visual Studio Code. Zrób to, jeśli nie został on jeszcze zainstalowany dla wybranego interpretera języka Python.
   Aby zainstalować pakiet zestawu Speech SDK, otwórz terminal. Wyświetl ponownie paletę poleceń (Ctrl+Shift+P), a następnie wprowadź tekst **Terminal: Create New Integrated Terminal** (Terminal: utwórz nowy zintegrowany terminal).
   W wyświetlonym terminalu wprowadź polecenie `python -m pip install azure-cognitiveservices-speech` lub polecenie odpowiednie dla swojego systemu.
1. Aby uruchomić przykładowy kod, kliknij prawym przyciskiem myszy w dowolnym miejscu w edytorze. Wybierz pozycję **Uruchom plik języka Python w terminalu**.
   W odpowiedzi na monit powiedz kilka słów. Po chwili powinna zostać wyświetlona transkrypcja tekstu.

   ![Uruchamianie przykładu](media/sdk/qs-python-vscode-python-run.png)

Jeśli masz problemy z wykonaniem tych instrukcji, zapoznaj się z obszerniejszym [samouczkiem języka Python dla programu Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla języka Python w serwisie GitHub](https://aka.ms/csspeech/samples)
