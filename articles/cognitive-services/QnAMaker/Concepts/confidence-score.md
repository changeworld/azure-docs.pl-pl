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
ms.date: 06/17/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: afc50a5adb591550f6e988a572d1ac9a8c4439cb
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955183"
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

- Jeśli **dokładność** (lub dokładności) jest niezwykle ważne dla danego scenariusza, a następnie zwiększ próg. Dzięki temu za każdym razem, aby zwrócić odpowiedź, będzie znacznie więcej CONFIDENT, wielkości i znacznie większe szanse na czy szukasz użytkowników odpowiedzi. W takim przypadku może się to zakończyć opuszczania więcej pytań, bez odpowiedzi. *Na przykład:* Jeśli wprowadzisz wartość progową **70**, możesz pominąć niektóre polubienia niejednoznaczne przykłady "co to jest zapisywanie i szkolenie?".

- Jeśli **pokrycia** (lub odwołania) jest bardziej ważne — i chcesz odpowiedzieć jak najwięcej pytań do odpowiedzi najszybciej, nawet w przypadku częściowej relacji do użytkownika pytanie - ZMNIEJSZYSZ wartość progową. Oznacza to, że może być więcej przypadków, gdy odpowiedź nie odpowiada na kwerendy rzeczywistego użytkownika, ale zapewnia nieco powiązane odpowiedzi. *Na przykład:* w przypadku progu **30**można udzielić odpowiedzi na zapytania, takie jak "gdzie mogę edytować moją KB?".

> [!NOTE]
> Nowsze wersje usługi QnA Maker obejmują udoskonalenia w zakresie logiki oceniania i mogą mieć wpływ na próg. Ilekroć, zaktualizuj usługę, upewnij się, testowanie i dostosować wartość progową, jeśli to konieczne. Wersję pytań i odpowiedzi usługi można sprawdzić [tutaj](https://www.qnamaker.ai/UserSettings)i zobacz, jak uzyskać najnowsze aktualizacje [tutaj](../How-To/troubleshooting-runtime.md).

## <a name="set-threshold"></a>Ustaw próg 

Ustaw ocenę wartości progowej jako właściwość [treści JSON interfejsu API GenerateAnswer](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). Oznacza to, że ustawisz go dla każdego wywołania do GenerateAnswer. 

W środowisku bot należy ustawić wynik jako część obiektu options z [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) lub [Node. js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Poprawić wyniki ufności
Aby poprawić współczynnik ufności z określonej odpowiedzi na zapytanie użytkownika, można dodać zapytania użytkownika do bazy wiedzy knowledge base, jako alternatywnej pytanie na odpowiedź. Można również użyć [zmian wyrazów](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) bez uwzględniania wielkości liter, aby dodać synonimy do słów kluczowych w KB.


## <a name="similar-confidence-scores"></a>Podobne wyniki ufności
Wiele odpowiedzi mają podobne współczynnik ufności, jest prawdopodobne, że zapytanie było zbyt ogólne i w związku z tym dopasowane z równym prawdopodobieństwem przy użyciu wielu odpowiedzi. Spróbuj struktury usługi znacznie lepiej, tak, aby każda jednostka pytań i odpowiedzi ma różne opcje.


## <a name="confidence-score-differences"></a>Różnice ocena ufności
Współczynnik ufności odpowiedzi mogą ulec zmianie w nieznaczny między testu i opublikowanej wersji w bazie wiedzy knowledge base, nawet jeśli zawartość jest taka sama. Jest to spowodowane zawartości testu i opublikowane wiedzy znajdują się w różnych indeksów usługi Azure Search. Po opublikowaniu bazy wiedzy zawartość pytania i odpowiedzi bazy wiedzy przechodzi z indeksu testu do indeksu produkcyjnego w usłudze Azure Search. Zobacz, jak działa operacja [publikowania](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) .

Jeśli masz bazę wiedzy w różnych regionach, każdy region używa własnego indeksu Azure Search. Ponieważ są używane różne indeksy, wyniki nie będą dokładnie takie same. 


## <a name="no-match-found"></a>Nie znaleziono dopasowań
Po znalezieniu niezgodności dobre, oceniania współczynnik ufności 0.0, lub "None" jest zwracany, a odpowiedź domyślną jest "Dobrą niezgodności w w bazie wiedzy". Można zastąpić tę [domyślną odpowiedź](#change-default-answer) w bot lub kod aplikacji wywołujący punkt końcowy. Alternatywnie można również ustawić odpowiedzi zastąpienie na platformie Azure i spowoduje to zmianę domyślnego dla wszystkich baz wiedzy wdrożone w określonej usługi QnA Maker.

## <a name="change-default-answer"></a>Zmień domyślny odpowiedzi

1. Przejdź do [witryny Azure portal](https://portal.azure.com) i przejdź do grupy zasobów, która reprezentuje utworzonej usługi QnA Maker.

2. Kliknij, aby otworzyć **usługi App Service**.

    ![W witrynie Azure portal dostęp do usługi App service dotyczące usługi QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Kliknij pozycję **ustawienia aplikacji** i edytować **DefaultAnswer** pole odpowiedź domyślną. Kliknij pozycję **Zapisz**.

    ![Wybierz ustawienia aplikacji, a następnie edytuj DefaultAnswer dotyczące usługi QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Uruchom ponownie usługi App service

    ![Po zmianie DefaultAnswer, uruchom ponownie usługi QnA Maker usługi App Service](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Źródła danych obsługiwane](./data-sources-supported.md)

