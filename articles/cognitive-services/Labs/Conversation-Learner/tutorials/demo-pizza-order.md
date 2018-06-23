---
title: Pokaz uczeń konwersacji aplikacji, zamówienie pizza — kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak utworzyć demonstrację aplikacji uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 99cd89c4f4430f2d65ed0963e3092d51a83842d7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348700"
---
# <a name="demo-pizza-order"></a>Pokaz: Kolejność Pizza
Ten pokaz przedstawiono pizza bot porządkowania. Obsługuje ona kolejność pojedynczego pizza dzięki tej funkcji:

- rozpoznawanie toppings pizza w zniesławiających użytkownika
- sprawdzania, czy pizza toppings znajdują się w magazynie lub poza giełdowych i odpowiednio odpowiada
- Zapamiętywanie toppings pizza z poprzedniej kolejności i oferty — Uruchom nową kolejność z tej samej toppings

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa bot kolejności pizza

    npm run demo-pizza

### <a name="open-the-demo"></a>Otwórz pokaz

Na liście aplikacji w interfejsie użytkownika sieci web kliknij TutorialDemo Pizza kolejności. 

## <a name="entities"></a>Jednostki

Utworzono trzy jednostki.

- Toppings: będą gromadzone toppings, który zostanie wyświetlony monit o podanie. Zawiera on nieprawidłowy toppings, które znajdują się w magazynie. Sprawdza, czy nadmiarów jest do lub z zasobów.
- OutofStock: to jest używany do komunikacji użytkownikowi, że wybranych nadmiarów nie znajduje się w magazynie.
- LastToppings: po zamówienie tej jednostki służy do zaoferowania użytkownikowi na liście toppings na ich kolejność.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Akcje

Utworzono zestaw akcji, w tym prosząc użytkowników mają ich pizza informacją o tym, co one dodane do tej pory itp.

Dostępne są również dwa wywołań interfejsu API:

- FinalizeOrder: można umieścić kolejność pizza
- UseLastToppings: migracji toppings z poprzedniej kolejności 

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Szkolenie w oknach dialogowych
Zdefiniowaniu kilka okien dialogowych szkolenia. 

![](../media/tutorial_pizza_dialogs.PNG)

Na przykład spróbujmy sesji nauczania.

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
1. Wprowadź "order pizza".
2. Kliknij przycisk wynik akcji.
3. Kliknij przycisk do wybierz opcję "co chcesz umieścić na Twoje pizza?"
4. Wprowadź "grzyby i ser".
    - Należy zauważyć, że zarówno jako Toppings etykietą LUIS. Jeśli jest niepoprawny, można kliknij, aby zaznaczyć, a następnie go poprawić.
    - Znak "+" obok jednostki oznacza, że jest dodawany do zestawu toppings.
5. Kliknij przycisk wynik akcji.
    - Grzyby powiadomienia i ser nie są w pamięci dla Toppings.
3. Kliknij, aby wybrać "masz $Toppings Twojego pizza"
    - Należy zauważyć, że tak bot poprosi o następnej akcji jest akcją bez oczekiwania.
6. Wybierz opcję "Ma inny?"
7. Wprowadź "Usuń grzyby i dodać papryki".
    - Powiadomienie **grzyba** ma '-' znakiem obok niej go do usunięcia. I ma papryki '+', aby dodać go do toppings.
2. Kliknij przycisk wynik akcji.
    - Powiadomienie **papryki** jest teraz w pogrubienia w postaci, w jakiej jest nowa. I **grzyby** przekroczony został.
8. Kliknij, aby wybrać "masz $Toppings Twojego pizza"
6. Wybierz opcję "Ma inny?"
7. Wprowadź "Dodaj groch".
    - Groch są przykładem nadmiarów, która jest spoza zasobów. Należy pamiętać, że nadal jest oznaczony jako nadmiarów.
2. Kliknij przycisk wynik akcji.
    - Groch wyświetlane jako OutOfStock.
    - Aby zobaczyć, jak to się stało, teraz otworzyć kodu w C:\<\installedpath > \src\demos\demoPizzaOrder.ts. I zanotuj metodę EntityDetectionCallback. Ta metoda jest wywoływana po wykonaniu każdego nadmiarów, jeśli znajduje się w magazynie. Jeśli nie, usuwa ją z zestawu toppings i dodaje do jednostki OutOfStock. Zmienna inStock jest zdefiniowany powyżej tej metody, która ma listę toppings — jest zasobów.
6. Wybierz opcję "Nie mamy $OutOfStock".
7. Wybierz opcję "Ma inny?"
8. Wprowadź "no".
9. Kliknij przycisk wynik akcji.
10. Wybierz wywołanie "FinalizeOrder" interfejsu API. 
    - To spowoduje wywołanie funkcji "FinalizeOrder" zdefiniowane w kodzie. Czyści toppings i zwraca "Twoje zamówienie jest w drodze". 
2. Wprowadź "order innego". Zostanie użyty nową kolejność.
9. Kliknij przycisk wynik akcji.
    - Uwaga ser i papryki znajdują się w pamięci jako toppings od ostatniego zamówienia.
1. Wybierz opcję "Chcesz $LastToppings".
2. Wprowadź wartość "tak"
3. Kliknij przycisk wynik akcji.
    - Bot chce reakcję UseLastToppings. To drugi dwie metody wywołania zwrotnego. Spowoduje skopiowanie ostatniego zamówienia toppings do toppings i wyczyść toppings ostatni. Jest to sposób zapamiętywanie ostatnio zlecenia, a jeśli użytkownik odpowie, że chcą pizza innego, zapewniając tych toppings jako opcje.
2. Kliknij, aby wybrać "masz $Toppings Twojego pizza".
3. Wybierz opcję "Ma inny?"
8. Wprowadź "no".
4. Kliknij przycisk Done nauczania.

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wersja demonstracyjna — uruchamianie aplikacji VR](./demo-vr-app-launcher.md)
