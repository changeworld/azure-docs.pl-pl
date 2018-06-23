---
title: Jak używać oczekiwania i akcji bez oczekiwania z aplikacją uczeń konwersacji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Informacje o sposobie korzystania z aplikacji uczeń konwersacji oczekiwania i akcji bez oczekiwania.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bb2cbd55b6c8be51213095926bb592169613d1fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348581"
---
# <a name="wait-and-non-wait-actions"></a>Zaczekaj i akcji bez oczekiwania

W tym samouczku pokazano różnicę między oczekiwania działania i bez oczekiwania działania w uczeń konwersacji.

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczka

    npm run tutorial-general

## <a name="details"></a>Szczegóły

- Poczekaj akcji: po systemie przyjmuje akcji "Czekaj", będzie zatrzymać wykonywanie akcji i poczekaj, dane wejściowe użytkownika.
- Akcji bez oczekiwania: po system przyjmuje akcji "bez oczekiwania", zostanie on natychmiast wybierz innej akcji (bez oczekiwania na użytkownika inpu najpierw).

## <a name="steps"></a>Kroki

### <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

1. W Interfejsie użytkownika sieci Web kliknij przycisk nowej aplikacji
2. W polu Nazwa wprowadź WaitNonWait. Następnie kliknij przycisk Utwórz.

### <a name="create-the-first-wait-action"></a>Utwórz pierwszą akcją oczekiwania

1. Kliknij przycisk akcje, a następnie nowej akcji.
2. W odpowiedzi wprowadź "zwierzę, które chcesz?".
    - Jest to akcja oczekiwania, oczekiwanie na zaznaczonym polem odpowiedzi, a więc zostaw.
3. Kliknij przycisk Gotowe.

### <a name="create-a-non-wait-action"></a>Tworzenie akcji bez oczekiwania

1. Kliknij przycisk Nowa akcja
2. W odpowiedzi wpisz "Krów powiedzieć moo".
3. Usuń zaznaczenie oczekiwanie na odpowiedź pole wyboru.
4. Kliknięcie pozycji Utwórz

### <a name="create-a-second-non-wait-action"></a>Tworzenie drugiej akcji bez oczekiwania

1. Kliknij przycisk Nowa akcja
2. W odpowiedzi wpisz "Kaczki powiedzieć quack".
3. Usuń zaznaczenie oczekiwanie na odpowiedź pole wyboru.
4. Kliknięcie pozycji Utwórz

![](../media/tutorial2_actions.PNG)

### <a name="train-the-bot"></a>Szkolenie bot

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
2. Typ "hello"
3. Kliknij wynik akcji, a następnie wybierz "zwierzę, które chcesz?".
4. Wprowadź "krów"
5. Kliknij wynik akcji, a następnie wybierz "Krów powiedzieć moo".
    - Zauważ, że bot nie będzie czekać na dane wejściowe i spowoduje przejście do następnej akcji.
2. Wybierz opcję "zwierzę, które chcesz?".
3. Wprowadź "kaczka"
5. Kliknij wynik akcji, a następnie wybierz "Kaczki powiedzieć quack".

![](../media/tutorial2_dialogs.PNG)

Zanotuj sekwencję odpowiedzi bot w odniesieniu do oczekiwania i akcji bez oczekiwania.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do jednostek](./3-introduction-to-entities.md)
