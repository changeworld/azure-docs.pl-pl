---
title: Jak używać wywołania zwrotnego wykrywania jednostek z modelem Conversation Learner — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak używać wywołania zwrotnego wykrywania jednostek z modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3f6094da7b103f1c99e9bb3fa9313354fcb2a8bc
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703988"
---
# <a name="how-to-use-entity-detection-callback"></a>Jak używać wywołania zwrotnego wykrywania jednostek

W tym samouczku przedstawiono wywołanie zwrotne wykrywania jednostki i przedstawiono typowy wzorzec rozpoznawania jednostek.

## <a name="video"></a>Połączenia wideo

[![Samouczek wywołania zwrotnego dla wykrywania jednostek](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby `tutorialEntityDetectionCallback` bot był uruchomiony.

    npm run tutorial-entity-detection

## <a name="details"></a>Szczegóły
Wywołania zwrotne wykrywania jednostki umożliwiają modyfikację zachowania rozpoznawania jednostki i manipulowanie jednostką za pomocą kodu. Przedstawimy tę funkcję poprzez przechodzenie przez typowy Wzorzec projektowy wywołania zwrotnego wykrywania jednostek. Na przykład rozpoznawanie "Wielkiej firmy Apple" do "New York".

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W interfejsie użytkownika sieci Web kliknij pozycję "nowy model".
2. W polu "nazwa" wpisz "EntityDetectionCallback" i naciśnij klawisz ENTER.
3. Kliknij przycisk "Utwórz".

W tym przykładzie jest wymaganych trzy jednostki, więc Utwórz trzy.

### <a name="create-the-custom-trained-entity"></a>Utwórz niestandardową, wyszkolony obiekt

1. Na panelu po lewej stronie kliknij pozycję "jednostki", a następnie przycisk "Nowa jednostka".
2. Wybierz pozycję "niestandardowa przeszkolony" dla "typu jednostki".
3. Wpisz "miasto" dla "Nazwa jednostki".
4. Kliknij przycisk "Utwórz".

### <a name="create-the-first-programmatic-entity"></a>Tworzenie pierwszej jednostki programistycznej

1. Kliknij przycisk "Nowa jednostka".
2. Wybierz opcję "programistyczne" dla "typu jednostki".
3. Wpisz "CityUnknown" dla "Nazwa jednostki".
4. Kliknij przycisk "Utwórz".

### <a name="create-the-first-programmatic-entity"></a>Tworzenie pierwszej jednostki programistycznej

1. Kliknij przycisk "Nowa jednostka".
2. Wybierz opcję "programistyczne" dla "typu jednostki".
3. Wpisz "CityResolved" dla "Nazwa jednostki".
4. Kliknij przycisk "Utwórz".

Teraz Utwórz trzy akcje.

### <a name="action-creation"></a>Tworzenie akcji

1. Na panelu po lewej stronie kliknij pozycję akcje, a następnie przycisk "nowa akcja".
2. W odpowiedzi "bot"... " Wpisz "miasto, które chcesz?".
3. W "oczekiwana jednostka w odpowiedzi użytkownika..." Wpisz "miasto".
4. W polu "niekwalifikujące się jednostki" wpisz "miasto".
5. Kliknij przycisk "Utwórz".
6. Kliknij przycisk "nowa akcja".
7. W odpowiedzi "bot"... " Wpisz "miasto, które chcesz?".
8. W "oczekiwana jednostka w odpowiedzi użytkownika..." Wpisz "nie wiemy to miasto".
9. Kliknij przycisk "Utwórz".
10. Kliknij przycisk "nowa akcja".
11. W odpowiedzi "bot"... " Wpisz "$CityResolved jest bardzo świetna".
12. Kliknij przycisk "Utwórz".

Oto kod wywołania zwrotnego:

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>Uczenie modelu

1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie przycisk "okno dialogowe nowego uczenia".
2. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Witaj"
3. Kliknij przycisk "akcje oceny".
4. Wybierz odpowiedź, "której miejscowości chcesz użyć?".
5. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Big Apple"
6. Kliknij przycisk "akcje oceny".
    - Kliknięcie przycisku wyzwala wywołanie zwrotne wykrywania jednostki
    - Kod wywołania zwrotnego prawidłowo ustawia wartość jednostki CityResolved na "New York"
7. Wybierz odpowiedź "Nowy Jork jest bardzo świetny".

Ten wzorzec jest typowy w wielu scenariuszach bot. Wyrażenia długości użytkownika i wyodrębnione jednostki są dostarczane do logiki biznesowej, a logika przekształca wypowiedź w postać kanoniczną, która następnie jest zapisywana w jednostkach programistycznych w celu kolejnego wyłączania okna dialogowego.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wywołania zwrotne sesji](./13-session-callbacks.md)
