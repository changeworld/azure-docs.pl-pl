---
title: Co to jest usługa mowy (wersja zapoznawcza)?
description: 'Usługa rozpoznawania mowy, częścią firmy Microsoft Cognitive Services, łączy w sobie kilka usług mowy platformy Azure, które były wcześniej dostępne oddzielnie: rozpoznawania mowy Bing (wchodzących w skład rozpoznawania mowy i zamiany tekstu na mowę), niestandardowe mowy i tłumaczenie mowy.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 60ff2f71766a14af17ebb1cb9d20825976471296
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "41987504"
---
# <a name="what-is-the-speech-service-preview"></a>Co to jest usługa mowy (wersja zapoznawcza)?

Z jedną subskrypcją usługi mowy daje deweloperom prosty sposób dodać zaawansowane funkcje obsługujące rozpoznawanie mowy do aplikacji. Aplikacje można teraz funkcji poleceń głosowych, transkrypcji, funkcją dyktowania, synteza mowy i tłumaczenie mowy.

Usługa rozpoznawania mowy jest obsługiwane przez technologie używane w innych produktów firmy Microsoft, w tym Cortana i Microsoft Office.

> [!NOTE]
> Usługa rozpoznawania mowy jest obecnie w publicznej wersji zapoznawczej. Wróć tutaj regularne aktualizacje i dokumentację, próbki kodu dodatkowe i inne.

## <a name="speech-service-features"></a>Funkcje usługi rozpoznawania mowy

|Funkcja|Opis|
|-|-|
|[Zamiany mowy na tekst](speech-to-text.md)| Transcribes strumieni audio na tekst, który aplikacja może akceptować jako dane wejściowe. Integruje się również z [usługi Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) ma być intencji użytkownika z wypowiedzi.|
|[Zamiana tekstu na mowę](text-to-speech.md)| Konwertuje zwykłego tekstu do naturalnym brzmiącą mowę, na dostarczane do aplikacji w pliku audio. Wiele głosów, różnicowanie płci lub akcent, są dostępne dla wielu języków. |
|[Tłumaczenie mowy](speech-translation.md)| Może służyć do translacji strumienie audio niemal w czasie rzeczywistym lub do przetwarzania mowy zarejestrowane. |
|Niestandardowe mowy na tekst|Można dostosować mowy na tekst, tworząc własne [akustyczny](how-to-customize-acoustic-models.md) i [języka](how-to-customize-language-model.md) modeli i określenie niestandardowych [Wymowa](how-to-customize-pronunciation.md) reguły. |
|[Niestandardowy tekst na mowę](how-to-customize-voice-font.md)|Możesz utworzyć własne głosów zamiany tekstu na mowę.|
|[Zestaw Speech Devices SDK](speech-devices-sdk.md)| Wraz z wprowadzeniem ujednolicone usługi mowy firmy Microsoft i jej partnerzy oferują platforma zintegrowane sprzętu i oprogramowania, zoptymalizowana pod kątem tworzenia urządzenia obsługujące rozpoznawanie mowy |

## <a name="access-to-the-speech-service"></a>Dostęp do usługi rozpoznawania mowy

Usługa rozpoznawania mowy jest udostępniane na dwa sposoby. [Zestaw SDK](speech-sdk.md) ukrywa szczegóły protokołów sieciowych opracowywania łatwiejsze na obsługiwanych platformach. [Interfejsu API REST](rest-apis.md) współdziała z dowolnego języka programowania, ale nie oferuje wszystkie funkcje oferowane przez zestaw SDK.

|<br>Metoda|Mowa<br>do tekstu|Tekst<br>Mowa|Mowa<br>Tłumaczenie|<br>Opis|
|-|-|-|-|-|
|[Zestawy SDK](speech-sdk.md)|Yes|Nie|Yes|Biblioteki dla określonych języków programowania, korzystanie z protokołu na podstawie protokołu Websocket, które upraszczają proces tworzenia.|
|[REST](rest-apis.md)|Yes|Yes|Nie|Prosty oparty na protokole HTTP interfejs API, która ułatwia dodawanie funkcji rozpoznawania mowy do aplikacji.|

## <a name="speech-scenarios"></a>Scenariusze mowy

Kilka typowych zastosowań technologii rozpoznawania mowy w krótko omówiono poniżej. [Zestaw SDK rozpoznawania mowy](speech-sdk.md) stanowi podstawę do większości z tych scenariuszy.

> [!div class="checklist"]
> * Tworzenie aplikacji wyzwalany głosu
> * Transkrypcja nagrania Centrum wywołania
> * Implementowanie botów głosu

### <a name="voice-triggered-apps"></a>Wyzwalane głosu aplikacji

Wprowadzania danych głosowych to doskonały sposób, aby zapewnić aplikacji, elastyczne bezobsługowego i szybkiego używania. W przypadku aplikacji z obsługą głosu użytkownicy mogą po prostu zadawać na informacje, które chcą zamiast konieczności przejście do niej, klikając lub naciskając.

Jeśli aplikacja jest przeznaczona do użytku ogółowi społeczeństwa, można użyć linii bazowej model rozpoznawania mowy, udostępniane przez usługę rozpoznawania mowy. Robi Dobra robota rozpoznawać szerokiej gamy prelegentów w typowym środowisku.

Jeśli aplikacji, które będą używane w określonej domenie (na przykład medycyna lub IT), możesz utworzyć [modelu językowego](how-to-customize-language-model.md) do nauki usług mowy o terminologię, używane przez Twoją aplikację.

Jeśli aplikacji, które będą używane w środowisku generujące dużo alertów, takie jak fabrykę, można utworzyć niestandardowy [model akustyczny](how-to-customize-acoustic-models.md) umożliwia lepsze usługą mowy w celu odróżnienia mowy od szumu.

Rozpoczęcie pracy jest równie proste jak pobieranie [zestaw SDK rozpoznawania mowy](speech-sdk.md) i zgodnie z odpowiednimi [Szybki Start](quickstart-csharp-dotnet-windows.md) artykułu.

### <a name="transcribe-call-center-recordings"></a>Transkrypcja nagrania Centrum wywołania

Często nagrania Centrum wywołania są konsultacje tylko w razie problemu z wywołaniem. Usługa rozpoznawania mowy jest proste także co nagranie tekstu. Po tekst, możesz je, aby łatwo indeksu [wyszukiwania pełnotekstowego](https://docs.microsoft.com/azure/search/search-what-is-azure-search) lub zastosować [analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) wykrywanie tonacji, języka i fraz kluczowych.

Wywołanie nagrań Centrum często zawierają specjalne terminologii (np. nazwy produktów lub żargon IT), można utworzyć [modelu językowego](how-to-customize-language-model.md) do nauki usług mowy tego słownika. Niestandardowy [model akustyczny](how-to-customize-acoustic-models.md) pomocy usługi mowy wyjaśnić, w mniej niż optymalne połączeń telefonicznych.

Aby uzyskać więcej informacji na temat tego scenariusza, przeczytaj więcej na temat [batch transkrypcji](batch-transcription.md) z usługa mowy.

### <a name="voice-bots"></a>Boty głosu

[Boty](https://dev.botframework.com/) mają coraz bardziej popularnym sposobem łączenia się użytkowników przy użyciu informacji i klientów z firm ulubionych zajęć. Dodawanie interfejsu użytkownika konwersacji do aplikacji lub witryny sieci Web sprawia, że jej funkcjonalność ułatwia znajdowanie i szybciej uzyskać dostęp do. Usługa rozpoznawania mowy tej konwersacji przyjmie nowy wymiar fluency faktycznie odpowiedzi na zapytania prowadzone za pomocą syntezatora mowy.

Dodaj unikatowy osobowość do bota obsługą głosu (i Wzmocnij swoją markę), możesz nadać mu własnego głosu. Tworzenie niestandardowych voice jest procesem dwuetapowym. Najpierw należy [wprowadzić nagrania](record-custom-voice-samples.md) głosu, którego chcesz użyć. Możesz [przesyłania tych nagrania](how-to-customize-voice-font.md) (wraz z transkrypcji tekstu) do usługi rozpoznawania mowy [głosu dostosowywania portalu](https://cris.ai/Home/CustomVoice), który zajmie się resztą. Po utworzeniu niestandardowych głos jest proste z niej korzystać w swojej aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Pobierz klucz subskrypcji dla usługi mowy.

> [!div class="nextstepaction"]
> [Wypróbuj bezpłatnie usługę rozpoznawania mowy](get-started.md)
