---
title: Utwórz lub przyłącz gałęzi równoległych — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Jak utworzyć lub Dołącz do gałęzi równoległych dla przepływów pracy w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 2e1c155a371fa96e4f772f632a9585948b012e54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60685160"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Utwórz lub przyłącz gałęzi równoległej dla akcji przepływu pracy w usłudze Azure Logic Apps

Domyślnie swoje działania w przepływach pracy aplikacji logiki uruchamiają się po kolei. Aby wykonać akcje niezależne w tym samym czasie, można utworzyć [równoległych gałęziach](#parallel-branches), a następnie [Dołącz do tych gałęzi](#join-branches) później w Twój przepływ. 

> [!TIP] 
> Jeśli masz wyzwalacz, który odbiera tablicy i chcesz uruchomić przepływ pracy dla każdego elementu tablicy, możesz to zrobić *debatch* tablicy przy użyciu [ **SplitOn** wyzwalacza właściwości](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Dodaj gałąź równoległą

Aby uruchomić niezależnych kroków w tym samym czasie, możesz dodać gałęzi równoległych obok istniejącego kroku. 

![Wykonaj kroki w sposób równoległy](media/logic-apps-control-flow-branches/parallel.png)

Twoja aplikacja logiki czeka, aż wszystkie gałęzie zakończyć działanie przed kontynuowaniem przepływu pracy. Równoległe gałęzi Uruchom tylko wtedy, gdy ich `runAfter` wartości właściwości zgodny stan krok Zakończono nadrzędnej. Na przykład zarówno `branchAction1` i `branchAction2` są ustawione na uruchamianie tylko wtedy, gdy `parentAction` kończy się `Succeeded` stanu.

> [!NOTE]
> Przed rozpoczęciem, aplikacja logiki musi już mieć krok gdzie dodaniu gałęzi równoległych.

1. W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a>, Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Umieść wskaźnik nad strzałką znajdującą się powyżej kroku, w której chcesz dodać gałęzi równoległych. Wybierz **oraz** logowania (**+**) pojawia się, a następnie wybierz **Dodaj gałąź równoległą**. 

   ![Dodaj gałąź równoległą](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. W polu wyszukiwania Znajdź i wybierz akcję, którą chcesz.

   ![Znajdź i wybierz akcję, którą chcesz](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   Wybrana akcja teraz pojawia się w gałęzi równoległej, na przykład:

   ![Znajdź i wybierz akcję, którą chcesz](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Teraz w każdej gałęzi równoległych, należy dodać kroki, które chcesz. Aby dodać kolejną akcję do gałęzi, wskaźnik myszy w obszarze akcji gdzie chcesz dodać kolejne działania. Wybierz **oraz** (**+**) znak, który pojawia się, a następnie wybierz **Dodaj akcję**.

   ![Dodawanie kolejnych akcji do gałęzi równoległych](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. W polu wyszukiwania Znajdź i wybierz akcję, którą chcesz.

   ![Znajdź i zaznacz działania sekwencyjne](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   Wybrana akcja teraz pojawia się w bieżącej gałęzi, na przykład:

   ![Znajdź i zaznacz działania sekwencyjne](media/logic-apps-control-flow-branches/added-sequential-action.png)

Do scalenia gałęzi z powrotem ze sobą [Dołącz do gałęzi równoległych](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Definicja gałąź równoległą (JSON)

Jeśli pracujesz w widoku kodu, można zdefiniować strukturę równoległe w definicji JSON aplikacji logiki zamiast tego, na przykład:

``` json
{
  "triggers": {
    "myTrigger": {}
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Dołącz do gałęzi równoległych

Mają być scalone razem gałęzi równoległych, Dodaj krok u dołu w ramach wszystkich gałęzi. Ten krok jest wykonywany po wszystkich Zakończ gałęzi równoległych uruchomiona.

![Dołącz do gałęzi równoległych](media/logic-apps-control-flow-branches/join.png)

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki. 

1. W obszarze gałęzi równoległych, którą chcesz przyłączyć, wybierz opcję **nowy krok**. 

   ![Dodaj krok, aby dołączyć](media/logic-apps-control-flow-branches/add-join-step.png)

1. W polu wyszukiwania Znajdź i wybierz akcję, którą chcesz krokiem, który łączy gałęzi.

   ![Znajdź i wybierz akcję, która łączy gałęzi równoległych](media/logic-apps-control-flow-branches/join-steps.png)

   Teraz są scalane z gałęzi równoległych.

   ![Przyłączone do gałęzi](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Dołącz do definicji (JSON)

Jeśli pracujesz w widoku kodu, można zdefiniować strukturę sprzężenia w definicji JSON aplikacji logiki zamiast tego, na przykład:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać lub głosować na funkcje i sugestie, odwiedź [witrynie opinii użytkowników usługi Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Wykonaj kroki na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Wykonaj kroki na podstawie różnych wartości (instrukcji switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchom i wykonaj ponownie kroki (pętli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Wykonaj kroki na podstawie stanu akcji grupowanych (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
