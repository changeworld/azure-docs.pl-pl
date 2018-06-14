---
title: Akcje żądań i odpowiedzi | Dokumentacja firmy Microsoft
description: Omówienie wyzwalacza żądań i odpowiedzi i akcji w aplikacji logiki platformy Azure
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 58210db585befd7ce915d4579d4d0303eb15bff3
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27960565"
---
# <a name="get-started-with-the-request-and-response-components"></a>Rozpoczęcie pracy ze składnikami żądań i odpowiedzi
Ze składnikami żądań i odpowiedzi w aplikacji logiki możesz odpowiedzieć w czasie rzeczywistym na zdarzenia.

Można na przykład:

* Odpowiada na żądania HTTP z danymi z lokalną bazą danych za pośrednictwem aplikacji logiki.
* Uruchomienie aplikacji logiki z zdarzenie zewnętrznego elementu webhook.
* Wywołania z akcją żądań i odpowiedzi z poziomu innej aplikacji logiki aplikacji logiki.

Aby rozpocząć korzystanie z akcji żądań i odpowiedzi w aplikacji logiki, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-http-request-trigger"></a>Użyj wyzwalacza żądania HTTP
Wyzwalacz to zdarzenie służy do uruchomienia przepływu pracy, który jest zdefiniowany w aplikacji logiki. [Dowiedz się więcej o wyzwalaczy](connectors-overview.md).

Oto przykład sekwencji sposobu konfigurowania żądania HTTP w Projektancie aplikacji logiki.

1. Dodaj wyzwalacza **żądania - zostanie odebrane żądanie HTTP podczas** w aplikacji logiki. Opcjonalnie można podać schematu JSON (przy użyciu narzędzia, takiego jak [JSONSchema.net](http://jsonschema.net)) dla treści żądania. Dzięki temu projektanta, aby generować tokeny dla właściwości w żądaniu HTTP.
2. Dodaj inną akcję, aby zapisać aplikacji logiki.
3. Po zapisaniu aplikację logiki, może uzyskać adres URL żądania HTTP z karty żądania.
4. HTTP POST (można użyć narzędzia, takiego jak [Postman](https://www.getpostman.com/)) do adresu URL wyzwala aplikacji logiki.

> [!NOTE]
> Jeśli nie zdefiniowano akcję odpowiedzi `202 ACCEPTED` natychmiast zwrócił odpowiedź do obiektu wywołującego. Akcja odpowiedzi służy do dostosowywania odpowiedzi.
> 
> 

![Wyzwalacz odpowiedzi](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Za pomocą akcji odpowiedzi HTTP
Akcja odpowiedzi HTTP jest prawidłowa tylko w przypadku, gdy jest używany w przepływie pracy, który jest wyzwalana przez żądanie HTTP. Jeśli nie zdefiniowano akcję odpowiedzi `202 ACCEPTED` natychmiast zwrócił odpowiedź do obiektu wywołującego.  Można dodać akcję odpowiedzi na dowolnym etapie w przepływie pracy. Aplikację logiki przechowuje tylko przychodzące żądanie otwarte przez jedną minutę na odpowiedź.  Po minucie, jeśli odpowiedź nie został wysłany z przepływu pracy (i istnieje akcja odpowiedzi w definicji) `504 GATEWAY TIMEOUT` jest zwracany do obiektu wywołującego.

Poniżej przedstawiono sposób dodawania akcji odpowiedzi HTTP:

1. Wybierz **nowy krok** przycisku.
2. Wybierz **Dodaj akcję**.
3. W polu wyszukiwania akcji wpisz **odpowiedzi** Aby wyświetlić listę akcji odpowiedzi.
   
    ![Wybierz akcję odpowiedzi](./media/connectors-native-reqres/using-action-1.png)
4. Dodaj w żadnych parametrów, które są wymagane dla komunikatu odpowiedzi HTTP.
   
    ![Zakończenie akcji odpowiedzi](./media/connectors-native-reqres/using-action-2.png)
5. Kliknij w lewym górnym rogu paska narzędzi, aby zapisać i aplikacji logiki będzie Zapisz i opublikuj (Aktywuj).

## <a name="request-trigger"></a>Wyzwalacz żądania
Poniżej przedstawiono szczegóły wyzwalacz, który obsługuje ten łącznik. Brak wyzwalacz pojedyncze żądanie.

| Wyzwalacz | Opis |
| --- | --- |
| Żądanie |Występuje, gdy zostanie odebrane żądanie HTTP |

## <a name="response-action"></a>Akcja odpowiedzi
Poniżej przedstawiono szczegóły akcję, która obsługuje ten łącznik. Brak akcji pojedynczą odpowiedź, który można użyć tylko, gdy towarzyszy wyzwalacz żądania.

| Akcja | Opis |
| --- | --- |
| Odpowiedź |Zwraca odpowiedź skorelowane żądanie HTTP |

### <a name="trigger-and-action-details"></a>Szczegóły TRIGGER i action
W poniższych tabelach opisano pole wejściowe dla wyzwalacza i akcji, a odpowiednie szczegóły danych wyjściowych.

#### <a name="request-trigger"></a>Wyzwalacz żądania
Poniżej znajduje się pole wejściowe dla wyzwalacza z przychodzącego żądania HTTP.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Schematu JSON |Schemat |Schematu JSON w treści żądania HTTP |

<br>

**Szczegóły danych wyjściowych**

Poniżej przedstawiono szczegóły danych wyjściowych dla żądania.

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Nagłówki |obiekt |Nagłówki żądań |
| Treść |obiekt |Obiekt żądania |

#### <a name="response-action"></a>Akcja odpowiedzi
Poniżej przedstawiono pól wejściowych dla akcji odpowiedzi HTTP. A * oznacza, że jest polem wymaganym.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Kod stanu * |statusCode |Kod stanu HTTP |
| Nagłówki |nagłówki |Obiekt JSON nagłówków odpowiedzi do uwzględnienia |
| Treść |treść |Treść odpowiedzi |

## <a name="next-steps"></a>Kolejne kroki
Teraz, wypróbuj platformy i [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Dostępne łączniki w aplikacjach logiki można eksplorować analizując naszych [listy interfejsów API](apis-list.md).

