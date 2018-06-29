---
title: Często zadawane pytania dotyczące mowy usługi tekst na platformie Azure | Dokumentacja firmy Microsoft
description: Poniżej przedstawiono odpowiedzi na pytania najpopularniejszych o mowy na tekst.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 4a29435c0ace79fc3a5d3a5a42a0e91bdbc8da5e
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082828"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Tekst na mowę, często zadawane pytania

Jeśli nie możesz znaleźć odpowiedzi na pytania w tych często zadawanych PYTAŃ, spróbuj pytaniem społeczności niestandardowej usługi rozpoznawania mowy na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) i [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Ogólne

**Pytanie**: jaka jest różnica między modelami głosu standardowe i niestandardowe?

**Odpowiedzi**: standardowego głosu modeli () czcionki głosu) przeprowadzono uczenia z właścicielem danych firmy Microsoft i są już wdrożone w chmurze. Modele niestandardowe głosu Zezwalaj użytkownikowi dostosowanie model średni i Przenieś timbre i sposób wyrażenia zgodnie z styl głosu prelegenta lub w celu przeszkolenia pełnej nowy model na podstawie danych szkoleniowych przygotowane przez użytkownika. Dzisiaj coraz więcej klienci chcą mieć jeden z rodzajem, marką głosu dla ich robotów. Niestandardowe głos tworzenia platformy przydaje się do tego.

**Pytanie**: gdzie uruchomić Aby użyć standardowego głosu modelu?

**Odpowiedzi**: więcej niż 80 modeli standardowego głosu w ciągu 45 języki są dostępne za pośrednictwem żądania HTTP. Najpierw należy pobrać [klucza subskrypcji](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started). W celu wykonywania wywołań REST modele wstępnie wdrożonej głosowych, zapoznaj się [szczegóły tutaj](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

**Pytanie**: Aby korzystać z modelu głosowych, jest interfejs API taka sama jak standardowe głosy?

**Odpowiedzi**: gdy model niestandardowych głosu utworzeniu i wdrożeniu, otrzyma unikatowy punkt końcowy dla modelu. Należy określić punkt końcowy w Twojej żądań HTTP na potrzeby głosu mowy w aplikacjach. Te same funkcje dostępne za pośrednictwem interfejsu API REST usługi tekst na mowę, jest również dostępny do niestandardowych punktu końcowego. Zobacz temat jak [tworzenia i używania niestandardowych punktu końcowego](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**Pytanie**: należy przygotować dane szkoleniowe tworzenia modeli niestandardowych głosu samodzielnie?

**Odpowiedzi**: musisz przygotować danych szkoleniowych do siebie. Kolekcja danych mowy jest wymagana do utworzenia modelu głosowych. Ta kolekcja składa się z zestawu plików audio nagrań mowy i plik tekstowy z przekształcania każdego pliku audio. Wynik cyfrowe głosu zależy silnie jakości danych szkoleniowych. Do tworzenia dobrej głos TTS, ważne jest, że nagrania są wykonywane w pokoju quiet mikrofon stałego wysokiej jakości. Spójne woluminu mówiąc szybkość mówiąc gęstość i nawet spójność obszerne mannerisms mowy są niezbędne do tworzenia niezwykłych głosu cyfrowego. Zdecydowanie zaleca się ma głosy zapisane w studio rejestrowania.
Obecnie firma Microsoft nie zapewniają obsługę rejestrowania w trybie online lub mieć żadnych zaleceń studio rejestrowania. Wymagania formatu zobacz [sposób przygotowania nagrań i zapisy rozmów](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts)
 
**Pytanie**: skrypty, jakie należy używać do rejestrowania danych mowy szkolenia głosu niestandardowych? 

**Odpowiedzi**: skrypty do nagrywanie głosu nie jest ograniczona. Własnych skryptów służy do rejestrowania mowy. Po prostu upewnić się, że za mało fonetyczne pokrycia danych mowy. W celu przeszkolenia głosu niestandardowych, można uruchomić z małej ilości danych mowy, który może być 50 różnych zdań (około 3 – 5 min mowy). Zapewniają więcej danych, bardziej naturalne głosu. Można uruchomić w celu przeszkolenia czcionki pełne głosu podając nagrań ponad 2000 zdań (około 3 – 4 godziny mowy). Aby uzyskać pełne głosu wysokiej jakości, należy przygotować nagrań więcej niż 6000 zdań (około 8-10 godzin mowy).  
Firma Microsoft udostępnia dodatkowe usługi, aby pomóc w przygotowaniu skryptów do rejestrowania. Skontaktuj się z [głosu niestandardowe techniczną](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) dla zapytania.

**Pytanie**: gdzie należy współbieżności wyższa niż wartość domyślna lub co to jest oferowana w portalu?

**Odpowiedzi**: można skalować modelu w przyrostach 20 równoczesnych żądań. Skontaktuj się z [głosu niestandardowe techniczną](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) dla zapytania o wyższych skalowania.

**Pytanie**: można pobrać moich modelu i uruchom lokalnie?

**Odpowiedzi**: modeli nie może zostać pobrana i wykonywane lokalnie.

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów](troubleshooting.md)
* [Informacje o wersji](releasenotes.md)
