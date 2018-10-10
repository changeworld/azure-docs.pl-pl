---
title: 'Szybki Start: Rozpoznawanie mowy w języku JavaScript w przeglądarce, za pomocą Cognitive Services SDK rozpoznawania mowy'
titleSuffix: Microsoft Cognitive Services
description: Dowiedz się, jak rozpoznawanie mowy w języku JavaScript w przeglądarce, za pomocą Cognitive Services SDK rozpoznawania mowy
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: Speech
ms.topic: article
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: 75dcda643741e3aeb1238f82128e4c5b058be840
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883662"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-cognitive-services-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy w języku JavaScript w przeglądarce, za pomocą Cognitive Services SDK rozpoznawania mowy

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule dowiesz się, jak utworzyć witrynę sieci Web, za pomocą powiązanie JavaScript Cognitive Services SDK mowy transkrypcja mowy na tekst.
Ta aplikacja jest oparta na Microsoft Cognitive Services SDK mowy ([pobierania wersji 1.0.0](https://aka.ms/csspeech/jsbrowserpackage)).

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji dla usługi mowy. Zobacz [bezpłatnie wypróbować usługę rozpoznawania mowy](get-started.md).
* PC lub Mac przy użyciu mikrofonu pracy.
* Edytor tekstu.
* Bieżąca wersja przeglądarki Chrome lub Microsoft Edge.
* Opcjonalnie serwer sieci web obsługujący hostingu skrypty PHP.

## <a name="create-a-new-website-folder"></a>Utwórz nowy folder witryny sieci Web

Utwórz nowy, pusty folder. W przypadku, gdy chcesz hostować próbki na serwerze sieci web, upewnij się, że serwer sieci web można uzyskać dostęp do folderu.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Rozpakowywanie mowy zestawu SDK dla języka JavaScript do tego folderu

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Pobierz zestaw SDK rozpoznawania mowy jako [pakiet wincache.zip](https://aka.ms/csspeech/jsbrowserpackage) i Rozpakuj go do nowo utworzonego folderu. Powinno to spowodować dwa pliki, które są dopiero, czyli `microsoft.cognitiveservices.speech.sdk.bundle.js` i `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Ostatnie pliku jest opcjonalne i używane, aby ułatwić debugowanie kodu zestawu SDK, w razie potrzeby.

## <a name="create-an-indexhtml-page"></a>Tworzenie strony index.html

Utwórz nowy plik w folderze o nazwie `index.html` , a następnie otwórz ten plik za pomocą edytora tekstów.

1. Utwórz następujący szkielet kodu HTML:

  ```html
  <html>
  <head>
      <title>Microsoft Cognitive Service Speech SDK JavaScript Quickstart</title>
  </head>
  <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
  </body>
  </html>
  ```

1. Dodaj następujący kod interfejsu użytkownika do pliku, poniżej pierwszego komentarza:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Dodaj odwołanie do zestawu SDK usługi mowy

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. Podłączanie programów obsługi dla przycisku rozpoznawania, wynik rozpoznawania i subskrypcji powiązanych pól definiowany przez kod interfejsu użytkownika:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>Tworzenie źródłowego tokenu (opcjonalnie)

W przypadku, gdy użytkownik chce udostępnić stronę sieci web na serwerze sieci web, opcjonalnie możesz podać źródło tokenu dla swojej aplikacji pokazowej.
W ten sposób swój klucz subskrypcji nigdy nie pozostawi serwera podczas zezwolenie użytkownikom na korzystanie z funkcji rozpoznawania mowy bez wprowadzania żadnych kod autoryzacji samodzielnie.

1. Utwórz nowy plik o nazwie `token.php`. W tym przykładzie przyjęto założenie, że serwer sieci web obsługuje język skryptowy PHP. Wprowadź następujący kod:

  [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. Edytuj `index.html` pliku i Dodaj następujący kod do pliku:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> Tokeny autoryzacji mieć tylko ograniczony okres istnienia.
> To uproszczony przykład pokazuje, jak do automatycznego odświeżania tokenach autoryzacji. Jako użytkownik możesz ręcznie ponownie załaduj stronę lub nacisnąć klawisz F5, aby odświeżyć.

## <a name="build-and-run-the-sample-locally"></a>Tworzenie i uruchamianie przykładu lokalnie

Aby uruchomić aplikację, kliknij dwukrotnie plik index.html lub Otwórz przeglądarkę internetową index.html. Przedstawi prostego graficznego interfejsu użytkownika umożliwia wprowadzenie swój klucz subskrypcji i [region](regions.md) i wyzwalać rozpoznawanie mikrofonu.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Tworzenie i uruchamianie aplikacji przykładowej za pośrednictwem serwera sieci web

Aby uruchomić aplikację, otwórz przeglądarkę internetową i wskaż publiczny adres URL, który hostował folder na, wprowadź swoje [region](regions.md)i wyzwalać rozpoznawanie mikrofonu. Jeśli skonfigurowano, pobierane będą token ze źródła tokenu.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj tego przykładu w `quickstart/js-browser` folderu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Pobierz nasze przykłady](speech-sdk.md#get-the-samples)
