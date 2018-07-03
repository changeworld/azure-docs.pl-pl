---
title: Co to jest usługa mowy (wersja zapoznawcza)? | Microsoft Docs
description: 'Usługa rozpoznawania mowy, częścią firmy Microsoft Cognitive Services, łączy w sobie kilka usług mowy platformy Azure, które były wcześniej dostępne oddzielnie: rozpoznawania mowy Bing (wchodzących w skład rozpoznawania mowy i zamiany tekstu na mowę), niestandardowe mowy i tłumaczenie mowy.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: ccdcdeeaf4ac8730be4f9e3ee648dc41c2a02641
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345162"
---
# <a name="what-is-the-speech-service-preview"></a>Co to jest usługa mowy (wersja zapoznawcza)?

Usługa rozpoznawania mowy, częścią firmy Microsoft Cognitive Services, łączy w sobie kilka usług mowy platformy Azure, które były wcześniej dostępne oddzielnie: rozpoznawania mowy Bing (wchodzących w skład rozpoznawania mowy i zamiany tekstu na mowę), niestandardowe mowy i tłumaczenie mowy. Takie jak jego prekursorów usługi mowy jest obsługiwane przez technologie używane w innych produktów firmy Microsoft, w tym Cortana i Microsoft Office.

> [!NOTE]
> Usługa rozpoznawania mowy jest obecnie w publicznej wersji zapoznawczej. Wróć tutaj regularne aktualizacje i dokumentację, próbki kodu dodatkowe i inne.

Z jedną subskrypcją ujednolicone usługi mowy daje deweloperom prosty sposób, aby zapewnić ich aplikacje zaawansowanych włączony mowy funkcji. Aplikacje można teraz funkcji poleceń głosowych, transkrypcji, funkcją dyktowania, synteza mowy i tłumaczenia.

|Funkcja|Opis|
|-|-|
|Zamiana mowy na tekst|Konwertuje ciągłe ludzkiej mowy na tekst, który może służyć jako dane wejściowe do aplikacji. Można zintegrować z [usługi Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) ma być intencji użytkownika z wypowiedzi.|
|Zamiana tekstu na mowę|Konwertuje tekst na pliki audio brzmiącą naturalnego syntezatora mowy.|
|Mowy&nbsp;tłumaczenia|Podaj tłumaczenia mowy do innych języków z danymi wyjściowymi tekstu i mowy.|

## <a name="using-the-speech-service"></a>Przy użyciu usługi mowy

Usługa rozpoznawania mowy jest udostępniane na dwa sposoby. [Zestaw SDK](speech-sdk.md) ukrywa szczegóły protokołów sieciowych. [Interfejsu API REST](rest-apis.md) współdziała z dowolnego języka programowania, ale nie oferuje wszystkie funkcje oferowane przez zestaw SDK.

|<br>Metoda|Mowa<br>do tekstu|Tekst<br>Mowa|Mowa<br>Tłumaczenie|<br>Opis|
|-|-|-|-|-|
|[Zestawy SDK](speech-sdk.md)|Yes|Nie|Yes|Biblioteki dla określonych języków programowania, które upraszczają proces tworzenia.|
|[REST](rest-apis.md)|Yes|Yes|Nie|Prosty oparty na protokole HTTP interfejs API, która ułatwia dodawanie funkcji rozpoznawania mowy do aplikacji.|

## <a name="speech-to-text"></a>Zamiana mowy na tekst

[Zamiana mowy na tekst](speech-to-text.md) (STT) lub transcribes interfejsu API rozpoznawania mowy, strumieni audio na tekst, który aplikacja może akceptować jako dane wejściowe. Aplikacja można następnie wprowadzić ten tekst do dokumentu lub potraktować go jako polecenie.

Zamiana mowy na tekst oddzielnie zoptymalizowany do interaktywnego, konwersacji oraz dyktowanie scenariuszy. Poniżej przedstawiono typowe przypadki użycia usługi rozpoznawania mowy do interfejsu API tłumaczenia tekstu. 

* Rozpoznaje krótki wypowiedź, takich jak polecenia, bez wyników pośrednich
* Transkrypcja wypowiedź długie, zarejestrowane wcześniej, takich jak wiadomości poczty głosowej
* Transkrypcja mowy przesyłania strumieniowego w czasie rzeczywistym, wyniki częściowe, funkcją dyktowania
* Określić, co użytkownicy chcą są oparte na żądanie prowadzone języka naturalnego

Zamiana mowy na tekst interfejs API obsługuje transkrypcja mowy interakcyjne przy użyciu rozpoznawania ciągłej w czasie rzeczywistym oraz wyników pośrednich. Obsługuje on również rozpoznawanie końca wypowiedzi, opcjonalną automatyczną interpunkcję i zamianę tekstu na wielkie litery, maskowanie niestosownych wyrażeń oraz normalizację tekstu.

Można dostosować, zamiana mowy na tekst akustyczne i modeli językowych, aby pomieścić specjalistycznego słownictwa, hałas środowisk i różne sposoby mówić.

## <a name="text-to-speech"></a>Zamiana tekstu na mowę

[Zamiany tekstu na mowę](text-to-speech.md) (TTS) lub synteza mowy, interfejs API konwertuje tekst zwykły do naturalnym brzmiącą mowę, na dostarczane do aplikacji w pliku audio. Wiele głosów, różnicowanie płci lub akcent, są dostępne dla wielu języków.

Interfejs API obsługuje tagi mowy syntezy Markup Language (SSML), dzięki czemu można określić dokładne wymowa fonetyczna problematycznych wyrazów. Za pomocą języka SSML można również bezpośrednio w tekście oznaczać właściwości mowy (w tym nacisk, szybkość, głośność, płeć i wysokość).

Poniżej przedstawiono typowe przypadki użycia dla zamiany tekstu na mowę interfejsu API.

* Mowie jako dane wyjściowe ekranu alternatywnych dla użytkowników niedowidzących
* Głos monitowania dla aplikacji w samochodzie, takie jak nawigacja
* Interfejsy użytkownika konwersacji z usługą mowy do interfejsu API tłumaczenia tekstu

Jeśli trzeba nieobsługiwany dialekt lub po prostu ma głos unikatowy dla aplikacji, zamiana tekstu na mowę interfejs API obsługuje modeli usługi custom voice.

## <a name="speech-translation"></a>Tłumaczenie mowy

[Tłumaczenia mowy](speech-translation.md) interfejsu API może służyć do translacji strumienie audio niemal w czasie rzeczywistym lub do przetwarzania mowy zarejestrowane. W transmisji strumieniowej tłumaczenia, usługa zwraca wyników pośrednich, które mogą zostać wyświetlone użytkownikowi, aby wskazać tłumaczenia. Wyniki mogą być zwracane w postaci tekstu lub głosu.

Przypadki użycia tłumaczenia mowy, m.in.

* Implementowanie aplikacji mobilnej "konwersacji" tłumaczenia lub urządzenie dla podróże 
* Podaj automatycznego tłumaczenia dla napisy nagrań audio i wideo

## <a name="speech-devices-sdk"></a>Zestaw Speech Devices SDK

Wraz z wprowadzeniem ujednolicone usługi mowy firmy Microsoft i jej partnerzy oferują platforma zintegrowane sprzętu i oprogramowania, zoptymalizowana pod kątem tworzenia urządzenia z włączoną obsługą mowy: [zestawu Speech Devices SDK](speech-devices-sdk.md). Ten zestaw SDK umożliwia projektowanie urządzeń z inteligentną obsługą mowy dla wszystkich typów aplikacji.

Zestawu Speech Devices SDK umożliwia tworzenie otoczenia urządzeń za pomocą wyrazem wznawiania dostosowany tak, aby wskaźnika, który wywołuje przechwytywania audio jest unikatowy dla Twojej marki. Zestaw zapewnia także doskonałe przetwarzanie audio ze źródeł wielokanałowych, co zwiększa dokładność rozpoznawania mowy, a także pomijanie szumu, głos dalekiego pola i kształtowanie wiązki.

Zestaw SDK zależy od gniazda sieci web przy użyciu portu 443.

## <a name="next-steps"></a>Następne kroki

Pobierz klucz subskrypcji wersji próbnej usługi mowy.

> [!div class="nextstepaction"]
> [Wypróbuj bezpłatnie usługę rozpoznawania mowy](get-started.md)
