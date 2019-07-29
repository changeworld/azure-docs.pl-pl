---
title: Migracja obszaru roboczego Microsoft Translator Hub i projektów? — Niestandardowy w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Migrowanie Centrum obszar roboczy i projekty do niestandardowych w usłudze Translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cd821ad8fce813d269ace8fb4945cb796c2ae758
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595737"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrowanie centrum obszaru roboczego i projekty do niestandardowych w usłudze Translator

Możesz z łatwością migrować swoje [Microsoft Translator Hub](https://hub.microsofttranslator.com/) obszar roboczy i projekty do niestandardowych w usłudze Translator. Migracja jest inicjowane z Microsoft Hub przez wybranie obszaru roboczego lub projektu, a następnie wybierając obszar roboczy w niestandardowych w usłudze Translator, a następnie wybierając szkoleniach, którą chcesz przenieść. Po rozpoczęciu migracji ustawień wybranych szkoleń zostanie przeniesiona z wszystkich odpowiednich dokumentach. Wdrożone modele są uczone i może być autodeployed po zakończeniu.

Te akcje są wykonywane podczas migracji:
* Wszystkie dokumenty i definicje projekt będzie mieć ich nazwy, przeniesiona dodając "hub_" poprzedza nazwę. Automatycznie generowane dane testów i dostrajania będą mieć nazwę hub_systemtune_\<ModelId > lub hub_systemtest_\<ModelId >.
* Wszelkie szkoleniach, które zostały wdrożone, gdy odbywa się migracja będzie automatycznie uczony przy użyciu dokumentów szkolenia koncentratora. To szkolenie nie zostanie obciążona do Twojej subskrypcji. Jeśli zostanie automatycznie wdrożona została wybrana do migracji uczonego modelu zostanie wdrożony po zakończeniu. Regularne hostingu opłaty zostaną zastosowane.
* Zmigrowane szkoleniach, które nie zostały wdrożone, będą umieszczone w stanie zmigrowanych wersji roboczej. W ten stan będzie mieć możliwość uczenia modelu przy użyciu definicji zmigrowane, ale zostaną naliczone opłaty regularne szkolenie.
* W dowolnym momencie wynik BELEU migrowane z Centrum szkolenia można znaleźć na stronie TrainingDetails modelu w "Beleu wynik w Centrum MT" nagłówek.

> [!Note]
> Aby szkolenie zakończyło się pomyślnie, translator niestandardowy wymaga co najmniej 10 000 unikatowych rozpakowanych zdań. Translator niestandardowy nie może przeprowadzić szkolenia z mniejszą ilością niż Sugerowana wartość [minimalna](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences).

## <a name="find-custom-translator-workspace-id"></a>Znajdź identyfikator obszaru roboczego niestandardowych w usłudze Translator

Aby przeprowadzić migrację [Centrum w usłudze Translator firmy Microsoft](https://hub.microsofttranslator.com/) obszaru roboczego, należy docelowy identyfikator obszaru roboczego w niestandardowych w usłudze Translator. Docelowy obszar roboczy w niestandardowych w usłudze Translator to, gdzie Centrum obszary robocze i projekty są migrowane do.

Można znaleźć lokalizacji docelowej identyfikator obszaru roboczego na stronie Ustawienia w usłudze Translator niestandardowe:

1. Przejdź do strony "Ustawienia" w portalu niestandardowe w usłudze Translator.

2. Identyfikator obszaru roboczego można znaleźć w sekcji podstawowe informacje.

    ![Jak znaleźć identyfikator obszaru roboczego w docelowej](media/how-to/how-to-find-destination-ws-id.png)

3. Zachowaj miejsce docelowe kopii identyfikator obszaru roboczego do odwoływania się podczas procesu migracji.

## <a name="migrate-a-project"></a>Migracja projektu

Jeśli użytkownik chce migrować swoje projekty selektywnie, Microsoft Translator Hub umożliwia tym.

Aby przeprowadzić migrację projektu:

1. Zaloguj się do Centrum usługi Microsoft Translator.

2. Przejdź do strony "Projekty".

3. Kliknij łącze "Migracji" dla odpowiedniego projektu.

    ![Jak przeprowadzić migrację z Centrum](media/how-to/how-to-migrate-from-hub.png)

4. Po naciśnięciu łącze migracji zostaną wyświetlone za pomocą formularza, co umożliwia:
   * Określ obszar roboczy, który chcesz przenieść do na niestandardowe w usłudze Translator
   * Wskazuje, czy chcesz przenieść wszystkie szkolenia z szkoleń związanych z pomyślnym lub po prostu wdrożonej szkoleniach. Domyślnie wszystkie szkolenia pomyślne zostaną przesłane.
   * Wskazuje, czy chcesz usługi automatycznie szkolenia wdrożony po zakończeniu szkolenia. Domyślnie szkolenia, nie będzie ona automatycznie wdrożona, po jego ukończeniu.

5. Kliknij przycisk "Prześlij żądanie".

## <a name="migrate-a-workspace"></a>Migracja obszaru roboczego

Oprócz migracji do tego pojedynczego projektu, można również migrację wszystkich projektów o pomyślnym szkoleniach w obszarze roboczym. To spowoduje, że każdy projekt w obszarze roboczym, który ma zostać obliczone tak, jakby było został wciśnięty łącze migracji. Ta funkcja jest odpowiedni dla użytkowników z wielu projektów, które chcesz przeprowadzić migrację wszystkich z nich do niestandardowych w usłudze Translator z tymi samymi ustawieniami. Migracja obszaru nazw można zainicjować ze strony Ustawienia w usłudze Translator koncentratora.

Aby przeprowadzić migrację obszaru roboczego:

1. Zaloguj się do Centrum usługi Microsoft Translator.

2. Przejdź do strony "Ustawienia".

3. Na stronie "Ustawienia" kliknij "Dane migracji w obszarze roboczym do niestandardowych w usłudze Translator".

    ![Jak przeprowadzić migrację z Centrum](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Na następnej stronie wybierz jedną z tych dwóch opcji:

    a. Wdrożone tylko szkolenia: Wybranie tej opcji spowoduje migrację tylko wdrożonych systemów i powiązanych dokumentów.

    b. Wszystkie pomyślne szkolenia: Wybranie tej opcji spowoduje migrację wszystkich pomyślnych szkoleń i powiązanych dokumentów.

    c. Wprowadź identyfikator obszaru roboczego lokalizacji docelowej w niestandardowych w usłudze Translator.

    ![Jak przeprowadzić migrację z Centrum](media/how-to/how-to-migrate-from-hub-screen.png)

5. Kliknij opcję Prześlij żądanie.

## <a name="migration-history"></a>Migracji historii

Jeśli żądanego obszaru roboczego / projekt migracji z Centrum, znajdziesz historię migracji na stronie Ustawienia w usłudze Translator niestandardowe.

Aby wyświetlić historię migracji, wykonaj następujące kroki:

1. Przejdź do strony "Ustawienia" w portalu niestandardowe w usłudze Translator.

2. W sekcji historię migracji na stronie ustawień kliknij przycisk historię migracji.

    ![Migracji historii](media/how-to/how-to-migration-history.png)

Migracji historii stronie są wyświetlane następujące informacje, jak podsumowanie dla każdej migracji, której szukasz.

1. Zmigrowane przez: Nazwa i adres e-mail użytkownika przesłała to żądanie migracji

2. Zmigrowane: Sygnatura daty i godziny migracji

3. Projekty: Liczba projektów zażądana do migracji liczba projektów zakończonych pomyślnie.

4. Szkoleń związanych Liczba zadawanych szkoleń dotyczących liczby przemigrowanych szkoleń w przypadku migracji/s.

5. Secret Liczba dokumentów zażądanych do migracji w przypadku przeprowadzono pomyślnie migracji liczby dokumentów.

    ![Szczegóły historii migracji](media/how-to/how-to-migration-history-details.png)

Jeśli chcesz bardziej szczegółowych raport z migracji o szkoleniach, projekty i dokumenty, masz opcję Szczegóły eksportu jako plik CSV.

## <a name="implementation-notes"></a>Uwagi dotyczące implementacji
* Systemy z parami języka, które nie są jeszcze dostępne w usłudze translatora niestandardowego, będą dostępne tylko w celu uzyskania dostępu do danych lub ich rozmieszczenia za pomocą translatora niestandardowego. Te projekty zostaną oznaczone jako "niedostępne" na stronie projekty. Po włączeniu nowych par językowych przy użyciu translatora niestandardowego projekty staną się aktywne do uczenia i wdrożenia. 
* Migracja projektu z koncentratora do niestandardowych w usłudze Translator nie ma żadnego wpływu na Centrum szkolenia lub projektów. Firma Microsoft nie należy usuwać projektów lub dokumentów z Centrum podczas migracji, a firma Microsoft nie cofnięcie wdrożenia modeli.
* Tekst są dozwolone tylko po migracji na projekt. Należy powtórzyć migracji w projekcie, skontaktuj się z nami.
* Translator niestandardowy obsługuje pary językowe NMT do i z języka angielskiego. [Zapoznaj się z pełną listą obsługiwanych langauges](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization). Centrum nie wymaga modeli podstawowych i w związku z tym obsługuje kilka tysięcy języków. Jednak firma Microsoft będzie tylko dokonać migracji dokumentów, a projekt definicji, można migrować parę nieobsługiwany język. Firma Microsoft nie będzie to w opracowywaniu nowego modelu. Ponadto te dokumenty i projekty pojawi się jako nieaktywny w celu wskazania, że nie można użyć w tej chwili. Jeśli zostanie dodana jego obsługa dla projektów i/lub dokumentów, staną się aktywne i trainable.
* Niestandardowe w usłudze Translator nie obsługuje obecnie danych szkoleniowych jednojęzyczne. Jak pary nieobsługiwany język można migrować jednojęzyczne dokumentów, ale wykazują jako nieaktywne do momentu jednojęzyczne danych jest obsługiwana.
* Niestandardowe w usłudze Translator wymaga 10 KB, równoległe zdań w celu nauczenia. Microsoft Hub można uczenie na mniejszy zestaw danych. Jeśli przeprowadzono migrację szkoleń, która nie spełnia tego wymagania, nie zostanie ona przeszkolony.

## <a name="custom-translator-versus-hub"></a>Niestandardowe w usłudze Translator w stosunku do Centrum

Ta tabela zawiera zestawienie funkcji między centrum w usłudze Translator firmy Microsoft i niestandardowe w usłudze Translator.

|   | Centrum | Custom Translator |
|:-----|:----:|:----:|
|Stan funkcji dostosowywania   | Ogólna dostępność  | Ogólna dostępność |
| Interfejs API tłumaczenia tekstu w wersji  | Wersja 2    | V3  |
| Dostosowywanie SMT | Yes   | Nie |
| Dostosowywanie NMT | Nie    | Yes |
| Nowe ujednolicone dostosowywanie usług mowy | Nie    | Yes |
| Bez śledzenia | Yes | Yes |

## <a name="new-languages"></a>Nowe języki

Jeśli jesteś społecznością lub organizacją, która pracuje nad tworzeniem nowego systemu językowego dla usługi Microsoft translator, skontaktuj [custommt@microsoft.com](mailto:custommt@microsoft.com) się z, aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki

- [Uczenie modelu](how-to-train-model.md).
- Rozpoczynanie korzystania z modelu tłumaczenia niestandardowych wdrożone za pośrednictwem [V3 interfejs API tekstu usługi Translator firmy Microsoft](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
