---
title: 'Szybki Start: Rozpoznawanie mowy, JavaScript (przeglądarki) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznać mowę w języku JavaScript w przeglądarce przy użyciu zestawu Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 77afdb491e4a6cee0a3d6861ea76f57502e56278
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802432"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy w języku JavaScript w przeglądarce przy użyciu zestawu Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule dowiesz się, jak utworzyć witrynę sieci Web przy użyciu powiązania języka JavaScript zestawu transkrypcja Speech SDK Cognitive Services, aby zapewnić zamianę mowy na tekst.
Aplikacja jest oparta na [zestawie mowy SDK dla języka JavaScript](https://aka.ms/csspeech/jsbrowserpackage).

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji usługi mowy. Zobacz [bezpłatnie usługę Speech](get-started.md).
* Komputer PC lub Mac z działającym mikrofonem.
* Edytor tekstu.
* Bieżąca wersja przeglądarki Chrome, przeglądarki Microsoft Edge lub przeglądarki Safari.
* Opcjonalnie serwer sieci Web obsługujący obsługę skryptów PHP.

## <a name="create-a-new-website-folder"></a>Utwórz nowy folder witryny sieci Web

Utwórz nowy, pusty folder. W przypadku, gdy chcesz hostować przykład na serwerze sieci Web, upewnij się, że serwer sieci Web ma dostęp do tego folderu.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Rozpakowywanie zestawu Speech SDK dla języka JavaScript do tego folderu

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Pobierz zestaw Speech SDK jako [pakiet ZIP](https://aka.ms/csspeech/jsbrowserpackage) i rozpakuj go do nowo utworzonego folderu. Powoduje to odpakowanie dwóch plików, `microsoft.cognitiveservices.speech.sdk.bundle.js` i `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Ten ostatni plik jest opcjonalny i jest przydatny do debugowania w kodzie zestawu SDK.

## <a name="create-an-indexhtml-page"></a>Tworzenie strony index. html

Utwórz nowy plik w folderze o nazwie `index.html` i Otwórz ten plik za pomocą edytora tekstu.

1. Utwórz następujący szkielet kodu HTML:

   ```html
   <html>
   <head>
      <title>Speech SDK JavaScript Quickstart</title>
   </head>
   <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
   </body>
   </html>
   ```

1. Dodaj następujący kod interfejsu użytkownika do pliku poniżej pierwszego komentarza:

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Dodawanie odwołania do zestawu Speech SDK

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. Okablowanie obsługi dla przycisku rozpoznawania, wynik rozpoznawania i pola powiązane z subskrypcją zdefiniowane przez kod interfejsu użytkownika:

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>Utwórz źródło tokenu (opcjonalnie)

Jeśli chcesz hostować stronę sieci Web na serwerze sieci Web, opcjonalnie możesz udostępnić Źródło tokenu dla aplikacji demonstracyjnej.
Dzięki temu klucz subskrypcji nigdy nie opuszcza serwera, a użytkownicy mogą korzystać z możliwości mowy bez wprowadzania kodu autoryzacji.

1. Utwórz nowy plik o nazwie `token.php`. W tym przykładzie przyjęto, że serwer sieci Web obsługuje język skryptów PHP. Wprowadź następujący kod:

   [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. Edytuj plik `index.html` i Dodaj następujący kod do pliku:

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> Tokeny autoryzacji mają tylko ograniczony okres istnienia.
> Ten uproszczony przykład nie pokazuje, jak automatycznie odświeżać tokeny autoryzacji. Jako użytkownik możesz ręcznie załadować ponownie stronę lub nacisnąć klawisz F5, aby odświeżyć.

## <a name="build-and-run-the-sample-locally"></a>Kompiluj i uruchamiaj przykład lokalnie

Aby uruchomić aplikację, kliknij dwukrotnie plik index. html lub Otwórz polecenie index. html przy użyciu ulubionej przeglądarki sieci Web. Udostępnimy prosty graficzny interfejs użytkownika, który umożliwi wprowadzanie klucza subskrypcji i [regionu](regions.md) oraz wyzwalanie rozpoznawania przy użyciu mikrofonu.

> [!NOTE]
> Ta metoda nie działa w przeglądarce Safari.
> W przeglądarce Safari Przykładowa strona sieci Web musi być hostowana na serwerze sieci Web. Przeglądarka Safari nie zezwala na korzystanie z mikrofonu w witrynach sieci Web ładowanych z pliku lokalnego.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Kompiluj i uruchamiaj przykład za pośrednictwem serwera sieci Web

Aby uruchomić aplikację, Otwórz ulubioną przeglądarkę internetową i wskaż jej publiczny adres URL, na którym znajduje się folder, wprowadź swój [region](regions.md)i Wyzwól rozpoznawanie przy użyciu mikrofonu. W przypadku skonfigurowania token zostanie pozyskany ze źródła tokenu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady kodu JavaScript w witrynie GitHub](https://aka.ms/csspeech/samples)
