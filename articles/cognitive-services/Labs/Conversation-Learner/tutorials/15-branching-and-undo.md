---
title: Jak używać rozgałęzianie i Cofnij operacje z aplikacją uczeń konwersacji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać rozgałęzianie i Cofnij operacje z aplikacją uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 724a9e47267e0bd7417130efe54c609ac7a465fb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348640"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Jak używać rozgałęzianie i operacji cofania
W tym samouczku będziemy będą przekazywane cofania i operacje rozgałęziania.

## <a name="details"></a>Szczegóły
- Cofnij: umożliwia deweloperowi "Cofnij" wybór danych wejściowych lub akcji użytkownika. W tle "wycofać" faktycznie tworzy nowe okno dialogowe i ponownie odgrywa do poprzedniego kroku.  Oznacza to, że wywołanie zwrotne wykrywania jednostek i interfejsu API wywołuje w oknie dialogowym zostanie ponownie wywołany.

- Gałąź: tworzy nowe okno dialogowe pociągu, który zaczyna się w taki sam sposób jak istniejące uczenia okna dialogowego — spowoduje to zapisanie prac związanych z włącza ręcznie ponownego wprowadzania okna dialogowego. W tle "gałęzi" tworzy nowe okno dialogowe i ponownie odgrywa maksymalnie wybrany krok train istniejących w oknie dialogowym.  Oznacza to, że wywołanie zwrotne wykrywania jednostek i interfejsu API wywołuje w oknie dialogowym zostanie ponownie wywołany.


## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa pizza bot kolejności:

    npm run demo-pizza

### <a name="open-the-demo"></a>Otwórz pokaz

Na liście aplikacji w interfejsie użytkownika sieci web kliknij TutorialDemo Pizza kolejności. 

Szczegółowe informacje dotyczące pokaz Pizza kolejności zobacz samouczek kolejności Pizza.

## <a name="undo"></a>Cofnij

Firma Microsoft będzie Cofnij części okna dialogowego, a następnie utwórz ją ponownie z tego kroku.

### <a name="training-dialogs"></a>Szkolenie w oknach dialogowych
Zacznijmy sesji szkolenia. 

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
1. Wprowadź "order pizza".
2. Kliknij przycisk wynik akcji.
3. Kliknij przycisk do wybierz opcję "co chcesz umieścić na Twoje pizza?"
4. Wprowadź "grzyby i ser".
5. Kliknij przycisk wynik akcji.
3. Kliknij, aby wybrać "masz $Toppings Twojego pizza".
6. Wybierz opcję "Ma inny?"
7. Wprowadź "Usuń grzyby i dodać papryki".
    - Wybierz grzyby i usuń zaznaczenie Toppings jednostki. Tworzymy akcji, które zostaną cofnięte.
2. Kliknij krok cofania.
    - Należy pamiętać, że ostatni wpis zostanie usunięty i pracujemy w "Ma inny?"  (Poniższy zrzut ekranu)
2. Wprowadź "Usuń grzyby i dodać papryki".
8. Kliknij, aby wybrać "masz $Toppings Twojego pizza"
    - Upewnij się, że oba podmioty są wybrane poprawnie.
2. Kliknij przycisk wynik akcji. Można kontynuować poprawiony okna dialogowego teraz.
4. Kliknij przycisk Done nauczania.

Sposób użycia akcji i Cofnij, aby usunąć dane wejściowe użytkownika mają teraz widoczna.

![](../media/tutorial15_undo.PNG)

## <a name="branch"></a>Rozgałęzienie

Na przykład załóżmy Otwórz istniejący okno dialogowe pociągu i utworzyć okno dialogowe pociągu przez rozgałęzianie.

1. Kliknij przycisk Train okna, a następnie "nową kolejność" Aby otworzyć okno dialogowe istniejących. 
2. Kliknij w ciągu ostatnich "nie" w oknie dialogowym (zobacz poniższy zrzut ekranu).
3. Kliknij przycisk gałęzi.
    - Należy pamiętać, że "nie" zostanie usunięta, a całe okno do tego punktu jest kopiowana do nowej. 
    - Oszczędza ponowne wprowadzenie poprzedniego włącza do eksplorowania nowej "gałęzi" z tego punktu.
1. Wprowadź wartość "tak".
2. Kliknij przycisk wynik akcji.
3. Wybierz opcję "Masz $Toppings Twojego pizza".
6. Wybierz opcję "Ma inny?"
7. Wprowadź "no".
4. Kliknij przycisk Done nauczania.

![](../media/tutorial15_branch.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przechowywanie wersji i znakowanie](./16-versioning-and-tagging.md)
