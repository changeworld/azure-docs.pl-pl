---
title: Grupowanie i uruchamianie działań według zakresu
description: Tworzenie akcji o określonym zakresie, które są uruchamiane na podstawie stanu grupy w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: b84db69f79b1611347a4c55d929e5426141e7ac6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74791491"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Uruchamianie akcji na podstawie stanu grupy przy użyciu zakresów w usłudze Azure Logic Apps

Aby uruchamiać akcje tylko wtedy, gdy inna grupa akcji zakończy się powodzeniem lub niepowodzeniem, pogrupuj te akcje wewnątrz *zakresu*. Ta struktura jest przydatna, gdy chcesz zorganizować akcje jako grupę logiczną, ocenić stan tej grupy i wykonać akcje, które są oparte na stanie zakresu. Po zakończeniu wszystkich akcji w zakresie, zakres również pobiera swój własny stan. Na przykład można użyć zakresów, gdy chcesz zaimplementować [obsługę wyjątków i błędów](../logic-apps/logic-apps-exception-handling.md#scopes). 

Aby sprawdzić stan zakresu, można użyć tych samych kryteriów, których używasz do określenia stanu uruchamiania aplikacji logiki, takich jak "Powodem", "Nie powiodło się", "Anulowane" i tak dalej. Domyślnie, gdy wszystkie działania zakresu zakończy się pomyślnie, stan zakresu jest oznaczony jako "Powodem". Ale gdy jakakolwiek akcja w zakresie zakończy się niepowodzeniem lub zostanie anulowana, stan zakresu jest oznaczony jako "Nie powiodło się". Aby uzyskać limity dotyczące zakresów, zobacz [Limity i config](../logic-apps/logic-apps-limits-and-config.md). 

Na przykład w tym miejscu jest aplikacja logiki wysokiego poziomu, który używa zakresu do uruchamiania określonych akcji i warunek, aby sprawdzić stan zakresu. Jeśli wszelkie akcje w zakresie nie powiedzie się lub zakończyć nieoczekiwanie, zakres jest oznaczony jako "Failed" lub "Aborted" odpowiednio, a aplikacja logiki wysyła komunikat "Zakres nie powiodło się". Jeśli wszystkie akcje o określonym zakresie powiedzą się, aplikacja logiki wysyła komunikat "Zakres powiódł się".

![Konfigurowanie wyzwalacza "Harmonogram — cykl"](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby postępować zgodnie z przykładem w tym artykule, potrzebujesz tych elementów:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Konto e-mail od dowolnego dostawcy poczty e-mail obsługiwane przez aplikacje logiki. W tym przykładzie użyto Outlook.com. Jeśli używasz innego dostawcy, ogólny przepływ pozostaje taki sam, ale interfejs użytkownika wygląda inaczej.

* Klucz mapy Bing. Aby uzyskać ten klucz, zobacz <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Pobierz klucz Mapy Bing</a>.

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Tworzenie przykładowej aplikacji logiki

Najpierw utwórz tę przykładową aplikację logiki, aby dodać zakres później:

![Tworzenie przykładowej aplikacji logiki](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* A **Schedule - Wyzwalacz cyklu,** który sprawdza usługę Mapy Bing w określonym interwale
* **Mapy Bing — pobierz** akcję trasy, która sprawdza czas podróży między dwiema lokalizacjami
* Oświadczenie warunkowe, które sprawdza, czy czas podróży przekracza określony czas podróży
* Akcja, która wysyła wiadomość e-mail, że bieżący czas podróży przekracza określony czas

Możesz zapisać aplikację logiki w dowolnym momencie, więc często zapisuj swoją pracę.

1. Zaloguj się do <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a>, jeśli jeszcze tego nie zrobiłeś. Tworzenia pustej aplikacji logiki.

1. Dodaj wyzwalacz **harmonogramu - cyklu** z tymi ustawieniami: **Interwał** = "1" i **Częstotliwość** = "Minuta"

   ![Konfigurowanie wyzwalacza "Harmonogram — cykl"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Aby wizualnie uprościć widok i ukryć szczegóły każdej akcji w projektancie, zwiń kształt każdej akcji w miarę postępów w tych krokach.

1. Dodaj **akcję Mapy Bing — pobierz trasę.** 

   1. Jeśli nie masz jeszcze połączenia mapy Bing Maps, zostaniesz poproszony o utworzenie połączenia.

      | Ustawienie | Wartość | Opis |
      | ------- | ----- | ----------- |
      | **Nazwa połączenia** | BingMapsConnection | Podaj nazwę połączenia. | 
      | **Klucz INTERFEJSU API** | <*twój-Bing-Mapy-klucz*> | Wprowadź uzyskany wcześniej klucz usługi Mapy Bing. | 
      ||||  

   1. Skonfiguruj akcję **Pobierz trasę,** jak pokazano w tabeli pod tym obrazem:

      ![Konfigurowanie akcji "Mapy Bing - Pobierz trasę"](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Aby uzyskać więcej informacji na temat tych parametrów, zobacz [Calculate route (Obliczanie trasy)](https://msdn.microsoft.com/library/ff701717.aspx).

      | Ustawienie | Wartość | Opis |
      | ------- | ----- | ----------- |
      | **Punkt nawigacyjny 1** | <*Uruchomić*> | Wprowadź początek trasy. | 
      | **Punkt nawigacyjny 2** | <*Końcu*> | Wprowadź miejsce docelowe trasy. | 
      | **Unikaj** | Brak | Wprowadź elementy, których należy unikać na trasie, takie jak autostrady, opłaty drogowe itd. Aby uzyskać możliwe wartości, zobacz [Obliczanie trasy](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Optymalizacji** | timeWithTraffic | Wybierz parametr, aby zoptymalizować trasę, na przykład odległość, czas z bieżącymi informacjami o ruchu drogowym i tak dalej. W tym przykładzie użyto tej wartości: "timeWithTraffic" | 
      | **Jednostka odległości** | <*twoje preferencje*> | Wprowadź jednostkę odległości, aby obliczyć trasę. W tym przykładzie użyto tej wartości: "Mile" | 
      | **Tryb podróży** | Jazda samochodem | Wprowadź tryb podróży dla trasy. W tym przykładzie użyto tej wartości "Jazda" | 
      | **Transport publiczny — data i godzina** | Brak | Dotyczy tylko trybu tranzytowego. | 
      | **Typ daty tranzytu** | Brak | Dotyczy tylko trybu tranzytowego. | 
      ||||  

1. [Dodaj warunek,](../logic-apps/logic-apps-control-flow-conditional-statement.md) który sprawdza, czy bieżący czas podróży z ruchem przekracza określony czas. 
   W tym przykładzie wykonaj następujące kroki:

   1. Zmień nazwę warunku o ten opis: **Jeśli czas ruchu jest większy niż określony czas**

   1. W kolumnie po lewej stronie kliknij wewnątrz pola **Wybierz wartość,** aby wyświetlić dynamiczną listę zawartości. Z tej listy wybierz pole **Ruch czasowy podróży,** które znajduje się w sekundach. 

      ![Kompilowanie warunku](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. W polu środkowym wybierz ten operator: **jest większy niż**

   1. W prawej kolumnie wprowadź tę wartość porównania, która jest w sekundach i odpowiada 10 minutom: **600**

      Gdy wszystko będzie gotowe, warunek będzie wyglądać następująco:

      ![Stan gotowy](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. W gałęzi **Jeśli prawdziwa** dodaj akcję "Wyślij wiadomość e-mail" dla swojego dostawcy poczty e-mail. 
   Skonfiguruj tę akcję, wykonując czynności opisane w tym obrazie:

   ![Dodaj akcję "Wyślij wiadomość e-mail" do gałęzi "Jeśli jest prawdziwa"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. W polu **Do** wprowadź swój adres e-mail do celów testowych.

   1. W polu **Temat** wprowadź ten tekst:

      ```Time to leave: Traffic more than 10 minutes```

   1. W polu **Treść** wprowadź ten tekst z spacją końcową: 

      ```Travel time:```

      Gdy kursor pojawia się w polu **Treść,** dynamiczna lista zawartości pozostaje otwarta, dzięki czemu można wybrać dowolne parametry, które są dostępne w tym momencie.

   1. Na liście zawartości dynamicznej wybierz pozycję **Wyrażenie**.

   1. Znajdź i wybierz funkcję **div().** 
      Umieść kursor wewnątrz nawiasów funkcji.

   1. Gdy kursor znajduje się w nawiasach funkcji, wybierz pozycję **Zawartość dynamiczna,** aby wyświetlić dynamiczną listę zawartości. 
   
   1. W sekcji **Uzyskaj trasę** wybierz pole **Ruch czasowy.**

      ![Wybierz "Ruch na czas trwania ruchu"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Po rozwiązaniu pola do formatu JSON```,```dodaj **przecinek** ```60``` ( ), po którym następuje liczba, aby przekonwertować wartość w **ruchu czas trwania ruchu** z sekund na minuty. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Wyrażenie wygląda teraz następująco:

      ![Wyrażenie Zakończ](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   <!-- markdownlint-disable MD038 -->
   1. Po zakończeniu rozpoznawania wyrażenia dodaj ten tekst z interwnianą przestrzenią:``` minutes```
  
       Twoje pole **Ciało** wygląda teraz następująco:

       ![Gotowe pole "Ciało"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Zapisz aplikację logiki.

Następnie dodaj zakres, aby można było grupować określone akcje i oceniać ich stan.

## <a name="add-a-scope"></a>Dodawanie zakresu

1. Jeśli jeszcze tego nie zrobiłeś, otwórz aplikację logiki w logice App Designer. 

1. Dodaj zakres w żądanej lokalizacji przepływu pracy. Na przykład, aby dodać zakres między istniejącymi krokami w przepływie pracy aplikacji logiki, wykonaj następujące kroki: 

   1. Przesuń wskaźnik myszy na strzałkę, w której chcesz dodać zakres. 
   Wybierz **znak** plus**+**( ) > **Dodaj akcję**.

      ![Dodawanie zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. W polu wyszukiwania wpisz "zakres" jako filtr. 
   Wybierz akcję **Zakres.**

## <a name="add-steps-to-scope"></a>Dodawanie kroków do zakresu

1. Teraz dodaj kroki lub przeciągnij istniejące kroki, które chcesz uruchomić wewnątrz zakresu. W tym przykładzie przeciągnij te akcje do zakresu:
      
   * **Pobierz trasę**
   * **Jeśli czas ruchu jest większy niż określony czas,** który obejmuje zarówno **prawdziwe,** jak i **fałszywe** gałęzie

   Aplikacja logiki wygląda teraz następująco:

   ![Dodano zakres](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. W ramach zakresu dodaj warunek, który sprawdza stan zakresu. Zmień nazwę warunku z tym opisem: **Jeśli zakres nie powiódł się**

   ![Dodaj warunek, aby sprawdzić stan zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. W warunku dodaj te wyrażenia, które sprawdzają, czy stan zakresu jest równy "Nie powiodło się" lub "Przerwane". 

   1. Aby dodać kolejny wiersz, wybierz pozycję **Dodaj**. 

   1. W każdym wierszu kliknij wewnątrz lewego pola, aby wyświetlić dynamiczną listę zawartości. 
   Z dynamicznej listy zawartości wybierz pozycję **Wyrażenie**. W polu edycji wprowadź to wyrażenie, a następnie wybierz przycisk **OK**: 
   
      `result('Scope')[0]['status']`

      ![Dodaj wyrażenie, które sprawdza stan zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Dla obu wierszy wybierz **jest równa** jako operator. 
   
   1. Dla wartości porównawczych w pierwszym `Failed`wierszu wprowadź . 
   W drugim wierszu `Aborted`wprowadź . 

      Gdy wszystko będzie gotowe, warunek będzie wyglądać następująco:

      ![Dodaj wyrażenie, które sprawdza stan zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Teraz ustaw `runAfter` właściwość warunku, aby warunek sprawdzał stan zakresu i uruchamia pasujące działania, które można zdefiniować w późniejszych krokach.

   1. W warunku **Jeśli zakres nie powiodło się,** wybierz przycisk **wielokropka** (...) , a następnie wybierz pozycję **Konfiguruj uruchamianie po**.

      ![Konfigurowanie właściwości "runAfter"](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Wybierz wszystkie te stany zakresu: **zakończy się pomyślnie,** **nie powiodło się,** **jest pomijany**i **ma limit czasu**

      ![Wybieranie stanów zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Po zakończeniu wybierz pozycję **Gotowe**. 
   Warunek pokazuje teraz ikonę "informacje".

1. W **jeśli true** i **Jeśli fałszywe** gałęzie, dodaj akcje, które chcesz wykonać na podstawie każdego stanu zakresu, na przykład wysłać wiadomość e-mail lub wiadomość.

   ![Dodawanie akcji do podjęcia na podstawie stanu zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Zapisz aplikację logiki.

Twoja ukończona aplikacja logiki wygląda teraz następująco:

![Ukończona aplikacja logiczna z zakresem](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Przetestuj swoją pracę

Na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Jeśli wszystkie akcje o określonym zakresie powiedzą się, zostanie wyświetlony komunikat "Zakres zakończył się pomyślnie". Jeśli akcje o określonym zakresie nie powiedzie się, zostanie wyświetlony komunikat "Zakres nie powiodło się". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definicja JSON

Jeśli pracujesz w widoku kodu, można zdefiniować strukturę zakresu w definicji JSON aplikacji logiki zamiast. Na przykład oto definicja JSON dla wyzwalacza i akcji w poprzedniej aplikacji logiki:

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
* Aby przesłać lub zagłosować na funkcje i sugestie, odwiedź [witrynę opinii użytkowników usługi Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie kroków na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Uruchamianie kroków na podstawie różnych wartości (instrukcje switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchamianie i powtarzanie kroków (pętli)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchamianie lub scalanie równoległych kroków (gałęzi)](../logic-apps/logic-apps-control-flow-branches.md)
