---
title: Tworzenie przepływów pracy opartych na zdarzenia lub akcje — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie przepływów pracy opartych na zdarzenia lub akcje przy użyciu elementów webhook i usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.topic: article
tags: connectors
ms.date: 07/21/2016
ms.openlocfilehash: 7b1886321ca4afd4b4710bd9fddf16d2d5eb224b
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126591"
---
# <a name="create-event-based-workflows-or-actions-by-using-webhooks-and-azure-logic-apps"></a>Tworzenie przepływów pracy opartych na zdarzenia lub akcje przy użyciu elementów webhook i usługi Azure Logic Apps

Za pomocą wyzwalacz i Akcja elementu webhook można uruchomić, wstrzymywać i wznawiać przepływy do wykonywania następujących zadań:

* Wyzwalającej wydanie na podstawie [usługi Azure Event Hub](https://github.com/logicappsio/EventHubAPI) po odebraniu elementu
* Oczekiwanie na zatwierdzenie przed kontynuowaniem przepływu pracy

Dowiedz się więcej o [sposobu tworzenia niestandardowych interfejsów API, który obsługuje element webhook](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>Użyj wyzwalacza elementu webhook

A [ *wyzwalacza* ](connectors-overview.md) jest zdarzeniem uruchamiającym przepływ pracy aplikacji logiki. Wyzwalacza elementu webhook jest oparty na zdarzeniach i nie jest zależny od sondowania dla nowych elementów. Podobnie jak [wyzwalacza żądania](connectors-native-reqres.md), generowane aplikację logiki, natychmiastowe, która występuje zdarzenie. Rejestruje wyzwalacza elementu webhook *adresów URL wywołania zwrotnego* usługi i używa tego adresu URL, aby uruchomić aplikację logiki jako wymagane.

Oto przykład pokazujący sposób konfigurowania wyzwalacza HTTP w Projektancie aplikacji logiki. W krokach założono, że został już wdrożony lub uzyskują dostęp do interfejsu API, który następuje po [elementu webhook subskrybowanie i anulowanie subskrypcji wzorca w usłudze logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). Wykonano wywołanie Subskrybuj, zawsze wtedy, gdy aplikacja logiki jest zapisywany przy użyciu nowego elementu webhook lub z wyłączonego na włączony. Wykonano wywołanie anulowania subskrypcji, gdy usuwane i zapisać lub przełączono z wyzwalacza elementu webhook aplikacji logiki włączony na wyłączony.

**Aby dodać wyzwalacza elementu webhook**

1. Dodaj **element Webhook protokołu HTTP** wyzwalacza jako pierwszy krok w aplikacji logiki.
2. Podaj parametry dla elementu webhook subskrybowanie i anulowanie subskrypcji wywołania.

   Ten krok jest zgodna z tym samym wzorcem jako [Akcja HTTP](connectors-native-http.md) formatu.

     ![Wyzwalacz HTTP](./media/connectors-native-webhook/using-trigger.png)

3. Dodaj co najmniej jedną akcję.
4. Kliknij przycisk **Zapisz** do publikowania aplikacji logiki. Ten krok wywołuje punkt końcowy subskrypcji za pomocą adresu URL wywołania zwrotnego, wymagane do wyzwalania tej aplikacji logiki.
5. Zawsze, gdy usługa wykonuje `HTTP POST` do adresu URL wywołania zwrotnego, aplikacja logiki jest uruchamiany i zawiera wszystkie dane przekazywane do żądania.

## <a name="use-the-webhook-action"></a>Używanie akcji elementu webhook

[ *Akcji* ](connectors-overview.md) operacji odbywa się przez przepływ pracy zdefiniowane w aplikacji logiki. Rejestruje działania elementu webhook *adresów URL wywołania zwrotnego* z usługą i czeka, aż adres URL jest wywoływana przed wznowieniem. ["Wyślij wiadomość E-mail dotyczącą zatwierdzenia"](connectors-create-api-office365-outlook.md) jest przykładem łącznika, który następuje po ten wzorzec. Ten wzorzec można rozszerzyć na dowolną usługę za pomocą akcji elementu webhook. 

Oto przykład pokazujący sposób konfigurowania akcją elementu webhook w Projektancie aplikacji logiki. Te czynności zakładają, że został już wdrożony lub uzyskują dostęp do interfejsu API, który następuje po [elementu webhook subskrybowanie i anulowanie subskrypcji wzorzec używany w usłudze logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions). Wykonano wywołanie subskrypcji, gdy aplikacja logiki wykonuje akcji elementu webhook. Wykonano wywołanie anulowania subskrypcji, gdy uruchomienie zostało anulowane podczas oczekiwania na odpowiedź lub przed logiki aplikacji upłynie limit czasu.

**Aby dodać akcję elementu webhook**

1. Wybierz **nowy krok** > **Dodaj akcję**.

2. W polu wyszukiwania wpisz "element webhook", aby znaleźć **element Webhook protokołu HTTP** akcji.

    ![Wybieranie akcji zapytania](./media/connectors-native-webhook/using-action-1.png)

3. Podaj parametry dla elementu webhook subskrybowanie i anulowanie subskrypcji wywołania

   Ten krok jest zgodna z tym samym wzorcem jako [Akcja HTTP](connectors-native-http.md) formatu.

     ![Akcja pełnej kwerendy](./media/connectors-native-webhook/using-action-2.png)
   
   W czasie wykonywania aplikacja logiki wywołuje punkt końcowy subskrypcji po osiągnięciu tego kroku.

4. Kliknij przycisk **Zapisz** do publikowania aplikacji logiki.

## <a name="technical-details"></a>Szczegóły techniczne

Poniżej przedstawiono więcej informacji o wyzwalacze i akcje obsługuje tego elementu webhook.

## <a name="webhook-triggers"></a>Wyzwalaczy elementu Webhook

| Akcja | Opis |
| --- | --- |
| HTTP Webhook |Subskrybuj adres URL wywołania zwrotnego z usługą, który można wywoływać adresu URL wyzwolenie aplikacji logiki zgodnie z potrzebami. |

### <a name="trigger-details"></a>Szczegóły wyzwalacza

#### <a name="http-webhook"></a>HTTP Webhook

Subskrybuj adres URL wywołania zwrotnego z usługą, który można wywoływać adresu URL wyzwolenie aplikacji logiki zgodnie z potrzebami.
* Oznacza, że wymagane pole.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Subskrybuj metoda * |method |Metoda HTTP do użycia podczas żądania subskrypcji |
| Subskrybuj URI * |identyfikator URI |Identyfikator URI protokołu HTTP do użycia dla żądania subskrypcji |
| Anulowanie subskrypcji — metoda * |method |Metoda HTTP do użycia podczas żądania anulowania |
| Anulowanie subskrypcji identyfikatora URI * |identyfikator URI |Identyfikator URI protokołu HTTP do użycia dla żądania anulowania |
| Subskrybuj treści |treść |Treść żądania HTTP dla subskrypcji |
| Subskrybuj nagłówki |nagłówki |Nagłówki żądania HTTP dla subskrypcji |
| Subskrybuj uwierzytelniania |uwierzytelnianie |Uwierzytelnianie HTTP do użycia dla subskrypcji. [Zobacz łącznik protokołu HTTP](connectors-native-http.md#authentication) Aby uzyskać szczegółowe informacje |
| Anulowanie subskrypcji treści |treść |Treść żądania HTTP do anulowania subskrypcji |
| Anulowanie subskrypcji nagłówki |nagłówki |Nagłówki żądania HTTP do anulowania subskrypcji |
| Anulowanie subskrypcji uwierzytelniania |uwierzytelnianie |Uwierzytelnianie HTTP na potrzeby anulowania subskrypcji. [Zobacz łącznik protokołu HTTP](connectors-native-http.md#authentication) Aby uzyskać szczegółowe informacje |

**Szczegóły danych wyjściowych**

Żądanie elementu Webhook

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Nagłówki |obiekt |Nagłówki żądania elementu Webhook |
| Treść |obiekt |Obiekt żądania elementu Webhook |
| Kod stanu |Int |Kod stanu żądania elementu Webhook |

## <a name="webhook-actions"></a>Akcje elementu Webhook

| Akcja | Opis |
| --- | --- |
| HTTP Webhook |Subskrybuj adres URL wywołania zwrotnego z usługą, który można wywoływać adres URL, aby wznowić krok przepływu pracy, zgodnie z potrzebami. |

### <a name="action-details"></a>Szczegóły akcji

#### <a name="http-webhook"></a>HTTP Webhook

Subskrybuj adres URL wywołania zwrotnego z usługą, który można wywoływać adres URL, aby wznowić krok przepływu pracy, zgodnie z potrzebami.
* Oznacza, że wymagane pole.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Subskrybuj metoda * |method |Metoda HTTP do użycia podczas żądania subskrypcji |
| Subskrybuj URI * |identyfikator URI |Identyfikator URI protokołu HTTP do użycia dla żądania subskrypcji |
| Anulowanie subskrypcji — metoda * |method |Metoda HTTP do użycia podczas żądania anulowania |
| Anulowanie subskrypcji identyfikatora URI * |identyfikator URI |Identyfikator URI protokołu HTTP do użycia dla żądania anulowania |
| Subskrybuj treści |treść |Treść żądania HTTP dla subskrypcji |
| Subskrybuj nagłówki |nagłówki |Nagłówki żądania HTTP dla subskrypcji |
| Subskrybuj uwierzytelniania |uwierzytelnianie |Uwierzytelnianie HTTP do użycia dla subskrypcji. [Zobacz łącznik protokołu HTTP](connectors-native-http.md#authentication) Aby uzyskać szczegółowe informacje |
| Anulowanie subskrypcji treści |treść |Treść żądania HTTP do anulowania subskrypcji |
| Anulowanie subskrypcji nagłówki |nagłówki |Nagłówki żądania HTTP do anulowania subskrypcji |
| Anulowanie subskrypcji uwierzytelniania |uwierzytelnianie |Uwierzytelnianie HTTP na potrzeby anulowania subskrypcji. [Zobacz łącznik protokołu HTTP](connectors-native-http.md#authentication) Aby uzyskać szczegółowe informacje |

**Szczegóły danych wyjściowych**

Żądanie elementu Webhook

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Nagłówki |obiekt |Nagłówki żądania elementu Webhook |
| Treść |obiekt |Obiekt żądania elementu Webhook |
| Kod stanu |Int |Kod stanu żądania elementu Webhook |

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Znajdź inne łączniki](apis-list.md)