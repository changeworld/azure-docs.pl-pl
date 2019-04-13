---
title: Dodawanie pętli, które Powtarzanie operacji, lub przetwarzać tablice — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie pętli, które należy powtórzyć akcji przepływu pracy lub przetwarzać tablice w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
manager: jeconnoc
ms.date: 01/05/2019
ms.topic: article
ms.openlocfilehash: 339d4270dc1803879607663e9e2db4a86591ec76
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523005"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Tworzenie pętli, które należy powtórzyć akcji przepływu pracy lub przetwarzać tablice w usłudze Azure Logic Apps

W celu przetwarzania tablicy w aplikacji logiki, można utworzyć [pętlę "Foreach"](#foreach-loop). Ta pętla powtarza się co najmniej jedną akcję dla każdego elementu w tablicy. Ograniczenia dotyczące liczby elementów tablicy, które wykonuje pętlę "Foreach" mogą przetwarzać, w temacie [limity i Konfiguracja](../logic-apps/logic-apps-limits-and-config.md). 

Powtarzanie operacji, dopóki nie zostanie spełniony warunek lub stan zmieni się, można utworzyć ["pętlą Until"](#until-loop). Twoja aplikacja logiki najpierw uruchamia wszystkie akcje wewnątrz pętli, a następnie sprawdza warunek lub stanu. Jeśli warunek jest spełniony, pętla zatrzymuje się. W przeciwnym razie powtórzeniu pętli. Limity liczby "Do" pętli w aplikacji logiki, uruchamianie, zobacz [limity i Konfiguracja](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP]
> Jeśli masz wyzwalacz, który odbiera tablicy i chcesz uruchomić przepływ pracy dla każdego elementu tablicy, możesz to zrobić *debatch* tablicy przy użyciu [ **SplitOn** wyzwalacza właściwości](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Pętlę "Foreach"

Pętlę"Foreach" powtarza się co najmniej jedną akcję dla każdego elementu tablicy i działa tylko w przypadku tablic. Liczba iteracji w pętli "Foreach" Uruchom równolegle. Jednak można uruchamiać iteracji jednego naraz, konfigurując [sekwencyjne pętlę "Foreach"](#sequential-foreach-loop). 

Poniżej przedstawiono kilka kwestii, korzystając z pętli "Foreach":

* W zagnieżdżonej pętli iteracji zawsze uruchamiane sekwencyjnie, nie równolegle. Równolegle operacji dla elementów w zagnieżdżonej pętli, należy utworzyć i [wywołać aplikację logiki podrzędnych](../logic-apps/logic-apps-http-endpoint.md).

* Aby uzyskać przewidywalne wyniki operacji na zmienne podczas każdej iteracji pętli, Uruchom te pętli sekwencyjnie. Na przykład gdy uruchomionych współbieżnie pętli kończy się, inkrementacja, dekrementacja i Dołącz do zmiennej operacji zwrócić przewidywalne wyniki. Jednak podczas każdej iteracji w pętli jednocześnie uruchomione te operacje może zwrócić nieoczekiwane wyniki. 

* Akcje w pętli "Foreach" pętli użycia [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
wyrażenie odwołania do przetworzenia każdego elementu w tablicy. Jeśli określisz dane, które nie znajduje się w tablicy, przepływ pracy aplikacji logiki nie powiedzie się. 

Ta przykładowa aplikacja logiki wysyła codzienne podsumowanie kanału informacyjnego RSS witryny sieci Web. Aplikacja używa pętli "Foreach", która wysyła wiadomość e-mail dla każdego nowego elementu.

1. [Ta przykładowa aplikacja logiki tworzenie](../logic-apps/quickstart-create-first-logic-app-workflow.md) przy użyciu konta Outlook.com lub Office 365 Outlook.

2. Między RSS wyzwalania oraz akcję wysyłania wiadomości e-mail, Dodaj pętlę "Foreach". 

   1. Aby dodać pętlę między krokami, wskaźnik myszy nad strzałką znajdującą się między tymi krokami. 
   Wybierz **znak plus** (**+**), zostanie wyświetlone, następnie wybierz pozycję **Dodaj akcję**.

      ![Wybierz pozycję "Dodaj akcję"](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. W polu wyszukiwania wybierz pozycję **Wszystko**. W polu wyszukiwania wpisz "for each" jako filtr. Z listy akcji wybierz następującą akcję: **Dla każdego — formant**

      ![Dodaj pętlę "For each"](media/logic-apps-control-flow-loops/select-for-each.png)

3. Tworzenie pętli. W obszarze **wybierz dane wyjściowe z poprzednich kroków** po **Dodaj zawartość dynamiczną** zostanie wyświetlona lista, wybierz **linki źródła danych** tablicy, która jest wynikiem wyzwalacz kanału informacyjnego RSS. 

   ![Wybierz z listy zawartości dynamicznej](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Możesz wybrać *tylko* tablicy danych wyjściowych z poprzedniego kroku.

   Wybranej tablicy pojawi się w tym miejscu:

   ![Wybierz tablicy](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Aby uruchomić akcję dla każdego elementu tablicy, przeciągnij **Wyślij wiadomość e-mail** akcji do pętli. 

   Twoja aplikacja logiki może wyglądać następująco:

   ![Dodaj kroki do pętli "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Zapisz aplikację logiki. Aby ręcznie przetestować aplikację logiki, na pasku narzędzi Projektanta wybierz **Uruchom**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definicja pętlę "Foreach" (JSON)

Jeśli pracujesz w widoku kodu aplikacji logiki, można zdefiniować `Foreach` pętli w definicji JSON aplikacji logiki zamiast tego, na przykład:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>Pętlę "Foreach": Sekwencyjne

Domyślnie cykle w pętli "Foreach" Uruchom równolegle. Aby uruchomić każdy cykl po kolei, należy ustawić pętli **sekwencyjnego** opcji. Pętli "Foreach" musi działać po kolei po można zagnieżdżać pętli lub zmienne wewnątrz pętli, w których spodziewasz przewidywalne wyniki. 

1. W pętli w prawym górnym rogu wybierz **wielokropek** (**...** ) > **Ustawienia**.

   ![W pętli "Foreach" Wybierz "..." > "Ustawienia"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. W obszarze **kontroli współbieżności**, Włącz **kontroli współbieżności** ustawienie **na**. Przenieś **stopień równoległości** suwak, aby **1**i wybierz polecenie **gotowe**.

   ![Włącz mechanizm kontroli współbieżności](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Jeśli pracujesz z definicji JSON aplikacji logiki, możesz użyć `Sequential` opcji, dodając `operationOptions` parametru, na przykład:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Pętlą until"
  
Aby uruchomić i powtarzanie operacji, dopóki nie zostanie spełniony warunek lub stan zmieni się, należy umieścić te akcje w pętli "Do momentu". Twoja aplikacja logiki najpierw uruchamia wszystkie akcje wewnątrz pętli, a następnie sprawdza warunek lub stanu. Jeśli warunek jest spełniony, pętla zatrzymuje się. W przeciwnym razie powtórzeniu pętli.

Poniżej przedstawiono kilka typowych scenariuszy, w którym można korzystać z pętli "Do momentu":

* Wywołanie punktu końcowego, dopóki nie uzyskasz odpowiedzi, który ma.

* Utwórz rekord w bazie danych. Poczekaj, aż określone pole w tym zatwierdzeniu rekordu. Kontynuować przetwarzanie. 

Począwszy od 8:00:00 każdego dnia, to przykładowa aplikacja logiki zwiększa wartość zmiennej do momentu wartość zmiennej jest równa 10. Aplikacja logiki wysyła następnie wiadomość e-mail, który potwierdza bieżącą wartość. 

> [!NOTE]
> Te kroki odnoszą się usługi Office 365 Outlook, ale można użyć dowolnego dostawcy poczty e-mail, który obsługuje Logic Apps. 
> [Sprawdź tę listę łączników](https://docs.microsoft.com/connectors/). Jeśli używasz innego konta e-mail, ogólne kroki pozostają takie same, ale Twój interfejs użytkownika może wyglądać trochę inaczej. 

1. Tworzenia pustej aplikacji logiki. W Projektancie aplikacji logiki, w polu wyszukiwania, wybierz **wszystkich**. Wyszukaj "cykl". 
   Z listy wyzwalaczy wybierz następujący wyzwalacz: **Cykl - harmonogramu**

   ![Dodawanie wyzwalacza "Harmonogram — cykl"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Określ, kiedy wyzwalacza, ustawiając interwał, częstotliwość i godzinę. Aby ustawić godzinę, wybierz **Pokaż opcje zaawansowane**.

   ![Skonfiguruj harmonogram cyklu](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Właściwość | Wartość |
   | -------- | ----- |
   | **Interwał** | 1 | 
   | **Częstotliwość** | Dzień |
   | **W tych godzinach** | 8 |
   ||| 

1. W obszarze wyzwalacza wybierz **nowy krok**. 
   Wyszukaj "zmienne" i wybierz następującą akcję: **Inicjowanie zmiennej — zmienne**

   ![Dodawanie akcji "Initialize zmiennej — zmienne"](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Ustawianie zmiennej następującymi wartościami:

   ![Ustawianie właściwości zmiennej](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Właściwość | Wartość | Opis |
   | -------- | ----- | ----------- |
   | **Nazwa** | Limit | Nazwa zmiennej użytkownika | 
   | **Typ** | Liczba całkowita | Typ danych zmiennej użytkownika | 
   | **Wartość** | 0 | Zmiennej przez wartość początkowa | 
   |||| 

1. W obszarze **inicjowane zmiennej** akcji, wybierz **nowy krok**. 

1. W polu wyszukiwania wybierz pozycję **Wszystko**. Wyszukaj "do" i wybierz następującą akcję: **Do momentu — formant**

   ![Dodawanie "pętli do"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Tworzenie warunku zakończenia pętli, wybierając **Limit** zmiennej i **jest taki sam** operatora. 
   Wprowadź **10** jako wartość porównania.

   ![Tworzenie warunku zakończenia pętli zatrzymywania](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Wewnątrz pętli, wybierz **Dodaj akcję**. 

1. W polu wyszukiwania wybierz pozycję **Wszystko**. Wyszukaj "zmienne" i wybierz następującą akcję: **Zwiększ wartość zmiennej — zmienne**

   ![Dodawanie akcji dla zmiennej przyrostu o wartości](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. Dla **nazwa**, wybierz opcję **Limit** zmiennej. Aby uzyskać **wartość**, wpisz "1". 

     ![Zwiększ "Limit" o 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Wybierz poza i w obszarze pętli **nowy krok**. 

1. W polu wyszukiwania wybierz pozycję **Wszystko**. 
     Znajduj i dodawaj akcji, która wysyła wiadomość e-mail, na przykład: 

     ![Dodaj akcję, która wysyła wiadomość e-mail](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. W razie potrzeby zaloguj się do swojego konta e-mail.

1. Ustaw adres e-mail właściwości akcji. Dodaj **Limit** zmiennej do tematu. W ten sposób można sprawdzić, czy bieżąca wartość zmiennej spełnia określony warunek, na przykład:

      ![Ustawianie właściwości wiadomości e-mail](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Właściwość | Wartość | Opis |
      | -------- | ----- | ----------- | 
      | **Do** | *\<email-address\@domain>* | adres e-mail adresata. Do testowania, należy użyć własnego adresu e-mail. | 
      | **Temat** | Bieżąca wartość "Limit" jest **Limit** | Określ temat wiadomości e-mail. W tym przykładzie, upewnij się, że zawrzesz **Limit** zmiennej. | 
      | **Treść** | <*email-content*> | Określ treść wiadomości wiadomości e-mail, który chcesz wysłać. W tym przykładzie wprowadź tekst, niezależnie od chcesz. | 
      |||| 

1. Zapisz aplikację logiki. Aby ręcznie przetestować aplikację logiki, na pasku narzędzi Projektanta wybierz **Uruchom**.

      Po logikę zacznie działać, otrzymasz wiadomość e-mail z zawartością, który określiłeś:

      ![Odebrano wiadomość e-mail](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Zapobiegaj nieskończone pętle

Pętlę "Do momentu" ma limity domyślne, które zatrzymać wykonywanie, jeśli którykolwiek z tych warunków:

| Właściwość | Wartość domyślna | Opis | 
| -------- | ------------- | ----------- | 
| **Liczba** | 60 | Największa liczba pętli, które Uruchom przed kończy pętli. Wartość domyślna to 60 cykli. | 
| **limit czasu** | PT1H | Kończy działanie większości ilość czasu, aby uruchomić pętlę przed pętli. Wartość domyślna to 1 godzina i jest określony w formacie ISO 8601. <p>Wartość limitu czasu jest szacowana dla każdego cyklu pętli. Jeśli żadnych działań w pętli trwa dłużej niż limit czasu, nie zatrzymuje bieżącego cyklu. Jednak na następny cykl nie zaczyna się, ponieważ nie jest spełniony warunek limit. | 
|||| 

Aby zmienić te domyślne limity, wybierz **Pokaż opcje zaawansowane** w kształcie akcji pętli.

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Do" definition (JSON)

Jeśli pracujesz w widoku kodu aplikacji logiki, można zdefiniować `Until` pętli w definicji JSON aplikacji logiki zamiast tego, na przykład:

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

W tym przykładzie "Do momentu" pętli wywołuje punkt końcowy HTTP, która umożliwia utworzenie zasobu. Pętla zatrzymuje się wraz z treści odpowiedzi HTTP zwraca `Completed` stanu. Aby zapobiec nieskończonej pętli, pętla zatrzymuje również Jeśli którykolwiek z tych warunków:

* Pętla uruchomiono 10 razy określony przez `count` atrybutu. Wartość domyślna to 60 razy. 

* Pętli został uruchomiony przez dwie godziny, określony przez `timeout` atrybutu w formacie ISO 8601. Wartość domyślna to jedna godzina.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać lub głosować na funkcje i sugestie, [witrynie opinii użytkowników usługi Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Wykonaj kroki na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Wykonaj kroki na podstawie różnych wartości (instrukcji switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchom lub scalania równoległymi krokami (gałęzie)](../logic-apps/logic-apps-control-flow-branches.md)
* [Wykonaj kroki na podstawie stanu akcji grupowanych (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
