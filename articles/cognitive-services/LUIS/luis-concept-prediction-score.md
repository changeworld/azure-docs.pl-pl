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
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: b360bc82b80e834492b524acc5c4535b0409eda1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280820"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Wyniki przewidywania wskazują dokładność przewidywania dla zamiar i jednostek

Wynik przewidywania wskazuje, że stopień zaufania LUIS jest przeznaczony do przewidywania wyników wypowiedź użytkownika.

Wynik prognoz jest między zero (0) i jeden (1). Przykład wysoce pewność oceny usługi LUIS wynosi 0,99. Przykładem wynik niskim poziomem pewności, że jest 0,01. 

|Wartość|Confidence|
|--|--|
|1|określony dopasowania|
|0,99.|o dużej pewności|
|0,01|niski ufności|
|0|określony błąd dopasowania|

## <a name="top-scoring-intent"></a>Ocenianie górnej intencji

Co prognozowania wypowiedź zwraca intencji oceniania top. To prognozowanie to liczbowe porównanie wyników przewidywania. 

## <a name="proximity-of-scores-to-each-other"></a>Bliskość wyników do siebie

2 pierwsze wyniki mogą mieć bardzo małą różnicę między nimi. LUIS nie wskazuje tej bliskości innej niż zwraca górny wynik.  

## <a name="return-prediction-score-for-all-intents"></a>Zwraca wynik prognoz dla wszystkich intencji

Wynik testu lub punkt końcowy może obejmować wszystkie intencji. Ta konfiguracja jest ustawiana w punkcie końcowym przy użyciu poprawnej pary nazwa/wartość QueryString.

|Interfejs API przewidywania|Nazwa QueryString|
|--|--|
|V3|`show-all-intents=true`|
|Wersja 2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Przejrzyj intencji z podobne wyniki

Przegląd oceny dla wszystkich intencji to dobry sposób na sprawdzenie, czy nie tylko jest to poprawna metoda określona, ale że wynik następnego zidentyfikowanego celu jest znacząco i ciągle niższy dla wyrażenia długości.

Jeśli wiele intencji Zamknij prognozowania oceny na podstawie kontekstu wypowiedź LUIS może przełączać się między intencji. Aby rozwiązać ten problem, Kontynuuj dodawanie wyrażenia długości do każdego zamiaru z większą różnorodnością różnic kontekstowych lub aplikację kliencką, taką jak rozmowa bot, wybierz programowe opcje dotyczące obsługi 2 najważniejszych założeń.

2 intencje, które są zbyt blisko oceniane, mogą odwrócić ze względu na **niedeterministyczne szkolenie**. Najważniejsze wynik może stać się druga u góry, a drugi oceny najważniejszych może stać się pierwszym oceny najważniejszych. Aby uniknąć tej sytuacji, należy dodać przykład wyrażenia długości do każdego z dwóch najlepszych zamiar dla tego wypowiedź z wyborem słowa i kontekstem, który odróżnia 2 intencje. Dwa intencji powinny mieć o takiej samej liczby wypowiedzi przykład. Zasada mówi dla separacji w celu uniemożliwienia odwrócenie ze względu na szkolenie, jest 15% różnica w wyniki.

Możesz wyłączyć **niedeterministyczne szkolenie** , [przekształcenie ze wszystkimi danymi](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Różnice związane z przewidywaniami między różnymi sesjami szkoleniowymi

W przypadku uczenia tego samego modelu w innej aplikacji, a wyniki nie są takie same, różnica wynika z faktu, że jest to **niedeterministyczne szkolenie** (element losowości). Po drugie wszelkie nakładania się numerów wypowiedź na intencje więcej niż jeden oznacza, że najważniejsze przeznaczenie tego samego wypowiedź można zmienić w zależności od szkolenia.

Jeśli rozmowa bot wymaga określonego wyniku LUIS, aby wskazać wiarygodność, należy użyć różnicy wynikowej między dwoma najczęściej używanymi intencjami. Ta sytuacja zapewnia elastyczność w zakresie różnic w szkoleniu.

Możesz wyłączyć **niedeterministyczne szkolenie** , [przekształcenie ze wszystkimi danymi](luis-how-to-train.md#train-with-all-data).

## <a name="e-exponent-notation"></a>Notacja E (wykładnik)

Wyniki przewidywania mogą korzystać z notacji wykładniczej, która znajduje _się powyżej zakresu_ 0-1, takiego jak `9.910309E-07`. Ten wynik wskazuje bardzo **małą** liczbę.

|Wynik notacji E |Rzeczywisty wynik|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Znaki interpunkcyjne

[Dowiedz się więcej](luis-concept-utterance.md#punctuation-marks) na temat używania lub ignorowania interpunkcji. 

## <a name="next-steps"></a>Następne kroki

Zobacz [Dodawanie jednostek](luis-how-to-add-entities.md) , aby dowiedzieć się więcej na temat dodawania jednostek do aplikacji Luis.
