---
title: Kiedy należy używać Wyliczenia jednostek i USTAWIĆ jednostki akcji z modelem uczeń konwersacji - usług Azure Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, gdy należy używać Wyliczenia jednostek i akcje USTAWIĆ jednostki przy użyciu modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1393d0bd1c31a2c9c24652e260ef7f3182d91367
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476496"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>Kiedy należy używać Wyliczenia jednostek i USTAWIĆ jednostki akcji

W tym samouczku wyjaśniono, kiedy należy używać Wyliczenia (wyliczania) jednostek i SET_ENTITY akcji.

## <a name="video"></a>Połączenia wideo

[![Zestaw jednostek samouczka (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>Omawiane zagadnienia

W tym samouczku przedstawiono dwie nowe funkcje. Nowy typ jednostki nazywane Wyliczenia (skrót od wyliczania), a nowy typ akcji o nazwie SET_ENTITY, mogą odwoływać się do jednej z tych wartości wyliczenia, która ustawia jednostkę na tę wartość, jak wskazuje nazwa. Jak przedstawiono poniżej, te nowe funkcje są używane razem i wyjaśnimy, czym one są i jak ich używać poniżej. Przed przejściem do szczegółów, należy zrozumieć, jaki problem rozwiązuje te funkcje.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Problem

Istnieją przypadki, gdzie znaczenie słowa zależy od kontekstu konwersacji.  Zwykle etykietami słowa kluczowe są rozpoznawane i wyodrębnić przy użyciu usługi interpretacji języka, ale w takich przypadkach te systemy nie można się przy użyciu przykładów z etykietami.

Wyobraź sobie overhear część konwersacji między osobami w pobliżu i słyszysz tylko słowo "Yes". Czy wiesz, "Yes" zgodę na lub potwierdzenie, ponieważ nie słyszysz pytania zadawane przed nią. Zapytania przed jest kontekst, który zapewnia znaczenie dla odpowiedzi. Podobnie ponieważ "Yes" typowych odpowiedzi na wiele różnych pytania nie może być rozpoznawane przez podanie przykładów, tak jak przy użyciu [niestandardowe Uczonego](04-introduction-to-entities.md) jednostek ponieważ, a następnie będzie on Dowiedz się, jak etykiety co "Yes" jako tej jednostki.

### <a name="example"></a>Przykład

Umożliwia dalsze wyjaśnienia z poniższym przykładzie:

BOT: Czy podoba Ci usług Azure Cognitive Services?
Użytkownik: Tak, Bot: Czy podoba Ci ice cream?
Użytkownik: Tak

W poprzednich samouczkach przyjrzeliśmy się [niestandardowe Uczonego](04-introduction-to-entities.md) jednostek i myśli początkowej może być Utwórz jednostkę o nazwie "likesCogServices" i oznaczanie pierwszy "Yes" jako tej jednostki.  System będzie również etykieta drugi "Yes". Staraliśmy się poprawić etykiety drugiego "Yes" do "likesIceCream", firma Microsoft może następnie utworzyć konfliktu dwóch danych wejściowych w tym samym "Yes" znaczenie różnych rzeczy i będzie zablokowany.

Znajduje się w tych przypadkach, które muszą używać Wyliczenia jednostek i SET_ENTITY akcji.

## <a name="when-to-use-enums-or-setentity-actions"></a>Kiedy należy używać wyliczenia lub SET_ENTITY akcji

Użyj tych reguł poniżej, aby wiedzieć, kiedy należy używać Wyliczenia jednostek i akcje SET_ENTITY:

- Wykrywanie lub ustawienie jednostki są zależne od kontekstu
- Liczba możliwych wartości jest stała (tak i nie będzie mieć dwie wartości)

Innymi słowy ich używać do żadnych monitów zakończenia Zamknij, takich jak pytania potwierdzenia, które zawsze skutkuje Yes lub No.

> [!NOTE]
> Obecnie nie mamy ograniczenie wartości maksymalnie 5 na jednostkę wyliczenia. Każda wartość korzysta z jednego gniazda na bieżący limit 64. Zobacz [cl wartości i granice](../cl-values-and-boundaries.md)

Przykład: BOT: Twoje zamówienie jest poprawne?
Użytkownik: Yes

W przypadku możliwych wartości jednostki otwarty i nie zostało naprawione, należy użyć alternatywnej funkcji, takich jak [oczekiwaniami entity](05-expected-entity.md).

Przykład: BOT: Jak się nazywasz?
Użytkownik: Matt Bot: Co to jest Twoje ulubione koloru?
Użytkownik: Srebrny

Monity są uznawane za nieograniczony, ponieważ może udzielić odpowiedzi przy użyciu dowolnego wartości.

## <a name="what"></a>Elementy

### <a name="enum-entities"></a>Wyliczenie jednostki

Podobnie jak inne jednostki są tworzone jednostki Wyliczenia. Podobnie jak jednostki "programowy", możesz nie etykietą słowa te jednostki. Zamiast tego muszą zostać ustawione za pośrednictwem kodu lub SET_ENTITY akcji.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Ustaw akcje jednostki

Jak wspomniano powyżej, akcje "Ustaw jednostki" po prostu ustaw jednostki z wartością wyliczenia znane. Te same wyniki można osiągnąć, tworząc akcję wywołania zwrotnego interfejsu API i za pomocą Menedżera pamięci można ustawić jednostki z wartością. Na przykład `memory.Set(entityName, entityValue)`. Konieczności napisać ten kod i utworzyć te akcje stanie się żmudnym i trudny do zarządzania — więc uczeń konwersacji nie stosowanie specjalnych działań w celu ułatwienia tej pracy i automatyczne generowanie tych akcji, gdy są one używane. Posiadanie je jako niezależne akcje zachowuje możliwość tworzenia te nie są połączone z innych akcji lub kod w Twoim czatbocie.

- Akcje jednostki zestawu można tworzyć tylko przy odwoływaniu się do wartości jednostki typu wyliczeniowego, więc należy najpierw utworzyć jednostki typu wyliczeniowego.
- Akcje jednostki w zestawie są również "bez await", ponieważ mają żadne widoczne dane wyjściowe i powinny być kontynuowane w przez akcję "Czekaj", które mogą być widoczne dla użytkownika.
- Akcje jednostki w zestawie są niezmienne, co oznacza, że nie można ich edytować po utworzeniu.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Akcja automatycznego generowania

Jeśli jednostki wyliczenia istnieje w modelu, uczeń konwersacji utworzysz symbolu zastępczego akcje dla wszystkich możliwych wartości i były dostępne do wybrania podczas szkolenia. Po ich wybraniu akcja będzie automatycznie utworzona.

Na przykład, jeśli utworzę jednostki wyliczenia z wartości "Yes" i "No":

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Nawet bez jawnego tworzenia akcji w przypadku ten nowy typ wyliczeniowy zobaczysz dwie nowe akcje dostępne podczas szkolenia:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Utwórz robota przy użyciu tych nowych funkcji

### <a name="requirements"></a>Wymagania

Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

Utworzymy bot, aby zasymulować fast food porządkowanie. Będzie mieć osobne dla wartości rozmiarów w przeddzień i fries (MAŁA liczba godzin/średni/DUŻA), a potwierdzenia pytania z wartością tak / nie odpowiedzi. Oba te jednostki spełnia dwie reguły powyżej odpowiedzi zależne od kontekstu i wartości stałych.

### <a name="create-the-model"></a>Tworzenie modelu

1. W internetowym interfejsie użytkownika kliknij pozycję "Import"
2. Wybierz Samouczek o nazwie "Samouczek wyliczenia-Set-jednostki"

Spowoduje to przejście do strony zarządzania w modelu.
Zwróć uwagę, że model zawiera już kilka jednostek typu wyliczeniowego, a następnie ustaw akcje jednostki.

### <a name="create-the-first-dialog"></a>Pierwsze okno dialogowe tworzenia

1. W panelu nawigacyjnym po lewej stronie kliknij pozycję "Okien dialogowych Train", a następnie kliknij przycisk "Nowy Train Dialog".
2. Typ użytkownika w "Witaj, proszę o kolejność koksu i fries.".
3. Kliknij przycisk "Wynik akcji"

   > Użytkownik nie zostało określone rozmiary napój lub fries, dlatego musimy poprosić ich.

4. Wybierz akcję przy użyciu odpowiedzi: "Napój rozmiar, jaki chcesz użyć?"
5. Typ użytkownika w "large"
6. Kliknij przycisk "Wynik akcji"
7. Wybierz akcję SET_ENTITY — "drinkSize: DUŻE"
8. Wybierz akcję przy użyciu odpowiedzi: "Rozmiar fries czy?"
9. Jako typ użycia w "Um, przechowuj te nośnik.
10. Kliknij przycisk "Wynik akcji"
11. Wybierz akcję SET_ENTITY — "friesSize: ŚREDNI"
12. Wybierz akcję przy użyciu odpowiedzi: "Czy chcesz wszelkie condiments?"
13. Jako typ użycia w "Yes"
14. Kliknij przycisk "Wynik akcji"
15. Wybierz akcję SET_ENTITY — "condimentsConfirmation: WARTOŚĆ YES"
16. Wybierz akcję przy użyciu odpowiedzi: "Dobrze, masz zamówienie napój duże i średnie fries. Jest to poprawne?"
17. Jako typ użycia w "Yes"
18. Kliknij przycisk "Wynik akcji"
19. Wybierz akcję SET_ENTITY — "orderConfirmation: WARTOŚĆ YES"
20. Wybierz akcję przy użyciu odpowiedzi: "Dobrze, numeru zamówienia jest 58. Czekaj, ktoś."
21. Kliknij przycisk "Zapisz", aby zamknąć okno dialogowe

Pierwszy okna dialogowego za pomocą Wyliczenia jednostek i akcje SET_ENTITY właśnie utworzony. Można wprowadzić liczbę kombinacji więcej użytkownika, określania częściowe informacje oraz eksperymentowania przy użyciu innych typów zakończenia Zamknij pytania.

> [!NOTE]
> Podczas szkolenia zobaczysz symboli zastępczych dla akcji SET_ENTITY takich jak
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> jednak tworzenie po zalogowaniu się okien dialogowych lub przy użyciu wdrożone botów użytkownicy nie będą widzieć je.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Alternatywne danych wejściowych](./10-alternative-inputs.md)
