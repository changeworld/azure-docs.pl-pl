---
title: Wyniki przewidywania — LUIS
titleSuffix: Azure Cognitive Services
description: Wynik przewidywania wskazuje stopień zaufania usługi interfejsu API LUIS w celu przewidywania wyników w oparciu o wypowiedź użytkownika.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: ccbdb60687bd81de57ff675634f9d124f10784e1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563953"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Wyniki przewidywania wskazują dokładność przewidywania dla zamiar i jednostek

Wynik przewidywania wskazuje stopień zaufania LUIS dla wyników przewidywania w oparciu o wypowiedź użytkownika.

Wynik prognoz jest między zero (0) i jeden (1). Przykład wysoce pewność oceny usługi LUIS wynosi 0,99. Przykładem wynik niskim poziomem pewności, że jest 0,01. 

|Wartość|Ufność|
|--|--|
|1|określony dopasowania|
|0,99.|o dużej pewności|
|0.01|niski ufności|
|0|określony błąd dopasowania|

Gdy wypowiedź w wyniku oceny niskim poziomie pewności, LUIS wyróżnia w [LUIS](luis-reference-regions.md) witryny sieci Web **intencji** zawierającej wskazywanego przez nią **etykietą intencji** opisane z czerwonym.

![Niezgodność wynik](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Ocenianie górnej intencji

Co prognozowania wypowiedź zwraca intencji oceniania top. To prognozowanie to liczbowe porównanie wyników przewidywania. 2 pierwsze wyniki mogą mieć bardzo małą różnicę między nimi. LUIS nie wskazuje tej bliskości innej niż zwraca górny wynik.  

## <a name="return-prediction-score-for-all-intents"></a>Zwraca wynik prognoz dla wszystkich intencji

Wynik testu lub punkt końcowy może obejmować wszystkie intencji. Ta konfiguracja jest ustawiona na [punktu końcowego](https://aka.ms/v1-endpoint-api-docs) z `verbose=true` pary nazwa/wartość ciągu zapytania.

## <a name="review-intents-with-similar-scores"></a>Przejrzyj intencji z podobne wyniki

Przeglądanie wyników dla wszystkich intencji to dobry sposób, aby sprawdzić, czy nie określono poprawny intencji tylko że następnego identyfikowany oceny jego celem jest znacznie niższe spójnie wypowiedzi.

Jeśli wiele intencji Zamknij prognozowania oceny na podstawie kontekstu wypowiedź LUIS może przełączać się między intencji. Aby rozwiązać ten problem, Kontynuuj dodawanie wyrażenia długości do każdego zamiaru z większą różnorodnością różnic kontekstowych lub aplikację kliencką, taką jak rozmowa bot, wybierz programowe opcje dotyczące obsługi 2 najważniejszych założeń.

2 intencje, które są zbyt blisko oceniane, mogą odwrócić ze względu na niedeterministyczne szkolenie. Najważniejsze wynik może stać się druga u góry, a drugi oceny najważniejszych może stać się pierwszym oceny najważniejszych. Aby uniknąć tej sytuacji, należy dodać przykład wyrażenia długości do każdego z dwóch najlepszych zamiar dla tego wypowiedź z wyborem słowa i kontekstem, który odróżnia 2 intencje. Dwa intencji powinny mieć o takiej samej liczby wypowiedzi przykład. Zasada mówi dla separacji w celu uniemożliwienia odwrócenie ze względu na szkolenie, jest 15% różnica w wyniki.

Możesz wyłączyć niedeterministyczne szkolenie, [przekształcenie ze wszystkimi danymi](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Różnice związane z przewidywaniami między różnymi sesjami szkoleniowymi

W przypadku uczenia tego samego modelu w innej aplikacji, a wyniki nie są takie same, różnica wynika z faktu, że jest to niedeterministyczne szkolenie (element losowości). Po drugie wszelkie nakładania się numerów wypowiedź na intencje więcej niż jeden oznacza, że najważniejsze przeznaczenie tego samego wypowiedź można zmienić w zależności od szkolenia.

Jeśli rozmowa bot wymaga określonego wyniku LUIS, aby wskazać wiarygodność, należy użyć różnicy wynikowej między dwoma najczęściej używanymi intencjami. Ta sytuacja zapewnia elastyczność w zakresie różnic w szkoleniu.

## <a name="e-exponent-notation"></a>Notacja E (wykładnik)

Wyniki prognozowania można używać notacji wykładnika *pojawiające się* powyżej 0 – 1 do zakresu, takich jak `9.910309E-07`. Ten wynik jest wskazaniem bardzo **małych** numer.

|Wynik notacji E |Rzeczywisty wynik|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Znaki interpunkcyjne

Znak interpunkcyjny jest tokenu oddzielnych w usługi LUIS. Wypowiedź, który zawiera kropkę na końcu, a wypowiedź, który nie zawiera kropki na końcu, to dwa oddzielne wyrażenia długości i mogą uzyskać dwa różne przewidywania. Upewnij się, że model obsługuje znaki interpunkcyjne, albo w [wypowiedzi przykład](luis-concept-utterance.md) (istnienie i nie ma znaków interpunkcyjnych) lub w [wzorców](luis-concept-patterns.md) gdzie jest łatwiej Ignoruj znaków interpunkcyjnych przy użyciu specjalnej składni: `I am applying for the {Job} position[.]`

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Dodaj jednostki](luis-how-to-add-entities.md) Aby dowiedzieć się więcej o sposobie dodawania jednostki z aplikacją usługi LUIS.
