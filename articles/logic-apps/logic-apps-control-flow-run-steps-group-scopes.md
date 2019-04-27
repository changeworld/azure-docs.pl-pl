---
title: Dodaj zakresy, które Uruchom akcje na podstawie stanu grupy — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Jak utworzyć zakresy systemem akcji przepływu pracy na podstawie stanu akcji grupy w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.reviewer: klam, LADocs
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: 48fb2d14cd4cf99510fff88b25b9ae45814a92a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60685560"
---
# <a name="run-actions-based-on-group-status-with-scopes-in-azure-logic-apps"></a>Uruchom akcje na podstawie stanu grupy z zakresów w usłudze Azure Logic Apps

Do uruchamiania działań, tylko wtedy, gdy inna grupa akcji powodzenie lub niepowodzenie, grupy te akcje wewnątrz *zakres*. Ta struktura jest przydatne, gdy chcesz Organizuj akcje logiczne grupy, ocenić stan dla tej grupy i wykonywać akcje, które są oparte na stanie zakres. Po wszystkie akcje w zakresie zakończą działanie, zakres zapewnia również swój własny status. Na przykład, można użyć zakresów, gdy należy wdrożyć [wyjątek i obsługa błędów](../logic-apps/logic-apps-exception-handling.md#scopes). 

Aby sprawdzić stan zakresu, można użyć takich samych kryteriów, które można określić logiki aplikacji uruchom stan, takich jak "Powodzenie", "Nie", "Odwołania" i tak dalej. Domyślnie gdy uda się zakresem wszystkie akcje, stan zakresu jest oznaczona "Powodzenie". Ale kiedy żadnych działań w zakresie ulegnie awarii lub zostanie anulowane, stan zakresu jest oznaczona "Niepowodzenie". Limity zakresów, zobacz [limity i Konfiguracja](../logic-apps/logic-apps-limits-and-config.md). 

Na przykład poniżej przedstawiono aplikację logiki wysokiego poziomu, która używa zakresu, aby uruchamiać określone akcje i warunek, aby sprawdzić stan zakresu. Jeśli wszystkie akcje w zakresie się nie powieść się lub kończy się nieoczekiwanie, zakres jest oznaczony jako "Niepowodzenie" lub "Aborted" odpowiednio i aplikacja logiki wysyła wiadomość "Zakresu nie powiodło się.". Jeśli wszystkie akcje o określonym zakresie powiedzie się, aplikacja logiki wysyła wiadomość "Zakres zakończyło się pomyślnie.".

![Konfigurowanie wyzwalacza "Harmonogram — cyklicznie"](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać instrukcje opisane w przykładzie w tym artykule, będą potrzebne następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Konto e-mail od dowolnego dostawcy poczty e-mail obsługiwane przez usługę Logic Apps. W tym przykładzie użyto usługi Outlook.com. Jeśli używasz innego dostawcy, ogólny przebieg pozostaje taka sama, ale Twój interfejs użytkownika zostanie wyświetlony inny.

* Klucz usługi mapy Bing. Aby pobrać ten klucz, zobacz <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">uzyskiwanie klucza usługi mapy Bing</a>.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Tworzenie przykładowej aplikacji logiki

Najpierw utwórz Ta przykładowa aplikacja logiki, tak aby później można dodać zakresu:

![Tworzenie przykładowej aplikacji logiki](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* A **harmonogram — cyklicznie** wyzwalacz, który sprawdza, czy usługi mapy Bing z interwałem określonym przez użytkownika
* A **mapy Bing — Pobierz trasę** akcji, która sprawdza czas podróży między dwiema lokalizacjami
* Instrukcję warunkową, która sprawdza, czy czas podróży przekracza określony podróży
* Akcja, która wysyła wiadomości e-mail tego aktualny czas podróży przekracza określony czas

Można zapisywanie aplikacji logiki w dowolnym momencie, dlatego często Zapisz swoją pracę.

1. Zaloguj się do <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a>, jeśli jeszcze go. Tworzenia pustej aplikacji logiki.

1. Dodaj **harmonogram — cyklicznie** wyzwalacz za pomocą tych ustawień: **Interwał** = "1" i **częstotliwość** = "Minute"

   ![Konfigurowanie wyzwalacza "Harmonogram — cyklicznie"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Aby wizualnie uprościć widok i ukryć szczegóły każdej akcji w projektancie, zwinięty kształt w każdej akcji w czasie wykonywania tych kroków.

1. Dodaj **mapy Bing — Pobierz trasę** akcji. 

   1. Jeśli nie masz jeszcze połączenia z usługą mapy Bing, zostanie wyświetlony monit utworzyć połączenie.

      | Ustawienie | Wartość | Opis |
      | ------- | ----- | ----------- |
      | **Nazwa połączenia** | BingMapsConnection | Podaj nazwę połączenia. | 
      | **Klucz interfejsu API** | <*klucz_usługi_Mapy_Bing*> | Wprowadź uzyskany wcześniej klucz usługi Mapy Bing. | 
      ||||  

   1. Konfigurowanie usługi **Pobierz trasę** akcji, jak pokazano w tabeli poniżej następująco:

      ![Konfigurowanie "Mapy Bing — Pobierz trasę" Akcja](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Aby uzyskać więcej informacji na temat tych parametrów, zobacz [Calculate route (Obliczanie trasy)](https://msdn.microsoft.com/library/ff701717.aspx).

      | Ustawienie | Wartość | Opis |
      | ------- | ----- | ----------- |
      | **Punkt nawigacyjny 1** | <*start*> | Wprowadź początek trasy. | 
      | **Punkt nawigacyjny 2** | <*koniec*> | Wprowadź trasy. | 
      | **Unikaj** | Brak | Wprowadź elementy, aby unikać trasy, na przykład autostrady, drogi i tak dalej. Możliwe wartości, zobacz [obliczanie trasy](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Optymalizacja** | timeWithTraffic | Wybierz parametr używany do optymalizowania trasy, na przykład odległość, czas przy użyciu bieżących informacji o ruchu i tak dalej. W tym przykładzie użyto tej wartości: "timeWithTraffic" | 
      | **Jednostka odległości** | <*według_preferencji*> | Wprowadź Jednostka odległości trasy obliczania. W tym przykładzie użyto tej wartości: "Mile" | 
      | **Tryb podróży** | Jazda samochodem | Tryb podróży dla trasy. W tym przykładzie użyto tej wartości "Prawa jazdy." | 
      | **Transport publiczny — data i godzina** | Brak | Dotyczy tylko tryb przesyłania. | 
      | **Typ Data Type przesyłania** | Brak | Dotyczy tylko tryb przesyłania. | 
      ||||  

1. [Dodaj warunek](../logic-apps/logic-apps-control-flow-conditional-statement.md) który sprawdza, czy aktualny czas podróży z ruchem przekroczy określony czas. 
   W tym przykładzie wykonaj następujące kroki:

   1. Zmień nazwę warunku na następujący opis: **Jeśli natężenie ruchu sieciowego jest większy niż określony czas**

   1. W skrajnej lewej kolumnie kliknij wewnątrz **wybierz wartość** polu, aby wyświetlić listę zawartości dynamicznej. Z tej listy, wybierz **podróży — natężenie ruchu czas trwania** pola, które znajduje się w ciągu kilku sekund. 

      ![Kompilowanie warunku](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. W środkowym polu Wybierz następujący operator: **jest większa niż**

   1. W kolumnie po prawej stronie wprowadź tej wartości porównania, która znajduje się w ciągu kilku sekund i równoważne do 10 minut: **600**

      Gdy wszystko będzie gotowe, warunek będzie wyglądać następująco:

      ![Gotowy warunek](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. W **w przypadku opcji true** gałęzi, Dodaj akcję "Wyślij wiadomość e-mail" dla dostawcy poczty e-mail. 
   Skonfiguruj tę akcję, wykonując kroki pod tą ilustracją:

   ![Dodaj akcję "Wyślij wiadomość e-mail", "w przypadku opcji true" gałęzi](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. W **do** wprowadź swój adres e-mail do celów testowych.

   1. W **podmiotu** wprowadź następujący tekst:

      ```Time to leave: Traffic more than 10 minutes```

   1. W **treści** wprowadź ten tekst ze spacją końcową: 

      ```Travel time:```

      Gdy kursor jest wyświetlana w **treści** pola listy zawartości dynamicznej pozostanie otwarte, w którym można wybrać żadnych parametrów, które są dostępne w tym momencie.

   1. Na liście zawartości dynamicznej wybierz pozycję **Wyrażenie**.

   1. Znajdź i zaznacz **div()** funkcji. 
      Umieść kursor w wewnątrz nawiasów funkcji.

   1. Gdy kursor znajduje się wewnątrz nawiasów funkcji, wybierz **zawartości dynamicznej** tak, aby wyświetlić listę zawartości dynamicznej. 
   
   1. Z **Pobierz trasę** zaznacz **ruchu czas ruchu** pola.

      ![Wybierz pozycję "Ruchu czas ruchu"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Po umieszczeniu pola do formatu JSON, Dodaj **przecinkami** (```,```) i numer ```60``` tak, aby przekonwertować wartość **ruchu czas ruchu** z sekund na minuty. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Teraz wyrażenie będzie wyglądać następująco:

      ![Zakończenie wyrażenia](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   <!-- markdownlint-disable MD038 -->
   1. Po wyrażeniu jest rozpoznawany jako, Dodaj następujący tekst ze spacją: ``` minutes```
  
       Twoje **treści** pole wygląda teraz następująco:

       ![Zakończono pole "Treść"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Zapisz aplikację logiki.

Następnie dodaj zakresu, dzięki czemu można grupować konkretne akcje i oceniają swój stan.

## <a name="add-a-scope"></a>Dodaj zakres

1. Jeśli jeszcze nie, należy otworzyć aplikację logiki w Projektancie aplikacji logiki. 

1. Dodaj zakres lokalizacji przepływu pracy, który ma. Na przykład aby dodać zakresu między krokami istniejących w przepływ pracy aplikacji logiki, wykonaj następujące kroki: 

   1. Przesuń wskaźnik myszy strzałkę, w której chcesz dodać zakresu. 
   Wybierz **znak plus** (**+**) > **Dodaj akcję**.

      ![Dodaj zakres](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. W polu wyszukiwania wprowadź "scope" jako filtr. 
   Wybierz **zakres** akcji.

## <a name="add-steps-to-scope"></a>Dodaj kroki do zakresu

1. Teraz dodać kroki lub przeciągnij istniejące czynności, które mają być uruchamiane w zakresie. W tym przykładzie przeciągnij te akcje w zakresie:
      
   * **Pobierz trasę**
   * **Jeśli natężenie ruchu sieciowego jest większy niż określony czas**, która obejmuje zarówno **true** i **false** gałęzi

   Twoja aplikacja logiki wygląda teraz następująco:

   ![Dodano zakres](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. W ramach zakresu Dodaj warunek, który umożliwia sprawdzenie stanu zakresu. Zmień nazwę warunku na następujący opis: **Jeśli zakres nie powiodła się.**

   ![Dodaj warunek do sprawdzenia stanu zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. W tym stanie należy dodać tych wyrażeń, które sprawdzają, czy stan zakresu jest równa "Niepowodzenie" lub "Aborted". 

   1. Aby dodać kolejny wiersz, wybierz **Dodaj**. 

   1. W każdym wierszu kliknij w polu po lewej stronie, aby wyświetlić listę zawartości dynamicznej. 
   Z listy zawartości dynamicznej wybierz **wyrażenie**. W polu edycji, wprowadź następujące wyrażenie, a następnie wybierz **OK**: 
   
      `result('Scope')[0]['status']`

      ![Dodaj wyrażenie, który umożliwia sprawdzenie stanu zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Dla obu wierszy wybierz **jest równa** jako operatora. 
   
   1. Dla wartości porównania, w pierwszym wierszu wprowadź `Failed`. 
   W drugim wierszu wprowadź `Aborted`. 

      Gdy wszystko będzie gotowe, warunek będzie wyglądać następująco:

      ![Dodaj wyrażenie, który umożliwia sprawdzenie stanu zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Teraz, ustawienie dla warunku `runAfter` właściwości, więc warunek sprawdza stan zakresu i uruchamia akcję dopasowania, które definiujesz w kolejnych krokach.

   1. Na **Jeśli zakres nie powiodła się** warunku, wybierz polecenie **wielokropka** (...) przycisk, a następnie wybierz **konfigurowanie uruchamiania**.

      ![Skonfiguruj właściwość "runAfter"](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Wybierz te stany zakresu: **zakończy się pomyślnie**, **nie powiodło się**, **jest pomijana**, i **przekroczyło limit czasu**

      ![Wybierz zakres stanów](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Po zakończeniu wybierz pozycję **Gotowe**. 
   Warunek zawiera teraz ikonę "informacje".

1. W **w przypadku opcji true** i **w przypadku wartości FAŁSZ** gałęzie, dodawać akcje, które mają być wykonywane na podstawie stanu każdego zakresu, na przykład, Wyślij wiadomość e-mail lub wiadomości.

   ![Dodawanie akcji do wykonania na podstawie stanu zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Zapisz aplikację logiki.

Twoja aplikacja logiki Zakończono teraz wyglądać następująco:

![Ukończona aplikacja logiki z zakresem](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Testowanie pracy

Na pasku narzędzi Projektanta wybierz **Uruchom**. Jeśli wszystkie akcje o określonym zakresie powiedzie się, pojawi się komunikat "Zakres zakończyło się pomyślnie.". Jeśli wszystkie akcje w zakresie nie powiedzie się, pojawi się komunikat "Zakresu nie powiodło się.". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definicji JSON

Jeśli pracujesz w widoku kodu, można zdefiniować zakres struktury w definicji JSON aplikacji logiki zamiast tego. Na przykład Oto definicja JSON wyzwalacza i akcji w poprzedniej aplikacji logiki:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
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
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
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
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
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
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
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
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
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
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać lub głosować na funkcje i sugestie, odwiedź [witrynie opinii użytkowników usługi Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Wykonaj kroki na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Wykonaj kroki na podstawie różnych wartości (instrukcji switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchom i wykonaj ponownie kroki (pętli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchom lub scalania równoległymi krokami (gałęzie)](../logic-apps/logic-apps-control-flow-branches.md)
