---
title: Rozwiązywanie problemów - Personalizer
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące rozwiązywania problemów z personalizatorem.
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 904953f028eb31afe42cf477ac05be43e8b72a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336029"
---
# <a name="personalizer-troubleshooting"></a>Rozwiązywanie problemów z personalizatorem

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące rozwiązywania problemów z personalizatorem.

## <a name="transaction-errors"></a>Błędy transakcji

<details>
<summary><b>Otrzymuję odpowiedź HTTP 429 (zbyt wiele żądań) z usługi. Co mogę zrobić?</b></summary>

**Odpowiedź:** Jeśli podczas tworzenia instancji Personalizer wybrano bezpłatną warstwę cenową, istnieje limit przydziału liczby żądań rangi, które są dozwolone. Przejrzyj szybkość wywołania interfejsu API dla interfejsu API rangi (w okienku Metryki w witrynie Azure portal dla zasobu Personalizer) i dostosuj warstwę cenową (w okienku Warstwy cenowej), jeśli oczekuje się, że wolumin wywołania wzrośnie powyżej progu dla wybranej warstwy cenowej.

</details>

<details>
<summary><b>Dostaję błąd 5xx w interfejsach API rangi lub nagrody. Co należy zrobić?</b></summary>

**Odpowiedź**: Kwestie te powinny być przejrzyste. Jeśli będą kontynuowane, skontaktuj się z pomocą techniczną, wybierając **nowe żądanie pomocy technicznej** w sekcji Pomoc **techniczna + rozwiązywanie problemów** w witrynie Azure portal dla zasobu personalizatora.

</details>

## <a name="learning-loop"></a>Pętla uczenia się

<details>
<summary>
<b>Pętla uczenia się nie osiąga 100% dopasowania do systemu bez Personalizer. Jak rozwiązać ten problem?</b></summary>

**Odpowiedź**: Powody, dla których nie osiągasz celu za pomocą pętli uczenia się:
* Za mało funkcji wysłanych z wywołaniem interfejsu API rangi
* Błędy w wysyłanych funkcjach , takie jak wysyłanie nieagregowanych danych funkcji, takich jak sygnatury czasowe do interfejsu API rangi
* Błędy z przetwarzaniem pętli - takie jak niesyłanie danych nagrody do interfejsu API nagrody dla zdarzeń

Aby naprawić, musisz zmienić przetwarzanie, zmieniając funkcje wysyłane do pętli lub upewnij się, że nagroda jest prawidłową oceną jakości odpowiedzi rangi.

</details>

<details>
<summary>
<b>Pętla uczenia się nie wydaje się uczyć. Jak rozwiązać ten problem?</b></summary>

**Odpowiedź:** Pętla uczenia się wymaga kilku tysięcy połączeń z nagrodami, zanim ranga będzie skutecznie ustalać priorytety.

Jeśli nie masz pewności co do tego, jak aktualnie działa twoja pętla uczenia się, uruchom [ocenę offline](concepts-offline-evaluation.md)i zastosuj poprawioną politykę uczenia się.

</details>

<details>
<summary><b>Wciąż otrzymuję wyniki rankingowe z tymi samymi prawdopodobieństwami dla wszystkich przedmiotów. Skąd mam wiedzieć, że Personalizer się uczy?</b></summary>

**Odpowiedź:** Personalizer zwraca te same prawdopodobieństwa w wyniku interfejsu API rangi, gdy właśnie się rozpoczął i ma _pusty_ model lub po zresetowaniu pętli Personalizer, a model jest nadal w okresie **częstotliwości aktualizacji modelu.**

Po rozpoczęciu nowego okresu aktualizacji używany jest zaktualizowany model, a prawdopodobieństwo ulegnie zmianie.

</details>

<details>
<summary><b>Pętla uczenia się była uczeniem się, ale wydaje się, że już się nie uczy, a jakość wyników rangi nie jest tak dobra. Co należy zrobić?</b></summary>

**Odpowiedź**:
* Upewnij się, że ukończono i zastosowałeś jedną ocenę w witrynie Azure portal dla tego zasobu Personalizer (pętla uczenia się).
* Upewnij się, że wszystkie nagrody są wysyłane za pośrednictwem interfejsu API nagród i przetwarzane.

</details>


<details>
<summary><b>Skąd mam wiedzieć, że pętla uczenia się jest regularnie aktualizowana i służy do oceniania moich danych?</b></summary>

**Odpowiedź:** Można znaleźć czas, kiedy model został ostatnio zaktualizowany na stronie **Ustawienia modelu i uczenia** się w witrynie Azure portal. Jeśli widzisz stary sygnatura czasowa, prawdopodobnie nie wysyłasz połączeń rangi i nagrody. Jeśli usługa nie ma danych przychodzących, nie aktualizuje uczenia się. Jeśli widzisz, że pętla uczenia się nie jest aktualizowana wystarczająco często, możesz edytować **częstotliwość aktualizacji modelu**pętli .

</details>

## <a name="offline-evaluations"></a>Oceny w trybie offline

<details>
<summary><b>Znaczenie funkcji oceny w trybie offline zwraca długą listę z setkami lub tysiącami elementów. Co się stało?</b></summary>

**Odpowiedź:** Jest to zazwyczaj spowodowane sygnaturami czasowymi, identyfikatorami użytkowników lub innymi szczegółowymi funkcjami wysyłanymi.

</details>

<details>
<summary><b>Stworzyłem ocenę offline i udało się niemal natychmiast. Dlaczego? Nie widzę żadnych wyników?</b></summary>

**Odpowiedź**: Ocena w trybie offline używa danych modelu przeszkolonego z zdarzeń w tym okresie. Jeśli nie wysłać żadnych danych w okresie między rozpoczęciem i końcem oceny, zostanie zakończona bez żadnych wyników. Prześlij nową ocenę w trybie offline, wybierając zakres czasu ze zdarzeniami, o których wiadomo, że zostały wysłane do Personalizer.

</details>


## <a name="learning-policy"></a>Polityka edukacyjna

<details>
<summary><b>Jak zaimportować zasady uczenia się?</b></summary>

**Odpowiedź**: Dowiedz się więcej o [pojęciach zasad uczenia się](concept-active-learning.md#understand-learning-policy-settings) i [o tym, jak zastosować](how-to-manage-model.md) nowe zasady uczenia się. Jeśli nie chcesz wybierać zasad uczenia się, możesz użyć [oceny w trybie offline,](how-to-offline-evaluation.md) aby zaproponować zasady uczenia się na podstawie bieżących zdarzeń.

</details>

## <a name="security"></a>Zabezpieczenia

<details>
<summary><b>Klucz interfejsu API dla mojej pętli został naruszony. Co mogę zrobić?</b></summary>

**Odpowiedź:** Można ponownie wygenerować jeden klucz po zamianie klientów, aby użyć drugiego klucza. Posiadanie dwóch klawiszy pozwala propagować klucz w leniwy sposób bez konieczności żadnych przestojów. Zalecamy wykonanie tego w regularnym cyklu jako środek bezpieczeństwa.

</details>

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie częstotliwości aktualizacji modelu](how-to-settings.md#model-update-frequency)