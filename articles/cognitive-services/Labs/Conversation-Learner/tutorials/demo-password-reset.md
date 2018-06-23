---
title: Pokaz uczeń konwersacji aplikacji resetowania hasła - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak utworzyć demonstrację aplikacji uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 24d61787a79ee1a1a9737c417aa966cc8fd75930
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348613"
---
# <a name="demo-password-reset"></a>Pokaz: Resetowanie hasła
Ten pokaz przedstawia robotów proste pomocy technicznej pomagające w resetowanie haseł. 

Pokazuje, jak uczeń konwersacji można znaleźć przepływów nieuproszczony okna dialogowego, Włącz wielu sekwencji, tym klasy poza domeną. Ten pokaz nie korzysta z kodu ani jednostek.

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa bot resetowania hasła

    npm run demo-password

### <a name="open-the-demo"></a>Otwórz pokaz

Na liście aplikacji w interfejsie użytkownika sieci web kliknij resetowania hasła pokaz samouczka. 

### <a name="actions"></a>Akcje

Utworzono zestaw akcji, gdy użytkownik szuka pomocy ze swojego hasła, w tym rozwiązania.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Szkolenie w oknach dialogowych

Istnieje kilka okien dialogowych szkolenia. Dostępne są także pokazy poza klasy domeny — na przykład żądania użytkownika, takie jak "zwiększają instrukcjami" są spoza domeny; bot podano przykłady kilka poza żądania domeny oraz mogą odpowiadać, podając "I nie może pomóc ze specyfikacją."

![](../media/tutorial_pw_reset_entities.PNG)

Na przykład spróbujmy sesji nauczania.

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
1. Wprowadź "I utraty hasła".
2. Kliknij przycisk wynik akcji.
3. Kliknij, aby wybrać "Jest to dla konta lokalnego lub konta Microsoft?"
4. Wprowadź "Konta lokalnego".
5. Kliknij przycisk wynik akcji.
3. Kliknij, aby wybrać "wersji systemu Windows, czy masz?"
4. Wprowadź "systemu Windows 8'.
5. Kliknij przycisk wynik akcji.
6. Wybierz opcję "rozwiązania: sposób resetowania hasła w systemie Windows 8."
4. Kliknij przycisk Done nauczania.

Poniżej przedstawiono, jak bot można znaleźć klasy poza domeną.

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
1. Wprowadź "Wyszukiwanie w sieci web".
    - To jest przykład klasy poza domeną. 
2. Kliknij przycisk wynik akcji.
3. Kliknij, aby wybrać "Niestety, I nie może pomóc ze specyfikacją".
    - Należy zauważyć, że wynik dla tej opcji jest aktualnie niska. Jednak po nauczania nieco więcej wyższy uzyskają wynik.
4. Kliknij przycisk Done nauczania.

Ma teraz widoczna, jak utworzyć pokaz podstawowej pomocy technicznej i jak można znaleźć dostarczanie rozwiązań, a także obsługiwać poza przykładowe zapytania.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wersja demonstracyjna - pizza kolejności](./demo-pizza-order.md)
