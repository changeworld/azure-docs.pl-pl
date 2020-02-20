---
title: Rozwiązywanie problemów — Personalizacja
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące narzędzia do rozwiązywania problemów dotyczących narzędzia Personalizacja.
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: diberry
ms.openlocfilehash: fec403da7f54098dbf197d14f3b16afd30bf5efc
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77469548"
---
# <a name="personalizer-troubleshooting"></a>Rozwiązywanie problemów z narzędziem Personalizacja

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące narzędzia do rozwiązywania problemów dotyczących narzędzia Personalizacja.

## <a name="transaction-errors"></a>Błędy transakcji

<details>
<summary><b>Otrzymuję odpowiedź HTTP 429 (zbyt wiele żądań) od usługi. Co mogę zrobić?</b></summary>

**Odpowiedź**: Jeśli wybrano bezpłatną warstwę cenową podczas tworzenia wystąpienia personalizacji, obowiązuje limit przydziału liczby dozwolonych żądań rangi. Sprawdź częstotliwość wywołań interfejsu API dla interfejsu API rangi (w okienku metryki w Azure Portal dla zasobu personalizacji) i Dostosuj warstwę cenową (w okienku warstwy cenowej), jeśli oczekiwano zwiększenia wartości progowej dla wybranej warstwy cenowej.

</details>

<details>
<summary><b>Otrzymuję błąd 5xx w interfejsie API rangi lub nagradzania. Co mam zrobić?</b></summary>

**Odpowiedź**: te problemy powinny być przezroczyste. Jeśli będziesz kontynuować, skontaktuj się z pomocą techniczną, wybierając pozycję **nowe żądanie pomocy technicznej** w sekcji **Pomoc techniczna i rozwiązywanie problemów** w Azure Portal dla zasobu narzędzia Personalizacja.

</details>

## <a name="learning-loop"></a>Pętla szkoleniowa

<details>
<summary>
<b>Pętla szkoleniowa nie wydaje się uczyć. Jak mogę rozwiązać ten problem?</b></summary>

**Odpowiedź**: Pętla szkoleniowa wymaga kilku tysięcy wywołań, zanim funkcja Range wywoła priorytet.

Jeśli nie wiesz, jak działa pętla szkoleniowa, uruchom [ocenę w trybie offline](concepts-offline-evaluation.md)i Zastosuj poprawione zasady uczenia.

</details>

<details>
<summary><b>Zachowuję wyniki rangi ze wszystkimi tymi samymi prawdopodobieństwami dla wszystkich elementów. Jak mogę wiesz, że Personalizacja to nauka?</b></summary>

**Odpowiedź**: Personalizacja zwraca te same prawdopodobieństwa w wyniku interfejsu API rangi, gdy właśnie rozpoczął się i ma _pusty_ model, lub w przypadku zresetowania pętli personalizacji, a model jest nadal w okresie **częstotliwości aktualizacji modelu** .

Po rozpoczęciu nowego okresu aktualizacji zostanie użyty zaktualizowany model i zobaczysz zmiany prawdopodobieństwa.

</details>

<details>
<summary><b>Pętla szkoleniowa była uczyć się, ale wydaje się, że nie dowiesz się już, a jakość wyników nie jest dobra. Co mam zrobić?</b></summary>

**Odpowiedź**:
* Upewnij się, że ukończono i zastosowano jedną ocenę w Azure Portal dla tego zasobu personalizacji (pętla uczenia).
* Upewnij się, że wszystkie nagrody są wysyłane za pośrednictwem interfejsu API nagradzania i przetworzone.

</details>


<details>
<summary><b>Jak mogę wiesz, że pętla szkoleniowa jest regularnie aktualizowana i jest używana do oceny moich danych?</b></summary>

**Odpowiedź**: można znaleźć godzinę ostatniej aktualizacji modelu na stronie **Ustawienia modelu i uczenia** Azure Portal. Jeśli zobaczysz starą sygnaturę czasową, prawdopodobnie nie są wysyłane żadne wywołania rangi i nagrody. Jeśli usługa nie ma danych przychodzących, nie aktualizuje uczenia. Jeśli zobaczysz, że pętla uczenia nie jest często aktualizowana, możesz zmienić **częstotliwość aktualizacji modelu**pętli.

</details>

## <a name="offline-evaluations"></a>Oceny w trybie offline

<details>
<summary><b>Ważność funkcji oceny w trybie offline zwraca długą listę z setkami lub tysiącami elementów. Co się stało?</b></summary>

**Odpowiedź**: zwykle jest to spowodowane sygnaturami czasowymi, identyfikatorami użytkowników lub innymi szczegółowymi funkcjami wysyłanymi w programie.

</details>

<details>
<summary><b>Utworzyliśmy ocenę w trybie offline i pomyślnie prawie natychmiast. Dlaczego? Nie widzę żadnych wyników?</b></summary>

**Odpowiedź**: Ocena w trybie offline używa przeszkolonego modelu danych ze zdarzeń w tym okresie. Jeśli nie wysłano żadnych danych w okresie między czasem rozpoczęcia i zakończenia oceny, zakończy się bez żadnych wyników. Prześlij nową ocenę w trybie offline, wybierając przedział czasu, który ma znane zdarzenia, które zostały wysłane do personalizacji.

</details>


## <a name="learning-policy"></a>Zasady uczenia

<details>
<summary><b>Jak mogę zaimportować zasady uczenia?</b></summary>

**Odpowiedź**: Dowiedz się więcej o [pojęciach dotyczących zasad uczenia](concept-active-learning.md#understand-learning-policy-settings) i [sposobach stosowania](how-to-learning-policy.md) nowych zasad nauki. Jeśli nie chcesz wybierać zasad uczenia, możesz użyć [oceny w trybie offline](how-to-offline-evaluation.md) , aby zasugerować zasady uczenia na podstawie bieżących wydarzeń.

</details>

## <a name="security"></a>Bezpieczeństwo

<details>
<summary><b>Klucz interfejsu API dla mojej pętli został naruszony. Co mogę zrobić?</b></summary>

**Odpowiedź**: po zamianie klientów na inne klucze można wygenerować ponownie jeden klucz. Dwa klucze umożliwiają propagowanie klucza w oddziałach bez konieczności przestojów. Zalecamy to w regularnych cyklach jako środki bezpieczeństwa.

</details>

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie częstotliwości aktualizacji modelu](how-to-settings.md#model-update-frequency)