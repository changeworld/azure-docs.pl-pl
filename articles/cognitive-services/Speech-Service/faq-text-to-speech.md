---
title: Często zadawane pytania dotyczące usługi zamiany tekstu na mowę na platformie Azure
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na najpopularniejsze pytania dotyczące usługi zamiany tekstu na mowę.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: d93de4ff3f6dd00ac049ea9d83aebb7aad07137e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60539013"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Zamiana tekstu na mowę — często zadawane pytania

Jeśli nie możesz znaleźć odpowiedzi na pytania w tych często zadawanych PYTAŃ, zapoznaj się z [inne opcje pomocy technicznej](support.md).

## <a name="general"></a>Ogólne

**Pyt.: Jaka jest różnica między modelu standardowego głosu i modelu niestandardowego głosu?**

**Odp.:** Model głosowy standardowego (nazywanego również *czcionka głosowa*) przeszkoleni przy użyciu danych należącymi do firmy Microsoft i została już wdrożona w chmurze. Aby dostosować model średni i Przenieś timbre i wyrażenia stylu głosu głośnika lub wytrenuj pełne, nowy model na podstawie danych szkolenia przygotowane przez użytkownika, można użyć modelu niestandardowego głosu. Obecnie coraz więcej klientów mają głosu jednego z rodzajem, marki dla ich botów. Niestandardowe platforma tworzenia głosowych jest dobrym wyborem dla tej opcji.

**Pyt.: Gdzie rozpocząć, jeśli chcę korzystać z modelu standardowego głosu?**

**Odp.:** Za pomocą żądań HTTP, dostępnych jest więcej niż 80 modeli standardowego głosu w ponad 45 językach. Najpierw pobierz [klucz subskrypcji](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Aby wprowadzić wywołania REST do modeli głosowych predeployed, zobacz [interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

**Pyt.: Aby użyć dostosowany model głosowy, jest interfejs API taka sama jak ta, która jest używana do standardowego głosów?**

**Odp.:** Po utworzeniu i wdrożeniu modelu niestandardowego głosu uzyskasz unikatowych punktów końcowych dla modelu. Aby za pomocą głosu głosu w aplikacjach, należy określić punkt końcowy w żądaniach HTTP. Te same funkcje, które są dostępne w interfejsie API REST usługi zamiany tekstu na mowę, jest dostępna dla niestandardowego punktu końcowego. Dowiedz się, jak [tworzenia i używania niestandardowego punktu końcowego](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**Pyt.: Należy przygotować danych szkoleniowych do tworzenia modeli usługi custom voice na własną rękę?**

**Odp.:** Tak, należy przygotować dane szkoleniowe samodzielnie modelu niestandardowego głosu.

Kolekcja danych mowy jest wymagana do utworzenia dostosowany model głosowy. Ta kolekcja składa się z zestawu plików audio, nagrań mowy i tekst pliku transkrypcji każdy plik dźwiękowy. Wynik Twojego głosu cyfrowego rolę odgrywa jakość danych szkoleniowych. Aby uzyskać dobre głosu zamiany tekstu na mowę, jest ważne, czy nagrania są przekazywane w pokoju cichy przy użyciu mikrofonu stałego wysokiej jakości. Wolumin spójne wypowiedzi szybkości i wypowiedzi pomysłu, nawet and consistency in i ekspresyjny mannerisms mowy są niezbędne do tworzenia wspaniałych głosu cyfrowych. Zdecydowanie zaleca się zapisywania głosy w studio rejestrowania.

Obecnie firma Microsoft nie zapewniają obsługę rejestrowania w trybie online ani nie ma żadnych zaleceń dotyczących studio rejestrowania. Format wymagań, zobacz [sposób przygotowania, nagrania i transkrypcje](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts).

**Pyt.: Jakie skryptów należy używać do rejestrowania danych mowy do trenowania niestandardowych voice?**

**Odp.:** Skrypty do nagrywanie głosu nie jest ograniczona. Aby zarejestrować mowy, można użyć własnych skryptów. Po prostu upewnij się, że masz wystarczające pokrycie fonetycznych w Twoich danych mowy. To w opracowywaniu niestandardowych voice, można uruchomić przy użyciu małych ilości danych mowy, która może być 50 różnych zdań (około 3 – 5 minut mowy). Podaj większej ilości danych, stosować bardziej naturalne Twojego głosu. Możesz rozpocząć szkolenie czcionki głosowe pełną po podaniu nagrania zdania ponad 2000 (około 3 – 4 godziny mowy). Aby uzyskać pełne głos, wysokiej jakości, należy przygotować nagrania ponad 6000 zdań (około 8 – 10 godzin mowy).

Firma Microsoft oferuje dodatkowe usługi, które ułatwiają przygotowanie skryptów do rejestrowania. Skontaktuj się z pomocą [Custom Voice techniczną](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) dla zapytania.

**Pyt.: Co zrobić, jeśli potrzebne jest zapewnienie większej współbieżności niż wartość domyślna lub treści oferowanych w portalu?**

**Odp.:** Możesz skalować w górę w przyrostach co 20 równoczesnych żądań modelu. Skontaktuj się z pomocą [Custom Voice techniczną](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) dla zapytania o wyższych skalowania.

**Pyt.: Czy mogę pobrać swój model i uruchomić go lokalnie?**

**Odp.:** Modele nie pobierane i wykonywany lokalnie.

**Pyt.: Moje żądania są ograniczane?**

**Odp.:** Interfejs API REST ogranicza żądania do 25 na 5 sekund. Szczegółowe informacje można znaleźć w naszych stron [zamiany tekstu na mowę](text-to-speech.md). 

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów](troubleshooting.md)
* [Informacje o wersji](releasenotes.md)
