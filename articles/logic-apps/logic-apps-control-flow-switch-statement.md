---
title: Dodawanie instrukcji switch przepływów pracy — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Jak utworzyć instrukcji switch, które kontrolują akcji przepływu pracy na podstawie określonej wartości w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 2a3f8ee5cba3110d392555fad78c1cb2513b5d4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60683176"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Tworzenie instrukcji switch, systemem akcji przepływu pracy na podstawie określonej wartości w usłudze Azure Logic Apps

Aby uruchomić konkretne akcje na podstawie wartości obiektów, wyrażenia lub tokenów, należy dodać *Przełącz* instrukcji. Ta struktura ocenia obiektów, wyrażenie lub tokenu, wybierze case, który odpowiada wynik i uruchamia określone działania tylko dla tego przypadku. Po uruchomieniu instrukcji switch tylko jeden przypadek powinna być zgodna z wynikiem.

Na przykład załóżmy, że chcesz aplikację logiki, która wykonuje różne czynności na podstawie opcji wybrane w wiadomości e-mail. W tym przykładzie aplikacja logiki sprawdza, czy witryny sieci Web źródła danych RSS dla nowej zawartości. Gdy w kanale informacyjnym RSS pojawi się nowy element, aplikacja logiki wysyła wiadomość e-mail do osoby zatwierdzającej. Oparte na tego, czy osoba zatwierdzająca wybiera "Zatwierdź" lub "Odrzuć", aplikacja logiki wykonać różne czynności.

> [!TIP]
> Podobnie jak wszystkie języki programowania instrukcji switch obsługują tylko operatory porównania. Jeśli potrzebujesz innych operatorów relacyjnych, takich jak "większe niż", użyj [instrukcji warunkowej](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> Aby zapewnić zachowanie wykonywania deterministyczna, przypadków musi zawierać wartość unikatowa i statyczne zamiast dynamicznej tokeny lub wyrażenia.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Na przykład w tym artykule, wykonaj [tworzenie Ta przykładowa aplikacja logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) przy użyciu konta Outlook.com lub Office 365 Outlook.

  1. Po dodaniu akcji do wysyłania wiadomości e-mail, należy znaleźć i zamiast tego wybierz następującą akcję: **Wyślij wiadomość e-mail z zatwierdzeniem**

     ![Wybierz pozycję "Wyślij wiadomość e-mail z zatwierdzeniem"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Podaj wymagane pola, takie jak adres e-mail osoby, która pobiera wiadomości e-mail dotyczącej zatwierdzenia. 
  W obszarze **opcje użytkownika**, wprowadź "Zatwierdź, Odrzuć".

     ![Wprowadź szczegóły wiadomości e-mail](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Dodawanie instrukcji switch

1. W tym przykładzie Dodaj instrukcję switch na końcu przykładowego przepływu pracy. Po wykonaniu ostatniego kroku wybierz **nowy krok**.

   Jeśli chcesz dodać instrukcji switch między krokami, przesuń wskaźnik nad strzałką znajdującą się gdzie chcesz dodać instrukcji switch. Wybierz **znak plus** (**+**) wyświetlany, wybierz **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "Przejdź" jako filtr. Wybierz tę akcję: **Przełącznik — formant**

   ![Dodaj przełącznik](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Instrukcja switch pojawia się z jednym przypadkiem i przypadek domyślny. 
   Domyślnie instrukcji switch wymaga co najmniej jeden przypadek, a także przypadek domyślny. 

   ![Domyślnym pustym instrukcji switch](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Kliknij wewnątrz **na** pola tak, aby wyświetlić listę zawartości dynamicznej. Z tej listy, wybierz **SelectedOption** pole, którego dane wyjściowe Określa akcję do wykonania. 

   ![Select "SelectedOption"](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Sposób obsługiwać przypadki, w których osoba zatwierdzająca wybiera `Approve` lub `Reject`, Dodaj inny przypadek między **przypadek** i **domyślne**. 

   ![Dodaj inny przypadek](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Dodaj te akcje do odpowiednich przypadkach:

   | # Wielkości liter | **SelectedOption** | Akcja |
   |--------|--------------------|--------|
   | Przypadek 1 | **Zatwierdź** | Dodawanie programu Outlook **Wyślij wiadomość e-mail** akcji wysyłania szczegóły dotyczące elementu kanału RSS, tylko wtedy, gdy osoba zatwierdzająca zaznaczyła opcję **Zatwierdź**. |
   | Przypadek 2 | **Odrzuć** | Dodawanie programu Outlook **Wyślij wiadomość e-mail** akcji do powiadamiania innych osób zatwierdzających o odrzuceniu elementu kanału RSS. |
   | Domyślne | Brak | Trzeba podejmować żadnych działań. W tym przykładzie **domyślne** przypadek jest pusty ponieważ **SelectedOption** ma tylko dwie opcje. |
   |||

   ![Instrukcja switch zakończone](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Zapisz aplikację logiki. 

   Aby ręcznie przetestować w tym przykładzie, wybierz **Uruchom** aż aplikacja logiki znajdzie nowy element RSS i wysyła wiadomość e-mail z zatwierdzeniem. 
   Wybierz **Zatwierdź** aby obserwować wyniki.

## <a name="json-definition"></a>Definicji JSON

Teraz, gdy utworzono aplikację logiki, za pomocą instrukcji switch, Przyjrzyjmy się definicję kodu wysokiego poziomu za instrukcji switch.

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
| `"Switch"`         | Nazwa instrukcji switch, które możesz zmienić nazwę, aby zwiększyć czytelność |
| `"type": "Switch"` | Określa, że akcja instrukcji switch |
| `"expression"`     | W tym przykładzie określa osoby zatwierdzającej wybranej opcji, które jest obliczane przed każdym przypadku, ponieważ nie zadeklarowano w dalszej części definicji |
| `"cases"` | Definiuje dowolnej liczby przypadków. W przypadku każdego `"Case_*"` jest nazwą domyślną, w tym przypadku możesz zmienić nazwę, aby zwiększyć czytelność |
| `"case"` | Określa wartość przypadek, który musi być unikatowy i stałych wartość, która korzysta z instrukcji switch do porównania. Jeśli żadne przypadki zgodne wyniku wyrażenia switch, akcje `"default"` sekcji są uruchamiane. | 
| | | 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać lub głosować na funkcje lub sugestie, odwiedź [witrynie opinii użytkowników usługi Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Wykonaj kroki na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Uruchom i wykonaj ponownie kroki (pętli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchom lub scalania równoległymi krokami (gałęzie)](../logic-apps/logic-apps-control-flow-branches.md)
* [Wykonaj kroki na podstawie stanu akcji grupowanych (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
