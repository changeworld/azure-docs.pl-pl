---
title: Jak dodać Pre-Trained jednostki do modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak dodać Pre-trained jednostki do modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: afa927009e684fa7f8c6217c91dcb589b331b5f5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224177"
---
# <a name="how-to-add-pre-trained-entities"></a>Jak dodać Pre-trained jednostek
W tym samouczku przedstawiono sposób dodawania Pre-Trained jednostki do modelu uczeń konwersacji.

## <a name="video"></a>Połączenia wideo

[![Samouczek wstępnie przeszkolonych jednostki (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Wstępnie przeszkolonych jednostek rozpoznaje typowe rodzaje jednostek, takich jak liczb, dat, kwot pieniężnych i innych.  Działają one "out-of--box," nie wymagają żadnych szkoleń i nie można zmienić ich zachowania w odróżnieniu od jednostek niestandardowych.  Domyślnie Pre-Trained jednostki są wielowartościowy grupowania każdy zidentyfikowany wystąpienia jednostki.

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W Interfejsie użytkownika sieci Web kliknij przycisk "Nowy Model".
2. W polu "Nazwa" wpisz "PretrainedEntities" i naciśnij klawisz enter.
3. Kliknij przycisk "Utwórz".

### <a name="entity-creation"></a>Tworzenie jednostki

1. Na lewym panelu kliknij przycisk "Jednostki", a następnie przycisku "Nowa jednostka".
2. Wybierz pozycję "Wstępnej-Trained/datetimeV2" dla "Typu jednostek".
3. Sprawdź "Wielowartościowe" pole wyboru.
    - Wielowartościowy jednostki są gromadzone co najmniej jednej wartości w jednostce.
    - Właściwości można negować są wyłączone dla Pre-Trained jednostek.
4. Kliknij przycisk "Utwórz".

![](../media/tutorial7_entities_a.PNG)

### <a name="create-the-first-action"></a>Tworzenie pierwszej akcji

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi...", wpisz "przypada $builtin-datetimev2"
3. Kliknij przycisk "Utwórz".

![](../media/tutorial7_actions_a.PNG)

### <a name="create-the-second-action"></a>Tworzenie drugiej akcji

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi..." pola, wpisz "Co to jest data"?
    - Wstępnie przeszkolonych jednostki nie może być wymagane jednostki rozpoznawanym przez domyślne dla wszystkich wypowiedzi użytkowników.
3. W polu "Dyskwalifikacji uprawnia" typu "builtin datetimev2."
4. Kliknij przycisk "Utwórz".

![](../media/tutorial7_actions2_a.PNG)

### <a name="train-the-model"></a>Uczenie modelu

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "hello".
3. Kliknij przycisk "Wynik akcje".
4. Wybierz odpowiedź "Co to jest data"?
5. W panelu rozmowy, w której wyświetlany jest tekst "Type wiadomość...", wpisz "dzisiaj"
    - Dzisiaj wypowiedź jest automatycznie rozpoznawany przez wstępnie szkolone modele w usługi LUIS.
    - Kursor wartości jednostki Pre-Trained przedstawia dodatkowe dane udostępniane przez usługi LUIS.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Mechanizmy rozpoznawania elementów jednostki](./09-entity-resolvers.md)
