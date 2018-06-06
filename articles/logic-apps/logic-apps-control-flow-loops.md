---
title: Dodawanie pętli, które Powtórz akcje lub przetworzyć tablice - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie pętli, które powtórzyć działania przepływu pracy lub procesu tablic w aplikacjach logiki platformy Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: cfowler
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 26db63ed7706bdff9eb5c037a906fbc252b83a9a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725959"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Tworzenie pętli, które powtórzyć działania przepływu pracy lub procesu tablic w aplikacjach logiki platformy Azure

Aby wykonać iterację tablic w aplikacji logiki, można użyć [pętli "Foreach"](#foreach-loop) lub [sekwencyjnych pętli "Foreach"](#sequential-foreach-loop). Iteracje dla standardowych pętli "Foreach" równolegle, podczas uruchamiania iteracji dla pętli "Foreach" sekwencyjnych, pojedynczo. Maksymalna liczba elementów tablicy, które pętle "Foreach" można przetwarzać w aplikacji logiki pojedynczego uruchomienia, zobacz [limity i konfiguracji](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP] 
> Jeśli masz wyzwalacz, który odbiera tablicy i chcesz uruchomić przepływ pracy dla każdego elementu tablicy, możesz *debatch* tablicy z [ **SplitOn** wyzwolenia właściwości](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 
  
Aby powtórzyć działania, dopóki spełniony jest warunek lub niektóre stan został zmieniony, należy użyć ["Do" pętli](#until-loop). Aplikację logiki najpierw wykonuje wszystkie akcje wewnątrz pętli, a następnie sprawdza warunek jako ostatni krok. Jeśli warunek jest spełniony, zatrzymuje pętli. W przeciwnym razie powtarza pętli. Maksymalna liczba "Do" pętli w aplikacji logiki pojedynczego uruchomienia, zobacz [limity i konfiguracji](../logic-apps/logic-apps-limits-and-config.md). 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Pętla "Foreach"

Aby akcje należy powtórzyć dla każdego elementu w tablicy, należy skorzystać z pętli "Foreach" w przepływie pracy aplikacji logiki. Może zawierać wiele akcji w pętli "Foreach" i można zagnieżdżać pętle "Foreach" wewnątrz siebie nawzajem. Domyślnie cykle w pętli "Foreach" standardowe są uruchamiane równolegle. Aby uzyskać maksymalną liczbę równoległych cykli tego "Foreach" pętle można uruchomić, zobacz [limity i konfiguracji](../logic-apps/logic-apps-limits-and-config.md).

> [!NOTE] 
> Pętla "Foreach" działa tylko w przypadku tablicy i działania w pętli przy użyciu `@item()` odwołanie do przetworzenia każdego elementu w tablicy. Określ dane, które nie znajduje się w tablicy, przepływ pracy aplikacji logiki kończy się niepowodzeniem. 

Na przykład ta aplikacja logiki wysyła do Ciebie dzienne podsumowanie z kanału informacyjnego RSS witryny sieci Web. Aplikacja korzysta z pętli "Foreach", który wysyła wiadomości e-mail dla znaleziono każdego nowego elementu.

1. [Tworzenie aplikacji logiki tej próbki](../logic-apps/quickstart-create-first-logic-app-workflow.md) przy użyciu konta usługi Outlook.com lub Office 365 Outlook.

2. Między RSS wyzwolenia i wysłanie wiadomości e-mail, Dodaj pętlę "Foreach". 

   Aby dodać pętla między krokami, wskaźnika na strzałkę której chcesz dodać pętli. 
   Wybierz **znak plus** (**+**) wyświetlony, a następnie wybierz **Dodaj dla każdego**.

   ![Dodaj pętlę "Foreach" między krokami](media/logic-apps-control-flow-loops/add-for-each-loop.png)

3. Teraz tworzyć pętli. W obszarze **wybierz wyjścia z poprzednich kroków** po **dodać zawartość dynamiczną** zostanie wyświetlona lista, wybierz **źródła łącza** tablicy, czyli danych wyjściowych z wyzwalacza RSS. 

   ![Wybierz z listy zawartości dynamicznej](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Możesz wybrać *tylko* tablicy danych wyjściowych z poprzedniego kroku.

   Wybrane tablicy pojawi się tutaj:

   ![Wybierz tablicy](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Aby wykonać akcję na każdy element tablicy, przeciągnij **wysłać wiadomość e-mail** akcji do **dla każdego** pętli. 

   Aplikację logiki może wyglądać następująco:

   ![Dodaj kroki do pętli "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Zapisz aplikację logiki. Aby ręcznie przetestować aplikację logiki, na pasku narzędzi projektanta, wybierz **Uruchom**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definicja pętli "Foreach" (JSON)

Jeśli pracujesz w widoku kodu aplikacji logiki, można zdefiniować `Foreach` pętli w definicji JSON aplikację logiki, na przykład:

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

## <a name="foreach-loop-sequential"></a>Pętla "Foreach": sekwencyjne

Domyślnie każdy cykl w pętli "Foreach" uruchamia się równolegle dla każdego elementu tablicy. Aby cyklu są wykonywane sekwencyjnie, ustaw **sekwencyjnego** opcji w Twojej pętli "Foreach".

1. W pętli prawym górnym rogu wybierz **wielokropek** (**...** ) > **Ustawienia**.

   ![W pętli "Foreach", wybierz polecenie "..." > "Ustawienia"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

2. Włącz **sekwencyjnego** ustawienia, a następnie wybierz **gotowe**.

   ![Włącz ustawienie sekwencyjnego "Foreach" pętli](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Można również ustawić **operationOptions** parametr `Sequential` w definicji JSON aplikację logiki. Na przykład:

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

## <a name="until-loop"></a>"Do" pętli
  
Powtarzaj akcje, dopóki spełniony jest warunek lub niektóre stan został zmieniony, należy użyć pętli "Do momentu" w przepływie pracy aplikacji logiki. Poniżej przedstawiono niektóre typowe przypadki użycia, których można użyć pętli "Do momentu":

* Wywołanie punkt końcowy do momentu uzyskania odpowiedzi, który ma.
* Utwórz rekord w bazie danych, czekać do momentu określonego pola w tym zatwierdzeniu rekordu i kontynuować przetwarzania. 

Na przykład o 8:00 każdego dnia, ta aplikacja logiki zwiększa zmiennej dopóki wartość zmiennej jest równa 10. Następnie aplikacji logiki wysyła wiadomość e-mail, który potwierdza bieżącą wartość. Chociaż w tym przykładzie użyto programu Outlook pakietu Office 365, możesz użyć dowolnego dostawcy poczty e-mail obsługiwane przez aplikacje logiki ([przeglądu łączniki tutaj](https://docs.microsoft.com/connectors/)). Jeśli korzystasz z innego konta e-mail, ogólne kroki będą takie same, ale interfejs użytkownika może się trochę różnić. 

1. Tworzenia pustej aplikacji logiki. W Projektancie aplikacji logiki, wyszukaj "cyklu", a następnie wybierz tego wyzwalacza: **harmonogram - cyklu** 

   ![Dodaj wyzwalacza "Harmonogram cyklu —"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

2. Określ, kiedy wyzwalacz uruchamia się przez ustawienie interwału częstotliwość i godzinę dnia. Aby ustawić godzinę, wybierz **Pokaż zaawansowane opcje**.

   ![Dodaj wyzwalacza "Harmonogram cyklu —"](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Właściwość | Wartość |
   | -------- | ----- |
   | **Interwał** | 1 | 
   | **Częstotliwość** | Dzień |
   | **W tych godzinach** | 8 |
   ||| 

3. W obszarze wyzwalacza, wybierz **nowy krok** > **Dodaj akcję**. Wyszukaj "zmiennych", a następnie wybierz tę akcję: **zmienne - zainicjować zmiennej**

   ![Dodaj "Zmienne - zainicjować zmiennej" akcji](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

4. Ustawienie zmiennej użytkownika z tymi wartościami:

   ![Ustawianie właściwości zmiennej](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Właściwość | Wartość | Opis |
   | -------- | ----- | ----------- |
   | **Nazwa** | Limit | Nazwa zmiennej użytkownika | 
   | **Typ** | Liczba całkowita | Typ danych zmiennej użytkownika | 
   | **Wartość** | 0 | Do zmiennej na wartość początkowa | 
   |||| 

5. W obszarze **zainicjować zmiennej** akcji, wybierz **nowy krok** > **więcej**. Wybierz tę pętlę: **czy do dodania**

   ![Dodaj pętlę "należy do"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

6. Tworzenie warunku zakończenia pętli, wybierając **Limit** zmiennej i **jest taki sam** operatora. Wprowadź **10** jako wartość porównania.

   ![Tworzenie warunku zakończenia pętli zatrzymywania](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

7. Wewnątrz pętli wybierz **Dodaj akcję**. Wyszukaj "zmiennych", a następnie dodaj tę akcję: **zmienne - przyrostu zmiennej**

   ![Dodaj akcję dla wartości zmiennej](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

8. Dla **nazwa**, wybierz pozycję **Limit** zmiennej. Aby uzyskać **wartość**, wprowadź wartość "1". 

   ![Increment "Limit" przez użytkownika 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

9. W obszarze, ale poza pętli Dodaj akcję, która wysyła wiadomość e-mail. W razie potrzeby zaloguj się do swojego konta e-mail.

   ![Dodaj akcję, która wysyła wiadomość e-mail](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

10. Ustaw właściwości adresu e-mail. Dodaj **Limit** zmienną do tematu. W ten sposób można potwierdzić, bieżąca wartość zmiennej spełnia określony warunek, na przykład:

    ![Ustawianie właściwości wiadomości e-mail](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

    | Właściwość | Wartość | Opis |
    | -------- | ----- | ----------- | 
    | **Do** | *<email-address@domain>* | adres e-mail adresata. Do testowania, użyj adresu e-mail. | 
    | **Temat** | Bieżąca wartość dla "Limit" jest **Limit** | Podaj temat wiadomości e-mail. Na przykład upewnij się, że obejmuje **Limit** zmiennej. | 
    | **Treść** | <*email-content*> | Określ zawartość wiadomości e-mail, którą chcesz wysłać. Na przykład wprowadź tekst, niezależnie od chcesz. | 
    |||| 

11. Zapisz aplikację logiki. Aby ręcznie przetestować aplikację logiki, na pasku narzędzi projektanta, wybierz **Uruchom**.

    Logika uruchamiania, możesz otrzymywać wiadomości e-mail z wybraną zawartością:

    ![Odebranych wiadomości e-mail](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Zapobiegaj nieskończone pętle

Pętla "Do momentu" ma domyślne limity, które zatrzymuje wykonywanie, jeśli jeden z następujących warunków:

| Właściwość | Wartość domyślna | Opis | 
| -------- | ------------- | ----------- | 
| **Liczba** | 60 | Maksymalna liczba pętle uruchamiane przed opuszcza pętlę. Wartość domyślna to 60 cykli. | 
| **Limit czasu** | PT1H | Zamyka maksymalną ilość czasu na wykonanie pętli przed pętli. Wartość domyślna to jedna godzina i jest określona w formacie ISO 8601. <p>Wartość limitu czasu jest obliczane dla każdego cyklu pętli. Jeśli dowolnych akcji w pętli trwa dłużej niż limit czasu, Zatrzymaj nie bieżącego cyklu, ale nie uruchamia na następny cykl, ponieważ nie jest spełniony warunek limit. | 
|||| 

Aby zmienić te limity domyślne, wybierz **Pokaż zaawansowane opcje** w kształcie akcji pętli.

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Do" definition (JSON)

Jeśli pracujesz w widoku kodu aplikacji logiki, można zdefiniować `Until` pętli w definicji JSON aplikację logiki, na przykład:

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

W kolejnym przykładzie pętla "Do momentu" wymaga punktu końcowego HTTP, który tworzy zasób i zatrzymywana, gdy treść odpowiedzi HTTP zwraca ze stanem "Completed". Aby zapobiec nieskończone pętle, pętla zatrzymuje również jeśli jeden z następujących warunków:

* Pętla została uruchomiona 10 razy określony przez `count` atrybutu. Wartość domyślna to 60 razy. 
* Pętla próbował uruchomić przez 2 godziny, określony przez `timeout` atrybutu w formacie ISO 8601. Wartość domyślna to jedna godzina.
  
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
* Aby przesłać lub oddawać głosy na funkcje i sugestie, [witrynę opinii użytkowników usługi Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Wykonanie kroków na podstawie warunku (warunkowe instrukcje)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Wykonanie kroków na podstawie różnych wartości (instrukcji switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchom lub scalania czynności równoległe (gałęzi)](../logic-apps/logic-apps-control-flow-branches.md)
* [Wykonanie kroków na podstawie stanu akcji grupowanych (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
