---
title: 'Szybki start: rozpoznawanie mowy, JavaScript (przeglądarka) — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w języku JavaScript w przeglądarce przy użyciu zestawu SDK usługi Mowa
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: fmegen
ms.openlocfilehash: a418966ce72876d58d39b939ecb7dd5a05e41b9b
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59008990"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-speech-sdk"></a>Szybki start: Rozpoznawanie mowy w języku JavaScript w przeglądarce przy użyciu zestawu SDK usługi Mowa

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Z tego artykułu dowiesz się, jak utworzyć witrynę internetową przy użyciu powiązania języka JavaScript zestawu SDK usługi Mowa z usługi Cognitive Services, aby wykonać transkrypcję mowy na tekst.
Ta aplikacja jest oparta na zestaw SDK rozpoznawania mowy dla języka JavaScript ([pobierania wersji 1.4.0](https://aka.ms/csspeech/jsbrowserpackage)).

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji dla usługi Mowa. Zobacz [bezpłatnego wypróbowania usługi mowy](get-started.md).
* Komputer PC lub Mac z działającym mikrofonem.
* Edytor tekstów.
* Bieżąca wersja przeglądarki Chrome, Microsoft Edge lub przeglądarki Safari.
* Opcjonalnie serwer internetowy, który obsługuje skrypty hostingu w języku PHP.

## <a name="create-a-new-website-folder"></a>Tworzenie nowego folderu witryny internetowej

Utwórz nowy, pusty folder. W przypadku, gdy chcesz hostować przykład na serwerze internetowym, upewnij się, że serwer internetowy ma dostęp do tego folderu.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Rozpakowywanie zestawu SDK usługi Mowa dla języka JavaScript do tego folderu

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Pobierz zestaw SDK usługi Mowa jako [pakiet zip](https://aka.ms/csspeech/jsbrowserpackage) i rozpakuj go do nowo utworzonego folderu. Powoduje to rozpakowanie dwóch plików: `microsoft.cognitiveservices.speech.sdk.bundle.js` i `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Ten drugi plik jest opcjonalny i przydaje się podczas debugowania do kodu zestawu SDK.

## <a name="create-an-indexhtml-page"></a>Tworzenie strony index.html

Utwórz w folderze nowy plik o nazwie `index.html`, a następnie otwórz ten plik za pomocą edytora tekstów.

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

1. Dodaj następujący kod interfejsu użytkownika do pliku, poniżej pierwszego komentarza:

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Dodaj odwołanie do zestawu SDK usługi Mowa

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. Podłącz procedury obsługi dla przycisku rozpoznawania, wyniku rozpoznawania i pól powiązanych z subskrypcją zdefiniowanych przez kod interfejsu użytkownika:

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>Tworzenie źródła tokenu (opcjonalnie)

W przypadku, gdy chcesz udostępnić stronę internetową na serwerze internetowym, możesz opcjonalnie podać źródło tokenu dla swojej aplikacji pokazowej.
W ten sposób Twój klucz subskrypcji nigdy nie opuści serwera, ale jednocześnie umożliwi użytkownikom korzystanie z możliwości funkcji rozpoznawania mowy bez wprowadzania przez nich kodu autoryzacji.

1. Utwórz nowy plik o nazwie `token.php`. W tym przykładzie przyjęto założenie, że serwer internetowy obsługuje język skryptów PHP. Wprowadź następujący kod:

   [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. Edytuj plik `index.html` i dodaj następujący kod do pliku:

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> Tokeny autoryzacji mają tylko ograniczony okres istnienia.
> Ten uproszczony przykład nie pokazuje, jak automatycznie odświeżać tokeny autoryzacji. Jako użytkownik możesz ręcznie ponownie załadować stronę lub nacisnąć klawisz F5, aby odświeżyć.

## <a name="build-and-run-the-sample-locally"></a>Lokalne kompilowanie i uruchamianie przykładu

Aby uruchomić aplikację, kliknij dwukrotnie plik index.html lub otwórz plik index.html za pomocą swojej ulubionej przeglądarki internetowej. Będzie on prezentował prosty graficzny interfejs użytkownika umożliwiający wprowadzenie klucza subskrypcji i [regionu](regions.md) oraz wyzwolenie rozpoznawanie przy użyciu mikrofonu.

> [!NOTE]
> Ta metoda nie działa w przeglądarce Safari.
> W przeglądarce Safari przykładowej strony internetowej musi być hostowana na serwerze sieci web. Safari nie zezwala na witryn sieci Web załadowanego z pliku lokalnego do użycia mikrofonu.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Kompilowanie i uruchamianie przykładu za pomocą serwera internetowego

Aby uruchomić aplikację, otwórz przeglądarkę internetową i wskaż w niej publiczny adres URL, pod którym hostowany jest folder, wprowadź swój [region](regions.md) i wyzwól rozpoznawanie za pomocą mikrofonu. Jeśli skonfigurowano tę funkcję, pobierze ona token ze źródła tokenu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Eksploruj przykładowe skrypty JavaScript w usłudze GitHub](https://aka.ms/csspeech/samples)
