---
title: Dodawanie pętli, które Powtarzanie operacji, lub przetwarzać tablice — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie pętli, które należy powtórzyć akcji przepływu pracy lub przetwarzać tablice w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 5ba5e5abef4ebdc58c44cbe7f5ba584efe8abfc7
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233110"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Tworzenie pętli, które należy powtórzyć akcji przepływu pracy lub przetwarzać tablice w usłudze Azure Logic Apps

Aby wykonać iterację tablic w aplikacji logiki, można użyć [pętlę "Foreach"](#foreach-loop) lub [sekwencyjne pętlę "Foreach"](#sequential-foreach-loop). Iteracji dla standardowych pętli "Foreach" Uruchom równolegle, podczas uruchamiania iteracji dla pętli "Foreach" sekwencyjnej, pojedynczo. Uzyskać maksymalną liczbę elementów tablicy, które pętli "Foreach" mogą przetwarzać w przebiegu aplikacji logiki pojedynczego, zobacz [limity i Konfiguracja](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP] 
> Jeśli masz wyzwalacz, który odbiera tablicy i chcesz uruchomić przepływ pracy dla każdego elementu tablicy, możesz to zrobić *debatch* tablicy przy użyciu [ **SplitOn** wyzwalacza właściwości](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 
  
Aby powtórzyć działania, dopóki spełniony jest warunek lub niektóre stan został zmieniony, należy użyć ["pętlą Until"](#until-loop). Twoja aplikacja logiki najpierw wykonuje wszystkie akcje wewnątrz pętli, a następnie sprawdza warunek jako ostatni krok. Jeśli warunek jest spełniony, pętla zatrzymuje się. W przeciwnym razie powtórzeniu pętli. Maksymalna liczba "Do" pętli w aplikacji logiki pojedynczego uruchamiania, zobacz [limity i Konfiguracja](../logic-apps/logic-apps-limits-and-config.md). 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Pętlę "Foreach"

Akcje należy powtórzyć dla każdego elementu w tablicy, należy użyć pętli "Foreach" w przepływie pracy aplikacji logiki. Może zawierać wiele działań w pętli "Foreach" i można zagnieżdżać pętli "Foreach" wewnątrz siebie nawzajem. Domyślnie cykle w pętlę "Foreach" Standardowa uruchamiane równolegle. Aby uzyskać maksymalną liczbę równoległych cykle tego "Foreach" pętli można uruchomić, zobacz [limity i Konfiguracja](../logic-apps/logic-apps-limits-and-config.md).

> [!NOTE] 
> Pętlę "Foreach" działa tylko w przypadku tablicy, a następnie użyć działania w pętli `@item()` odwołanie do przetworzenia każdego elementu w tablicy. Jeśli określisz dane, które nie znajduje się w tablicy, przepływ pracy aplikacji logiki nie powiedzie się. 

Na przykład ta aplikacja logiki wyśle do Ciebie codzienne podsumowanie z kanału informacyjnego RSS witryny sieci Web. Aplikacja używa pętli "Foreach", która wysyła wiadomość e-mail każdego nowego elementu znaleziono.

1. [Ta przykładowa aplikacja logiki tworzenie](../logic-apps/quickstart-create-first-logic-app-workflow.md) przy użyciu konta Outlook.com lub Office 365 Outlook.

2. Między RSS wyzwalania oraz akcję wysyłania wiadomości e-mail, Dodaj pętlę "Foreach". 

   Aby dodać pętlę między krokami, przesuń wskaźnik nad strzałką znajdującą się, w której chcesz dodać pętli. 
   Wybierz **znak plus** (**+**) wyświetlany, wybierz **Dodaj dla każdego**.

   ![Dodaj pętlę "Foreach" między krokami](media/logic-apps-control-flow-loops/add-for-each-loop.png)

3. Tworzenie pętli. W obszarze **wybierz dane wyjściowe z poprzednich kroków** po **Dodaj zawartość dynamiczną** zostanie wyświetlona lista, wybierz **linki źródła danych** tablicy, która jest wynikiem wyzwalacz kanału informacyjnego RSS. 

   ![Wybierz z listy zawartości dynamicznej](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Możesz wybrać *tylko* tablicy danych wyjściowych z poprzedniego kroku.

   Wybranej tablicy pojawi się w tym miejscu:

   ![Wybierz tablicy](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Aby wykonać akcję dla każdego elementu tablicy, przeciągnij **Wyślij wiadomość e-mail** akcji do **dla każdego** pętli. 

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
        "runAfter": {},
    }
},
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>Pętlę "Foreach": sekwencyjne

Domyślnie każdy cykl w pętli "Foreach" uruchamia się równolegle dla każdego elementu tablicy. Aby uruchomić każdy cykl po kolei, należy ustawić **sekwencyjnego** opcji w pętlę "Foreach".

1. W pętli w prawym górnym rogu wybierz **wielokropek** (**...** ) > **Ustawienia**.

   ![W pętli "Foreach" Wybierz "..." > "Ustawienia"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

2. Włącz **sekwencyjnego** ustawienie, wybierz **gotowe**.

   ![Włącz ustawienie sekwencyjne pętlę "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Można również ustawić **operationOptions** parametr `Sequential` w definicji JSON aplikacji logiki. Na przykład:

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {               
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
        "operationOptions": "Sequential"
    }
},
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Pętlą until"
  
Aby Powtarzanie operacji, dopóki spełniony jest warunek lub niektóre stan został zmieniony, należy użyć pętli "Do momentu" w przepływie pracy aplikacji logiki. Poniżej przedstawiono niektóre typowe przypadki użycia, w którym można korzystać z pętli "Do momentu":

* Wywołanie punktu końcowego, dopóki nie zostanie wyświetlona odpowiedź, którą chcesz.
* Utwórz rekord w bazie danych, poczekaj, aż określone pole w tym zatwierdzeniu rekordu i kontynuować przetwarzanie. 

Na przykład o 8:00:00 każdego dnia, ta aplikacja logiki zwiększa wartość zmiennej do momentu wartość zmiennej jest równa 10. Następnie aplikacja logiki wysyła wiadomość e-mail, który potwierdza bieżącą wartość. Chociaż w tym przykładzie użyto usługi Office 365 Outlook, można użyć dowolnego dostawcy poczty e-mail obsługiwane przez usługę Logic Apps ([Przejrzyj tę listę łączników](https://docs.microsoft.com/connectors/)). Jeśli korzystasz z innego konta e-mail, ogólne kroki będą takie same, ale interfejs użytkownika może się trochę różnić. 

1. Tworzenia pustej aplikacji logiki. W Projektancie aplikacji logiki, wyszukaj "cykl", a następnie wybierz następujący wyzwalacz: **harmonogram — cyklicznie** 

   ![Dodawanie wyzwalacza "Harmonogram — cyklicznie"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

2. Określ, kiedy wyzwalacza, ustawiając interwał, częstotliwość i godzinę. Aby ustawić godzinę, wybierz **Pokaż opcje zaawansowane**.

   ![Dodawanie wyzwalacza "Harmonogram — cyklicznie"](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Właściwość | Wartość |
   | -------- | ----- |
   | **Interwał** | 1 | 
   | **Częstotliwość** | Dzień |
   | **W tych godzinach** | 8 |
   ||| 

3. W obszarze wyzwalacza wybierz **nowy krok** > **Dodaj akcję**. Wyszukaj "zmienne", a następnie wybierz tę akcję: **zmienne — inicjowane zmiennej**

   ![Dodaj "Zmienne - inicjowane zmiennej" Akcja](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

4. Ustawianie zmiennej następującymi wartościami:

   ![Ustawianie właściwości zmiennej](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Właściwość | Wartość | Opis |
   | -------- | ----- | ----------- |
   | **Nazwa** | Limit | Nazwa zmiennej użytkownika | 
   | **Typ** | Liczba całkowita | Typ danych zmiennej użytkownika | 
   | **Wartość** | 0 | Zmiennej przez wartość początkowa | 
   |||| 

5. W obszarze **inicjowane zmiennej** akcji, wybierz **nowy krok** > **więcej**. Wybierz tę pętlę: **Dodaj instrukcję wykonuj do**

   ![Dodaj pętlę "wykonuj, dopóki"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

6. Tworzenie warunku zakończenia pętli, wybierając **Limit** zmiennej i **jest taki sam** operatora. Wprowadź **10** jako wartość porównania.

   ![Tworzenie warunku zakończenia pętli zatrzymywania](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

7. Wewnątrz pętli, wybierz **Dodaj akcję**. Wyszukaj "zmienne", a następnie dodać tę akcję: **zmienne — Zwiększ wartość zmiennej**

   ![Dodawanie akcji dla zmiennej przyrostu o wartości](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

8. Dla **nazwa**, wybierz opcję **Limit** zmiennej. Aby uzyskać **wartość**, wpisz "1". 

   ![Zwiększ "Limit" o 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

9. W obszarze, ale poza pętlę Dodaj akcję, która wysyła wiadomość e-mail. W razie potrzeby zaloguj się do swojego konta e-mail.

   ![Dodaj akcję, która wysyła wiadomość e-mail](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

10. Ustaw właściwości adresu e-mail. Dodaj **Limit** zmiennej do tematu. W ten sposób można sprawdzić, czy bieżąca wartość zmiennej spełnia określony warunek, na przykład:

    ![Ustawianie właściwości wiadomości e-mail](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

    | Właściwość | Wartość | Opis |
    | -------- | ----- | ----------- | 
    | **Do** | *<email-address@domain>* | adres e-mail adresata. Do testowania, należy użyć własnego adresu e-mail. | 
    | **Temat** | Bieżąca wartość "Limit" jest **Limit** | Określ temat wiadomości e-mail. W tym przykładzie, upewnij się, że zawrzesz **Limit** zmiennej. | 
    | **Treść** | <*email-content*> | Określ treść wiadomości wiadomości e-mail, który chcesz wysłać. W tym przykładzie wprowadź tekst, niezależnie od chcesz. | 
    |||| 

11. Zapisz aplikację logiki. Aby ręcznie przetestować aplikację logiki, na pasku narzędzi Projektanta wybierz **Uruchom**.

    Po logikę zacznie działać, otrzymasz wiadomość e-mail z zawartością, który określiłeś:

    ![Odebrano wiadomość e-mail](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Zapobiegaj nieskończone pętle

Pętlę "Do momentu" ma limity domyślne, które zatrzymać wykonywanie, jeśli którykolwiek z tych warunków:

| Właściwość | Wartość domyślna | Opis | 
| -------- | ------------- | ----------- | 
| **Liczba** | 60 | Maksymalna liczba pętli, które Uruchom przed kończy pętli. Wartość domyślna to 60 cykli. | 
| **limit czasu** | PT1H | Kończy działanie maksymalną ilość czasu, aby uruchomić pętlę przed pętli. Wartość domyślna to 1 godzina i jest określony w formacie ISO 8601. <p>Wartość limitu czasu jest szacowana dla każdego cyklu pętli. Jeśli żadnych działań w pętli trwa dłużej niż limit czasu, nie zatrzymuje bieżącego cyklu, ale na następny cykl i nie uruchomi się, ponieważ nie jest spełniony warunek limit. | 
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
        },
    }
},
```

W kolejnym przykładzie ta pętla "Do momentu" wywołuje punkt końcowy HTTP, który tworzy zasób i zatrzymuje się wraz z treści odpowiedzi HTTP zwraca stan "Completed". Aby zapobiec nieskończonej pętli, pętla zatrzymuje również Jeśli którykolwiek z tych warunków:

* Pętla została uruchomiona 10 razy określony przez `count` atrybutu. Wartość domyślna to 60 razy. 
* Pętla zawiera próbowano uruchomić na dwie godziny, określony przez `timeout` atrybutu w formacie ISO 8601. Wartość domyślna to jedna godzina.
  
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
},
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać lub głosować na funkcje i sugestie, [witrynie opinii użytkowników usługi Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Wykonaj kroki na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Wykonaj kroki na podstawie różnych wartości (instrukcji switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchom lub scalania równoległymi krokami (gałęzie)](../logic-apps/logic-apps-control-flow-branches.md)
* [Wykonaj kroki na podstawie stanu akcji grupowanych (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
