---
title: Demonstracja Conversation Learner model, zamówienie Pizza — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak utworzyć model Conversation Learner demonstracyjnej.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 4d42121468b8a875ecd13fb936d810728bd4b644
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703426"
---
# <a name="demo-pizza-order"></a>Pokaz: Pizza
W tej wersji demonstracyjnej przedstawiono Pizza porządkowanie bot, które obsługują pojedyncze porządkowanie Pizza według:

- Rozpoznawanie Pizza toppings z użytkownika wyrażenia długości
- Zarządzanie toppings spisem i odpowiadanie odpowiednio
- Zapamiętywanie poprzednich zamówień i przyspieszenie zmiany kolejności identycznych Pizza

## <a name="video"></a>Połączenia wideo

[![Demonstracja Pizza wersja zapoznawcza](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby program Pizza Order bot był uruchomiony

    npm run demo-pizza

### <a name="open-the-demo"></a>Otwórz demonstrację

Na liście model interfejsu użytkownika sieci Web kliknij pozycję TutorialDemo Pizza Order (zamówienie). 

## <a name="entities"></a>Jednostki

Model zawiera trzy jednostki:

- "Toppings" sumuje określony toppings użytkownika, jeśli jest dostępny.
- "OutofStock" sygnalizuje użytkownikowi, że wybrany topping jest poza zapasem
- "LastToppings" zawiera historyczne toppings z ich poprzedniej kolejności

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Akcje

Model zawiera zestaw akcji, które pyta użytkownika o wybór topping, skumulowany toppings i nie tylko.

Dostępne są również dwa wywołania interfejsu API:

- "FinalizeOrder" obsługuje realizację zamówienia
- "UseLastToppings" przetwarza historyczne informacje toppings

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Okna dialogowe szkoleń

W modelu znaleziono kilka okien dialogowych szkoleniowych.

![](../media/tutorial_pizza_dialogs.PNG)

Przekażmy model nieco więcej, tworząc kolejne okno dialogowe uczenia.

1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie przycisk "okno dialogowe nowego uczenia".
2. W panelu rozmowy, gdzie mówi "wpisz wiadomość...", wpisz "Order a Pizza with sera"
    - Wyraz "ser" został wyodrębniony przez Ekstraktor obiektu.
3. Kliknij przycisk "akcje oceny".
4. Wybierz odpowiedź "otrzymasz ser z Pizza".
5. Wybierz odpowiedź "czy chcesz coś innego?"
6. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Dodaj grzyby i paprykę"
7. Kliknij przycisk "akcje oceny".
8. Wybierz odpowiedź, "masz sery, grzyby i paprykę w Pizza".
9. Wybierz odpowiedź "czy chcesz coś innego?"
10. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Usuń paprykę i Dodaj kiełbasę"
11. Kliknij przycisk "akcje oceny".
12. Wybierz odpowiedź, "masz sery, grzyby i kiełbasy na swojej Pizza".
13. Wybierz odpowiedź "czy chcesz coś innego?"
14. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz polecenie "Add Jam"
15. Kliknij przycisk "akcje oceny".
    - Wartość "Jam" została dodana do elementu "OutofStock" przez kod wywołania zwrotnego wykrywania jednostki, ponieważ tekst nie jest zgodny z żadnym z obsługiwanych składników.
16. Wybierz odpowiedź "OutOfStock"
17. Wybierz odpowiedź "czy chcesz coś innego?"
18. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "No" (nie)
    - Wartość "No" nie jest oznaczona jako dowolny typ zamiaru. Zamiast tego wybieramy odpowiednią akcję na podstawie bieżącego kontekstu.
19. Kliknij przycisk "akcje oceny".
20. Wybierz odpowiedź "FinalizeOrder"
    - Wybranie tej akcji spowodowało, że bieżący toppings klienta jest zapisywany w jednostce "LastToppings" i usunięcie jednostki "toppings" przez FinalizeOrder kod wywołania zwrotnego.
21. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Order innego"
22. Kliknij przycisk "akcje oceny".
23. Wybierz odpowiedź, "czy chcesz mieć ser, grzyby i kiełbasy?".
    - Ta akcja jest teraz dostępna ze względu na ustawioną jednostkę "LastToppings".
24. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "yes" (tak)
25. Kliknij przycisk "akcje oceny".
26. Wybierz odpowiedź "UseLastToppings"
27. Wybierz odpowiedź, "masz sery, grzyby i kiełbasy na swojej Pizza".
28. Wybierz odpowiedź "czy chcesz coś innego?"

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wdrażanie Conversation Learner bot](../deploy-to-bf.md)
