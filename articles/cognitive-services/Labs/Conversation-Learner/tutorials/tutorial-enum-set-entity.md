---
title: Kiedy używać jednostek WYLICZENIA i ustawiać akcje jednostek z modelem Conversation Learner — Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, kiedy należy używać jednostek WYLICZENIA i ustawiać akcje jednostek z modelem Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nolachar
ROBOTS: NOINDEX
ms.openlocfilehash: 5443b97febd6bf3831690531bceb540181e7676c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706974"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>Kiedy używać jednostek WYLICZAnia i ustawiania akcji jednostek

Ten samouczek wyjaśnia, kiedy należy używać jednostek WYLICZENIA (wyliczenia) i akcji SET_ENTITY.

## <a name="video"></a>Połączenia wideo

[![Samouczek ustawiania jednostki — wersja zapoznawcza](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>Co obejmuje

Ten samouczek wprowadzi dwie nowe funkcje. Nowy typ jednostki o nazwie ENUM (Short for Enumeration) i nowy typ akcji o nazwie SET_ENTITY, która może odwoływać się do jednej z tych wartości wyliczeniowych, a jako że nazwa wskazuje, ustawia jednostkę na tę wartość. Zgodnie z poniższymi informacjami te nowe funkcje są używane razem i wyjaśnimy, czym są i jak korzystać z nich. Zanim przejdziemy do szczegółów, ważne jest, aby zrozumieć, jaki problem może rozwiązać te funkcje.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Problem

Istnieją przypadki konwersacji, w których znaczenie wyrazów zależy od kontekstu.  Normalne słowa kluczowe z etykietą są interpretowane i wyodrębniane przy użyciu usługi interpretacji języka, ale w takich przypadkach te systemy mogą nie być w stanie uczyć się, używając przykładów z etykietami.

Wyobraź sobie, że przesłuchasz część konwersacji między osobami znajdującymi się w pobliżu i tylko wyraz "tak". Nie wiesz, co jest zgodne z wyrażeniem "yes" lub "potwierdzeniem", ponieważ nie zostało to potwierdzone przed tym pytaniem. Pytanie przed kontekstem, które daje znaczenie odpowiedzi. Podobnie od "tak" jest to typowa odpowiedź na wiele różnych pytań, ponieważ nie jest to możliwe, dostarczając przykłady tak jak w przypadku [niestandardowych](04-introduction-to-entities.md) , wyszkolonych jednostek, ponieważ następnie dowiesz się, jak oznaczyć każdą "tak" jako tę jednostkę.

### <a name="example"></a>Przykład

Wyjaśnimy teraz Poniższy przykład:

Bot Czy chcesz uzyskać Cognitive Services platformy Azure?
Użytkownik: Tak bot: Czy chcesz mieć lody?
Użytkownik: Yes

W poprzednich samouczkach zostały wyszukane niestandardowi [wyszkolone](04-introduction-to-entities.md) jednostki i Twoje początkowe zamyślinie może być przyczyną utworzenia jednostki o nazwie "likesCogServices" i etykiety pierwszej "yes" jako tej jednostki.  Jednak system również oznaczy drugi "tak". Gdy podjęto próbę skorygowania etykiety drugiego "tak" do "likesIceCream", utworzymy konflikt dwóch tych samych danych wejściowych "yes", co oznacza różne elementy i byłyby zablokowane.

W takich przypadkach należy używać jednostek WYLICZENIA i akcji SET_ENTITY.

## <a name="when-to-use-enums-or-setentity-actions"></a>Kiedy należy używać wyliczeń lub akcji SET_ENTITY

Użyj poniższych reguł, aby dowiedzieć się, kiedy należy używać jednostek WYLICZENIA i akcji SET_ENTITY:

- Wykrywanie lub ustawienie jednostki jest zależne od kontekstu
- Liczba możliwych wartości jest stała (tak i nie byłyby dwie wartości)

Innymi słowy, użyj ich w przypadku wszystkich monitów o bliskim zakończeniu, takich jak pytania potwierdzające, które zawsze powodują wartość tak lub nie.

> [!NOTE]
> Obecnie obowiązuje ograniczenie do 5 wartości na jednostkę enum. Każda wartość używa jednego z gniazd w bieżącym limicie 64. Zobacz [CL-wartości-i-granice](../cl-values-and-boundaries.md)

Przykład: Bot Czy zamówienie jest poprawne?
Użytkownik: Tak

Gdy możliwe wartości jednostki są otwarte — zakończono nienaprawione, należy użyć funkcji alternatywnej, takiej jak [oczekiwana jednostka](05-expected-entity.md).

Przykład: Bot Jak się nazywasz?
Użytkownik: Bot matowy: Jaki jest Twój ulubiony kolor?
Użytkownik: Srebrny

Te monity są uważane za otwarte, ponieważ mogą być odpowiedzi z dowolnymi wartościami.

## <a name="what"></a>Co

### <a name="enum-entities"></a>Jednostki WYLICZAjące

Jednostki WYLICZAne są tworzone podobnie jak inne jednostki. Podobnie jak w przypadku jednostek "programistycznych", nie można oznaczyć wyrazów jako tych jednostek. Zamiast tego muszą być ustawiane za poorednictwem kodu lub akcji SET_ENTITY.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Ustaw akcje jednostki

Jak wspomniano powyżej, Akcja "Ustaw jednostkę" po prostu ustawia jednostkę na znaną wartość enum. Można osiągnąć te same wyniki, tworząc akcję wywołania zwrotnego interfejsu API i używając Menedżera pamięci do ustawienia jednostki na wartość. Na przykład `memory.Set(entityName, entityValue)`. Konieczność zapisu tego kodu i tworzenia tych akcji staje się żmudnym i trudne do zarządzania — Conversation Learner ma specjalne akcje, które ułatwiają wykonywanie tej pracy i automatycznie generują te akcje, gdy są one używane. Posiadanie takich niezależnych akcji zachowuje możliwość redagowania tych danych bez łączenia z innymi akcjami lub kodem w bot.

- Akcje zestawu można tworzyć tylko w odniesieniu do wartości jednostki wyliczenia, dlatego należy najpierw utworzyć jednostkę enum.
- Akcje ustawiania jednostki są również "nieoczekiwane", ponieważ nie mają żadnych widocznych danych wyjściowych i muszą być spełnione przez akcję "Czekaj", którą użytkownik może zobaczyć.
- Akcje ustawiania jednostki są niezmienne, co oznacza, że nie można ich edytować po utworzeniu.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Automatyczne generowanie akcji

Jeśli w modelu istnieje jednostka wyliczenia, Conversation Learner utworzy akcje zastępcze dla każdej z możliwych wartości i udostępni je do wyboru podczas szkolenia. Po wybraniu akcja zostanie automatycznie utworzona.

Jeśli na przykład utworzysz jednostkę Enum o wartościach "yes" i "No":

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Nawet bez jawnego tworzenia akcji dla tego nowego wyliczenia zobaczysz dwie nowe akcje dostępne podczas szkolenia:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Tworzenie bot przy użyciu tych nowych funkcji

### <a name="requirements"></a>Wymagania

Ten samouczek wymaga, aby ogólny samouczek bot był uruchomiony

    npm run tutorial-general

Utworzymy Bot do symulowania szybkiej kolejności żywności. Będą mieć odrębne wartości dla rozmiarów napojów i Fries (małe/średnie/duże) i pytania potwierdzające z odpowiedziami tak/nie. Obie te jednostki spełniają te dwie reguły, które są zależne od kontekstu i ustalone wartości.

### <a name="create-the-model"></a>Tworzenie modelu

1. W interfejsie użytkownika sieci Web kliknij pozycję "Importuj".
2. Wybierz samouczek o nazwie "samouczek-enum-Set-Entity"

Spowoduje to przejście do strony zarządzania modelami.
Zwróć uwagę, że model zawiera już kilka jednostek wyliczenia i ustawia akcje jednostki.

### <a name="create-the-first-dialog"></a>Utwórz pierwsze okno dialogowe

1. W okienku nawigacji po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie kliknij przycisk "okno dialogowe nowego uczenia".
2. Gdy użytkownik wpisz w, "Witaj, chcę zamówić koks i Fries".
3. Kliknij przycisk "akcje oceny"

   > Użytkownik nie określił rozmiarów napoju lub Fries, więc musimy je zadawać.

4. Wybierz akcję z odpowiedzią: "Jakiego rozmiaru napój chcesz?".
5. Jako typ użytkownika, "duży"
6. Kliknij przycisk "akcje oceny"
7. Wybierz akcję SET_ENTITY-"drinkSize: ZNACZNIE
8. Wybierz akcję z odpowiedzią: "Jakiego rozmiaru Fries chcesz?"
9. Jako typ użycia w, "UM, uczyń je średnią.
10. Kliknij przycisk "akcje oceny"
11. Wybierz akcję SET_ENTITY-"friesSize: ŚREDNIOOKRESOW
12. Wybierz akcję z odpowiedzią: "Czy chcesz dowolnie dowolnych?"
13. Jako typ użycia w, "tak"
14. Kliknij przycisk "akcje oceny"
15. Wybierz akcję SET_ENTITY-"condimentsConfirmation: OPCJĘ
16. Wybierz akcję z odpowiedzią: "OK, mam zamówienie dla dużego napoju i ŚREDNIego friesu. Czy jest to poprawne? ".
17. Jako typ użycia w, "tak"
18. Kliknij przycisk "akcje oceny"
19. Wybierz akcję SET_ENTITY-"orderConfirmation: OPCJĘ
20. Wybierz akcję z odpowiedzią: "OK, numer zamówienia to 58. Zaczekaj na to wszystko ".
21. Kliknij przycisk "Zapisz", aby zamknąć okno dialogowe

Właśnie utworzono pierwsze okno dialogowe przy użyciu jednostek WYLICZENIA i akcji SET_ENTITY. Można utworzyć wiele większej liczby kombinacji użytkownika, określając częściowe informacje lub eksperymentując z innymi typami pytań zakończonych nieprzerwanie.

> [!NOTE]
> Podczas szkoleń zobaczysz symbole zastępcze dla akcji SET_ENTITY, takich jak
>
> ![action_set_entity_training. png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> Jednak podczas tworzenia okien dialogowych lub korzystania ze wdrożonych użytkowników botów nie będą one wyświetlane.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Alternatywne dane wejściowe](./10-alternative-inputs.md)
