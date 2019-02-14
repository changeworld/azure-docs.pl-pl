---
title: Czym jest usługa rozpoznawania mowy?
titleSuffix: Azure Cognitive Services
description: 'Usługa Speech Service, która jest częścią usług Azure Cognitive Services, łączy w sobie kilka usług rozpoznawania mowy, które wcześniej były dostępne oddzielnie: rozpoznawanie mowy Bing (składające się z rozpoznawania mowy i zamiany tekstu na mowę), Custom Speech i tłumaczenie mowy.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: d60e5f881e44f397090a3ba5e467c08f20137d72
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858836"
---
# <a name="what-is-speech-services"></a>Czym są usługi Speech Services?

Podobnie jak inne usługi rozpoznawania mowy platformy Azure usługi Speech Services są obsługiwane przez technologie mowy używane w produktach takich jak Cortana i pakiet Microsoft Office.

Usługi Speech Services łączą w sobie funkcje mowy platformy Azure dostępne dotychczas za pośrednictwem [interfejsu API rozpoznawania mowy Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home), [interfejsu API tłumaczenia mowy w usłudze Translator](https://docs.microsoft.com/azure/cognitive-services/translator-speech/) oraz usług [Custom Speech](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home) i [Custom Voice](http://customvoice.ai/). Teraz jedna subskrypcja zapewnia dostęp do wszystkich tych możliwości.

## <a name="main-speech-services-functions"></a>Główne funkcje usługi Speech Services

Podstawowymi funkcjami usług Speech Services są: zamiana mowy na tekst (nazywane również rozpoznawaniem mowy lub transkrypcją), zamiana tekstu na mowę (synteza mowy) i tłumaczenie mowy.

|Funkcja|Funkcje|
|-|-|
|[Zamiana mowy na tekst](speech-to-text.md)| <li>Przekształca w czasie rzeczywistym ciągłą mowę na tekst.<li>Może być używana do wsadowego przekształcania mowy z nagrań audio. <li>Obsługuje pośrednie wyniki, rozpoznawanie końca wypowiedzi, automatyczne formatowanie tekstu i maskowanie niestosownych wyrażeń. <li>Może wywoływać [interfejs API usługi Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) w celu wyodrębnienia intencji użytkownika z przekształconej mowy.\*|
|[Zamiana tekstu na mowę](text-to-speech.md)| <li>**NOWOŚĆ**: Udostępnia neuronowe głosy zamiany tekstu na mowę, które są praktycznie nie do odróżnienia od ludzkiej mowy (w języku angielskim). <li>Konwertuje tekst na naturalnie brzmiącą mowę. <li>Oferuje możliwość wyboru płci i/lub dialektów w wielu obsługiwanych językach. <li>Obsługuje dane wejściowe w postaci zwykłego tekstu lub języku znaczników syntezy mowy (SSML). |
|[Tłumaczenie mowy](speech-translation.md)| <li>Tłumaczy przesyłane strumieniowo dane audio w czasie zbliżonym do rzeczywistego.<li> Może również przetwarzać zarejestrowaną mowę.<li>Wyniki zwraca w postaci tekstu lub syntezowanej mowy. |


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

Aby uprościć tworzenie aplikacji z obsługą mowy, firma Microsoft udostępnia [zestaw SDK rozpoznawania mowy](speech-sdk.md) do użycia z usługą Mowa. Zestaw SDK rozpoznawania mowy udostępnia spójne natywne interfejsy API zamiany mowy na tekst i tłumaczenia mowy dla języków C#, C++ i Java. Jeśli tworzysz aplikacje przy użyciu jednego z tych języków, zestaw SDK rozpoznawania mowy ułatwi Ci projektowanie dzięki obsłudze zagadnień sieciowych.

Usługi Speech Services mają również interfejs [API REST](rest-apis.md) współpracujący z dowolnym językiem programowania, który może wysyłać żądania HTTP. Interfejs REST nie oferuje przesyłania strumieniowego w czasie rzeczywistym, które jest obsługiwane w zestawie SDK.

|<br>Metoda|Mowa<br>na tekst|Tekst na<br>Mowa|Mowa<br>Tłumaczenie|<br>Opis|
|-|-|-|-|-|
|[Zestaw SDK rozpoznawania mowy](speech-sdk.md)|Yes|Nie|Yes|Natywne interfejsy API dla języków C#, C++ i Java upraszczające projektowanie aplikacji.|
|[Interfejsy API REST](rest-apis.md)|Yes|Yes|Nie|Prosty interfejs API oparty na protokole HTTP, który ułatwia dodawanie mowy do aplikacji.|

### <a name="websockets"></a>Protokoły WebSocket

Usługi Speech Services obsługują również protokoły WebSocket na potrzeby funkcji zamiany mowy na tekst i tłumaczenia mowy w oparciu o dane przesyłane strumieniowo. Zestawy SDK rozpoznawania mowy używają tych protokołów do komunikacji z usługą rozpoznawania mowy. Użyj tego zestawu SDK rozpoznawania mowy zamiast przeprowadzania prób zaimplementowania własnej komunikacji z usługą rozpoznawania mowy opartej na protokołach WebSocket.

Jeśli masz już kod korzystający z interfejsu API rozpoznawania mowy Bing lub interfejsu API tłumaczenia mowy w usłudze Translator za pośrednictwem protokołów WebSocket, możesz go zaktualizować, aby korzystał z usług Speech Services. Protokoły WebSocket są zgodne, jednak punkty końcowe są inne.

### <a name="speech-devices-sdk"></a>Zestaw Speech Devices SDK

Zestaw [Speech Devices SDK](speech-devices-sdk.md) to zintegrowana platforma sprzętu i oprogramowania dla deweloperów urządzeń z włączoną obsługą mowy. Nasz partner dostarczający sprzęt udostępnia projekty referencyjne i jednostki rozwojowe. Firma Microsoft udostępnia zoptymalizowany pod kątem urządzeń zestaw SDK, który potrafi w pełni wykorzystać możliwości sprzętu.


## <a name="speech-scenarios"></a>Scenariusze z zastosowaniem mowy

Przypadki użycia usług Speech Services obejmują następujące sytuacje:

> [!div class="checklist"]
> * Tworzenie aplikacji wyzwalanych głosem
> * Przekształcanie nagrań w centrach telefonicznej obsługi klienta
> * Implementowanie botów głosowych

### <a name="voice-user-interface"></a>Głosowy interfejs użytkownika

Obsługa wejściowych danych głosowych to doskonały sposób, aby zapewnić elastyczność Twojej aplikacji oraz sprawić, żeby działa szybko i bez użycia rąk. W aplikacji z obsługą głosu użytkownicy mogą po prostu prosić o informacje, których potrzebują.

Jeśli Twoja aplikacja jest przeznaczona do ogólnego użytku publicznego, możesz użyć domyślnych modeli rozpoznawania mowy. Rozpoznają one szeroką gamę prelegentów w typowych środowiskach.

Jeśli Twoja aplikacja jest używana w konkretnym środowisku (na przykład medycznym lub informatycznym), możesz utworzyć [model językowy](how-to-customize-language-model.md). Ten model umożliwia nauczenie usług Speech Services specjalistycznej terminologii używanej przez Twoją aplikację.

Jeśli Twoja aplikacja jest używana w hałaśliwym środowisku, na przykład w fabryce, możesz utworzyć niestandardowy [model akustyczny](how-to-customize-acoustic-models.md). Ten model umożliwia usługom Speech Services lepsze rozróżnienie mowy od szumu.

### <a name="call-center-transcription"></a>Przekształcanie nagrań w centrach telefonicznej obsługi klienta

Często nagrania z centrów telefonicznej obsługi klienta są wykorzystywane tylko w przypadku wystąpienia problemu podczas rozmowy telefonicznej. Dzięki usłudze rozpoznawania mowy każde nagranie można łatwo przekształcić w tekst. Dla takiego tekstu można łatwo utworzyć indeks na potrzeby [wyszukiwania pełnotekstowego](https://docs.microsoft.com/azure/search/search-what-is-azure-search) lub zastosować [analizę tekstu](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) w celu wykrycia tonacji, języka i fraz kluczowych.

Jeśli nagrania z centrów telefonicznej obsługi klienta zawierają specjalistyczną terminologię, na przykład nazwy produktu lub żargon informatyczny, możesz utworzyć [model językowy](how-to-customize-language-model.md), aby nauczyć usługi Speech Services tego słownictwa. Niestandardowy [model akustyczny](how-to-customize-acoustic-models.md) może pomóc usługom Speech Services w interpretacji rozmów telefonicznych prowadzonych, gdy połączenia są słabej jakości.

Aby uzyskać więcej informacji na temat tego scenariusza, przeczytaj więcej o [transkrypcji wsadowej](batch-transcription.md) w usłudze rozpoznawania mowy.

### <a name="voice-bots"></a>Boty głosowe

[Boty](https://dev.botframework.com/) to popularny sposób łączenia użytkowników z informacjami, których potrzebują, i klientów z ulubionymi firmami. Po dodaniu konwersacyjnego interfejsu użytkownika do aplikacji lub witryny internetowej łatwiej jest znaleźć oferowane funkcje oraz szybciej można uzyskać do nich dostęp. Dzięki usłudze rozpoznawania mowy taka konwersacja przenosi się do nowego wymiaru płynności przez odpowiadanie na różne wymawiane zapytania.

Aby dodać botowi z obsługą mowy unikatową osobowość, możesz wyposażyć go we własny głos. Tworzenie niestandardowego głosu jest procesem dwuetapowym. Najpierw [utwórz nagrania](record-custom-voice-samples.md) głosu, który ma zostać użyty. Następnie [prześlij te nagrania](how-to-customize-voice-font.md) wraz z tekstem transkrypcji do [portalu dostosowywania głosu](https://cris.ai/Home/CustomVoice) usługi rozpoznawania mowy, który zajmie się resztą. Po utworzeniu niestandardowego głosu kroki użycia go w aplikacji są już całkiem proste.

## <a name="next-steps"></a>Następne kroki

Pobierz klucz subskrypcji dla usług Speech Services.

> [!div class="nextstepaction"]
> [Wypróbuj bezpłatnie usługi Speech Services](get-started.md)
