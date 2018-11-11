---
title: Pokaz uczeń konwersacji model, zamówienie pizza — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak utworzyć model uczeń konwersacji pokaz.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e23ff60a0a2ea10ace09130ba115e72b4e1c9ad7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249816"
---
# <a name="demo-pizza-order"></a>Pokaz: Kolejność Pizza
Ten pokaz ilustruje głosi, porządkowanie botów. Obsługuje ona kolejność pojedynczego pizza dzięki tej funkcji:

- rozpoznawanie toppings pizza w wypowiedzi użytkowników
- sprawdzania, czy pizza toppings znajdują się w magazynie lub na stanie, a następnie prawidłową odpowiedź mechanizmom
- Uzupełnij toppings pizza z wcześniejsze zamówienie i oferty — powinny zaczynać się nowe zamówienie toppings tego samego

## <a name="video"></a>Połączenia wideo

[![Pokaz Pizza w wersji zapoznawczej](https://aka.ms/cl-demo-pizza-preview)](https://aka.ms/blis-demo-pizza)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa bot kolejności pizza

    npm run demo-pizza

### <a name="open-the-demo"></a>Otwórz wersję demonstracyjną

Na liście modelu interfejsu użytkownika sieci web kliknij TutorialDemo Pizza zamówienia. 

## <a name="entities"></a>Jednostki

Utworzono trzech jednostek.

- Toppings: tę jednostkę będą gromadzone toppings, który użytkownik monitowany o. Zawiera ona prawidłowy toppings, które znajdują się w magazynie. Sprawdza, czy nadmiarów jest w lub na stanie.
- OutofStock: Ta jednostka służy do komunikowania się do użytkownika, że wybrany nadmiarów nie znajduje się w magazynie.
- LastToppings: gdy zamówienie ta jednostka służy do zaoferowania użytkownikowi listę toppings na ich kolejność.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Akcje

Utworzono zestaw akcji, w tym prosząc użytkownik chce na ich pizza informacją o tym, czym one dodane do tej pory, i tak dalej.

Istnieją dwa wywołania interfejsu API:

- FinalizeOrder: umieszcza kolejność pizza
- UseLastToppings: migracji toppings z wcześniejsze zamówienie 

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Szkolenie w oknach dialogowych
Zdefiniowano aspekty szkolenia w oknach dialogowych. 

![](../media/tutorial_pizza_dialogs.PNG)

Na przykład Wypróbujmy sesji nauczania.

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
1. Wprowadź "order pizza".
2. Kliknij wynik akcji.
3. Kliknij przycisk do wybierz opcję "co chcesz umieścić w swojej pizza?"
4. Wprowadź "grzyby oraz miejscowych serów".
    - Należy zauważyć, że zarówno jako Toppings etykietą usługi LUIS. Jeśli nie było poprawne, można kliknij, aby zaznaczyć, a następnie go poprawić.
    - Znak "+" obok jednostki oznacza, że jest dodawany do zestawu toppings.
5. Kliknij wynik akcji.
    - Zwróć uwagę `mushrooms` i `cheese` nie znajdują się w pamięci dla Toppings.
3. Kliknij, aby wybrać "masz $Toppings na Twoje pizza"
    - Zwróć uwagę, że jest to akcja-wait, aby bot poprosi o następnej akcji.
6. Wybierz pozycję "Czy chcesz jeszcze jakieś?"
7. Wprowadź "Usuń grzyby i Dodaj ogrodnictwu".
    - Zwróć uwagę `mushroom` ma "-" Logowanie obok niej, na jego usunięcia. I `peppers` zawiera znak "+" obok niej, aby dodać go do toppings.
2. Kliknij wynik akcji.
    - Zwróć uwagę `peppers` jest teraz w pogrubienie, ponieważ jest nowy. I `mushrooms` przekroczony został.
8. Kliknij, aby wybrać "masz $Toppings na Twoje pizza"
6. Wybierz pozycję "Czy chcesz jeszcze jakieś?"
7. Wprowadź "Dodaj groch".
    - `Peas` jest przykładem nadmiarów, czyli na stanie. Nadal jest ona oznaczona jako nadmiarów.
2. Kliknij wynik akcji.
    - `Peas` jest wyświetlany jako OutOfStock.
    - Aby zobaczyć, jak to się stało, otwórz kod w `C:\<\installedpath>\src\demos\demoPizzaOrder.ts`. Przyjrzyj się metoda EntityDetectionCallback. Ta metoda jest wywoływana po każdym nadmiarów, jeśli znajduje się w magazynie. Jeśli nie, usuwa ją z zestawu toppings i dodaje do jednostki OutOfStock. InStock zmienna jest zdefiniowana powyżej tej metody, która zawiera listę toppings — jest akcji.
6. Wybierz opcję "Nie mamy $OutOfStock".
7. Wybierz pozycję "Czy chcesz jeszcze jakieś?"
8. Wprowadź "no".
9. Kliknij wynik akcji.
10. Wybierz wywołanie "FinalizeOrder" interfejsu API. 
    - To spowoduje wywołanie funkcji "FinalizeOrder" zdefiniowana w kodzie. Czyści toppings i zwraca "zamówienia są w drodze". 
2. Wprowadź klauzula "order inny". Rozpoczynamy nową kolejność.
9. Kliknij wynik akcji.
    - "ser" i "ogrodnictwu" znajdują się w pamięci jako toppings od ostatniego zamówienia.
1. Wybierz pozycję "Czy chcesz $LastToppings".
2. Wprowadź wartość "tak"
3. Kliknij wynik akcji.
    - Bot chce wykonać akcję UseLastToppings. To drugi dwie metody wywołania zwrotnego. Go zostanie skopiowany toppings ostatniego zamówienia toppings i Wyczyść ostatnie toppings. Jest to sposób zapamiętywanie ostatniego zamówienia, a jeśli użytkownik mówi, że mają inną głosi, podając te toppings jako opcje.
2. Kliknij "masz $Toppings na Twoje pizza".
3. Wybierz pozycję "Czy chcesz jeszcze jakieś?"
8. Wprowadź "no".
4. Kliknij przycisk Gotowe, nauczania.

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Demonstracja — uruchamianie aplikacji VR](./demo-vr-app-launcher.md)
