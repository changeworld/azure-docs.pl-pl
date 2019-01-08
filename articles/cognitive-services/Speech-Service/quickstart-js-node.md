---
title: 'Szybki start: Rozpoznawanie mowy w języku JavaScript na platformie Node.js przy użyciu zestawu SDK usługi Mowa'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w języku JavaScript na platformie Node.js przy użyciu zestawu SDK usługi Mowa
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: fmegen
ms.openlocfilehash: 35652b169067bc545fa0d1fcc977bbaee79ec3aa
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724437"
---
# <a name="quickstart-recognize-speech-in-javascript-in-nodejs-using-the-speech-service-sdk"></a>Szybki start: Rozpoznawanie mowy w języku JavaScript na platformie Node.js przy użyciu zestawu SDK usługi Mowa

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Z tego artykułu dowiesz się, jak utworzyć projekt platformy Node.js przy użyciu powiązania języka JavaScript zestawu Speech SDK z usługi Cognitive Services, aby wykonać transkrypcję mowy na tekst.
Aplikacja jest oparta na [zestawie Speech SDK z usługi Microsoft Cognitive Services](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
* Bieżąca wersja platformy [Node.js](https://nodejs.org).

## <a name="create-a-new-project-folder"></a>Tworzenie nowego folderu projektu

Utwórz nowy, pusty folder i zainicjuj go jako nowy projekt języka JavaScript i platformy Node.js.

```sh
npm init -f
```

Spowoduje to zainicjowanie plików package.json przy użyciu wartości domyślnych. Prawdopodobnie będziesz później edytować ten plik.

## <a name="install-the-speech-sdk-for-javascript-into-that-folder"></a>Instalowanie zestawu Speech SDK dla języka JavaScript w tym folderze

Dodaj zestaw Speech SDK do projektu platformy Node.js za pomocą polecenia `npm install microsoft-cognitiveservices-speech-sdk`.

Spowoduje to pobranie i zainstalowanie najnowszej wersji zestawu Speech SDK i wymaganych wstępnie elementów z witryny npmjs. Zestaw SDK zostanie zainstalowany w katalogu `node_modules` w folderze projektu.

## <a name="using-the-speech-sdk"></a>Korzystanie z zestawu Speech SDK

Utwórz w folderze nowy plik o nazwie `index.js`, a następnie otwórz ten plik za pomocą edytora tekstów.

> [!NOTE]
> Na platformie Node.js zestaw Speech SDK nie obsługuje mikrofonu ani typu danych File. Oba te elementy są obsługiwane tylko w przeglądarkach. Zamiast tego użyj interfejsu Stream do zestawu Speech SDK, za pośrednictwem funkcji `AudioInputStream.createPushStream()` lub `AudioInputStream.createPullStream()`.

W tym przykładzie zostanie użyty interfejs `PushAudioInputStream`.

Dodaj następujący kod JavaScript:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="running-the-sample-from-command-line"></a>Uruchamianie przykładu z wiersza polecenia

Aby uruchomić aplikację, dostosuj wartości `YourSubscriptionKey`, `YourServiceRegion` i `YourAudioFile.wav` do swojej konfiguracji. Następnie możesz ją uruchomić, wywołując następujące polecenie:

```sh
node index.js
```

Spowoduje to rozpoczęcie rozpoznawania przy użyciu podanej nazwy pliku i wyświetlenie danych wyjściowych w konsoli.

Oto przykładowe dane wyjściowe uruchomienia pliku `index.js` po zaktualizowaniu klucza subskrypcji i użyciu pliku `whatstheweatherlike.wav`.

```json
SpeechRecognitionResult {
  "privResultId": "9E30EEBD41AC4571BB77CF9164441F46",
  "privReason": 3,
  "privText": "What's the weather like?",
  "privDuration": 15900000,
  "privOffset": 300000,
  "privErrorDetails": null,
  "privJson": {
    "RecognitionStatus": "Success",
    "DisplayText": "What's the weather like?",
    "Offset": 300000,
    "Duration": 15900000
  },
  "privProperties": null
}
```

## <a name="running-the-sample-from-visual-studio-code"></a>Uruchamianie przykładu z programu Visual Studio Code

Przykład możesz też uruchomić z programu Visual Studio Code. Wykonaj następujące kroki, aby zainstalować, otworzyć i uruchomić ten przewodnik Szybki start:

1. Uruchom program Visual Studio Code i kliknij pozycję „Open Folder” (Otwórz folder), a następnie przejdź do folderu przewodnika Szybki start

   ![Zrzut ekranu przedstawiający otwieranie folderu](media/sdk/qs-js-node-01-open_project.png)

1. Otwórz terminal w programie Visual Studio Code

   ![Zrzut ekranu przedstawiający okno terminalu](media/sdk/qs-js-node-02_open_terminal.png)

1. Uruchom narzędzie npm, aby zainstalować zależności

   ![Zrzut ekranu przedstawiający instalację przy użyciu narzędzia npm](media/sdk/qs-js-node-03-npm_install.png)

1. Teraz możesz otworzyć plik `index.js` i ustawić punkt przerwania

   ![Zrzut ekranu przedstawiający plik index.js z punktem przerwania w wierszu 16](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Aby rozpocząć debugowanie, naciśnij klawisz F5 lub wybierz pozycję Debug / Start Debugging (Debuguj / Rozpocznij debugowanie) z menu

   ![Zrzut ekranu przedstawiający menu debugowania](media/sdk/qs-js-node-05-start_debugging.png)

1. Po osiągnięciu punktu przerwania możesz sprawdzić stos wywołań i zmienne

   ![Zrzut ekranu przedstawiający debuger](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. Wszystkie dane wyjściowe będą wyświetlane w oknie konsoli debugowania

   ![Zrzut ekranu konsoli debugowania](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla platformy Node.js w serwisie GitHub](https://aka.ms/csspeech/samples)
