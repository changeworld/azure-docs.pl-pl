---
title: Czy przeprowadzić migrację obszaru roboczego i projektów usługi Microsoft Translator Hub? - Tłumacz niestandardowy
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak przeprowadzić migrację obszaru roboczego centrum i projektów do usługi Azure Cognitive Services Custom Translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 2fa90a8099778bf37ce8534e968a2b1b4345c2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75446777"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrowanie obszaru roboczego centrum i projektów do usługi Custom Translator

Obszar roboczy [usługi Microsoft Translator Hub](https://hub.microsofttranslator.com/) i projekty można łatwo migrować do usługi Translator. Migracja jest inicjowana z centrum Microsoft Hub przez wybranie obszaru roboczego lub projektu, a następnie wybranie obszaru roboczego w usłudze Custom Translator, a następnie wybranie szkoleń, które chcesz przenieść. Po rozpoczęciu migracji wybrane ustawienia szkolenia zostaną przeniesione wraz ze wszystkimi odpowiednimi dokumentami. Wdrożone modele są szkolone i mogą być automatycznie rozmieszczane po zakończeniu.

Te akcje są wykonywane podczas migracji:
* Wszystkie dokumenty i definicje projektów będą miały swoje nazwy przeniesione z dodatkiem "hub_" poprzedzonym nazwą. Automatycznie wygenerowane dane testowe i strojenia\<zostaną nazwane hub_systemtune_> lub\<hub_systemtest_ modelid>.
* Wszystkie szkolenia, które były w stanie wdrożonym podczas migracji zostanie automatycznie przeszkolony przy użyciu dokumentów szkolenia centrum. To szkolenie nie zostanie obciążone subskrypcją. Jeśli dla migracji wybrano automatyczne wdrażanie, przeszkolony model zostanie wdrożony po zakończeniu. Będą pobierane regularne opłaty hostingowe.
* Wszystkie migrowane szkolenia, które nie były w stanie wdrożonym zostaną umieszczone w stanie projekt zmigrowany. W tym stanie będziesz mieć możliwość szkolenia modelu z migrowanym zdefiniowaniem, ale będą obowiązywać regularne opłaty szkoleniowe.
* W dowolnym momencie wynik BLEU migrowane z centrum szkolenia można znaleźć na TrainingDetails strony modelu w "Wynik Bleu w MT Hub" nagłówek.

> [!Note] 
> Aby szkolenie zakończyło się pomyślnie, usługa Custom Translator wymaga co najmniej 10 000 unikatowych wyodrębnionych zdań. Tłumacz niestandardowy nie może przeprowadzić szkolenia z mniejszą liczbą niż [sugerowane minimum.](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences)

## <a name="find-custom-translator-workspace-id"></a>Znajdowanie identyfikatora niestandardowego obszaru roboczego tłumacza

Aby przeprowadzić migrację obszaru roboczego [Centrum usługi Microsoft Translator,](https://hub.microsofttranslator.com/) potrzebny jest docelowy identyfikator obszaru roboczego w usłudze Translator niestandardowy. Docelowy obszar roboczy w uzywce Niestandardowy jest, gdzie wszystkie obszary robocze centrum i projekty są migrowane do.

Docelowy identyfikator obszaru roboczego znajduje się na stronie Ustawienia tłumacza niestandardowego:

1. Przejdź do strony "Ustawianie" w portalu Translatora niestandardowego.

2. Identyfikator obszaru roboczego znajduje się w sekcji Informacje podstawowe.

    ![Jak znaleźć identyfikator docelowego obszaru roboczego](media/how-to/how-to-find-destination-ws-id.png)

3. Zachowaj docelowy identyfikator obszaru roboczego, aby odwoływać się podczas procesu migracji.

## <a name="migrate-a-project"></a>Migrowanie projektu

Jeśli chcesz przeprowadzić selektywną migrację projektów, usługa Microsoft Translator Hub daje tę możliwość.

Aby przeprowadzić migrację projektu:

1. Zaloguj się do Centrum microsoft translatora.

2. Przejdź do strony "Projekty".

3. Kliknij łącze "Migruj" dla odpowiedniego projektu.

    ![Jak przeprowadzić migrację z centrum](media/how-to/how-to-migrate-from-hub.png)

4. Po naciśnięciu linku migracji zostanie wyświetlony formularz umożliwiający:
   * Określanie obszaru roboczego, do którego chcesz przenieść w u translatora niestandardowego
   * Określ, czy chcesz przenieść wszystkie szkolenia z udanymi szkoleniami, czy tylko z wdrożonymi szkoleniami. Domyślnie wszystkie udane szkolenia zostaną przeniesione.
   * Określ, czy chcesz, aby twoje automatyczne szkolenie było wdrażane po zakończeniu szkolenia. Domyślnie szkolenie nie zostanie automatycznie wdrożone po zakończeniu.

5. Kliknij "Wyślij wniosek".

## <a name="migrate-a-workspace"></a>Migrowanie obszaru roboczego

Oprócz migracji pojedynczego projektu można również przeprowadzić migrację wszystkich projektów z udanymi szkoleniami w obszarze roboczym. Spowoduje to, że każdy projekt w obszarze roboczym mają być oceniane tak, jakby link migracji został naciśnięty. Ta funkcja jest odpowiednia dla użytkowników z wieloma projektami, którzy chcą przeprowadzić migrację wszystkich do usługi Custom Translator z tymi samymi ustawieniami. Migrację obszaru roboczego można zainicjować na stronie ustawień Usługi Translator.

Aby przeprowadzić migrację obszaru roboczego:

1. Zaloguj się do Centrum microsoft translatora.

2. Przejdź do strony "Ustawienia".

3. Na stronie "Ustawienia" kliknij "Migruj dane obszaru roboczego do niestandardowego tłumacza".

    ![Jak przeprowadzić migrację z centrum](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Na następnej stronie wybierz jedną z tych dwóch opcji:

    a. Tylko wdrożone szkolenia: wybranie tej opcji spowoduje migrację tylko wdrożonych systemów i powiązanych dokumentów.

    b. Wszystkie udane szkolenia: wybranie tej opcji spowoduje migrację wszystkich udanych szkoleń i powiązanych dokumentów.

    d. Wprowadź docelowy identyfikator obszaru roboczego w uzywaczu Niestandardowy.

    ![Jak przeprowadzić migrację z centrum](media/how-to/how-to-migrate-from-hub-screen.png)

5. Kliknij pozycję Prześlij żądanie.

## <a name="migration-history"></a>Historia migracji

Po zażądaniu migracji obszaru roboczego/projektu z centrum historia migracji znajdziesz na stronie Niestandardowe ustawienia tłumacza.

Aby wyświetlić historię migracji, wykonaj następujące czynności:

1. Przejdź do strony "Ustawianie" w portalu Translatora niestandardowego.

2. W sekcji Historia migracji na stronie Ustawienia kliknij pozycję Historia migracji.

    ![Historia migracji](media/how-to/how-to-migration-history.png)

Strona Historia migracji wyświetla następujące informacje jako podsumowanie dla każdej żądanej migracji.

1. Migrowane według: nazwa i adres e-mail użytkownika przesłanego tego żądania migracji

2. Migrowane w dniu: data i godzina sygnatury migracji

3. Projekty: Liczba projektów wymaganych do migracji v/s liczba projektów pomyślnie zmigrowanych.

4. Szkolenia: Liczba szkoleń wymaganych do migracji v / s liczba szkoleń pomyślnie migrowane.

5. Dokumenty: Liczba dokumentów wymaganych do migracji v/s liczba dokumentów pomyślnie zmigrowanych.

    ![Szczegóły historii migracji](media/how-to/how-to-migration-history-details.png)

Jeśli chcesz uzyskać bardziej szczegółowy raport migracji dotyczący projektów, szkoleń i dokumentów, masz opcję eksportu szczegółów jako CSV.

## <a name="implementation-notes"></a>Uwagi dotyczące implementacji
* Systemy z parami językowymi, które nie są jeszcze dostępne w udzielce niestandardowej, będą dostępne tylko w celu uzyskania dostępu do danych lub cofnięcia dostępu za pośrednictwem usługi Custom Translator. Te projekty zostaną oznaczone jako "Niedostępne" na stronie Projekty. W miarę włączania nowych par językowych za pomocą usługi Custom Translator projekty będą aktywne w szkoleniu i wdrażaniu. 
* Migracja projektu z centrum do usługi Custom Translator nie będzie miała żadnego wpływu na szkolenia lub projekty w centrum. Nie usuwamy projektów ani dokumentów z centrum podczas migracji i nie cofamy modeli.
* Migracja jest dozwolona tylko raz na projekt. Jeśli chcesz powtórzyć migrację w projekcie, skontaktuj się z nami.
* Usługa Custom Translator obsługuje pary języków NMT do i z języka angielskiego. [Wyświetl pełną listę obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization). Koncentrator nie wymaga modeli linii bazowych i dlatego obsługuje kilka tysięcy języków. Można migrować nieobsługiwałą parę języków, jednak będziemy przeprowadzać migrację tylko dokumentów i definicji projektu. Nie będziemy w stanie trenować nowego modelu. Ponadto te dokumenty i projekty będą wyświetlane jako nieaktywne, aby wskazać, że nie mogą być używane w tej chwili. Jeśli zostanie dodana pomoc dla tych projektów i/lub dokumentów, staną się one aktywne i możliwe do przeszkolenia.
* Usługa Custom Translator nie obsługuje obecnie jednojęzycznych danych szkoleniowych. Podobnie jak nieobsługiwały pary języków, można migrować dokumenty jednojęzyczne, ale są one wyświetlane jako nieaktywne, dopóki dane jednojęzyczne nie są obsługiwane.
* Custom Translator wymaga 10k równoległych zdań w celu szkolenia. Microsoft Hub może trenować na mniejszym zestawie danych. Jeśli szkolenie zostanie przeniesione, co nie spełnia tego wymogu, nie zostanie przeszkolone.

## <a name="custom-translator-versus-hub"></a>Tłumacz niestandardowy a koncentrator

W tej tabeli porównano funkcje między Centrum usługi Microsoft Translator i translatorem niestandardowym.

|   | Koncentrator | Custom Translator |
|:-----|:----:|:----:|
|Stan funkcji dostosowywania   | Ogólna dostępność  | Ogólna dostępność |
| Wersja interfejsu API tekstu  | Wersja 2    | Wersja V3  |
| Dostosowywanie SMT | Tak   | Nie |
| Dostosowywanie NMT | Nie    | Tak |
| Nowe ujednolicone dostosowanie usług mowy | Nie    | Tak |
| Brak śladu | Tak | Tak |

## <a name="new-languages"></a>Nowe języki

Jeśli jesteś społecznością lub organizacją pracującą nad utworzeniem nowego systemu [custommt@microsoft.com](mailto:custommt@microsoft.com) językowego dla usługi Microsoft Translator, skontaktuj się z nami, aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki

- [Trenuj model](how-to-train-model.md).
- Zacznij korzystać z wdrożonego niestandardowego modelu tłumaczenia za pośrednictwem [interfejsu API tekstu usługi Microsoft Translator w wersji 3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
