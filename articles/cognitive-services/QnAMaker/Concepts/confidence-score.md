---
title: Współczynnik ufności — QnA Maker
titleSuffix: Azure Cognitive Services
description: Współczynnik ufności określa stopień dopasowania użytkownika pytanie i odpowiedź zwrócona.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 30a29bdcb56eb39bcd004b5a7bf8f3526e2d5c75
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2018
ms.locfileid: "45541738"
---
# <a name="confidence-score"></a>Współczynnik ufności

Współczynnik ufności określa stopień dopasowania użytkownika pytanie i odpowiedź zwrócona.

Jeśli kwerenda użytkownika jest dopasowywana zawartość bazy wiedzy, może istnieć więcej niż jedną odpowiedź zwrócona. Odpowiedzi są zwracane w rankingu kolejności malejących współczynnik ufności.

Współczynnik ufności jest od 0 do 100.

|Wartość|Ufność|
|--|--|
|100|Dokładne dopasowanie użytkownika zapytania i zapytania KB|
|90|Odpowiada większość słów o dużej pewności-|
|40-60|Dopasowania słów ważne uczciwe obaw —|
|10|Niski obaw — ważne słowa nie są zgodne|
|0|Brak dopasowania programu word|


## <a name="similar-confidence-scores"></a>Podobne wyniki ufności
Wiele odpowiedzi mają podobne współczynnik ufności, jest prawdopodobne, że zapytanie było zbyt ogólne i w związku z tym dopasowane z równym prawdopodobieństwem przy użyciu wielu odpowiedzi. Spróbuj struktury usługi znacznie lepiej, tak, aby każda jednostka pytań i odpowiedzi ma różne opcje.


## <a name="improving-confidence-scores"></a>Usprawnienie oceny zaufania
Aby poprawić współczynnik ufności z określonej odpowiedzi na zapytanie użytkownika, można dodać zapytania użytkownika do bazy wiedzy knowledge base, jako alternatywnej pytanie na odpowiedź.
   
## <a name="confidence-score-differences"></a>Różnice ocena ufności
Współczynnik ufności odpowiedzi mogą ulec zmianie w nieznaczny między testu i opublikowanej wersji w bazie wiedzy knowledge base, nawet jeśli zawartość jest taka sama. Jest to spowodowane zawartości testu i opublikowane wiedzy znajdują się w różnych indeksów usługi Azure Search.

Zobacz tutaj sposób, w jaki [publikowania](../How-To/publish-knowledge-base.md) działanie.


## <a name="no-match-found"></a>Nie znaleziono dopasowań
Po znalezieniu niezgodności dobre, oceniania współczynnik ufności 0.0, lub "None" jest zwracany, a odpowiedź domyślną jest "Dobrą niezgodności w w bazie wiedzy". Możesz przesłonić to odpowiedź domyślną w kodzie bot lub aplikacji, wywołanie punktu końcowego. Alternatywnie można również ustawić odpowiedzi zastąpienie na platformie Azure i spowoduje to zmianę domyślnego dla wszystkich baz wiedzy wdrożone w określonej usługi QnA Maker.

1. Przejdź do [witryny Azure portal](http://portal.azure.com) i przejdź do grupy zasobów, która reprezentuje utworzonej usługi QnA Maker.

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
