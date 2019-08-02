---
title: Jak używać akcji oczekiwania i nieoczekiwania z modelem Conversation Learner — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak używać akcji oczekiwania i nieczekania z modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 5d9a67f0b413b624baec369a219547cf18e9dbd2
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705598"
---
# <a name="wait-and-non-wait-actions"></a>Akcje oczekiwania i nieoczekiwanie

W tym samouczku przedstawiono różnicę między akcjami oczekiwania i akcjami braku oczekiwania w Conversation Learner.

## <a name="video"></a>Połączenia wideo

[![Zaczekaj na podgląd samouczka bez oczekiwania](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby ogólny samouczek bot był uruchomiony

    npm run tutorial-general

## <a name="details"></a>Szczegóły

- Akcja oczekiwania: Gdy system przyjmuje akcję "Czekaj", spowoduje to zatrzymanie akcji i oczekiwanie na dane wejściowe użytkownika.
- Akcja bez oczekiwania: Gdy system przyjmuje akcję "bez oczekiwania", natychmiast wybierze inną akcję (bez czekania na dane wejściowe użytkownika).

## <a name="steps"></a>Kroki

### <a name="create-a-new-model"></a>Utwórz nowy model

1. W interfejsie użytkownika sieci Web kliknij pozycję Nowy model.
2. W polu "nazwa" wpisz "Czekaj, nie czekaj", naciśnij klawisz ENTER lub kliknij przycisk "Utwórz".

### <a name="create-the-first-two-wait-actions"></a>Utwórz pierwsze dwie akcje oczekiwania

1. Na panelu po lewej stronie kliknij pozycję akcje, a następnie przycisk "nowa akcja".
2. W odpowiedzi "bot"... " Wpisz "jakie Pizza chcesz?".
    - Jest to akcja oczekiwania, dlatego pozostaw zaznaczone pole "Czekaj na odpowiedź".
3. Kliknij przycisk "Utwórz".
4. Powtórz te kroki, Utwórz kolejną akcję z "Pizza w sposób". jako odpowiedź bot.

### <a name="train-using-those-wait-actions"></a>Uczenie przy użyciu tych akcji oczekiwania

1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie przycisk "okno dialogowe nowego uczenia".
2. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Witaj". 
    - To symuluje po stronie rozmowy.
3. Kliknij przycisk "akcje oceny".
4. Wybierz odpowiedź "jakie Pizza chcesz?".
5. W miarę jak użytkownik reaguje na "Margherita".
6. Kliknij przycisk "akcje oceny".
7. Wybierz odpowiedź "Pizza w drodze!".
8. Kliknij przycisk "Zapisz".

### <a name="create-a-non-wait-action-while-training"></a>Tworzenie akcji, która nie jest oczekiwana podczas szkolenia
Mimo że można utworzyć akcję nieoczekiwaną tak jak wcześniej, można ją również utworzyć z poziomu sesji szkoleniowej.
1. Kliknij przycisk "okno dialogowe nowego uczenia".
2. Jako typ użytkownika, "Hello".
3. Kliknij przycisk "akcje oceny".
4. Kliknij przycisk "+ Akcja". 
    - Spowoduje to przejście do znanego okna dialogowego "Tworzenie akcji".
5. Wpisz odpowiedź bot jako "Witamy w Pizza bot!"
6. Usuń zaznaczenie pola wyboru "Czekaj na odpowiedź".
7. Kliknij przycisk "Utwórz".
    - Zwróć uwagę, że bot natychmiast reaguje na "Witamy w Pizza bot!" i ponownie zostanie wyświetlony monit o podanie innej odpowiedzi bot. Wynika to z faktu, że odpowiedź bot była akcją bez oczekiwania, która została właśnie utworzona.
9. Wybierz odpowiedź "jakie Pizza chcesz?".
10. W miarę jak użytkownik reaguje na "Margherita".
11. Kliknij przycisk "akcje oceny".
12. Wybierz odpowiedź "Pizza w drodze!".
13. Kliknij przycisk "Zapisz".

> [!NOTE]
> Sekwencja odpowiedzi bot w odniesieniu do akcji oczekiwania i braku oczekiwania.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do jednostek](./04-introduction-to-entities.md)
