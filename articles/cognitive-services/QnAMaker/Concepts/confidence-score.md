---
title: Wynik pewności — QnA Maker
titleSuffix: Azure Cognitive Services
description: Współczynnik ufności wskazuje ufności padnie odpowiednie dopasowania dla zapytania danego użytkownika.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: e2f7136ea7b973386eeb746a74ad09fadb490e83
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229116"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>Współczynnik ufności z bazy wiedzy usługi QnA Maker
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

- Jeśli **dokładność** (lub precyzja) jest ważniejsze dla danego scenariusza, zwiększ wartość progową. Dzięki temu za każdym razem, aby zwrócić odpowiedź, będzie znacznie więcej CONFIDENT, wielkości i znacznie większe szanse na czy szukasz użytkowników odpowiedzi. W takim przypadku może się to zakończyć opuszczania więcej pytań, bez odpowiedzi. *Na przykład:* Jeśli wprowadzisz próg **70**, możesz pominąć niektóre niejednoznaczne przykłady polubimy "co to jest zapisywanie i uczenie?".

- Jeśli **pokrycie** (lub odwołanie) jest ważniejsze i chcesz odpowiedzieć na tyle, na ile to możliwe, nawet jeśli istnieje tylko część relacji z pytaniem użytkownika, Obniż wartość progu. Oznacza to, że może być więcej przypadków, gdy odpowiedź nie odpowiada na kwerendy rzeczywistego użytkownika, ale zapewnia nieco powiązane odpowiedzi. *Na przykład:* w przypadku progu **30**można udzielić odpowiedzi na zapytania, takie jak "gdzie mogę edytować moją KB?".

> [!NOTE]
> Nowsze wersje usługi QnA Maker obejmują udoskonalenia w zakresie logiki oceniania i mogą mieć wpływ na próg. Ilekroć, zaktualizuj usługę, upewnij się, testowanie i dostosować wartość progową, jeśli to konieczne. W [tym miejscu](https://www.qnamaker.ai/UserSettings)możesz sprawdzić wersję usługi QNA, a także zapoznać się z artykułem jak uzyskać najnowsze aktualizacje w [tym miejscu](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

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
Po znalezieniu niezgodności dobre, oceniania współczynnik ufności 0.0, lub "None" jest zwracany, a odpowiedź domyślną jest "Dobrą niezgodności w w bazie wiedzy". Można zastąpić tę [domyślną odpowiedź](#change-default-answer) w bot lub kod aplikacji wywołujący punkt końcowy. Alternatywnie można również ustawić odpowiedzi zastąpienie na platformie Azure i spowoduje to zmianę domyślnego dla wszystkich baz wiedzy wdrożone w określonej usługi QnA Maker.

## <a name="change-default-answer"></a>Zmień domyślny odpowiedzi

1. Przejdź do [Azure Portal](https://portal.azure.com) i przejdź do grupy zasobów, która reprezentuje utworzoną usługę QNA Maker.

2. Kliknij, aby otworzyć **App Service**.

    ![W witrynie Azure portal dostęp do usługi App service dotyczące usługi QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Kliknij pozycję **Ustawienia aplikacji** i Zmień pole **DefaultAnswer** na żądaną domyślną odpowiedź. Kliknij pozycję **Zapisz**.

    ![Wybierz ustawienia aplikacji, a następnie edytuj DefaultAnswer dotyczące usługi QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Uruchom ponownie usługi App service

    ![Po zmianie DefaultAnswer, uruchom ponownie usługi QnA Maker usługi App Service](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Obsługiwane źródła danych](./data-sources-supported.md)

