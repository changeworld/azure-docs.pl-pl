---
title: Czym jest usługa rozpoznawania mowy?
description: 'Usługa rozpoznawania mowy będąca częścią usług Microsoft Cognitive Services łączy w sobie kilka usług mowy platformy Azure, które były wcześniej dostępne oddzielnie: rozpoznawanie mowy Bing (składające się z rozpoznawania mowy i zamiany tekstu na mowę), Custom Speech i tłumaczenie mowy.'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: overview
ms.date: 09/24/2018
ms.author: v-jerkin
ms.openlocfilehash: c12cd754d9979683fac4b819739d53e78ab9259c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990199"
---
# <a name="what-is-the-speech-service"></a>Czym jest usługa rozpoznawania mowy?

Usługa rozpoznawania mowy łączy w sobie funkcje mowy platformy Azure dostępne uprzednio za pośrednictwem [interfejsu API rozpoznawania mowy Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home), [interfejsu API tłumaczenia mowy w usłudze Translator](https://docs.microsoft.com/azure/cognitive-services/translator-speech/) oraz usług [Custom Speech](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home) i [Custom Voice](http://customvoice.ai/). Teraz jedna subskrypcja zapewnia dostęp do wszystkich tych możliwości.

Podobnie jak inne usługi mowy platformy Azure usługa rozpoznawania mowy jest obsługiwana przez technologie mowy używane w produktach, takich jak Cortana i Microsoft Office. Możesz liczyć na jakość wyników i niezawodność platformy w chmurze.

## <a name="main-speech-service-functions"></a>Główne funkcje usługi rozpoznawania mowy

Podstawowymi funkcjami usług rozpoznawania mowy są: zamiana mowy na tekst (nazywane również rozpoznawaniem mowy lub transkrypcją), zamiana tekstu na mowę (synteza mowy) i tłumaczenie mowy.

|Funkcja|Funkcje|
|-|-|
|[Zamiana mowy na tekst](speech-to-text.md)| <ul><li>Przekształca w czasie rzeczywistym ciągłą mowę na tekst.<li>Może być używana do wsadowego przekształcania mowy z nagrań audio. <li>Oferuje różne tryby rozpoznawania: interaktywny, konwersacja i dyktowanie.<li>Obsługuje pośrednie wyniki, rozpoznawanie końca wypowiedzi, automatyczne formatowanie tekstu i maskowanie niestosownych wyrażeń. <li>Może wywoływać [interfejs API usługi Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) w celu wyodrębnienia intencji użytkownika z przekształconej mowy.\*|
|[Zamiana tekstu na mowę](text-to-speech.md)| <ul><li>Konwertuje tekst na naturalnie brzmiącą mowę. <li>Oferuje możliwość wyboru płci i/lub dialektów w wielu obsługiwanych językach. <li>Obsługuje dane wejściowe w postaci zwykłego tekstu lub języku znaczników syntezy mowy (SSML). |
|[Tłumaczenie mowy](speech-translation.md)| <ul><li>Tłumaczy przesyłane strumieniowo dane audio w czasie zbliżonym do rzeczywistego.<li> Może również przetwarzać zarejestrowaną mowę.<li>Wyniki zwraca w postaci tekstu lub syntezowanej mowy. |

\* *Funkcja rozpoznawania intencji wymaga subskrypcji usługi LUIS.*

## <a name="customize-speech-features"></a>Dostosowywanie funkcji rozpoznawania mowy

Na potrzeby szkolenia modeli bazowych funkcji usługi rozpoznawania mowy — zamiany mowy na tekst i zamiany tekstu na mowę — możesz użyć własnych danych.

|Cecha|Modelowanie|Przeznaczenie|
|-|-|-|
|Zamiana mowy na tekst|[Model akustyczny](how-to-customize-acoustic-models.md)|Pomaga podczas transkrypcji określonych prelegentów i środowiskach, takich jak samochody lub fabryki.|
||[Model językowy](how-to-customize-language-model.md)|Pomaga podczas transkrypcji słownictwa i gramatyki specyficznych dla różnych grup zawodowych, na przykład żargonu medycznego lub informatycznego.|
||[Model wymowy](how-to-customize-pronunciation.md)|Pomaga podczas transkrypcji skrótów i akronimów, takich jak „IOU” i „I owe you”. |
|Zamiana tekstu na mowę|[Czcionka głosowa](how-to-customize-voice-font.md)|Nadaje aplikacji własny głos po przeszkoleniu modelu na próbkach ludzkiej mowy.|

Modeli niestandardowych można używać w takich samych scenariuszach funkcji zamiany mowy na tekst i zamiany tekstu na mowę jak modeli standardowych.

## <a name="use-the-speech-service"></a>Korzystanie z usługi rozpoznawania mowy

Aby uprościć tworzenie aplikacji z obsługą mowy, firma Microsoft udostępnia [zestaw SDK rozpoznawania mowy](speech-sdk.md) do użycia z nową usługą rozpoznawania mowy. Zestaw SDK rozpoznawania mowy udostępnia spójne natywne interfejsy API zamiany mowy na tekst i tłumaczenia mowy dla języków C#, C++ i Java. Jeśli tworzysz aplikacje przy użyciu jednego z tych języków, zestaw SDK rozpoznawania mowy ułatwi Ci projektowanie dzięki obsłudze zagadnień sieciowych.

Usługa rozpoznawania mowy ma również [interfejs API REST](rest-apis.md) współpracujący z dowolnym językiem programowania, który może obsługiwać żądania HTTP. Interfejs REST nie oferuje przesyłania strumieniowego w czasie rzeczywistym, które jest obsługiwane w zestawie SDK.

|<br>Metoda|Mowa<br>na tekst|Tekst na<br>Mowa|Mowa<br>Tłumaczenie|<br>Opis|
|-|-|-|-|-|
|[Zestaw SDK rozpoznawania mowy](speech-sdk.md)|Yes|Nie|Yes|Natywne interfejsy API dla języków C#, C++ i Java upraszczające projektowanie aplikacji.|
|[REST](rest-apis.md)|Yes|Yes|Nie|Prosty interfejs API oparty na protokole HTTP, który ułatwia dodawanie mowy do aplikacji.|

### <a name="websockets"></a>Protokoły WebSocket

Usługa rozpoznawania mowy obsługuje również protokoły WebSocket na potrzeby funkcji zamiany mowy na tekst i tłumaczenia mowy w oparciu o dane przesyłane strumieniowo. Zestawy SDK rozpoznawania mowy używają tych protokołów do komunikacji z usługą rozpoznawania mowy. Użyj tego zestawu SDK rozpoznawania mowy zamiast przeprowadzania prób zaimplementowania własnej komunikacji z usługą rozpoznawania mowy opartej na protokołach WebSocket.

Jeśli masz już kod korzystający z interfejsu API rozpoznawania mowy Bing lub interfejsu API tłumaczenia mowy w usłudze Translator za pośrednictwem protokołów WebSocket, możesz zaktualizować go, aby korzystał z usługi rozpoznawania mowy. Protokoły WebSocket są zgodne; różnią się tylko punkty końcowe.

### <a name="speech-devices-sdk"></a>Zestaw Speech Devices SDK

Zestaw [Speech Devices SDK](speech-devices-sdk.md) to zintegrowana platforma sprzętu i oprogramowania dla deweloperów urządzeń z włączoną obsługą mowy. Nasz partner dostarczający sprzęt udostępnia projekty referencyjne i jednostki rozwojowe. Firma Microsoft udostępnia zoptymalizowany pod kątem urządzeń zestaw SDK, który potrafi w pełni wykorzystać możliwości sprzętu.

## <a name="speech-scenarios"></a>Scenariusze z zastosowaniem mowy

Przypadki użycia usługi rozpoznawania mowy obejmują następujące sytuacje:

> [!div class="checklist"]
> * Tworzenie aplikacji wyzwalanych głosem
> * Przekształcanie nagrań w centrach telefonicznej obsługi klienta
> * Implementowanie botów głosowych

### <a name="voice-user-interface"></a>Głosowy interfejs użytkownika

Obsługa wejściowych danych głosowych to doskonały sposób, aby zapewnić elastyczność Twojej aplikacji oraz sprawić, żeby działa szybko i bez użycia rąk. W aplikacji z obsługą głosu użytkownicy mogą po prostu prosić o informacje, których potrzebują.

Jeśli Twoja aplikacja jest przeznaczona do ogólnego użytku publicznego, możesz użyć domyślnych modeli rozpoznawania mowy. Rozpoznają one szeroką gamę prelegentów w typowych środowiskach.

Jeśli Twoja aplikacja jest używana w konkretnym środowisku (na przykład medycznym lub informatycznym), możesz utworzyć [model językowy](how-to-customize-language-model.md). Ten model umożliwia nauczenie usługi rozpoznawania mowy specjalistycznej terminologii używanej przez Twoją aplikację.

Jeśli Twoja aplikacja jest używana w hałaśliwym środowisku, na przykład w fabryce, możesz utworzyć niestandardowy [model akustyczny](how-to-customize-acoustic-models.md). Ten model pomaga usłudze rozpoznawania mowy lepsze rozróżnienie mowy od szumu.

Rozpoczęcie pracy jest łatwe. Wystarczy pobrać [zestaw SDK rozpoznawania mowy](speech-sdk.md) i postępować zgodnie z odpowiednim artykułem [Szybki start](quickstart-csharp-dotnet-windows.md).

### <a name="call-center-transcription"></a>Przekształcanie nagrań w centrach telefonicznej obsługi klienta

Często nagrania z centrów telefonicznej obsługi klienta są wykorzystywane tylko w przypadku wystąpienia problemu podczas rozmowy telefonicznej. Dzięki usłudze rozpoznawania mowy każde nagranie można łatwo przekształcić w tekst. Dla takiego tekstu można łatwo utworzyć indeks na potrzeby [wyszukiwania pełnotekstowego](https://docs.microsoft.com/azure/search/search-what-is-azure-search) lub zastosować [analizę tekstu](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) w celu wykrycia tonacji, języka i fraz kluczowych.

Jeśli nagrania z centrów telefonicznej obsługi klienta zawierają specjalistyczną terminologię, na przykład nazwy produktu lub żargon informatyczny, możesz utworzyć [model językowy](how-to-customize-language-model.md), aby nauczyć usługę rozpoznawania mowy tego słownictwa. Niestandardowy [model akustyczny](how-to-customize-acoustic-models.md) może pomóc usłudze rozpoznawania mowy w interpretacji rozmów telefonicznych prowadzonych w oparciu o słabej jakości połączenia.

Aby uzyskać więcej informacji na temat tego scenariusza, przeczytaj więcej o [transkrypcji wsadowej](batch-transcription.md) w usłudze rozpoznawania mowy.

### <a name="voice-bots"></a>Boty głosowe

[Boty](https://dev.botframework.com/) to popularny sposób łączenia użytkowników z informacjami, których potrzebują, i klientów z ulubionymi firmami. Po dodaniu konwersacyjnego interfejsu użytkownika do aplikacji lub witryny internetowej łatwiej jest znaleźć oferowane funkcje oraz szybciej można uzyskać do nich dostęp. Dzięki usłudze rozpoznawania mowy taka konwersacja przenosi się do nowego wymiaru płynności przez odpowiadanie na różne wymawiane zapytania.

Aby dodać botowi z obsługą mowy unikatową osobowość, możesz wyposażyć go we własny głos. Tworzenie niestandardowego głosu jest procesem dwuetapowym. Najpierw [utwórz nagrania](record-custom-voice-samples.md) głosu, który ma zostać użyty. Następnie [prześlij te nagrania](how-to-customize-voice-font.md) wraz z tekstem transkrypcji do [portalu dostosowywania głosu](https://cris.ai/Home/CustomVoice) usługi rozpoznawania mowy, który zajmie się resztą. Po utworzeniu niestandardowego głosu kroki użycia go w aplikacji są już całkiem proste.

## <a name="next-steps"></a>Następne kroki

Pobierz klucz subskrypcji dla usługi rozpoznawania mowy.

> [!div class="nextstepaction"]
> [Wypróbuj bezpłatnie usługę rozpoznawania mowy](get-started.md)
