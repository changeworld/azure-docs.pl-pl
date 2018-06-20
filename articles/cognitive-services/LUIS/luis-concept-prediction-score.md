---
title: Zrozumienie prognozowania wynik zwracany przez LUIS - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, co wynik prognozowania oznacza LUIS
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 31c101a23892df8599b8cdc0f67647fefb969490
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265992"
---
# <a name="prediction-score"></a>Wynik prognozowania
Wynik prognozowania wskazuje stopień gwarancją LUIS ma wyniki prognozowania. 

Wynik prognozowania jest zwykle między zero (0) i jednego (1). Przykład upewnieniu wysokiej wynik LUIS wynosi 0,99. Przykładem wynik niski zaufania jest 0,01. 

|Wartość wyniku|Ufność|
|--|--|
|1|określoną dopasowania|
|0,99.|wysokiego zaufania|
|0.01|niski zaufania|
|0|Niezgodność określoną|

Gdy utterance powoduje wynik zaufania niski, LUIS wyróżnia w [LUIS] [ LUIS] witryny sieci Web **zamiar** strony z wskazywanego przez nią **zamiar etykietą**  opisane czerwonym. 

![Niezgodność wynik](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Celem oceniania góry
Każdy prognozowania utterance zwraca zamiar oceniania top. To porównanie liczbowe wyniki prognozowania. Górny wyniki dwóch mają bardzo niewielkie różnice między nimi. LUIS nie oznacza to zbliżeniowe niż zwracania wyników.  

Jeśli użytkownik interesuje zbliżeniowe wyniki top, powinien zwrócić wyników dla wszystkich lokalizacji docelowych. Można dodać zniesławiających do dwóch opcji, które wskazują różnice między nimi z wyborem word i rozmieszczenie lub aplikacja LUIS telefonicznej, takich jak chatbot, podejmować decyzje programowe dotyczące sposobu obsługi dwóch intencje najwyższego. 

## <a name="return-prediction-score-for-all-intents"></a>Zwraca wynik prognozowania dla wszystkich lokalizacji docelowych
Wynik testu lub punktu końcowego może zawierać wszystkich lokalizacji docelowych. Ta konfiguracja jest ustawiona na [punktu końcowego](https://aka.ms/v1-endpoint-api-docs) z `verbose=true` pary nazwa/wartość ciągu zapytania. 

## <a name="review-intents-with-similar-scores"></a>Przejrzyj intencje z podobne wyniki
Przeglądanie wyników dla wszystkich lokalizacji docelowych jest dobry sposób, aby sprawdzić, czy nie określono poprawny zamiar tylko że następne zidentyfikowane wynik jego celem jest znacznie niższy spójnie dla zniesławiających. 

Jeśli wiele opcji Zamknij przewidywania wyników, na podstawie kontekstu utterance LUIS może przełączać się między lokalizacji docelowych. Aby rozwiązać ten problem, nadal można dodać zniesławiających do każdego zamiar z wieloma różnymi różnic kontekstowych.   

## <a name="e-exponent-notation"></a>Notacja E (wykładnik)

Wyniki prognozowania można użyć notacji wykładnika *wyświetlaniu* powyżej 0-1 należeć do zakresu, takich jak `9.910309E-07`. Ten wynik będzie wskazywać na bardzo **małych** numer.

|Wynik notacji E |Wynik rzeczywiste|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="differences-with-predictions"></a>W przypadku prognoz
Podczas uczenia tego samego modelu w innej aplikacji, a wyniki nie są w tym samym, jest to, ponieważ jest elementem losowości w szkolenia. Po drugie wszelkie nakładanie się utterance do więcej niż jeden profil konwersji oznacza, że celem top dla tego samego utterance można zmieniać w oparciu o szkolenia.

Jeśli Twoje chatbot wymaga określonych wynik LUIS wskazująca zaufania celem, zamiast tego należy używać wynik różnicy top dwóch opcji. Zapewnia to elastyczność zmienności szkolenia. 

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Dodaj jednostki](luis-how-to-add-entities.md) Aby dowiedzieć się więcej o sposobie dodawania do aplikacji LUIS jednostek.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions