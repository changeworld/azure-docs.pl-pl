---
title: Wykonywanie operacji na danych
description: Konwertowanie danych i formatów danych, zarządzanie nimi i manipulowanie nimi w aplikacjach Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283942"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Wykonywanie operacji na danych w usłudze Azure Logic Apps

W tym artykule pokazano, jak można pracować z danymi w aplikacjach logiki, dodając akcje dla tych zadań i nie tylko:

* Tworzenie tabel z tablic.
* Tworzenie tablic z innych tablic na podstawie warunku.
* Utwórz przyjazne dla użytkownika tokeny z właściwości obiektu JavaScript Object Notation (JSON), aby łatwo używać tych właściwości w przepływie pracy.

Jeśli nie znajdziesz w tym miejscu żądanej akcji, spróbuj przejrzeć wiele różnych [funkcji manipulowania danymi,](../logic-apps/workflow-definition-language-functions-reference.md) które udostępnia usługa Azure Logic Apps.

Te tabele podsumowują operacje danych, których można używać i są zorganizowane na podstawie typów danych źródłowych, nad którymi działają operacje, ale każdy opis jest wyświetlany alfabetycznie.

**Akcje tablicowe** 

Te akcje ułatwiają pracę z danymi w tablicach.

| Akcja | Opis |
|--------|-------------|
| [**Tworzenie tabeli CSV**](#create-csv-table-action) | Utwórz tabelę wartości oddzielonych przecinkami (CSV) z tablicy. |
| [**Tworzenie tabeli HTML**](#create-html-table-action) | Tworzenie tabeli HTML z tablicy. |
| [**Tablica filtru**](#filter-array-action) | Utwórz podzbiór tablicy z tablicy na podstawie określonego filtru lub warunku. |
| [**Dołączyć**](#join-action) | Utwórz ciąg ze wszystkich elementów w tablicy i oddziel każdy element określonym znakiem. |
| [**Wybierz**](#select-action) | Utwórz tablicę z określonych właściwości dla wszystkich elementów w innej tablicy. |
||| 

**Akcje JSON**

Te akcje ułatwiają pracę z danymi w formacie JavaScript Object Notation (JSON).

| Akcja | Opis |
|--------|-------------|
| [**Komponować**](#compose-action) | Utwórz wiadomość lub ciąg z wielu danych wejściowych, które mogą mieć różne typy danych. Następnie można użyć tego ciągu jako pojedynczego wejścia, zamiast wielokrotnie wprowadzać te same dane wejściowe. Na przykład można utworzyć pojedynczy komunikat JSON z różnych danych wejściowych. |
| [**Analizować JSON**](#parse-json-action) | Tworzenie przyjaznych dla użytkownika tokenów danych dla właściwości w zawartości JSON, dzięki czemu można łatwiej używać właściwości w aplikacjach logiki. |
|||

Aby utworzyć bardziej złożone przekształcenia JSON, zobacz [Wykonywanie zaawansowanych przekształceń JSON za pomocą szablonów cieczy](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki, w której potrzebna jest operacja do pracy z danymi

  Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md) i Szybki [start: Utwórz pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako pierwszy krok w aplikacji logiki 

  Operacje na danych są dostępne tylko jako akcje, więc zanim będzie można użyć tych akcji, uruchom aplikację logiki za pomocą wyzwalacza i dołącz inne akcje wymagane do tworzenia żądanych danych wyjściowych.

<a name="compose-action"></a>

## <a name="compose-action"></a>Skomponowanie akcji

Aby skonstruować pojedyncze dane wyjściowe, takie jak obiekt JSON z wielu danych wejściowych, można użyć **compose** akcji. Dane wejściowe mogą mieć różne typy, takie jak liczby całkowite, wartości logiczne, tablice, obiekty JSON i inny typ macierzysty, który obsługuje usługa Azure Logic Apps, na przykład binarny i XML. Następnie można użyć danych wyjściowych w akcjach, które następują po **akcji Redagowanie.** Akcja **Redagowanie** można również zaoszczędzić od wielokrotnego wprowadzania tych samych danych wejściowych podczas tworzenia przepływu pracy aplikacji logiki.

Na przykład można utworzyć komunikat JSON z wielu zmiennych, takich jak zmienne ciągów, które przechowują imiona i nazwiska osób oraz zmienną całkowitą, która przechowuje wiek osób. W tym miejscu **compose** akcji akceptuje te dane wejściowe:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

i tworzy to wyjście:

`{"age":35,"fullName":"Owens,Sophie"}`

Aby wypróbować przykład, wykonaj następujące kroki przy użyciu projektanta aplikacji logiki. Lub, jeśli wolisz pracować w edytorze widoku kodu, możesz skopiować przykładowe definicji akcji **redagowania** i **inicjowania zmiennych** z tego artykułu do podstawowej definicji przepływu pracy własnej aplikacji logiki: [Przykłady kodu operacji danych — Redagowanie](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. W [witrynie Azure portal](https://portal.azure.com) lub visual studio otwórz aplikację logiki w logice App Designer.

   W tym przykładzie użyto witryny Azure portal i aplikacji logiki z **wyzwalaczem cyklu** i kilka akcji **inicjuj zmienne.** Te akcje są skonfigurowane do tworzenia dwóch zmiennych ciągów i zmiennej całkowitej. Po późniejszym przetestowaniu aplikacji logiki można ręcznie uruchomić aplikację bez oczekiwania na uruchomienie wyzwalacza.

   ![Uruchamianie przykładowej aplikacji logiki dla akcji "Redone"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. W aplikacji logiki, w której chcesz utworzyć dane wyjściowe, wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **Nowy krok**.

     ![Wybierz "Nowy krok" dla akcji "Redagowanie"](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik**+** myszy na strzałkę łączącą, aby pojawić się znak plus ( ). Zaznacz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**w polu `compose` wyszukiwania wprowadź jako filtr. Z listy akcje wybierz akcję **Redagowanie.**

   ![Wybierz akcję "Redagowanie"](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. W polu **Wejścia** podaj dane wejściowe, które chcesz utworzyć dane wyjściowe.

   W tym przykładzie po kliknięciu wewnątrz pola **Wejścia** zostanie wyświetlona dynamiczna lista zawartości, dzięki czemu można wybrać wcześniej utworzone zmienne:

   ![Wybierz dane wejściowe do użycia dla akcji "Redagowanie"](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Oto ukończona akcja **compose przykład:** 

   ![Ukończony przykład akcji "Skomponuj"](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji na temat tej akcji w podstawowej definicji przepływu pracy, zobacz [akcję Redagowanie](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action).

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby potwierdzić, czy akcja **Redagowanie** tworzy oczekiwane wyniki, wyślij sobie powiadomienie, które zawiera dane wyjściowe z akcji **Redagowanie.**

1. W aplikacji logiki dodaj akcję, która może wysyłać wyniki z akcji **Redagowanie.**

1. W tej akcji kliknij dowolne miejsce, w które mają być wyświetlane wyniki. Po otwarciu dynamicznej listy zawartości w obszarze Akcja **Redagowanie** wybierz pozycję **Wyjście**.

   W tym przykładzie użyto akcji **Wyślij wiadomość e-mail** i uwzględniono pola **Dane wyjściowe** w treści i temacie wiadomości e-mail:

   ![Pola "Dane wyjściowe" dla akcji "Redagowanie"](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Teraz ręcznie uruchom aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   Na podstawie używanego łącznika wiadomości e-mail, oto wyniki, które otrzymujesz:

   ![Wyślij wiadomość e-mail z wynikami akcji "Skomponuj"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Tworzenie akcji tabeli CSV

Aby utworzyć tabelę wartości oddzielonych przecinkami (CSV), która ma właściwości i wartości z obiektów JSON (JavaScript Object Notation) w tablicy, użyj akcji **Utwórz tabelę CSV.** Następnie można użyć tabeli wynikowej w akcjach zgodnych z akcją **Utwórz csv tabeli.**

Jeśli wolisz pracować w edytorze widoku kodu, możesz skopiować **przykładową tabelę CsV** i zainicjować definicje akcji **zmiennych** z tego artykułu do podstawowej definicji przepływu pracy własnej aplikacji logiki: [Przykłady kodu operacji danych — Tworzenie tabeli CSV](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. W [witrynie Azure portal](https://portal.azure.com) lub visual studio otwórz aplikację logiki w logice App Designer.

   W tym przykładzie użyto witryny Azure portal i aplikacji logiki z **wyzwalaczem cyklu** i **inicjuj akcję zmiennej.** Akcja jest skonfigurowana do tworzenia zmiennej, której wartością początkową jest tablica, która ma pewne właściwości i wartości w formacie JSON. Po późniejszym przetestowaniu aplikacji logiki można ręcznie uruchomić aplikację bez oczekiwania na uruchomienie wyzwalacza.

   ![Uruchamianie przykładowej aplikacji logiki dla akcji "Tworzenie tabeli CSV"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. W aplikacji logiki, w której chcesz utworzyć tabelę CSV, wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **Nowy krok**.

     ![Wybierz "Nowy krok" dla akcji "Utwórz tabelę CSV"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik**+** myszy na strzałkę łączącą, aby pojawić się znak plus ( ). Zaznacz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**w polu `create csv table` wyszukiwania wprowadź jako filtr. Z listy akcje wybierz akcję **Utwórz tabelę CSV.**

   ![Wybierz akcję "Utwórz tabelę CSV"](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. W polu **Od** podaj tablicę lub wyrażenie, które chcesz utworzyć do tworzenia tabeli.

   W tym przykładzie po kliknięciu wewnątrz pola **Od** zostanie wyświetlona lista zawartości dynamicznej, dzięki czemu można wybrać poprzednio utworzoną zmienną:

   ![Wybieranie danych wyjściowych tablicy do tworzenia tabeli CSV](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > Aby utworzyć przyjazne dla użytkownika tokeny właściwości w obiektach JSON, aby można było wybrać te właściwości jako dane wejściowe, należy użyć [parse JSON](#parse-json-action) przed wywołaniem akcji **tabeli Utwórz CSV.**

   Oto gotowy przykład Utwórz akcję **tabeli CSV:** 

   ![Ukończony przykład akcji "Tworzenie tabeli CSV"](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="customize-table-format"></a>Dostosowywanie formatu tabeli

Domyślnie **właściwość Kolumny** jest ustawiona na automatyczne tworzenie kolumn tabeli na podstawie elementów tablicy. Aby określić niestandardowe nagłówki i wartości, wykonaj następujące czynności:

1. Otwórz listę **Kolumny** i wybierz pozycję **Niestandardowe**.

1. We właściwości **Nagłówek** określ niestandardowy tekst nagłówka, który ma być używany.

1. We właściwości **Value** określ wartość niestandardową, która ma być używana.

Aby zwrócić wartości z tablicy, można użyć [ `item()` funkcji](../logic-apps/workflow-definition-language-functions-reference.md#item) z akcji **Utwórz tabelę CSV.** W `For_each` pętli można użyć [ `items()` funkcji](../logic-apps/workflow-definition-language-functions-reference.md#items).

Załóżmy na przykład, że chcesz kolumny tabeli, które mają tylko wartości właściwości, a nie nazwy właściwości z tablicy. Aby zwrócić tylko te wartości, wykonaj następujące kroki do pracy w widoku projektanta lub w widoku kodu. Oto wynik, który zwraca w tym przykładzie:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Praca w widoku projektanta

W akcji zachowaj pustą kolumnę **Nagłówek.** W każdym wierszu w **kolumnie Wartość** wyłuduj każdą właściwość tablicy, którą chcesz. Każdy wiersz w obszarze **Wartość** zwraca wszystkie wartości dla określonej właściwości tablicy i staje się kolumną w tabeli.

1. W obszarze **Wartość**w każdym wierszu kliknij wewnątrz pola edycji, aby wyświetlić dynamiczną listę zawartości.

1. Na dynamicznej liście zawartości wybierz pozycję **Wyrażenie**.

1. W edytorze wyrażeń wprowadź to wyrażenie określające żądaną wartość właściwości tablicy, a następnie wybierz przycisk **OK**.

   `item()?['<array-property-name>']`

   Przykład:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Wyłuskanie "Opis" dla "Tworzenie tabeli CSV"](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. Powtórz poprzednie kroki dla każdej właściwej właściwości tablicy. Po zakończeniu działanie wygląda następująco:

   !["item()" w "Tworzenie tabeli CSV"](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. Aby rozpoznać wyrażenia w bardziej opisowych wersjach, przełącz się do widoku kodu i z powrotem do widoku projektanta, a następnie otwórz ponownie zwiniętej akcji:

   Akcja **Utwórz tabelę CSV** wygląda teraz następująco:

   !["Tworzenie tabeli CSV" - rozpoznawane wyrażenia, brak nagłówków](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Praca w widoku kodu

W definicji JSON akcji, `columns` w tablicy, ustawić `header` właściwość na pusty ciąg. Dla `value` każdej właściwości wyłuskania każdej właściwości tablicy, które chcesz.

1. Na pasku narzędzi projektanta wybierz **pozycję Widok kodu**.

1. W edytorze kodu w tablicy `columns` akcji dodaj `header` pustą `value` właściwość i to wyrażenie dla każdej kolumny wartości tablicy, które chcesz:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Przykład:

   ```json
   "Create_CSV_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Przełącz się z powrotem do widoku projektanta i ponownie otwórz zwiniętej akcji.

   Akcja **Utwórz tabelę CSV** jest teraz wyświetlana w tym przykładzie, a wyrażenia zostały rozpoznane w bardziej opisowych wersjach:

   !["Tworzenie tabeli CSV" - rozpoznawane wyrażenia i brak nagłówków](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

Aby uzyskać więcej informacji na temat tej akcji w podstawowej definicji przepływu pracy, zobacz [akcję Tabela](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby potwierdzić, czy akcja **Tworzenie tabeli CSV** tworzy oczekiwane wyniki, wyślij sobie powiadomienie, które zawiera dane wyjściowe z akcji **Tworzenie tabeli CSV.**

1. W aplikacji logiki dodaj akcję, która może wysyłać wyniki z akcji **Tworzenie tabeli CSV.**

1. W tej akcji kliknij dowolne miejsce, w które mają być wyświetlane wyniki. Po otwarciu dynamicznej listy zawartości w obszarze **Akcja Utwórz tabelę CSV** wybierz pozycję **Wyjście**. 

   W tym przykładzie użyto akcji **Wyślij wiadomość e-mail w** programie Office 365 Outlook i uwzględniono pole Dane **wyjściowe** w treści wiadomości e-mail:

   ![Pola "Dane wyjściowe" dla akcji "Tworzenie tabeli CSV"](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Teraz ręcznie uruchom aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   Na podstawie używanego łącznika wiadomości e-mail, oto wyniki, które otrzymujesz:

   ![Wysyłanie wiadomości e-mail z wynikami akcji "Tworzenie tabeli CSV"](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Tworzenie akcji tabeli HTML

Aby utworzyć tabelę HTML zawierającą właściwości i wartości z obiektów Notacji obiektów JavaScript (JSON) w tablicy, użyj akcji **Utwórz tabelę HTML.** Następnie można użyć tabeli wynikowej w akcjach zgodnych z akcjami **Utwórz tabelę HTML.**

Jeśli wolisz pracować w edytorze widoku kodu, możesz skopiować **przykładową tabelę Tworzenie html** i **inicjowanie** definicji akcji zmiennych z tego artykułu do podstawowej definicji przepływu pracy aplikacji logiki: [Przykłady kodu operacji danych — Tworzenie tabeli HTML](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. W [witrynie Azure portal](https://portal.azure.com) lub visual studio otwórz aplikację logiki w logice App Designer.

   W tym przykładzie użyto witryny Azure portal i aplikacji logiki z **wyzwalaczem cyklu** i **inicjuj akcję zmiennej.** Akcja jest skonfigurowana do tworzenia zmiennej, której wartością początkową jest tablica, która ma pewne właściwości i wartości w formacie JSON. Po późniejszym przetestowaniu aplikacji logiki można ręcznie uruchomić aplikację bez oczekiwania na uruchomienie wyzwalacza.

   ![Uruchamianie przykładowej aplikacji logiki dla "Tworzenie tabeli HTML"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. W aplikacji logiki, w której chcesz utworzyć tabelę HTML, wykonaj jedną z następujących czynności:

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **Nowy krok**.

     ![Wybierz "Nowy krok" dla akcji "Utwórz tabelę HTML"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik**+** myszy na strzałkę łączącą, aby pojawić się znak plus ( ). Zaznacz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**w polu `create html table` wyszukiwania wprowadź jako filtr. Z listy akcje wybierz akcję **Utwórz tabelę HTML.**

   ![Wybierz akcję "Utwórz tabelę HTML"](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. W polu **Od** podaj tablicę lub wyrażenie, które chcesz utworzyć do tworzenia tabeli.

   W tym przykładzie po kliknięciu wewnątrz pola **Od** zostanie wyświetlona lista zawartości dynamicznej, dzięki czemu można wybrać poprzednio utworzoną zmienną:

   ![Wybieranie danych wyjściowych tablicy do tworzenia tabeli HTML](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > Aby utworzyć przyjazne dla użytkownika tokeny właściwości w obiektach JSON, aby można było wybrać te właściwości jako dane wejściowe, należy użyć [parse JSON](#parse-json-action) przed wywołaniem akcji **Tabela Utwórz HTML.**

   Oto gotowy przykład Utwórz akcję **tabeli HTML:**

   ![Gotowy przykład "Tworzenie tabeli HTML"](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="customize-table-format"></a>Dostosowywanie formatu tabeli

Domyślnie **właściwość Kolumny** jest ustawiona na automatyczne tworzenie kolumn tabeli na podstawie elementów tablicy. Aby określić niestandardowe nagłówki i wartości, wykonaj następujące czynności:

1. Otwórz listę **Kolumny** i wybierz pozycję **Niestandardowe**.

1. We właściwości **Nagłówek** określ niestandardowy tekst nagłówka, który ma być używany.

1. We właściwości **Value** określ wartość niestandardową, która ma być używana.

Aby zwrócić wartości z tablicy, można użyć [ `item()` funkcji](../logic-apps/workflow-definition-language-functions-reference.md#item) z akcji **Utwórz tabelę HTML.** W `For_each` pętli można użyć [ `items()` funkcji](../logic-apps/workflow-definition-language-functions-reference.md#items).

Załóżmy na przykład, że chcesz kolumny tabeli, które mają tylko wartości właściwości, a nie nazwy właściwości z tablicy. Aby zwrócić tylko te wartości, wykonaj następujące kroki do pracy w widoku projektanta lub w widoku kodu. Oto wynik, który zwraca w tym przykładzie:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Praca w widoku projektanta

W akcji zachowaj pustą kolumnę **Nagłówek.** W każdym wierszu w **kolumnie Wartość** wyłuduj każdą właściwość tablicy, którą chcesz. Każdy wiersz w obszarze **Wartość** zwraca wszystkie wartości dla określonej właściwości i staje się kolumną w tabeli.

1. W obszarze **Wartość**w każdym wierszu kliknij wewnątrz pola edycji, aby wyświetlić dynamiczną listę zawartości.

1. Na dynamicznej liście zawartości wybierz pozycję **Wyrażenie**.

1. W edytorze wyrażeń wprowadź to wyrażenie określające żądaną wartość właściwości tablicy, a następnie wybierz przycisk **OK**.

   `item()?['<array-property-name>']`

   Przykład:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Dereference właściwość w akcji "Tworzenie tabeli HTML"](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. Powtórz poprzednie kroki dla każdej właściwej właściwości tablicy. Po zakończeniu działanie wygląda następująco:

   !["item()" w "Tworzenie tabeli HTML"](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. Aby rozpoznać wyrażenia w bardziej opisowych wersjach, przełącz się do widoku kodu i z powrotem do widoku projektanta, a następnie otwórz ponownie zwiniętej akcji:

   Akcja **Utwórz tabelę HTML** wygląda teraz następująco:

   !["Tworzenie tabeli HTML" - rozpoznawane wyrażenia, brak nagłówków](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Praca w widoku kodu

W definicji JSON akcji, `columns` w tablicy, ustawić `header` właściwość na pusty ciąg. Dla `value` każdej właściwości wyłuskania każdej właściwości tablicy, które chcesz.

1. Na pasku narzędzi projektanta wybierz **pozycję Widok kodu**.

1. W edytorze kodu w tablicy `columns` akcji dodaj `header` pustą `value` właściwość i to wyrażenie dla każdej kolumny wartości tablicy, które chcesz:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Przykład:

   ```json
   "Create_HTML_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Przełącz się z powrotem do widoku projektanta i ponownie otwórz zwiniętej akcji.

   Akcja **Utwórz tabelę HTML** jest teraz wyświetlana w tym przykładzie, a wyrażenia zostały rozpoznane w bardziej opisowych wersjach:

   !["Tworzenie tabeli HTML" - rozpoznawane wyrażenia i brak nagłówków](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

Aby uzyskać więcej informacji na temat tej akcji w podstawowej definicji przepływu pracy, zobacz [akcję Tabela](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby potwierdzić, czy akcja **Utwórz tabelę HTML** tworzy oczekiwane wyniki, wyślij sobie powiadomienie, które zawiera dane wyjściowe z akcji **Utwórz tabelę HTML.**

1. W aplikacji logiki dodaj akcję, która może wysyłać wyniki z akcji **Utwórz tabelę HTML.**

1. W tej akcji kliknij dowolne miejsce, w które mają być wyświetlane wyniki. Po otwarciu dynamicznej listy zawartości w obszarze Akcja **Utwórz tabelę HTML** wybierz pozycję **Wyjście**. 

   W tym przykładzie użyto akcji **Wyślij wiadomość e-mail w** programie Office 365 Outlook i uwzględniono pole Dane **wyjściowe** w treści wiadomości e-mail:

   ![Pola "Dane wyjściowe" dla "Tworzenie tabeli HTML"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > Podczas dołączania danych wyjściowych tabeli HTML do akcji wiadomości e-mail upewnij się, że właściwość **Jest HTML** została ustawiona na **Tak** w zaawansowanych opcjach akcji e-mail. W ten sposób akcja wiadomości e-mail poprawnie formatuje tabelę HTML.

1. Teraz ręcznie uruchom aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   Na podstawie używanego łącznika wiadomości e-mail, oto wyniki, które otrzymujesz:

   ![Wiadomość e-mail z wynikami "Tworzenie tabeli HTML"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Akcja tablicy filtru

Aby utworzyć mniejszą tablicę zawierającą elementy, które spełniają określone kryteria, z istniejącej tablicy, należy użyć akcji **Tablica filtru.** Następnie można użyć tablicy filtrowane w akcje, które następują po **akcji filter tablicy.**

> [!NOTE]
> Każdy tekst filtru, który jest używany w stanie jest rozróżniana wielkość liter. Ponadto ta akcja nie może zmienić formatu lub składników elementów w tablicy. 
> 
> Aby akcje używały danych wyjściowych tablicy z akcji **Tablica Filtr,** te akcje muszą akceptować tablice jako dane wejściowe lub może być konieczne przekształcenie tablicy wyjściowej w inny zgodny format.
> 
> Jeśli wywołasz punkt końcowy HTTP i odbierzesz odpowiedź JSON, użyj akcji **Parse JSON,** aby przetworzyć odpowiedź JSON. 
> W przeciwnym razie **akcja tablica Filter** może odczytywać tylko treść odpowiedzi, a nie strukturę ładunku JSON.

Jeśli wolisz pracować w edytorze widoku kodu, możesz skopiować **przykładową tablicę filtru** i zainicjować definicje akcji **zmiennych** z tego artykułu do podstawowej definicji przepływu pracy aplikacji logiki: [przykłady kodu operacji danych — tablica filtru](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. W [witrynie Azure portal](https://portal.azure.com) lub visual studio otwórz aplikację logiki w logice App Designer.

   W tym przykładzie użyto witryny Azure portal i aplikacji logiki z **wyzwalaczem cyklu** i **inicjuj akcję zmiennej.** Akcja jest skonfigurowana do tworzenia zmiennej, której wartość początkowa jest tablicą zawierającą kilka przykładowych całkowitych. Po późniejszym przetestowaniu aplikacji logiki można ręcznie uruchomić aplikację bez oczekiwania na uruchomienie wyzwalacza.

   > [!NOTE]
   > Chociaż w tym przykładzie użyto prostej tablicy liczby całkowitej, ta akcja jest szczególnie przydatna w przypadku tablic obiektów JSON, w których można filtrować na podstawie właściwości i wartości obiektów.

   ![Uruchamianie przykładowej aplikacji logiki dla akcji "Filtrowanie tablicy"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. W aplikacji logiki, w której chcesz utworzyć tablicę filtrowane, wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **Nowy krok**.

     ![Wybierz "Nowy krok" dla akcji "Tablica filtru"](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik**+** myszy na strzałkę łączącą, aby pojawić się znak plus ( ). Zaznacz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania `filter array` wprowadź jako filtr. Z listy akcje wybierz akcję **Tablica Filtruj.**

   ![Wybierz akcję "Tablica filtru"](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. W polu **Od** podaj tablicę lub wyrażenie, które chcesz filtrować.

   W tym przykładzie po kliknięciu wewnątrz pola **Od** zostanie wyświetlona lista zawartości dynamicznej, dzięki czemu można wybrać poprzednio utworzoną zmienną:

   ![Wybieranie danych wyjściowych tablicy do tworzenia tablicy filtrowanych](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. Dla warunku określ elementy tablicy do porównania, wybierz operator porównania i określ wartość porównania.

   W tym przykładzie `item()` użyto funkcji dostępu do każdego elementu w tablicy, podczas gdy akcja **Tablica Filtruj** wyszukuje elementy tablicy, których wartość jest większa niż jedna:

   ![Ukończony przykład akcji "Tablica filtru"](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji na temat tej akcji w podstawowej definicji przepływu pracy, zobacz [Akcja kwerendy](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby potwierdzić, czy akcja **tablicy filtrowania** tworzy oczekiwane wyniki, wyślij sobie powiadomienie, które zawiera dane wyjściowe z akcji **tablicy Filtruj.**

1. W aplikacji logiki dodaj akcję, która może wysyłać wyniki z akcji **tablicy Filtruj.**

1. W tej akcji kliknij dowolne miejsce, w które mają być wyświetlane wyniki. Po otwarciu dynamicznej listy zawartości wybierz pozycję **Wyrażenie**. Aby uzyskać dane wyjściowe tablicy z akcji **Tablica Filtruj,** wprowadź to wyrażenie, które zawiera nazwę akcji **Tablica filtru:**

   `@actionBody('Filter_array')`

   W tym przykładzie użyto akcji **Wyślij wiadomość e-mail** w programie Office 365 Outlook i uwzględniono dane wyjściowe z wyrażenia **actionBody ("Filter_array")** w treści wiadomości e-mail:

   ![Wyjścia akcji z akcji "Tablica filtru"](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Teraz ręcznie uruchom aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   Na podstawie używanego łącznika wiadomości e-mail, oto wyniki, które otrzymujesz:

   ![Wysyłanie wiadomości e-mail z wynikami akcji "Tablica filtru"](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Dołącz do akcji

Aby utworzyć ciąg, który ma wszystkie elementy z tablicy i oddzielić te elementy z określonym znakiem ogranicznika, należy użyć **Akcji Sprzężenie.** Następnie można użyć ciągu w akcjach, które następują po **akcji Sprzężenie.**

Jeśli wolisz pracować w edytorze widoku kodu, możesz skopiować przykładowe definicji akcji **Sprzężenia** i **inicjowania zmiennych** z tego artykułu do podstawowej definicji przepływu pracy aplikacji logiki: [Przykłady kodu operacji danych — Dołącz](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. W [witrynie Azure portal](https://portal.azure.com) lub visual studio otwórz aplikację logiki w logice App Designer.

   W tym przykładzie użyto witryny Azure portal i aplikacji logiki z **wyzwalaczem cyklu** i **inicjuj akcję zmiennej.** Ta akcja jest skonfigurowana do tworzenia zmiennej, której wartość początkowa jest tablicą zawierającą kilka przykładowych wartości całkowitych. Podczas testowania aplikacji logiki później, można ręcznie uruchomić aplikację bez oczekiwania na wyzwalacz do uruchomienia.

   ![Uruchamianie przykładowej aplikacji logiki dla akcji "Dołącz"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. W aplikacji logiki, w której chcesz utworzyć ciąg z tablicy, wykonaj jedną z następujących czynności:

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **Nowy krok**.

     ![SSelect "Nowy krok" dla akcji "Dołącz"](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik**+** myszy na strzałkę łączącą, aby pojawić się znak plus ( ). Zaznacz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania `join` wprowadź jako filtr. Z listy akcji wybierz tę akcję: **Dołącz**

   ![Wybierz akcję "Dołącz"](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. W **polu Od** podaj tablicę zawierającą elementy, które chcesz dołączyć jako ciąg znaków.

   W tym przykładzie po kliknięciu wewnątrz pola **Od** pojawi się dynamiczna lista zawartości, dzięki której można wybrać poprzednio utworzoną zmienną:  

   ![Wybieranie danych wyjściowych tablicy do tworzenia ciągu](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. W polu **Dołącz do** wprowadź odpowiedni znak do oddzielenia każdego elementu tablicy. 

   W tym przykładzie użyto dwukropka (:) jako separator.

   ![Podaj znak separatora](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji na temat tej akcji w podstawowej definicji przepływu pracy, zobacz [akcję Dołączanie](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby potwierdzić, czy **akcja Sprzężenie** tworzy oczekiwane wyniki, wyślij sobie powiadomienie, które zawiera dane wyjściowe z akcji **Sprzężenie.**

1. W aplikacji logiki dodaj akcję, która może wysyłać wyniki z akcji **Dołączanie.**

1. W tej akcji kliknij dowolne miejsce, w które mają być wyświetlane wyniki. Po otwarciu dynamicznej listy zawartości w obszarze Akcji **Dołączanie** wybierz pozycję **Wyjście**. 

   W tym przykładzie użyto akcji **Wyślij wiadomość e-mail w** programie Office 365 Outlook i uwzględniono pole Dane **wyjściowe** w treści wiadomości e-mail:

   ![Pola "Dane wyjściowe" dla akcji "Dołącz"](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Teraz ręcznie uruchom aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   Na podstawie używanego łącznika wiadomości e-mail, oto wyniki, które otrzymujesz:

   ![Wyślij wiadomość e-mail z wynikami akcji "Dołącz"](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Akcja Analizuj JSON

Aby odwoływać się lub uzyskiwać dostęp do właściwości w zawartości Notacji obiektu JavaScript (JSON), można utworzyć przyjazne dla użytkownika pola lub tokeny dla tych właściwości przy użyciu akcji **Parse JSON.** W ten sposób można wybrać te właściwości z listy zawartości dynamicznej po określeniu danych wejściowych dla aplikacji logiki. Dla tej akcji można podać schemat JSON lub wygenerować schemat JSON z przykładowej zawartości JSON lub ładunku.

Jeśli wolisz pracować w edytorze widoku kodu, możesz skopiować **przykładowe parse JSON** i zainicjować definicje akcji **zmiennych** z tego artykułu do podstawowej definicji przepływu pracy aplikacji logiki: [przykłady kodu operacji danych — Parse JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. W [witrynie Azure portal](https://portal.azure.com) lub visual studio otwórz aplikację logiki w logice App Designer.

   W tym przykładzie użyto witryny Azure portal i aplikacji logiki z **wyzwalaczem cyklu** i **inicjuj akcję zmiennej.** Akcja jest skonfigurowana do tworzenia zmiennej, której wartością początkową jest obiekt JSON, który ma właściwości i wartości. Po późniejszym przetestowaniu aplikacji logiki można ręcznie uruchomić aplikację bez oczekiwania na uruchomienie wyzwalacza.

   ![Uruchamianie przykładowej aplikacji logiki dla akcji "Analizowanie JSON"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. W aplikacji logiki, w której chcesz przeanalizować zawartość JSON, wykonaj jedną z następujących czynności:

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **Nowy krok**.

     ![Wybierz "Nowy krok" dla akcji "Parse JSON"](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik**+** myszy na strzałkę łączącą, aby pojawić się znak plus ( ). Zaznacz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania `parse json` wprowadź jako filtr. Z listy akcji wybierz akcję **Analizuj JSON.**

   ![Wybierz akcję "Analizuj JSON"](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. W polu **Zawartość** podaj zawartość JSON, którą chcesz przeanalizować.

   W tym przykładzie po kliknięciu wewnątrz pola **Zawartość** zostanie wyświetlona dynamiczna lista zawartości, dzięki czemu można wybrać poprzednio utworzoną zmienną:

   ![Wybierz obiekt JSON dla analizy akcji JSON](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Wprowadź schemat JSON opisujący zawartość JSON, którą analizujesz.

   W tym przykładzie oto schemat JSON:

   ![Podaj schemat JSON dla obiektu JSON, który chcesz przeanalizować](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Jeśli nie masz schematu, można wygenerować ten schemat z zawartości JSON lub *ładunku*, analizujesz. 
   
   1. W akcji **Analizowanie JSON** wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

   1. W obszarze **Wprowadzanie lub wklejanie przykładowego ładunku JSON**podaj zawartość JSON, a następnie wybierz pozycję **Gotowe**.

      ![Wprowadź zawartość JSON do generowania schematu](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji na temat tej akcji w podstawowej definicji przepływu pracy, zobacz [Parse JSON akcji](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby potwierdzić, czy **parse JSON** akcji tworzy oczekiwane wyniki, wyślij sobie powiadomienie, które zawiera dane wyjściowe z **parse JSON** akcji.

1. W aplikacji logiki dodaj akcję, która może wysyłać wyniki z **parse JSON** akcji.

1. W tej akcji kliknij dowolne miejsce, w które mają być wyświetlane wyniki. Po otwarciu dynamicznej listy zawartości w ramach akcji **Analizuj JSON** można teraz wybrać właściwości z przeanalizowanej zawartości JSON.

   W tym przykładzie użyto akcji Wyślij **wiadomość e-mail** w programie Office 365 Outlook i uwzględniono pola **Imię,** **Nazwisko**i **Poczta e-mail** w treści wiadomości e-mail:

   ![Właściwości JSON w akcji "Wyślij wiadomość e-mail"](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Oto gotowa akcja e-mail:

   ![Ukończony przykład akcji e-mail](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Teraz ręcznie uruchom aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**. 

   Na podstawie używanego łącznika wiadomości e-mail, oto wyniki, które otrzymujesz:

   ![Wyślij wiadomość e-mail z wynikami akcji "Analizuj JSON"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Wybierz akcję

Aby utworzyć tablicę, która ma obiekty JSON zbudowane na podstawie wartości w istniejącej tablicy, należy użyć **select** akcji. Na przykład można utworzyć obiekt JSON dla każdej wartości w tablicy liczby całkowitej, określając właściwości, które każdy obiekt JSON musi mieć i jak mapować wartości w tablicy źródłowej na te właściwości. I chociaż można zmienić składniki w tych obiektach JSON, tablica wyjściowa zawsze ma taką samą liczbę elementów jak tablica źródłowa.

> [!NOTE]
> Aby akcje mogły używać danych wyjściowych tablicy z akcji **Select,** te akcje muszą akceptować tablice jako dane wejściowe lub może być konieczne przekształcenie tablicy wyjściowej w inny zgodny format. 

Jeśli wolisz pracować w edytorze widoku kodu, możesz skopiować **przykładowe** definicje akcji Select i **Initialize z** tego artykułu do podstawowej definicji przepływu pracy aplikacji logiki: [przykłady kodu operacji danych — wybierz](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. W [witrynie Azure portal](https://portal.azure.com) lub visual studio otwórz aplikację logiki w logice App Designer.

   W tym przykładzie użyto witryny Azure portal i aplikacji logiki z **wyzwalaczem cyklu** i **inicjuj akcję zmiennej.** Akcja jest skonfigurowana do tworzenia zmiennej, której wartość początkowa jest tablicą zawierającą kilka przykładowych całkowitych. Po późniejszym przetestowaniu aplikacji logiki można ręcznie uruchomić aplikację bez oczekiwania na uruchomienie wyzwalacza.

   ![Uruchamianie przykładowej aplikacji logiki dla akcji "Wybierz"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. W aplikacji logiki, w której chcesz utworzyć tablicę, wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **Nowy krok**.

     ![Wybierz "Nowy krok" dla akcji "Wybierz"](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik**+** myszy na strzałkę łączącą, aby pojawić się znak plus ( ). Zaznacz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**wybierz pozycję **Wbudowane**. W polu wyszukiwania `select` wprowadź jako filtr. Z listy akcje wybierz akcję **Wybierz.**

   ![Wybierz akcję "Wybierz"](./media/logic-apps-perform-data-operations/select-select-action.png)

1. W polu **Od** określ odpowiednią tablicę źródłową.

   W tym przykładzie po kliknięciu wewnątrz pola **Od** zostanie wyświetlona lista zawartości dynamicznej, dzięki czemu można wybrać poprzednio utworzoną zmienną:

   ![Wybierz tablicę źródłą dla akcji Wybierz](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. W lewej kolumnie pola **Mapa** podaj nazwę właściwości, którą chcesz przypisać każdą wartość w tablicy źródłowej. W prawej kolumnie określ wyrażenie reprezentujące wartość, którą chcesz przypisać właściwość.

   W tym przykładzie określa "Product_ID" jako nazwę właściwości, aby przypisać każdą wartość `item()` w tablicy liczby całkowitej przy użyciu funkcji w wyrażeniu, które uzyskuje dostęp do każdego elementu tablicy. 

   ![Określanie właściwości i wartości obiektu JSON w celu utworzenia tablicy](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Oto ukończona akcja:

   ![Gotowy przykład akcji "Wybierz"](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji na temat tej akcji w podstawowej definicji przepływu pracy, zobacz [Wybieranie akcji](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby potwierdzić, czy **Select** akcji tworzy oczekiwane wyniki, wyślij sobie powiadomienie, które zawiera dane wyjściowe z **Select** akcji.

1. W aplikacji logiki dodaj akcję, która może wysyłać wyniki z **select** akcji.

1. W tej akcji kliknij dowolne miejsce, w które mają być wyświetlane wyniki. Po otwarciu dynamicznej listy zawartości wybierz pozycję **Wyrażenie**. Aby uzyskać dane wyjściowe tablicy z akcji **Wybierz,** wprowadź to wyrażenie, które zawiera nazwę akcji **Wybierz:**

   `@actionBody('Select')`

   W tym przykładzie użyto akcji **Wyślij wiadomość e-mail** w `@actionBody('Select')` programie Office 365 Outlook i uwzględniono dane wyjściowe z wyrażenia w treści wiadomości e-mail:

   ![Wyjścia akcji z akcji "Wybierz"](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Teraz ręcznie uruchom aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   Na podstawie używanego łącznika wiadomości e-mail, oto wyniki, które otrzymujesz:

   ![Wyślij wiadomość e-mail z wynikami akcji "Wybierz"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [łącznikach aplikacji logiki](../connectors/apis-list.md)
