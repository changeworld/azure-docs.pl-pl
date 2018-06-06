---
title: Dodaj zakresy, których wykonanie akcji na podstawie stanu grupy - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Jak utworzyć zakresy, których wykonanie akcji przepływu pracy na podstawie grupy stanu akcji w aplikacjach logiki platformy Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: cfowler
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: c6f6b54e17d12ff21d50748810699e78e3a14757
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726384"
---
# <a name="create-scopes-that-run-workflow-actions-based-on-group-status-in-azure-logic-apps"></a>Utwórz zakresy, których wykonanie akcji przepływu pracy na podstawie stanu grupy w usłudze Azure Logic Apps

Do uruchamiania działań, tylko po innej grupy działań powodzenie lub niepowodzenie, grupy te akcje wewnątrz *zakres*. Ta struktura jest przydatne, gdy chcesz Organizuj akcje jako grupę logiczną, oceny stanu tej grupy i akcje, które są oparte na stanie zakresu. Po wszystkie akcje w zakresie zakończą działanie, zakres również pobiera własny stan. Na przykład zakresów można użyć podczas implementowania [wyjątku i obsługa błędów](../logic-apps/logic-apps-exception-handling.md#scopes). 

Aby sprawdzić stan zakresu, można użyć takich samych kryteriów, które można określić logiki aplikacji uruchom stanu, takie jak "Powodzenie", "Nie powiodło się", "Odwołania" i tak dalej. Domyślnie gdy zakres wszystkie akcje powiedzie się, stan zakresu jest on oznaczony "Powodzenie". Ale jeśli żadnych czynności w zakresie nie powiedzie się lub jest anulowany, stan zakresu jest oznaczony jako "Nieudane". Dla ograniczenia na podstawie zakresów, zobacz [limity i konfiguracji](../logic-apps/logic-apps-limits-and-config.md). 

Na przykład w tym miejscu jest aplikacji logiki wysokiego poziomu, który używa zakresu uruchamianie określonych akcji i warunek, aby sprawdzić stan zakresu. Jeśli wszystkie akcje w zakresie się nie powieść się ani kończyć się nieoczekiwanie, zakres jest oznaczony jako "Niepowodzenie" lub "Przerwania" odpowiednio i aplikację logiki wysyła komunikat "Scope nie powiodło się". W przypadku wszystkich akcji zakresami, aplikację logiki wysyła komunikat "Zakresu zakończyło się pomyślnie.".

![Konfigurowanie wyzwalacza "Harmonogram cyklu —"](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć w przykładzie w tym artykule, będą potrzebne następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Konto e-mail od dowolnego dostawcy poczty e-mail obsługiwane przez aplikacje logiki. W tym przykładzie użyto Outlook.com. Jeśli używasz innego dostawcę ogólny przebieg pozostaje taki sam, ale Interfejsie użytkownika pojawia się w różnych.

* Klucz usługi mapy Bing. Aby pobrać ten klucz, zobacz <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">uzyskać klucz usługi mapy Bing</a>.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Tworzenie aplikacji logiki próbki

Najpierw utwórz tej przykładowej aplikacji logiki, tak aby później można dodać zakresu:

![Tworzenie aplikacji logiki próbki](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* A **harmonogram - cyklu** wyzwalacz, który sprawdza usługi mapy Bing z interwałem określonym przez użytkownika
* A **mapy Bing - Get trasy** akcji, która sprawdza podczas podróży między dwiema lokalizacjami
* Instrukcji warunkowej, która sprawdza, czy podczas podróży przekracza określony podróży
* Akcja, która wysyła wiadomości e-mail tego bieżący czas podróży przekracza określony czas

Można zapisać aplikację logiki w dowolnym momencie, dlatego często Zapisz swoją pracę.

1. Zaloguj się do <a href="https://portal.azure.com" target="_blank">portalu Azure</a>, jeśli nie jest jeszcze. Tworzenia pustej aplikacji logiki.

2. Dodaj **harmonogram - cyklu** wyzwalacza przy użyciu tych ustawień: **interwał** = "1" i **częstotliwość** = "Min"

   ![Konfigurowanie wyzwalacza "Harmonogram cyklu —"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Aby wizualnie uprościć widoku i Ukryj szczegóły każdego działania w projektancie, należy Zwiń kształtu każdego działania w czasie wykonywania tych kroków.

3. Dodaj **mapy Bing - Get trasy** akcji. 

   1. Jeśli nie masz już połączenie z usługą mapy Bing, jest wyświetlany monit o utworzyć połączenie.

      | Ustawienie | Wartość | Opis |
      | ------- | ----- | ----------- |
      | **Nazwa połączenia** | BingMapsConnection | Podaj nazwę połączenia. | 
      | **Klucz interfejsu API** | <*klucz_usługi_Mapy_Bing*> | Wprowadź uzyskany wcześniej klucz usługi Mapy Bing. | 
      ||||  

   2. Konfigurowanie sieci **trasy Get** akcji, jak pokazano w tabeli poniżej tego obrazu:

      ![Konfigurowanie "Mapy Bing - Get trasy" akcji](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Aby uzyskać więcej informacji na temat tych parametrów, zobacz [Calculate route (Obliczanie trasy)](https://msdn.microsoft.com/library/ff701717.aspx).

      | Ustawienie | Wartość | Opis |
      | ------- | ----- | ----------- |
      | **Punkt nawigacyjny 1** | <*start*> | Wprowadź źródła z trasy. | 
      | **Punkt nawigacyjny 2** | <*Koniec*> | Wprowadź docelowej z trasy. | 
      | **Unikaj** | Brak | Wprowadź elementy, aby uniknąć na trasy, na przykład autostrady, przejazd i tak dalej. Możliwe wartości, zobacz [obliczyć trasę](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Optymalizacja** | timeWithTraffic | Wybierz parametr w celu zoptymalizowania trasy, na przykład odległość, czas z bieżących informacji ruchu i tak dalej. W tym przykładzie użyto tej wartości: "timeWithTraffic" | 
      | **Jednostka odległości** | <*według_preferencji*> | Wprowadź jednostkę odległość do obliczenia z trasy. W tym przykładzie użyto tej wartości: "Mil" | 
      | **Tryb podróży** | Jazda samochodem | Tryb podróży z trasy. W tym przykładzie użyto wartości "Prowadzenie" | 
      | **Transport publiczny — data i godzina** | Brak | Dotyczy tylko tryb przesyłania. | 
      | **Przesyłania typu Date — typ** | Brak | Dotyczy tylko tryb przesyłania. | 
      ||||  

4. Dodaj warunek do Sprawdź, czy bieżący czas podróży z ruchem przekroczy określony czas. Na przykład wykonaj kroki opisane w tym obrazu:

   ![Tworzenie warunku](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Zmień nazwę warunku o tym opisie: **Jeśli ruch czas więcej niż określony czas**

   2. Wybierz z listy wartości parametru **ruchu czas trwania podróży** pola, które jest w sekundach. 

   3. Ten operator wybierz operator porównania: **jest większa niż**

   4. Dla wartości porównania wprowadź **600**, która znajduje się w sekundach i równoważne do 10 minut.

5. W tym stanie **w przypadku wartości PRAWDA** gałęzi, dodać akcję "Wyślij wiadomość e-mail" dla dostawcy usługi poczty e-mail. Konfigurowanie tej akcji ze szczegółami, jak pokazano w tabeli poniżej tego obrazu:

   ![Dodaj akcję "Wyślij wiadomość e-mail" "Jeśli to prawda" gałęzi](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. Aby uzyskać **do** wprowadź swój adres e-mail do celów testowych.

   2. Aby uzyskać **podmiotu** wprowadź tekst:

      ```Time to leave: Traffic more than 10 minutes```

   3. Aby uzyskać **treści** wprowadź tekst spacji: 

      ```Travel time: ```

      Gdy kursor jest wyświetlany w **treści** pola listy dynamicznej zawartości pozostanie otwarte, w którym można wybrać żadnych parametrów, które są dostępne w tym momencie.

   4. Na liście zawartości dynamicznej wybierz pozycję **Wyrażenie**.

   5. Znajdź i zaznacz pozycję **(div)** funkcji.

   6. Gdy kursor znajduje się wewnątrz nawiasów funkcji, wybierz **zawartości dynamicznej** , w którym można dodawać **ruchu czas trwania ruchu** parametru dalej.

   7. W obszarze **trasy Get** na liście parametrów dynamicznych, wybierz **ruchu czas trwania ruchu** pola.

      ![Wybierz opcję "Ruchu czas trwania ruch"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   8. Po rozpoznaje pole do formatu JSON, dodać **przecinkami** (```,```) wraz z numerem ```60``` tak, aby zamienić wartości w **ruchu czas trwania ruchu** z czasu w sekundach minut. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Teraz w wyrażeniu wygląda następująco:

      ![Zakończenie wyrażenia](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   9. Upewnij się, że wybierasz **OK** po zakończeniu.

  10. Po wyrażenie rozwiązanie, należy dodać ten tekst z spacje wiodące: ``` minutes```
  
      Twoje **treści** pola teraz wygląda następująco:

      ![Pole "Treść" zakończone](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)

6. Zapisz aplikację logiki.

Następnie Dodaj zakres, aby mogli grupy określonych akcji i oceny ich stanu.

## <a name="add-a-scope"></a>Dodaj zakres

1. Jeśli nie jest jeszcze, Otwórz aplikację logiki w Projektancie aplikacji logiki. 

2. Dodawanie zakresu w lokalizacji przepływu pracy, który ma. Na przykład:

   * Aby dodać zakres między krokami istniejących w przepływie pracy aplikacji logiki, wskaźnika na strzałkę której chcesz dodać do zakresu. 
   Wybierz **znak plus** (**+**) > **Dodawanie zakresu**.

     ![Dodaj zakres](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

     Jeśli chcesz dodać zakres na końcu przepływu pracy, w dolnej części aplikacji logiki, wybierz **+ nowy krok** > **... Więcej** > **Dodawanie zakresu**.

3. Teraz dodać kroki lub przeciągnij istniejące kroki, które mają być uruchamiane wewnątrz zakresu. Na przykład przeciągnij te akcje w zakresie:
      
   * **Pobierz trasy**
   * **Jeśli ruch czas więcej niż określony czas**, która obejmuje zarówno **true** i **false** gałęzi

   Aplikację logiki teraz wygląda następująco:

   ![Dodaje zakres](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

4. W zakresie należy dodać warunek, który umożliwia sprawdzenie stanu zakresu. Zmień nazwę warunku o tym opisie: **Jeśli zakres nie powiodła się.**

   ![Dodaj warunek do sprawdzenia stanu zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
5. To wyrażenie, które sprawdza, czy stan zakresu jest równa kompilacji `Failed` lub `Aborted`.

   ![Dodaj wyrażenie, które umożliwia sprawdzenie stanu zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/build-expression-check-scope-status.png)

   Lub aby wprowadzić tego wyrażenia jako tekst, wybierz **edytowanie w trybie zaawansowanym**.

   ```@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')```

6. W **w przypadku wartości PRAWDA** i **w przypadku wartości FAŁSZ** gałęzie, Dodaj akcje, które ma zostać wykonana, na przykład wysłać wiadomości e-mail lub wiadomości.

   ![Dodaj wyrażenie, które umożliwia sprawdzenie stanu zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

7. Zapisz aplikację logiki.

Aplikację logiki Zakończono teraz wygląda tak jak ten przykład wszystkie kształty rozszerzona:

![Zakończono logiki aplikacji o zakresie](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Testowanie pracy

Na pasku narzędzi projektanta, wybierz **Uruchom**. W przypadku wszystkich akcji zakresami, pojawia się komunikat "Pomyślnie zakresu". Jeśli wszystkie akcje w zakresie nie powiedzie się, pojawi się komunikat "Scope nie powiodło się". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definicja formatu JSON

Jeśli pracujesz w widoku kodu, można zdefiniować struktury zakresu w definicji JSON aplikację logiki zamiast tego. Na przykład w tym miejscu jest definicji JSON dla wyzwalacza i działania opisane w poprzedniej aplikacji logiki:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    },
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "None",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": "@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')",
    "runAfter": {
      "Scope": [
        "Succeeded"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'], 60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": "@greater(body('Get_route')?['travelDurationTraffic'], 600)",
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
}
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać lub oddawać głosy na funkcje i sugestie, odwiedź stronę [witrynę opinii użytkowników usługi Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Wykonanie kroków na podstawie warunku (warunkowe instrukcje)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Wykonanie kroków na podstawie różnych wartości (instrukcji switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchom i powtórz kroki (pętle)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchom lub scalania czynności równoległe (gałęzi)](../logic-apps/logic-apps-control-flow-branches.md)
