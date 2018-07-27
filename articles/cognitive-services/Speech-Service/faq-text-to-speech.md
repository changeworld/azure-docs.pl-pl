---
title: Często zadawane pytania dotyczące zamiana mowy na tekst usługi na platformie Azure
description: Poniżej przedstawiono odpowiedzi na najpopularniejsze pytania dotyczące zamiana mowy na tekst.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 8d70c4a359c713d6c5f46423193e9c9e7e1f3baf
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282861"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Zamiana tekstu na mowę — często zadawane pytania

Jeśli nie możesz znaleźć odpowiedzi na pytania w tych często zadawanych PYTAŃ, zapoznaj się z inne opcje pomocy technicznej [tutaj](support.md).

## <a name="general"></a>Ogólne

**Pytanie**: jaka jest różnica między modelami standardowych i niestandardowych voice?

**Odpowiedź**: standardowego głosu modeli (zwane) czcionki głosowe) przeszkoleni należące do danych firmy Microsoft i są już wdrożone w chmurze. Modeli usługi Custom voice umożliwia użytkownikom adaptowanie model średni i przesyłanie timbre i sposób wyrażenia zgodnie z style głosowej osoby mówiącej lub do nauczenia pełnej nowy model na podstawie danych szkolenia przygotowane przez użytkownika. Obecnie coraz więcej klienci chcą mieć jeden z rodzajem, marki głosu dla ich botów. Platforma tworzenia głosów niestandardowych voice jest właściwym wyborem dla tego.

**Pytanie**: gdzie rozpocząć jeśli chcę korzystać z modelu standardowego głosu?

**Odpowiedź**: więcej niż 80 modeli standardowego głosu w ponad 45 języków są dostępne za pośrednictwem żądania HTTP. Najpierw trzeba pobrać [klucz subskrypcji](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started). Aby wywołania REST do modeli głosowych wstępnie wdrożone, zapoznaj się z [szczegółowych informacji w tym miejscu](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

**Pytanie**: Aby użyć dostosowany model głosowy, jest interfejs API taka sama jak standardowy głosów?

**Odpowiedź**: w przypadku modelu głosowego niestandardowych, utworzeniu i wdrożeniu otrzymasz unikatowych punktów końcowych dla modelu. Należy określić punkt końcowy w żądaniach HTTP, mowy w aplikacjach za pomocą głosu. Te same funkcje dostępne za pośrednictwem interfejsu API REST usługi zamiany tekstu na mowę jest również dostępna dla niestandardowego punktu końcowego. Zobacz jak [tworzenia i używania niestandardowego punktu końcowego](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**Pytanie**: należy przygotować danych szkoleniowych do tworzenia modeli usługi custom voice na własną rękę?

**Odpowiedź**: musisz przygotować dane szkoleniowe dla siebie. Kolekcja danych mowy jest wymagana do utworzenia dostosowany model głosowy. Ta kolekcja składa się z zestawu plików audio, nagrań mowy i plik tekstowy w transkrypcji każdy plik dźwiękowy. Wynik Twojego głosu cyfrowego silnie zależy od jakości danych szkoleniowych. Aby wygenerować dobre głos TTS, ważne jest, nagrania są wykonywane w pokoju cichy z mikrofonu stałego wysokiej jakości. Spójne woluminu wypowiedzi szybkości, wypowiedzi pomysłu, nawet and consistency in i ekspresyjny mannerisms mowy są niezbędne do kompilowania doskonały cyfrowy głosu. Firma Microsoft zdecydowanie zaleca się mają głosy zapisane w studio rejestrowania.
W tej chwili firma Microsoft nie zapewniają obsługę rejestrowania w trybie online i nie ma żadnych zaleceń dotyczących studio rejestrowania. Format wymagań, zobacz [sposób przygotowania, nagrania i transkrypcje](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts)
 
**Pytanie**: jakie skryptów należy używać do rejestrowania danych mowy do trenowania niestandardowych voice? 

**Odpowiedź**: skrypty do nagrywanie głosu nie jest ograniczona. Aby zarejestrować mowy, można użyć własnych skryptów. Po prostu upewnij się, że mają wystarczającą ilość fonetycznych pokrycia w Twoich danych mowy. To w opracowywaniu niestandardowych voice, można zacząć od małej ilości danych mowy, który może być 50 różnych zdań (o 3 – 5 minut mowy). Podaj większej ilości danych, stosować bardziej naturalne Twojego głosu. Możesz rozpocząć szkolenie czcionki głosowe pełną po podaniu nagrania zdania ponad 2000 (około 3 – 4 godziny mowy). Aby uzyskać pełne głosu wysokiej jakości, należy przygotować nagrania ponad 6000 zdań (około 8 – 10 godzin mowy).  
Firma Microsoft oferuje dodatkowe usługi, które ułatwiają przygotowanie skryptów do rejestrowania. Skontaktuj się z pomocą [Custom Voice techniczną](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) dla zapytania.

**Pytanie**: co zrobić, jeśli konieczne zapewnienie większej współbieżności niż domyślna wartość lub treści oferowanych w portalu?

**Odpowiedź**: można zwiększać z przyrostem równym 20 równoczesnych żądań modelu. Skontaktuj się z pomocą [Custom Voice techniczną](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) dla zapytania o wyższych skalowania.

**Pytanie**: można pobrać swój model i uruchomić go lokalnie?

**Odpowiedź**: modeli nie mogą być pobierane i wykonywany lokalnie.

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów](troubleshooting.md)
* [Informacje o wersji](releasenotes.md)
