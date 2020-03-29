---
title: Wymiana wiadomości za pomocą usługi Azure Service Bus
description: Tworzenie zautomatyzowanych zadań i przepływów pracy, które wysyłają i odbierają wiadomości przy użyciu usługi Azure Service Bus w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 1b38b8508dbe17d42bf191149410f5db638cf834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261623"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Wymiana wiadomości w chmurze przy użyciu usług Azure Logic Apps i usługi Azure Service Bus

Za pomocą [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) i łącznika usługi Azure Service [Bus](../service-bus-messaging/service-bus-messaging-overview.md) można tworzyć zautomatyzowane zadania i przepływy pracy, które przesyłają dane, takie jak zamówienia sprzedaży i zakupu, arkusze i ruchy zapasów w aplikacjach dla twojej organizacji. Łącznik nie tylko monitoruje, wysyła i zarządza wiadomościami, ale także wykonuje akcje z kolejkami, sesjami, tematami, subskrypcjami i tak dalej, na przykład:

* Monitoruj, kiedy wiadomości przychodzą (autouzupełniaj) lub są odbierane (zaglądać do blokowania) w kolejkach, tematach i subskrypcjach tematów.
* Wysyłaj wiadomości.
* Tworzenie i usuwanie subskrypcji tematów.
* Zarządzanie wiadomościami w kolejkach i subskrypcjach tematów, na przykład, pobierz, pobierz odroczone, kompletne, odroczenie, porzucenie i martwą literę.
* Odnawianie blokad wiadomości i sesji w kolejkach i subskrypcjach tematów.
* Zamykanie sesji w kolejkach i tematach.

Można użyć wyzwalaczy, które otrzymują odpowiedzi z usługi Service Bus i udostępnić dane wyjściowe do innych akcji w aplikacjach logiki. Można również mieć inne akcje użyć danych wyjściowych z akcji usługi Service Bus. Jeśli jesteś nowym użytkownikem usługi Service Bus i aplikacje logiki, sprawdź [Co to jest usługa Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) i Co to jest usługa Azure Logic [Apps?](../logic-apps/logic-apps-overview.md)

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* A Service Bus namespace i jednostki obsługi wiadomości, takich jak kolejki. Te elementy i aplikacja logiki należy użyć tej samej subskrypcji platformy Azure. Jeśli nie masz tych elementów, dowiedz się, jak [utworzyć obszar nazw usługi Service Bus i kolejkę.](../service-bus-messaging/service-bus-create-namespace-portal.md)

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której używasz obszaru nazw usługi Service Bus i jednostki obsługi wiadomości. Aplikacja logiki i magistrala usługi muszą używać tej samej subskrypcji platformy Azure. Aby uruchomić przepływ pracy za pomocą wyzwalacza usługi Service Bus, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji usługi Service Bus w przepływie pracy, uruchom aplikację logiki za pomocą innego wyzwalacza, na przykład [wyzwalacza cyklu](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Sprawdź uprawnienia

Upewnij się, że aplikacja logiki ma uprawnienia dostępu do obszaru nazw usługi Service Bus.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Przejdź do *obszaru nazw*usługi Service Bus . Na stronie obszaru nazw w obszarze **Ustawienia**wybierz pozycję **Zasady dostępu udostępnionego**. W obszarze **Oświadczenia**sprawdź, czy masz uprawnienia **do zarządzania** dla tej przestrzeni nazw.

   ![Zarządzanie uprawnieniami do obszaru nazw usługi Service Bus](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Pobierz ciąg połączenia dla obszaru nazw usługi Service Bus. Ten ciąg jest potrzebny po podaniu informacji o połączeniu w aplikacji logiki.

   1. W okienku **Zasady dostępu udostępnionego** wybierz pozycję **RootManageSharedAccessKey**.
   
   1. Obok podstawowego ciągu połączenia wybierz przycisk kopiowania. Zapisz ciąg połączenia do późniejszego użycia.

      ![Kopiuj ciąg połączenia obszaru nazw usługi Service Bus](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Aby sprawdzić, czy parametry połączenia są skojarzone z obszarem nazw usługi Service Bus, czy `EntityPath`  z jednostką obsługi wiadomości, taką jak kolejka, wyszukaj parametry połączenia dla tego parametru. Jeśli znajdziesz ten parametr, parametry połączenia jest dla określonej jednostki i nie jest poprawny ciąg do użycia z aplikacją logiki.

## <a name="add-service-bus-trigger"></a>Dodaj wyzwalacz usługi Service Bus

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz pustą aplikację logiki w projektancie aplikacji logiki.

1. W polu wyszukiwania wprowadź "azure service bus" jako filtr. Z listy wyzwalaczy wybierz odpowiedni wyzwalacz.

   Na przykład, aby wyzwolić aplikację logiki, gdy nowy element zostanie wysłany do kolejki usługi Service Bus, wybierz **wyzwalacz Po odebraniu wiadomości w kolejce (autouzupełnianie).**

   ![Wybieranie wyzwalacza usługi Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Wszystkie wyzwalacze usługi Service Bus są *wyzwalaczami długiego sondowania.* Ten opis oznacza, że po uruchomieniu wyzwalacza wyzwalacz przetwarza wszystkie komunikaty, a następnie czeka 30 sekund na więcej wiadomości, aby pojawić się w kolejce lub subskrypcji tematu. Jeśli w ciągu 30 sekund nie pojawią się żadne komunikaty, uruchomienie wyzwalacza zostanie pominięte. W przeciwnym razie wyzwalacz kontynuuje odczytywanie wiadomości, dopóki subskrypcja kolejki lub tematu nie będzie pusta. Następna ankieta wyzwalacza jest oparta na interwału cyklu określonego we właściwościach wyzwalacza.

   Niektóre wyzwalacze, takie jak **Gdy jeden lub więcej wiadomości dociera do wyzwalacza kolejki (autouzupełniaj),** może zwrócić jedną lub więcej wiadomości. Gdy te wyzwalacze są uruchamiane, zwracają między jednym a liczbą wiadomości, która jest określona przez właściwość **liczba wiadomości maksymalna** wyzwalacza.

1. Jeśli wyzwalacz łączy się z obszarem nazw usługi Service Bus po raz pierwszy, wykonaj następujące kroki, gdy projektant aplikacji logiki monituje o informacje o połączeniu.

   1. Podaj nazwę połączenia i wybierz obszar nazw usługi Service Bus.

      ![Tworzenie połączenia service bus, część 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Aby ręcznie wprowadzić parametry połączenia, wybierz opcję **Ręcznie wprowadź informacje o połączeniu**. Jeśli nie masz parametry połączenia, dowiedz się, [jak znaleźć parametry połączenia](#permissions-connection-string).

   1. Wybierz zasadę usługi Service Bus i wybierz pozycję **Utwórz**.

      ![Tworzenie połączenia service bus, część 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Wybierz odpowiednią encję obsługi wiadomości, taką jak kolejka lub temat. W tym przykładzie wybierz kolejkę usługi Service Bus.
   
      ![Wybierz kolejkę usługi Service Bus](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Podaj informacje niezbędne dla wybranego wyzwalacza. Aby dodać inne dostępne właściwości do akcji, otwórz listę **Dodaj nowy parametr** i wybierz żądane właściwości.

   W tym przykładzie wyzwalacz wybierz interwał sondowania i częstotliwość sprawdzania kolejki.

   ![Ustawianie interwału sondowania](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Aby uzyskać więcej informacji na temat dostępnych wyzwalaczy i właściwości, zobacz [stronę odwołania](/connectors/servicebus/)łącznika .

1. Kontynuuj tworzenie aplikacji logiki, dodając żądane akcje.

   Na przykład można dodać akcję, która wysyła wiadomość e-mail po odebraniu nowej wiadomości. Gdy wyzwalacz sprawdza kolejkę i znajduje nową wiadomość, aplikacja logiki uruchamia wybrane akcje dla znalezionej wiadomości.

## <a name="add-service-bus-action"></a>Dodaj akcję usługi Service Bus

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w projektancie aplikacji logiki.

1. W obszarze kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**.

   Możesz też dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między tymi krokami. Wybierz wyświetlony znak**+** plus ( ) i wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**w polu wyszukiwania wpisz jako filtr "azure service bus". Z listy akcji wybierz odpowiednią akcję. 

   W tym przykładzie wybierz akcję **Wyślij wiadomość.**

   ![Akcja Wybierz usługę Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Jeśli akcja łączy się z obszarem nazw usługi Service Bus po raz pierwszy, wykonaj następujące kroki, gdy projektant aplikacji logiki monituje o informacje o połączeniu.

   1. Podaj nazwę połączenia i wybierz obszar nazw usługi Service Bus.

      ![Tworzenie połączenia service bus, część 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Aby ręcznie wprowadzić parametry połączenia, wybierz opcję **Ręcznie wprowadź informacje o połączeniu**. Jeśli nie masz parametry połączenia, dowiedz się, [jak znaleźć parametry połączenia](#permissions-connection-string).

   1. Wybierz zasadę usługi Service Bus i wybierz pozycję **Utwórz**.

      ![Tworzenie połączenia service bus, część 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Wybierz odpowiednią encję obsługi wiadomości, taką jak kolejka lub temat. W tym przykładzie wybierz kolejkę usługi Service Bus.

      ![Wybierz kolejkę usługi Service Bus](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Podaj szczegóły niezbędne do wybranej akcji. Aby dodać inne dostępne właściwości do akcji, otwórz listę **Dodaj nowy parametr** i wybierz żądane właściwości.

   Na przykład wybierz **właściwości Zawartość** i Typ **zawartości,** aby dodać je do akcji. Następnie określ zawartość wiadomości, którą chcesz wysłać.

   ![Podaj zawartość wiadomości i szczegóły](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Aby uzyskać więcej informacji na temat dostępnych akcji i ich właściwości, zobacz [stronę odwołania](/connectors/servicebus/)łącznika .

1. Kontynuuj tworzenie aplikacji logiki, dodając inne akcje, które chcesz.

   Na przykład można dodać akcję, która wysyła wiadomość e-mail, aby potwierdzić, że wiadomość została wysłana.

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

## <a name="connector-reference"></a>Dokumentacja łączników

Łącznik usługi Service Bus można zapisać do 1500 unikatowych sesji naraz z magistrali usług do pamięci podręcznej łącznika. Jeśli liczba sesji przekracza ten limit, stare sesje są usuwane z pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Sesje wiadomości](../service-bus-messaging/message-sessions.md).

Aby uzyskać inne informacje techniczne dotyczące wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną](/connectors/servicebus/)łącznika . Aby uzyskać więcej informacji o usłudze Azure Service Bus Messaging, zobacz [Co to jest usługa Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
