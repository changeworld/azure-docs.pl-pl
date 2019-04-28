---
title: Tworzenie zmiennych do zapisywania wartości — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Jak zapisać i zarządzać nimi wartości, tworząc zmiennych w usłudze Azure Logic Apps
services: logic-apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: e525e5584e4835b0f2b73203c818c3f799b77cf5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61004588"
---
# <a name="create-variables-for-saving-and-managing-values-in-azure-logic-apps"></a>Utwórz zmienne, zapisywania i zarządzaniu wartości w usłudze Azure Logic Apps

W tym artykule przedstawiono sposób przechowywania i pracować z wartościami w całej aplikacji logiki przez utworzenie zmiennych. Na przykład zmienne mogą pomóc liczbę przypadków, kiedy pętla zostanie uruchomiona. Po Iterowanie tablicy lub tablicy dla określonego elementu sprawdzanie, aby odwołać numer indeksu dla każdego elementu tablicy można użyć zmiennej. 

Można utworzyć zmienne dla typów danych, takich jak liczba całkowita, liczba zmiennoprzecinkowa, atrybut typu wartość logiczna, ciąg, tablicy i obiektu. Po utworzeniu zmiennej, można wykonywać inne zadania, na przykład:

* Uzyskaj lub odwoływać się wartość zmiennej.
* Zwiększanie lub zmniejszanie zmiennej przez wartość stałą, znany także jako *przyrostu* i *dekrementacji*.
* Przypisz inną wartość do zmiennej.
* Wstaw lub *Dołącz* wartość zmiennej jako ostatnia razem w ciągu lub tablicy.

Zmienne istnieją i są globalne tylko w ramach wystąpienie aplikacji logiki, która je tworzy. Ponadto utrzymują się między wszystkie iteracje pętli zostaną wewnątrz wystąpienie aplikacji logiki. Podczas odwoływania się do zmiennej, należy użyć nazwy zmiennej jako token, a nie nazwy akcji, która jest w zwykły sposób, aby odwoływać się do danych wyjściowych akcji. 

> [!IMPORTANT]
> Domyślnie cykle w pętli "Foreach" Uruchom równolegle. W przypadku używania zmiennych w pętlach uruchomić pętlę [sekwencyjnie](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) tak zwracają przewidywalne wartości zmiennych. 

Jeśli nie masz jeszcze subskrypcji platformy Azure <a href="https://azure.microsoft.com/free/" target="_blank">Załóż bezpłatne konto platformy Azure</a>. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego artykułu, Oto elementy, które są potrzebne:

* Aplikacja logiki, w którym chcesz utworzyć zmienną 

  Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako pierwszy krok w aplikacji logiki 

  Przed dodaniem akcje w przypadku tworzenia i pracy ze zmiennymi aplikacja logiki musi rozpoczynać wyzwalacza.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Inicjuj zmienną

Można utworzyć zmienną i zadeklarować jego typu danych i wartość początkową — wszystko w ramach jednej akcji w aplikacji logiki. Można zadeklarować tylko zmienne na poziomie globalnym w zakresach, warunków i pętli. 

1. W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a> lub Visual Studio, Otwórz aplikację logiki w Projektancie aplikacji logiki. 

   W tym przykładzie za pomocą witryny Azure portal i aplikacja logiki istniejącego wyzwalacza.

2. W aplikacji logiki, w ramach kroku, w której chcesz dodać zmienną wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz **nowy krok** > **Dodaj akcję**.

     ![Dodaj akcję](./media/logic-apps-create-variables-store-values/add-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy strzałkę łączącego się więc pojawia się znak plusa (+). 
   Wybierz znak plus, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "zmienne" jako filtr. Wybierz z listy akcji **zmienne — inicjowane zmiennej**.

   ![Wybierz akcję](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

4. Podaj te informacje w zmiennej:

   | Właściwość | Wymagany | Value |  Opis |
   |----------|----------|-------|--------------|
   | Name | Yes | <*Nazwa zmiennej*> | Nazwa zmiennej zwiększyć | 
   | Type | Yes | <*Typ zmiennej*> | Typ danych dla zmiennej | 
   | Value | Nie | <*Wartość początkowa*> | Początkowa wartość zmiennej <p><p>**Porada**: Mimo, że jest to opcjonalne, ustaw tę wartość zgodnie z zaleceniami, dzięki czemu zawsze będzie wiadomo, wartość początkową zmiennej. | 
   ||||| 

   ![Inicjuj zmienną](./media/logic-apps-create-variables-store-values/initialize-variable.png)

5. Teraz kontynuować dodawanie akcji, które chcesz. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**.

Jeśli przełącznik przy użyciu projektanta widoku edytora kodu poniżej przedstawiono sposób **inicjowane zmiennej** akcji pojawia się wewnątrz definicji aplikacji logiki, która jest w formacie JavaScript Object Notation (JSON):

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

Poniżej przedstawiono przykłady dla innych typów zmiennych:

*Zmienna String*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*Wartość logiczna*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*Tablica z liczb całkowitych*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*Tablica z ciągami*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>Pobierz wartość zmiennej

Można pobrać lub odwoływać się do zmiennej na zawartości, można również użyć [funkcja variables()](../logic-apps/workflow-definition-language-functions-reference.md#variables) w Projektancie aplikacji logiki i Edytor widoku kodu.
Podczas odwoływania się do zmiennej, należy użyć nazwy zmiennej jako token, a nie nazwy akcji, która jest w zwykły sposób, aby odwoływać się do danych wyjściowych akcji. 

Na przykład, wyrażenie pobiera elementy z zmiennej tablicowej [utworzone wcześniej w tym artykule](#append-value) przy użyciu **variables()** funkcji. **String()** funkcja zwraca zawartość zmiennej w formacie ciągu: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Zwiększ wartość zmiennej 

Aby zwiększyć lub *przyrostu* zmiennej przez wartość stałą, Dodaj **zmienne — Zwiększ wartość zmiennej** akcji aplikacji logiki. Ta akcja działa tylko w przypadku liczb całkowitych i zmiennoprzecinkowych zmiennych.

1. W Projektancie aplikacji logiki w ramach kroku, w której chcesz zwiększyć istniejącą zmienną, wybierz **nowy krok** > **Dodaj akcję**. 

   Na przykład ta aplikacja logiki już wyzwalacz i akcja, która utworzyć zmienną. Tak Dodaj nową akcję w obszarze następujące kroki:

   ![Dodaj akcję](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Aby dodać akcję między krokami istniejących, przesuń wskaźnik myszy strzałkę łączącego się tak, aby pojawia się znak plusa (+). Wybierz znak plus, a następnie wybierz **Dodaj akcję**.

2. W polu wyszukiwania wprowadź "Zwiększ wartość zmiennej" jako filtr. Z listy akcji wybierz **zmienne — Zwiększ wartość zmiennej**.

   ![Wybierz akcję "Zwiększ wartość zmiennej"](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

3. Podaj te informacje w zmiennej przyrostu o wartości:

   | Właściwość | Wymagany | Value |  Opis |
   |----------|----------|-------|--------------|
   | Name | Yes | <*Nazwa zmiennej*> | Nazwa zmiennej zwiększyć | 
   | Value | Nie | <*increment-value*> | Wartość przyrostu o wartości zmiennej. Wartość domyślna to jeden. <p><p>**Porada**: Mimo że jest to opcjonalne, ta wartość zgodnie z zaleceniami, dzięki czemu zawsze będzie wiadomo, określona wartość przyrostu o wartości do zmiennej. | 
   |||| 

   Na przykład: 
   
   ![Przykład wartości przyrostu](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

4. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**. 

Jeśli przełącznik przy użyciu projektanta widoku edytora kodu poniżej przedstawiono sposób **Zwiększ wartość zmiennej** akcji pojawia się wewnątrz definicji aplikacji logiki, która jest w formacie JSON:

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>Przykład: Utwórz Licznik pętli

Zmienne są często używane do zliczania liczby, które jest uruchamiane w pętli. Ten przykład pokazuje, jak utworzyć i używać zmiennych dla tego zadania, tworząc pętli, który zlicza załączników do wiadomości e-mail.

1. W witrynie Azure portal utworzyć pustej aplikacji logiki. Dodaj wyzwalacz, który sprawdza, czy nowy adres e-mail i załączniki. 

   W tym przykładzie użyto usługi Office 365 Outlook wyzwalacz **po nadejściu nowej wiadomości e-mail**. 
   Można ustawić tego wyzwalacza wyzwalać tylko wtedy, gdy wiadomość e-mail ma załączniki.
   Jednak można użyć dowolnego łącznik, który sprawdza, czy dla nowych wiadomości e-mail z załącznikami, takich jak łącznik usługi Outlook.com.

2. W wyzwalaczu, wybierz **Pokaż opcje zaawansowane**. Aby wyzwalacz sprawdzania załączników i przekazać te załączniki do przepływu pracy aplikacji logiki, wybierz **tak** tych właściwości:
   
   * **Zawiera załącznik** 
   * **Uwzględnij załączniki** 

   ![Sprawdź i Uwzględnij załączniki](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

3. Dodaj [ **inicjowane zmiennej** akcji](#create-variable). Utwórz zmienną całkowitą o nazwie **liczba** cyfrą zero: wartość początkowa.

   ![Dodawanie akcji dla "Inicjowane zmiennej"](./media/logic-apps-create-variables-store-values/initialize-variable.png)

4. Aby przechodzić między każdego załącznika, należy dodać *dla każdego* pętli, wybierając **nowy krok** > **więcej** > **Dodaj dla każdego**.

   ![Dodaj pętlę "for each"](./media/logic-apps-create-variables-store-values/add-loop.png)

5. W pętli, kliknij wewnątrz **wybierz dane wyjściowe z poprzednich kroków** pole. Po wyświetleniu listy zawartości dynamicznej wybierz **załączniki**. 

   ![Wybieranie pozycji „Załączniki”](./media/logic-apps-create-variables-store-values/select-attachments.png)

   **Załączniki** pola przekazuje tablicę, która ma załączniki wiadomości e-mail z danych wyjściowych wyzwalacza w pętlę metodyki.

6. W pętli "for each" Wybierz **Dodaj akcję**. 

   ![Wybierz pozycję "Dodaj akcję"](./media/logic-apps-create-variables-store-values/add-action-2.png)

7. W polu wyszukiwania wprowadź "Zwiększ wartość zmiennej" jako filtr. Wybierz z listy akcji **zmienne — Zwiększ wartość zmiennej**.

   > [!NOTE]
   > Upewnij się, że **Zwiększ wartość zmiennej** akcji pojawia się wewnątrz pętli. Jeśli akcja pojawia się poza pętlę, przeciągnij akcji do pętli.

8. W **Zwiększ wartość zmiennej** akcji z **nazwa** listy wybierz **liczba** zmiennej. 

   ![Wybierz zmienną "Liczba"](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

9. W obszarze pętli Dodaj dowolną akcję, która wyśle do Ciebie liczba załączników. W akcji, zawierają wartości z **liczba** zmiennej, na przykład: 

   ![Dodaj akcję, która wysyła wyniki](./media/logic-apps-create-variables-store-values/send-email-results.png)

10. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. 

### <a name="test-your-logic-app"></a>Przetestuj aplikację logiki

1. Jeśli Twoja aplikacja logiki nie jest włączone, w menu aplikacji logiki, wybierz opcję **Przegląd**. Na pasku narzędzi wybierz **Włącz**. 

2. Na pasku narzędzi Projektanta aplikacji logiki wybierz **Uruchom**. Ten krok spowoduje ręczne uruchomienie aplikacji logiki.

3. Wyślij wiadomość e-mail z załącznikami co najmniej jeden z kontem e-mail, którego użyto w tym przykładzie.

   Ten krok jest uruchamiany wyzwalacz aplikacji logiki, które tworzy i uruchamia wystąpienie przepływu pracy aplikacji logiki.
   W rezultacie aplikacja logiki wyśle do Ciebie wiadomości lub wiadomości e-mail, która pokazuje liczbę załączników do wiadomości e-mail wysłanej.

Jeśli przełącznik przy użyciu projektanta widoku edytora kodu poniżej przedstawiono sposób pętli "for each" pojawia się z **Zwiększ wartość zmiennej** akcji wewnątrz definicji aplikacji logiki, która jest w formacie JSON.

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>Zmniejsz zmienną

Aby zmniejszyć lub *dekrementacji* zmiennej przez wartość stałą, postępuj zgodnie z instrukcjami, aby uzyskać [zwiększenie zmienną](#increment-value) z tą różnicą, że możesz znaleźć i wybrać **zmienne — Zmniejsz zmienną**akcji zamiast tego. Ta akcja działa tylko w przypadku liczb całkowitych i zmiennoprzecinkowych zmiennych.

Poniżej przedstawiono właściwości dla **Zmniejsz zmienną** akcji:

| Właściwość | Wymagany | Value |  Opis |
|----------|----------|-------|--------------|
| Name | Yes | <*Nazwa zmiennej*> | Nazwa zmiennej zmniejszyć | 
| Value | Nie | <*increment-value*> | Wartość dla zmiennej zmniejszanie. Wartość domyślna to jeden. <p><p>**Porada**: Mimo że jest to opcjonalne, ta wartość zgodnie z zaleceniami, dzięki czemu zawsze będzie wiadomo, określonej wartości dla zmniejszanie zmiennej. | 
||||| 

Jeśli przełącznik przy użyciu projektanta widoku edytora kodu poniżej przedstawiono sposób **Zmniejsz zmienną** akcji pojawia się wewnątrz definicji aplikacji logiki, która jest w formacie JSON.

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```


<a name="assign-value"></a>

## <a name="set-variable"></a>Ustaw zmienną

Aby przypisać inną wartość do istniejącej zmiennej, wykonaj kroki [zwiększenie zmienną](#increment-value) z wyjątkiem, że: 

1. Znajdź i zaznacz **zmienne — Ustawianie zmiennej** akcji zamiast tego. 

2. Podaj nazwę i wartość zmiennej, którą chcesz przypisać. Nowa wartość i zmienna musi mieć ten sam typ danych.
Wartość jest wymagana, ponieważ ta akcja nie ma wartości domyślnej. 

Poniżej przedstawiono właściwości dla **Ustaw zmienną** akcji:

| Właściwość | Wymagany | Value |  Opis | 
|----------|----------|-------|--------------| 
| Name | Yes | <*Nazwa zmiennej*> | Nazwa zmiennej zmienić | 
| Value | Yes | <*new-value*> | Wartość, którą chcesz przypisać do zmiennej. Oba muszą mieć ten sam typ danych. | 
||||| 

> [!NOTE]
> Jeśli nie jesteś zwiększanie lub zmniejszanie zmiennych, zmienianie zmiennych wewnątrz pętli *może* utworzyć nieoczekiwane wyniki, ponieważ pętle są uruchamiane równolegle i jednocześnie, domyślnie. W takich przypadkach spróbuj pętli w taki sposób, w celu uruchamiają się po kolei. Na przykład odwoływać się do wartości zmiennej wewnątrz pętli i oczekują tę samą wartość na początku i końca tego wystąpienia pętli, wykonaj następujące kroki, aby zmienić, jak działa Pętla: 
>
> 1. W pętlę metodyki prawym górnym rogu, wybierz przycisk wielokropka (...), a następnie wybierz **ustawienia**.
> 
> 2. W obszarze **kontroli współbieżności**, zmień **Zastąp domyślne** ustawienie **na**.
>
> 3. Przeciągnij **stopień równoległości** suwak, aby **1**.

Jeśli przełącznik przy użyciu projektanta widoku edytora kodu poniżej przedstawiono sposób **Ustaw zmienną** akcji pojawia się wewnątrz definicji aplikacji logiki, która jest w formacie JSON. W tym przykładzie zmienia bieżącą wartość zmiennej "Liczba" z inną wartością. 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>Dołącz do zmiennej

W przypadku zmiennych, które przechowują ciągi lub tablice można wstawić lub *Dołącz* wartość zmiennej jako ostatni element w tych ciągów lub tablic. Możesz wykonać kroki dla [zwiększenie zmienną](#increment-value) z tą różnicą, że zamiast tego wykonaj następujące kroki: 

1. Znajdź i wybierz jedno z tych działań na podstawie informacji o tego, czy Twoja Zmienna ciągu lub tablicy: 

   * **Zmienne — Dołącz do zmiennej ciągu**
   * **Zmienne — Dołącz do zmiennej tablicowej** 

2. Podaj wartość do dołączenia jako ostatni element w tablicy lub ciągu. 
   Ta wartość jest wymagana. 

Poniżej przedstawiono właściwości dla **dołączenia do...**  akcje:

| Właściwość | Wymagany | Value |  Opis | 
|----------|----------|-------|--------------| 
| Name | Yes | <*Nazwa zmiennej*> | Nazwa zmiennej zmienić | 
| Value | Yes | <*append-value*> | Wartość, którą chcesz dołączyć, i może mieć dowolny typ | 
|||||  

Jeśli przełącznik przy użyciu projektanta widoku edytora kodu poniżej przedstawiono sposób **Dołącz do zmiennej tablicowej** akcji pojawia się wewnątrz definicji aplikacji logiki, która jest w formacie JSON.
W tym przykładzie tworzy zmienną tablicową i dodaje inną wartość jako ostatni element w tablicy. Wynik Twojego jest zaktualizowane zmienna, która zawiera Ta tablica: `[1,2,3,"red"]` 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [łączników Logic Apps](../connectors/apis-list.md)
