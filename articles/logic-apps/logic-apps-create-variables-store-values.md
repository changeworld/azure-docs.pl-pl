---
title: Tworzenie zmiennych i zarządzanie nimi do przechowywania i przekazywania wartości
description: Dowiedz się, jak przechowywać wartości, zarządzać nimi i przekazywać je przy użyciu zmiennych w zautomatyzowanych zadaniach i przepływie pracy utworzonym za pomocą aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 55984082a6b287e9f7cdca005a24ef3c18032491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456700"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Przechowywanie wartości i zarządzanie nimi przy użyciu zmiennych w usłudze Azure Logic Apps

W tym artykule pokazano, jak tworzyć i pracować ze zmiennymi, które są używane do przechowywania wartości w aplikacji logiki. Na przykład zmienne mogą pomóc w śledzeniu liczby uruchomień pętli. Aby iterować za pomocą tablicy lub sprawdzić tablicę dla określonego elementu, można użyć zmiennej, aby odwołać się do numeru indeksu dla każdego elementu tablicy.

Można tworzyć zmienne dla typów danych, takich jak liczba całkowita, float, logiczny, ciąg, tablica i obiekt. Po utworzeniu zmiennej można wykonywać inne zadania, na przykład:

* Pobierz lub odwołaj się do wartości zmiennej.
* Zwiększ lub zmniejsz zmienną o stałą wartość, zwana również *przyrostem* i *dekrementem*.
* Przypisz inną wartość do zmiennej.
* Wstaw lub *dołącz* wartość zmiennej jako ostatni raz w ciągu lub tablicy.

Zmienne istnieją i są globalne tylko w wystąpieniu aplikacji logiki, która je tworzy. Ponadto utrzymują się w dowolnej iteracji pętli wewnątrz wystąpienia aplikacji logiki. Podczas odwoływania się do zmiennej należy użyć nazwy zmiennej jako tokenu, a nie nazwy akcji, która jest zwykłym sposobem odwoływania się do danych wyjściowych akcji.

> [!IMPORTANT]
> Domyślnie cykle w pętli "Dla każdego" są uruchamiane równolegle. W przypadku używania zmiennych w pętlach uruchom pętlę [sekwencyjnie,](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) aby zmienne zwracać przewidywalne wyniki.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się, aby korzystać z bezpłatnego konta platformy Azure.](https://azure.microsoft.com/free/)

* Aplikacja logiki, w której chcesz utworzyć zmienną

  Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md) i Szybki [start: Utwórz pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako pierwszy krok w aplikacji logiki

  Aby można było dodać akcje do tworzenia i pracy ze zmiennymi, aplikacja logiki musi rozpocząć się od wyzwalacza.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Inicjowanie zmiennej

Można utworzyć zmienną i zadeklarować jej typ danych i wartość początkową — wszystko w ramach jednej akcji w aplikacji logiki. Zmienne można deklarować tylko na poziomie globalnym, a nie w zakresach, warunkach i pętlach.

1. W [witrynie Azure portal](https://portal.azure.com) lub visual studio otwórz aplikację logiki w projektancie aplikacji logiki.

   W tym przykładzie użyto witryny Azure portal i aplikacji logiki z istniejącym wyzwalaczem.

1. W aplikacji logiki w kroku, w którym chcesz dodać zmienną, wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **Nowy krok**.

     ![Dodawanie akcji](./media/logic-apps-create-variables-store-values/add-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy**+** na strzałkę łączącą, tak aby pojawił się znak plus ( ). Zaznacz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**w polu `variables` wyszukiwania wprowadź jako filtr. Z listy akcji wybierz pozycję **Inicjuj zmienną**.

   ![Wybierz akcję](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. Podaj te informacje o zmiennej, jak opisano poniżej:

   | Właściwość | Wymagany | Wartość |  Opis |
   |----------|----------|-------|--------------|
   | **Nazwa** | Tak | <*nazwa zmiennej*> | Nazwa zmiennej do przyrostu |
   | **Typ** | Tak | <*zmienny typ*> | Typ danych dla zmiennej |
   | **Wartość** | Nie | <*wartość początkowa*> | Początkowa wartość zmiennej <p><p>**Wskazówka:** Chociaż jest to opcjonalne, ustaw tę wartość jako najlepszą praktykę, aby zawsze znać wartość początkową zmiennej. |
   |||||

   Przykład:

   ![Inicjowanie zmiennej](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Teraz kontynuuj dodawanie żądanych akcji. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Jeśli przełączysz się z projektanta do edytora widoku kodu, w tym miejscu jest sposób, w jaki akcja **Inicjuj zmienną** pojawia się w definicji aplikacji logiki, która jest w formacie JavaScript Object Notation (JSON):If you switch from the designer to the code view editor, here is the way that the Initialize variable action appears in your logic app definition, which is in JavaScript Object Notation (JSON) format:

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

> [!NOTE]
> Mimo że akcja **inicjuj zmienną** ma sekcję, `variables` która jest skonstruowana jako tablica, akcja może utworzyć tylko jedną zmienną naraz. Każda nowa zmienna wymaga pojedynczej akcji **inicjuj zmienną.**

Oto przykłady dla niektórych innych typów zmiennych:

*Zmienna ciągu*

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

*Zmienna logiczna*

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

*Tablica z całkowitej liczby*

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

## <a name="get-the-variables-value"></a>Uzyskaj wartość zmiennej

Aby pobrać lub odwołać się do zawartości zmiennej, można również użyć [funkcji variables()](../logic-apps/workflow-definition-language-functions-reference.md#variables) w projektancie aplikacji logiki i edytorze widoku kodu. Podczas odwoływania się do zmiennej należy użyć nazwy zmiennej jako tokenu, a nie nazwy akcji, która jest zwykłym sposobem odwoływania się do danych wyjściowych akcji.

Na przykład to wyrażenie pobiera elementy ze zmiennej tablicy [utworzonej wcześniej w tym artykule](#append-value) przy użyciu `variables()` funkcji. Funkcja `string()` zwraca zawartość zmiennej w formacie ciągu:`"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Zmienna przyrostu 

Aby zwiększyć lub *zwiększyć* zmienną o stałą wartość, dodaj akcję **zmiennej przyrostu** do aplikacji logiki. Ta akcja działa tylko ze zmiennymi całkowitymi i zmiennymi zmiennymi zmiennymi zmiennymi zmiennymi zmiennymi.

1. W projektancie aplikacji logiki w kroku, w którym chcesz zwiększyć istniejącą zmienną, wybierz pozycję **Nowy krok**. 

   Na przykład ta aplikacja logiki ma już wyzwalacz i akcję, która utworzyła zmienną. Dodaj więc nową akcję w następujących krokach:

   ![Dodawanie akcji](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę łączącą, aby pojawić się znak plus (+). Zaznacz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wpisz jako filtr "zmienna przyrostowa". Na liście akcji wybierz **zmienną Przyrost**.

   ![Wybierz akcję "Zmienna przyrostu"](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. Podaj te informacje, aby zwiększać zmienną:

   | Właściwość | Wymagany | Wartość |  Opis |
   |----------|----------|-------|--------------|
   | **Nazwa** | Tak | <*nazwa zmiennej*> | Nazwa zmiennej do przyrostu |
   | **Wartość** | Nie | <*wartość przyrostu*> | Wartość używana do zwiększania zmiennej. Wartość domyślna to jedna. <p><p>**Wskazówka:** Chociaż opcjonalne, ustawić tę wartość jako najlepsze rozwiązanie, dzięki czemu zawsze znasz określoną wartość dla zwiększania zmiennej. |
   ||||

   Przykład:

   ![Przykład wartości przyrostu](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Jeśli przełączysz się z projektanta do edytora widoku kodu, oto sposób, w jaki akcja **zmiennej przyrostu** pojawia się wewnątrz definicji aplikacji logiki, która jest w formacie JSON:

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

Zmienne są często używane do zliczania liczby uruchomień pętli. W tym przykładzie pokazano, jak tworzyć i używać zmiennych dla tego zadania, tworząc pętlę, która zlicza załączniki w wiadomości e-mail.

1. W witrynie Azure portal utwórz pustą aplikację logiki. Dodaj wyzwalacz, który sprawdza dostępność nowych wiadomości e-mail i wszelkich załączników.

   W tym przykładzie użyto wyzwalacza programu Office 365 Outlook dla **po nadejściu nowej wiadomości e-mail**. Ten wyzwalacz można skonfigurować tak, aby uruchamiał się tylko wtedy, gdy wiadomość e-mail ma załączniki. Można jednak użyć dowolnego łącznika, który sprawdza dostępność nowych wiadomości e-mail z załącznikami, takich jak łącznik Outlook.com.

1. W wyzwalaczu, aby sprawdzić załączniki i przekazać te załączniki do przepływu pracy aplikacji logiki, wybierz **opcję Tak** dla tych właściwości:

   * **Zawiera załącznik**
   * **Uwzględnij załączniki**

   ![Sprawdzanie i dołączanie załączników](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. Dodaj [akcję **Inicjuj zmienną** ](#create-variable). Utwórz zmienną całkowitą `Count` o nazwie o zerowej wartości początkowej.

   ![Dodaj akcję dla "Inicjuj zmienną"](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Aby przełączać się między każdym załącznikiem, dodaj *a dla każdej* pętli.

   1. W obszarze Akcji **Inicjuj zmienną** wybierz pozycję **Nowy krok**.

   1. W obszarze **Wybierz akcję**wybierz pozycję **Wbudowane**. W polu wyszukiwania `for each` wprowadź jako filtr wyszukiwania i wybierz pozycję **Dla każdego**.

      ![Dodaj pętlę "dla każdego"](./media/logic-apps-create-variables-store-values/add-loop.png)

1. W pętli kliknij wewnątrz pola **Wybierz dane wyjściowe z poprzednich kroków.** Po wyświetleniu dynamicznej listy zawartości wybierz pozycję **Załączniki**.

   ![Wybieranie pozycji „Załączniki”](./media/logic-apps-create-variables-store-values/select-attachments.png)

   **Właściwość Załączniki** przekazuje tablicę, która ma załączniki wiadomości e-mail z danych wyjściowych wyzwalacza, do pętli.

1. W obszarze **Dla każdej** pętli wybierz pozycję **Dodaj akcję**.

   ![Wybierz "Dodaj akcję"](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. W polu wyszukiwania wpisz jako filtr "zmienna przyrostowa". Z listy akcji wybierz **zmienną Przyrost**.

   > [!NOTE]
   > Upewnij się, że akcja **zmiennej przyrostu** pojawia się wewnątrz pętli. Jeśli akcja pojawi się poza pętlą, przeciągnij akcję do pętli.

1. W akcji **Zmienna przyrostowa** z listy **Nazwa** wybierz zmienną **Count.**

   ![Wybierz zmienną "Count"](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. W obszarze pętli dodaj dowolną akcję, która wysyła ci liczbę załączników. W akcji uwzględnij wartość ze zmiennej **Count,** na przykład:

   ![Dodawanie akcji, która wysyła wyniki](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

1. Jeśli aplikacja logiki nie jest włączona, w menu aplikacji logiki wybierz pozycję **Przegląd**. Na pasku narzędzi wybierz pozycję **Włącz**.

1. Na pasku narzędzi Projektanta aplikacji logiki wybierz pozycję **Uruchom**. Ten krok ręcznie uruchamia aplikację logiki.

1. Wyślij wiadomość e-mail z co najmniej jednym załącznikiem na konto e-mail użyte w tym przykładzie.

   Ten krok uruchamia wyzwalacz aplikacji logiki, który tworzy i uruchamia wystąpienie dla przepływu pracy aplikacji logiki. W rezultacie aplikacja logiki wysyła wiadomość lub wiadomość e-mail z numerem załączników w wysłanej wiadomości e-mail.

Jeśli przełączysz się z projektanta do edytora widoku kodu, oto sposób, że **dla każdej** pętli pojawia się wraz z akcji **zmiennej przyrost** wewnątrz definicji aplikacji logiki, który jest w formacie JSON.

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

## <a name="decrement-variable"></a>Zmienna dekrementacji

Aby zmniejszyć lub *zmniejszyć* zmienną o stałą wartość, wykonaj kroki [zwiększania zmiennej,](#increment-value) z tą różnicą, że zamiast tego znajdziesz i wybierz akcję **zmiennej Decrement.** Ta akcja działa tylko ze zmiennymi całkowitymi i zmiennymi zmiennymi zmiennymi zmiennymi zmiennymi zmiennymi.

Oto właściwości akcji **zmiennej Decrement:**

| Właściwość | Wymagany | Wartość |  Opis |
|----------|----------|-------|--------------|
| **Nazwa** | Tak | <*nazwa zmiennej*> | Nazwa zmiennej do dekrementacji | 
| **Wartość** | Nie | <*wartość przyrostu*> | Wartość zmniejszania zmiennej. Wartość domyślna to jedna. <p><p>**Wskazówka:** Chociaż opcjonalne, ustawić tę wartość jako najlepsze rozwiązanie, dzięki czemu zawsze znasz określoną wartość dla zmniejszania zmiennej. |
||||| 

Jeśli przełączysz się z projektanta do edytora widoku kodu, oto sposób, w jaki akcja **zmiennej dekrementu** pojawia się wewnątrz definicji aplikacji logiki, która jest w formacie JSON.

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

Aby przypisać inną wartość do istniejącej zmiennej, wykonaj kroki [zwiększające zmienną,](#increment-value) z tą różnicą, że:

1. Zamiast tego znajdź i wybierz akcję **Ustaw zmienną.**

1. Podaj nazwę i wartość zmiennej, którą chcesz przypisać. Zarówno nowa wartość, jak i zmienna muszą mieć ten sam typ danych. Wartość jest wymagana, ponieważ ta akcja nie ma wartości domyślnej.

Oto właściwości akcji **Ustaw zmienną:**

| Właściwość | Wymagany | Wartość |  Opis |
|----------|----------|-------|--------------|
| **Nazwa** | Tak | <*nazwa zmiennej*> | Nazwa zmiennej, która ma się zmienić |
| **Wartość** | Tak | <*nowa wartość*> | Wartość, którą chcesz przypisać zmienną. Oba muszą mieć ten sam typ danych. |
||||| 

> [!NOTE]
> O ile zmienne nie są zwiększane lub zmniejszane, zmiana zmiennych wewnątrz pętli *może* spowodować nieoczekiwane wyniki, ponieważ pętle są uruchamiane równolegle lub jednocześnie domyślnie. W takich przypadkach spróbuj skonfigurować pętlę do uruchamiania sekwencyjnie. Na przykład, jeśli chcesz odwołać się do wartości zmiennej wewnątrz pętli i oczekiwać tej samej wartości na początku i na końcu tego wystąpienia pętli, wykonaj następujące kroki, aby zmienić sposób działania pętli: 
>
> 1. W prawym górnym rogu pętli wybierz przycisk wielokropek (**...**), a następnie wybierz **pozycję Ustawienia**.
> 
> 2. W obszarze **Kontrola współbieżności**zmień ustawienie **Domyślne zastępowanie** na **Włączone**.
>
> 3. Przeciągnij suwak **Stopień równoległości** do **1**.

Jeśli przełączysz się z projektanta do edytora widoku kodu, oto sposób, że **akcja Ustaw zmienną** pojawia się wewnątrz definicji aplikacji logiki, która jest w formacie JSON. W tym `Count` przykładzie zmienia bieżącą wartość zmiennej na inną wartość.

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

Dla zmiennych, które przechowują ciągi lub tablice, można wstawić lub *dołączyć* wartość zmiennej jako ostatni element w tych ciągów lub tablic. Można wykonać kroki zwiększania zmiennej, z tą [różnicą,](#increment-value) że zamiast tego wykonaj następujące kroki: 

1. Znajdź i wybierz jedną z tych akcji na podstawie tego, czy zmienna jest ciągiem, czy tablicą: 

   * **Dołączanie do zmiennej ciągu**
   * **Dołączanie do zmiennej tablicowej** 

1. Podaj wartość do doskładniania jako ostatni element w ciągu lub tablicy. Ta wartość jest wymagana.

Oto właściwości akcji **Dołącz do...**

| Właściwość | Wymagany | Wartość |  Opis |
|----------|----------|-------|--------------|
| **Nazwa** | Tak | <*nazwa zmiennej*> | Nazwa zmiennej, która ma się zmienić |
| **Wartość** | Tak | <*wartość dołączania*> | Wartość, którą chcesz dołączyć, która może mieć dowolny typ |
|||||

Jeśli przełączysz się z projektanta do edytora widoku kodu, oto sposób, w jaki akcja **dołączania do zmiennej tablicy** pojawia się wewnątrz definicji aplikacji logiki, która jest w formacie JSON. W tym przykładzie tworzy zmienną tablicową i dodaje inną wartość jako ostatni element w tablicy. Wynik jest zaktualizowaną zmienną zawierającą tę tablicę:`[1,2,3,"red"]`

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

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [łącznikach aplikacji logiki](../connectors/apis-list.md)
