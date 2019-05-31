---
title: Pokaz uczeń konwersacji model, zamówienie pizza — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak utworzyć model uczeń konwersacji pokaz.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 010245480d8e1f59d5c1b92a9e717f73b5ba7f4c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389137"
---
# <a name="demo-pizza-order"></a>Pokaz: Kolejność pizza
Ten pokaz ilustruje głosi, porządkowanie bot, obsługa pizza jednej ustalania kolejności przez:

- rozpoznawanie toppings pizza z wypowiedzi użytkowników
- Zarządzanie spisem toppings i prawidłową odpowiedź mechanizmom
- zapamiętywać poprzednie zamówienia i skracając zmianę kolejności identyczne pizza

## <a name="video"></a>Połączenia wideo

[![Pokaz Pizza w wersji zapoznawczej](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa bot kolejności pizza

    npm run demo-pizza

### <a name="open-the-demo"></a>Otwórz wersję demonstracyjną

Na liście modelu interfejsu użytkownika sieci web kliknij TutorialDemo Pizza zamówienia. 

## <a name="entities"></a>Jednostki

Model zawiera trzy jednostki:

- "Toppings" gromadzi toppings określonego użytkownika, jeśli jest dostępny.
- "OutofStock" powiadamia użytkownika, że wybrany nadmiarów za mało zasobów
- "LastToppings" zawiera historycznych toppings z ich wcześniejsze zamówienie

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Akcje

Model zawiera zestaw działań, które prosi użytkownika o ich zaznaczenia nadmiarów, skumulowany toppings i wiele innych.

Dostępne są również dwa wywołania interfejsu API:

- "FinalizeOrder" obsługuje realizacja zamówień
- "UseLastToppings" przetwarza informacje historyczne toppings

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Szkolenie w oknach dialogowych

Kilka okien dialogowych szkolenia znajdują się w modelu.

![](../media/tutorial_pizza_dialogs.PNG)

Spróbujmy trenowania nieco więcej, tworząc szkolenie okno dialogowe.

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Order pizza z ser"
    - Słowo "ser" został wyodrębniony przez ekstraktor jednostki.
3. Kliknij przycisk "Wynik akcje".
4. Wybierz odpowiedź "Masz ser na Twoje pizza."
5. Wybierz odpowiedź, "Czy chcesz jeszcze jakieś?"
6. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Dodaj grzyby i ogrodnictwu"
7. Kliknij przycisk "Wynik akcje".
8. Wybierz odpowiedź "Masz ser, grzyby i ogrodnictwu na Twoje pizza."
9. Wybierz odpowiedź, "Czy chcesz jeszcze jakieś?"
10. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Usuń ogrodnictwu i Dodaj produkcji kiełbas"
11. Kliknij przycisk "Wynik akcje".
12. Wybierz odpowiedź "Masz ser, grzyby i produkcji kiełbas na Twoje pizza."
13. Wybierz odpowiedź, "Czy chcesz jeszcze jakieś?"
14. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Dodaj jam"
15. Kliknij przycisk "Wynik akcje".
    - Wartość "jam" dodano "OutofStock" jednostki kodu wywołania zwrotnego wykrywania, jak tekst jest niezgodna z dowolnym obsługiwanych składników.
16. Wybierz odpowiedź "OutOfStock"
17. Wybierz odpowiedź, "Czy chcesz jeszcze jakieś?"
18. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "no"
    - "no" nie jest oznaczony jako dowolny typ intencji. Przeciwnie wybierzemy odpowiednią akcję, na podstawie bieżącego kontekstu.
19. Kliknij przycisk "Wynik akcje".
20. Wybierz odpowiedź "FinalizeOrder"
    - Wybranie tej akcji w wyniku toppings bieżącego klienta, wprowadzenie zapisane w jednostce "LastToppings", a następnie usunięcie jednostki "Toppings" przez kod FinalizeOrder wywołania zwrotnego.
21. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "order innego"
22. Kliknij przycisk "Wynik akcje".
23. Wybierz odpowiedź, "Czy chcesz ser, grzyby i produkcji kiełbas?"
    - Ta akcja jest teraz dostępna, ze względu na jednostkę "LastToppings" zostanie ustawiony.
24. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "tak"
25. Kliknij przycisk "Wynik akcje".
26. Wybierz odpowiedź "UseLastToppings"
27. Wybierz odpowiedź "Masz ser, grzyby i produkcji kiełbas na Twoje pizza."
28. Wybierz odpowiedź, "Czy chcesz jeszcze jakieś?"

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wdrażanie bot uczeń konwersacji](../deploy-to-bf.md)
