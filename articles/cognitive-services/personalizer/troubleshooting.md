---
title: Rozwiązywanie problemów — Personalizacja
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące narzędzia do rozwiązywania problemów dotyczących narzędzia Personalizacja.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: diberry
ms.openlocfilehash: 5aeda9abcebda50cf97e1473b458d8f1f9d15970
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832173"
---
# <a name="personalizer-troubleshooting"></a>Rozwiązywanie problemów z narzędziem Personalizacja

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące narzędzia do rozwiązywania problemów dotyczących narzędzia Personalizacja.

## <a name="transaction-errors"></a>Błędy transakcji

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>Otrzymuję odpowiedź HTTP 429 (zbyt wiele żądań) od usługi. Co mogę zrobić?

W przypadku wybrania warstwy cenowej bezpłatnej podczas tworzenia wystąpienia personalizacji obowiązuje limit przydziału liczby dozwolonych żądań rangi. Sprawdź częstotliwość wywołań interfejsu API dla interfejsu API rangi (w okienku metryki w Azure Portal dla zasobu personalizacji) i Dostosuj warstwę cenową (w okienku warstwy cenowej), jeśli oczekiwano zwiększenia wartości progowej dla wybranej warstwy cenowej.

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>Otrzymuję błąd 5xx w interfejsie API rangi lub nagradzania. Co mamy zrobić?

Te problemy powinny być przezroczyste. Jeśli będziesz kontynuować, skontaktuj się z pomocą techniczną, wybierając pozycję **nowe żądanie pomocy technicznej** w sekcji **Pomoc techniczna i rozwiązywanie problemów** w Azure Portal dla zasobu narzędzia Personalizacja.


## <a name="learning-loop"></a>Pętla szkoleniowa

<!--

### How do I import a learning policy?


-->

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Pętla szkoleniowa nie wydaje się uczyć. W jaki sposób rozwiązać ten problem?

Pętla szkoleniowa wymaga kilku tysięcy wywołań, zanim funkcja Range wywoła priorytet.

Jeśli nie wiesz, jak działa pętla szkoleniowa, uruchom [ocenę w trybie offline](concepts-offline-evaluation.md)i Zastosuj poprawione zasady uczenia.

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>Zachowuję wyniki rangi ze wszystkimi tymi samymi prawdopodobieństwami dla wszystkich elementów. Jak mogę wiesz, że Personalizacja to nauka?

Personalizacja zwraca te same prawdopodobieństwa w wyniku interfejsu API rangi, gdy właśnie rozpoczął się i ma _pusty_ model, lub w przypadku zresetowania pętli personalizacji, a model jest nadal w okresie **częstotliwości aktualizacji modelu** .

Po rozpoczęciu nowego okresu aktualizacji zostanie użyty zaktualizowany model i zobaczysz zmiany prawdopodobieństwa.

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>Pętla szkoleniowa była uczyć się, ale wydaje się, że nie dowiesz się już, a jakość wyników nie jest dobra. Co mamy zrobić?

* Upewnij się, że ukończono i zastosowano jedną ocenę w Azure Portal dla tego zasobu personalizacji (pętla uczenia).
* Upewnij się, że wszystkie nagrody są wysyłane za pośrednictwem interfejsu API nagradzania i przetworzone.

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>Jak mogę wiesz, że pętla szkoleniowa jest regularnie aktualizowana i jest używana do oceny moich danych?

Czas ostatniej aktualizacji modelu można znaleźć na stronie **Ustawienia modelu i uczenia** Azure Portal. Jeśli zobaczysz starą sygnaturę czasową, prawdopodobnie nie są wysyłane żadne wywołania rangi i nagrody. Jeśli usługa nie ma danych przychodzących, nie aktualizuje uczenia. Jeśli zobaczysz, że pętla uczenia nie jest często aktualizowana, możesz zmienić **częstotliwość aktualizacji modelu**pętli.


## <a name="offline-evaluations"></a>Oceny w trybie offline

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>Ważność funkcji oceny w trybie offline zwraca długą listę z setkami lub tysiącami elementów. Co się stało?

Zwykle jest to spowodowane sygnaturami czasowymi, identyfikatorami użytkowników lub innymi szczegółowymi funkcjami wysyłanymi w programie.

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>Utworzyliśmy ocenę w trybie offline i pomyślnie prawie natychmiast. Dlaczego? Nie widzę żadnych wyników?

Ocena w trybie offline używa przeszkolonego modelu danych ze zdarzeń w tym okresie. Jeśli nie wysłano żadnych danych w okresie między czasem rozpoczęcia i zakończenia oceny, zakończy się bez żadnych wyników. Prześlij nową ocenę w trybie offline, wybierając przedział czasu, który ma znane zdarzenia, które zostały wysłane do personalizacji.

## <a name="learning-policy"></a>Zasady uczenia

### <a name="how-do-i-import-a-learning-policy"></a>Jak mogę zaimportować zasady uczenia?

Dowiedz się więcej o [pojęciach dotyczących zasad uczenia](concept-active-learning.md#understand-learning-policy-settings) i [sposobach stosowania](how-to-learning-policy.md) nowych zasad nauki. Jeśli nie chcesz wybierać zasad uczenia, możesz użyć [oceny w trybie offline](how-to-offline-evaluation.md) , aby zasugerować zasady uczenia na podstawie bieżących wydarzeń.


## <a name="security"></a>Zabezpieczenia

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>Klucz interfejsu API dla mojej pętli został naruszony. Co mogę zrobić?

Po zamianie klientów na inne można wygenerować ponownie klucz. Dwa klucze umożliwiają propagowanie klucza w oddziałach bez konieczności przestojów. Zalecamy to w regularnych cyklach jako środki bezpieczeństwa.


## <a name="next-steps"></a>Następne kroki

[Konfigurowanie częstotliwości aktualizacji modelu](how-to-settings.md#model-update-frequency)