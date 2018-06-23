---
title: Jak dodać wbudowanych jednostek aplikacji uczeń konwersacji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak dodać wbudowanych jednostek aplikacji uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f014464419bfac39a9e57e679fcd28a737e9ebdb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349344"
---
# <a name="how-to-add-pre-built-entities"></a>Jak dodać wbudowanych jednostek
W tym samouczku przedstawiono sposób dodawania "wstępnie skompilowany" jednostek aplikacji uczeń konwersacji.

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczka

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Wbudowanych jednostek rozpoznaje popularne typy jednostek, takich jak liczb, dat, ceny i innych użytkowników.  W przeciwieństwie do niestandardowej jednostki pracy "out of box" i nie wymagają żadnych szkolenia.  W przeciwieństwie do obiektów niestandardowych nie można zmienić ich zachowania.  Domyślnie wbudowanych jednostek wielowartościowe — to znaczy bot pamięci będą gromadzone co zidentyfikowanych wystąpienia jednostki.

## <a name="steps"></a>Kroki

### <a name="create-the-application"></a>Tworzenie aplikacji

1. W Interfejsie użytkownika sieci Web kliknij przycisk nowej aplikacji
2. W polu Nazwa wprowadź BuiltInEntities. Następnie kliknij przycisk Utwórz.

### <a name="create-an-entity"></a>Tworzenie jednostki

1. Kliknij przycisk jednostek, a następnie nowej jednostki.
2. Polecenie EntityType listy rozwijanej, a następnie wybierz datetimev2.
    - Opcje programowalny i Negatable są wyłączone, ponieważ nie mają zastosowania do wstępnego tworzenia jednostek.
3. Kliknij pozycję Utwórz.

![](../media/tutorial7_entities.PNG)

### <a name="create-two-actions"></a>Utwórz dwie akcje

1. Kliknij przycisk akcje, a następnie nowa akcja
2. W odpowiedzi, wpisz "Data jest $luis-datetimev2".
3. Kliknij pozycję Utwórz.

![](../media/tutorial7_actions.PNG)

Następnie należy utworzyć drugą akcję:

1. Kliknij przycisk akcje, a następnie nową akcję w celu utworzenia drugiej akcji.
3. W odpowiedzi wpisz "Co to jest data?".
4. W dyskwalifikacji jednostki wprowadź "luis datetimev2".
4. Kliknięcie pozycji Utwórz

![](../media/tutorial7_actions2.PNG)

Teraz masz dwie akcje.

### <a name="train-the-bot"></a>Szkolenie bot

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
2. Wpisz tekst "hello".
3. Kliknij wynik akcji, a następnie wybierz pozycję "Co to jest data?"
2. Wprowadź "dzisiaj". 
    - Obecnie jest oznakowany, a pokazywany w drugim wierszu, ponieważ jest wstępnie utworzonej jednostki i nie można edytować.
5. Kliknij przycisk wynik akcji
    - Należy zauważyć, że teraz Data pojawia się w sekcji pamięci jednostki. 
    - Jeśli myszą daty, zostanie wyświetlony dodatkowe dane dostarczone przez LUIS, które są wykorzystywane i dodatkowe mogą być przetwarzane w kodzie. 
6. Wybierz opcję "Data jest $luis-datetimev2".
7. Kliknij przycisk Done nauczania

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Alternatywne dane wejściowe](./8-alternative-inputs.md)
