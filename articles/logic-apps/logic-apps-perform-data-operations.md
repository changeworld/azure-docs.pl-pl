---
title: Wykonywanie operacji na danych — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Konwertuj, zarządzanie i modyfikować dane wyjściowe i formatów w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 93c24f88fcd6a002493933ef71c5c80bd2ff8c10
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206211"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Wykonaj operacje na danych w usłudze Azure Logic Apps

W tym artykule pokazano, jak można pracować z danymi w aplikacjach logiki, dodawanie akcji dla tych zadań i nie tylko:

* Tworzenie tabel z tablic.
* Tworzenie tablic z innych tablic, na podstawie warunku.
* Tworzenie tokenów przyjazny dla użytkownika z właściwości obiektu JavaScript Object Notation (JSON), dzięki czemu można łatwo użyć tych właściwości w przepływie pracy.

Jeśli nie możesz znaleźć akcję, która ma w tym miejscu, spróbuj wiele różnych [funkcje manipulowania danymi](../logic-apps/workflow-definition-language-functions-reference.md) zapewniająca Logic Apps. 

Te tabele zawierają podsumowanie dane, które operacje, można użyć i są zorganizowane na podstawie typów danych źródłowych, które operacje działają na, ale każdy opis pojawia się w kolejności alfabetycznej.

**Akcje tablicy** 

Te akcje ułatwiają pracę z danymi w tablicach.

| Akcja | Opis | 
|--------|-------------| 
| [**Utwórz tabelę CSV**](#create-csv-table-action) | Utwórz tabelę wartości rozdzielanych przecinkami (CSV) z tablicy. | 
| [**Utwórz tabelę HTML**](#create-html-table-action) | Utwórz tabelę HTML z tablicy. | 
| [**Filtruj tablicę**](#filter-array-action) | Utwórz podzbiór tablicy z tablicy, na podstawie określonego filtra lub warunku. | 
| [**Join**](#join-action) | Tworzenie ciągu z wszystkich elementów w tablicy i Oddziel poszczególne elementy określonego znaku. | 
| [**Select**](#select-action) | Utwórz tablicę ze określonych właściwości dla wszystkich elementów w innej tablicy. | 
||| 

**Akcje JSON**

Działania te ułatwiają pracę z danymi w formacie JavaScript Object Notation (JSON).

| Akcja | Opis | 
|--------|-------------| 
| [**Compose**](#compose-action) | Tworzenie wiadomości lub ciąg, z wielu danych wejściowych, które mogą mieć różnych typów danych. Następnie można użyć tego ciągu jako pojedynczy dane wejściowe, a nie wielokrotnego wprowadzania tych samych danych wejściowych. Na przykład można utworzyć pojedynczy komunikat JSON z różnych danych wejściowych. | 
| [**Parse JSON**](#parse-json-action) | Utwórz tokeny przyjazny dla użytkownika danych dla właściwości w formacie JSON zawartości, dzięki czemu łatwiej można użyć właściwości w aplikacjach logiki. | 
||| 

Aby utworzyć bardziej złożonych przekształceń JSON, zobacz [wykonaj zaawansowane przekształceń JSON i łącznika Liquid szablony](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z przykładów w tym artykule, będą potrzebne następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure <a href="https://azure.microsoft.com/free/" target="_blank">Załóż bezpłatne konto platformy Azure</a>.

* Aplikacja logiki gdzie potrzebujesz operacji do pracy z danymi 

  Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako pierwszy krok w aplikacji logiki 

  Operacje na danych są dostępne tylko akcje, więc zanim będzie można użyć tych akcji uruchomić swoją aplikację logiki z wyzwalaczem i inne akcje wymagane do utworzenia dane wyjściowe, które chcesz uwzględnić.

<a name="compose-action"></a>

## <a name="compose-action"></a>Akcja redagowania

Do utworzenia pojedynczego wyjścia, takich jak obiekt JSON z wielu danych wejściowych, można użyć **operacje danych — Redaguj** akcji. Dane wejściowe mogą mieć różnych typów, takie jak liczb całkowitych, wartości logicznych, tablice, obiekty JSON i innych native wpisz ten obsługuje usługi Azure Logic Apps, na przykład binarne i XML. Następnie wykorzystywać dane wyjściowe w akcjach, które należy wykonać po **Compose** akcji. **Compose** akcji można także zapisać możesz wielokrotnego wprowadzania tych samych wejściach, podczas tworzenia przepływu pracy aplikacji logiki. 

Na przykład można utworzyć komunikat JSON z wielu zmiennych, takich jak zmiennych ciągu, które przechowują imion i nazwisk osób, a zmienna typu Liczba całkowita, która przechowuje wieku osób. W tym miejscu **Compose** akcji akceptuje te dane wejściowe:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

i tworzy następujące dane wyjściowe:

`{"age":35,"fullName":"Owens,Sophie"}`

Aby wypróbować przykładową, wykonaj następujące czynności za pomocą projektanta aplikacji logiki. Lub, jeśli wolisz, Praca w edytorze widoku kodu, możesz skopiować przykładu **Compose** i **inicjowane zmiennej** przepływu pracy podstawowej definicji działania z tego artykułu w aplikacji logiki Definicja: [Przykłady kodu operacji danych - Compose](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a> lub Visual Studio, Otwórz aplikację logiki w Projektancie aplikacji logiki. 

   W tym przykładzie użyto portalu Azure i aplikacji logiki z **cyklu** wyzwalacza i kilka **inicjowane zmiennej** akcji. 
   Te akcje są konfigurowane do tworzenia dwóch zmiennych ciągu oraz zmienna typu Liczba całkowita. Gdy później przetestuj aplikację logiki, możesz ręcznie uruchomić aplikację bez konieczności oczekiwania na Aby uruchomić wyzwalacz.

   ![Począwszy od przykładowej aplikacji logiki](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

2. W aplikacji logiki gdzie chcesz utworzyć dane wyjściowe wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz **nowy krok** > **Dodaj akcję**.

     ![Dodaj akcję](./media/logic-apps-perform-data-operations/add-compose-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy strzałkę łączącego się więc pojawia się znak plusa (+). 
   Wybierz znak plusa, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "compose" jako filtr. Z listy akcji wybierz następującą akcję: **Redaguj**

   ![Wybierz akcję "Utwórz"](./media/logic-apps-perform-data-operations/select-compose-action.png)

4. W **dane wejściowe** Podaj dane wejściowe dla tworzenia danych wyjściowych. 

   W tym przykładzie po kliknięciu wewnątrz **dane wejściowe** , lista zawartości dynamicznej pojawi się okno, można wybrać wcześniej utworzony zmiennych:

   ![Zaznacz dane wejściowe do redagowania](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Oto przykład Zakończono **Compose** akcji: 

   ![Zakończono akcję "Utwórz"](./media/logic-apps-perform-data-operations/finished-compose-action.png)

5. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji dotyczących tej akcji w podstawowej definicji przepływu pracy, zobacz [Akcja redagowania](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action). 

### <a name="test-your-logic-app"></a>Przetestuj aplikację logiki

Aby upewnić się, czy **Compose** akcja powoduje utworzenie oczekiwanych rezultatów, Wyślij do siebie powiadomienie, które zawiera dane wyjściowe z **Compose** akcji.

1. W aplikacji logiki, Dodaj akcję, którą można wysłać wyniki z **Compose** akcji.

2. Ta akcja kliknij gdziekolwiek mają być wyświetlane wyniki. Jeśli lista zawartości dynamicznej otwiera w obszarze **Compose** akcji, wybierz opcję **dane wyjściowe**. 

   W tym przykładzie użyto **Office 365 Outlook — Wyślij wiadomość e-mail** akcji i zawiera **dane wyjściowe** pól w treści i tematu wiadomości e-mail:

   ![Pola "Wyjście" w akcji "Wyślij wiadomość e-mail"](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

3. Teraz ręcznie uruchomić swoją aplikację logiki. Na pasku narzędzi Projektanta wybierz **Uruchom**. 

   Oparte na Łącznik poczty e-mail, którego użyłeś, Oto wyniki Ci:

   ![Wyślij wiadomość e-mail z wynikami akcji "Utwórz"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Akcji tworzenia tabeli CSV

Aby utworzyć tabelę wartości rozdzielanych przecinkami (CSV) zawierający właściwości i wartości z JavaScript Object Notation (JSON) obiektów w tablicy, należy użyć **operacje na danych — Utwórz tabelę CSV** akcji. Można następnie użyć tabeli wynikowej w akcjach, które należy wykonać **Utwórz tabelę CSV** akcji. 

Jeśli wolisz, Praca w edytorze widoku kodu, możesz skopiować przykładu **Utwórz tabelę CSV** i **inicjowane zmiennej** przepływu pracy podstawowej definicji działania z tego artykułu w aplikacji logiki Definicja: [Przykłady kodu operacji danych — Utwórz tabelę CSV](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example) 

1. W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a> lub Visual Studio, Otwórz aplikację logiki w Projektancie aplikacji logiki. 

   W tym przykładzie użyto portalu Azure i aplikacji logiki z **cyklu** wyzwalacza i **inicjowane zmiennej** akcji. 
   Akcja jest skonfigurowany dla tworzenia zmiennej, którego wartość początkowa to tablica, która ma kilka właściwości i wartości w formacie JSON. 
   Gdy później przetestuj aplikację logiki, możesz ręcznie uruchomić aplikację bez konieczności oczekiwania na Aby uruchomić wyzwalacz.

   ![Począwszy od przykładowej aplikacji logiki](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. W aplikacji logiki gdzie chcesz utworzyć tabelę CSV wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz **nowy krok** > **Dodaj akcję**.

     ![Dodaj akcję](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy strzałkę łączącego się więc pojawia się znak plusa (+). 
   Wybierz znak plusa, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "Utwórz tabelę csv" jako filtr. Z listy akcji wybierz następującą akcję: **Utwórz tabelę CSV**

   ![Wybierz pozycję "Utwórz CSV tabeli" Akcja](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

4. W **z** Podaj tablicy lub wyrażenie dla tworzenia tabeli. 

   W tym przykładzie po kliknięciu wewnątrz **z** , lista zawartości dynamicznej pojawi się okno, można wybrać wcześniej utworzony zmiennej:

   ![Wybierz dane wyjściowe tablicy, do tworzenia tabeli CSV](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   Oto przykład Zakończono **Utwórz tabelę CSV** akcji: 

   ![Zakończono "CSV Utwórz tabelę" akcji](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

   Domyślnie ta akcja powoduje automatyczne utworzenie kolumn na podstawie elementów tablicy. 
   Aby ręcznie utworzyć nagłówki kolumn i wartości, wybierz **Pokaż opcje zaawansowane**. 
   Aby podać tylko wartości niestandardowych, należy zmienić **kolumn** do **niestandardowe**. 
   Aby zapewnić zbyt nagłówków kolumn niestandardowych, należy zmienić **zawierają nagłówki** do **tak**. 

   > [!TIP]
   > Aby utworzyć czytelne tokenów dla właściwości w obiekty JSON, te właściwości można wybrać jako dane wejściowe, użyj [Przeanalizuj dane JSON](#parse-json-action) przed wywołaniem **Utwórz tabelę CSV** akcji.

5. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji dotyczących tej akcji w podstawowej definicji przepływu pracy, zobacz [Akcja tabeli](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Przetestuj aplikację logiki

Aby upewnić się, czy **Utwórz tabelę CSV** akcja powoduje utworzenie oczekiwanych rezultatów, Wyślij do siebie powiadomienie, które zawiera dane wyjściowe z **Utwórz tabelę CSV** akcji.

1. W aplikacji logiki, Dodaj akcję, którą można wysłać wyniki z **Utwórz tabelę CSV** akcji.

2. Ta akcja kliknij gdziekolwiek mają być wyświetlane wyniki. Jeśli lista zawartości dynamicznej otwiera w obszarze **Utwórz tabelę CSV** akcji wybierz **dane wyjściowe**. 

   W tym przykładzie użyto **Office 365 Outlook — Wyślij wiadomość e-mail** akcji i zawiera **dane wyjściowe** w treści wiadomości e-mail:

   ![Pola "Wyjście" w akcji "Wyślij wiadomość e-mail"](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

3. Teraz ręcznie uruchomić swoją aplikację logiki. Na pasku narzędzi Projektanta wybierz **Uruchom**. 

   Oparte na Łącznik poczty e-mail, którego użyłeś, Oto wyniki Ci:

   ![Wiadomość e-mail z "CSV Utwórz tabelę" wyników akcji](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Utwórz akcję tabeli HTML

Aby utworzyć tabelę HTML, który ma właściwości i wartości z JavaScript Object Notation (JSON) obiektów w tablicy, należy użyć **operacje na danych — Utwórz tabelę HTML** akcji. Można następnie użyć tabeli wynikowej w akcjach, które należy wykonać **Utwórz tabelę HTML** akcji.

Jeśli wolisz, Praca w edytorze widoku kodu, możesz skopiować przykładu **Utwórz tabelę HTML** i **inicjowane zmiennej** przepływu pracy podstawowej definicji działania z tego artykułu w aplikacji logiki Definicja: [Przykłady kodu operacji danych — Utwórz tabelę HTML](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a> lub Visual Studio, Otwórz aplikację logiki w Projektancie aplikacji logiki. 

   W tym przykładzie użyto portalu Azure i aplikacji logiki z **cyklu** wyzwalacza i **inicjowane zmiennej** akcji. 
   Akcja jest skonfigurowany dla tworzenia zmiennej, którego wartość początkowa to tablica, która ma kilka właściwości i wartości w formacie JSON. 
   Gdy później przetestuj aplikację logiki, możesz ręcznie uruchomić aplikację bez konieczności oczekiwania na Aby uruchomić wyzwalacz.

   ![Począwszy od przykładowej aplikacji logiki](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. W aplikacji logiki gdzie chcesz utworzyć tabelę HTML wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz **nowy krok** > **Dodaj akcję**.

     ![Dodaj akcję](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy strzałkę łączącego się więc pojawia się znak plusa (+). 
   Wybierz znak plusa, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "Utwórz tabelę html" jako filtr. Z listy akcji wybierz następującą akcję: **Utwórz tabelę HTML**

   ![Wybierz akcję "Utwórz HTML tabelę"](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

4. W **z** Podaj tablicy lub wyrażenie dla tworzenia tabeli. 

   W tym przykładzie po kliknięciu wewnątrz **z** , lista zawartości dynamicznej pojawi się okno, można wybrać wcześniej utworzony zmiennej:

   ![Wybierz dane wyjściowe tablicy, do tworzenia tabeli HTML](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   Oto przykład Zakończono **Utwórz tabelę HTML** akcji: 

   ![Zakończono akcję "Utwórz HTML tabelę"](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

   Domyślnie ta akcja powoduje automatyczne utworzenie kolumn na podstawie elementów tablicy. 
   Aby ręcznie utworzyć nagłówki kolumn i wartości, wybierz **Pokaż opcje zaawansowane**. 
   Aby podać tylko wartości niestandardowych, należy zmienić **kolumn** do **niestandardowe**. 
   Aby zapewnić zbyt nagłówków kolumn niestandardowych, należy zmienić **zawierają nagłówki** do **tak**. 

   > [!TIP]
   > Aby utworzyć czytelne tokenów dla właściwości w obiekty JSON, te właściwości można wybrać jako dane wejściowe, użyj [Przeanalizuj dane JSON](#parse-json-action) przed wywołaniem **Utwórz tabelę HTML** akcji.

5. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji dotyczących tej akcji w podstawowej definicji przepływu pracy, zobacz [Akcja tabeli](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Przetestuj aplikację logiki

Aby upewnić się, czy **Utwórz tabelę HTML** akcja powoduje utworzenie oczekiwanych rezultatów, Wyślij do siebie powiadomienie, które zawiera dane wyjściowe z **Utwórz tabelę HTML** akcji.

1. W aplikacji logiki, Dodaj akcję, którą można wysłać wyniki z **Utwórz tabelę HTML** akcji.

2. Ta akcja kliknij gdziekolwiek mają być wyświetlane wyniki. Jeśli lista zawartości dynamicznej otwiera w obszarze **Utwórz tabelę HTML** akcji wybierz **dane wyjściowe**. 

   W tym przykładzie użyto **Office 365 Outlook — Wyślij wiadomość e-mail** akcji i zawiera **dane wyjściowe** w treści wiadomości e-mail:

   ![Pola "Wyjście" w akcji "Wyślij wiadomość e-mail"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)
   
   > [!NOTE]
   > Po tym tabeli HTML dane wyjściowe w akcji poczty e-mail, upewnij się, możesz ustawić **HTML jest** właściwości **tak** akcji w wiadomości e-mail zaawansowane opcje. W ten sposób akcji w wiadomości e-mail poprawnie formatuje tabelę HTML.

3. Teraz ręcznie uruchomić swoją aplikację logiki. Na pasku narzędzi Projektanta wybierz **Uruchom**. 

   Oparte na Łącznik poczty e-mail, którego użyłeś, Oto wyniki Ci:

   ![Wyślij wiadomość e-mail z wynikami akcji "Utwórz HTML tabelę"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Akcji filtrowania tablicy

Aby utworzyć mniejsze tablica, która zawiera elementy, które spełniają określone kryteria, z istniejącej tablicy, należy użyć **operacje na danych — Filtruj tablicę** akcji. Następnie można użyć tablicy filtrowane w akcjach, które należy wykonać po **Filtruj tablicę** akcji. 

> [!NOTE]
> Dowolny tekst filtr, którego używasz w warunku jest uwzględniana wielkość liter. Ponadto tej akcji nie można zmienić format lub składniki elementów w tablicy. 
> 
> Dla akcji umożliwiających korzystanie z danych wyjściowych tablicy **Filtruj tablicę** akcji, te akcje muszą zaakceptować tablic jako dane wejściowe lub może być konieczne przekształcenie tablicy danych wyjściowych w innym formacie zgodne. 

Jeśli wolisz, Praca w edytorze widoku kodu, możesz skopiować przykładu **Filtruj tablicę** i **inicjowane zmiennej** przepływu pracy podstawowej definicji działania z tego artykułu w aplikacji logiki Definicja: [Przykłady kodu operacji danych — Filtruj tablicę](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example) 

1. W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a> lub Visual Studio, Otwórz aplikację logiki w Projektancie aplikacji logiki. 

   W tym przykładzie użyto portalu Azure i aplikacji logiki z **cyklu** wyzwalacza i **inicjowane zmiennej** akcji. 
   Akcja jest skonfigurowany dla tworzenia zmiennej, którego wartość początkowa to tablica, która zawiera niektóre przykładowe liczb całkowitych. Gdy później przetestuj aplikację logiki, możesz ręcznie uruchomić aplikację bez konieczności oczekiwania na Aby uruchomić wyzwalacz.

   > [!NOTE]
   > Mimo że w tym przykładzie użyto tablicy liczb całkowitych prosty, ta akcja jest szczególnie przydatne w przypadku obiektu JSON, który tablic, w którym można filtrować na podstawie obiektów właściwości i wartości.

   ![Począwszy od przykładowej aplikacji logiki](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

2. W aplikacji logiki gdzie chcesz utworzyć tablicę filtrowane wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz **nowy krok** > **Dodaj akcję**.

     ![Dodaj akcję](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy strzałkę łączącego się więc pojawia się znak plusa (+). 
   Wybierz znak plusa, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "Filtruj tablicę" jako filtr. Z listy akcji wybierz następującą akcję: **Filtruj tablicę**

   ![Wybieranie akcji "Filtrowanie tablicy"](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

4. W **z** Podaj tablicy lub wyrażenie, które chcesz filtrować. 

   W tym przykładzie po kliknięciu wewnątrz **z** , lista zawartości dynamicznej pojawi się okno, można wybrać wcześniej utworzony zmiennej:

   ![Wybierz wyjścia tablicy do utworzenia tablicy filtrowane](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

5. Warunek Określ elementów tablicy, aby porównać, wybierz operator porównania, a następnie podaj wartość porównania.

   W tym przykładzie użyto **item()** funkcji do uzyskiwania dostępu do każdego elementu w tablicy podczas **Filtruj tablicę** wyszukiwania akcji dla tablicy elementów, których wartość jest większa niż 1:
   
   ![Zakończono akcji "Filtrowanie tablicy"](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

6. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji dotyczących tej akcji w podstawowej definicji przepływu pracy, zobacz [Akcja zapytania](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Przetestuj aplikację logiki

Aby upewnić się, czy **Filtruj tablicę** akcja powoduje utworzenie oczekiwanych rezultatów, Wyślij do siebie powiadomienie, które zawiera dane wyjściowe z **Filtruj tablicę** akcji.

1. W aplikacji logiki, Dodaj akcję, którą można wysłać wyniki z **Filtruj tablicę** akcji.

2. Ta akcja kliknij gdziekolwiek mają być wyświetlane wyniki. Po otwarciu listy zawartości dynamicznej wybierz **wyrażenie**. Aby uzyskać dane wyjściowe tablicy z **Filtruj tablicę** akcji, wprowadź następujące wyrażenie, który zawiera **Filtruj tablicę** nazwę akcji:

   ```
   @actionBody('Filter_array')
   ```

   W tym przykładzie użyto **Office 365 Outlook — Wyślij wiadomość e-mail** akcji i zawiera dane wyjściowe z **actionBody('Filter_array')** wyrażeń w treści wiadomości e-mail:

   ![W akcji "Wyślij wiadomość e-mail" dane wyjściowe akcji](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

3. Teraz ręcznie uruchomić swoją aplikację logiki. Na pasku narzędzi Projektanta wybierz **Uruchom**. 

   Oparte na Łącznik poczty e-mail, którego użyłeś, Oto wyniki Ci:

   ![Wyślij wiadomość e-mail z wynikami akcji "Filtruj tablicę"](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Dołącz do akcji

Aby utworzyć ciąg, który zawiera wszystkie elementy z tablicy i tych elementów należy oddzielić znak ogranicznika określonego, użyj **operacje na danych — Połącz** akcji. Można następnie używać ciągu w akcjach, które należy wykonać po **Dołącz** akcji.

Jeśli wolisz, Praca w edytorze widoku kodu, możesz skopiować przykładu **Dołącz** i **inicjowane zmiennej** definicji działania z tego artykułu w aplikacji logiki podstawowej definicji przepływu pracy: [Przykłady kodu operacji danych — sprzężenia](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example) 

1. W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a> lub Visual Studio, Otwórz aplikację logiki w Projektancie aplikacji logiki. 

   W tym przykładzie użyto portalu Azure i aplikacji logiki z **cyklu** wyzwalacza i **inicjowane zmiennej** akcji. 
   Ta akcja jest skonfigurowany dla tworzenia zmiennej, którego wartość początkowa to tablica, która zawiera niektóre przykładowe liczb całkowitych. 
   Przetestuj aplikację logiki w dalszej części można ręcznie uruchomić aplikację, bez konieczności oczekiwania na Aby uruchomić wyzwalacz.

   ![Począwszy od przykładowej aplikacji logiki](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

2. W aplikacji logiki gdzie chcesz utworzyć ciągu z tablicy wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz **nowy krok** > **Dodaj akcję**.

     ![Dodaj akcję](./media/logic-apps-perform-data-operations/add-join-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy strzałkę łączącego się więc pojawia się znak plusa (+). 
   Wybierz znak plusa, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "join" jako filtr. Z listy akcji wybierz następującą akcję: **Join**

   ![Wybierz akcję "Danych operacji Join —"](./media/logic-apps-perform-data-operations/select-join-action.png)

4. W **z** wprowadź tablicę, która zawiera elementy, aby dołączyć jako ciąg. 

   W tym przykładzie po kliknięciu wewnątrz **z** pole listy zawartości dynamicznej, który pojawia się, można wybrać zmiennej utworzonej wcześniej:  

   ![Wybierz dane wyjściowe tablicy, do tworzenia ciągu](./media/logic-apps-perform-data-operations/configure-join-action.png)

5. W **członkiem społeczności użytkowników na** Wprowadź znak ma do oddzielania każdego elementu tablicy. 

   W tym przykładzie użyto dwukropek (:) jako separatora.

   ![Podaj znak separatora](./media/logic-apps-perform-data-operations/finished-join-action.png)

6. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji dotyczących tej akcji w podstawowej definicji przepływu pracy, zobacz [Dołącz akcji](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Przetestuj aplikację logiki

Aby upewnić się, czy **Dołącz** akcja powoduje utworzenie oczekiwanych rezultatów, Wyślij do siebie powiadomienie, które zawiera dane wyjściowe z **Dołącz** akcji. 

1. W aplikacji logiki, Dodaj akcję, którą można wysłać wyniki z **Dołącz** akcji.

2. Ta akcja kliknij gdziekolwiek mają być wyświetlane wyniki. Jeśli lista zawartości dynamicznej otwiera w obszarze **Dołącz** akcji, wybierz opcję **dane wyjściowe**. 

   W tym przykładzie użyto **Office 365 Outlook — Wyślij wiadomość e-mail** akcji i zawiera **dane wyjściowe** w treści wiadomości e-mail:

   ![Pola "Wyjście" w akcji "Wyślij wiadomość e-mail"](./media/logic-apps-perform-data-operations/send-email-join-action.png)

3. Teraz ręcznie uruchomić swoją aplikację logiki. Na pasku narzędzi Projektanta wybierz **Uruchom**. 

   Oparte na Łącznik poczty e-mail, którego użyłeś, Oto wyniki Ci:

   ![Wyślij wiadomość e-mail z wynikami akcji "Join"](./media/logic-apps-perform-data-operations/join-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Analizowanie JSON akcji

Odwołanie i dostęp do właściwości w JavaScript Object Notation (JSON) zawartości pola przyjazny dla użytkownika lub tokenów dla tych właściwości można utworzyć przy użyciu **operacje na danych — Przeanalizuj dane JSON** akcji.
W ten sposób można wybrać te właściwości z listy zawartości dynamicznej po określeniu dane wejściowe dla twojej aplikacji logiki. Dla tej akcji możesz podać schematu JSON lub generowanie schematu JSON z przykładowej zawartości JSON lub ładunku.

Jeśli wolisz, Praca w edytorze widoku kodu, możesz skopiować przykładu **Przeanalizuj dane JSON** i **inicjowane zmiennej** definicji działania z tego artykułu w aplikacji logiki podstawowej definicji przepływu pracy : [Przykłady kodu operacji danych — Przeanalizuj dane JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example) 

1. W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a> lub Visual Studio, Otwórz aplikację logiki w Projektancie aplikacji logiki. 

   W tym przykładzie użyto portalu Azure i aplikacji logiki z **cyklu** wyzwalacza i **inicjowane zmiennej** akcji. 
   Akcja jest skonfigurowany dla tworzenia zmiennej, którego wartość początkowa to obiekt JSON, który zawiera właściwości i wartości. 
   Gdy później przetestuj aplikację logiki, możesz ręcznie uruchomić aplikację bez konieczności oczekiwania na Aby uruchomić wyzwalacz.

   ![Począwszy od przykładowej aplikacji logiki](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

2. W aplikacji logiki gdzie chcesz przeanalizować zawartość JSON wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz **nowy krok** > **Dodaj akcję**.

     ![Dodaj akcję](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy strzałkę łączącego się więc pojawia się znak plusa (+). 
   Wybierz znak plusa, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "Przeanalizuj dane json" jako filtr. Z listy akcji wybierz następującą akcję: **Przeanalizuj dane JSON**

   ![Wybierz akcję "Przeanalizuj dane JSON"](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

4. W **zawartości** Podaj zawartość JSON, aby przeanalizować. 

   W tym przykładzie po kliknięciu wewnątrz **zawartości** , lista zawartości dynamicznej pojawi się okno, można wybrać wcześniej utworzony zmiennej:

   ![Wybierz obiekt JSON dla akcji analizy danych JSON](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

5. Wprowadź schemat JSON opisujące zawartość JSON, który jest analiza kodu. 

   Na przykład Oto schematu JSON:

   ![Podaj obiekt JSON, który chcesz przeanalizować schematu JSON](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Jeśli nie masz schematu z zawartość JSON można wygenerować schemat lub *ładunku*, są analizy. 
   
   1. W **Przeanalizuj dane JSON** akcji wybierz **Użyj przykładowego ładunku do wygenerowania schematu**.

   2. W obszarze **wpisz lub wklej przykładowy ładunek JSON**Podaj zawartość JSON, a następnie wybierz **gotowe**.

      ![Wprowadź zawartość JSON do generowania schematu](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

6. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji dotyczących tej akcji w podstawowej definicji przepływu pracy, zobacz [akcji analizy danych JSON](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Przetestuj aplikację logiki

Aby upewnić się, czy **Przeanalizuj dane JSON** akcja powoduje utworzenie oczekiwanych rezultatów, Wyślij do siebie powiadomienie, które zawiera dane wyjściowe z **Przeanalizuj dane JSON** akcji.

1. W aplikacji logiki, Dodaj akcję, którą można wysłać wyniki z **Przeanalizuj dane JSON** akcji.

2. Ta akcja kliknij gdziekolwiek mają być wyświetlane wyniki. Jeśli lista zawartości dynamicznej otwiera w obszarze **Przeanalizuj dane JSON** działania, można teraz wybrać właściwości z przeanalizowanego zawartość JSON.

   W tym przykładzie użyto **Office 365 Outlook — Wyślij wiadomość e-mail** akcji i zawiera **FirstName**, **LastName**, i **E-mail** pola w Treść wiadomości e-mail:

   ![Właściwości JSON w akcji "Wyślij wiadomość e-mail"](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Oto akcji Zakończono poczty e-mail:

   ![Akcja zakończona poczty e-mail](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

3. Teraz ręcznie uruchomić swoją aplikację logiki. Na pasku narzędzi Projektanta wybierz **Uruchom**. 

   Oparte na Łącznik poczty e-mail, którego użyłeś, Oto wyniki Ci:

   ![Wyślij wiadomość e-mail z wynikami akcji "Join"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Wybierz akcję

Aby utworzyć tablicę z obiektami JSON utworzony na podstawie wartości w istniejącej tablicy, należy użyć **operacje na danych — wybierz** akcji. Na przykład można utworzyć obiekt JSON dla każdej wartości w tablicę liczb całkowitych, określając właściwości, które musi zawierać każdego obiektu JSON i sposób mapowania wartości w tablicy źródłowej do tych właściwości. I można zmienić składników w tych obiektów JSON, ale tablicy danych wyjściowych zawsze ma taką samą liczbę elementów jako tablica źródłowa.

> [!NOTE]
> Dla akcji umożliwiających korzystanie z danych wyjściowych tablicy **wybierz** akcji, te akcje muszą zaakceptować tablic jako dane wejściowe lub może być konieczne przekształcenie tablicy danych wyjściowych w innym formacie zgodne. 

Jeśli wolisz, Praca w edytorze widoku kodu, możesz skopiować przykładu **wybierz** i **inicjowane zmiennej** definicji działania z tego artykułu w aplikacji logiki podstawowej definicji przepływu pracy: [Przykłady kodu operacji danych — wybierz](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a> lub Visual Studio, Otwórz aplikację logiki w Projektancie aplikacji logiki. 

   W tym przykładzie użyto portalu Azure i aplikacji logiki z **cyklu** wyzwalacza i **inicjowane zmiennej** akcji. 
   Akcja jest skonfigurowany dla tworzenia zmiennej, którego wartość początkowa to tablica, która zawiera niektóre przykładowe liczb całkowitych. 
   Gdy później przetestuj aplikację logiki, możesz ręcznie uruchomić aplikację bez konieczności oczekiwania na Aby uruchomić wyzwalacz.

   ![Począwszy od przykładowej aplikacji logiki](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

2. W aplikacji logiki gdzie chcesz utworzyć tablicę wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz **nowy krok** > **Dodaj akcję**.

     ![Dodaj akcję](./media/logic-apps-perform-data-operations/add-select-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy strzałkę łączącego się więc pojawia się znak plusa (+). 
   Wybierz znak plusa, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "Wybierz" jako filtr. Z listy akcji wybierz następującą akcję: **Select**

   ![Wybieranie akcji "Select"](./media/logic-apps-perform-data-operations/select-select-action.png)

4. W **z** Określ ma tablica źródłowa.

   W tym przykładzie po kliknięciu wewnątrz **z** , lista zawartości dynamicznej pojawi się okno, można wybrać wcześniej utworzony zmiennej:

   ![Wybierz źródło tablicy dla akcji wybierania](./media/logic-apps-perform-data-operations/configure-select-action.png)

5. W **mapy** kolumny po lewej stronie pola, podaj nazwę właściwości, którą chcesz przypisać każdej wartości w tablicy źródłowej. W kolumnie po prawej stronie należy określić wyrażenie, które reprezentuje wartość, którą chcesz przypisać właściwości.

   W tym przykładzie określa "Product_ID" jako nazwy właściwości, aby przypisać każdej wartości w tablicy liczb całkowitych przy użyciu **item()** funkcji w wyrażeniu, który uzyskuje dostęp do każdego elementu tablicy. 

   ![Określ właściwość obiektu JSON i wartości dla tablicy, który chcesz utworzyć](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Oto Zakończono akcji:

   ![Zakończono akcji wybierania](./media/logic-apps-perform-data-operations/finished-select-action.png)

6. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji dotyczących tej akcji w podstawowej definicji przepływu pracy, zobacz [wybierz akcję](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Przetestuj aplikację logiki

Aby upewnić się, czy **wybierz** akcja powoduje utworzenie oczekiwanych rezultatów, Wyślij do siebie powiadomienie, które zawiera dane wyjściowe z **wybierz** akcji.

1. W aplikacji logiki, Dodaj akcję, którą można wysłać wyniki z **wybierz** akcji.

2. Ta akcja kliknij gdziekolwiek mają być wyświetlane wyniki. Po otwarciu listy zawartości dynamicznej wybierz **wyrażenie**. Aby uzyskać dane wyjściowe tablicy z **wybierz** akcji, wprowadź następujące wyrażenie, który zawiera **wybierz** nazwę akcji:

   ```
   @actionBody('Select')
   ```

   W tym przykładzie użyto **Office 365 Outlook — Wyślij wiadomość e-mail** akcji i zawiera dane wyjściowe z **actionBody('Select')** wyrażeń w treści wiadomości e-mail:

   ![W akcji "Wyślij wiadomość e-mail" dane wyjściowe akcji](./media/logic-apps-perform-data-operations/send-email-select-action.png)

3. Teraz ręcznie uruchomić swoją aplikację logiki. Na pasku narzędzi Projektanta wybierz **Uruchom**. 

   Oparte na Łącznik poczty e-mail, którego użyłeś, Oto wyniki Ci:

   ![Wyślij wiadomość e-mail z wynikami akcji "Select"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [łączników Logic Apps](../connectors/apis-list.md)
