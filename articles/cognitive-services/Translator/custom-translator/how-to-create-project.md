---
title: Jak utworzyć projekt? — Niestandardowy w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Jak utworzyć projekt w niestandardowych w usłudze Translator?
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 456860c74810a692b4839e4204ec0b78d5620864
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66383005"
---
# <a name="create-a-project"></a>Tworzenie projektu

Projekt jest kontenerem dla modeli, dokumenty i testów. Każdy projekt automatycznie zawiera wszystkich dokumentów, które zostały załadowane do obszaru roboczego, które mają pary odpowiedni język.

Tworzenie projektu jest pierwszym krokiem procesu tworzenia modeli.

## <a name="create-a-project"></a>Utwórz projekt:

1.  W [niestandardowe w usłudze Translator](https://portal.customtranslator.azure.ai) portalu, kliknij polecenie Utwórz projekt.

    ![Tworzenie projektu](media/how-to/how-to-create-project.png)

2.  Wprowadź następujące szczegóły dotyczące projektu w oknie dialogowym:

    a.  Nazwa projektu (wymagane): Nadaj projektowi unikatową nazwę opisową. Nie jest konieczne podawanie języków, w polu Tytuł.

    b.  Opis: Krótkie podsumowanie dotyczące projektu. Ten opis ma żadnego wpływu na zachowanie niestandardowe Translator lub wynikowy niestandardowego systemu, ale może pomóc w rozróżnienia między różnymi projektami.

    c.  Para języka (wymagane): Wybierz język, którego jesteś tłumaczenia, z i do.

    d.  Kategoria (wymagane): Wybierz kategorię, która jest najbardziej odpowiednia dla Twojego projektu. Kategoria opisuje terminologii i stylu dokumentów, które mają zostać tłumaczenie.

    e.  Opis kategorii: Użyj tego pola, aby lepiej opisać określonego pola lub branżowych, w którym pracujesz. Na przykład jeśli kategoria ma medycyna, możesz dodać określonego dokumentu, takie zabiegu lub pediatrics. Opis nie ma żadnego wpływu na zachowanie niestandardowe Translator lub wynikowy niestandardowego systemu.

    f.  Etykieta projektu: [Etykiety projektu](workspace-and-project.md#project-labels) rozróżnia projektów za pomocą tej samej pary języka i kategorii. Najlepszym rozwiązaniem jest użycie etykietę *tylko* Jeśli planowane jest tworzenie wielu projektów dla tej samej pary języka i tej samej kategorii i ma dostęp do tych projektów za pomocą różnych CategoryID. Nie należy używać tego pola, jeśli tworzysz systemów do tylko jednej kategorii. Etykieta projektu nie jest wymagane i nie pomaga rozróżnić między parami języka. Dla wielu projektów, można użyć tej samej etykiety.

    ![Tworzenie okna dialogowego projekt](media/how-to/how-to-create-project-dialog.png)

3.  Kliknięcie pozycji Utwórz

## <a name="view-project-details"></a>Wyświetl szczegóły projektu

Strona docelowa niestandardowe w usłudze Translator zawiera 10 pierwszych projektów w obszarze roboczym. Wyświetla nazwę projektu, pary języków, kategorii, stanu i ocenę BELEU.

Po wybraniu projektu, na stronie projektu będzie widoczny jest następujący:

- CategoryID: CategoryID jest tworzona przez dołączenie identyfikator obszaru roboczego, etykieta projektu i kodu kategorii. Używasz CategoryID przy użyciu interfejsu API tekstu usługi Translator można pobrać tłumaczenia niestandardowych.

- Przycisk Train: Użyj tego przycisku, aby uruchomić [uczenia modelu](how-to-train-model.md).

- Dodaj przycisk dokumentów: Użyj tego przycisku, aby [przekazywać dokumenty](how-to-upload-document.md).

- Przycisk dokumentów filtru: Użyj tego przycisku do filtrowania i wyszukiwanie określonych dokumentów.

    ![Wyświetl szczegóły projektu](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, [jak wyszukiwanie, edytowanie, usuwanie projektu](how-to-search-edit-delete-projects.md).
- Dowiedz się, [sposób przekazywania dokumentów](how-to-upload-document.md) tworzyć modele tłumaczenia.
