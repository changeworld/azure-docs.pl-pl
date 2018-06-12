---
title: Konfigurowanie obsługi wiadomości za pomocą usługi Azure Service Bus dla usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wysyłanie i odbieranie wiadomości z aplikacji logiki przy użyciu usługi Azure Service Bus
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 02/06/2018
ms.author: ladocs
ms.openlocfilehash: aa6ab10dded541b352bdb7c8c3a47dbbbfe6a15c
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295472"
---
# <a name="send-and-receive-messages-with-the-azure-service-bus-connector"></a>Wysyłanie i odbieranie komunikatów za pomocą łącznika usługi Azure Service Bus

Aby wysyłać i odbierać komunikaty z aplikacji logiki, połącz się z [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Można wykonać akcje, takie jak wysyłanie do kolejki, Wyślij do tematu, odbierania z kolejki i odbierania z subskrypcji. Dowiedz się więcej o [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) i [jak ceny działa Logic Apps wyzwala](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Wymagania wstępne

Zanim będzie możliwe użycie łącznika usługi Service Bus, musi mieć tych elementów, które musi istnieć w tej samej subskrypcji platformy Azure, dzięki czemu są one widoczne dla siebie:

* A [przestrzeń nazw magistrali usług i jednostki obsługi komunikatów, takie jak kolejki](../service-bus-messaging/service-bus-create-namespace-portal.md)
* A [aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-service-bus"></a>Połączenie do usługi Azure Service Bus

Zanim aplikację logiki można uzyskać dostęp do dowolnej usługi, należy utworzyć [ *połączenia* ](./connectors-overview.md) między aplikację logiki i usługi, jeśli nie jest jeszcze. To połączenie autoryzuje aplikację logiki, aby uzyskać dostęp do danych. Aby uzyskać dostęp do konta usługi Service Bus, sprawdź swoje uprawnienia aplikacji logiki.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com "Azure Portal"). 

2. Przejdź do Twojej usługi Service Bus *przestrzeni nazw*, nie określonych "jednostki obsługi komunikatów". Na stronie przestrzeni nazw w obszarze **ustawienia**, wybierz **zasady dostępu współużytkowanego**. W obszarze **oświadczeń**, sprawdź, czy masz **Zarządzaj** uprawnienia dla tej przestrzeni nazw.

   ![Zarządzaj uprawnieniami dla przestrzeni nazw magistrali usług](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Jeśli chcesz później ręcznie wprowadzić informacje o połączeniu, należy pobrać ciągu połączenia dla przestrzeni nazw usługi Service Bus. Wybierz **RootManageSharedAccessKey**. Obok ciągu połączenia klucza podstawowego kliknij przycisk Kopiuj. Zapisz parametry połączenia do późniejszego użycia.

   ![Skopiuj parametry połączenia przestrzeń nazw magistrali usług](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Aby sprawdzić, czy ciąg połączenia jest skojarzony z przestrzeni nazw magistrali usług lub z określonej jednostki, sprawdź ciąg połączenia dla `EntityPath` parametru. Możesz znaleźć tego parametru, ciąg połączenia jest dla określonego obiektu i nie jest poprawny ciąg do korzystania z aplikacji logiki.

## <a name="trigger-workflow-when-your-service-bus-gets-new-messages"></a>Wyzwalające przepływ pracy, gdy Twoje usługi Service Bus pobiera nowe wiadomości

A [ *wyzwalacza* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest zdarzeniem, która uruchamia przepływ pracy w aplikacji logiki. Aby uruchomić przepływ pracy, gdy nowe komunikaty są wysyłane do Twojej usługi Service Bus, wykonaj następujące kroki dodawania wyzwalaczy, które wykrywa te wiadomości.

1. W [portalu Azure](https://portal.azure.com "portalu Azure"), przejdź do istniejących aplikacji logiki lub tworzenie aplikacji logiki puste.

2. W Projektancie aplikacji logiki w polu wyszukiwania wprowadź "service bus", filtr. Wybierz **usługi Service Bus** łącznika. 

   ![Wybierz łącznik usługi Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-connector.png) 

3. Wybierz wyzwalacz, który ma być używany. Na przykład, aby uruchomić aplikację logiki, gdy nowy element są wysyłane do kolejki usługi Service Bus, wybierz wyzwalacz: **usługi Service Bus — po odebraniu wiadomości w kolejce (automatycznego zakończenia)**

   ![Wybierz wyzwalacz usługi Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Niektóre wyzwala zwracany co najmniej komunikaty, takie jak *usługi Service Bus - nadejściu jedną lub więcej wiadomości w kolejce (automatycznie uzupełniać)* wyzwalacza.
   > Gdy te wyzwalacze uruchamiają, zwracają między jeden a liczba komunikatów określonego przez tego wyzwalacza **maksymalna liczba komunikatów** właściwości.

   1. Jeśli masz już połączenie do obszaru nazw usługi Service Bus, zostanie wyświetlony monit o utworzyć teraz tego połączenia. Nadaj nazwę połączenia, a następnie wybierz obszar nazw usługi Service Bus, który ma być używany.

      ![Tworzenie połączenia magistrali usług](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Lub, aby ręcznie wprowadzić parametry połączenia, wybierz **ręcznie wprowadzić informacje o połączeniu**. 
      Dowiedz się [jak znaleźć parametrów połączenia](#permissions-connection-string).
      

   2. Teraz wybierz zasady usługi Service Bus, a następnie wybierz **Utwórz**.

      ![Utwórz połączenie z usługi Service Bus, część 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

4. Wybierz kolejki usługi Service Bus, a następnie skonfigurować interwał i częstotliwość czas wyszukiwania kolejki.

   ![Wybierz kolejki usługi Service Bus, skonfiguruj interwał sondowania](./media/connectors-create-api-azure-service-bus/select-service-bus-queue.png)

   > [!NOTE]
   > Wszystkich wyzwalaczy usługi Service Bus są **long sondowania** wyzwalaczy, co oznacza, że gdy wyzwalacza, wyzwalacz przetwarza wszystkie komunikaty i następnie czeka na 30 sekund na komunikaty mają być widoczne w subskrypcji kolejka lub temat.
   > Jeśli żadne komunikaty są odbierane w ciągu 30 sekund, zostanie pominięty wykonywania wyzwalacza. W przeciwnym razie wyzwalacza nadal odczytywania wiadomości do momentu kolejka lub temat subskrypcji jest pusty.
   > Następny sondowania wyzwalacza jest oparta na interwał cyklu określony we właściwości wyzwalacza.

5. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Teraz po aplikację logiki sprawdza wybranej kolejki i klient znajdzie nowy komunikat, wyzwalacz działa akcje w aplikacji logiki dla znalezionej wiadomości.

## <a name="send-messages-from-your-logic-app-to-your-service-bus"></a>Wysyłanie komunikatów z aplikacji logiki do Twojej usługi Service Bus

[*Akcja*](../logic-apps/logic-apps-overview.md#logic-app-concepts) to zadanie wykonywane przez przepływ pracy aplikacji logiki. Po dodaniu wyzwalacza do aplikacji logiki możesz dodać akcję, aby wykonywać operacje na danych generowanych przez ten wyzwalacz. Aby wysłać komunikat do Twojej jednostka z aplikacji logiki do obsługi komunikatów usługi Service Bus, wykonaj następujące kroki.

1. W Projektancie aplikacji logiki, w obszarze wyzwalacz, wybierz **+ nowy krok** > **Dodaj akcję**.

2. W polu wyszukiwania wprowadź "service bus" jako filtr. Wybierz ten łącznik: **usługi Service Bus**

   ![Wybierz łącznik usługi Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-connector-for-action.png) 

3. Wybierz tę akcję: **usługi Service Bus — wysyłanie wiadomości**

   ![Wybierz opcję "Service Bus — Wyślij komunikat"](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png)

4. Wybierz jednostki obsługi komunikatów, który jest nazwą kolejka lub temat, który ma zostać wysłana wiadomość. Wprowadź treść wiadomości i inne szczegóły.

   ![Wybierz jednostki obsługi komunikatów i podaj szczegóły komunikatu](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)    

5. Zapisz aplikację logiki. 

Teraz ustawiono akcję, która wysyła wiadomości z aplikacji logiki. 

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Aby dowiedzieć się więcej o wyzwalacze i akcje zdefiniowane w pliku programu Swagger oraz żadnych limitów, przejrzyj [szczegóły łącznika](/connectors/servicebus/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [inne łączniki dla usługi Azure Logic apps](../connectors/apis-list.md)