---
title: Jak używać właściwości "oczekiwana jednostka" akcji Conversation Learner — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak używać właściwości "oczekiwana jednostka" modelu Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 5fbe4f09d377b9f157368184ab26341782e9aed1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707324"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Jak używać właściwości "oczekiwana jednostka" akcji

W tym samouczku przedstawiono Właściwość "oczekiwana jednostka" akcji.

## <a name="video"></a>Połączenia wideo

[![Oczekiwany Podgląd samouczka jednostki](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby ogólny samouczek bot był uruchomiony

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Użyj właściwości "oczekiwana jednostka" akcji, aby zapisać odpowiedź użytkownika do tej akcji w jednostce.

Podczas dodawania jednostek do właściwości "oczekiwana jednostka" akcji system będzie:

1. Zacznij od próby dopasowania jednostek przy użyciu modelu wyodrębniania jednostek na podstawie uczenia maszynowego
2. Przypisz całą wypowiedź użytkowników do $entity opartej na algorytmach heurystycznych, jeśli nie zostanie znaleziona żadna jednostka
3. Wywołaj `EntityDetectionCallback`i wybierz akcję do wyboru.

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W interfejsie użytkownika sieci Web kliknij pozycję "nowy model".
2. W polu "nazwa" wpisz "ExpectedEntities" i naciśnij klawisz ENTER.
3. Kliknij przycisk "Utwórz".

### <a name="entity-creation"></a>Tworzenie jednostki

1. Na panelu po lewej stronie kliknij pozycję "jednostki", a następnie przycisk "Nowa jednostka".
2. Wybierz pozycję "niestandardowa przeszkolony" dla "typu jednostki".
3. Wpisz "name" (nazwa jednostki).
4. Kliknij przycisk "Utwórz".

> [!NOTE]
> Typ jednostki "niestandardowy przeszkolony" oznacza, że ta jednostka może być przeszkolony, w przeciwieństwie do innych typów jednostek.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>Utwórz pierwszą akcję

1. Na panelu po lewej stronie kliknij pozycję akcje, a następnie przycisk "nowa akcja".
2. W odpowiedzi "bot"... " Wpisz "imię i nazwisko"?
3. W polu "oczekiwane jednostki" wpisz "name" (nazwa).
4. Kliknij przycisk "Utwórz".

> [!NOTE]
> Jednostki wykryte i wyodrębnione z odpowiedzi użytkownika zostaną zapisane w jednostce "nazwa", jeśli ta akcja zostanie wybrana. Jeśli żadna jednostka nie zostanie wykryta, cała odpowiedź zostanie zapisana w tej jednostce.

### <a name="create-the-second-action"></a>Utwórz drugą akcję

1. Na panelu po lewej stronie kliknij pozycję akcje, a następnie przycisk "nowa akcja".
2. W odpowiedzi "bot"... " Wpisz "Witaj $name!"
3. Kliknij przycisk "Utwórz".

> [!NOTE]
> Jednostka "name" została automatycznie dodana jako "wymagane jednostki" przez odwołanie w odpowiedzi.

Teraz masz dwie akcje.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>Uczenie modelu

1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie przycisk "okno dialogowe nowego uczenia".
2. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Witaj".
    - To symuluje po stronie rozmowy.
3. Kliknij przycisk "akcje oceny".
4. Wybierz odpowiedź, "Jaka jest Twoja nazwa?"
    - "Witaj $name!" nie można wybrać odpowiedzi, ponieważ ta odpowiedź wymaga, aby jednostka "name" została zdefiniowana teraz w pamięci modelu.
5. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Piotr".
    - "Piotr" jest wyróżniony jako jednostka na podstawie algorytmu heurystycznego, który został wcześniej skonfigurowany do zapisania odpowiedzi jako jednostki.
6. Kliknij przycisk "akcje oceny".
    - Jednostka "name" została teraz zdefiniowana jako "Piotr" w pamięci modelu, więc akcja "Hello $name" jest wybierana jako akcja.
7. Wybierz odpowiedź "Witaj $name!"
8. Kliknij przycisk "Zapisz".

Dodanie alternatywnych danych wejściowych w dalszej postaci pociąga za model.

1. W polu "Dodaj alternatywne dane wejściowe..." Wpisz "jestem Jose".
    - Model nie rozpoznaje nazwy jako jednostki, dlatego wybiera cały blok tekstu jako wartość jednostki
2. Kliknij frazę "jestem Jose", a następnie kliknij ikonę kosza.
3. Kliknij "Jose", a następnie kliknij "nazwa" z listy jednostek.
4. Kliknij pozycję wyniki akcje.
5. Wybierz odpowiedź "Witaj Piotr!"
6. Kliknij przycisk "Zapisz".

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jednostki negacji](./06-negatable-entities.md)
