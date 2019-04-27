---
title: Dodawanie instrukcji warunkowych do przepływów pracy — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Jak utworzyć warunki kontrolujące akcje w przepływach pracy w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 9ee484971e217b0ca4dd7ad855e9e6dc3313e5d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684806"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Tworzenie instrukcji warunkowych, które kontrolują akcji przepływu pracy w usłudze Azure Logic Apps

Aby uruchomić konkretne działania w aplikacji logiki, dopiero po przekazywanie określony warunek, dodać *instrukcji warunkowej*. Ta struktura kontrolki porównuje dane w przepływie pracy dla określonych wartości lub pola. Następnie możesz określić różne akcje, które są uruchamiane na podstawie na informację, czy dane spełnia warunek. Można zagnieżdżać warunki wewnątrz siebie nawzajem.

Na przykład załóżmy, że masz aplikację logiki, która wysyła zbyt dużo żądań wiadomości e-mail, gdy pojawią się nowe elementy w źródle danych RSS witryny sieci Web. Możesz dodać instrukcję warunkową, aby wysłać wiadomość e-mail, tylko wtedy, gdy nowy element zawiera określony ciąg znaków. 

> [!TIP]
> Aby uruchomić innych czynności na podstawie różnych wartości określonego, użyj [ *switch, instrukcja* ](../logic-apps/logic-apps-control-flow-switch-statement.md) zamiast tego.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Na przykład w tym artykule, wykonaj [tworzenie Ta przykładowa aplikacja logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) przy użyciu konta Outlook.com lub Office 365 Outlook.

## <a name="add-condition"></a>Dodaj warunek

1. W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a>, Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Dodaj warunek w dowolnej lokalizacji. 

   Aby dodać warunek między krokami, przesuń wskaźnik nad strzałką znajdującą się, w której chcesz dodać warunek. Wybierz **znak plus** (**+**) wyświetlany, wybierz **Dodaj akcję**. Na przykład:

   ![Dodawanie akcji między krokami](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Jeśli chcesz dodać warunek, pod koniec przepływu pracy, w dolnej części swojej aplikacji logiki, wybierz **nowy krok** > **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "warunek" jako filtr. Wybierz tę akcję: **Warunek — formant**

   ![Dodaj warunek](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. W **warunek** polu, tworzenie warunku. 

   1. W polu po lewej stronie Określ dane lub pola, które chcesz porównać.

      Po kliknięciu wewnątrz pola po lewej stronie pojawi się lista zawartości dynamicznej, można wybrać dane wyjściowe z poprzednich kroków w aplikacji logiki. 
      W tym przykładzie wybierz Podsumowanie kanału informacyjnego RSS.

      ![Tworzenie warunku](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. W środkowym polu Wybierz operację do wykonania. 
   Na przykład wybierz pozycję "**zawiera**". 

   1. W polu prawego należy określić wartość lub pola jako kryteria. 
   W tym przykładzie określ następujący ciąg: **Microsoft**

   Oto pełny warunek:

   ![Gotowy warunek](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Aby dodać kolejny wiersz do warunku, wybierz **Dodaj** > **Dodaj wiersz**. 
   Aby dodać grupę z subconditions, wybierz opcję **Dodaj** > **Dodaj grupę**. 
   Aby zgrupować istniejące wiersze, zaznacz pola wyboru dla tych wierszy, wybierz przycisk wielokropka (...) dla każdego wiersza, a następnie wybierz **Ustaw jako grupę**.

1. W obszarze **w przypadku opcji true** i **w przypadku wartości FAŁSZ**, dodać czynności do wykonania oparte na tego, czy warunek jest spełniony. Na przykład:

   ![Warunek z "w przypadku opcji true" i ścieżki "w przypadku wartości false"](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Można przeciągnąć istniejące akcje do **w przypadku opcji true** i **w przypadku wartości FAŁSZ** ścieżki.

1. Zapisz aplikację logiki.

Ta aplikacja logiki wysyła wiadomość e-mail teraz, tylko wtedy, gdy nowe elementy w źródle danych RSS spełniają warunek.

## <a name="json-definition"></a>Definicji JSON

Poniżej przedstawiono definicję kodu wysokiego poziomu za instrukcji warunkowej:

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", 
            "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać lub głosować na funkcje i sugestie, odwiedź [witrynie opinii użytkowników usługi Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Wykonaj kroki na podstawie różnych wartości (instrukcji switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchom i wykonaj ponownie kroki (pętli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchom lub scalania równoległymi krokami (gałęzie)](../logic-apps/logic-apps-control-flow-branches.md)
* [Wykonaj kroki na podstawie stanu akcji grupowanych (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
