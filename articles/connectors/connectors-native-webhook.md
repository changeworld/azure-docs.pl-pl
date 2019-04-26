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
ms.openlocfilehash: c3047000843e054e71ec1a80313118a25e7c4905
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60447238"
---
# <a name="create-event-based-workflows-or-actions-by-using-webhooks-and-azure-logic-apps"></a>Tworzenie przepływów pracy opartych na zdarzenia lub akcje przy użyciu elementów webhook i usługi Azure Logic Apps

Za pomocą wyzwalacz i Akcja elementu webhook można uruchomić, wstrzymywać i wznawiać przepływy do wykonywania następujących zadań:

* Wyzwalającej wydanie na podstawie [usługi Azure Event Hub](https://github.com/logicappsio/EventHubAPI) po odebraniu elementu
* Oczekiwanie na zatwierdzenie przed kontynuowaniem przepływu pracy

Dowiedz się więcej o [sposobu tworzenia niestandardowych interfejsów API, który obsługuje element webhook](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>Użyj wyzwalacza elementu webhook

A [ *wyzwalacza* ](../connectors/apis-list.md) jest zdarzeniem uruchamiającym przepływ pracy aplikacji logiki. Wyzwalacza elementu webhook jest oparty na zdarzeniach, które nie są zależne od sondowania dla nowych elementów. Po zapisaniu aplikacji logiki za pomocą wyzwalacza elementu webhook lub po zmianie aplikacji logiki z wyłączonego na włączony wyzwalacza elementu webhook *subskrybuje* do określonej usługi lub punktu końcowego, rejestrując *adresów URL wywołania zwrotnego* od danej usługi lub punktu końcowego. Wyzwalacz następnie używa tego adresu URL do uruchomienia aplikacji logiki zgodnie z potrzebami. Podobnie jak [wyzwalacza żądania](connectors-native-reqres.md), aplikacja logiki jest uruchamiany natychmiast po oczekiwane zdarzenie. Wyzwalacz *anulowań subskrypcji* Usuń wyzwalacz i zapisywanie aplikacji logiki lub gdy zmienią się Twoja aplikacja logiki z włączony na wyłączony.

Oto przykład pokazujący sposób konfigurowania wyzwalacza HTTP w Projektancie aplikacji logiki. W krokach założono, że został już wdrożony lub uzyskują dostęp do interfejsu API, który następuje po [elementu webhook subskrybowanie i anulowanie subskrypcji wzorca w usłudze logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). 

**Aby dodać wyzwalacza elementu webhook**

1. Dodaj **element Webhook protokołu HTTP** wyzwalacza jako pierwszy krok w aplikacji logiki.
2. Podaj parametry dla elementu webhook subskrybowanie i anulowanie subskrypcji wywołania.

   Ten krok jest zgodna z tym samym wzorcem jako [Akcja HTTP](connectors-native-http.md) formatu.

     ![Wyzwalacz HTTP](./media/connectors-native-webhook/using-trigger.png)

3. Dodaj co najmniej jedną akcję.
4. Kliknij przycisk **Zapisz** do publikowania aplikacji logiki. Ten krok wywołuje punkt końcowy subskrypcji za pomocą adresu URL wywołania zwrotnego, wymagane do wyzwalania tej aplikacji logiki.
5. Zawsze, gdy usługa wykonuje `HTTP POST` do adresu URL wywołania zwrotnego, aplikacja logiki jest uruchamiany i zawiera wszystkie dane przekazywane do żądania.

## <a name="use-the-webhook-action"></a>Używanie akcji elementu webhook

[ *Akcji* ](../connectors/apis-list.md) jest operacja, która jest zdefiniowana i wykonywania przez przepływ pracy aplikacji logiki. Po uruchomieniu akcji elementu webhook, ta akcja w aplikacji logiki *subskrybuje* do określonej usługi lub punktu końcowego, rejestrując *adresów URL wywołania zwrotnego* od danej usługi lub punktu końcowego. Akcja elementu webhook, a następnie czeka, aż który analizującą adres URL przed wznawia aplikacji logiki, które są uruchomione. Aplikacja logiki anulowań subskrypcji z usługi lub punktu końcowego w następujących przypadkach: 

* Po pomyślnym zakończeniu działania elementu webhook
* Jeśli przebieg aplikacji logiki jest anulowane podczas oczekiwania na odpowiedź
* Przed logiki aplikacji upłynie limit czasu

Na przykład [ **Wyślij wiadomość e-mail dotyczącą zatwierdzenia** ](connectors-create-api-office365-outlook.md) akcji znajduje się przykład akcji elementu webhook, która wynika z tego wzorca. Ten wzorzec można rozszerzyć na dowolną usługę za pomocą akcji elementu webhook. 

Oto przykład pokazujący sposób konfigurowania akcją elementu webhook w Projektancie aplikacji logiki. Te czynności zakładają, że został już wdrożony lub uzyskują dostęp do interfejsu API, który następuje po [elementu webhook subskrybowanie i anulowanie subskrypcji wzorzec używany w usłudze logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions). 

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
| Subskrybuj nagłówki |Nagłówki |Nagłówki żądania HTTP dla subskrypcji |
| Subskrybuj uwierzytelniania |uwierzytelnianie |Uwierzytelnianie HTTP do użycia dla subskrypcji. [Zobacz łącznik protokołu HTTP](connectors-native-http.md#authentication) Aby uzyskać szczegółowe informacje |
| Anulowanie subskrypcji treści |treść |Treść żądania HTTP do anulowania subskrypcji |
| Anulowanie subskrypcji nagłówki |Nagłówki |Nagłówki żądania HTTP do anulowania subskrypcji |
| Anulowanie subskrypcji uwierzytelniania |uwierzytelnianie |Uwierzytelnianie HTTP na potrzeby anulowania subskrypcji. [Zobacz łącznik protokołu HTTP](connectors-native-http.md#authentication) Aby uzyskać szczegółowe informacje |

**Szczegóły danych wyjściowych**

Żądanie elementu Webhook

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Nagłówki |obiekt |Nagłówki żądania elementu Webhook |
| Treść |obiekt |Obiekt żądania elementu Webhook |
| Kod stanu |int |Kod stanu żądania elementu Webhook |

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
| Subskrybuj nagłówki |Nagłówki |Nagłówki żądania HTTP dla subskrypcji |
| Subskrybuj uwierzytelniania |uwierzytelnianie |Uwierzytelnianie HTTP do użycia dla subskrypcji. [Zobacz łącznik protokołu HTTP](connectors-native-http.md#authentication) Aby uzyskać szczegółowe informacje |
| Anulowanie subskrypcji treści |treść |Treść żądania HTTP do anulowania subskrypcji |
| Anulowanie subskrypcji nagłówki |Nagłówki |Nagłówki żądania HTTP do anulowania subskrypcji |
| Anulowanie subskrypcji uwierzytelniania |uwierzytelnianie |Uwierzytelnianie HTTP na potrzeby anulowania subskrypcji. [Zobacz łącznik protokołu HTTP](connectors-native-http.md#authentication) Aby uzyskać szczegółowe informacje |

**Szczegóły danych wyjściowych**

Żądanie elementu Webhook

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Nagłówki |obiekt |Nagłówki żądania elementu Webhook |
| Treść |obiekt |Obiekt żądania elementu Webhook |
| Kod stanu |int |Kod stanu żądania elementu Webhook |

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Znajdź inne łączniki](apis-list.md)
