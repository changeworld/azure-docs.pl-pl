---
title: Łączenie z usługą Azure Event Hubs — Azure Logic Apps
description: Zarządzanie i monitorowanie zdarzeń za pomocą usługi Azure Event Hubs i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 882bae14678d8bfff15b35c63c666a20aeee3d1d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720040"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorowanie, odbieranie i wysyłanie zdarzeń za pomocą usługi Azure Event Hubs i Azure Logic Apps

W tym artykule pokazano, jak monitorować i zarządzać zdarzeniami wysłany do [usługi Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) z wewnątrz aplikacji logiki za pomocą łącznika usługi Azure Event Hubs. Dzięki temu można tworzyć aplikacje logiki, które automatyzują zadania i przepływów pracy na potrzeby sprawdzania, wysyłanie i odbieranie zdarzeń z Centrum zdarzeń. Aby uzyskać informacje techniczne dotyczące łącznika, zobacz [dokumentacja łączników usługi Azure Event Hubs](https://docs.microsoft.com/connectors/eventhubs/)</a>.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* [Przestrzeni nazw usługi Azure Event Hubs i Centrum zdarzeń](../event-hubs/event-hubs-create.md)

* Aplikacja logiki gdzie mają dostęp do Centrum zdarzeń. Aby uruchomić aplikację logiki z wyzwalaczem usługi Azure Event Hubs, musisz mieć [pusta aplikacja logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Sprawdź uprawnienia i pobieranie parametrów połączenia

Aby uzyskać dostęp do Centrum zdarzeń, aplikacja logiki Sprawdź swoje uprawnienia i pobieranie parametrów połączenia dla przestrzeni nazw usługi Event Hubs.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do usługi Event Hubs *przestrzeni nazw*, nie określonym Centrum zdarzeń. 

1. W menu przestrzeni nazw w ramach **ustawienia**, wybierz opcję **zasady dostępu współdzielonego**. W obszarze **oświadczeń**, upewnij się, że masz **Zarządzaj** uprawnienia dla tej przestrzeni nazw.

   ![Zarządzaj uprawnieniami dla przestrzeni nazw Centrum zdarzeń](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Jeśli chcesz później ręcznie wprowadzić informacje o połączeniu, Pobierz parametry połączenia dla przestrzeni nazw usługi Event Hubs.

   1. W obszarze **zasad**, wybierz **RootManageSharedAccessKey**.

   1. Znajdź parametry połączenia klucza podstawowego. Kliknij przycisk kopiowania, a następnie zapisz parametry połączenia w celu późniejszego użycia.

      ![Skopiuj parametry połączenia przestrzeni nazw usługi Event Hubs](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Aby sprawdzić, czy parametry połączenia usługi skojarzono z przestrzeni nazw usługi Event Hubs lub określonym Centrum zdarzeń, upewnij się, parametry połączenia nie ma `EntityPath`  parametru. Jeśli ten parametr, ciąg połączenia jest określonym Centrum zdarzeń "entity" i nie jest prawidłowy ciąg za pomocą aplikacji logiki.

1. Teraz kontynuować [dodać wyzwalacza usługi Event Hubs](#add-trigger) lub [Dodaj akcję usługi Event Hubs](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Dodawanie wyzwalacza usługi Event Hubs

W usłudze Azure Logic Apps, każda aplikacja logiki musi rozpoczynać się [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), który jest aktywowany wystąpienia, gdy zajdzie określone zdarzenie lub po spełnieniu określonego warunku. Każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki i uruchamiania przepływu pracy Twojej aplikacji.

Ten przykład pokazuje, jak można uruchomić przepływu pracy aplikacji logiki, gdy nowe zdarzenia są wysyłane do Centrum zdarzeń. 

1. W witrynie Azure portal lub programu Visual Studio należy utworzyć pustej aplikacji logiki, który zostanie otwarty projektant aplikacji logiki. W tym przykładzie użyto portalu Azure.

1. W polu wyszukiwania wprowadź "event hubs" jako filtr. Z listy wyzwalaczy wybierz następujący wyzwalacz: **Gdy zdarzenia są dostępne w Centrum zdarzeń usługi — Usługa Event Hubs**

   ![Wybierz wyzwalacz](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Jeśli zostanie wyświetlony monit, aby uzyskać informacje dotyczące połączenia, [utworzyć połączenie usługi Event Hubs teraz](#create-connection). 

1. W wyzwalaczu Podaj informacje o Centrum zdarzeń, które chcesz monitorować. Aby uzyskać więcej właściwości, należy otworzyć **dodano nowy parametr** listy. Wybranie parametru spowoduje dodanie tej właściwości na karcie wyzwalacza.

   ![Właściwości wyzwalacza](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa Centrum zdarzeń** | Yes | Nazwa Centrum zdarzeń, które chcesz monitorować |
   | **Typ zawartości** | Nie | Typ zawartości zdarzenia. Wartość domyślna to `application/octet-stream`. |
   | **Nazwa grupy konsumentów** | Nie | [Nazwę grupę konsumentów Centrum zdarzeń](../event-hubs/event-hubs-features.md#consumer-groups) do używania w celu odczytywania zdarzeń. Jeśli nie zostanie określony, domyślna grupa odbiorców jest używana. |
   | **Liczba zdarzeń maksymalna** | Nie | Maksymalna liczba zdarzeń. Wyzwalacz zwróci między jednym i liczbę zdarzeń, określonej przez tę właściwość. |
   | **Interwał** | Yes | Dodatnia liczba całkowita, która w tym artykule opisano, jak często wykonywania przepływu pracy na podstawie częstotliwości |
   | **Częstotliwość** | Yes | Jednostka czasu cyklu |
   ||||

   **Dodatkowe właściwości**

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Schemat zawartości** | Nie | Schemat zawartości JSON dla zdarzeń, które można odczytać z Centrum zdarzeń. Na przykład jeśli określisz schemat zawartości, można uruchomić aplikacji logiki dla tych zdarzeń, które pasuje do schematu. |
   | **Klucz partycji minimalne** | Nie | Wprowadź minimalną [partycji](../event-hubs/event-hubs-features.md#partitions) identyfikator do odczytu. Domyślnie wszystkie partycje są do odczytu. |
   | **Klucz partycji maksymalna** | Nie | Wprowadź maksymalną [partycji](../event-hubs/event-hubs-features.md#partitions) identyfikator do odczytu. Domyślnie wszystkie partycje są do odczytu. |
   | **Strefa czasowa** | Nie | Ma zastosowanie tylko wtedy, gdy określa godziny rozpoczęcia, ponieważ ten wyzwalacz nie akceptuje przesunięcie czasu UTC. Wybierz strefę czasową, który chcesz zastosować. <p>Aby uzyskać więcej informacji, zobacz [tworzenia i wykonywania powtarzających się zadań i przepływów pracy z usługą Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   | **Godzina rozpoczęcia** | Nie | Podaj godzinę rozpoczęcia w następującym formacie: <p>RRRR-MM-Ddtgg w przypadku wybrania strefy czasowej<p>— lub —<p>RRRR-MM-Ddtgg, jeśli nie zaznaczysz strefy czasowej<p>Aby uzyskać więcej informacji, zobacz [tworzenia i wykonywania powtarzających się zadań i przepływów pracy z usługą Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   ||||

1. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**.

1. Teraz kontynuowana, dodając jedną lub więcej akcji do aplikacji logiki dla zadań, które mają być wykonywane z wynikami wyzwalacza. 

   Na przykład do filtrowania zdarzeń, w oparciu o określonej wartości, takich jak kategorii, można dodać warunek tak, aby **wysłać zdarzenie — Event Hubs** akcja spowoduje wysłanie tylko te zdarzenia, które spełniają warunek. 

> [!NOTE]
> Wszystkie wyzwalacze Centrum zdarzeń są *długiego sondowania* wyzwalacze, co oznacza, że gdy wyzwalacza, wyzwalacz przetwarza wszystkie zdarzenia, a następnie czeka 30 sekund na więcej zdarzeń są wyświetlane w Centrum zdarzeń.
> Jeśli żadne zdarzenia nie są odbierane w ciągu 30 sekund, uruchom wyzwalacz zostanie pominięty. W przeciwnym razie wyzwalacz nadal odczytu zdarzenia, dopóki nie Centrum zdarzeń jest pusty.
> Dalej sondowania wyzwalacza ma miejsce na podstawie na interwał cyklu, który określisz we właściwościach tego wyzwalacza.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Dodawanie akcji usługi Event Hubs

W usłudze Azure Logic Apps [akcji](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest to krok w przepływie pracy, występującego wyzwalacza lub innej akcji. W tym przykładzie aplikacja logiki rozpoczyna się od wyzwalacza usługi Event Hubs, która sprawdza, czy są dostępne nowe zdarzenia w Centrum zdarzeń.

1. W witrynie Azure portal lub programu Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki. W tym przykładzie użyto portalu Azure.

1. W obszarze wyzwalacza lub akcji, wybierz opcję **nowy krok**.

   Aby dodać akcję między krokami istniejących, myszą strzałkę nawiązującego połączenie. 
   Wybierz znak plus (**+**) pojawia się, a następnie wybierz **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "event hubs" jako filtr.
Z listy akcji wybierz następującą akcję: **Wyślij zdarzenie — Event Hubs**

   ![Wybierz akcję "Wyślij zdarzenie"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Jeśli zostanie wyświetlony monit, aby uzyskać informacje dotyczące połączenia, [utworzyć połączenie usługi Event Hubs teraz](#create-connection). 

1. W działaniu zawierają informacje dotyczące zdarzenia, które mają zostać wysłane. Aby uzyskać więcej właściwości, należy otworzyć **dodano nowy parametr** listy. Wybranie parametru spowoduje dodanie tej właściwości na karcie akcji.

   ![Wybierz nazwę Centrum zdarzeń i dostarczanie zawartości zdarzeń](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa Centrum zdarzeń** | Yes | Centrum zdarzeń, które chcesz wysłać zdarzenie |
   | **Zawartość** | Nie | Zawartość dla zdarzenia, które mają zostać wysłane |
   | **Właściwości** | Nie | Właściwości aplikacji i wartości do wysłania |
   | **Klucz partycji** | Nie | [Partycji](../event-hubs/event-hubs-features.md#partitions) identyfikator, gdzie wysyłać zdarzenia |
   ||||

   Na przykład możesz wysłać dane wyjściowe z wyzwalacza usługi Event Hubs do innego Centrum zdarzeń:

   ![Wysyłanie zdarzeń przykład](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Nawiązać połączenie z Centrum zdarzeń

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Po wyświetleniu monitu, aby uzyskać informacje o połączeniu, należy podać następujące dane:

   | Właściwość | Wymagany | Value | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa połączenia** | Yes | <*connection-name*> | Nazwa do utworzenia połączenia |
   | **Event Hubs Namespace** | Yes | <*event-hubs-namespace*> | Wybierz przestrzeń nazw usługi Event Hubs, którego chcesz użyć. |
   |||||  

   Na przykład:

   ![Utwórz połączenie z Centrum zdarzeń](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Aby ręcznie wprowadzić parametry połączenia, wybierz opcję **ręcznie wprowadzić informacje o połączeniu**. 
   Dowiedz się, [jak znaleźć parametrów połączenia](#permissions-connection-string).

2. Wybierz zasady usługi Event Hubs, aby użyć, jeśli jeszcze nie wybrano. Wybierz pozycję **Utwórz**.

   ![Utwórz połączenie z Centrum zdarzeń, część 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Po utworzeniu połączenia, kontynuuj [wyzwalacza usługi Event Hubs Dodaj](#add-trigger) lub [akcji Dodaj usługi Event Hubs](#add-action).

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegóły techniczne, takich jak wyzwalacze, akcje i ograniczeń, zgodnie z opisem w łącznika interfejsu OpenAPI (dawniej Swagger) plików, zobacz [strona referencyjna łącznika](/connectors/eventhubs/).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)