---
title: 'Szybki Start: konfiguracja zestawu Speech SDK dla platformy Python — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika, aby skonfigurować platformę do korzystania z języka Python z zestawem SDK usługi Speech Service.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/09/2019
ms.author: erhopf
ms.openlocfilehash: 218d0dca43d126c1318c273603a4980697c465af
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751804"
---
W tym przewodniku przedstawiono sposób instalowania [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) dla języka Python.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

- Pakiet zestawu Speech SDK dla języka Python jest dostępny dla tych systemów operacyjnych:
  - Windows: x64 i x86
  - Mac: macOS X w wersji 10,12 lub nowszej
  - Linux: Ubuntu 16,04, Ubuntu 18,04, Debian 9 w x64

## <a name="prerequisites"></a>Wymagania wstępne

- Obsługiwane platformy Linux wymagają zainstalowanych niektórych bibliotek (`libssl` do obsługi protokołu Secure Sockets Layer i `libasound2` do obsługi dźwięku). Zapoznaj się z dystrybucją poniżej, aby zapoznać się z poleceniami wymaganymi do zainstalowania odpowiednich wersji tych bibliotek.

  - W systemie Ubuntu Uruchom następujące polecenia, aby zainstalować wymagane pakiety:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - W systemie Debian 9 Uruchom następujące polecenia, aby zainstalować wymagane pakiety:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- W systemie Windows potrzebna jest wersja [Microsoft Visual C++ redystrybucyjna dla programu Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) . Należy pamiętać, że zainstalowanie tego programu po raz pierwszy może wymagać ponownego uruchomienia systemu Windows przed kontynuowaniem pracy z tym przewodnikiem.
- Na koniec należy potrzebować języka [Python 3,5, 3,6 lub 3,7](https://www.python.org/downloads/). Aby sprawdzić instalację, Otwórz wiersz polecenia i wpisz polecenie `python --version` i sprawdź wynik. Jeśli jest zainstalowana prawidłowo, otrzymasz odpowiedź "Python 3.5.1" lub podobną.

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Instalowanie zestawu Speech SDK przy użyciu Visual Studio Code

1. Pobierz i zainstaluj najnowszą obsługiwaną wersję języka [Python](https://www.python.org/downloads/) dla danej platformy, 3,5 lub nowszej.
   - Użytkownicy systemu Windows pamiętaj o wybraniu opcji "Dodaj język Python do ścieżki" podczas procesu instalacji.
1. Pobierz i zainstaluj program [Visual Studio Code](https://code.visualstudio.com/Download).
1. Otwórz program Visual Studio Code i zainstaluj rozszerzenie języka Python. Wybierz z menu pozycję **Plik** > **Preferencje** > **Rozszerzenia**. Wyszukaj środowisko **Python** i kliknij przycisk **Instaluj**.

   ![Instalowanie rozszerzenia języka Python](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Ponadto z poziomu Visual Studio Code Zainstaluj pakiet języka Python zestawu mowy SDK z poziomu wiersza polecenia zintegrowanego:
   1. Otwórz Terminal (z menu rozwijanego, **terminalu** > **nowym terminalu**)
   1. W otwartym terminalu wprowadź polecenie `python -m pip install azure-cognitiveservices-speech`

Jest to gotowe do rozpoczęcia kodowania do zestawu Speech SDK w języku Python i można przejść do [kolejnych kroków](#next-steps) poniżej. Jeśli jesteś nowym do Visual Studio Code, zapoznaj się z bardziej obszerną [dokumentacją Visual Studio Code](https://code.visualstudio.com/docs). Aby uzyskać więcej informacji na temat Visual Studio Code i języka Python, zobacz [Przewodnik po Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="install-the-speech-sdk-using-the-command-line"></a>Instalowanie zestawu Speech SDK przy użyciu wiersza polecenia

Jeśli nie używasz Visual Studio Code, następujące polecenie zainstaluje pakiet języka Python z [PyPI](https://pypi.org/) dla zestawu Speech SDK. W przypadku użytkowników Visual Studio Code przejdź do następnej podsekcji.

```sh
pip install azure-cognitiveservices-speech
```

Jeśli pracujesz w usłudze macOS, może być konieczne uruchomienie następującego polecenia, aby uzyskać `pip` powyższym poleceniu:

```sh
python3 -m pip install --upgrade pip
```

Po pomyślnym użyciu `pip` zainstalowania `azure-cognitiveservices-speech`można użyć zestawu Speech SDK przez zaimportowanie przestrzeni nazw do projektów języka Python. Przykład:

```py
import azure.cognitiveservices.speech as speechsdk
```

Jest to bardziej szczegółowe w przykładach kodu wymienionych w [następnych krokach](#next-steps) poniżej.

## <a name="support-and-updates"></a>Pomoc techniczna i aktualizacje

Aktualizacje pakietu zestawu Speech SDK dla języka Python będą rozpowszechniane za pośrednictwem witryny PyPI i ogłaszane w [informacjach o wersji](~/articles/cognitive-services/speech-service/releasenotes.md).
Gdy będzie dostępna nowa wersja, możesz przeprowadzić aktualizację do niej, używając polecenia `pip install --upgrade azure-cognitiveservices-speech`.
Ustal, która wersja jest obecnie zainstalowana, sprawdzając zmienną `azure.cognitiveservices.speech.__version__`.

W przypadku problemu lub braku funkcji zobacz [opcje pomocy i obsługi technicznej](~/articles/cognitive-services/speech-service/support.md).

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list.md)]
