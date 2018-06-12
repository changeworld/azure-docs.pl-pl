---
title: Połączenie do usługi Azure Event Hubs — aplikacje logiki platformy Azure | Dokumentacja firmy Microsoft
description: Zarządzanie i monitorowanie zdarzeń z usługi Azure Event Hubs i usługi Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 86fc1c3542bea1be840041bb73df15631c066c7e
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294976"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorowanie, odbierania i wysyłania zdarzeń z usługi Azure Event Hubs i usługi Azure Logic Apps 

W tym artykule opisano, jak monitorować i zarządzać zdarzeń wysłanych do [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) z wewnątrz aplikacji logiki z łącznika usługi Azure Event Hubs. W ten sposób można utworzyć aplikacji logiki, które automatyzują zadania i przepływy pracy do sprawdzania, wysyłanie i odbieranie zdarzeń z Centrum zdarzeń. 

Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. Jeśli jesteś nowym użytkownikiem aplikacji logiki, przejrzyj [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Aby uzyskać informacje techniczne dotyczące łącznika, zobacz <a href="https://docs.microsoft.com/connectors/eventhubs/" target="blank">odwołanie łącznika usługi Azure Event Hubs</a>.

## <a name="prerequisites"></a>Wymagania wstępne

* [Nazw centra zdarzeń Azure i Centrum zdarzeń](../event-hubs/event-hubs-create.md)

* Aplikację logiki, w którym chcesz uzyskać dostępu do Centrum zdarzeń. Aby uruchomić aplikację logiki z wyzwalaczem Azure Event Hubs, należy [aplikacji logiki puste](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Sprawdź uprawnienia i pobrać ciągu połączenia

Dla aplikacji logiki do Centrum zdarzeń sprawdź swoje uprawnienia i pobrać ciągu połączenia dla przestrzeni nazw usługi Event Hubs.

1. Zaloguj się w <a href="https://portal.azure.com" target="_blank">Portalu Azure</a>. 

2. Przejdź do centrów zdarzeń *przestrzeni nazw*, nie określonym Centrum zdarzeń. Na stronie przestrzeni nazw w obszarze **ustawienia**, wybierz **zasady dostępu współużytkowanego**. W obszarze **oświadczeń**, sprawdź, czy masz **Zarządzaj** uprawnienia dla tej przestrzeni nazw.

   ![Zarządzaj uprawnieniami dla przestrzeni nazw Centrum zdarzeń](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Jeśli chcesz później ręcznie wprowadzić informacje o połączeniu, należy pobrać ciągu połączenia dla przestrzeni nazw usługi Event Hubs. 

   1. W obszarze **zasad**, wybierz **RootManageSharedAccessKey**. 

   2. Znajdź parametry połączenia klucza podstawowego. Wybierz przycisk Kopiuj i zapisać parametry połączenia do późniejszego użycia.

      ![Skopiuj parametry połączenia w przestrzeni nazw usługi Event Hubs](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Aby sprawdzić, czy ciąg połączenia jest skojarzony z przestrzeni nazw usługi Event Hubs lub przy użyciu koncentratora określonego zdarzenia, upewnij się, ciąg połączenia nie ma `EntityPath` parametru. Jeśli ten parametr, ciąg połączenia jest przeznaczony dla określonego Centrum zdarzeń "entity" i nie jest poprawny ciąg do korzystania z aplikacji logiki.

4. Teraz kontynuować [dodać wyzwalacza usługi Event Hubs](#add-trigger) lub [Dodaj akcję usługi Event Hubs](#add-action).

<a name="add-trigger"></a>

## <a name="add-an-event-hubs-trigger"></a>Dodaj wyzwalacz centra zdarzeń

W aplikacjach logiki platformy Azure, każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), które odbywa się uruchamiany, gdy określonego zdarzenia lub gdy zostanie spełniony określony warunek. Zawsze uruchamiany wyzwalacza aparat Logic Apps tworzy wystąpienie aplikacji logiki i uruchamiania aplikacji przepływu pracy.

Ten przykład przedstawia, jak można uruchomić przepływu pracy aplikacji logiki, gdy nowe zdarzenia są wysyłane do Centrum zdarzeń. 

1. W portalu Azure lub programu Visual Studio tworzenie aplikacji logiki puste, który otwiera projektanta aplikacji logiki. W przykładzie użyto portalu Azure.

2. W polu wyszukiwania wprowadź "event hubs" jako filtr. Na liście wyzwalaczy zaznacz wyzwalacz, który ma. 

   W tym przykładzie użyto tego wyzwalacza: **usługi Event Hubs — gdy zdarzenia są dostępne w Centrum zdarzeń**

   ![Wybierz wyzwalacz](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

3. Jeśli zostanie wyświetlony monit o szczegóły połączenia [utworzyć połączenie usługi Event Hubs teraz](#create-connection). Lub, jeśli połączenie już istnieje, podaj informacje niezbędne do wyzwalacza.

   1. Z **nazwę Centrum zdarzeń** wybierz Centrum zdarzeń, który chcesz monitorować.

      ![Określ Centrum zdarzeń lub grupy odbiorców](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

   2. Wybierz interwał i częstotliwość Częstotliwość trigger, aby sprawdzić Centrum zdarzeń.
 
   3. Opcjonalnie wybierz opcje zaawansowane wyzwalacza, wybierz **Pokaż opcje zaawansowane**.
   
      ![Wyzwalacz, opcje zaawansowane](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

      | Właściwość | Szczegóły | 
      |----------|---------| 
      | Typ zawartości  | Wybierz typ zawartości dla zdarzenia. Wartość domyślna to "application/octet-stream". |
      | Zawartość schematu | Wprowadź schematu zawartości w formacie JSON, dla zdarzeń, które są odczytywane z Centrum zdarzeń. |
      | Nazwa grupy konsumentów | Wprowadź Centrum zdarzeń [Nazwa grupy konsumentów](../event-hubs/event-hubs-features.md#consumer-groups) odczytywanie zdarzeń. Jeśli nie zostanie określony, używana jest domyślna grupa odbiorców. |
      | Klucz partycji minimalne | Wprowadź minimalny [partycji](../event-hubs/event-hubs-features.md#partitions) ID do odczytu. Domyślnie wszystkie partycje są odczytywane. |
      | Klucz partycji maksymalna | Wprowadź maksymalną [partycji](../event-hubs/event-hubs-features.md#partitions) ID do odczytu. Domyślnie wszystkie partycje są odczytywane. |
      | Liczba zdarzeń maksymalne | Wprowadź wartość dla maksymalna liczba zdarzeń. Wyzwalacz zwraca się między jednym a liczbą zdarzeń określona przez tę właściwość. |
      |||

4. Gdy wszystko będzie gotowe, na pasku narzędzi projektanta, wybierz pozycję **zapisać**.

5. Teraz kontynuować dodawanie co najmniej jednej akcji do aplikacji logiki dla zadań, które chcesz wykonać z wynikami wyzwalacza.

> [!NOTE]
> Wszystkie wyzwalacze Centrum zdarzeń są *long sondowania* wyzwalaczy, co oznacza, że gdy wyzwalacza, wyzwalacz przetwarza wszystkie zdarzenia, a następnie oczekiwanie przez 30 sekund więcej zdarzeń mają być widoczne w Centrum zdarzeń.
> Jeśli żadne zdarzenia nie są odbierane w ciągu 30 sekund, uruchom wyzwalacz jest pomijany. W przeciwnym razie wyzwalacza nadal odczytywanie zdarzeń, aż do Centrum zdarzeń jest pusta.
> Następny sondowania wyzwalacza odbywa się na podstawie na interwał cyklu, który określisz we właściwościach tego wyzwalacza.

<a name="add-action"></a>

## <a name="add-an-event-hubs-action"></a>Dodaj akcję centra zdarzeń

W aplikacjach logiki platformy Azure [akcji](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest krokiem w przepływie pracy następujący wyzwalacz inną akcję. Na przykład aplikację logiki rozpoczyna się od wyzwalacz centra zdarzeń, która sprawdza nowych zdarzeń w Centrum zdarzeń. 

1. W portalu Azure lub programu Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki. W przykładzie użyto portalu Azure.

2. W ramach wyzwalacza lub akcji, wybierz **nowy krok** > **Dodaj akcję**.

   Aby dodać akcję między krokami istniejących, myszą strzałkę nawiązującego połączenie. 
   Wybierz znak plus (**+**) zostanie wyświetlony, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "event hubs" jako filtr.
Z listy akcji wybierz akcję, którą chcesz. 

   Na przykład wybierz tę akcję: **usługi Event Hubs — wysyłanie zdarzeń**

   ![Wybierz opcję "Event Hubs — wysyłanie zdarzeń"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

4. Jeśli zostanie wyświetlony monit o szczegóły połączenia [utworzyć połączenie usługi Event Hubs teraz](#create-connection). Lub, jeśli połączenie już istnieje, podaj informacje niezbędne do działania.

   | Właściwość | Wymagane | Opis | 
   |----------|----------|-------------|
   | Nazwa centrum zdarzeń | Yes | Wybierz Centrum zdarzeń, które chcesz wysłać zdarzenia | 
   | Zawartość zdarzeń | Nie | Zawartość dla zdarzenia, który ma zostać wysłany | 
   | Właściwości | Nie | Właściwości aplikacji i wartości do wysyłania | 
   |||| 

   Na przykład: 

   ![Wybierz nazwę Centrum zdarzeń i dostarczanie zawartości zdarzeń](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

5. Gdy wszystko będzie gotowe, na pasku narzędzi projektanta, wybierz pozycję **zapisać**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Połącz do Centrum zdarzeń

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Gdy zostanie wyświetlony monit o informacje dotyczące połączenia, podaj te szczegóły:

   | Właściwość | Wymagane | Wartość | Opis | 
   |----------|----------|-------|-------------|
   | Nazwa połączenia | Yes | <*Nazwa połączenia*> | Nazwę można utworzyć połączenia |
   | Przestrzeń nazw usługi Event Hubs | Yes | <*przestrzeni nazw koncentratory zdarzenia*> | Wybierz obszar nazw usługi Event Hubs, którego chcesz użyć. | 
   |||||  

   Na przykład:

   ![Utwórz połączenie z Centrum zdarzeń](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Aby ręcznie wprowadzić parametry połączenia, wybierz **ręcznie wprowadzić informacje o połączeniu**. 
   Dowiedz się [jak znaleźć parametrów połączenia](#permissions-connection-string).

2. Wybierz zasady centra zdarzeń, które mają być używane, jeśli jeszcze nie został wybrany. Wybierz pozycję **Utwórz**.

   ![Utwórz połączenie z Centrum zdarzeń, część 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Po utworzeniu połączenia kontynuować [wyzwolenia dodać usługi Event Hubs](#add-trigger) lub [akcji Dodaj usługi Event Hubs](#add-action).

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku programu Swagger łącznika, zobacz [strony odwołanie łącznika](/connectors/eventhubs/). 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączniki Logic Apps](../connectors/apis-list.md)