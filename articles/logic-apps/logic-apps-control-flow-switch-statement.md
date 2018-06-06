---
title: Dodaj instrukcje przełącznika do przepływów pracy - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Jak utworzyć instrukcji switch, kontrolujących takie akcje przepływu pracy na podstawie wartości określonych w aplikacjach logiki platformy Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: cfowler
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 2ffac49d14e05ff252d6cd0e90fc23d77ac0caff
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726078"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Tworzenie przełącznika instrukcji, które wykonanie akcji przepływu pracy na podstawie wartości określonych w aplikacjach logiki platformy Azure

Aby uruchomić określone czynności na podstawie wartości obiektów, wyrażenia lub tokenów, należy dodać *przełącznika* instrukcji. Ta struktura ocenia obiektu, wyrażenie lub token, wybierze przypadek, który odpowiada wynik i uruchamia określone czynności tylko dla tego przypadku. Po uruchomieniu instrukcji switch tylko jeden przypadek powinien być zgodny z wynikiem.

Na przykład załóżmy, że chcesz aplikację logiki, która ma inne czynności na podstawie opcji wybranej w wiadomości e-mail. W tym przykładzie aplikacji logiki sprawdza witryny sieci Web źródło danych RSS dla nowej zawartości. Po wyświetleniu nowego elementu w źródła danych RSS, aplikację logiki wysyła wiadomości e-mail do osoby zatwierdzającej. W oparciu o czy osoba zatwierdzająca wybiera "Zatwierdź" lub "Odrzuć", aplikacji logiki wykonać różne czynności.

> [!TIP]
> Podobnie jak wszystkie języki programowania instrukcji switch obsługuje tylko operatory równości. Jeśli potrzebujesz innych operatory relacyjne, takie jak "większe niż", użyj [instrukcji warunkowej](#conditions).
> W celu zapewnienia sposób wykonywania deterministyczna, przypadków musi zawierać wartość unikatowa i statyczne zamiast tokeny dynamicznych lub wyrażenia.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Na przykład w tym artykule, wykonaj [tworzenie aplikacji logiki tej próbki](../logic-apps/quickstart-create-first-logic-app-workflow.md) przy użyciu konta usługi Outlook.com lub Office 365 Outlook.

  1. Po dodaniu działania do wysyłania wiadomości e-mail, wybierz **wysłanie wiadomości e-mail zatwierdzania** zamiast tego.

     ![Zaznacz pole wyboru "Wyślij wiadomości e-mail zatwierdzania"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  2. Podaj wymagane pola, takie jak adres e-mail osoby, która pobiera wiadomości e-mail zatwierdzania. 
  W obszarze **opcji użytkownika**, wprowadź "Zatwierdź, Odrzuć".

     ![Wprowadź szczegóły poczty e-mail](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-a-switch-statement"></a>Dodawanie instrukcji switch

1. Na końcu przykładowego przepływu pracy, wybierz **+ nowy krok** > **... Więcej** > **dodawanie przypadku przełącznika**. 

   ![Dodawanie instrukcji switch](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Instrukcja switch zostanie wyświetlony jeden przypadek oraz przypadek domyślny. 
   Instrukcja switch wymaga co najmniej jeden przypadek plus na przypadek domyślny. 

   Jeśli chcesz dodać instrukcji switch między krokami wskaźnika na strzałkę której chcesz dodać instrukcji switch. 
   Wybierz **znak plus** (**+**) wyświetlony, a następnie wybierz **dodawanie przypadku przełącznika**.

4. W **na** wybierz opcję **SelectedOption** pola, której wyjście Określa akcję do wykonania. 
   
   Można wybrać wartość z **dodać zawartość dynamiczną** listy.

5. Aby obsługiwać przypadki, w których osoba zatwierdzająca wybiera `Approve` lub `Reject`, dodać innego przypadku między **przypadku** i **domyślne**. 
   
6. Dodaj te akcje w odpowiednich przypadkach:

   | Wielkość # | **SelectedOption** | Akcja |
   |:------ |:-------------------|:------ |
   | Przypadek 1 | **Zatwierdzanie** | Dodaj programu Outlook **wysłać wiadomość e-mail** akcji wysyłania szczegóły elementu RSS, tylko w przypadku wybrania osoby zatwierdzającej **Zatwierdź**. |
   | Przypadek 2 | **Odrzuć** | Dodaj programu Outlook **wysłać wiadomość e-mail** akcji powiadamiania innych osób zatwierdzających odrzucono elementu RSS. |
   | Domyślne | \<Brak\> | Nie trzeba nic robić. W tym przykładzie **domyślne** przypadek jest pusty ponieważ **SelectedOption** ma tylko dwie opcje. |
   |         |          |

   ![Switch — instrukcja](./media/logic-apps-control-flow-switch-statement/switch.png)

7. Zapisz aplikację logiki. 

   Aby ręcznie przetestować w tym przykładzie, wybierz **Uruchom** aż do aplikacji logiki znajdzie nowy element funkcji RSS i wysyła wiadomości e-mail zatwierdzania. 
   Wybierz **Zatwierdź** obserwować wyniki.

## <a name="json-definition"></a>Definicja formatu JSON

Teraz, zostało utworzone przy użyciu instrukcji switch aplikacji logiki, Przyjrzyjmy się definicję wysokiego poziomu kodu za instrukcji switch.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case" : {
         "actions" : {
           "Send_an_email": { }
         },
         "case" : "Approve"
      },
      "Case_2" : {
         "actions" : {
           "Send_an_email_2": { }
         },
         "case" : "Reject"
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

| Etykieta              | Opis |
| :----------------- | :---------- |
| `"Switch"`         | Nazwa instrukcji switch, które można zmienić nazwy dla czytelności |
| `"type": "Switch"` | Określa, że akcja instrukcji switch |
| `"expression"`     | W tym przykładzie określa osoby zatwierdzającej wybranej opcji, które jest obliczane przed każdym przypadku zgodnie z deklaracją później w definicji |
| `"cases"` | Definiuje dowolnej liczby przypadków. W przypadku każdego `"Case_*"` jest domyślną nazwę dla tego przypadku można zmienić nazwy dla czytelności |
| `"case"` | Określa wartość przypadek, który musi być unikatowa i stałych wartość, która używa instrukcji switch do porównania. Jeśli przypadkach nie pasuje do wyniku wyrażenia switch akcje w `"default"` sekcji są uruchamiane.
|           |         |

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać lub oddawać głosy na funkcje lub sugestie, odwiedź stronę [witrynę opinii użytkowników usługi Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Wykonanie kroków na podstawie warunku (warunkowe instrukcje)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Uruchom i powtórz kroki (pętle)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchom lub scalania czynności równoległe (gałęzi)](../logic-apps/logic-apps-control-flow-branches.md)
* [Wykonanie kroków na podstawie stanu akcji grupowanych (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
