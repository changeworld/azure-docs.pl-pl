---
title: Akcje żądania i odpowiedzi | Dokumentacja firmy Microsoft
description: Przegląd żądania i odpowiedzi wyzwalacza i akcji w aplikacji logiki platformy Azure
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
ms.openlocfilehash: 0f6ee8729cbed9cb8baf3668f7b1a332bc5eddc1
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58892831"
---
# <a name="get-started-with-the-request-and-response-components"></a>Wprowadzenie do składników żądań i odpowiedzi
Dzięki składnikom żądań i odpowiedzi w aplikacji logiki może odpowiadać w czasie rzeczywistym na zdarzenia.

Można na przykład:

* Odpowiadanie na żądania HTTP przy użyciu danych z lokalnej bazy danych za pomocą aplikacji logiki.
* Wyzwalacza aplikacji logiki ze zdarzenia zewnętrznego elementu webhook.
* Wywoływanie aplikacji logiki przy użyciu akcji żądań i odpowiedzi z w ramach innej aplikacji logiki.

Aby rozpocząć pracę, przy użyciu akcje żądania i odpowiedzi w aplikacji logiki, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-http-request-trigger"></a>Użyj wyzwalacza żądania HTTP
Wyzwalacz to zdarzenie, który może służyć do uruchamiania tego przepływu, który jest zdefiniowany w aplikacji logiki. 
[Dowiedz się więcej na temat wyzwalaczy](../connectors/apis-list.md).

Oto przykładowa sekwencja sposób konfigurowania żądań HTTP w Projektancie aplikacji logiki.

1. Dodaj wyzwalacz **żądania — zostanie odebrane żądanie HTTP podczas** w aplikacji logiki. Opcjonalnie możesz podać schematu JSON (przy użyciu narzędzia, takiego jak [JSONSchema.net](https://jsonschema.net)) do treści żądania. Dzięki temu projektanta aby generować tokeny dla właściwości w żądaniu HTTP.
2. Dodaj kolejną akcję, dzięki czemu można zapisać aplikacji logiki.
3. Po zapisaniu aplikacji logiki, możesz uzyskać adres URL żądania HTTP z karty żądania.
4. Metodę POST protokołu HTTP (można użyć narzędzia, takiego jak [Postman](https://www.getpostman.com/)) do adresu URL wyzwalacze aplikacji logiki.

> [!NOTE]
> Jeśli nie zdefiniowano akcję odpowiedzi `202 ACCEPTED` odpowiedzi są natychmiast zwracane do obiektu wywołującego. Aby dostosować odpowiedzi, można użyć akcji odpowiedzi.
> 
> 

![Odpowiedź wyzwalacza](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Używanie akcji odpowiedzi HTTP
Akcja odpowiedzi HTTP jest prawidłowa tylko w przypadku, gdy używasz w przepływie pracy, która jest wyzwalana przez żądanie HTTP. Jeśli nie zdefiniowano akcję odpowiedzi `202 ACCEPTED` odpowiedzi są natychmiast zwracane do obiektu wywołującego.  Możesz dodać akcję odpowiedzi na dowolnym etapie w przepływie pracy. Aplikacja logiki przechowuje tylko przychodzące żądanie otwarte przez jedną minutę na odpowiedź.  Po jednej minucie, jeśli odpowiedź nie została wysłana z przepływu pracy (i w definicji istnieje akcja odpowiedzi) `504 GATEWAY TIMEOUT` jest zwracany do obiektu wywołującego.

Poniżej przedstawiono sposób dodawania akcji odpowiedzi HTTP:

1. Wybierz **nowy krok** przycisku.
2. Wybierz **Dodaj akcję**.
3. Wpisz w polu wyszukiwania akcji **odpowiedzi** Aby wyświetlić listę akcji odpowiedzi.
   
    ![Wybieranie akcji odpowiedzi](./media/connectors-native-reqres/using-action-1.png)
4. Dodaj wszystkie parametry, które są wymagane dla komunikatu odpowiedzi HTTP.
   
    ![Wykonaj akcję odpowiedzi](./media/connectors-native-reqres/using-action-2.png)
5. Kliknij w lewym górnym rogu narzędzi, aby zapisać, a Twoja aplikacja logiki będzie zapisywanie i publikowanie (Aktywuj).

## <a name="request-trigger"></a>Wyzwalacza żądania
Poniżej przedstawiono szczegółowe informacje dotyczące wyzwalacza, który obsługuje ten łącznik. Brak wyzwalacza pojedynczego żądania.

| Wyzwalacz | Opis |
| --- | --- |
| Żądanie |Występuje po odebraniu żądania HTTP |

## <a name="response-action"></a>Akcja odpowiedzi
Poniżej przedstawiono szczegółowe informacje dotyczące akcji, która obsługuje ten łącznik. Brak akcji pojedynczą odpowiedź, która może służyć tylko gdy towarzyszy wyzwalacza żądania.

| Akcja | Opis |
| --- | --- |
| Odpowiedź |Zwraca odpowiedź do skorelowane żądanie HTTP |

### <a name="trigger-and-action-details"></a>Szczegóły akcji i wyzwalaczy
W poniższych tabelach opisano pól wejściowych dla wyzwalacza i akcji, a odpowiednie dane wyjściowe zawierają szczegóły.

#### <a name="request-trigger"></a>Wyzwalacza żądania
Poniżej znajduje się pole wejściowe dla wyzwalacza z przychodzącego żądania HTTP.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Schemat JSON |Schemat |Schemat JSON treści żądania HTTP |

<br>

**Szczegóły danych wyjściowych**

Poniżej przedstawiono szczegóły danych wyjściowych dla żądania.

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Nagłówki |obiekt |Nagłówki żądań |
| Treść |obiekt |Obiekt "Request" |

#### <a name="response-action"></a>Akcja odpowiedzi
Dostępne są następujące pola wejściowe dla akcji odpowiedzi HTTP. A * oznacza, że jest polem wymaganym.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Kod stanu * |statusCode |Kod stanu HTTP |
| Nagłówki |Nagłówki |Obiekt JSON nagłówków odpowiedzi do uwzględnienia |
| Treść |treść |Treść odpowiedzi |

## <a name="next-steps"></a>Kolejne kroki
Teraz wypróbuj platformę i [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Możesz zapoznać się z innych dostępnych łączników w usłudze logic apps, analizując naszych [listy interfejsów API](apis-list.md).

