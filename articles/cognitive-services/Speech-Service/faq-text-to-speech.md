---
title: Często zadawane pytania dotyczące usługi zamiany tekstu na mowę na platformie Azure
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na najpopularniejsze pytania dotyczące usługi zamiany tekstu na mowę.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: f8d80ab189d8ed1f4b153e81963ef31cc5f685b8
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470051"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Zamiana tekstu na mowę — często zadawane pytania

Jeśli nie możesz znaleźć odpowiedzi na pytania w tych często zadawanych PYTAŃ, zapoznaj się z [inne opcje pomocy technicznej](support.md).

## <a name="general"></a>Ogólne

**P: jaka jest różnica między modelu standardowego głosu i modelu niestandardowego głosu?**

**A**: model głosowy standardowego (nazywanego również *czcionka głosowa*) przeszkoleni przy użyciu danych należącymi do firmy Microsoft i została już wdrożona w chmurze. Aby dostosować model średni i Przenieś timbre i wyrażenia stylu głosu głośnika lub wytrenuj pełne, nowy model na podstawie danych szkolenia przygotowane przez użytkownika, można użyć modelu niestandardowego głosu. Obecnie coraz więcej klientów mają głosu jednego z rodzajem, marki dla ich botów. Niestandardowe platforma tworzenia głosowych jest dobrym wyborem dla tej opcji.

**Pyt.: gdzie rozpocząć, jeśli chcę korzystać z modelu standardowego głosu?**

**A**: więcej niż 80 modeli standardowego głosu w ponad 45 języków są dostępne za pośrednictwem żądania HTTP. Najpierw pobierz [klucz subskrypcji](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Aby wprowadzić wywołania REST do modeli głosowych predeployed, zobacz [interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

**P: Chcę użyć dostosowany model głosowy, czy interfejsu API taka sama jak ta, która jest używana do standardowego głosów?**

**A**: po utworzeniu i wdrożeniu modelu niestandardowego głosu uzyskasz unikatowych punktów końcowych dla modelu. Aby za pomocą głosu głosu w aplikacjach, należy określić punkt końcowy w żądaniach HTTP. Te same funkcje, które są dostępne w interfejsie API REST usługi zamiany tekstu na mowę, jest dostępna dla niestandardowego punktu końcowego. Dowiedz się, jak [tworzenia i używania niestandardowego punktu końcowego](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**P: czy należy przygotować danych szkoleniowych do tworzenia modeli usługi custom voice na własną rękę?**

**A**: tak, należy przygotować dane szkoleniowe samodzielnie modelu niestandardowego głosu.

Kolekcja danych mowy jest wymagana do utworzenia dostosowany model głosowy. Ta kolekcja składa się z zestawu plików audio, nagrań mowy i tekst pliku transkrypcji każdy plik dźwiękowy. Wynik Twojego głosu cyfrowego rolę odgrywa jakość danych szkoleniowych. Aby uzyskać dobre głosu zamiany tekstu na mowę, jest ważne, czy nagrania są przekazywane w pokoju cichy przy użyciu mikrofonu stałego wysokiej jakości. Wolumin spójne wypowiedzi szybkości i wypowiedzi pomysłu, nawet and consistency in i ekspresyjny mannerisms mowy są niezbędne do tworzenia wspaniałych głosu cyfrowych. Zdecydowanie zaleca się zapisywania głosy w studio rejestrowania.

Obecnie firma Microsoft nie zapewniają obsługę rejestrowania w trybie online ani nie ma żadnych zaleceń dotyczących studio rejestrowania. Format wymagań, zobacz [sposób przygotowania, nagrania i transkrypcje](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts).

**P: jakie skryptów należy używać do rejestrowania danych mowy do trenowania niestandardowych voice?**

**A**: skrypty do nagrywanie głosu nie jest ograniczona. Aby zarejestrować mowy, można użyć własnych skryptów. Po prostu upewnij się, że masz wystarczające pokrycie fonetycznych w Twoich danych mowy. To w opracowywaniu niestandardowych voice, można uruchomić przy użyciu małych ilości danych mowy, która może być 50 różnych zdań (około 3 – 5 minut mowy). Podaj większej ilości danych, stosować bardziej naturalne Twojego głosu. Możesz rozpocząć szkolenie czcionki głosowe pełną po podaniu nagrania zdania ponad 2000 (około 3 – 4 godziny mowy). Aby uzyskać pełne głos, wysokiej jakości, należy przygotować nagrania ponad 6000 zdań (około 8 – 10 godzin mowy).

Firma Microsoft oferuje dodatkowe usługi, które ułatwiają przygotowanie skryptów do rejestrowania. Skontaktuj się z pomocą [Custom Voice techniczną](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) dla zapytania.

**P: co mogę zrobić, jeśli potrzebne jest zapewnienie większej współbieżności niż wartość domyślna lub treści oferowanych w portalu?**

**A**: można zwiększać z przyrostem równym 20 równoczesnych żądań modelu. Skontaktuj się z pomocą [Custom Voice techniczną](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) dla zapytania o wyższych skalowania.

**P: czy mogę Pobierz swój model i uruchomić go lokalnie?**

**A**: modeli nie mogą być pobierane i wykonywany lokalnie.

**P: czy Moje żądania są ograniczane?**

**A**: interfejs API REST limity żądań do 25 na 5 sekund. Szczegółowe informacje można znaleźć w naszych stron [zamiany tekstu na mowę](text-to-speech.md). 

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów](troubleshooting.md)
* [Informacje o wersji](releasenotes.md)
