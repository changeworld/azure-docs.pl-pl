---
title: Wywołaj punkty końcowe REST z HTTP + Swagger connector dla usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Nawiązywanie połączenia z aplikacji logiki za pośrednictwem programu Swagger z HTTP + programu Swagger do punkty końcowe REST łącznika
services: logic-apps
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
tags: connectors
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 4e4421b0dfe0f29c3d50764b9ca04471c73a1450
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296523"
---
# <a name="get-started-with-the-http--swagger-action"></a>Wprowadzenie do protokołu HTTP + Swagger akcji

Można utworzyć biletu łącznika do dowolnego punktu końcowego REST za pośrednictwem [dokumentu Swagger](https://swagger.io) korzystając HTTP + Swagger działań w przepływie pracy aplikacji logiki. Można również rozszerzyć zasięg aplikacji logiki, aby wywołać dowolnego punktu końcowego REST o najwyższej jakości środowisko projektanta aplikacji logiki.

Aby dowiedzieć się, jak tworzyć aplikacje logiki z łączników, zobacz [Utwórz nową aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Użyj protokołu HTTP + Swagger jako wyzwalacz lub akcji

HTTP + Swagger wyzwolenia i akcji w taki sam, jak działają [akcji HTTP](connectors-native-http.md) choć zapewnia lepsze środowisko pracy w Projektancie aplikacji logiki w przypadku wystawianego interfejsu API struktury i dane wyjściowe z [Swagger metadanych](https://swagger.io). Można również użyć HTTP + łącznika programu Swagger jako wyzwalacz. Do zaimplementowania wyzwalacz sondowania, należy wykonać wzorzec sondowania, który jest opisany w [Tworzenie niestandardowych interfejsów API do wywoływania z aplikacji logiki innych interfejsów API, usług i systemy](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

Dowiedz się więcej o [logiki aplikacji wyzwalacze i akcje](connectors-overview.md).

Oto przykład tego, jak to użycie HTTP + operacji programu Swagger jako akcja w przepływie pracy w aplikacji logiki.

1. Wybierz **nowy krok** przycisku.
2. Wybierz **Dodaj akcję**.
3. W polu wyszukiwania akcji wpisz **swagger** do listy HTTP + akcji struktury Swagger.
   
    ![Wybierz opcję HTTP + Swagger akcji](./media/connectors-native-http-swagger/using-action-1.png)
4. Wpisz adres URL dokumentu Swagger:
   
   * Aby pracować przy użyciu projektanta aplikacji logiki, adres URL musi być punkt końcowy HTTPS i włączono CORS.
   * Jeśli dokumentu Swagger nie spełnia tego wymagania, możesz użyć [magazynu Azure z włączonym mechanizmem CORS](#hosting-swagger-from-storage) do przechowywania dokumentu.
5. Kliknij przycisk **dalej** do odczytu i renderowania z dokumentu programu Swagger.
6. Dodaj w żadnych parametrów, które są wymagane dla połączenia HTTP.
   
    ![Zakończenie akcji HTTP](./media/connectors-native-http-swagger/using-action-2.png)
7. Kliknij, aby zapisać i publikowanie aplikacji logiki **zapisać** na pasku narzędzi projektanta.

### <a name="host-swagger-from-azure-storage"></a>Swagger hosta z usługi Azure Storage
Możesz chcieć odwołuje się do dokumentu Swagger, który nie jest obsługiwany lub który nie spełnia wymagań dotyczących cross-origin projektanta zabezpieczeń i. Aby rozwiązać ten problem, można przechowywać dokumentu Swagger w usłudze Azure Storage i włączyć mechanizm CORS do odwołania dokumentu.  

Poniżej przedstawiono procedurę tworzenia, konfigurowania i przechowywanie dokumentów struktury Swagger w usłudze Azure Storage:

1. [Utwórz konto magazynu platformy Azure z magazynu obiektów Blob Azure](../storage/common/storage-create-storage-account.md). Aby wykonać ten krok, ustaw uprawnienia **dostępu publicznego**.

2. Włączanie mechanizmu CORS w obiekcie blob. 

   Aby automatycznie skonfigurować to ustawienie, można użyć [ten skrypt programu PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

3. Przekaż plik struktury Swagger do obiektu blob. 

   Można wykonać ten krok z [portalu Azure](https://portal.azure.com) lub z narzędzia, takiego jak [Eksploratora usługi Storage Azure](http://storageexplorer.com/).

4. Odwołanie łącza HTTPS do dokumentu w magazynie obiektów Blob Azure. 

   Łącze używany format to:

   `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`

## <a name="technical-details"></a>Szczegóły techniczne
Poniżej przedstawiono szczegóły wyzwalacze i akcje który to HTTP + Swagger łącznik obsługuje.

## <a name="http--swagger-triggers"></a>HTTP + wyzwalaczy programu Swagger
Wyzwalacz to zdarzenie służy do uruchomienia przepływu pracy, który jest zdefiniowany w aplikacji logiki. [Dowiedz się więcej na temat wyzwalaczy.](connectors-overview.md) HTTP + Swagger łącznik ma jeden wyzwalacz.

| Wyzwalacz | Opis |
| --- | --- |
| HTTP + Swagger |Wywoływania HTTP i zwrócić zawartość zgodnie z odpowiedzi |

## <a name="http--swagger-actions"></a>HTTP + akcje programu Swagger
Akcja jest operacja odbywa się przez przepływ pracy, który jest zdefiniowany w aplikacji logiki. [Dowiedz się więcej na temat akcji.](connectors-overview.md) HTTP + Swagger łącznik ma jedną akcję możliwe.

| Akcja | Opis |
| --- | --- |
| HTTP + Swagger |Wywoływania HTTP i zwrócić zawartość zgodnie z odpowiedzi |

### <a name="action-details"></a>Szczegóły akcji
HTTP + Swagger łącznika jest dostarczany z jedną akcję możliwe. Poniżej znajdują się informacje o poszczególnych działań, ich wymaganych i opcjonalnych pól wejściowych i odpowiednie szczegóły danych wyjściowych skojarzonych z ich użycia.

#### <a name="http--swagger"></a>HTTP + Swagger
Przesyłania żądania wychodzącego HTTP pomocy metadanych struktury Swagger.
Znak gwiazdki (*) oznacza wymaganego pola.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Metoda * |method |Zlecenie HTTP do użycia. |
| IDENTYFIKATOR URI * |identyfikator URI |Identyfikator URI dla żądania HTTP. |
| Nagłówki |nagłówki |Obiekt JSON nagłówków HTTP w celu uwzględnienia. |
| Treść |treść |Treść żądania HTTP. |
| Authentication |uwierzytelnianie |Uwierzytelniania dla żądania. Aby uzyskać więcej informacji, zobacz [łącznika HTTP](connectors-native-http.md#authentication). |

**Szczegóły danych wyjściowych**

Odpowiedź HTTP

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Nagłówki |obiekt |Nagłówki odpowiedzi |
| Treść |obiekt |Obiekt odpowiedzi |
| Kod stanu |int |Kod stanu HTTP |

### <a name="http-responses"></a>Odpowiedzi HTTP
Podczas wykonywania wywołań do różnych działań, może pobrać niektórych odpowiedzi. Poniżej znajduje się tabela przedstawiono odpowiedzi odpowiedniego wraz z opisami.

| Name (Nazwa) | Opis |
| --- | --- |
| 200 |OK |
| 202 |Zaakceptowany |
| 400 |Nieprawidłowe żądanie |
| 401 |Brak autoryzacji |
| 403 |Zabroniony |
| 404 |Nie znaleziono |
| 500 |Wewnętrzny błąd serwera. Wystąpił nieznany błąd. |

- - -
## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Znajdź inne łączniki](apis-list.md)