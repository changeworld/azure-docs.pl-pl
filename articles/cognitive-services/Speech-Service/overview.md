---
title: Co to jest usługa mowy (wersja zapoznawcza)? | Microsoft Docs
description: 'Usługa rozpoznawania mowy część kognitywnych usług firmy Microsoft, unites kilka usług Azure mowy, które były wcześniej dostępne oddzielnie: mowy usługi Bing (obejmujący rozpoznawania mowy i zamiany tekstu na mowę), niestandardowe mowy i translację mowy.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: e7c09eee1634c52e78a523a7cc65641ea99f23e6
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356171"
---
# <a name="what-is-the-speech-service-preview"></a>Co to jest usługa mowy (wersja zapoznawcza)?

Usługa rozpoznawania mowy część kognitywnych usług firmy Microsoft, unites kilka usług Azure mowy, które były wcześniej dostępne oddzielnie: mowy usługi Bing (obejmujący rozpoznawania mowy i zamiany tekstu na mowę), niestandardowe mowy i translację mowy. Podobnie jak jego prekursorów mowy usługi jest obsługiwany przez technologie używane w innych produktów firmy Microsoft, w tym Cortana i Microsoft Office.

> [!NOTE]
> Usługa rozpoznawania mowy jest obecnie w wersji zapoznawczej. Wróć tutaj regularnie aktualizacji do dokumentacji, dodatkowe przykłady i inne.

Z jedną subskrypcją ujednoliconego usługi mowy zapewnia deweloperom łatwe podać swoje aplikacje zaawansowanego mowy funkcji. Aplikacje można teraz funkcji poleceń głosowych, przekształcania, dyktowania, syntezy mowy i translacji.

|Funkcja|Opis|
|-|-|
|Zamiana mowy na tekst|Konwertuje człowieka mowy na tekst, który może służyć jako dane wejściowe do aplikacji. Można zintegrować z [usługi języka opis](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) pochodzić z zamiarów użytkownika z zniesławiających.|
|Zamiana tekstu na mowę|Konwertuje tekst do plików audio pomiarowe naturalnego syntezatora mowy.|
|Mowy&nbsp;tłumaczenia|Podaj tłumaczenia mowy dla innych języków, z danych wyjściowych tekst lub mowy.|

## <a name="using-the-speech-service"></a>Korzystanie z usługi rozpoznawania mowy

Usługa rozpoznawania mowy ma zostać udostępnione na dwa sposoby. [Zestaw SDK](speech-sdk.md) abstracts optymalizacji szczegóły protokołów sieciowych. [Interfejsu API REST](rest-apis.md) współpracuje z żadnego języka programowania, ale nie oferuje wszystkie funkcje oferowane przez zestaw SDK.

|<br>Metoda|Mowa<br>do tekstu|Tekst, który ma<br>Mowa|Mowa<br>Tłumaczenie|<br>Opis|
|-|-|-|-|-|
|[Zestawy SDK](speech-sdk.md)|Yes|Nie|Yes|Biblioteki dla określonych języków programowania, które upraszczają programowanie.|
|[REST](rest-apis.md)|Yes|Yes|Nie|Prosty oparte na protokole HTTP interfejs API, który ułatwia dodawanie mowy do aplikacji.|

## <a name="speech-to-text"></a>Zamiana mowy na tekst

[Mowy na tekst](speech-to-text.md) (STT) lub rozpoznawania mowy interfejsu API transcribes strumieni audio na tekst, który aplikacja może zaakceptować jako dane wejściowe. Aplikację można następnie, na przykład wprowadź tekst do dokumentu lub obsługiwanie go jako polecenia.

Mowy na tekst oddzielnie zoptymalizowany do interaktywnego, konwersacji, dyktowania scenariusze i. Poniżej przedstawiono typowe przypadki użycia mowy API tekstu. 

* Rozpoznaje krótki utterance, takich jak polecenia, bez wyników pośrednich
* Wykonać transkrypcji utterance długie, zarejestrowane wcześniej, takie jak wiadomości poczty głosowej
* Wykonać transkrypcji mowy przesyłania strumieniowego w czasie rzeczywistym, wyniki częściowe, dyktowania
* Określ, co użytkownicy celu oparte na żądanie języka naturalnego rozmowy

Mowy tekst API obsługuje przekształcania interakcyjne mowy rozpoznawania ciągłej w czasie rzeczywistym i wyników pośrednich. Obsługuje ona również wykrywania zakończenia z mowy, opcjonalne automatyczne wielkie litery i znaków interpunkcyjnych maskowania niestosownych wyrażeń i normalizacji tekstu.

Można dostosować mowy na tekst akustycznego i modele języka aby pomieścić specjalistycznego słownictwa, zakłócenia środowiska i różne sposoby, mówiąc.

## <a name="text-to-speech"></a>Zamiana tekstu na mowę

[Tekst na mowę](text-to-speech.md) (TTS) lub syntezy mowy interfejsu API konwertuje zwykłego tekstu na mowę pomiarowe naturalnego, dostarczane do aplikacji w pliku audio. Wiele głosy, zróżnicowanie płci lub akcent, są dostępne dla wielu języków.

Interfejs API obsługuje tagi mowy syntezy Markup Language (SSML), można określić dokładną Fonetyczne brzmienie powodującymi wyrazów. SSML może również oznaczać właściwości mowy (w tym nacisk, szybkość woluminu, płci i wysokością) w tekst do prawej.

Poniżej przedstawiono typowe przypadki użycia tekst na mowę interfejsu API.

* Mowie jako alternatywne ekranu danych wyjściowych dla użytkowników niedowidzących
* Monitowanie o aplikacji do samochodu, takich jak nawigacji głosu
* Interfejsy użytkownika konwersacji w połączeniu z mowy API tekstu

Jeśli konieczne dialekt nieobsługiwany lub po prostu mają unikatowe głosu dla aplikacji, tekst na mowę interfejsu API obsługuje głosu niestandardowych modeli.

## <a name="speech-translation"></a>Tłumaczenie mowy

[Tłumaczenia mowy](speech-translation.md) interfejsu API może służyć do tłumaczenia przesyłania strumieniowego audio w pobliżu czasie rzeczywistym lub przetwarzania zarejestrowane mowy. W strumieniowym tłumaczenia, usługa zwraca wyników pośrednich, które mogą być wyświetlane użytkownikowi, aby wskazać tłumaczenia. Wyniki mogą być zwracane w postaci tekstu lub głosu.

Przypadki użycia w celu przetłumaczenia mowy są następujące.

* Wdrożenie aplikacji mobilnej "konwersacji" tłumaczenia lub urządzenia dla podróże 
* Podaj automatycznego tłumaczenia dla napisy nagrań audio i wideo

## <a name="speech-devices-sdk"></a>Zestaw Speech Devices SDK

Wraz z wprowadzeniem ujednoliconego mowy usługi firmy Microsoft i jej partnerzy oferują platformy zintegrowane oprogramowanie zoptymalizowane pod kątem tworzenia urządzenia z włączoną obsługą mowy: [mowy urządzeń SDK](speech-devices-sdk.md). Zestaw SDK jest odpowiednia do tworzenia urządzeń inteligentnych mowy dla wszystkich typów aplikacji.

SDK urządzenia mowy umożliwia tworzenie otoczenia urządzenia wyrazem wznawiania dostosowane, tak, aby wskaźnika, które wyzwala rejestracji dźwięku jest unikatowy dla oznakowanie. Umożliwia także audio wyższego poziomu przetwarzania ze źródeł wielokanałowe dokładniejsze rozpoznawania mowy, w tym pomijania szumu, daleko pola głosu i kształtowaniem wiązki.

## <a name="next-steps"></a>Kolejne kroki

Pobierz klucz bezpłatnej subskrypcji próbnej usługi mowy.

> [!div class="nextstepaction"]
> [Spróbuj bezpłatnie usługę mowy](get-started.md)
