---
title: Uczenie modelu — niestandardowe w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Uczenia modelu jest ważnym krokiem podczas tworzenia modelu tłumaczenia. Szkolenie odbywa się na podstawie na dokumentach, wybrany dla tego szkoleniach.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 9ec8cbe3d2467714a4b2586db79566aaef30d6d7
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627424"
---
# <a name="train-a-model"></a>Szkolenie modelu

Uczenia modelu jest ważnym krokiem do tworzenia modelu tłumaczenia, ponieważ bez szkolenia, nie można utworzyć modelu. Szkolenie odbywa się na podstawie na dokumentach, wybrany dla szkoleniach.

Do nauczenia modelu:

1.  Wybierz projekt, w którym chcesz utworzyć model.

2.  Na karcie danych dla projektu zostaną wyświetlone wszystkie dokumenty dla pary języka projektu. Ręcznie wybrać dokumenty, które chcesz użyć do uczenia modelu. Możesz wybrać szkolenia, dostosowywania i testowania dokumentów za pośrednictwem tego ekranu. Również możesz po prostu wybrać zestaw szkoleniowy i niestandardowe w usłudze Translator tworzenie, dostosowywanie i testowanie zestawy dla Ciebie.

    -  Nazwa dokumentu: nazwa dokumentu.

    -  Parowanie: Jeśli ten dokument jest równoległe lub jednojęzyczne dokumentu.

    - Jednojęzyczne dokumenty nie są obecnie obsługiwane dla szkolenia.

    -  Typ dokumentu: może być szkolenia, dostosowywania, testy lub słownika.

    -  Para języka: Pokaż to źródłowy i docelowy język dla projektu.

    -  Źródło zdania: Pokazuje liczbę wyodrębnione ze zdań
    - plik źródłowy.

    -  Docelowa zdania: Pokazuje liczbę wyodrębnione ze zdań
    - Plik docelowy.

    ![Trenowanie modelu](media/how-to/how-to-train-model.png)

3.  Kliknij przycisk pociągu.

4.  W oknie dialogowym Określ nazwę dla modelu.

5.  Kliknij Train model.

    ![Szkolenie modelu w oknie dialogowym](media/how-to/how-to-train-model-2.png)

6.  Niestandardowe w usłudze Translator przesłać szkolenia i wyświetlić stan szkolenia na karcie modeli.

    ![Szkolenie modelu strony](media/how-to/how-to-train-model-3.png)


## <a name="edit-a-model"></a>Edytowanie modelu

Możesz edytować model przy użyciu łącza edycji na stronie szczegółów modelu.

1.  Kliknij ikonę ołówka.

    ![Edytuj model](media/how-to/how-to-edit-model.png)

2.  W polu Zmień okna dialogowego

    1.  Model (wymagane): nadaj nazwę opisową modelu.

        ![Więcej okno dialogowe Edycja](media/how-to/how-to-edit-model-dialog.png)

3.  Kliknij pozycję Zapisz.


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, [sposobu wyświetlania szczegółów modelu](how-to-view-model-details.md).
