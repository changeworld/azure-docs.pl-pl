---
title: Dodawanie i uruchamianie fragmenty kodu — Azure Logic Apps
description: Dodawanie i uruchamianie fragmenty kodu przy użyciu kodu wbudowanego w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: derek1ee, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 9ef11eb2099ff617fb4da4b9a924dc3f0550f226
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160548"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Dodawanie i uruchamianie fragmenty kodu przy użyciu kodu wbudowanego w usłudze Azure Logic Apps

Gdy użytkownik chce uruchomić fragment kodu wewnątrz aplikacji logiki, możesz dodać wbudowane **kodu wbudowanego** akcji jako krok w przepływie pracy aplikacji logiki. Ta akcja działa najlepiej, gdy użytkownik chce uruchomić kod, który pasuje do tego scenariusza:

* Uruchamiany w języku JavaScript. Już wkrótce więcej języków.
* Zostanie zakończone w ciągu pięciu sekund lub mniej.
* Służy do obsługi danych maksymalnie 50 MB.
* Używa środowiska Node.js w wersji 8.11.1. Aby uzyskać więcej informacji, zobacz [standardowe wbudowane obiekty](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

Ta akcja uruchamia fragment kodu i zwraca dane wyjściowe z tej wstawki jako token o nazwie **wynik**, którego można użyć w kolejnych akcjach w aplikacji logiki. W innych sytuacjach, w którym chcesz utworzyć funkcję w kodzie, spróbuj [tworzenie i wywoływanie funkcji platformy Azure](../logic-apps/logic-apps-azure-functions.md) w aplikacji logiki.

W tym artykule przykład wyzwalacze aplikacji logiki po nowej wiadomości e-mail odebraniu na koncie usługi Office 365 Outlook. Fragment kodu wyodrębnia i zwraca wszystkie adresy e-mail, które pojawiają się w treści wiadomości e-mail.

![Omówienie przykładowego](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki gdzie chcesz dodać fragment kodu, w tym wyzwalaczem. Jeśli nie masz aplikacji logiki, zobacz [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Przykładowa aplikacja logiki, w tym temacie używa tego wyzwalacza usługi Office 365 Outlook: **Po nadejściu nowej wiadomości e-mail**

* [Konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) połączony do aplikacji logiki

## <a name="add-inline-code"></a>Dodawanie kodu wbudowanego

1. Jeśli nie jest już w [witryny Azure portal](https://portal.azure.com), Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W projektancie, Dodaj **kodu wbudowanego** akcji w lokalizacji, które mają w przepływie pracy aplikacji logiki.

   * Aby dodać akcję na końcu przepływu pracy, wybierz opcję **nowy krok**.

   * Aby dodać akcję między krokami istniejący, należy przesunąć wskaźnik myszy nad strzałkę, która łączy się z tych kroków. Wybierz znak plus (**+**) i wybierz **Dodaj akcję**.

   Ten przykład dodaje **kodu wbudowanego** akcji w obszarze wyzwalacza usługi Office 365 Outlook.

   ![Dodaj nowy krok](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. W obszarze **wybierz akcję**, w polu wyszukiwania wpisz "wbudowany kod" jako filtr. Z listy akcji wybierz następującą akcję: **Wykonywanie kodu JavaScript**

   ![Wybierz pozycję "Wykonania kodu w języku JavaScript"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   Akcja zostanie wyświetlony w Projektancie i zawiera niektóre domyślne kodu przykładu, w tym instrukcji return.

   ![Wbudowany kod akcję przy użyciu domyślnego przykładowy kod](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. W **kodu** pole, usunąć przykładowego kodu i wprowadź kod, który chcesz uruchomić. Napisz kod, który możesz umieścić wewnątrz metody, ale bez definiowania podpis metody. 

   Podczas wpisywania tekstu rozpoznane słowa kluczowego, zostanie wyświetlona lista autouzupełniania, tak, aby można wybrać dostępne słów kluczowych, na przykład:

   ![Listy autouzupełniania — słowo kluczowe](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Ten przykład fragment kodu najpierw tworzy zmienną, która przechowuje *wyrażenia regularnego*, która określa wzorca, aby dopasować w tekście wejściowym. Następnie kod tworzy zmienną, która przechowuje dane treści wiadomości e-mail z wyzwalacza.

   ![Tworzenie zmiennych](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Aby ułatwić wyniki z poprzednich akcji i wyzwalaczy do odwołania, listy zawartości dynamicznej pojawia się, gdy kursor znajduje się wewnątrz **kodu** pole. W tym przykładzie lista dostępnych wyników z wyzwalacza, włączając **treści** token, który można teraz wybrać.

   Po wybraniu **treści** tokenu, akcji kodu wbudowanego jest rozpoznawana jako token do `workflowContext` obiektu, który odwołuje się do adresu e-mail `Body` wartość właściwości:

   ![Wybierz wynik](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   W **kodu** polu fragmentu kodu można użyć tylko do odczytu `workflowContext` obiektu jako dane wejściowe. Ten obiekt zawiera właściwości podrzędnych, które zapewniają dostępu kodu do wyników z poprzednich akcji w przepływie pracy i wyzwalaczy.
   Aby uzyskać więcej informacji zobacz w tej sekcji w dalszej części tego tematu: [Odwołanie wyników akcji i wyzwalaczy w kodzie](#workflowcontext).

   > [!NOTE]
   >
   > Wstawki kodu odwołuje się do nazwy akcji, które można użyć operatora kropki (.), należy dodać te nazwy akcji, aby [ **akcje** parametru](#add-parameters). Te odwołania również ująć nazwy akcji z nawiasami kwadratowymi ([]) i znaki cudzysłowu, na przykład:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   Nie wymaga działania kodu wbudowanego `return` instrukcji, ale wyniki z `return` poufności są dostępne do użytku w kolejnych akcjach za pośrednictwem **wynik** tokenu. 
   Na przykład fragment kodu zwraca wynik, wywołując `match()` funkcji, które znajdzie podanej w treści wiadomości e-mail dla wyrażenia regularnego. **Compose** używa akcji **wynik** token odwołania do wyników z wbudowanej Akcja kodu i tworzy jeden wynik.

   ![Ukończona aplikacja logiki](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Gdy skończysz, Zapisz aplikację logiki.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Wyzwalacz i akcja wyników odwołania w kodzie

`workflowContext` Obiekt ma ta struktura, która zawiera `actions`, `trigger`, i `workflow` właściwości podrzędnych:

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

Ta tabela zawiera więcej informacji na temat tych właściwości:

| Właściwość | Typ | Opis |
|----------|------|-------|
| `actions` | Kolekcja obiektów | Obiektów wynikowych z działań, które są uruchamiane przed uruchomieniem fragmentu kodu. Każdy obiekt ma *pary klucz wartość* pary, gdy klucz jest nazwy akcji, a wartość jest równoważne z wywoływaniem [funkcja actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions) z `@actions('<action-name>')`. Nazwa akcji używa tej samej nazwy akcji, który jest używany w definicji przepływu pracy podstawowej zastępuje miejsca do magazynowania ("") w nazwie akcji za pomocą podkreślenia (_). Ten obiekt umożliwia dostęp do wartości właściwości działań, z bieżącego wystąpienia przepływu pracy, uruchom. |
| `trigger` | Object | Obiekt wyniku z wyzwalaczem i równoważne z wywoływaniem [funkcja trigger()](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Ten obiekt umożliwia dostęp do wartości właściwości wyzwalacza, z bieżącego wystąpienia przepływu pracy, uruchom. |
| `workflow` | Object | Obiekt przepływu pracy i równoważne z wywoływaniem [funkcja workflow()](../logic-apps/workflow-definition-language-functions-reference.md#workflow). Ten obiekt umożliwia dostęp do wartości właściwości przepływu pracy, takie jak nazwa przepływu pracy, identyfikator uruchomienia i tak dalej, z bieżącego wystąpienia przepływu pracy, uruchom. |
|||

W przykładzie w tym temacie `workflowContext` obiekt ma następujące właściwości, które mogą uzyskiwać dostęp do kodu:

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

W niektórych przypadkach może być jawnie wymagającą **kodu wbudowanego** akcji zawiera wyniki z wyzwalacza lub konkretne akcje, których kod odwołuje się jako zależności, dodając **wyzwalacza** lub **Akcje** parametrów. Ta opcja jest przydatne w scenariuszach, gdzie odwołania wyniki nie zostaną znalezione w czasie wykonywania.

> [!TIP]
> Jeśli planujesz ponowne użycie kodu należy dodać odwołania do właściwości, za pomocą **kodu** polu, tak aby kod zawiera rozpoznać odwołania tokenu, zamiast dodawania wyzwalacza lub akcji jako jawne zależności.

Załóżmy, że masz kod, który odwołuje się do **SelectedOption** wynikiem **Wyślij wiadomość e-mail dotyczącą zatwierdzenia** akcję dla łącznika usługi Office 365 Outlook. Na czas utworzenia, aparat usługi Logic Apps analizuje kod, aby ustalić, czy odwołania dowolnego wyzwalacza lub akcji wyniki i automatycznie dołącza te wyniki. W czasie wykonywania, powinien wystąpi błąd, do którego istnieje odwołanie wyniku wyzwalacza lub akcji nie jest dostępna w określonym `workflowContext` obiektu jako jawne zależności można dodać tego wyzwalacza lub akcji. W tym przykładzie należy dodać **akcje** parametru i określić, że **kodu wbudowanego** akcji jawnie dołączyć znak wyniku z **Wyślij wiadomość e-mail dotyczącą zatwierdzenia** akcji.

Aby dodać te parametry, otwórz **dodano nowy parametr** listy i wybierz parametry mają:

   ![Dodawanie parametrów](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parametr | Opis |
   |-----------|-------------|
   | **Akcje** | Uwzględnij wyniki z poprzednich akcji. Zobacz [obejmują wyników akcji](#action-results). |
   | **Wyzwalacz** | Uwzględnij wyniki z wyzwalacza. Zobacz [wyniki wyzwalacza Include](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Uwzględnij wyniki wyzwalacza

Jeśli wybierzesz **wyzwalaczy**, zostanie wyświetlony monit, czy do wyzwalacza obejmują wyniki.

* Z **wyzwalacza** listy wybierz **tak**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Uwzględnij wyniki w akcji

Jeśli wybierzesz **akcje**, zostanie wyświetlony monit dla akcji, które chcesz dodać. Jednak przed rozpoczęciem dodawania akcji, potrzebujesz wersję nazwy akcji, która pojawia się w podstawowej definicji przepływu pracy aplikacji logiki.

* Ta funkcja nie obsługuje zmiennych, pętli i indeksy iteracji.

* Nazwy w definicji przepływu pracy aplikacji logiki, użyj znaku podkreślenia (_), nie spację.

* Nazwy akcji, które można użyć operatora kropka (.) zawierać tych operatorów, na przykład:

  `My.Action.Name`

1. Na pasku narzędzi Projektanta wybierz **widok kodu**i wyszukaj wewnątrz `actions` atrybut dla nazwy akcji.

   Na przykład `Send_approval_email_` to nazwa JSON **Wyślij wiadomość e-mail dotyczącą zatwierdzenia** akcji.

   ![Znajdowanie nazwy działania w formacie JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Aby powrócić do widoku projektanta, na pasku narzędzi Widok kodu Wybierz **projektanta**.

1. Aby dodać pierwszą akcją w **element akcje — 1** wprowadź nazwę JSON akcji.

   ![Wprowadź pierwszą akcję](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Aby dodać kolejną akcję, wybierz **Dodaj nowy element**.

## <a name="reference"></a>Informacje ogólne

Aby uzyskać więcej informacji na temat **wykonywanie kodu JavaScript** akcji struktury i składni w podstawowej definicji przepływu pracy aplikacji logiki, za pomocą języka definicji przepływu pracy, zobacz tę akcję [w sekcji ](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md)
