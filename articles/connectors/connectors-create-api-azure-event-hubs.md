---
title: Łączenie się z centrum zdarzeń platformy Azure
description: Tworzenie zautomatyzowanych zadań i przepływów pracy monitorujących zdarzenia i zarządzania nimi przy użyciu usługi Azure Event Hubs i usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 32fa54ef0d8eccaf8745ee37cb028d4f3c6d73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247295"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorowanie, odbieranie i wysyłanie zdarzeń za pomocą usługi Azure Event Hubs i usługi Azure Logic Apps

W tym artykule pokazano, jak można monitorować zdarzenia wysyłane do [usługi Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) i zarządzać nimi z wewnątrz aplikacji logiki za pomocą łącznika usługi Azure Event Hubs. Dzięki temu możesz tworzyć aplikacje logiki, które automatyzują zadania i przepływy pracy służące do sprawdzania, wysyłania i otrzymywania zdarzeń z centrum zdarzeń. Aby uzyskać informacje techniczne dotyczące łącznika, zobacz odwołanie</a> [do łącznika usługi Azure Event Hubs](https://docs.microsoft.com/connectors/eventhubs/).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* [Obszar nazw centrów zdarzeń platformy Azure i Centrum zdarzeń](../event-hubs/event-hubs-create.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do Centrum zdarzeń. Aby uruchomić aplikację logiki za pomocą wyzwalacza usługi Azure Event Hubs, potrzebujesz [pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) i szybki [start: Utwórz pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Sprawdź uprawnienia i pobierz parametry połączenia

Aby upewnić się, że aplikacja logiki może uzyskać dostęp do Centrum zdarzeń, sprawdź uprawnienia i pobierz parametry połączenia dla obszaru nazw centrum zdarzeń.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Przejdź do obszaru *nazw*Centrum zdarzeń, a nie do określonego Centrum zdarzeń. 

1. W menu obszaru nazw w obszarze **Ustawienia**wybierz pozycję **Zasady dostępu współdzielonego**. W obszarze **Oświadczenia**sprawdź, czy masz uprawnienia **do zarządzania** dla tej przestrzeni nazw.

   ![Zarządzanie uprawnieniami do obszaru nazw Centrum zdarzeń](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Jeśli chcesz później ręcznie wprowadzić informacje o połączeniu, pobierz ciąg połączenia dla obszaru nazw centrum zdarzeń.

   1. W obszarze **Zasady**wybierz pozycję **RootManageSharedAccessKey**.

   1. Znajdź parametry połączenia klucza podstawowego. Wybierz przycisk kopiowania i zapisz ciąg połączenia do późniejszego użycia.

      ![Kopiuj ciąg połączenia obszaru nazw centrów zdarzeń](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Aby potwierdzić, czy parametry połączenia są skojarzone z obszarem nazw centrum zdarzeń, czy z `EntityPath`  określonym centrum zdarzeń, upewnij się, że parametry połączenia nie mają parametru. Jeśli znajdziesz ten parametr, parametry połączenia jest dla określonego Centrum zdarzeń "jednostki" i nie jest poprawny ciąg do użycia z aplikacją logiki.

1. Teraz kontynuuj z [wyzwalaczem Dodaj centra zdarzeń](#add-trigger) lub [Dodaj akcję Centrum zdarzeń](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Wyzwalanie Dodaj centra zdarzeń

W usłudze Azure Logic Apps każda aplikacja logiki musi zaczynać się od [wyzwalacza,](../logic-apps/logic-apps-overview.md#logic-app-concepts)który uruchamia się, gdy występuje określone zdarzenie lub gdy zostanie spełniony określony warunek. Za każdym razem, gdy wyzwalacz jest uruchamiany, aparat aplikacji logiki tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji.

W tym przykładzie pokazano, jak można uruchomić przepływ pracy aplikacji logiki, gdy nowe zdarzenia są wysyłane do Centrum zdarzeń. 

1. W witrynie Azure portal lub visual studio utwórz pustą aplikację logiki, która otwiera Projektanta aplikacji logiki. W tym przykładzie użyto witryny Azure portal.

1. W polu wyszukiwania wpisz "centra zdarzeń" jako filtr. Z listy wyzwalaczy wybierz ten wyzwalacz: **Gdy zdarzenia są dostępne w Centrum zdarzeń — Centra zdarzeń**

   ![Wybierz wyzwalacz](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [utwórz połączenie z centrum zdarzeń teraz](#create-connection). 

1. W wyzwalaczu podaj informacje o Centrum zdarzeń, które chcesz monitorować. Aby uzyskać więcej właściwości, otwórz listę **Dodaj nowy parametr.** Wybranie parametru powoduje dodanie tej właściwości do karty wyzwalacza.

   ![Właściwości wyzwalacza](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa Centrum zdarzeń** | Tak | Nazwa Centrum zdarzeń, które chcesz monitorować |
   | **Typ zawartości** | Nie | Typ zawartości wydarzenia. Wartość domyślna to `application/octet-stream`. |
   | **Nazwa grupy konsumentów** | Nie | [Nazwa grupy konsumentów Usługi Event Hub](../event-hubs/event-hubs-features.md#consumer-groups) do użycia do odczytu zdarzeń. Jeśli nie zostanie określona, używana jest domyślna grupa odbiorców. |
   | **Maksymalna liczba zdarzeń** | Nie | Maksymalna liczba zdarzeń. Wyzwalacz zwraca między jednym a liczbą zdarzeń określonych przez tę właściwość. |
   | **Interwał** | Tak | Dodatnia liczba całkowita opisująca, jak często przepływ pracy jest uruchamiany na podstawie częstotliwości |
   | **Częstotliwości** | Tak | Jednostka czasu nawrotu |
   ||||

   **Dodatkowe właściwości**

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Schemat zawartości** | Nie | Schemat zawartości JSON dla zdarzeń do odczytu z Centrum zdarzeń. Na przykład jeśli określisz schemat zawartości, można wyzwolić aplikację logiki tylko dla tych zdarzeń, które pasują do schematu. |
   | **Minimalny klucz partycji** | Nie | Wprowadź minimalny identyfikator [partycji](../event-hubs/event-hubs-features.md#partitions) do odczytu. Domyślnie odczytywane są wszystkie partycje. |
   | **Maksymalny klucz partycji** | Nie | Wprowadź maksymalny identyfikator [partycji](../event-hubs/event-hubs-features.md#partitions) do odczytu. Domyślnie odczytywane są wszystkie partycje. |
   | **Strefa czasowa** | Nie | Ma zastosowanie tylko wtedy, gdy określisz godzinę rozpoczęcia, ponieważ ten wyzwalacz nie akceptuje przesunięcia UTC. Wybierz strefę czasową, którą chcesz zastosować. <p>Aby uzyskać więcej informacji, zobacz [Tworzenie i uruchamianie zadań cyklicznych i przepływów pracy za pomocą usługi Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   | **Godzina rozpoczęcia** | Nie | Podaj godzinę rozpoczęcia w tym formacie: <p>YYYY-MM-DDThh:mm:ss po wybraniu strefy czasowej<p>— lub —<p>YYYY-MM-DDThh:mm:ssZ jeśli nie wybierzesz strefy czasowej<p>Aby uzyskać więcej informacji, zobacz [Tworzenie i uruchamianie zadań cyklicznych i przepływów pracy za pomocą usługi Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   ||||

1. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

1. Teraz kontynuuj dodawanie jednej lub więcej akcji do aplikacji logiki dla zadań, które chcesz wykonać z wynikami wyzwalacza. 

   Na przykład, aby filtrować zdarzenia na podstawie określonej wartości, takiej jak kategoria, można dodać warunek, aby akcja **Wyślij zdarzenie** wysyłała tylko zdarzenia spełniające twój warunek. 

> [!NOTE]
> Wszystkie wyzwalacze Centrum zdarzeń są *wyzwalacze długiego sondowania,* co oznacza, że po uruchomieniu wyzwalacza wyzwalacz przetwarza wszystkie zdarzenia, a następnie czeka na 30 sekund, aby więcej zdarzeń pojawiło się w Centrum zdarzeń.
> Jeśli w ciągu 30 sekund nie zostaną odebrane żadne zdarzenia, uruchomienie wyzwalacza zostanie pominięte. W przeciwnym razie wyzwalacz kontynuuje odczytywanie zdarzeń, dopóki Centrum zdarzeń nie będzie puste.
> Następna ankieta wyzwalacza odbywa się na podstawie interwału cyklu, który można określić we właściwościach wyzwalacza.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Akcja Dodaj centra zdarzeń

W usłudze Azure Logic Apps [akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest krokiem w przepływie pracy, który następuje wyzwalacz lub innej akcji. W tym przykładzie aplikacja logiki rozpoczyna się od wyzwalacza centrum zdarzeń, który sprawdza dostępność nowych zdarzeń w Centrum zdarzeń.

1. W witrynie Azure portal lub visual studio otwórz aplikację logiki w Projektancie aplikacji logiki. W tym przykładzie użyto witryny Azure portal.

1. W obszarze wyzwalacza lub akcji wybierz pozycję **Nowy krok**.

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę łączącą. 
   Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wpisz "centra zdarzeń" jako filtr.
Z listy akcji wybierz tę akcję: **Wyślij zdarzenie - Centra zdarzeń**

   ![Wybierz akcję "Wyślij zdarzenie"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [utwórz połączenie z centrum zdarzeń teraz](#create-connection). 

1. W akcji podaj informacje o zdarzeniach, które chcesz wysłać. Aby uzyskać więcej właściwości, otwórz listę **Dodaj nowy parametr.** Wybranie parametru powoduje dodanie tej właściwości do karty akcji.

   ![Wybierz nazwę Centrum zdarzeń i udostępnij zawartość wydarzenia](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa Centrum zdarzeń** | Tak | Centrum zdarzeń, w którym chcesz wysłać zdarzenie |
   | **Zawartość** | Nie | Zawartość wydarzenia, które chcesz wysłać |
   | **Właściwości** | Nie | Właściwości i wartości aplikacji do wysłania |
   | **Klucz partycji** | Nie | Identyfikator [partycji,](../event-hubs/event-hubs-features.md#partitions) gdzie wysłać zdarzenie |
   ||||

   Na przykład można wysłać dane wyjściowe z wyzwalacza centrum zdarzeń do innego Centrum zdarzeń:

   ![Przykład wysyłania zdarzenia](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Łączenie się z Centrum zdarzeń

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Po wyświetleniu monitu o podanie informacji o połączeniu podaj następujące szczegóły:

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa połączenia** | Tak | <*nazwa połączenia*> | Nazwa do utworzenia dla połączenia |
   | **Obszar nazw centrów zdarzeń** | Tak | <*obszar nazw event-hubs*> | Wybierz obszar nazw Centrum zdarzeń, którego chcesz użyć. |
   |||||  

   Przykład:

   ![Tworzenie połączenia z Centrum zdarzeń](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Aby ręcznie wprowadzić parametry połączenia, wybierz **pozycję Ręcznie wprowadź informacje o połączeniu**. 
   Dowiedz [się, jak znaleźć parametry połączenia](#permissions-connection-string).

2. Wybierz zasady centrum zdarzeń, które mają być używane, jeśli nie zostały jeszcze zaznaczone. Wybierz pozycję **Utwórz**.

   ![Tworzenie połączenia z Centrum zdarzeń, część 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Po utworzeniu połączenia kontynuuj [wyzwalacz Dodaj centra zdarzeń](#add-trigger) lub akcję Dodaj centra [zdarzeń](#add-action).

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger [łącznika,](https://docs.microsoft.com/connectors/eventhubs/)zobacz stronę odwołania łącznika .

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja z etykietą ISE tego łącznika używa [limitów komunikatów ŚRODOWISKA ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)