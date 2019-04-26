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
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: a59f21478f85f238d91c01faed44d8e49cb15f0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60313537"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorowanie, odbieranie i wysyłanie zdarzeń za pomocą usługi Azure Event Hubs i Azure Logic Apps

W tym artykule pokazano, jak monitorować i zarządzać zdarzeniami wysłany do [usługi Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) z wewnątrz aplikacji logiki za pomocą łącznika usługi Azure Event Hubs. Dzięki temu można tworzyć aplikacje logiki, które automatyzują zadania i przepływów pracy na potrzeby sprawdzania, wysyłanie i odbieranie zdarzeń z Centrum zdarzeń.

Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Aby uzyskać informacje techniczne dotyczące łącznika, zobacz <a href="https://docs.microsoft.com/connectors/eventhubs/" target="blank">dokumentacja łączników usługi Azure Event Hubs</a>.

## <a name="prerequisites"></a>Wymagania wstępne

* [Przestrzeni nazw usługi Azure Event Hubs i Centrum zdarzeń](../event-hubs/event-hubs-create.md)

* Aplikacja logiki gdzie mają dostęp do Centrum zdarzeń. Aby uruchomić aplikację logiki z wyzwalaczem usługi Azure Event Hubs, musisz mieć [pusta aplikacja logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Sprawdź uprawnienia i pobieranie parametrów połączenia

Aby uzyskać dostęp do Centrum zdarzeń, aplikacja logiki Sprawdź swoje uprawnienia i pobieranie parametrów połączenia dla przestrzeni nazw usługi Event Hubs.

1. Zaloguj się w witrynie <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

2. Przejdź do usługi Event Hubs *przestrzeni nazw*, nie określonym Centrum zdarzeń. Na stronie przestrzeń nazw w ramach **ustawienia**, wybierz **zasady dostępu współdzielonego**. W obszarze **oświadczeń**, upewnij się, że masz **Zarządzaj** uprawnienia dla tej przestrzeni nazw.

   ![Zarządzaj uprawnieniami dla przestrzeni nazw Centrum zdarzeń](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Jeśli chcesz później ręcznie wprowadzić informacje o połączeniu, Pobierz parametry połączenia dla przestrzeni nazw usługi Event Hubs.

   1. W obszarze **zasad**, wybierz **RootManageSharedAccessKey**.

   2. Znajdź parametry połączenia klucza podstawowego. Kliknij przycisk kopiowania, a następnie zapisz parametry połączenia w celu późniejszego użycia.

      ![Skopiuj parametry połączenia przestrzeni nazw usługi Event Hubs](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Aby sprawdzić, czy parametry połączenia usługi skojarzono z przestrzeni nazw usługi Event Hubs lub określonym Centrum zdarzeń, upewnij się, parametry połączenia nie ma `EntityPath`  parametru. Jeśli ten parametr, ciąg połączenia jest określonym Centrum zdarzeń "entity" i nie jest prawidłowy ciąg za pomocą aplikacji logiki.

4. Teraz kontynuować [dodać wyzwalacza usługi Event Hubs](#add-trigger) lub [Dodaj akcję usługi Event Hubs](#add-action).

<a name="add-trigger"></a>

## <a name="add-an-event-hubs-trigger"></a>Dodawanie wyzwalacza usługi Event Hubs

W usłudze Azure Logic Apps, każda aplikacja logiki musi rozpoczynać się [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), który jest aktywowany wystąpienia, gdy zajdzie określone zdarzenie lub po spełnieniu określonego warunku. Każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki i uruchamiania przepływu pracy Twojej aplikacji.

Ten przykład pokazuje, jak można uruchomić przepływu pracy aplikacji logiki, gdy nowe zdarzenia są wysyłane do Centrum zdarzeń.

1. W witrynie Azure portal lub programu Visual Studio należy utworzyć pustej aplikacji logiki, który zostanie otwarty projektant aplikacji logiki. W tym przykładzie użyto portalu Azure.

2. W polu wyszukiwania wprowadź "event hubs" jako filtr. Z listy wyzwalaczy wybierz wyzwalacz, który ma.

   W tym przykładzie użyto tego wyzwalacza:

   **Event Hubs — gdy zdarzenia są dostępne w Centrum zdarzeń**

   ![Wybierz wyzwalacz](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

3. Jeśli zostanie wyświetlony monit, aby uzyskać informacje dotyczące połączenia, [utworzyć połączenie usługi Event Hubs teraz](#create-connection). Lub, jeśli istnieje już połączenie, podaj informacje niezbędne dla wyzwalacza.

   1. Z **nazwy Centrum zdarzeń** wybierz Centrum zdarzeń, które chcesz monitorować.

      ![Określ Centrum zdarzeń lub grupy odbiorców](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

   2. Wybierz interwał i częstotliwość jak często ma wyzwalacz, aby sprawdzić Centrum zdarzeń.

   3. Aby opcjonalnie wybrać opcje zaawansowane wyzwalacza, wybierz **Pokaż opcje zaawansowane**.

      ![Wyzwalacz opcje zaawansowane](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

      | Właściwość | Szczegóły | 
      |----------|---------| 
      | Typ zawartości  | Wybierz typ zawartości zdarzenia. Wartość domyślna to "application/octet-stream". |
      | Schemat zawartości | Wprowadź schemat zawartości w formacie JSON dla zdarzeń, które są odczytywane z Centrum zdarzeń. |
      | Nazwa grupy konsumentów | Wprowadź Centrum zdarzeń [Nazwa grupy konsumentów](../event-hubs/event-hubs-features.md#consumer-groups) do odczytywania zdarzeń. Jeśli nie zostanie określony, domyślna grupa odbiorców jest używana. |
      | Klucz partycji minimalne | Wprowadź minimalną [partycji](../event-hubs/event-hubs-features.md#partitions) identyfikator do odczytu. Domyślnie wszystkie partycje są do odczytu. |
      | Klucz partycji maksymalna | Wprowadź maksymalną [partycji](../event-hubs/event-hubs-features.md#partitions) identyfikator do odczytu. Domyślnie wszystkie partycje są do odczytu. |
      | Liczba zdarzeń maksymalna | Wprowadź wartość maksymalną liczbę zdarzeń. Wyzwalacz zwróci między jednym i liczbę zdarzeń, określonej przez tę właściwość. |
      |||

4. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**.

5. Teraz kontynuowana, dodając jedną lub więcej akcji do aplikacji logiki dla zadań, które mają być wykonywane z wynikami wyzwalacza.

> [!NOTE]
> Wszystkie wyzwalacze Centrum zdarzeń są *długiego sondowania* wyzwalacze, co oznacza, że gdy wyzwalacza, wyzwalacz przetwarza wszystkie zdarzenia, a następnie czeka 30 sekund na więcej zdarzeń są wyświetlane w Centrum zdarzeń.
> Jeśli żadne zdarzenia nie są odbierane w ciągu 30 sekund, uruchom wyzwalacz zostanie pominięty. W przeciwnym razie wyzwalacz nadal odczytu zdarzenia, dopóki nie Centrum zdarzeń jest pusty.
> Dalej sondowania wyzwalacza ma miejsce na podstawie na interwał cyklu, który określisz we właściwościach tego wyzwalacza.

<a name="add-action"></a>

## <a name="add-an-event-hubs-action"></a>Dodawanie akcji usługi Event Hubs

W usłudze Azure Logic Apps [akcji](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest to krok w przepływie pracy, występującego wyzwalacza lub innej akcji. W tym przykładzie aplikacja logiki rozpoczyna się od wyzwalacza usługi Event Hubs, która sprawdza, czy są dostępne nowe zdarzenia w Centrum zdarzeń.

1. W witrynie Azure portal lub programu Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki. W tym przykładzie użyto portalu Azure.

2. W obszarze wyzwalacza lub akcji, wybierz opcję **nowy krok** > **Dodaj akcję**.

   Aby dodać akcję między krokami istniejących, myszą strzałkę nawiązującego połączenie. 
   Wybierz znak plus (**+**) pojawia się, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "event hubs" jako filtr.
Z listy akcji wybierz akcję, którą chcesz.

   W tym przykładzie wybierz następującą akcję: **Event Hubs — wysyłanie zdarzeń**

   ![Wybierz pozycję "Event Hubs — wysyłanie zdarzeń"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

4. Jeśli zostanie wyświetlony monit, aby uzyskać informacje dotyczące połączenia, [utworzyć połączenie usługi Event Hubs teraz](#create-connection). Lub, jeśli istnieje już połączenie, podaj informacje niezbędne do działania.

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | Nazwa centrum zdarzeń | Yes | Wybierz Centrum zdarzeń, które chcesz wysłać zdarzenie |
   | Zdarzenie zawartości | Nie | Zawartość dla zdarzenia, które mają zostać wysłane |
   | Właściwości | Nie | Właściwości aplikacji i wartości do wysłania |
   ||||

   Na przykład:

   ![Wybierz nazwę Centrum zdarzeń i dostarczanie zawartości zdarzeń](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

5. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Nawiązać połączenie z Centrum zdarzeń

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Po wyświetleniu monitu, aby uzyskać informacje o połączeniu, należy podać następujące dane:

   | Właściwość | Wymagany | Value | Opis |
   |----------|----------|-------|-------------|
   | Nazwa połączenia | Yes | <*connection-name*> | Nazwa do utworzenia połączenia |
   | Przestrzeń nazw usługi Event Hubs | Yes | <*event-hubs-namespace*> | Wybierz przestrzeń nazw usługi Event Hubs, którego chcesz użyć. |
   |||||  

   Na przykład:

   ![Utwórz połączenie z Centrum zdarzeń](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Aby ręcznie wprowadzić parametry połączenia, wybierz opcję **ręcznie wprowadzić informacje o połączeniu**. 
   Dowiedz się, [jak znaleźć parametrów połączenia](#permissions-connection-string).

2. Wybierz zasady usługi Event Hubs, aby użyć, jeśli jeszcze nie wybrano. Wybierz pozycję **Utwórz**.

   ![Utwórz połączenie z Centrum zdarzeń, część 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Po utworzeniu połączenia, kontynuuj [wyzwalacza usługi Event Hubs Dodaj](#add-trigger) lub [akcji Dodaj usługi Event Hubs](#add-action).

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegóły techniczne, takich jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku struktury Swagger łącznika, zobacz [strona referencyjna łącznika](/connectors/eventhubs/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)