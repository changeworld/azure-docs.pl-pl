---
title: Zrozumienie wynik prognozowania zwracane przez usługi LUIS — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, co wynik prognozowania oznacza usługi LUIS
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 88d5eb22186248024a356610addab0d43f68a961
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887134"
---
# <a name="prediction-score"></a>Współczynnik przewidywania
Wynik prognozowania wskazuje stopień zaufania, LUIS, ma dla wyników przewidywań. 

Wynik prognoz jest zazwyczaj między zero (0) i jeden (1). Przykład wysoce pewność oceny usługi LUIS wynosi 0,99. Przykładem wynik niskim poziomem pewności, że jest 0,01. 

|Wartość|Ufność|
|--|--|
|1|określony dopasowania|
|0,99.|o dużej pewności|
|0.01|niski ufności|
|0|określony błąd dopasowania|

Gdy wypowiedź w wyniku oceny niskim poziomie pewności, LUIS wyróżnia w [LUIS](luis-reference-regions.md) witryny sieci Web **intencji** zawierającej wskazywanego przez nią **etykietą intencji** opisane z czerwonym. 

![Niezgodność wynik](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Ocenianie górnej intencji
Co prognozowania wypowiedź zwraca intencji oceniania top. To jest porównanie numeryczne wyniki prognozy. Najważniejsze dwa wyniki mogą mieć niewielkie różnice między nimi. Usługa LUIS nie oznacza to odległości niż zwracania wyników.  

Jeśli zajmującym się ochroną odległości oceny najważniejszych powinien zwrócić wynik dla wszystkich intencji. Można dodać wypowiedzi do dwóch intencji, wskazujące różnice między nimi przy użyciu wybranego programu word i rozmieszczenie lub aplikacja wywoływania usługi LUIS, takich jak czatbota znajduje się w opcji programowe dotyczące postępowania dwa najważniejsze intencji. 

## <a name="return-prediction-score-for-all-intents"></a>Zwraca wynik prognoz dla wszystkich intencji
Wynik testu lub punkt końcowy może obejmować wszystkie intencji. Ta konfiguracja jest ustawiona na [punktu końcowego](https://aka.ms/v1-endpoint-api-docs) z `verbose=true` pary nazwa/wartość ciągu zapytania. 

## <a name="review-intents-with-similar-scores"></a>Przejrzyj intencji z podobne wyniki
Przeglądanie wyników dla wszystkich intencji to dobry sposób, aby sprawdzić, czy nie określono poprawny intencji tylko że następnego identyfikowany oceny jego celem jest znacznie niższe spójnie wypowiedzi. 

Jeśli wiele intencji Zamknij prognozowania oceny na podstawie kontekstu wypowiedź LUIS może przełączać się między intencji. Aby rozwiązać ten problem, przejdź do Dodawanie wypowiedzi na intencje, każdy z wieloma różnymi kontekstowych różnice.   

## <a name="e-exponent-notation"></a>Notacja E (wykładnik)

Wyniki prognozowania można używać notacji wykładnika *pojawiające się* powyżej 0 – 1 do zakresu, takich jak `9.910309E-07`. Ten wynik jest wskazaniem bardzo **małych** numer.

|Wynik notacji E |Rzeczywisty wynik|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="differences-with-predictions"></a>W przypadku prognozy
Możesz później tego samego modelu w innej aplikacji, gdy wyniki nie są tym samym, jest to, ponieważ ma elementu losowości w szkolenia. Po drugie wszelkie nakładania się numerów wypowiedź na intencje więcej niż jeden oznacza, że najważniejsze przeznaczenie tego samego wypowiedź można zmienić w zależności od szkolenia.

Jeśli Twoje chatbot wymaga określonych oceny usługi LUIS do wskazania zaufania intencji, należy w zamian użyć wynik różnicy dwa najważniejsze intencji. Zapewnia to elastyczność dla zmian w szkoleniu. 

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Dodaj jednostki](luis-how-to-add-entities.md) Aby dowiedzieć się więcej o sposobie dodawania jednostki z aplikacją usługi LUIS.