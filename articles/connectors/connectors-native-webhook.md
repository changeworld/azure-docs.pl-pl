---
title: Łącznik elementu Webhook dla usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Jak używać Akcje elementu webhook i wyzwalaczy do wykonania akcji, takich jak tablicy filtrów z aplikacji logiki
services: logic-apps
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
tags: connectors
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 647dcebdd8d66ca49e14b51633e6a4a3ce6cb32b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296564"
---
# <a name="get-started-with-the-webhook-connector"></a>Rozpoczynanie pracy z łącznika elementu webhook

O działaniu elementu webhook i wyzwalacza można uruchomić, wstrzymywać i wznawiać przepływy do wykonywania następujących zadań:

* Wyzwalanie z [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) po odebraniu elementu
* Oczekiwanie na zatwierdzenie przed kontynuowaniem przepływu pracy

Dowiedz się więcej o [Tworzenie niestandardowych interfejsów API, który obsługuje elementu webhook](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>Użyj wyzwalacza elementu webhook

A [ *wyzwalacza* ](connectors-overview.md) jest zdarzeniem, która uruchamia przepływ pracy aplikacji logiki. Wyzwalacz elementu webhook jest oparty na zdarzeniach i nie zależą od sondowania dla nowych elementów. Podobnie jak [wyzwalacza żądania](connectors-native-reqres.md), natychmiast po podejmowanych przez zdarzenie generowane aplikacji logiki. Rejestruje wyzwalacza elementu webhook *wywołania zwrotnego adresu URL* usługi i używa tego adresu URL, aby uruchomić aplikację logiki jako wymagane.

Oto przykład pokazujący sposób konfigurowania wyzwalacza HTTP w Projektancie aplikacji logiki. W krokach założono została już wdrożona, lub uzyskują dostęp do interfejsu API, który następuje [webhook subskrybowanie i anulowanie subskrypcji wzorca w aplikacjach logiki](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). Subskrybuj wywołanie zapisane przy użyciu nowego elementu webhook aplikacji logiki lub przełączono z wyłączonego na włączony. Wywołanie anulowania subskrypcji są wysyłane w przypadku usunięte i zapisać lub przełączono z elementu webhook aplikacji logiki wyzwalacza włączony na wyłączony.

**Aby dodać wyzwalacza elementu webhook**

1. Dodaj **HTTP elementu Webhook** wyzwalacza jako pierwszy krok w aplikacji logiki.
2. Wypełnij parametrów dla elementu webhook subskrybowanie i anulowanie subskrypcji wywołania.

   Ten krok jest zgodny ze wzorcem samej jako [akcji HTTP](connectors-native-http.md) format.

     ![Wyzwalacz protokołu HTTP](./media/connectors-native-webhook/using-trigger.png)

3. Dodaj co najmniej jedną akcję.
4. Kliknij przycisk **zapisać** do publikowania aplikacji logiki. Ten krok wymaga punktu końcowego Subskrybuj z adresem URL wywołania zwrotnego wymagane do wyzwalania tej aplikacji logiki.
5. Zawsze, gdy usługa wykonuje `HTTP POST` adres URL wywołania zwrotnego, aplikację logiki generowane i zawiera wszystkie dane przekazywane do żądania.

## <a name="use-the-webhook-action"></a>Za pomocą akcji elementu webhook

[ *Akcji* ](connectors-overview.md) operacji odbywa się przez przepływ pracy zdefiniowanych w aplikacji logiki. Rejestruje działania elementu webhook *wywołania zwrotnego adresu URL* z usługą i czeka, aż adres URL jest wywoływana przed wznowieniem. ["Wyślij E-mail zatwierdzenia"](connectors-create-api-office365-outlook.md) przykładem łącznik, który wykonuje tego wzorca. Ten wzorzec można rozszerzyć do dowolnej usługi za pomocą akcji elementu webhook. 

Oto przykład pokazujący sposób skonfigurować akcję elementu webhook w Projektancie aplikacji logiki. Tych krokach przyjęto założenie, zostały już wdrożone, lub uzyskują dostęp do interfejsu API, który następuje [webhook subskrybowanie i anulowanie subskrypcji wzorzec używany w aplikacjach logiki](../logic-apps/logic-apps-create-api-app.md#webhook-actions). Wywołanie Subskrybuj dotyczące aplikacji logiki wykonuje akcję elementu webhook. Wywołanie anulowania subskrypcji są wysyłane w przypadku Uruchom zostało anulowane podczas oczekiwania na odpowiedź lub przed logiki aplikacji upłynie limit czasu.

**Aby dodać akcję elementu webhook**

1. Wybierz **nowy krok** > **Dodaj akcję**.

2. W polu wyszukiwania wpisz "elementu webhook", aby znaleźć **HTTP elementu Webhook** akcji.

    ![Wybierz akcję zapytania](./media/connectors-native-webhook/using-action-1.png)

3. Wypełnij parametrów dla elementu webhook subskrybowanie i anulowanie subskrypcji wywołania

   Ten krok jest zgodny ze wzorcem samej jako [akcji HTTP](connectors-native-http.md) format.

     ![Akcja pełnej kwerendy](./media/connectors-native-webhook/using-action-2.png)
   
   W czasie wykonywania aplikacji logiki wywołuje punkt końcowy Subskrybuj po osiągnięciu tego kroku.

4. Kliknij przycisk **zapisać** do publikowania aplikacji logiki.

## <a name="technical-details"></a>Szczegóły techniczne

Poniżej przedstawiono więcej informacji o wyzwalacze i akcje obsługuje tego elementu webhook.

## <a name="webhook-triggers"></a>Wyzwalacze elementu Webhook

| Akcja | Opis |
| --- | --- |
| HTTP Webhook |Subskrypcja wywołania zwrotnego adresu URL usługi, który można wywołać, adres URL, aby uruchomić aplikację logiki, zgodnie z potrzebami. |

### <a name="trigger-details"></a>Szczegóły wyzwalacza

#### <a name="http-webhook"></a>HTTP Webhook

Subskrypcja wywołania zwrotnego adresu URL usługi, który można wywołać, adres URL, aby uruchomić aplikację logiki, zgodnie z potrzebami.
* Oznacza, że wymagane pole.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Subskrypcja — metoda * |method |Metoda HTTP do użycia podczas subskrybowania żądania |
| Subskrypcja URI * |identyfikator URI |Identyfikator URI protokołu HTTP do użycia dla żądania Subskrybuj |
| Anulowanie subskrypcji metody * |method |Metoda HTTP do użycia podczas żądania anulowania |
| Anulowanie subskrypcji URI * |identyfikator URI |Identyfikator URI protokołu HTTP do użycia dla żądania anulowania |
| Subskrypcja treści |treść |Żądania HTTP do subskrybowania |
| Subskrypcja nagłówki |nagłówki |Nagłówki żądania HTTP do subskrybowania |
| Subskrypcja uwierzytelniania |uwierzytelnianie |Uwierzytelnianie HTTP do użycia podczas subskrybowania. [Zobacz łącznika HTTP](connectors-native-http.md#authentication) Aby uzyskać więcej informacji |
| Anulowanie subskrypcji treści |treść |Żądania HTTP do anulowania subskrypcji |
| Anulowanie subskrypcji nagłówki |nagłówki |Nagłówki żądania HTTP do anulowania subskrypcji |
| Anulowanie subskrypcji uwierzytelniania |uwierzytelnianie |Uwierzytelnianie HTTP do użycia na potrzeby anulowania subskrypcji. [Zobacz łącznika HTTP](connectors-native-http.md#authentication) Aby uzyskać więcej informacji |

**Szczegóły danych wyjściowych**

Żądania elementu Webhook

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Nagłówki |obiekt |Nagłówki żądania elementu Webhook |
| Treść |obiekt |Obiekt żądania elementu Webhook |
| Kod stanu |int |Kod stanu żądania elementu Webhook |

## <a name="webhook-actions"></a>Akcje elementu Webhook

| Akcja | Opis |
| --- | --- |
| HTTP Webhook |Subskrypcja wywołania zwrotnego adresu URL usługi, który można wywołać, adres URL, aby wznowić krok przepływu pracy, zgodnie z potrzebami. |

### <a name="action-details"></a>Szczegóły akcji

#### <a name="http-webhook"></a>HTTP Webhook

Subskrypcja wywołania zwrotnego adresu URL usługi, który można wywołać, adres URL, aby wznowić krok przepływu pracy, zgodnie z potrzebami.
* Oznacza, że wymagane pole.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Subskrypcja — metoda * |method |Metoda HTTP do użycia podczas subskrybowania żądania |
| Subskrypcja URI * |identyfikator URI |Identyfikator URI protokołu HTTP do użycia dla żądania Subskrybuj |
| Anulowanie subskrypcji metody * |method |Metoda HTTP do użycia podczas żądania anulowania |
| Anulowanie subskrypcji URI * |identyfikator URI |Identyfikator URI protokołu HTTP do użycia dla żądania anulowania |
| Subskrypcja treści |treść |Żądania HTTP do subskrybowania |
| Subskrypcja nagłówki |nagłówki |Nagłówki żądania HTTP do subskrybowania |
| Subskrypcja uwierzytelniania |uwierzytelnianie |Uwierzytelnianie HTTP do użycia podczas subskrybowania. [Zobacz łącznika HTTP](connectors-native-http.md#authentication) Aby uzyskać więcej informacji |
| Anulowanie subskrypcji treści |treść |Żądania HTTP do anulowania subskrypcji |
| Anulowanie subskrypcji nagłówki |nagłówki |Nagłówki żądania HTTP do anulowania subskrypcji |
| Anulowanie subskrypcji uwierzytelniania |uwierzytelnianie |Uwierzytelnianie HTTP do użycia na potrzeby anulowania subskrypcji. [Zobacz łącznika HTTP](connectors-native-http.md#authentication) Aby uzyskać więcej informacji |

**Szczegóły danych wyjściowych**

Żądania elementu Webhook

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Nagłówki |obiekt |Nagłówki żądania elementu Webhook |
| Treść |obiekt |Obiekt żądania elementu Webhook |
| Kod stanu |int |Kod stanu żądania elementu Webhook |

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Znajdź inne łączniki](apis-list.md)