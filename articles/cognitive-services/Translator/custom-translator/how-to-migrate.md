---
title: Migracja obszaru roboczego Microsoft Translator Hub i projektów? — Niestandardowy w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Migrowanie Centrum obszar roboczy i projekty do niestandardowych w usłudze Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 378baad0735238dc0921e5e78e2a27b3ae907e19
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627798"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrowanie centrum obszaru roboczego i projekty do niestandardowych w usłudze Translator

Można migrować swoje [Microsoft Translator Hub](https://hub.microsofttranslator.com/) obszar roboczy i projekty do niestandardowych w usłudze Translator. Migracja rozpoczyna się z Centrum.


Elementy te są migrowane podczas procesu:

1.  Definicje projektów.

2.  Definicja szkolenia będzie używana w taki sposób, aby utworzyć nową definicję modelu na niestandardowe w usłudze Translator.

3.  Równoległych i jednojęzyczne pliki używane w szkoleniach będą migrowane jako nowe dokumenty w niestandardowych w usłudze Translator.

4.  Test systemu wygenerowany automatycznie i dostrajanie danych zostanie wyeksportowany i utworzony jako nowe dokumenty w niestandardowych w usłudze Translator.

Dla wszystkich wdrożonych szkoleniach niestandardowe w usłudze Translator zostanie uczenie modelu bez ponoszenia żadnych kosztów. Istnieje możliwość ręcznego wdrażania ich.

>[!Note]
>Szkolenia zakończyło się sukcesem, niestandardowe w usłudze Translator wymaga minimalnej zdania wyodrębnione 10 000. Mniejszą liczbę wyodrębnione zdania niż [sugerowane minimum](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences), niestandardowe w usłudze Translator nie należy przeprowadzić szkolenie.

Wszystkie pomyślne szkolenia, które nie są wdrażane, zostaną one migracji jako wersję roboczą w niestandardowych w usłudze Translator.

## <a name="find-custom-translator-workspace-id"></a>Znajdź identyfikator obszaru roboczego niestandardowych w usłudze Translator

Aby przeprowadzić migrację [Microsoft Translator Hub](https://hub.microsofttranslator.com/) obszar roboczy należy docelowy identyfikator obszaru roboczego w niestandardowych w usłudze Translator. Docelowy obszar roboczy w niestandardowych w usłudze Translator to, gdzie Centrum obszary robocze i projekty są migrowane do.

Można znaleźć lokalizacji docelowej identyfikator obszaru roboczego na stronie Ustawienia w usłudze Translator niestandardowe: 

1.  Przejdź do strony "Ustawienia" w portalu niestandardowe w usłudze Translator.

2.  Identyfikator obszaru roboczego można znaleźć w sekcji podstawowe informacje.

    ![Jak znaleźć identyfikator obszaru roboczego w docelowej](media/how-to/how-to-find-destination-ws-id.png)

3. Zachowaj miejsce docelowe kopii identyfikator obszaru roboczego do odwoływania się podczas procesu migracji.

## <a name="migrate-workspace"></a>Migracja obszaru roboczego

Podczas migracji obszaru pełną koncentratora do niestandardowych w usłudze translator, projekty, dokumentów i szkoleniach migrowani do niestandardowych w usłudze translator. Przed migracją masz do wyboru, jeśli chcesz przeprowadzić migrację tylko wdrożone szkoleniach lub chcesz przeprowadzić migrację, wszystkie Twoje pomyślne szkoleniach.

Aby przeprowadzić migrację obszaru roboczego:

1.  Zaloguj się do Centrum usługi Microsoft Translator.

2.  Przejdź do strony "Ustawienia".

3.  Na stronie "Ustawienia" kliknij "Dane migracji w obszarze roboczym do niestandardowych w usłudze Translator".

    ![Jak przeprowadzić migrację z Centrum](media/how-to/how-to-migrate-workspace-from-hub.png)

4.  Na następnej stronie wybierz jedną z tych dwóch opcji:

    a.  Tylko wdrożone szkoleniach: Wybranie tej opcji spowoduje zmigrowanie tylko wdrożone systemy i powiązanych dokumentów.

    b.  Wszystkie pomyślne szkolenia: Wybranie tej opcji zostaną zmigrowane, pomyślne szkoleniach i powiązanych dokumentów.

    c.  Wprowadź identyfikator obszaru roboczego lokalizacji docelowej w niestandardowych w usłudze Translator.

    ![Jak przeprowadzić migrację z Centrum](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Kliknij opcję Prześlij żądanie.

## <a name="migrate-project"></a>Przeprowadź migrację projektu

Jeśli użytkownik chce migrować swoje projekty selektywnie, Microsoft Translator Hub umożliwia tym.

Aby przeprowadzić migrację projektu:

1.  Zaloguj się do Centrum usługi Microsoft Translator.

2.  Przejdź do strony "Projekty".

3.  Kliknij łącze "Migracji" dla odpowiedniego projektu.

    ![Jak przeprowadzić migrację z Centrum](media/how-to/how-to-migrate-from-hub.png)

4.  Na następnej stronie wybierz jedną z tych dwóch opcji:

    a.  Tylko wdrożone szkoleniach: Wybranie tej opcji spowoduje zmigrowanie tylko wdrożone systemy i powiązanych dokumentów. 

    b.  Wszystkie pomyślne szkolenia: Wybranie tej opcji zostaną zmigrowane, pomyślne szkoleniach i powiązanych dokumentów.

    c.  Wprowadź identyfikator obszaru roboczego lokalizacji docelowej w niestandardowych w usłudze Translator.

    ![Jak przeprowadzić migrację z Centrum](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Kliknij przycisk "Prześlij żądanie".

## <a name="migration-history"></a>Migracji historii

Jeśli żądanego obszaru roboczego / projekt migracji z Centrum, znajdziesz historię migracji na stronie Ustawienia w usłudze Translator niestandardowe. 

Aby wyświetlić historię migracji, wykonaj następujące kroki:

1.  Przejdź do strony "Ustawienia" w portalu niestandardowe w usłudze Translator.

2.  W sekcji historię migracji na stronie ustawień kliknij przycisk historię migracji.

    ![Migracji historii](media/how-to/how-to-migration-history.png)

Migracji historii stronie są wyświetlane następujące informacje, jak podsumowanie dla każdej migracji, której szukasz.

1.  Migrowane przez: Nazwa i adres e-mail użytkownika na tej migracji żądanie przesłane

2.  Data migracji: Data i godzina sygnatury migracji

3.  Projektów: Liczba zażądano ilości v/s migracji projektów pomyślnie przeprowadzono migrację projektów.

4.  Szkolenia: Liczba szkoleniach zażądano v/s migracji na liczbę szkoleniach pomyślnie przeprowadzono migrację.

5.  Dokumenty: Liczba dokumentów zażądano pomyślnie przeprowadzono migrację v/s migracji liczba dokumentów.

    ![Szczegóły historii migracji](media/how-to/how-to-migration-history-details.png)

Jeśli chcesz bardziej szczegółowych raport z migracji o szkoleniach, projekty i dokumenty, masz opcję Szczegóły eksportu jako plik CSV.

>[!Note]
>Migracja jest obsługiwana tylko na kierunki, w której istnieje NMT języków. Sprawdź listę aktualnie [obsługiwane języki NMT](https://www.microsoft.com/translator/business/languages/). Kierunki, gdzie NMT języków nie istnieje dane zostaną przeniesione z Centrum do niestandardowych w usłudze Translator, ale szkoleniach nie może być przeprowadzane na te kierunki.

## <a name="next-steps"></a>Kolejne kroki

- [Uczenie modelu](how-to-train-model.md).
- Rozpoczynanie korzystania z modelu tłumaczenia niestandardowych wdrożone za pośrednictwem [V3 interfejs API tekstu usługi Translator firmy Microsoft](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).