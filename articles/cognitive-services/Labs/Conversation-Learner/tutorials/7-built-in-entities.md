---
title: Jak dodać wstępnie utworzone jednostki do modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak dodać wstępnie utworzone jednostki do modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2dbbf2a47cdc4240e5b0ba38658a4cb8d5307ff8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260061"
---
# <a name="how-to-add-pre-built-entities"></a>Jak dodać wstępnie utworzone jednostki
W tym samouczku przedstawiono sposób dodawania "wbudowana" jednostki do modelu uczeń konwersacji.

## <a name="video"></a>Połączenia wideo

[![Samouczek 7 (wersja zapoznawcza)](https://aka.ms/cl-tutorial-07-preview)](https://aka.ms/blis-tutorial-07)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Wstępnie utworzone jednostki rozpoznaje typowe rodzaje jednostek, takich jak liczb, dat, kwot pieniężnych i innych.  W odróżnieniu od jednostek niestandardowych Praca "out-of--box" i nie wymagają żadnych szkolenia.  W odróżnieniu od jednostek niestandardowych nie można zmienić ich zachowania.  Domyślnie, wstępnie utworzone jednostki wielowartościowe — oznacza to, bot pamięci będą gromadzone co określone wystąpienie jednostki.

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W Interfejsie użytkownika sieci Web kliknij przycisk Nowy Model
2. W polu Nazwa wprowadź BuiltInEntities. Następnie kliknij przycisk Utwórz.

### <a name="create-an-entity"></a>Tworzenie jednostki

1. Kliknij przycisk jednostki, a następnie nowej jednostki.
2. Kliknij listę rozwijaną dla obiektu, a następnie wybierz datetimev2.
    - Opcje programowalny i Negatable są wyłączone, ponieważ nie mają zastosowania do wstępnie tworzyć jednostki.
3. Kliknij pozycję Utwórz.

![](../media/tutorial7_entities.PNG)

### <a name="create-two-actions"></a>Utwórz dwie akcje

1. Kliknij pozycję operacje, a następnie nowa akcja
2. W odpowiedzi, wpisz "przypada $luis-datetimev2".
3. Kliknij pozycję Utwórz.

![](../media/tutorial7_actions.PNG)

Następnie należy utworzyć drugą akcję:

1. Kliknij pozycję operacje, a następnie nową akcję do utworzenia drugiej akcji.
3. W odpowiedzi wpisz "Co to jest data?".
4. W jednostkach dyskwalifikacji wprowadź "luis datetimev2".
4. Kliknięcie pozycji Utwórz

![](../media/tutorial7_actions2.PNG)

Masz teraz dwie akcje.

### <a name="train-the-bot"></a>Uczenie bota

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
2. Wpisz "hello".
3. Kliknij wynik akcji, a następnie wybierz pozycję "Co to jest data"?
2. Wprowadź "dzisiaj". 
    - Powiadomienie już dziś jest oznaczone i pojawia się w drugim wierszu, ponieważ jest ono wstępnie utworzone jednostki edytowalne i nieedytowalne.
5. Kliknij wynik akcji
    - Należy zauważyć, że teraz Data pojawia się w sekcji jednostki pamięci. 
    - Jeśli przesuniesz wskaźnik myszy nad daty, zobaczą dodatkowe dane, które są udostępniane przez usługi LUIS, które są wykorzystywane i dalsze można manipulować w kodzie. 
6. Wybierz pozycję "przypada $luis-datetimev2".
7. Kliknij przycisk Gotowe, nauczania

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Alternatywne danych wejściowych](./8-alternative-inputs.md)
