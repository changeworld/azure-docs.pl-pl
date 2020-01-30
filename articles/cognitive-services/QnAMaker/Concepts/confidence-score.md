---
title: Wynik pewności — QnA Maker
titleSuffix: Azure Cognitive Services
description: Baza wiedzy musi być opublikowana. Po opublikowaniu baza wiedzy jest wysyłana w punkcie końcowym przewidywania środowiska uruchomieniowego przy użyciu interfejsu API generateAnswer.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: d901a803311805825c22503af6098e805a67e8f6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843456"
---
# <a name="the-confidence-score-of-an-answer"></a>Wynik pewności odpowiedzi
Jeśli kwerenda użytkownika są dopasowywane do wiedzy, narzędzie QnA Maker zwraca odpowiednich odpowiedzi, wraz z współczynnik ufności. Ten wynik wskazuje ufności padnie odpowiednie dopasowania dla zapytania danego użytkownika.

Współczynnik ufności jest liczbą z zakresu od 0 do 100. Wynikiem 100 prawdopodobnie jest dokładne dopasowanie, podczas gdy wynik 0 oznacza, że, które można odnaleźć pasującego odpowiedzi. Im większy wynik - większą pewnością w odpowiedzi na pytanie. Dla danego zapytania może istnieć wiele odpowiedzi zwrócony. W takim przypadku odpowiedzi są zwracane w kolejności malejących współczynnik ufności.

W poniższym przykładzie widać jedną jednostkę pytań i odpowiedzi, przy użyciu 2 pytania.


![Przykładowy pary pytań i odpowiedzi](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Powyższe przykładowe-mogą spodziewać się wyniki, takie jak zakres wynik przykładowe poniżej — dla różnych typów kwerend użytkowników:


![Oceniania zakresu wynik](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


Poniższa tabela wskazuje typowe ufności skojarzone dla danego wynik.

|Wartość|Co oznacza wynik|Przykładowe zapytanie|
|--|--|--|
|90 - 100|A niemal dokładne dopasowanie użytkownika zapytania i zapytania KB|"Moje zmiany nie są aktualizowane w KB po opublikowaniu"|
|> 70|O dużej pewności — zwykle prawidłowej odpowiedzi, który całkowicie odpowiada na zapytania użytkownika|"Opublikowany bazę wiedzy, ale nie jest aktualizowany."|
|50 - 70|Średnie obaw — zwykle stosunkowo dobrej odpowiedź, którą należy odpowiedzieć na głównym celem kwerenda użytkownika|"Należy zapisywać Moje aktualizacje przed mogę opublikować moje bazy wiedzy?"|
|30 – 50|Niski obaw — zazwyczaj powiązane odpowiedzi, który częściowo odpowiedzi użytkownika intencji|"Czego zapisywanie i pociągu?"|
|< 30|Bardzo małe obaw — zwykle nie odpowiada na kwerendy użytkownika, ale ma pewne dopasowania słów i fraz |"Gdzie można dodać synonimy do bazę wiedzy"|
|0|Brak dopasowania, więc nie zostanie zwrócona odpowiedź.|"Ile usługa kosztuje"|

## <a name="choose-a-score-threshold"></a>Wybierz ocenę wartości progowej
Powyższej tabeli przedstawiono wyniki, które oczekują na większości artykułów bazy wiedzy. Jednak ponieważ każda baza wiedzy różni się od siebie i ma różne typy słów, intencje i cele — zalecamy przetestowanie i wybranie progu, który najlepiej działa. Domyślnie próg jest ustawiany na 0, tak aby wszystkie możliwe odpowiedzi są zwracane. Zalecany próg, który powinien być dla większości artykułów bazy wiedzy, wynosi **50**.

Wybierając próg, należy pamiętać, równowagę między dokładności i pokrycia i dostosować próg zgodnie z wymaganiami.

- Jeśli **dokładność** (lub dokładności) jest niezwykle ważne dla danego scenariusza, a następnie zwiększ próg. Dzięki temu za każdym razem, aby zwrócić odpowiedź, będzie znacznie więcej CONFIDENT, wielkości i znacznie większe szanse na czy szukasz użytkowników odpowiedzi. W takim przypadku może się to zakończyć opuszczania więcej pytań, bez odpowiedzi. *Na przykład:* Jeśli wprowadzisz wartość progową **70**, możesz pominąć niektóre polubienia niejednoznaczne przykłady "co to jest zapisywanie i szkolenie?".

- Jeśli **pokrycia** (lub odwołania) jest bardziej ważne — i chcesz odpowiedzieć jak najwięcej pytań do odpowiedzi najszybciej, nawet w przypadku częściowej relacji do użytkownika pytanie - ZMNIEJSZYSZ wartość progową. Oznacza to, że może być więcej przypadków, gdy odpowiedź nie odpowiada na kwerendy rzeczywistego użytkownika, ale zapewnia nieco powiązane odpowiedzi. *Na przykład:* w przypadku progu **30**można udzielić odpowiedzi na zapytania, takie jak "gdzie mogę edytować moją KB?".

> [!NOTE]
> Nowsze wersje usługi QnA Maker obejmują udoskonalenia w zakresie logiki oceniania i mogą mieć wpływ na próg. Ilekroć, zaktualizuj usługę, upewnij się, testowanie i dostosować wartość progową, jeśli to konieczne. Wersję pytań i odpowiedzi usługi można sprawdzić [tutaj](https://www.qnamaker.ai/UserSettings)i zobacz, jak uzyskać najnowsze aktualizacje [tutaj](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="set-threshold"></a>Ustaw próg

Ustaw ocenę wartości progowej jako właściwość [treści JSON interfejsu API GenerateAnswer](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). Oznacza to, że ustawisz go dla każdego wywołania do GenerateAnswer.

W środowisku bot należy ustawić wynik jako część obiektu options z [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) lub [Node. js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Poprawić wyniki ufności
Aby poprawić współczynnik ufności z określonej odpowiedzi na zapytanie użytkownika, można dodać zapytania użytkownika do bazy wiedzy knowledge base, jako alternatywnej pytanie na odpowiedź. Można również użyć [zmian wyrazów](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) bez uwzględniania wielkości liter, aby dodać synonimy do słów kluczowych w KB.


## <a name="similar-confidence-scores"></a>Podobne wyniki ufności
Wiele odpowiedzi mają podobne współczynnik ufności, jest prawdopodobne, że zapytanie było zbyt ogólne i w związku z tym dopasowane z równym prawdopodobieństwem przy użyciu wielu odpowiedzi. Spróbuj struktury usługi znacznie lepiej, tak, aby każda jednostka pytań i odpowiedzi ma różne opcje.


## <a name="confidence-score-differences-between-test-and-production"></a>Różnice oceny ufności między testem a produkcją
Współczynnik ufności odpowiedzi mogą ulec zmianie w nieznaczny między testu i opublikowanej wersji w bazie wiedzy knowledge base, nawet jeśli zawartość jest taka sama. Wynika to z faktu, że zawartość testu i opublikowanej bazy wiedzy znajdują się w różnych indeksach Wyszukiwanie poznawcze platformy Azure.

Indeks testu zawiera wszystkie pary QnA baz wiedzy. Podczas wykonywania zapytania względem indeksu testowego zapytanie dotyczy całego indeksu, a wyniki są ograniczone do partycji dla tej konkretnej bazy wiedzy. Jeśli wyniki zapytania testowego mają negatywny wpływ na możliwość zweryfikowania bazy wiedzy, możesz:
* Organizuj bazę wiedzy przy użyciu jednego z następujących elementów:
    * 1 zasób ograniczono do 1 KB: Ogranicz pojedynczy zasób QnA (oraz otrzymany indeks testu Wyszukiwanie poznawcze platformy Azure) do jednej bazy wiedzy.
    * 2 zasoby — 1 dla testu, 1 dla produkcji: mają dwa QnA Maker zasoby, przy użyciu jednej do testowania (z własnymi indeksami testowymi i produkcyjnymi) oraz jeden dla produktu (również ma własne indeksy testowe i produkcyjne)
* i, zawsze używaj tych samych parametrów, takich jak **[Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** , podczas wysyłania zapytań do bazy wiedzy dotyczącej testów i produkcji

Po opublikowaniu bazy wiedzy zawartość pytania i odpowiedzi bazy wiedzy przechodzi z indeksu testu do indeksu produkcyjnego w usłudze Azure Search. Zobacz, jak działa operacja [publikowania](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) .

Jeśli masz bazę wiedzy w różnych regionach, każdy region używa własnego indeksu Wyszukiwanie poznawcze platformy Azure. Ponieważ są używane różne indeksy, wyniki nie będą dokładnie takie same.


## <a name="no-match-found"></a>Nie znaleziono dopasowań
Po znalezieniu niezgodności dobre, oceniania współczynnik ufności 0.0, lub "None" jest zwracany, a odpowiedź domyślną jest "Dobrą niezgodności w w bazie wiedzy". Można zastąpić tę [domyślną odpowiedź](../How-To/metadata-generateanswer-usage.md) w bot lub kod aplikacji wywołujący punkt końcowy. Alternatywnie można również ustawić odpowiedzi zastąpienie na platformie Azure i spowoduje to zmianę domyślnego dla wszystkich baz wiedzy wdrożone w określonej usługi QnA Maker.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Najlepsze praktyki](./best-practices.md)

