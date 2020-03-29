---
title: Tworzenie oddziałów równoległych lub dołączanie do nich dla akcji w przepływach pracy
description: Dowiedz się, jak tworzyć lub scalać równoległe gałęzie uruchamiania dla niezależnych akcji przepływu pracy w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: c0b1519992ba930382a1987aed185ef3c92eded4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453433"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Tworzenie oddziałów równoległych lub dołączanie do nich dla akcji przepływu pracy w usłudze Azure Logic Apps

Domyślnie akcje w przepływach pracy aplikacji logiki są uruchamiane sekwencyjnie. Aby wykonać niezależne akcje w tym samym czasie, można utworzyć [równoległe gałęzie](#parallel-branches), a następnie [połączyć te gałęzie](#join-branches) później w przepływie. 

> [!TIP] 
> Jeśli masz wyzwalacz, który odbiera tablicę i chcesz uruchomić przepływ pracy dla każdego elementu tablicy, możesz *dyskutować o* tej tablicy za pomocą [właściwości wyzwalacza **SplitOn** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Dodawanie gałęzi równoległej

Aby uruchomić niezależne kroki w tym samym czasie, można dodać równoległe gałęzie obok istniejącego kroku. 

![Uruchamianie kroków równolegle](media/logic-apps-control-flow-branches/parallel.png)

Aplikacja logiki czeka na zakończenie wszystkich gałęzi przed kontynuowaniem przepływu pracy. Gałęzie równoległe są `runAfter` uruchamiane tylko wtedy, gdy ich wartości właściwości są zgodne ze stanem gotowego kroku nadrzędnego. Na przykład `branchAction1` oba `branchAction2` i są ustawione `parentAction` do uruchamiania tylko wtedy, gdy kończy się ze stanem. `Succeeded`

> [!NOTE]
> Przed rozpoczęciem aplikacja logiki musi mieć już krok, w którym można dodać równoległe gałęzie.

1. W <a href="https://portal.azure.com" target="_blank">witrynie Azure portal</a>otwórz aplikację logiki w logice App Designer.

1. Przesuń wskaźnik myszy nad strzałką nad krokiem, w którym chcesz dodać równoległe gałęzie. Wybierz **plus** wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj gałąź równoległą**. 

   ![Dodawanie gałęzi równoległej](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. W polu wyszukiwania znajdź i wybierz odpowiednią akcję.

   ![Znajdowanie i wybieranie żądanej akcji](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   Wybrana akcja jest teraz wyświetlana w gałęzi równoległej, na przykład:

   ![Znajdowanie i wybieranie żądanej akcji](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Teraz w każdej gałęzi równoległej dodaj żądane kroki. Aby dodać kolejną akcję do gałęzi, przesuń wskaźnik pod akcją, w której chcesz dodać akcję sekwencyjną. Wybierz **plus** wyświetlony**+** znak plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

   ![Dodawanie akcji sekwencyjnej do gałęzi równoległej](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. W polu wyszukiwania znajdź i wybierz odpowiednią akcję.

   ![Znajdowanie i wybieranie akcji sekwencyjnej](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   Wybrana akcja jest teraz wyświetlana w bieżącej gałęzi, na przykład:

   ![Znajdowanie i wybieranie akcji sekwencyjnej](media/logic-apps-control-flow-branches/added-sequential-action.png)

Aby scalić gałęzie z powrotem, [dołącz do równoległych gałęzi](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Definicja gałęzi równoległej (JSON)

Jeśli pracujesz w widoku kodu, możesz zdefiniować strukturę równoległą w definicji JSON aplikacji logiki, na przykład:

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

## <a name="join-parallel-branches"></a>Łączenie równoległych gałęzi

Aby scalić równoległe gałęzie, wystarczy dodać krok u dołu pod wszystkimi gałęziami. Ten krok jest uruchamiany po zakończeniu wszystkich gałęzi równoległych.

![Łączenie równoległych gałęzi](media/logic-apps-control-flow-branches/join.png)

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i otwórz aplikację logiki w logice App Designer. 

1. W obszarze równoległych gałęzi, do których chcesz dołączyć, wybierz pozycję **Nowy krok**. 

   ![Dodaj krok do dołączenia](media/logic-apps-control-flow-branches/add-join-step.png)

1. W polu wyszukiwania znajdź i wybierz odpowiednią akcję jako krok, który łączy gałęzie.

   ![Znajdowanie i wybieranie akcji, która łączy równoległe gałęzie](media/logic-apps-control-flow-branches/join-steps.png)

   Gałęzie równoległe są teraz scalane.

   ![Dołączył do oddziałów](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Definicja sprzężenia (JSON)

Jeśli pracujesz w widoku kodu, możesz zdefiniować strukturę sprzężenia w definicji JSON aplikacji logiki, na przykład:

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
* Aby przesłać lub zagłosować na funkcje i sugestie, odwiedź [witrynę opinii użytkowników usługi Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie kroków na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Uruchamianie kroków na podstawie różnych wartości (instrukcje switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchamianie i powtarzanie kroków (pętli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchamianie kroków na podstawie stanu akcji zgrupowanych (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
