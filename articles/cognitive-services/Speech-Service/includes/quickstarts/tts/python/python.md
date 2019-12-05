---
title: 'Szybki Start: synteza mowy, środowisko Python-Speech'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić funkcję syntezy mowy w języku Python przy użyciu zestawu Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/14/2019
ms.author: yulili
ms.openlocfilehash: 990e795d0331df41ff098ed498508d6ecfd58f0e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818113"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Konfigurowanie środowiska deweloperskiego](../../../../quickstarts/setup-platform.md)
> * [Tworzenie pustego przykładowego projektu](../../../../quickstarts/create-project.md)
```

## Support and updates

Updates to the Speech SDK Python package are distributed via PyPI and announced in the [Release notes](~/articles/cognitive-services/Speech-Service/releasenotes.md).
If a new version is available, you can update to it with the command `pip install --upgrade azure-cognitiveservices-speech`.
Check which version is currently installed by inspecting the `azure.cognitiveservices.speech.__version__` variable.

If you have a problem, or you're missing a feature, see [Support and help options](~/articles/cognitive-services/Speech-Service/support.md).

## Create a Python application that uses the Speech SDK

### Run the sample

You can copy the [sample code](#sample-code) from this quickstart to a source file `quickstart.py` and run it in your IDE or in the console:

```sh
python quickstart.py
```

Ewentualnie możesz pobrać ten samouczek Szybki start jako notes programu [Jupyter](https://jupyter.org) z [repozytorium przykładów zestawu Speech SDK](https://aka.ms/csspeech/samples) i uruchomić go jako notes.

### <a name="sample-code"></a>Przykładowy kod

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/text-to-speech/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalowanie i używanie zestawu SDK usługi Mowa za pomocą programu Visual Studio Code

1. Pobierz i zainstaluj na komputerze 64-bitową wersję 3.5 lub nowszą środowiska [Python](https://www.python.org/downloads/).
1. Pobierz i zainstaluj program [Visual Studio Code](https://code.visualstudio.com/Download).
1. Otwórz program Visual Studio Code i zainstaluj rozszerzenie języka Python. Wybierz z menu pozycję **Plik** > **Preferencje** > **Rozszerzenia**. Wyszukaj pozycję **Python**.

   ![Instalowanie rozszerzenia języka Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Utwórz folder do przechowywania projektu. Na przykład za pomocą Eksploratora Windows.
1. W programie Visual Studio Code wybierz ikonę **Plik**. Następnie otwórz utworzony przez siebie folder.

   ![Otwieranie folderu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Utwórz nowy plik źródłowy języka Python `speechsdk.py`, wybierając ikonę nowego pliku.

   ![Tworzenie pliku](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Skopiuj, wklej i zapisz [kod języka Python](#sample-code) w nowo utworzonym pliku.
1. Wstaw informacje dotyczące subskrypcji usługi rozpoznawania mowy.
1. Jeśli został wybrany interpreter języka Python, zostanie wyświetlony po lewej stronie paska stanu u dołu okna.
   W przeciwnym razie możesz wyświetlić listę dostępnych interpreterów języka Python. Otwórz paletę poleceń (Ctrl + Shift + P) i wprowadź **Python: SELECT interpreter**. Wybierz odpowiedni.
1. Możesz zainstalować pakiet języka Python zestawu Speech SDK z poziomu programu Visual Studio Code. Zrób to, jeśli nie został on jeszcze zainstalowany dla wybranego interpretera języka Python.
   Aby zainstalować pakiet zestawu Speech SDK, otwórz terminal. Ponownie wywołaj paletę poleceń (Ctrl + Shift + P) i wprowadź **Terminal: Utwórz nowy, zintegrowany terminal**.
   W wyświetlonym terminalu wprowadź polecenie `python -m pip install azure-cognitiveservices-speech` lub polecenie odpowiednie dla swojego systemu.
1. Aby uruchomić przykładowy kod, kliknij prawym przyciskiem myszy w dowolnym miejscu w edytorze. Wybierz pozycję **Uruchom plik języka Python w terminalu**.
   Po wyświetleniu monitu wpisz tekst. Dźwięk z syntezy jest odtwarzany wkrótce.

   ![Uruchamianie przykładu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run-tts.png)

Jeśli masz problemy z wykonaniem tych instrukcji, zapoznaj się z obszerniejszym [samouczkiem języka Python dla programu Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zobacz także

- [Tworzenie niestandardowego głosu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Rejestruj niestandardowe przykłady głosu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
