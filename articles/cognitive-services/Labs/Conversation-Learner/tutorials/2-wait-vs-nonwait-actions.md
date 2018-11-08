---
title: Jak używać oczekiwania i -wait akcji z modelem uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać oczekiwania i -wait akcji z modelem uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: b09279e61ad60abcbda8b5bf576f5145ea8b9602
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239257"
---
# <a name="wait-and-non-wait-actions"></a>Poczekaj chwilę i akcje-wait

W tym samouczku przedstawiono różnice między oczekiwania działania i działania-wait w uczeń konwersacji.

## <a name="video"></a>Połączenia wideo

[![Samouczek 2 (wersja zapoznawcza)](https://aka.ms/cl-tutorial-02-preview)](https://aka.ms/blis-tutorial-02)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły

- Poczekaj akcji: po jaki zajmuje akcji "wait", wówczas przestać pobierać elementy akcje i czeka na dane wejściowe użytkownika.
- Akcja non-wait: po jaki zajmuje akcji "bez oczekiwania", będzie ona natychmiast Wybierz kolejną akcję (bez oczekiwania na pierwsze dane wejściowe użytkownika).

## <a name="steps"></a>Kroki

### <a name="create-a-new-model"></a>Utwórz nowy model

1. W Interfejsie użytkownika sieci Web kliknij przycisk Nowy Model
2. W polu Nazwa wprowadź WaitNonWait. Następnie kliknij przycisk Utwórz.

### <a name="create-the-first-wait-action"></a>Utwórz pierwszą akcją oczekiwania

1. Kliknij pozycję operacje, a następnie nową akcję.
2. W odpowiedzi wprowadź "zwierząt, której chcesz użyć?".
    - Jest to akcja oczekiwania, więc pozostaw oczekiwanie na odpowiedź polem.
3. Kliknij pozycję Utwórz.

### <a name="create-a-non-wait-action"></a>Utwórz akcję Non-Wait

1. Kliknij przycisk Nowa akcja
2. W odpowiedzi wpisz "Krowy powiedzieć moo".
3. Zaznaczenie oczekiwania dla pola wyboru odpowiedzi.
4. Kliknięcie pozycji Utwórz

### <a name="create-a-second-non-wait-action"></a>Tworzenie drugiej akcji Non-Wait

1. Kliknij przycisk Nowa akcja
2. W odpowiedzi wpisz "Kaczkami powiedzieć quack".
3. Zaznaczenie oczekiwania dla pola wyboru odpowiedzi.
4. Kliknięcie pozycji Utwórz

![](../media/tutorial2_actions.PNG)

### <a name="train-the-bot"></a>Uczenie bota

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
2. Wpisz "hello"
3. Kliknij wynik akcji, a następnie wybierz pozycję "zwierząt, której chcesz użyć?".
4. Wprowadź "krów"
5. Kliknij wynik akcji, a następnie wybierz pozycję "Krowy powiedzieć moo".
    - Bot nie czeka na dane wejściowe i spowoduje przejście do następnej akcji.
2. Wybierz pozycję "zwierząt, której chcesz użyć?".
3. Wprowadź "kaczka"
5. Kliknij wynik akcji, a następnie wybierz pozycję "Kaczkami powiedzieć quack".

![](../media/tutorial2_dialogs.PNG)

> [!NOTE]
> Sekwencja odpowiedzi bot w odniesieniu do oczekiwania i akcje bez oczekiwania.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do jednostek](./3-introduction-to-entities.md)
