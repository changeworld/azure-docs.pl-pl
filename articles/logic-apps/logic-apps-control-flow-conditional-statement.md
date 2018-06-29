---
title: Dodaj instrukcje warunkowe do przepływów pracy - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie warunków kontrolujących takie akcje w przepływach pracy w aplikacjach logiki platformy Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: d4e69d33e07f484b4ccc5343786865230368c7ca
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096380"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Utwórz warunkowe instrukcje sterujące przepływu pracy akcji w aplikacjach logiki platformy Azure

Aby uruchomić określone czynności w aplikacji logiki tylko po przekazywanie określony warunek, dodać *instrukcji warunkowej*. Ta struktura porównuje dane w przepływie pracy przed określone wartości lub pól. Następnie można określić różne akcje, które są uruchamiane na podstawie na czy danych spełnia warunek. Można zagnieżdżać warunków wewnątrz siebie nawzajem.

Na przykład załóżmy, że masz aplikację logiki, która wysyła zbyt dużo żądań wiadomości e-mail, gdy nowe elementy wyświetlane na kanału informacyjnego RSS witryny sieci Web. Możesz dodać instrukcji warunkowej do wysyłania wiadomości e-mail, tylko wtedy, gdy nowy element zawiera określony ciąg znaków. 

> [!TIP]
> Aby wykonać inne czynności na podstawie różnych określonej wartości, należy użyć [ *instrukcja switch* ](../logic-apps/logic-apps-control-flow-switch-statement.md) zamiast tego.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Na przykład w tym artykule, wykonaj [tworzenie aplikacji logiki tej próbki](../logic-apps/quickstart-create-first-logic-app-workflow.md) przy użyciu konta usługi Outlook.com lub Office 365 Outlook.

## <a name="add-a-condition"></a>Dodaj warunek

1. W <a href="https://portal.azure.com" target="_blank">portalu Azure</a>, Otwórz aplikację logiki w Projektancie aplikacji logiki.

2. Dodaj warunek w dowolnej lokalizacji. 

   Aby dodać warunek między krokami, wskaźnika na strzałkę której chcesz dodać warunek. Wybierz **znak plus** (**+**) wyświetlony, a następnie wybierz **Dodaj warunek**. Na przykład:

   ![Dodaj warunek między krokami](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

   Jeśli chcesz dodać warunek na końcu przepływu pracy, w dolnej części aplikacji logiki, wybierz **+ nowy krok** > **Dodaj warunek**.

3. W obszarze **warunku**, tworzenie warunku. 

   1. W polu po lewej stronie Określ dane lub pola, które chcesz porównać.

      Po kliknięciu wewnątrz po lewej stronie pola, pojawi się listy zawartości dynamicznej, można wybrać dane wyjściowe z poprzednich kroków w aplikacji logiki. 
      Na przykład wybierz Podsumowanie kanału informacyjnego RSS.

      ![Tworzenie warunku](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   2. W środkowym listy wybierz operacji do wykonania. 
   Na przykład wybierz pozycję "**zawiera**". 

   3. W prawym polu Określ wartość lub pola kryterium. 
   Na przykład określ następujący ciąg: **firmy Microsoft**

   Oto pełny warunek:

   ![Gotowy warunek](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

5. W obszarze **w przypadku wartości PRAWDA** i **w przypadku wartości FAŁSZ**, dodaj kroki do wykonania oparte na Określa, czy warunek jest spełniony. Na przykład:

   ![Warunek z "true" czy "w przypadku wartości FAŁSZ" ścieżki](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Możesz przeciągnąć istniejących działań do **w przypadku wartości PRAWDA** i **w przypadku wartości FAŁSZ** ścieżki.

6. Zapisz aplikację logiki.

Teraz tej aplikacji logiki tylko wysyła poczty, gdy nowe elementy do źródła danych RSS spełniają warunek.

## <a name="json-definition"></a>Definicja formatu JSON

Teraz, gdy utworzono aplikację logiki, za pomocą instrukcji warunkowej, Przyjrzyjmy się definicję wysokiego poziomu kodu za instrukcji warunkowej.

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
            "@triggerBody()?['summary']", "Microsoft"
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
* Aby przesłać lub oddawać głosy na funkcje i sugestie, odwiedź stronę [witrynę opinii użytkowników usługi Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Wykonanie kroków na podstawie różnych wartości (instrukcji switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchom i powtórz kroki (pętle)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchom lub scalania czynności równoległe (gałęzi)](../logic-apps/logic-apps-control-flow-branches.md)
* [Wykonanie kroków na podstawie stanu akcji grupowanych (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
