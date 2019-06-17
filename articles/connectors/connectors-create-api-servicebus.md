---
title: Wysyłanie i odbieranie komunikatów za pomocą usługi Azure Service Bus — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Konfigurowanie chmury dla przedsiębiorstw Obsługa komunikatów za pomocą usługi Azure Service Bus w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 68378c87e18df874059579445352b8fd1b2b6c13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105584"
---
# <a name="exchange-messages-in-the-cloud-with-azure-service-bus-and-azure-logic-apps"></a>Wymiana komunikatów w chmurze za pomocą usługi Azure Service Bus i Azure Logic Apps

Korzystając z usługi Azure Logic Apps i łącznik usługi Azure Service Bus można utworzyć automatycznych zadań i przepływów pracy, które transferu danych, takie jak sprzedaż i zamówień zakupu, arkuszy i przesunięcia magazynowe w aplikacjach dla Twojej organizacji. Łącznik nie tylko monitoruje, wysyła i zarządza wiadomości, ale oraz wykonuje akcje przy użyciu kolejek, sesje, tematy, subskrypcje i tak dalej, na przykład:

* Monitor przychodzą (Automatyczne zakończenie) lub są odebrane (blokada podglądu) w kolejki, tematy i subskrypcje tematu wiadomości. 
* Wysyłanie komunikatów.
* Tworzyć i usuwać subskrypcje tematu.
* Zarządzanych komunikaty w kolejkach i subskrypcje tematu na przykład, Pobierz, Pobierz odroczony, wykonaj, odroczone, odrzuceniu i utraconych wiadomości.
* Odnowienie blokady wiadomości i sesje w przypadku kolejek i subskrypcji tematu.
* Zamknij sesje w przypadku kolejek i tematów.

Możesz użyć wyzwalaczy, które uzyskiwanie odpowiedzi z usługi Service Bus i udostępnić dane wyjściowe inne akcje w aplikacjach logiki. Mogą też istnieć inne akcje w strumieniu wyjściowym działania usługi Service Bus. Jeśli jesteś nowym użytkownikiem usługi Service Bus i Logic Apps, zapoznaj się z [co to jest Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) i [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Przestrzeń nazw usługi Service Bus i jednostki obsługi komunikatów, takiego jak kolejka. Jeśli nie masz tych elementów, Dowiedz się, jak [tworzenie przestrzeni nazw usługi Service Bus i kolejki](../service-bus-messaging/service-bus-create-namespace-portal.md). 

  Te elementy muszą istnieć w tej samej subskrypcji platformy Azure jako aplikacjach logiki, które korzystają z tych elementów.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz używać usługi Service Bus. Twoja aplikacja logiki musi istnieć w tej samej subskrypcji platformy Azure jako usługi Service bus. Aby uruchomić z wyzwalaczem usługi Service Bus [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji usługi Service Bus, uruchomić swoją aplikację logiki za pomocą wyzwalacza innego, na przykład, **cyklu** wyzwalacza.

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Sprawdź uprawnienia

Upewnij się, że Twoja aplikacja logiki ma uprawnienia do uzyskiwania dostępu do przestrzeni nazw usługi Service Bus. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Przejdź do usługi Service Bus *przestrzeni nazw*. Na stronie przestrzeń nazw w ramach **ustawienia**, wybierz opcję **zasady dostępu współdzielonego**. W obszarze **oświadczeń**, upewnij się, że masz **Zarządzaj** uprawnienia dla tej przestrzeni nazw

   ![Zarządzaj uprawnieniami dla przestrzeni nazw usługi Service Bus](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Pobierz parametry połączenia dla przestrzeni nazw usługi Service Bus. Parametry te będą potrzebne po wprowadzeniu informacji o połączeniu w aplikacji logiki.

   1. Wybierz **RootManageSharedAccessKey**. 
   
   1. Obok swoje podstawowe parametry połączenia kliknij przycisk Kopiuj. Zapisz parametry połączenia w celu późniejszego użycia.

      ![Skopiuj parametry połączenia przestrzeni nazw usługi Service Bus](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Aby upewnić się, czy parametry połączenia usługi jest skojarzona z przestrzeni nazw usługi Service Bus lub jednostki obsługi komunikatów, takiego jak kolejka, wyszukaj ciąg połączenia dla `EntityPath`  parametru. Jeśli ten parametr, ciąg połączenia jest dla określonej jednostki i nie jest prawidłowy ciąg za pomocą aplikacji logiki.

## <a name="add-trigger-or-action"></a>Dodawanie wyzwalacza lub akcji

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Aby dodać *wyzwalacza* do pustej aplikacji logiki, w polu wyszukiwania wpisz "Azure Service Bus" jako filtr. W obszarze listy wyzwalaczy wybierz wyzwalacz, który ma. 

   Na przykład aby wyzwolić aplikację logiki, gdy nowy element jest wysyłany do kolejki usługi Service Bus, wybierz ten wyzwalacz: **Gdy wiadomość zostaje odebrana w kolejce (Automatyczne zakończenie)**

   ![Wybieranie wyzwalacza usługi Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Niektóre wyzwalaczy może zwracać jedną ani wiadomości, na przykład wyzwalacz, **kiedy co najmniej jeden komunikat dociera w kolejce (Automatyczne zakończenie)** . Gdy te wyzwalacze są uruchamiane, zwracają one między jednym i liczby komunikatów określonej przez wyzwalacz **maksymalna liczba komunikatów** właściwości.

   *Wszystkie wyzwalacze usługi Service Bus to wyzwalacze długiego sondowania*, co oznacza, że po aktywowaniu wyzwalacza wyzwalacza są przetwarzane wszystkie komunikaty, a następnie czeka 30 sekund w przypadku większej liczby wiadomości, które pojawią się w subskrypcji kolejki lub tematu. 
   Jeśli żadne komunikaty są wyświetlane w ciągu 30 sekund, uruchom wyzwalacz zostanie pominięty. 
   W przeciwnym razie wyzwalacz będzie kontynuowane, odczytywanie wiadomości, aż subskrypcja kolejki lub tematu jest pusta. Dalej sondowania wyzwalacza jest oparty na interwał cyklu określona we właściwościach tego wyzwalacza.

1. Aby dodać *akcji* do istniejącej aplikacji logiki, wykonaj następujące kroki: 

   1. W ostatnim kroku, której chcesz dodać akcję, wybierz **nowy krok**. 

      Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. 
      Wybierz znak plus ( **+** ) pojawia się, a następnie wybierz **Dodaj akcję**.

   1. W polu wyszukiwania wprowadź "Azure Service Bus" jako filtr. 
   W obszarze listy akcji wybierz akcję, którą chcesz. 
 
      Na przykład wybierz tę akcję: **Wyślij wiadomość**

      ![Wybierz akcję usługi Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Jeśli łączysz swoją aplikację logiki do przestrzeni nazw usługi Service Bus po raz pierwszy Projektant aplikacji logiki teraz wyświetli monit o podanie informacji o połączeniu. 

   1. Podaj nazwę połączenia, a następnie wybierz przestrzeń nazw usługi Service Bus.

      ![Utwórz połączenie usługi Service Bus, część 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Aby ręcznie zamiast tego wprowadź parametry połączenia, wybierz opcję **ręcznie wprowadzić informacje o połączeniu**. 
      Dowiedz się, jeśli nie masz parametry połączenia, [jak znaleźć parametrów połączenia](#permissions-connection-string).

   1. Teraz wybierz zasady usługi Service Bus, a następnie wybierz **Utwórz**.

      ![Utwórz połączenie usługi Service Bus, część 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

1. W tym przykładzie wybierz jednostki obsługi komunikatów, których potrzebujesz, takie jak kolejki lub tematu. W tym przykładzie wybierz kolejki usługi Service Bus. 
   
   ![Wybierz kolejki usługi Service Bus](./media/connectors-create-api-azure-service-bus/service-bus-select-queue.png)

1. Podaj odpowiednie szczegóły dla wyzwalacza lub akcji. W tym przykładzie postępuj zgodnie z odpowiednimi instrukcjami dla wyzwalacza lub akcji: 

   * **Wyzwalacz przykładowe**: Ustaw interwał sondowania i częstotliwości sprawdzania kolejki.

     ![Skonfiguruj interwał sondowania](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

     Gdy wszystko będzie gotowe, nadal tworzenia przepływu pracy aplikacji logiki, dodając żądane akcje. Na przykład można dodać akcję, która wysyła wiadomość e-mail, gdy nadejdzie nowa wiadomość.
     Gdy wyzwalacz sprawdza kolejki i znajduje się nowy komunikat, aplikacja logiki działa wybranej operacji dla znaleziona wiadomość.

   * **Przykład akcji**: Wprowadź treść wiadomości i inne szczegóły. 

     ![Zawartość komunikatu i szczegóły](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

     Gdy wszystko będzie gotowe, nadal tworzenia przepływu pracy aplikacji logiki, dodając inne żądane akcje. Na przykład można dodać akcję, która wysyła wiadomość e-mail, potwierdzenia, czy wiadomość została wysłana.

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/servicebus/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)