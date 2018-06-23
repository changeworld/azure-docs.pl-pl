---
title: Wynik zaufania — kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Wyjaśniający wynik zaufania
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: c97bdb7e57275ebd1893bc28248c4ecc6b35c153
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348657"
---
# <a name="confidence-score"></a>Współczynnik ufności

Wynik zaufania określa stopień dopasowania użytkownika pytanie i odpowiedź zwrócona.

Jeśli kwerenda użytkownika jest dopasowywana zawartość bazy wiedzy knowledge base, może istnieć więcej niż jeden odpowiedź zwrócona. Odpowiedzi są zwracane w porządku uporządkowanej według rangi zmniejszanie wynik zaufania.

Wynik ufności jest od 0 do 100.

|Wartość wyniku|Ufność|
|--|--|
|100|Dokładne dopasowanie użytkownika zapytań i pytanie KB|
|90|Zgodne większość wyrazy wysokiego zaufania-|
|40-60|Zgodne słowa ważne odpowiedniego obaw —|
|10|Niski obaw — ważne wyrazy nie są zgodne|
|0|Brak zgodności programu word|


## <a name="similar-confidence-scores"></a>Podobne wyniki zaufania
Wiele odpowiedzi mają podobne wynik zaufania, jest prawdopodobne, że kwerenda ma zbyt ogólnym i dlatego dopasowanych z równym prawdopodobieństwem wiele odpowiedzi. Spróbuj struktury QnAs Twojego lepiej, dzięki czemu każdy podmiot — strona główna zawiera różne opcje.


## <a name="improving-confidence-scores"></a>Poprawa wyniki zaufania
Aby zwiększyć wynik zaufania określonej odpowiedzi na kwerendę użytkownika, można dodać kwerenda użytkownika w bazie wiedzy jako alternatywne pytanie na tej odpowiedzi.
   
## <a name="confidence-score-differences"></a>Różnice wynik zaufania
Wynik zaufania odpowiedzi mogą ulec zmianie w nieznaczny między testu i opublikowanej wersji w bazie wiedzy, nawet jeśli zawartość jest taka sama. Jest to spowodowane zawartości testu i opublikowanych wiedzy znajdują się w różnych indeksów usługi Azure Search.

Zobacz tutaj sposób [publikowania](../How-To/publish-knowledge-base.md) działanie.


## <a name="no-match-found"></a>Nie znaleziono dopasowania
Nie dobrej znaleziono przez ranker, wynik zaufania 0,0 lub "None" jest zwracany, a wartość domyślna jest "Nie znaleziono w artykule bazy wiedzy dopasowania dobrej". Można zastąpić tej odpowiedzi domyślnej w kodzie bot lub aplikacji wywołaniem punktu końcowego. Alternatywnie można również ustawić zastąpienie odpowiedzi na platformie Azure i spowoduje to zmianę domyślnego dla wszystkich baz wiedzy wdrożone w określonej usłudze Maker — strona główna.

1. Przejdź do [portalu Azure](http://portal.azure.com) i przejdź do grupę zasobów, która reprezentuje usługę Maker — strona główna został utworzony.

2. Kliknij, aby otworzyć **usługi aplikacji**.

    ![Usługa dostępu do aplikacji](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Polecenie **ustawienia aplikacji** i edytować **DefaultAnswer** pole odpowiedzi domyślną. Kliknij pozycję **Zapisz**.

    ![Zmień domyślny](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Uruchom ponownie usługi aplikacji

    ![Ponowne uruchomienie usługi aplikacji — strona główna Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Obsługiwane źródła danych](./data-sources-supported.md)

## <a name="see-also"></a>Zobacz także 

[Omówienie Maker — strona główna](../Overview/overview.md)
