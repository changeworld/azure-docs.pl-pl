---
title: Wyniki prognozowania - LUIS
description: Wynik przewidywanie wskazuje stopień zaufania usługi interfejsu API usługi LUIS dla wyników przewidywania, na podstawie wypowiedź użytkownika.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 709a34f0a278d8a17267c7544583798d54167dad
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382376"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Wyniki prognozowania wskazują dokładność przewidywania intencji i jednostek

Wynik przewidywanie wskazuje stopień zaufania usługi LUIS ma dla przewidywania wyników wypowiedzi użytkownika.

Wynik przewidywania wynosi od zera (0) do jednego (1). Przykładem bardzo pewny wynik usługi LUIS jest 0.99. Przykładem niskiej pewności siebie jest 0,01.

|Wartość wyniku|Ufność|
|--|--|
|1|określony mecz|
|0.99|wysokie zaufanie|
|0,01|niskie zaufanie|
|0|określony niedopasowanie|

## <a name="top-scoring-intent"></a>Intencja najwyższej punktacji

Każda przewidywanie wypowiedź zwraca intencji najwyższej punktacji. Ta prognoza jest numeryczne porównanie wyników prognozowania.

## <a name="proximity-of-scores-to-each-other"></a>Bliskość punktów do siebie

Najlepsze wyniki 2 mogą mieć bardzo małą różnicę między nimi. Usługa LUIS nie wskazuje tejblidy innych niż zwracanie najwyższego wyniku.

## <a name="return-prediction-score-for-all-intents"></a>Wynik przewidywania zwrotu dla wszystkich intencji

Wynik testu lub punktu końcowego może zawierać wszystkie intencje. Ta konfiguracja jest ustawiana w punkcie końcowym przy użyciu poprawnej pary nazwa/wartość querystring.

|Interfejs API przewidywania|Nazwa sznurka zapytania|
|--|--|
|Wersja V3|`show-all-intents=true`|
|Wersja 2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Przejrzyj intencje z podobnymi wynikami

Przeglądanie wynik dla wszystkich intencji jest dobrym sposobem, aby sprawdzić, czy nie tylko jest poprawny zamiar zidentyfikowane, ale że następny zidentyfikowany wynik intencji jest znacznie i konsekwentnie niższe dla wypowiedzi.

Jeśli wiele intencji mają wyniki przewidywania zamknięcia, na podstawie kontekstu wypowiedź, usługa LUIS może przełączać się między intencjami. Aby rozwiązać tę sytuację, nadal dodawać wypowiedzi do każdego zamiaru z szerszej gamy różnic kontekstowych lub można mieć aplikacji klienckiej, takich jak bot czatu, dokonać programowych wyborów dotyczących sposobu obsługi 2 najlepszych intencji.

2 intencje, które są zbyt ściśle oceniane, może odwrócić ze względu na **niedeterministyczne szkolenia**. Najlepszy wynik może stać się drugim szczytem, a drugi najlepszy wynik może stać się pierwszym najlepszym wynikiem. Aby zapobiec tej sytuacji, dodaj wypowiedzi przykład do każdego z dwóch głównych intencji dla tego wypowiedź z wyboru wyrazu i kontekstu, który odróżnia 2 intencji. Dwie intencje powinny mieć o tej samej liczbie wypowiedzi przykład. Zasadą separacji, aby zapobiec inwersji z powodu treningu, jest 15% różnica w wynikach.

**Szkolenie niedeterministyczne** można wyłączyć, [szkoląc się ze wszystkimi danymi.](luis-how-to-train.md#train-with-all-data)

## <a name="differences-with-predictions-between-different-training-sessions"></a>Różnice z przewidywaniami między różnymi sesjami treningowymi

Podczas trenowania tego samego modelu w innej aplikacji, a wyniki nie są takie same, różnica ta jest, ponieważ istnieje **niedeterministyczne szkolenia** (element losowości). Po drugie wszelkie nakładanie się wypowiedź do więcej niż jeden zamiar oznacza, że górna intencja dla tego samego wypowiedź może ulec zmianie na podstawie szkolenia.

Jeśli twój czat bot wymaga określonego wyniku usługi LUIS, aby wskazać zaufanie w intencji, należy użyć różnicy wyników między dwoma najlepszymi intencjami. Sytuacja ta zapewnia elastyczność w zakresie różnic w szkoleniu.

**Szkolenie niedeterministyczne** można wyłączyć, [szkoląc się ze wszystkimi danymi.](luis-how-to-train.md#train-with-all-data)

## <a name="e-exponent-notation"></a>E (wykładnik) notacja

Wyniki prognozowania mogą używać notacji wykładniczej, _wyświetlanej_ powyżej zakresu `9.910309E-07`0-1, na przykład . Ten wynik jest wskaźnikiem bardzo **małej** liczby.

|Wynik notacji E |Rzeczywisty wynik|
|--|--|
|9.910309E-07|.0000009910309|

<a name="punctuation"></a>

## <a name="application-settings"></a>Ustawienia aplikacji

[Ustawienia aplikacji](luis-reference-application-settings.md) służą do kontrolowania sposobu przewidywania znaków diakrutycznych i znaków interpunkcyjnych.

## <a name="next-steps"></a>Następne kroki

Zobacz [Dodawanie jednostek,](luis-how-to-add-entities.md) aby dowiedzieć się więcej o dodaniu jednostek do aplikacji usługi LUIS.
