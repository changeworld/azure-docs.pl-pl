---
title: Jak używać kart z aplikacją uczeń konwersacji, część 1 - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać kart z aplikacją uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e90ccd42b21eea6139c402937be7e20513d73c84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348605"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Jak używać kart (część 1 z 2)

Ten samouczek pokazuje, jak dodać i korzystać z prostego karty w Twojej bot.

Należy pamiętać, że uczeń konwersacji oczekuje plików definicji karty jako znajdujący się w katalogu o nazwie "karty", który znajduje się w katalogu gdzie bot została uruchomiona.

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczka

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Karty są elementy interfejsu użytkownika, które umożliwiają użytkownikowi na wybranie opcji w konwersacji. 

### <a name="open-the-demo"></a>Otwórz pokaz

Na liście aplikacji w interfejsie użytkownika sieci web kliknij samouczek-13 kart 1. 

### <a name="the-card"></a>Karta

Definicja karty jest w następującej lokalizacji: C:\<installedpath\>\src\cards\prompt.json.

System oczekuje można znaleźć definicji karty w tym katalogu kart.

![](../media/tutorial13_prompt.PNG)

- Zanotuj kontrolka TextBlock i szablonu zapytania.
- Istnieją dwa przesłać przycisków i tekst, który pobiera złożonych dla każdego.

### <a name="actions"></a>Akcje

Utworzono trzy czynności. Jak widać poniżej pierwszą akcją jest kartą.

![](../media/tutorial13_actions.PNG)

Zobaczmy, jak został utworzony typ akcji karty:

![](../media/tutorial13_cardaction.PNG)

Zanotuj wprowadzania pytanie i przyciski 1 i 2. Są to odwołań szablonu, na karcie, gdzie wprowadzisz pytania i odpowiedzi odpowiednich. Można także odwoływać się i korzystać jednostek oraz ich kombinacjami tekstu i jednostek.

Ikona oka pokazuje, jak wygląda karty.

### <a name="train-dialog"></a>Okno dialogowe pociągu

Przejdźmy nauczania okna dialogowego.

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
1. Wprowadź "hi".
2. Kliknij przycisk wynik akcji.
3. Kliknij, aby wybrać "Monituj Przejdź w lewo lub w prawo".
    - Kliknięcie przycisku "left" lub "right" jest równoważna użytkownika, wpisując "left" lub "po prawej" odpowiednio. 
4. Kliknij przycisk wynik akcji.
4. Kliknij przycisk Wybierz opcję "left". Jest to akcja bez oczekiwania.
6. Kliknij, aby wybrać "Monituj Przejdź w lewo lub w prawo".
4. Kliknij przycisk "po prawej".
5. Kliknij przycisk wynik akcji.
3. Kliknij, aby wybrać "Right"
6. Kliknij, aby wybrać "Monituj Przejdź w lewo lub w prawo".
4. Kliknij przycisk Done testowania.

Teraz już wspomniano, jak działają kart. Są one zdefiniowane w katalogu karty jako szablony json. Szablony zostaną powierzchni w interfejsie użytkownika, który można wypełnić przy użyciu ciągu lub jednostki lub kombinację obu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Karty część 2](./14-cards-2.md)
