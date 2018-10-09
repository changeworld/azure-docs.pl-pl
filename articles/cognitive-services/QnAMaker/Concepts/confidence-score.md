---
title: Współczynnik ufności — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Wyjaśnia współczynnik ufności
services: cognitive-services
author: tulasim88
manager: pchoudh
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 09/27/2018
ms.author: tulasim
ms.openlocfilehash: 9bcd2ccfc5fb8ddc71d2df178dd214be2a67160d
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856857"
---
# <a name="confidence-score"></a>Współczynnik ufności
Jeśli kwerenda użytkownika są dopasowywane do wiedzy, narzędzie QnA Maker zwraca odpowiednich odpowiedzi, wraz z współczynnik ufności. Ten wynik wskazuje ufności padnie odpowiednie dopasowania dla zapytania danego użytkownika. 

Współczynnik ufności jest liczbą z zakresu od 0 do 100. Wynikiem 100 prawdopodobnie jest dokładne dopasowanie, podczas gdy wynik 0 oznacza, że, które można odnaleźć pasującego odpowiedzi. Im większy wynik - większą pewnością w odpowiedzi na pytanie. Dla danego zapytania może istnieć wiele odpowiedzi zwrócony. W takim przypadku odpowiedzi są zwracane w kolejności malejących współczynnik ufności.

W poniższym przykładzie widać jedną jednostkę pytań i odpowiedzi, przy użyciu 2 pytania. 


![Przykładowy pary pytań i odpowiedzi](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Powyższe przykładowe-mogą spodziewać się wyniki, takie jak zakres wynik przykładowe poniżej — dla różnych typów kwerend użytkowników:


![Oceniania zakresu wynik](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


Poniższa tabela wskazuje typowe ufności skojarzone dla danego wynik.

|Wartość|Co oznacza wynik|Przykładowe zapytanie|
|--|--|--|
|90 - 100|A niemal dokładne dopasowanie użytkownika zapytania i zapytania KB|"Moje zmiany nie zostaną zaktualizowane w bazie wiedzy Knowledge base po opublikowaniu"|
|> 70|O dużej pewności — zwykle prawidłowej odpowiedzi, który całkowicie odpowiada na zapytania użytkownika|"Opublikowany bazę wiedzy, ale nie jest aktualizowany."|
|50 - 70|Średnie obaw — zwykle stosunkowo dobrej odpowiedź, którą należy odpowiedzieć na głównym celem kwerenda użytkownika|"Należy zapisywać Moje aktualizacje przed mogę opublikować moje bazy wiedzy?"|
|30 – 50|Niski obaw — zazwyczaj powiązane odpowiedzi, który częściowo odpowiedzi użytkownika intencji|"Czego zapisywanie i pociągu?"|
|< 30|Bardzo małe obaw — zwykle nie odpowiada na kwerendy użytkownika, ale ma pewne dopasowania słów i fraz |"Gdzie można dodać synonimy do bazę wiedzy"|
|0|Brak dopasowania, więc nie zostanie zwrócona odpowiedź.|"Ile usługa kosztuje"|

## <a name="choose-a-score-threshold"></a>Wybierz ocenę wartości progowej
Powyższej tabeli przedstawiono wyniki, które oczekują na większości artykułów bazy wiedzy. Jednak ponieważ każdy KB jest inna i ma różnych typów, słów, intencje i cele zalecamy testowania i wybierz wartość progową najlepiej preferencji. Jest to domyślna i zalecana wartość progowa, które powinny działać dla większości artykułów bazy wiedzy, **50**.

Wybierając próg, należy pamiętać, równowagę między dokładności i pokrycia i dostosować próg zgodnie z wymaganiami.

- Jeśli **dokładność** (lub dokładności) jest niezwykle ważne dla danego scenariusza, a następnie zwiększ próg. Dzięki temu za każdym razem, aby zwrócić odpowiedź, będzie znacznie więcej CONFIDENT, wielkości i znacznie większe szanse na czy szukasz użytkowników odpowiedzi. W takim przypadku może się to zakończyć opuszczania więcej pytań, bez odpowiedzi. *Na przykład:* Jeśli wprowadzisz wartość progową **70**, możesz pominąć niektóre polubienia niejednoznaczne przykłady "co to jest zapisywanie i szkolenie?".

- Jeśli **pokrycia** (lub odwołania) jest bardziej ważne — i chcesz odpowiedzieć jak najwięcej pytań do odpowiedzi najszybciej, nawet w przypadku częściowej relacji do użytkownika pytanie - ZMNIEJSZYSZ wartość progową. Oznacza to, że może być więcej przypadków, gdy odpowiedź nie odpowiada na kwerendy rzeczywistego użytkownika, ale zapewnia nieco powiązane odpowiedzi. *Na przykład:* Jeśli wprowadzisz wartość progową **30**, może udzielić odpowiedzi nie jest bardzo powiązane jak odpowiedzi z powyższego przykładu, dla kwerend, takich jak "gdzie mogę edytować Moje bazy wiedzy?"


## <a name="improve-confidence-scores"></a>Poprawić wyniki ufności
Aby poprawić współczynnik ufności z określonej odpowiedzi na zapytanie użytkownika, można dodać zapytania użytkownika do bazy wiedzy knowledge base, jako alternatywnej pytanie na odpowiedź.


## <a name="similar-confidence-scores"></a>Podobne wyniki ufności
Wiele odpowiedzi mają podobne współczynnik ufności, jest prawdopodobne, że zapytanie było zbyt ogólne i w związku z tym dopasowane z równym prawdopodobieństwem przy użyciu wielu odpowiedzi. Spróbuj struktury usługi znacznie lepiej, tak, aby każda jednostka pytań i odpowiedzi ma różne opcje.


## <a name="confidence-score-differences"></a>Różnice ocena ufności
Współczynnik ufności odpowiedzi mogą ulec zmianie w nieznaczny między testu i opublikowanej wersji w bazie wiedzy knowledge base, nawet jeśli zawartość jest taka sama. Jest to spowodowane zawartości testu i opublikowane wiedzy znajdują się w różnych indeksów usługi Azure Search.
Zobacz tutaj sposób, w jaki [publikowania](../How-To/publish-knowledge-base.md) działanie.


## <a name="no-match-found"></a>Nie znaleziono dopasowań
Po znalezieniu niezgodności dobre, oceniania współczynnik ufności 0.0, lub "None" jest zwracany, a odpowiedź domyślną jest "Dobrą niezgodności w w bazie wiedzy". Możesz przesłonić to odpowiedź domyślną w kodzie bot lub aplikacji, wywołanie punktu końcowego. Alternatywnie można również ustawić odpowiedzi zastąpienie na platformie Azure i spowoduje to zmianę domyślnego dla wszystkich baz wiedzy wdrożone w określonej usługi QnA Maker.

1. Przejdź do [witryny Azure portal](https://portal.azure.com) i przejdź do grupy zasobów, która reprezentuje utworzonej usługi QnA Maker.

2. Kliknij, aby otworzyć **usługi App Service**.

    ![Dostęp do usługi App service](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Kliknij pozycję **ustawienia aplikacji** i edytować **DefaultAnswer** pole odpowiedź domyślną. Kliknij pozycję **Zapisz**.

    ![Zmień domyślną odpowiedź](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Uruchom ponownie usługi App service

    ![Ponowne uruchomienie usługi App Service usługa QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Źródła danych obsługiwane](./data-sources-supported.md)
## <a name="see-also"></a>Zobacz także 
[Omówienie usługi QnA Maker](../Overview/overview.md)
