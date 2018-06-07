---
title: Tworzenie zmiennych przy zapisywaniu wartości - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zapisz i zarządzaniu wartości przez tworzenie zmiennych w aplikacjach logiki platformy Azure
services: logic-apps
author: ecfan
manager: cfowler
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: cc464edf416a2b036d84e65e05810104d2706041
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655597"
---
# <a name="create-variables-for-saving-and-managing-values-in-azure-logic-apps"></a>Utwórz zmienne, zapisywania i zarządzanie wartości w aplikacjach logiki platformy Azure

W tym artykule przedstawiono sposób przechowywania i działać z wartościami w całej aplikacji logiki, tworząc zmiennych. Na przykład zmienne mogą pomóc ustalić liczbę razy, które są wykonywane w pętli. Iterowanie po tablicy lub Sprawdzanie tablicy dla określonego elementu, można korzystając zmienną do odwołania numer indeksu dla każdego elementu tablicy. 

Można tworzyć zmienne dla typów danych, takich jak liczba całkowita, float, boolean, string, tablicy i obiektu. Po utworzeniu zmiennej można wykonywać inne zadania, na przykład:

* Uzyskaj lub odwołać wartość zmiennej.
* Zwiększanie lub zmniejszanie zmiennej przez wartość stałą, znanej także jako *przyrostu* i *dekrementacji*.
* Przypisać inną wartość zmiennej.
* Wstawianie lub *Dołącz* wartość zmiennej jako czas ostatniego w string lub array.

Zmienne istnieją i są globalne tylko w ramach której zostały utworzone wystąpienie aplikacji logiki. Ponadto są zachowywane w dowolnym iteracji pętli wewnątrz wystąpienie aplikacji logiki. Podczas odwoływania się do zmiennej, należy użyć nazwy zmiennej jako token, a nie nazwy akcji, która jest w zwykły sposób, aby odwołać wyniki akcji.

Jeśli nie masz jeszcze subskrypcji platformy Azure <a href="https://azure.microsoft.com/free/" target="_blank">Załóż bezpłatne konto platformy Azure</a>. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć w tym artykule, Oto elementy, które są potrzebne:

* Aplikację logiki, w której chcesz utworzyć zmienną 

  Jeśli jesteś nowym użytkownikiem aplikacji logiki, przejrzyj [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako pierwszy krok w aplikacji logiki 

  Przed dodaniem akcje dotyczące tworzenia i Praca z zmienne aplikację logiki musi rozpoczynać się od wyzwalacza.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Inicjuj zmienną

Można utworzyć zmienną i zadeklarować jego typu danych i wartości początkowej — wszystko w ramach jednej akcji w aplikacji logiki. Można zadeklarować tylko zmienne na poziomie globalnym w zakresach, warunków i pętli. 

1. W <a href="https://portal.azure.com" target="_blank">portalu Azure</a> lub Visual Studio, Otwórz aplikację logiki w Projektancie aplikacji logiki. 

   W przykładzie użyto portalu Azure i aplikacji logiki z istniejącego wyzwalacza.

2. W aplikacji logiki, w kroku, w której chcesz dodać zmienną wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz **nowy krok** > **Dodaj akcję**.

     ![Dodaj akcję](./media/logic-apps-create-variables-store-values/add-action.png)

   * Aby dodać akcję między krokami, myszą łączącego strzałkę, pojawi się znak plus (+). 
   Wybierz znak plus, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "zmiennych" jako filtr. Wybierz z listy akcji **zmienne - zainicjować zmiennej**.

   ![Wybierz akcję](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

4. Przekaż te informacje dla zmiennej użytkownika:

   | Właściwość | Wymagane | Wartość |  Opis |
   |----------|----------|-------|--------------|
   | Name (Nazwa) | Yes | <*Nazwa zmiennej*> | Nazwa zmiennej zwiększyć | 
   | Typ | Yes | <*Typ zmiennej*> | Typ danych zmiennej | 
   | Wartość | Nie | <*Wartość początkowa*> | Początkowa wartość zmiennej <p><p>**Porada**: mimo że jest to opcjonalne, ustaw tę wartość jako najlepsze rozwiązanie, aby zawsze wiedzieć, wartość początkową dla zmiennej użytkownika. | 
   ||||| 

   ![Inicjuj zmienną](./media/logic-apps-create-variables-store-values/initialize-variable.png)

5. Teraz kontynuować dodawanie żądane akcje. Gdy wszystko będzie gotowe, na pasku narzędzi projektanta, wybierz pozycję **zapisać**.

Jeśli przy użyciu projektanta przełączyć do widoku edytora kodu, w tym miejscu jest sposób **zainicjować zmiennej** Akcja pojawia się wewnątrz definicję aplikacji logiki, która jest w formacie JavaScript Object Notation (JSON):

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

Poniżej przedstawiono przykłady dla zmiennej typów:

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

*Tablica z ciągów*

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

Można pobrać lub odwołać zawartość zmiennej, umożliwia także [funkcja variables()](../logic-apps/workflow-definition-language-functions-reference.md#variables) w Projektancie aplikacji logiki i Edytor widoku kodu.
Podczas odwoływania się do zmiennej, należy użyć nazwy zmiennej jako token, a nie nazwy akcji, która jest w zwykły sposób, aby odwołać wyniki akcji. 

Na przykład, wyrażenie pobiera elementy tablicy zmiennej [utworzonej wcześniej w tym artykule](#append-value) za pomocą **variables()** funkcji. **String()** funkcja zwraca zawartość zmiennej w formacie ciągu: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Zwiększ wartość zmiennej 

Aby zwiększyć lub *przyrostu* dodać zmienną przez wartość stałą **zmienne — zmienna przyrostu** akcji do aplikacji logiki. Ta akcja działa tylko z liczb całkowitych i zmiennoprzecinkowych zmiennych.

1. W Projektancie aplikacji logiki, w kroku, w którym chcesz zwiększyć istniejącą zmienną, wybierz **nowy krok** > **Dodaj akcję**. 

   Na przykład ta aplikacja logiki już wyzwalacz i utworzenia zmiennej akcji. Tak Dodaj nową akcję w obszarze następujące kroki:

   ![Dodaj akcję](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Aby dodać akcję między krokami istniejących, myszą łączącego strzałkę, aby pojawi się znak plus (+). Wybierz znak plus, a następnie wybierz **Dodaj akcję**.

2. W polu wyszukiwania wprowadź "increment zmiennej" jako filtr. Na liście akcji wybierz **zmienne — zmienna przyrostu**.

   ![Wybierz akcję "Increment zmiennej"](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

3. Podaj te informacje dotyczące wartości zmiennej użytkownika:

   | Właściwość | Wymagane | Wartość |  Opis |
   |----------|----------|-------|--------------|
   | Name (Nazwa) | Yes | <*Nazwa zmiennej*> | Nazwa zmiennej zwiększyć | 
   | Wartość | Nie | <*wartość przyrostu*> | Wartość używana dla wartości zmiennej. Domyślna wartość to jeden. <p><p>**Porada**: mimo że jest to opcjonalne, ustaw tę wartość jako najlepsze rozwiązanie, aby wiedzieć, zawsze określoną wartość dla wartości do zmiennej. | 
   |||| 

   Na przykład: 
   
   ![Przykład wartość przyrostu](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

4. Gdy wszystko będzie gotowe, na pasku narzędzi projektanta, wybierz pozycję **zapisać**. 

Jeśli przy użyciu projektanta przełączyć do widoku edytora kodu, w tym miejscu jest sposób **zmiennej przyrostu** Akcja pojawia się wewnątrz definicję aplikacji logiki, która jest w formacie JSON:

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

## <a name="example-create-loop-counter"></a>Przykład: Tworzenie licznika pętli

Zmienne są często używane dla obliczając, ile razy, które są wykonywane w pętli. Ten przykład przedstawia sposób tworzenia i używania zmiennych dla tego zadania przez utworzenie pętli, które zlicza załączników w wiadomości e-mail.

1. W portalu Azure należy utworzyć aplikację logiki puste. Dodaj wyzwalacz, który sprawdza, czy nowy adres e-mail i załączniki. 

   W tym przykładzie użyto wyzwalacza programu Outlook pakietu Office 365 dla **po odebraniu nowej wiadomości e-mail**. 
   Uruchomienie tylko wtedy, gdy wiadomość e-mail ma załączniki, można skonfigurować tego wyzwalacza.
   Jednak można użyć wszystkich łączników, która sprawdza nowych wiadomości e-mail z załącznikami, takich jak Outlook.com łącznika.

2. Wyzwalacz, wybierz **Pokaż zaawansowane opcje**. Aby wyzwalacz, sprawdź, czy załączniki, a następnie przekaż te załączniki do przepływu pracy aplikacji logiki, wybierz **tak** tych właściwości:
   
   * **Zawiera załącznik** 
   * **Uwzględnij załączniki** 

   ![Sprawdź, czy i załączniki](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

3. Dodaj [ **zainicjować zmiennej** akcji](#create-variable). Utwórz zmienną liczbą całkowitą o nazwie **liczba** z zerem wartość początkowa.

   ![Dodaj akcję "Inicjowanie zmiennej"](./media/logic-apps-create-variables-store-values/initialize-variable.png)

4. Aby przechodzić między każdym załącznika, Dodaj *dla każdego* pętli, wybierając **nowy krok** > **więcej** > **Dodaj dla każdego**.

   ![Dodawanie pętli "for each"](./media/logic-apps-create-variables-store-values/add-loop.png)

5. W pętli, kliknij wewnątrz **wybierz wyjścia z poprzednich kroków** pole. Gdy pojawi się lista zawartość dynamiczna, wybierz **załączników**. 

   ![Wybieranie pozycji „Załączniki”](./media/logic-apps-create-variables-store-values/select-attachments.png)

   **Załączników** pola przekazuje tablicę, która ma załączników wiadomości e-mail z danych wyjściowych tego wyzwalacza w Twojej pętli.

6. W pętli "for each" Wybierz **Dodaj akcję**. 

   ![Wybierz opcję "Dodaj akcję"](./media/logic-apps-create-variables-store-values/add-action-2.png)

7. W polu wyszukiwania wprowadź "increment zmiennej" jako filtr. Wybierz z listy akcji **zmienne — zmienna przyrostu**.

   > [!NOTE]
   > Upewnij się, że **zmiennej przyrostu** Akcja pojawia się wewnątrz pętli. Jeśli akcja pojawia się poza pętli, przeciągnij akcję w pętli.

8. W **zmiennej przyrostu** akcji, z **nazwa** listy, wybierz **liczba** zmiennej. 

   ![Wybierz zmienną "Count"](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

9. W obszarze pętli Dodaj dowolną akcję, która wysyła do Ciebie liczba załączników. W akcji, zawierają wartość z **liczba** zmiennej, na przykład: 

   ![Dodaj akcję wysyłającą wyników](./media/logic-apps-create-variables-store-values/send-email-results.png)

10. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. 

### <a name="test-your-logic-app"></a>Przetestuj aplikację logiki

1. Jeśli nie włączono aplikację logiki, w menu aplikacji logiki, wybierz **omówienie**. Na pasku narzędzi wybierz **włączyć**. 

2. Na pasku narzędzi Projektanta aplikacji logiki, wybierz **Uruchom**. Ten krok zostanie uruchomiony ręcznie aplikacji logiki.

3. Wyślij wiadomość e-mail z jednego lub więcej załączników do konta e-mail używanego w tym przykładzie.

   Ten krok generowane aplikacji logiki wyzwalacza, który tworzy i uruchamia wystąpienia przepływu pracy aplikacji logiki.
   W związku z tym aplikacji logiki wysyła wiadomości lub wiadomości e-mail, która jest wyświetlana liczba załączników do wiadomości e-mail wysłanej.

Po przełączeniu z projektanta do widoku edytora kodu poniżej przedstawiono sposób pętli "for each" występuje z **zmiennej przyrostu** akcji wewnątrz definicję aplikacji logiki, która jest w formacie JSON.

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

Aby zmniejszyć lub *dekrementacji* zmienną przez wartość stałą, wykonaj kroki [zwiększenie zmiennej](#increment-value) z tą różnicą, że należy znaleźć i wybrać **zmienne — zmienna dekrementacja**akcji zamiast tego. Ta akcja działa tylko z liczb całkowitych i zmiennoprzecinkowych zmiennych.

Poniżej przedstawiono właściwości **zmiennej dekrementacja** akcji:

| Właściwość | Wymagane | Wartość |  Opis |
|----------|----------|-------|--------------|
| Name (Nazwa) | Yes | <*Nazwa zmiennej*> | Nazwa zmiennej zmniejszyć | 
| Wartość | Nie | <*wartość przyrostu*> | Wartość dla zmiennej zmniejszeniu. Domyślna wartość to jeden. <p><p>**Porada**: mimo że jest to opcjonalne, ustaw tę wartość jako najlepsze rozwiązanie, aby zawsze wiedzieć, określona wartość dla zmniejszanie do zmiennej. | 
||||| 

Jeśli przy użyciu projektanta przełączyć do widoku edytora kodu, w tym miejscu jest sposób **zmiennej dekrementacja** Akcja pojawia się wewnątrz definicję aplikacji logiki, która jest w formacie JSON.

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

Aby przypisać inną wartość istniejącej zmiennej, wykonaj kroki [zwiększenie zmiennej](#increment-value) z wyjątkiem, że: 

1. Znajdź i zaznacz pozycję **zmienne — zmienna zestaw** akcji zamiast tego. 

2. Podaj nazwę zmiennej i wartość, którą chcesz przypisać. Zarówno nowa wartość, jak i zmiennej musi mieć taki sam typ danych.
Wartość jest wymagana, ponieważ ta akcja nie ma wartości domyślnej. 

Poniżej przedstawiono właściwości **zmiennej zestaw** akcji:

| Właściwość | Wymagane | Wartość |  Opis | 
|----------|----------|-------|--------------| 
| Name (Nazwa) | Yes | <*Nazwa zmiennej*> | Nazwa zmiennej do zmiany | 
| Wartość | Yes | <*Nowa wartość*> | Wartość ma zostać przypisany do zmiennej. Oba muszą mieć ten sam typ danych. | 
||||| 

> [!NOTE]
> Jeśli nie jesteś zwiększanie lub zmniejszanie zmiennych Zmienianie zmiennych wewnątrz pętli *może* utworzyć nieoczekiwane wyniki, ponieważ pętle są uruchamiane równolegle lub jednocześnie, domyślnie. Dla tych przypadków spróbuj z pętli są wykonywane sekwencyjnie. Na przykład jeśli chcesz odwołać wartość zmiennej w pętli i oczekują tej samej wartości na początku i końcu to wystąpienie pętli, wykonaj następujące kroki, aby zmienić sposób uruchamiania pętli: 
>
> 1. W Twojej pętli prawym górnym rogu, wybierz przycisk wielokropka (...), a następnie wybierz **ustawienia**.
> 
> 2. W obszarze **sterowania współbieżnością**, zmień **zastępują domyślne** ustawienie **na**.
>
> 3. Przeciągnij **stopnia równoległości** suwak do **1**.

Jeśli przy użyciu projektanta przełączyć do widoku edytora kodu, w tym miejscu jest sposób **zmiennej zestaw** Akcja pojawia się wewnątrz definicję aplikacji logiki, która jest w formacie JSON. W tym przykładzie zmienia bieżącą wartość zmiennej "Count" z inną wartością. 

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

Zmiennych, które przechowują ciągi lub tablice, Wstaw lub *Dołącz* wartość zmiennej jako ostatni element w tych ciągów lub tablic. Możesz wykonać kroki [zwiększenie zmiennej](#increment-value) z tą różnicą, że zamiast tego wykonaj następujące kroki: 

1. Znajdź i wybierz jedną z następujących czynności, zależnie od tego, czy do zmiennej ma ciąg lub tablica: 

  * **Zmienne - dołączyć do zmiennej ciągu**
  * **Zmienne - Dołącz do zmiennej tablicy** 

2. Podaj wartość, aby dołączyć jako ostatni element string lub array. Ta wartość jest wymagana. 

Poniżej przedstawiono właściwości **dołączyć do...**  akcje:

| Właściwość | Wymagane | Wartość |  Opis | 
|----------|----------|-------|--------------| 
| Name (Nazwa) | Yes | <*Nazwa zmiennej*> | Nazwa zmiennej do zmiany | 
| Wartość | Yes | <*Dołącz wartość*> | Wartość, którą chcesz dołączyć, i może mieć dowolny typ | 
|||||  

Jeśli przy użyciu projektanta przełączyć do widoku edytora kodu, w tym miejscu jest sposób **Dołącz do zmiennej tablicowej** Akcja pojawia się wewnątrz definicję aplikacji logiki, która jest w formacie JSON.
W tym przykładzie tworzy zmienną tablicową i dodaje innej wartości jako ostatni element w tablicy. Powstaje zaktualizowane zmienna, która zawiera tej tablicy: `[1,2,3,"red"]` 

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
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [łączniki Logic Apps](../connectors/apis-list.md)
