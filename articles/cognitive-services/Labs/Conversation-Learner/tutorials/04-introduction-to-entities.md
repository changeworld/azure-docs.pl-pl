---
title: Jak używać jednostek z modelem Conversation Learner — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak używać jednostek z modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: cba12b6c09c1bdbf4e8f7841676a609c34109d93
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707338"
---
# <a name="introduction-to-entities"></a>Wprowadzenie do jednostek

W tym samouczku przedstawiono jednostki, niekwalifikujące się jednostki, wymagane jednostki i ich użycie w ramach Conversation Learner.

## <a name="video"></a>Połączenia wideo

[![Wprowadzenie do wersji zapoznawczej samouczka](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>Wymagania

Ten samouczek wymaga, aby ogólny samouczek bot był uruchomiony

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Jednostki przechwytują informacje, które bot potrzebują do wykonania swojego zadania, poprzez wyodrębnienie z wyrażenia długości użytkownika lub przypisanie przez kod niestandardowy. Jednostki mogą również ograniczyć dostępność akcji poprzez jawne zaklasyfikowanie ich jako "wymagane" lub "niekwalifikowanie".

- Wymagane jednostki muszą być obecne w pamięci modelu, aby akcja była dostępna
- Obiekty niekwalifikujące *nie* mogą być obecne w pamięci modelu, aby akcja była dostępna

Ten samouczek koncentruje się na jednostkach niestandardowych. Wstępnie nauczone, wielowartościowe jednostki i jednostki programistyczne są wprowadzane do innych samouczków.

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W interfejsie użytkownika sieci Web kliknij pozycję "nowy model".
2. W polu "nazwa" wpisz "IntroToEntities" i naciśnij klawisz ENTER.
3. Kliknij przycisk "Utwórz".

### <a name="entity-creation"></a>Tworzenie jednostki

1. Na panelu po lewej stronie kliknij pozycję "jednostki", a następnie przycisk "Nowa jednostka".
2. Wybierz pozycję "niestandardowa przeszkolony" dla "typu jednostki".
3. Wpisz "miasto" dla "Nazwa jednostki".
4. Kliknij przycisk "Utwórz".

> [!NOTE]
> Typ jednostki "niestandardowy przeszkolony" oznacza, że ta jednostka może być przeszkolony, w przeciwieństwie do innych typów jednostek.

### <a name="create-the-actions"></a>Tworzenie akcji

1. Na panelu po lewej stronie kliknij pozycję akcje, a następnie przycisk "nowa akcja".
2. W odpowiedzi "bot"... " Wpisz "nie wiem, czego szukasz".
3. W polu "niekwalifikujące się jednostki" wpisz "miasto".
4. Kliknij przycisk "Utwórz".

> [!NOTE]
> Dodanie jednostki "miasto" do "niekwalifikujące się jednostki" spowoduje odkwalifikowanie tej akcji od bot, gdy jednostka "miasto" jest zdefiniowana w pamięci bot.

Teraz Utwórz drugą akcję.

1. Na panelu po lewej stronie kliknij pozycję akcje, a następnie przycisk "nowa akcja".
2. W odpowiedzi "bot"... " , wpisz "Pogoda w $city prawdopodobnie jest Sunny".
3. Kliknij przycisk "Utwórz".

> [!NOTE]
> Jednostka "miasto" została dodana automatycznie na liście wymagane jednostki przez odwołanie w odpowiedzi.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>Uczenie modelu

1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie przycisk "okno dialogowe nowego uczenia".
2. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Hello".
    - To symuluje po stronie rozmowy.
3. Kliknij przycisk "akcje oceny".
4. Wybierz odpowiedź "nie wiem, czego szukasz".
5. W miarę jak użytkownik reaguje na "Seattle".
6. Kliknij przycisk "akcje oceny".
7. Wybierz odpowiedź, "Pogoda w $city prawdopodobnie jest Sunny".
8. Kliknij przycisk "Zapisz".

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Oczekiwano jednostki](./05-expected-entity.md)
