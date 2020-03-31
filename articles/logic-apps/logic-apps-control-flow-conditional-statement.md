---
title: Dodawanie instrukcji warunkowych do przepływów pracy
description: Jak utworzyć warunki, które kontrolują akcje w przepływach pracy w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: fe79cf5af86e1f303e4735214b993d8db4488a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793256"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Tworzenie instrukcji warunkowych sterujących akcjami przepływu pracy w usłudze Azure Logic Apps

Aby uruchomić określone akcje w aplikacji logiki tylko po przejściu określonego warunku, dodaj *instrukcję warunkową*. Ta struktura kontroli porównuje dane w przepływie pracy z określonymi wartościami lub polami. Następnie można określić różne akcje, które są uruchamiane na podstawie tego, czy dane spełniają warunek. Warunki można zagnieżdżać wewnątrz siebie.

Załóżmy na przykład, że masz aplikację logiki, która wysyła zbyt wiele wiadomości e-mail, gdy nowe elementy pojawiają się w kanale RSS witryny. Instrukcję warunkową można dodać, aby wysyłać wiadomości e-mail tylko wtedy, gdy nowy element zawiera określony ciąg. 

> [!TIP]
> Aby uruchomić różne kroki na podstawie różnych określonych wartości, należy użyć [*instrukcji switch*](../logic-apps/logic-apps-control-flow-switch-statement.md) zamiast.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aby wykonać przykład w tym artykule, [utwórz tę przykładową aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) z kontem Outlook.com lub programu Office 365 Outlook.

## <a name="add-condition"></a>Dodawanie warunku

1. W <a href="https://portal.azure.com" target="_blank">witrynie Azure portal</a>otwórz aplikację logiki w logice App Designer.

1. Dodaj warunek w żądanej lokalizacji. 

   Aby dodać warunek między krokami, przesuń wskaźnik myszy na strzałkę, w której chcesz dodać warunek. Wybierz **wyświetlony** znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**. Przykład:

   ![Dodawanie akcji między krokami](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Jeśli chcesz dodać warunek na końcu przepływu pracy, u dołu aplikacji logiki wybierz pozycję **Nowy krok** > **Dodaj akcję**.

1. W polu wyszukiwania wpisz "condition" jako filtr. Wybierz tę akcję: **Warunek - Kontrola**

   ![Dodawanie warunku](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. W polu **Warunek** zbuduj swój stan. 

   1. W lewym polu określ dane lub pole, które chcesz porównać.

      Po kliknięciu wewnątrz lewego pola zostanie wyświetlona dynamiczna lista zawartości, dzięki czemu można wybrać dane wyjściowe z poprzednich kroków w aplikacji logiki. 
      W tym przykładzie wybierz podsumowanie kanału informacyjnego RSS.

      ![Zbuduj swój stan](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. W środkowym polu wybierz operację do wykonania. 
   W tym przykładzie wybierz "**zawiera**". 

   1. W prawym polu określ wartość lub pole jako kryteria. 
   W tym przykładzie określ ten ciąg: **Microsoft**

   Oto pełny warunek:

   ![Gotowy warunek](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Aby dodać kolejny wiersz do warunku, wybierz pozycję **Dodaj** > **wiersz**. 
   Aby dodać grupę z podwarszadami, wybierz pozycję **Dodaj** > **grupę Dodaj**. 
   Aby zgrupować istniejące wiersze, zaznacz pola wyboru dla tych wierszy, wybierz przycisk elipsy (...) dla dowolnego wiersza, a następnie wybierz pozycję **Grupuj**.

1. W obszarze **Jeśli prawda** i **Jeśli false**, dodaj kroki do wykonania na podstawie tego, czy warunek jest spełniony. Przykład:

   ![Warunek ze ścieżkami "Jeśli jest prawdziwa" i "Jeśli fałsz"](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Można przeciągnąć istniejące akcje do **If true** i **If false** paths.

1. Zapisz aplikację logiki.

Ta aplikacja logiki teraz wysyła pocztę tylko wtedy, gdy nowe elementy w kanale RSS spełniają twój warunek.

## <a name="json-definition"></a>Definicja JSON

Oto definicja kodu wysokiego poziomu za instrukcją warunkową:

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
* Aby przesłać lub zagłosować na funkcje i sugestie, odwiedź [witrynę opinii użytkowników usługi Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie kroków na podstawie różnych wartości (instrukcje switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchamianie i powtarzanie kroków (pętli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchamianie lub scalanie równoległych kroków (gałęzi)](../logic-apps/logic-apps-control-flow-branches.md)
* [Uruchamianie kroków na podstawie stanu akcji zgrupowanych (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
