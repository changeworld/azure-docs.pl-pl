---
title: Dodawanie i uruchamianie fragmentów kodu przy użyciu kodu wbudowanego
description: Dowiedz się, jak tworzyć i uruchamiać fragmenty kodu przy użyciu akcji kodu wbudowanego dla zautomatyzowanych zadań i przepływów pracy utworzonych za pomocą aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: f7a134fd026b42d1666b8310b3fb0c10642c7bb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453491"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Dodawanie i uruchamianie fragmentów kodu przy użyciu kodu wbudowanego w usłudze Azure Logic Apps

Jeśli chcesz uruchomić fragment kodu wewnątrz aplikacji logiki, można dodać wbudowaną akcję **kod wbudowany** jako krok w przepływie pracy aplikacji logiki. Ta akcja działa najlepiej, gdy chcesz uruchomić kod, który pasuje do tego scenariusza:

* Działa w języku JavaScript. Więcej języków już wkrótce.
* Kończy się w ciągu pięciu sekund lub mniej.
* Obsługuje dane o rozmiarze do 50 MB.
* Nie wymaga pracy z [ **zmiennymi** akcji](../logic-apps/logic-apps-create-variables-store-values.md), które nie są jeszcze obsługiwane.
* Używa node.js w wersji 8.11.1. Aby uzyskać więcej informacji, zobacz [Standardowe obiekty wbudowane](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects). 

  > [!NOTE]
  > Funkcja `require()` nie jest obsługiwana przez akcję **Kod wbudowany** do uruchamiania języka JavaScript.

Ta akcja uruchamia fragment kodu i zwraca dane wyjściowe z tego fragmentu kodu jako token o nazwie **Wynik**, którego można użyć w kolejnych akcjach w aplikacji logiki. W przypadku innych scenariuszy, w których chcesz utworzyć funkcję dla kodu, spróbuj [utworzyć i wywołać funkcję platformy Azure](../logic-apps/logic-apps-azure-functions.md) w aplikacji logiki.

W tym artykule przykładowa aplikacja logiki wyzwala po odebraniu nowej wiadomości e-mail z kontem programu Outlook usługi Office 365. Fragment kodu wyodrębnia i zwraca wszystkie adresy e-mail, które pojawiają się w treści wiadomości e-mail.

![Przykładowy przegląd](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki, w której chcesz dodać fragment kodu, w tym wyzwalacz. Jeśli nie masz aplikacji logiki, zobacz [Szybki start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Przykładowa aplikacja logiki w tym temacie używa tego wyzwalacza programu Office 365 Outlook: **po nadejściu nowej wiadomości e-mail**

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) połączone z aplikacją logiki

  > [!NOTE]
  > Upewnij się, że używasz konta integracji, które jest odpowiednie dla przypadku użycia lub scenariusza. Na przykład konta integracji [bezpłatnej warstwy](../logic-apps/logic-apps-pricing.md#integration-accounts) są przeznaczone tylko dla scenariuszy odkrywczych i obciążeń, a nie scenariuszy produkcji, są ograniczone w użyciu i przepływności i nie są obsługiwane przez umowę dotyczącą poziomu usług (SLA). Inne warstwy ponoszą koszty, ale obejmują obsługę umowy SLA, oferują większą przepustowość i mają wyższe limity. Dowiedz się więcej o [warstwach](../logic-apps/logic-apps-pricing.md#integration-accounts)kont integracji, [cenach](https://azure.microsoft.com/pricing/details/logic-apps/)i [limitach.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="add-inline-code"></a>Dodaj kod wbudowany

1. Jeśli jeszcze tego nie zrobiłeś, w [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki.

1. W projektancie dodaj akcję **Kod wbudowany** w lokalizacji, która ma zostać w przepływie pracy aplikacji logiki.

   * Aby dodać akcję na końcu przepływu pracy, wybierz pozycję **Nowy krok**.

   * Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę łączącą te kroki. Wybierz znak plus**+**( ) i wybierz pozycję **Dodaj akcję**.

   W tym przykładzie dodano akcję **Kod wbudowany w** ramach wyzwalacza programu Office 365 Outlook.

   ![Dodaj nowy krok](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. W obszarze **Wybierz akcję**w polu wyszukiwania wpisz "kod wbudowany" jako filtr. Z listy akcji wybierz tę akcję: **Wykonywanie kodu JavaScript**

   ![Wybierz "Wykonaj kod JavaScript"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   Akcja pojawia się w projektancie i zawiera kilka domyślny przykładowy kod, w tym return instrukcji.

   ![Akcja Kod wbudowany z domyślnym przykładowym kodem](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. W polu **Kod** usuń przykładowy kod i wprowadź kod, który chcesz uruchomić. Napisz kod, który można umieścić wewnątrz metody, ale bez definiowania podpisu metody. 

   Po wpisaniu rozpoznanego słowa kluczowego zostanie wyświetlona lista autouzupełniania, dzięki czemu można wybrać spośród dostępnych słów kluczowych, na przykład:

   ![Lista autouzupełniania słów kluczowych](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Ten przykładowy fragment kodu najpierw tworzy zmienną, która przechowuje *wyrażenie regularne*, który określa wzorzec do dopasowania w tekście wejściowym. Kod następnie tworzy zmienną, która przechowuje dane treści wiadomości e-mail z wyzwalacza.

   ![Tworzenie zmiennych](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Aby ułatwić odwoływanie się do wyników z wyzwalacza i poprzednich akcji, dynamiczna lista zawartości jest wyświetlana, gdy kursor znajduje się wewnątrz pola **Kod.** W tym przykładzie lista zawiera dostępne wyniki z wyzwalacza, w tym **body** token, który można teraz wybrać.

   Po wybraniu **body** token, akcja kodu wbudowanego rozpoznaje token do `workflowContext` obiektu, `Body` który odwołuje się do wartości właściwości wiadomości e-mail:

   ![Wybierz wynik](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   W polu **Kod** fragment kodu może używać obiektu `workflowContext` tylko do odczytu jako danych wejściowych. Ten obiekt ma właściwości podrzędne, które dają dostęp do kodu do wyników z wyzwalacza i poprzednich akcji w przepływie pracy.
   Aby uzyskać więcej informacji, zobacz tę sekcję w dalszej części tego tematu: [Wyzwalacz odwołań i działanie powoduje kod](#workflowcontext).

   > [!NOTE]
   >
   > Jeśli fragment kodu odwołuje się do nazw akcji, które używają operatora kropki (.), należy dodać te nazwy akcji do [parametru **Akcje** ](#add-parameters). Odwołania te muszą również zawierać nazwy akcji z nawiasami kwadratowymi ([]) i cudzysłowami, na przykład:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   Akcja kodu wbudowanego `return` nie wymaga instrukcji, ale wyniki `return` z instrukcji są dostępne do odwołania w późniejszych akcjach za pośrednictwem tokenu **Wynik.** 
   Na przykład fragment kodu zwraca wynik, wywołując `match()` funkcję, która znajduje dopasowania w treści wiadomości e-mail względem wyrażenia regularnego. Akcja **Redagowanie** używa **wynik** tokenu do odwoływania się do wyników z akcji kodu wbudowanego i tworzy pojedynczy wynik.

   ![Ukończona aplikacja logiki](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Po zakończeniu zapisz aplikację logiki.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Wyzwalacz odwołań i działanie powoduje, że kod

Obiekt `workflowContext` ma tę strukturę, `actions` `trigger`która `workflow` obejmuje , i podwładne:

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

Ta tabela zawiera więcej informacji na temat tych właściwości podrzędnych:

| Właściwość | Typ | Opis |
|----------|------|-------|
| `actions` | Kolekcja obiektów | Wynik obiektów z akcji, które są uruchamiane przed uruchomieniem fragmentu kodu. Każdy obiekt ma parę *klucz-wartość,* gdzie klucz jest nazwą akcji, a wartość jest równoważna wywoływaniu [funkcji actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions) za pomocą programu `@actions('<action-name>')`. Nazwa akcji używa tej samej nazwy akcji, która jest używana w podstawowej definicji przepływu pracy, która zastępuje spacje (" ") w nazwie akcji podkreśleniami (_). Ten obiekt zapewnia dostęp do wartości właściwości akcji z bieżącego uruchomienia wystąpienia przepływu pracy. |
| `trigger` | Obiekt | Wynik obiektu z wyzwalacza i równoważne wywołanie [trigger() funkcji](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Ten obiekt zapewnia dostęp do wartości właściwości wyzwalacza z bieżącego uruchomienia wystąpienia przepływu pracy. |
| `workflow` | Obiekt | Obiekt przepływu pracy i odpowiednik wywoływania [funkcji workflow().](../logic-apps/workflow-definition-language-functions-reference.md#workflow) Ten obiekt zapewnia dostęp do wartości właściwości przepływu pracy, takich jak nazwa przepływu pracy, identyfikator uruchamiania i tak dalej, z bieżącego uruchomienia wystąpienia przepływu pracy. |
|||

W przykładzie tego tematu `workflowContext` obiekt ma następujące właściwości, do których kod może uzyskać dostęp:

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>Dodawanie parametrów

W niektórych przypadkach może być konieczne jawnie wymagać, aby akcja **Kod wbudowany** zawiera wyniki z wyzwalacza lub określonych akcji, które kod odwołuje się jako zależności przez dodanie **trigger** lub **akcje** parametry. Ta opcja jest przydatna w scenariuszach, w których nie można znaleźć wyników, do których istnieje odwołanie w czasie wykonywania.

> [!TIP]
> Jeśli planujesz ponownie użyć kodu, dodaj odwołania do właściwości przy użyciu **code** box, tak aby kod zawiera odwołań do tokenu rozwiązane, a nie dodawanie wyzwalacza lub akcji jako jawne zależności.

Załóżmy na przykład, że masz kod, który odwołuje się do **wyniku SelectedOption** z akcji **Wyślij zatwierdzenie wiadomości e-mail** dla łącznika programu Office 365 Outlook. W czasie tworzenia aparat aplikacji logiki analizuje kod, aby ustalić, czy odwołujesz się do dowolnego wyzwalacza lub wyniki akcji i zawiera te wyniki automatycznie. W czasie wykonywania, jeśli zostanie wyświetlony błąd, że przywołany wyzwalacz `workflowContext` lub wynik akcji nie jest dostępny w określonym obiekcie, można dodać ten wyzwalacz lub akcję jako jawną zależność. W tym przykładzie należy dodać **akcje** parametr i określić, że **kod wbudowany** akcji jawnie dołączyć wynik z **akcji Wyślij zatwierdzenie wiadomości e-mail.**

Aby dodać te parametry, otwórz listę **Dodaj nowy parametr** i wybierz żądane parametry:

   ![Dodawanie parametrów](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parametr | Opis |
   |-----------|-------------|
   | **Akcje** | Uwzględnij wyniki z poprzednich akcji. Zobacz [Dołączanie wyników akcji](#action-results). |
   | **Wyzwalacz** | Uwzględnij wyniki z wyzwalacza. Zobacz [Dołączanie wyników wyzwalaczy](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Dołącz wyniki wyzwalaczy

Jeśli **wybierzesz wyzwalacze,** zostanie wyświetlony monit o uwzględnienie wyników wyzwalaczy.

* Z listy **Wyzwalacz** wybierz pozycję **Tak**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Uwzględnij wyniki akcji

Jeśli wybierzesz **akcje**, zostanie wyświetlony monit o akcje, które chcesz dodać. Jednak przed rozpoczęciem dodawania akcji, trzeba wersję nazwy akcji, która pojawia się w podstawowej definicji przepływu pracy aplikacji logiki.

* Ta funkcja nie obsługuje zmiennych, pętli i indeksów iteracji.

* Nazwy w definicji przepływu pracy aplikacji logiki używają podkreślenia (_), a nie spacji.

* Dla nazw akcji, które używają operatora kropki (.), obejmują te operatory, na przykład:

  `My.Action.Name`

1. Na pasku narzędzi projektanta wybierz pozycję `actions` Widok **kodu**i wyszukaj wewnątrz atrybutu nazwę akcji.

   Na przykład `Send_approval_email_` jest nazwa JSON dla **akcji Wyślij zatwierdzenie wiadomości e-mail.**

   ![Znajdowanie nazwy akcji w języku JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Aby powrócić do widoku projektanta, na pasku narzędzi widoku kodu wybierz pozycję **Projektant**.

1. Aby dodać pierwszą akcję, w polu **Element akcji — 1** wprowadź nazwę JSON akcji.

   ![Wprowadź pierwszą akcję](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Aby dodać kolejną akcję, wybierz pozycję **Dodaj nowy element**.

## <a name="reference"></a>Tematy pomocy

Aby uzyskać więcej informacji na temat struktury i składni akcji **Wykonywanie kodu JavaScript** w podstawowej definicji przepływu pracy aplikacji logiki przy użyciu języka definicji przepływu pracy, zobacz [sekcję odwołania](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)do tej akcji .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [łącznikach dla aplikacji logiki azure](../connectors/apis-list.md)
