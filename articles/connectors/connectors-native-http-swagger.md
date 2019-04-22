---
title: Wywoływanie punkty końcowe REST z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, które komunikują się z punkty końcowe REST przy użyciu protokołu HTTP + Swagger łącznika w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
tags: connectors
ms.topic: article
ms.date: 07/18/2016
ms.openlocfilehash: 9408b66f74391b080ef46c758b07850b2ae8de57
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58893402"
---
# <a name="call-rest-endpoints-with-http--swagger-connector-in-azure-logic-apps"></a>Wywołaj punkty końcowe REST przy użyciu protokołu HTTP + Swagger łącznika w usłudze Azure Logic Apps

Możesz utworzyć najwyższej jakości łącznik do dowolnego punktu końcowego REST za pośrednictwem [dokument struktury Swagger](https://swagger.io) zastosowania protokołu HTTP + Swagger akcji w przepływie pracy aplikacji logiki. Można także rozszerzyć aplikacje logiki można wywołać z dowolnego punktu końcowego REST pierwszorzędne środowisko projektanta aplikacji logiki.

Aby dowiedzieć się, jak tworzyć aplikacje logiki za pomocą łączników, zobacz [Utwórz nową aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Użyj protokołu HTTP + Swagger jako wyzwalacz lub akcję

HTTP + Swagger wyzwalacza i akcji, które działają w taki sam jak [Akcja HTTP](connectors-native-http.md) jednak zapewnić lepsze doświadczenia w Projektancie aplikacji logiki, zapewniając strukturę interfejsu API i dane wyjściowe z [metadanych Swagger](https://swagger.io). Możesz również użyć protokołu HTTP + Swagger łącznika jako wyzwalacz. Jeśli chcesz zaimplementować wyzwalacza sondowania, postępuj zgodnie z wzorzec sondowania, który jest opisany w [Tworzenie niestandardowych interfejsów API można wywoływać z aplikacji logiki innych interfejsów API, usługami i systemami](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

Dowiedz się więcej o [wyzwalacze aplikacji logiki i akcje](../connectors/apis-list.md).

Poniżej przedstawiono przykładowy sposób do używają protokołu HTTP + Swagger operacji, ponieważ akcji w przepływie pracy w aplikacji logiki.

1. Wybierz **nowy krok** przycisku.
2. Wybierz **Dodaj akcję**.
3. Wpisz w polu wyszukiwania akcji **swagger** do listy HTTP + Swagger akcji.
   
    ![Wybieranie protokołu HTTP + Swagger akcji](./media/connectors-native-http-swagger/using-action-1.png)
4. Wpisz adres URL dokumentu Swagger:
   
   * Aby pracować z projektanta aplikacji logiki, adres URL musi być punkt końcowy HTTPS i włączono mechanizm CORS.
   * Jeśli dokument struktury Swagger nie spełnia tego wymagania, można użyć usługi Azure Storage przy użyciu mechanizmu CORS włączone do przechowywania dokumentu.
5. Kliknij przycisk **dalej** do odczytywania i renderowania z dokument struktury Swagger.
6. Dodaj wszystkie parametry, które są wymagane do wywołania HTTP.
   
    ![Zakończenie akcji HTTP](./media/connectors-native-http-swagger/using-action-2.png)
7. Kliknij, aby zapisać i opublikować swoją aplikację logiki **Zapisz** na pasku narzędzi projektanta.

### <a name="host-swagger-from-azure-storage"></a>Swagger hosta z usługi Azure Storage
Można odwoływać się do dokumentu Swagger, nie jest obsługiwana, lub który nie spełnia wymagań, zabezpieczenia i wymagania cross-origin projektanta. Aby rozwiązać ten problem, można zapisać dokumentu Swagger w usłudze Azure Storage i włączyć mechanizm CORS można odwoływać się do dokumentu.  

Poniżej przedstawiono kroki do tworzenia, konfigurowania i przechowywanie dokumentów programu Swagger w usłudze Azure Storage:

1. [Tworzenie konta usługi Azure storage za pomocą usługi Azure Blob storage](../storage/common/storage-create-storage-account.md). Aby wykonać ten krok, ustaw uprawnienia **dostęp publiczny**.

2. Włączanie mechanizmu CORS w obiekcie blob. 

   Aby automatycznie skonfigurować to ustawienie, można użyć [ten skrypt programu PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

3. Przekaż plik struktury Swagger do obiektu blob. 

   Można wykonać ten krok z [witryny Azure portal](https://portal.azure.com) lub z narzędzia, takiego jak [Eksploratora usługi Azure Storage](https://storageexplorer.com/).

4. Odwołanie łącza HTTPS do dokumentu w usłudze Azure Blob storage. 

   Link używa tego formatu:

   `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`

## <a name="technical-details"></a>Szczegóły techniczne
Poniżej przedstawiono szczegóły wyzwalaczy i akcji, to protokołu HTTP + Swagger łącznik obsługuje.

## <a name="http--swagger-triggers"></a>HTTP + Swagger wyzwalaczy
Wyzwalacz to zdarzenie, który może służyć do uruchamiania tego przepływu, który jest zdefiniowany w aplikacji logiki. HTTP + Swagger łącznika ma jeden wyzwalacz. [Dowiedz się więcej na temat wyzwalaczy](../connectors/apis-list.md).

| Wyzwalacz | Opis |
| --- | --- |
| HTTP + Swagger |Wywołanie HTTP i zwróć treść odpowiedzi |

## <a name="http--swagger-actions"></a>HTTP + Swagger akcji
Akcja jest operacja, która jest przeprowadzane przez przepływ pracy, który jest zdefiniowany w aplikacji logiki. HTTP + Swagger łącznika ma jedną możliwą akcją. [Dowiedz się więcej o akcjach](../connectors/apis-list.md).

| Akcja | Opis |
| --- | --- |
| HTTP + Swagger |Wywołanie HTTP i zwróć treść odpowiedzi |

### <a name="action-details"></a>Szczegóły akcji
HTTP + Swagger łącznika, który jest dostarczany za pomocą jednej akcji to możliwe. Poniżej znajdują się informacje o każdej akcji, ich wymaganych i opcjonalnych pól wejściowych i odpowiednimi szczegółami danych wyjściowych, które są skojarzone z ich użycia.

#### <a name="http--swagger"></a>HTTP + Swagger
Podejmuj wychodzące żądania HTTP, dzięki pomocy metadanych struktury Swagger.
Znak gwiazdki (*) oznacza, że wymagane pole.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Metoda * |method |Czasownik HTTP do użycia. |
| IDENTYFIKATOR URI * |identyfikator URI |Identyfikator URI żądania HTTP. |
| Nagłówki |Nagłówki |Obiekt JSON nagłówków HTTP do uwzględnienia. |
| Treść |treść |Treść żądania HTTP. |
| Authentication |uwierzytelnianie |Uwierzytelniania dla żądania. Aby uzyskać więcej informacji, zobacz [łącznik protokołu HTTP](connectors-native-http.md#authentication). |

**Szczegóły danych wyjściowych**

Odpowiedź HTTP

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Nagłówki |obiekt |Nagłówki odpowiedzi |
| Treść |obiekt |Obiekt odpowiedzi |
| Kod stanu |int |Kod stanu HTTP |

### <a name="http-responses"></a>Odpowiedzi HTTP
Podczas wykonywania wywołań do różnych działań, możesz otrzymać określone odpowiedzi. Poniżej znajduje się tabela, która przedstawia odpowiedniej odpowiedzi wraz z opisami.

| Name (Nazwa) | Opis |
| --- | --- |
| 200 |OK |
| 202 |Zaakceptowany |
| 400 |Nieprawidłowe żądanie |
| 401 |Brak autoryzacji |
| 403 |Zabroniony |
| 404 |Nie znaleziono |
| 500 |Wewnętrzny błąd serwera. Wystąpił nieznany błąd. |

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Znajdź inne łączniki](apis-list.md)
