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
ms.openlocfilehash: cdd9ad16096c85db21829840b2bfd7acaced5942
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683510"
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
    - Opcje programowalny i Negatable są wyłączone, ponieważ nie mają zastosowania do wstępnie utworzone jednostki.
3. Kliknij pozycję Utwórz.

![](../media/tutorial7_entities_a.PNG)

### <a name="create-two-actions"></a>Utwórz dwie akcje

1. Kliknij pozycję operacje, a następnie nową akcję.
1. W odpowiedzi, wpisz "przypada $builtin-datetimev2".
1. W wymaganych jednostek, wpisz "$builtin-datetimev2".
1. Kliknij pozycję Utwórz.

![](../media/tutorial7_actions_a.PNG)

Następnie należy utworzyć drugą akcję:

1. Kliknij pozycję operacje, a następnie nową akcję do utworzenia drugiej akcji.
1. W odpowiedzi wpisz "Co to jest data?".
1. W jednostkach dyskwalifikacji, wprowadź "$builtin-datetimev2".
1. Kliknij pozycję Utwórz.

![](../media/tutorial7_actions2_a.PNG)

Masz teraz dwie akcje.

### <a name="train-the-bot"></a>Uczenie bota

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
2. Wpisz "hello".
3. Kliknij wynik akcji, a następnie wybierz pozycję "Co to jest data"?
2. Wprowadź "dzisiaj". 
    - Powiadomienie już dziś jest oznaczone i pojawia się w drugim wierszu, ponieważ jest ono wstępnie utworzone jednostki edytowalne i nieedytowalne.
5. Kliknij wynik akcji.
    - Należy zauważyć, że teraz Data pojawia się w sekcji jednostki pamięci. 
    - Jeśli przesuniesz wskaźnik myszy nad daty, zobaczą dodatkowe dane, które są udostępniane przez usługi LUIS, które są wykorzystywane i dalsze można manipulować w kodzie. 
6. Wybierz pozycję "przypada $builtin-datetimev2".
7. Kliknij przycisk Gotowe, nauczania.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Alternatywne danych wejściowych](./8-alternative-inputs.md)
