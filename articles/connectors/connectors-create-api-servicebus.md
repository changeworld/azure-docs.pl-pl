---
title: Wysyłanie i odbieranie wiadomości za pomocą Azure Service Bus-Azure Logic Apps | Microsoft Docs
description: Konfigurowanie obsługi komunikatów w chmurze dla przedsiębiorstw przy użyciu Azure Service Bus w Azure Logic Apps
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
ms.openlocfilehash: 944bac44c1fc6504dfe1a93df5760ccf4ee46fa0
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982209"
---
# <a name="exchange-messages-in-the-cloud-with-azure-service-bus-and-azure-logic-apps"></a>Wymiana komunikatów w chmurze przy użyciu Azure Service Bus i Azure Logic Apps

Dzięki usłudze Azure Logic Apps i łącznikowi usługi Azure Service Bus możesz tworzyć automatyczne zadania i przepływy pracy, które przesyłają dane, takie jak zamówienia sprzedaży i zakupu, dzienniki oraz przesunięcia magazynowe między aplikacjami w organizacji. Łącznik nie tylko monitoruje, wysyła i zarządza komunikatami, ale wykonuje także akcje z kolejkami, sesjami, tematami, subskrypcjami i tak dalej, na przykład:

* Monitoruj po nadejściu wiadomości (Autouzupełnianie) lub odebraniu (wgląd-blokada) w kolejkach, tematach i subskrypcjach tematów. 
* Wysyłanie komunikatów.
* Tworzenie i usuwanie subskrypcji tematu.
* Zarządzanie komunikatami w kolejkach i subskrypcjami tematów, na przykład pobieranie, pobieranie odroczone, pełne, odkładanie, porzucanie i martwe.
* Odnów blokady komunikatów i sesji w kolejkach i subskrypcjach tematów.
* Zamknij sesje w kolejkach i tematach.

Można użyć wyzwalaczy, które pobierają odpowiedzi z Service Bus i udostępnić dane wyjściowe innym akcjom w usłudze Logic Apps. Możesz również mieć inne akcje, które używają danych wyjściowych z Service Bus akcji. Jeśli dopiero zaczynasz Service Bus i Logic Apps, zapoznaj [się z tematem co to jest Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Service Bus przestrzeń nazw i jednostki obsługi komunikatów, takie jak kolejka. Jeśli nie masz tych elementów, Dowiedz się, jak [utworzyć Service Bus przestrzeń nazw i kolejkę](../service-bus-messaging/service-bus-create-namespace-portal.md). 

  Te elementy muszą znajdować się w tej samej subskrypcji platformy Azure co Aplikacje logiki, które używają tych elementów.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której ma być używana Service Bus. Twoja aplikacja logiki musi znajdować się w tej samej subskrypcji platformy Azure co Usługa Service Bus. Aby rozpocząć pracę z wyzwalaczem Service Bus, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji Service Bus, uruchom aplikację logiki z innym wyzwalaczem, na przykład wyzwalaczem **cyklu** .

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Sprawdź uprawnienia

Upewnij się, że aplikacja logiki ma uprawnienia do uzyskiwania dostępu do przestrzeni nazw Service Bus. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Przejdź do *przestrzeni nazw*Service Bus. Na stronie przestrzeń nazw w obszarze **Ustawienia**wybierz pozycję **zasady dostępu**współdzielonego. W obszarze **oświadczenia**Sprawdź, czy masz uprawnienia do **zarządzania** tą przestrzenią nazw

   ![Zarządzanie uprawnieniami dla przestrzeni nazw Service Bus](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Pobierz parametry połączenia dla przestrzeni nazw Service Bus. Ten ciąg jest potrzebny, gdy wprowadzisz informacje o połączeniu w aplikacji logiki.

   1. Wybierz **RootManageSharedAccessKey**. 
   
   1. Obok podstawowych parametrów połączenia wybierz przycisk Kopiuj. Zapisz parametry połączenia do późniejszego użycia.

      ![Kopiuj parametry połączenia Service Bus przestrzeni nazw](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Aby sprawdzić, czy parametry połączenia są skojarzone z przestrzenią nazw Service Bus, czy z jednostką obsługi komunikatów, taką jak kolejka, Wyszukaj parametry `EntityPath`połączenia dla  parametru. Jeśli ten parametr zostanie znaleziony, ciąg połączenia jest przeznaczony dla określonej jednostki i nie jest poprawnym ciągiem używanym z aplikacją logiki.

## <a name="add-trigger-or-action"></a>Dodaj wyzwalacz lub akcję

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. Aby dodać *wyzwalacz* do pustej aplikacji logiki, w polu wyszukiwania wprowadź ciąg "Azure Service Bus" jako filtr. Na liście Wyzwalacze wybierz wyzwalacz, który chcesz. 

   Na przykład, aby wyzwolić aplikację logiki, gdy nowy element zostanie wysłany do kolejki Service Bus, wybierz ten wyzwalacz: **Po odebraniu komunikatu w kolejce (Autouzupełnianie)**

   ![Wybierz wyzwalacz Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Niektóre wyzwalacze mogą zwrócić jeden lub komunikaty, na przykład wyzwalacz, **gdy co najmniej jeden komunikat dociera do kolejki (Autouzupełnianie)** . Kiedy te wyzwalacze są wyzwalane, zwracają między jedną i liczbę komunikatów określonych przez właściwość **maksymalnej liczby komunikatów** wyzwalacza.

   *Wszystkie wyzwalacze Service Bus są wyzwalaczami o długim sondowaniu*, co oznacza, że wyzwalany jest wyzwalacz, wyzwalacz przetwarza wszystkie komunikaty, a następnie czeka 30 sekund, aby więcej komunikatów pojawiło się w kolejce lub subskrypcji tematu. 
   Jeśli w ciągu 30 sekund nie pojawią się żadne komunikaty, uruchomienie wyzwalacza zostanie pominięte. 
   W przeciwnym razie wyzwalacz będzie kontynuował odczytywanie wiadomości do momentu, gdy subskrypcja kolejki lub tematu nie jest pusta. Sonda następnego wyzwalacza zależy od interwału cyklu określonego we właściwościach wyzwalacza.

1. Aby dodać *akcję* do istniejącej aplikacji logiki, wykonaj następujące kroki: 

   1. W ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**. 

      Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. 
      Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

   1. W polu wyszukiwania wprowadź ciąg "Azure Service Bus" jako filtr. 
   Na liście Akcje wybierz żądaną akcję. 
 
      Na przykład wybierz tę akcję: **Wyślij wiadomość**

      ![Wybierz akcję Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Jeśli łączysz aplikację logiki z przestrzenią nazw Service Bus po raz pierwszy, projektant aplikacji logiki będzie teraz monitował o informacje o połączeniu. 

   1. Podaj nazwę połączenia i wybierz swoją przestrzeń nazw Service Bus.

      ![Utwórz połączenie Service Bus, część 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Aby zamiast tego wprowadzić parametry połączenia, wybierz pozycję **ręcznie wprowadź informacje o połączeniu**. 
      Jeśli nie masz parametrów połączenia, Dowiedz się, [jak znaleźć parametry połączenia](#permissions-connection-string).

   1. Wybierz zasady Service Bus a następnie wybierz pozycję **Utwórz**.

      ![Utwórz połączenie Service Bus, część 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

1. Na potrzeby tego przykładu wybierz żądaną jednostkę obsługi komunikatów, na przykład kolejkę lub temat. W tym przykładzie wybierz kolejkę Service Bus. 
   
   ![Wybierz kolejkę Service Bus](./media/connectors-create-api-azure-service-bus/service-bus-select-queue.png)

1. Podaj niezbędne szczegóły wyzwalacza lub akcji. Na potrzeby tego przykładu postępuj zgodnie z odpowiednimi instrukcjami dla wyzwalacza lub akcji: 

   * **Dla wyzwalacza przykładu**: Ustawianie interwału sondowania i częstotliwości sprawdzania kolejki.

     ![Konfigurowanie interwału sondowania](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

     Gdy wszystko będzie gotowe, Kontynuuj tworzenie przepływu pracy aplikacji logiki, dodając odpowiednie akcje. Na przykład możesz dodać akcję, która wysyła wiadomość e-mail po nadejściu nowej wiadomości.
     Gdy wyzwalacz sprawdzi swoją kolejkę i odnajdzie nowy komunikat, aplikacja logiki uruchamia wybrane akcje dla znalezionego komunikatu.

   * **Dla przykładowej akcji**: Wprowadź treść wiadomości i inne szczegóły. 

     ![Podaj zawartość wiadomości i szczegóły](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

     Gdy wszystko będzie gotowe, Kontynuuj tworzenie przepływu pracy aplikacji logiki, dodając inne żądane akcje. Na przykład można dodać akcję, która wysyła wiadomość e-mail z potwierdzeniem wysłania wiadomości.

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

## <a name="connector-reference"></a>Dokumentacja łączników

Łącznik Service Bus może zaoszczędzić do 1 500 unikatowych sesji jednocześnie z poziomu usługi Service Bus do pamięci podręcznej łącznika. Jeśli liczba sesji przekracza ten limit, stare sesje są usuwane z pamięci podręcznej. Aby poznać inne szczegóły techniczne dotyczące wyzwalaczy, akcji i limitów, które są opisane przez opis łącznika OpenAPI (dawniej Swagger), przejrzyj [stronę odwołania](/connectors/servicebus/)łącznika.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
