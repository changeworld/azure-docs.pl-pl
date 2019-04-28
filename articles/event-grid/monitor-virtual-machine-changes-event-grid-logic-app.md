---
title: Monitorowanie zmian maszyn wirtualnych — usługi Azure Event Grid i Logic Apps | Microsoft Docs
description: Sprawdzanie zmian konfiguracji maszyn wirtualnych za pomocą usług Azure Event Grid i Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 01/12/2019
ms.openlocfilehash: e735c9773971a4c594c32e9ae29eeb295c32810c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824817"
---
# <a name="tutorial-monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Samouczek: Monitorowanie zmian maszyn wirtualnych za pomocą usług Azure Event Grid i Logic Apps

W przypadku wystąpienia określonych zdarzeń w zasobach platformy Azure lub zasobach innych firm możliwe jest uruchamianie zautomatyzowanego [przepływu pracy aplikacji logiki](../logic-apps/logic-apps-overview.md). Zasoby te mogą publikować te zdarzenia w usłudze [Azure Event Grid](../event-grid/overview.md). Z kolei usługa Event Grid wypycha te zdarzenia do subskrybentów, którzy mają kolejki, elementy webhook lub [centra zdarzeń](../event-hubs/event-hubs-what-is-event-hubs.md) jako punkty końcowe. Jako subskrybent aplikacja logiki może czekać na te zdarzenia z usługi Event Grid przed uruchomieniem automatycznych przepływów pracy w celu wykonywania zadań — bez konieczności pisania kodu.

Na przykład poniżej przedstawiono niektóre zdarzenia, które wydawcy mogą wysyłać do subskrybentów za pośrednictwem usługi Azure Event Grid:

* Utworzenie, odczyt, aktualizacja lub usunięcie zasobu. Można na przykład monitorować zmiany, które mogą spowodować naliczenie opłat w ramach subskrypcji platformy Azure i wpłynąć na rachunek. 
* Dodanie lub usunięcie osoby z subskrypcji platformy Azure.
* Aplikacja wykonuje określoną akcję.
* Pojawienie się nowego komunikatu w kolejce.

W tym samouczku tworzona jest aplikacja logiki, która monitoruje zmiany maszyny wirtualnej, a następnie wysyła wiadomości e-mail dotyczące tych zmian. Podczas tworzenia aplikacji logiki z subskrypcją zdarzeń dla zasobu platformy Azure zdarzenia przepływają z tego zasobu za pośrednictwem usługi Event Grid do aplikacji logiki. Samouczek przeprowadzi Cię przez tworzenie tej aplikacji logiki:

![Omówienie — monitorowanie maszyny wirtualnej za pomocą usługi Event Grid i aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji logiki, która monitoruje zdarzenia z usługi Event Grid.
> * Dodawanie warunku, który konkretnie wyszukuje zmiany maszyny wirtualnej.
> * Wysyłanie wiadomości e-mail, gdy maszyna wirtualna ulegnie zmianie.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto e-mail od dowolnego [dostawcy poczty e-mail obsługiwanego przez usługę Azure Logic Apps](../connectors/apis-list.md), na przykład Office 365 Outlook, Outlook.com lub Gmail. W tym samouczku jest używana usługa Office 365 Outlook.

* [Maszyna wirtualna](https://azure.microsoft.com/services/virtual-machines). Jeśli jeszcze tego nie zrobiono, utwórz maszynę wirtualną za pomocą [samouczka tworzenia maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/). Aby maszyna wirtualna publikowała zdarzenia, [nie trzeba nic robić](../event-grid/overview.md).

## <a name="create-a-logic-app-that-monitors-events-from-an-event-grid"></a>Tworzenie aplikacji logiki, która monitoruje zdarzenia z usługi Event Grid

Najpierw należy utworzyć aplikację logiki i dodać wyzwalacz usługi Event Grid, który monitoruje grupę zasobów dla maszyny wirtualnej. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. W lewym górnym rogu głównego menu platformy Azure wybierz pozycję **Utwórz zasób** > **Integracja dla przedsiębiorstw** > **Aplikacja logiki**.

   ![Tworzenie aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

3. Utwórz własną aplikację logiki z ustawieniami określonymi w poniższej tabeli:

   ![Podawanie szczegółów aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Ustawienie | Sugerowana wartość | Opis | 
   | ------- | --------------- | ----------- | 
   | **Nazwa** | *{nazwa Twojej aplikacji logiki}* | Podaj unikatową nazwę aplikacji logiki. | 
   | **Subskrypcja** | *{Twoja subskrypcja platformy Azure}* | Wybierz tę samą subskrypcję platformy Azure dla wszystkich usług w tym samouczku. | 
   | **Grupa zasobów** | *{Twoja grupa zasobów platformy Azure}* | Wybierz tę samą grupę zasobów platformy Azure dla wszystkich usług w tym samouczku. | 
   | **Lokalizacja** | *{Twój region świadczenia usługi Azure}* | Wybierz ten sam region dla wszystkich usług w tym samouczku. | 
   | | | 

4. Gdy wszystko będzie gotowe, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie pozycję **Utwórz**.

   Teraz został utworzony zasób platformy Azure dla Twojej aplikacji logiki. 
   Po wdrożeniu Twojej aplikacji logiki na platformie Azure projektant aplikacji usługi Logic Apps pokaże szablony dla typowych wzorców, więc możesz szybciej rozpocząć pracę.

   > [!NOTE] 
   > Jeśli wybierzesz pozycję **Przypnij do pulpitu nawigacyjnego**, Twoja aplikacja logiki automatycznie zostanie otwarta w projektancie aplikacji usługi Logic Apps. W przeciwnym razie możesz ręcznie znaleźć i otworzyć swoją aplikację logiki.

5. Teraz wybierz szablon aplikacji logiki. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**, aby rozpocząć tworzenie swojej aplikacji logiki od podstaw.

   ![Wybieranie szablonu aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Projektant aplikacji usługi Logic Apps wyświetla teraz [*łączniki*](../connectors/apis-list.md) i [*wyzwalacze*](../logic-apps/logic-apps-overview.md#logic-app-concepts), których można użyć do uruchamiania aplikacji logiki, a także akcje, które można dodać po wyzwalaczu w celu wykonywania zadań. Wyzwalacz to zdarzenie, które tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji logiki. 
   Twoja aplikacja logiki wymaga wyzwalacza jako pierwszego elementu.

6. W polu wyszukiwania wprowadź wartość „Event Grid” jako filtr. Wybierz ten wyzwalacz: **Azure Event Grid — po wystąpieniu zdarzenia zasobu**

   ![Wybierz ten wyzwalacz: „Azure Event Grid — po wystąpieniu zdarzenia zasobu”](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

7. Po wyświetleniu monitu zaloguj się do usługi Azure Event Grid przy użyciu poświadczeń platformy Azure.

   ![Logowanie przy użyciu poświadczeń platformy Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Jeśli logujesz się za pomocą osobistego konta Microsoft, takiego jak @outlook.com lub @hotmail.com, wyzwalacz usługi Event Grid może nie być wyświetlany poprawnie. Aby obejść ten problem, wybierz pozycję [Nawiąż połączenie z jednostką usługi](../active-directory/develop/howto-create-service-principal-portal.md) lub uwierzytelnij się jako członek usługi Azure Active Directory skojarzonej z Twoją subskrypcją platformy Azure, na przykład *nazwa użytkownika*@emailoutlook.onmicrosoft.com.

8. Teraz subskrybuj zdarzenia wydawcy przy użyciu aplikacji logiki. Podaj szczegóły subskrypcji zdarzeń określone w poniższej tabeli:

   ![Podaj szczegóły subskrypcji zdarzeń](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Ustawienie | Sugerowana wartość | Opis | 
   | ------- | --------------- | ----------- | 
   | **Subskrypcja** | *{subskrypcja platformy Azure maszyny wirtualnej}* | Wybierz subskrypcję platformy Azure wydawcy zdarzeń. W tym samouczku wybierz subskrypcję platformy Azure dla maszyny wirtualnej. | 
   | **Typ zasobu** | Microsoft.Resources.resourceGroups | Wybierz typ zasobu wydawcy zdarzeń. W tym samouczku wybierz określoną wartość, aby aplikacja logiki monitorowała tylko grupy zasobów. | 
   | **Nazwa zasobu** | *{nazwa grupy zasobów maszyny wirtualnej}* | Wybierz nazwę zasobu wydawcy. W tym samouczku wybierz nazwę grupy zasobów dla maszyny wirtualnej. | 
   | Aby wyświetlić ustawienia opcjonalne, wybierz pozycję **Pokaż opcje zaawansowane**. | *{patrz opisy}* | * **Filtr prefiksu**: W tym samouczku pozostaw to ustawienie puste. Zachowanie domyślne dopasowuje wszystkie wartości. Można jednak określić ciąg prefiksu jako filtr, na przykład ścieżkę i parametr dla konkretnego zasobu. <p>* **Filtr sufiksu**: W tym samouczku pozostaw to ustawienie puste. Zachowanie domyślne dopasowuje wszystkie wartości. Można jednak określić ciąg sufiksu jako filtr, na przykład rozszerzenie nazwy pliku, aby uwzględniać tylko określone typy plików.<p>* **Nazwa subskrypcji**: Podaj unikatową nazwę dla Twojej subskrypcji zdarzeń. |
   | | | 

   Gdy skończysz, wyzwalacz usługi Event Grid może wyglądać jak w tym przykładzie:
   
   ![Szczegóły przykładowego wyzwalacza usługi Event Grid](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

9. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. Aby zwinąć i ukryć szczegóły akcji w aplikacji logiki, wybierz pasek tytułu tej akcji.

   ![Zapisywanie aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Podczas zapisywania aplikacji logiki z wyzwalaczem usługi Event Grid platforma Azure automatycznie tworzy dla tej aplikacji logiki subskrypcję zdarzeń wybranego zasobu. Dlatego gdy zasób publikuje zdarzenie w usłudze Event Grid, usługa ta automatycznie wypycha je do aplikacji logiki. Zdarzenie to wyzwala aplikację logiki, a następnie tworzy i uruchamia wystąpienie przepływu pracy, który zdefiniujesz w następnych krokach.

Aplikacja logiki jest teraz aktywna i nasłuchuje zdarzeń z usługi Event Grid, ale nie robi nic, dopóki nie dodasz akcji do przepływu pracy. 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>Dodawanie warunku, który wyszukuje zmiany maszyny wirtualnej

Aby uruchamiać przepływ pracy aplikacji logiki tylko w przypadku wystąpienia określonego zdarzenia, dodaj warunek, który sprawdza operacje „zapis” maszyny wirtualnej. Jeśli ten warunek zostanie spełniony, aplikacja logiki wyśle Ci wiadomość e-mail zawierającą szczegóły zaktualizowanej maszyny wirtualnej.

1. W Projektancie aplikacji logiki, w obszarze wyzwalacza usługi Event Grid, wybierz pozycję **Nowy krok** > **Dodaj warunek**.

   ![Dodawanie warunku do aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-add-condition-step.png)

   Projektant aplikacji logiki dodaje pusty warunek do przepływu pracy, w tym ścieżki akcji do wykonania na podstawie tego, czy warunek jest spełniony (wartość true), czy nie (wartość false).

   ![Pusty warunek](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-empty-condition.png)

2. W polu **Warunek** wybierz pozycję **Edytuj w trybie zaawansowanym**.
Wprowadź wyrażenie:

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   Teraz warunek wygląda następująco:

   ![Pusty warunek](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-expression.png)

   To wyrażenie sprawdza zdarzenie `body` dla obiektu `data`, gdzie właściwość `operationName` jest operacją `Microsoft.Compute/virtualMachines/write`. 
   Dowiedz się więcej o [schemacie zdarzeń usługi Event Grid](../event-grid/event-schema.md).

3. Aby podać opis warunku, wybierz przycisk **wielokropka** (**...**) znajdujący się na kształcie warunku, a następnie wybierz pozycję **Zmień nazwę**.

   > [!NOTE] 
   > Późniejsze przykłady w tym samouczku również zawierają opisy kroków w przepływie pracy aplikacji logiki.

4. Teraz wybierz pozycję **Edytuj w trybie podstawowym**, aby wyrażenie było automatycznie rozpoznawane w sposób przedstawiony poniżej:

   ![Warunek aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

5. Zapisz aplikację logiki.

## <a name="send-email-when-your-virtual-machine-changes"></a>Wysyłanie wiadomości e-mail, gdy maszyna wirtualna ulegnie zmianie

Teraz dodaj [*akcję*](../logic-apps/logic-apps-overview.md#logic-app-concepts), aby otrzymać wiadomość e-mail, gdy określony warunek jest spełniony (ma wartość true).

1. W polu **W przypadku wartości true** warunku wybierz pozycję **Dodaj akcję**.

   ![Dodawanie akcji do wykonania w przypadku wartości true warunku](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. W polu wyszukiwania wprowadź wartość „e-mail” jako filtr. W oparciu o Twojego dostawcę poczty e-mail znajdź i wybierz zgodny łącznik. Następnie wybierz akcję „Wyślij wiadomość e-mail” dla Twojego łącznika. Na przykład: 

   * Dla konta służbowego platformy Azure wybierz łącznik usługi Office 365 Outlook. 
   * Dla osobistych kont Microsoft wybierz łącznik usługi Outlook.com. 
   * Dla kont usługi Gmail wybierz łącznik usługi Gmail. 

   Będziemy kontynuować z użyciem łącznika usługi Office 365 Outlook. 
   Jeśli używasz innego dostawcy, kroki pozostają takie same, ale Twój interfejs użytkownika może wyglądać inaczej. 

   ![Wybieranie akcji „Wyślij wiadomość e-mail”](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

3. Jeśli nie masz jeszcze połączenia z dostawcą poczty e-mail, zaloguj się na swoje konto poczty e-mail po wyświetleniu prośby o uwierzytelnienie.

4. Podaj szczegóły dla wiadomości e-mail określone w poniższej tabeli:

   ![Pusta akcja wiadomości e-mail](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Aby wybrać pola spośród tych dostępnych w przepływie pracy, kliknij wewnątrz pola edycji w celu otworzenia listy **Zawartość dynamiczna** lub wybierz pozycję **Dodaj zawartość dynamiczną**. Aby wyświetlić więcej pól, wybierz pozycję **Zobacz więcej** dla każdej sekcji na liście. Aby zamknąć listę **Zawartość dynamiczna**, wybierz pozycję **Dodaj zawartość dynamiczną**.

   | Ustawienie | Sugerowana wartość | Opis | 
   | ------- | --------------- | ----------- | 
   | **Do** | *{adres e-mail adresata}* |Wprowadź adres e-mail adresata. Do celów testowych możesz użyć własnego adresu e-mail. | 
   | **Temat** | Zaktualizowano zasób: **Temat**| Wprowadź zawartość w polu tematu wiadomości e-mail. W tym samouczku wprowadź sugerowany tekst i wybierz pole **Temat** zdarzenia. Tutaj temat wiadomości e-mail zawiera nazwę zaktualizowanego zasobu (maszyny wirtualnej). | 
   | **Treść** | Grupa zasobów: **Temat** <p>Typ zdarzenia: **Typ zdarzenia**<p>Identyfikator zdarzenia: **Identyfikator**<p>Czas: **Czas zdarzenia** | Wprowadź zawartość w polu treści wiadomości e-mail. W tym samouczku wprowadź sugerowany tekst i wybierz pola **Temat**, **Typ zdarzenia**, **Identyfikator** i **Czas zdarzenia** dla zdarzenia, tak aby Twoja wiadomość e-mail zawierała nazwę grupy zasobów, typ zdarzenia, sygnaturę czasową zdarzenia i identyfikator zdarzenia dla aktualizacji. <p>Aby dodać puste wiersze w zawartości, naciśnij klawisze Shift + Enter. | 
   | | | 

   > [!NOTE] 
   > Jeśli wybierzesz pole reprezentujące tablicę, projektant automatycznie dodaje pętlę **Dla każdego** wokół akcji, która odwołuje się do tablicy. W ten sposób Twoja aplikacja logiki wykonuje tę akcję dla każdego elementu tablicy.

   Teraz Twoja akcja wiadomości e-mail może wyglądać tak jak w tym przykładzie:

   ![Wybieranie danych wyjściowych do uwzględnienia w wiadomości e-mail](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Gotowa aplikacja logiki może wyglądać jak w tym przykładzie:

   ![Ukończona aplikacja logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. Zapisz aplikację logiki. Aby zwinąć i ukryć szczegóły akcji w aplikacji logiki, wybierz pasek tytułu tej akcji.

   ![Zapisywanie aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   Aplikacja logiki jest teraz aktywna, ale czeka na zmiany maszyny wirtualnej, zanim wykona jakiekolwiek akcje. 
   Aby teraz przetestować Twoją aplikację logiki, przejdź do następnej sekcji.

## <a name="test-your-logic-app-workflow"></a>Testowanie przepływu pracy aplikacji logiki

1. Aby sprawdzić, czy aplikacja logiki otrzymuje określone zdarzenia, zaktualizuj maszynę wirtualną. 

   Możesz na przykład zmienić rozmiar maszyny wirtualnej w witrynie Azure Portal lub [zmienić rozmiar maszyny wirtualnej za pomocą programu Azure PowerShell](../virtual-machines/windows/resize-vm.md). 

   Po kilku chwilach powinna nadejść wiadomość e-mail. Na przykład:

   ![Wiadomość e-mail o aktualizacji maszyny wirtualnej](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. Aby przejrzeć historię przebiegów i historię wyzwalania aplikacji logiki, w menu aplikacji logiki wybierz pozycję **Przegląd**. Aby wyświetlić więcej szczegółów na temat uruchomienia, wybierz wiersz dla tego uruchomienia.

   ![Historia przebiegów aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

3. Aby wyświetlić dane wejściowe i wyjściowe dla każdego kroku, rozwiń krok, który chcesz przejrzeć. Te informacje ułatwiają diagnozowanie i debugowanie problemów w aplikacji logiki.
 
   ![Szczegóły historii przebiegów aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Gratulujemy utworzenia i uruchomienia aplikacji logiki, która monitoruje zdarzenia zasobów za pomocą usługi Event Grid i wysyła Ci wiadomości e-mail w przypadku wystąpienia tych zdarzeń. Wiesz już, jak łatwe jest tworzenie przepływów pracy automatyzujących procesy oraz integrowanie systemów i usług w chmurze.

Za pomocą usług Event Grid i Logic Apps możesz także monitorować inne zmiany, na przykład:

* Maszyna wirtualna uzyskuje prawa kontroli dostępu opartej na rolach (RBAC).
* Wprowadzenie zmian w sieciowej grupie zabezpieczeń dla interfejsu sieciowego (karty sieciowej).
* Dodanie lub usunięcie dysków dla maszyny wirtualnej.
* Przypisanie publicznego adresu IP do karty sieciowej maszyny wirtualnej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Ten samouczek używa zasobów i wykonuje akcje, które mogą spowodować naliczenie opłat w ramach Twojej subskrypcji platformy Azure. Gdy ukończysz pracę z samouczkiem i testowanie, upewnij się, że zostały wyłączone lub usunięte wszelkie zasoby, dla których nie chcesz naliczenia opłat.

* Aby zatrzymać aplikację logiki bez usuwania swojej pracy, wyłącz ją. W menu aplikacji logiki wybierz pozycję **Przegląd**. Na pasku narzędzi wybierz pozycję **Wyłącz**.

  ![Wyłączanie aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Jeśli nie widzisz menu aplikacji logiki, spróbuj wrócić do pulpitu nawigacyjnego platformy Azure i ponownie otworzyć aplikację logiki.

* Aby trwale usunąć aplikację logiki, z menu aplikacji logiki wybierz pozycję **Przegląd**. Na pasku narzędzi wybierz pozycję **Usuń**. Potwierdź, że chcesz usunąć aplikację logiki, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie i kierowanie zdarzeń niestandardowych za pomocą usługi Event Grid](../event-grid/custom-event-quickstart.md)
