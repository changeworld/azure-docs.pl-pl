---
title: 'Szybki Start: synteza mowy, środowisko Python-Speech'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić funkcję syntezy mowy w języku Python przy użyciu zestawu Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/14/2019
ms.author: yulili
ms.openlocfilehash: fed0dea1a562f5ddea05516125a38c690069f446
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803964"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-python"></a>Szybki Start: syntezowanie mowy przy użyciu zestawu Speech SDK dla języka Python

Przewodniki Szybki Start są również dostępne dla [funkcji rozpoznawania mowy](quickstart-python.md).

W tym artykule przedstawiono sposób korzystania z usług mowy za pomocą zestawu Speech SDK dla języka Python. Ilustruje to sposób, w jaki można przeprowadzić syntezę mowy z tekstu i odtworzyć ją z domyślnym wyjściem audio.

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji platformy Azure dla usługi Speech Services. [Uzyskaj bezpłatnie](get-started.md).
* [Środowisko Python 3.5 lub nowsze](https://www.python.org/downloads/).
* Pakiet zestawu Speech SDK dla języka Python jest dostępny dla tych systemów operacyjnych:
    * Windows 10: x64 i x86.
    * Mac: macOS X w wersji 10.12 lub nowszej.
    * Linux: Ubuntu 16,04, Ubuntu 18,04, Debian 9 w x64.
* W systemie Linux Uruchom następujące polecenia, aby zainstalować wymagane pakiety:

  * W systemie Ubuntu:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  * W programie Debian 9:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

* W systemie Windows potrzebna jest wersja [Microsoft Visual C++ redystrybucyjna dla programu Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) .

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Należy zauważyć, że ten samouczek nie będzie działał z wersją zestawu SDK wcześniejszą niż 1.7.0.

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

Ewentualnie możesz pobrać ten samouczek Szybki start jako notes programu [Jupyter](https://jupyter.org) z [repozytorium przykładów zestawu Speech SDK](https://aka.ms/csspeech/samples) i uruchomić go jako notes.

### <a name="sample-code"></a>Przykładowy kod

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/python/quickstart.py#code)]

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
1. Wstaw informacje o subskrypcji usługi Speech Services.
1. Jeśli został wybrany interpreter języka Python, zostanie wyświetlony po lewej stronie paska stanu u dołu okna.
   W przeciwnym razie możesz wyświetlić listę dostępnych interpreterów języka Python. Otwórz paletę poleceń (Ctrl + Shift + P) i wprowadź **Python: SELECT interpreter**. Wybierz odpowiedni.
1. Możesz zainstalować pakiet języka Python zestawu Speech SDK z poziomu programu Visual Studio Code. Zrób to, jeśli nie został on jeszcze zainstalowany dla wybranego interpretera języka Python.
   Aby zainstalować pakiet zestawu Speech SDK, otwórz terminal. Ponownie wywołaj paletę poleceń (Ctrl + Shift + P) i wprowadź **Terminal: Utwórz nowy, zintegrowany terminal**.
   W wyświetlonym terminalu wprowadź polecenie `python -m pip install azure-cognitiveservices-speech` lub polecenie odpowiednie dla swojego systemu.
1. Aby uruchomić przykładowy kod, kliknij prawym przyciskiem myszy w dowolnym miejscu w edytorze. Wybierz pozycję **Uruchom plik języka Python w terminalu**.
   Po wyświetleniu monitu wpisz tekst. Dźwięk z syntezy jest odtwarzany wkrótce.

   ![Uruchamianie przykładu](media/sdk/qs-python-vscode-python-run-tts.png)

Jeśli masz problemy z wykonaniem tych instrukcji, zapoznaj się z obszerniejszym [samouczkiem języka Python dla programu Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla języka Python w serwisie GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Dostosuj czcionki głosowe](how-to-customize-voice-font.md)
- [Rejestruj przykłady głosu](record-custom-voice-samples.md)
