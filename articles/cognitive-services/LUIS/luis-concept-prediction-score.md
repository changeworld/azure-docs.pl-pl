---
title: Wyniki prognozowania
titleSuffix: Language Understanding - Azure Cognitive Services
description: Wynik prognozowania wskazuje stopień ufności usługa interfejs API usługi LUIS ma dla wyników przewidywań oparte na wypowiedź użytkownika.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 383ce4c4248f7e21f745f503c74a29cb613983e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813989"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Wyniki prognozy wskazują na dokładność prognozowania intencje i podmioty

Wynik prognozowania wskazuje stopień ufności LUIS ma dla wyników przewidywań oparte na wypowiedź użytkownika.

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

Co prognozowania wypowiedź zwraca intencji oceniania top. Ta prognoz jest porównanie numeryczne wyniki prognozy. Najważniejsze wyniki 2 mogą mieć niewielkie różnice między nimi. Usługa LUIS nie oznacza to odległości niż zwracanie górnego wynik.  

## <a name="return-prediction-score-for-all-intents"></a>Zwraca wynik prognoz dla wszystkich intencji

Wynik testu lub punkt końcowy może obejmować wszystkie intencji. Ta konfiguracja jest ustawiona na [punktu końcowego](https://aka.ms/v1-endpoint-api-docs) z `verbose=true` pary nazwa/wartość ciągu zapytania.

## <a name="review-intents-with-similar-scores"></a>Przejrzyj intencji z podobne wyniki

Przeglądanie wyników dla wszystkich intencji to dobry sposób, aby sprawdzić, czy nie określono poprawny intencji tylko że następnego identyfikowany oceny jego celem jest znacznie niższe spójnie wypowiedzi.

Jeśli wiele intencji Zamknij prognozowania oceny na podstawie kontekstu wypowiedź LUIS może przełączać się między intencji. Aby rozwiązać tę sytuację, dodawanie wypowiedzi na intencje, każdy z wieloma różnymi kontekstowych różnice w dalszym ciągu, lub można aplikację klienta, takich jak czatbot, upewnij programowe wyborów dotyczących sposobu obsługi 2 intencji najważniejsze.

2 intencji, które są oceniane zbytnio, może być Odwróć ze względu na szkolenie deterministyczna. Najważniejsze wynik może stać się druga u góry, a drugi oceny najważniejszych może stać się pierwszym oceny najważniejszych. Aby uniknąć tej sytuacji, należy dodać wypowiedzi przykładzie do każdego z najważniejszych intencji dwóch dla tego wypowiedź wybór programu word i kontekstu, który odróżnia 2 intencji. Dwa intencji powinny mieć o takiej samej liczby wypowiedzi przykład. Zasada mówi dla separacji w celu uniemożliwienia odwrócenie ze względu na szkolenie, jest 15% różnica w wyniki.

Można wyłączyć niedeterministyczne szkolenia przez [szkolenia z wszystkimi danymi](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>W przypadku prognozy między różne sesje

Możesz później tego samego modelu w innej aplikacji, gdy wyniki nie są takie same, różnica ta ma ponieważ szkolenia niedeterministyczne (element losowości). Po drugie wszelkie nakładania się numerów wypowiedź na intencje więcej niż jeden oznacza, że najważniejsze przeznaczenie tego samego wypowiedź można zmienić w zależności od szkolenia.

Jeśli Twoje czatbot wymaga określonych oceny usługi LUIS do wskazania zaufania intencji, należy użyć wynik różnicy intencji dwa najważniejsze. Taka sytuacja zapewnia elastyczność dla zmian w szkoleniu.

## <a name="e-exponent-notation"></a>Notacja E (wykładnik)

Wyniki prognozowania można używać notacji wykładnika *pojawiające się* powyżej 0 – 1 do zakresu, takich jak `9.910309E-07`. Ten wynik jest wskazaniem bardzo **małych** numer.

|Wynik notacji E |Rzeczywisty wynik|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Znaki interpunkcyjne

Znak interpunkcyjny jest tokenu oddzielnych w usługi LUIS. Wypowiedź, która zawiera znak kropki na końcu, a wypowiedź, które nie zawierają kropki na końcu są dwa oddzielne wypowiedzi i może uzyskać dwa różne prognozy. Upewnij się, że model obsługuje znaki interpunkcyjne, albo w [wypowiedzi przykład](luis-concept-utterance.md) (istnienie i nie ma znaków interpunkcyjnych) lub w [wzorców](luis-concept-patterns.md) gdzie jest łatwiej Ignoruj znaków interpunkcyjnych przy użyciu specjalnej składni: `I am applying for the {Job} position[.]`

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Dodaj jednostki](luis-how-to-add-entities.md) Aby dowiedzieć się więcej o sposobie dodawania jednostki z aplikacją usługi LUIS.
