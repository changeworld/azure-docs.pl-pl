---
title: Jak używać oczekiwania i -wait akcji z modelem uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać oczekiwania i -wait akcji z modelem uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2869e6c59388adf548e5f239d3a831a5a3f060dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707618"
---
# <a name="wait-and-non-wait-actions"></a>Poczekaj chwilę i akcje-wait

W tym samouczku przedstawiono różnice między oczekiwania działania i działania-wait w uczeń konwersacji.

## <a name="video"></a>Połączenia wideo

[![Oczekiwania a Non-Wait samouczek w wersji zapoznawczej](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły

- Oczekiwania akcji: Po jaki zajmuje akcji "wait", spowoduje to zatrzymanie podejmowanie działań i czeka na dane wejściowe użytkownika.
- Akcja non-wait: Po jaki zajmuje akcji "bez oczekiwania", jego natychmiast wybierze inną akcję (bez oczekiwania na dane wejściowe użytkownika).

## <a name="steps"></a>Kroki

### <a name="create-a-new-model"></a>Utwórz nowy model

1. W Interfejsie użytkownika sieci Web kliknij przycisk Nowy Model
2. W polu "Name" wpisz "oczekiwania Non-Wait", wprowadź trafień, lub kliknij przycisk "Utwórz".

### <a name="create-the-first-two-wait-actions"></a>Utwórz pierwsze dwa działania oczekiwania

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. Pole "Botów odpowiedzi...", wpisz "głosi, które chcesz użyć?".
    - Jest to akcja oczekiwania, więc pozostaw zaznaczone pole "Oczekiwania na odpowiedź".
3. Kliknij przycisk "Utwórz".
4. Powtórzenie tych kroków, Utwórz kolejną akcję za pomocą "Głosi sposobu!" jak Bot odpowiedzi.

### <a name="train-using-those-wait-actions"></a>Szkolenie przy użyciu tych operacji oczekiwania

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Hi". 
    - Symuluje to użytkownika po stronie konwersacji.
3. Kliknij przycisk "Wynik akcje".
4. Wybierz odpowiedź "głosi, jakiego potrzebujesz?".
5. Jako użytkownik odpowiada za pomocą "Margherita".
6. Kliknij przycisk "Wynik akcje".
7. Wybierz odpowiedź "Głosi sposobu!".
8. Kliknij przycisk "Zapisz".

### <a name="create-a-non-wait-action-while-training"></a>Utwórz akcję-Wait, podczas szkolenia
Chociaż można utworzyć akcji Non-Wait, tak jak miało to miejsce wcześniej, można również utworzyć go z sesji szkoleniowych.
1. Kliknij przycisk "Nowy Train Dialog".
2. Typ użytkownika w "Hello".
3. Kliknij przycisk "Wynik akcje".
4. Kliknij pozycję "+ akcji" przycisk. 
    - Spowoduje to przejście do znanego okna dialogowego "Tworzenie Action".
5. Wpisz w odpowiedzi Bot jako "Witaj Pizza Bot!"
6. Zaznaczenie pole wyboru "Oczekiwania na odpowiedź".
7. Kliknij przycisk "Utwórz".
    - Należy zauważyć, że odpowiada Bot bezpośrednio z "Witamy Pizza Bot!" i zostaną ponownie monitowany o podanie kolejną reakcją Botów. Jest to spowodowane odpowiedzi Bot akcji Non-Wait, którą właśnie utworzyliśmy.
9. Wybierz odpowiedź "głosi, jakiego potrzebujesz?".
10. Jako użytkownik odpowiada za pomocą "Margherita".
11. Kliknij przycisk "Wynik akcje".
12. Wybierz odpowiedź "Głosi sposobu!".
13. Kliknij przycisk "Zapisz".

> [!NOTE]
> Sekwencja odpowiedzi bot w odniesieniu do oczekiwania i akcje bez oczekiwania.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do jednostek](./04-introduction-to-entities.md)
