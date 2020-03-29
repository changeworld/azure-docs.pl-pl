---
title: Dodawanie instrukcji przełączania do przepływów pracy
description: Jak utworzyć instrukcje przełączania, które kontrolują akcje przepływu pracy na podstawie określonych wartości w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 5c40feec2dca65e4bc9617a71a6d0a8e4c872a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793238"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Tworzenie instrukcji przełączania uruchamianych przez akcje przepływu pracy na podstawie określonych wartości w usłudze Azure Logic Apps

Aby uruchomić określone akcje na podstawie wartości obiektów, wyrażeń lub tokenów, dodaj instrukcję *switch.* Ta struktura ocenia obiekt, wyrażenie lub token, wybiera przypadek, który pasuje do wyniku i uruchamia określone akcje tylko dla tego przypadku. Po uruchomieniu instrukcji switch tylko jeden przypadek powinien odpowiadać wynikowi.

Załóżmy na przykład, że chcesz aplikacji logiki, która wykonuje różne kroki na podstawie opcji wybranej w wiadomości e-mail. W tym przykładzie aplikacja logiki sprawdza kanał RSS witryny sieci Web pod kątem nowej zawartości. Gdy nowy element pojawi się w kanale RSS, aplikacja logiki wysyła wiadomość e-mail do osoby zatwierdzającej. Na podstawie tego, czy osoba zatwierdzająca wybiera "Zatwierdź" lub "Odrzuć", aplikacja logiki wykonuje różne kroki.

> [!TIP]
> Podobnie jak wszystkie języki programowania, instrukcje switch obsługują tylko operatory równości. Jeśli potrzebujesz innych operatorów relacyjnych, takich jak "większa niż", użyj [instrukcji warunkowej](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> Aby zapewnić deterministyczne zachowanie wykonywania, sprawy muszą zawierać unikatową i statyczną wartość zamiast tokenów dynamicznych lub wyrażeń.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aby wykonać przykład w tym artykule, [utwórz tę przykładową aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) z kontem Outlook.com lub programu Office 365 Outlook.

  1. Po dodaniu akcji do wysłania wiadomości e-mail znajdź i wybierz tę akcję: **Wyślij wiadomość e-mail z zatwierdzeniem**

     ![Wybierz opcję "Wyślij wiadomość e-mail z zatwierdzeniem"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Podaj wymagane pola, takie jak adres e-mail osoby, która otrzymuje wiadomość e-mail z zatwierdzeniem. 
  W obszarze **Opcje użytkownika**wpisz "Zatwierdź, odrzuć".

     ![Wprowadzanie szczegółów wiadomości e-mail](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Dodaj instrukcję switch

1. W tym przykładzie dodaj instrukcję switch na końcu przykładowego przepływu pracy. Po ostatnim kroku wybierz pozycję **Nowy krok**.

   Jeśli chcesz dodać instrukcję przełączania między krokami, przesuń wskaźnik myszy na strzałkę, w której chcesz dodać instrukcję switch. Wybierz **wyświetlony** znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wpisz "przełącz" jako filtr. Wybierz tę akcję: **Przełącznik - Sterowanie**

   ![Dodaj przełącznik](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Instrukcja switch pojawia się z jednym przypadkiem i przypadkiem domyślnym. 
   Domyślnie instrukcja switch wymaga co najmniej jednego przypadku plus przypadek domyślny. 

   ![Pusta domyślna instrukcja przełączania](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Kliknij wewnątrz pola **Włączone,** aby wyświetlić dynamiczną listę zawartości. Z tej listy wybierz pole **SelectedOption,** którego dane wyjściowe określa akcję do wykonania. 

   ![Wybierz "SelectedOption"](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Aby obsłużyć przypadki, `Approve` `Reject`w których osoba zatwierdzająca wybiera lub , dodaj inną sprawę między **sprawą** a **domyślną**. 

   ![Dodaj kolejną sprawę](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Dodaj te akcje do odpowiednich przypadków:

   | Przypadku # | **SelectedOption (SelectedOption)** | Akcja |
   |--------|--------------------|--------|
   | Przypadek 1 | **Zatwierdzenie** | Dodaj akcję **Wyślij wiadomość e-mail** w programie Outlook do wysyłania szczegółów dotyczących elementu RSS tylko wtedy, gdy osoba zatwierdzająca wybrano **opcję Zatwierdź**. |
   | Przypadek 2 | **Reject** | Dodaj akcję **Wyślij wiadomość e-mail** w programie Outlook w celu powiadomienia innych osób zatwierdzających, że element RSS został odrzucony. |
   | Domyślne | Brak | Nie trzeba podejmować żadnych działań. W tym przykładzie **domyślna** sprawa jest pusta, ponieważ **SelectedOption** ma tylko dwie opcje. |
   |||

   ![Instrukcja gotowego przełączania](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Zapisz aplikację logiki. 

   Aby ręcznie przetestować ten przykład, wybierz pozycję **Uruchom,** dopóki aplikacja logiki nie znajdzie nowego elementu RSS i nie wyśle wiadomości e-mail z zatwierdzeniem. 
   Wybierz **przycisk Zatwierdź,** aby obserwować wyniki.

## <a name="json-definition"></a>Definicja JSON

Teraz, gdy utworzono aplikację logiki przy użyciu instrukcji switch, przyjrzyjmy się definicji kodu wysokiego poziomu za switch instrukcji.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {}
         },
         "case" : "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| Label | Opis |
|-------|-------------|
| `"Switch"`         | Nazwa instrukcji switch, której można zmienić nazwę w celu uzyskania czytelności |
| `"type": "Switch"` | Określa, że akcja jest instrukcją switch |
| `"expression"`     | W tym przykładzie określa wybraną opcję osoby zatwierdzającej, która jest oceniana w każdym przypadku, zgodnie z definicją |
| `"cases"` | Definiuje dowolną liczbę przypadków. Dla każdego `"Case_*"` przypadku jest domyślną nazwą dla tej sprawy, której można zmienić nazwę w celu uzyskania czytelności |
| `"case"` | Określa wartość sprawy, która musi być stałą i unikatową wartością używaną przez instrukcję switch do porównania. Jeśli żadne przypadki nie odpowiadają wynikowi `"default"` wyrażenia przełącznika, akcje w sekcji są uruchamiane. | 
| | | 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać lub zagłosować na funkcje lub sugestie, odwiedź [witrynę opinii użytkowników usługi Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie kroków na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Uruchamianie i powtarzanie kroków (pętli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchamianie lub scalanie równoległych kroków (gałęzi)](../logic-apps/logic-apps-control-flow-branches.md)
* [Uruchamianie kroków na podstawie stanu akcji zgrupowanych (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
