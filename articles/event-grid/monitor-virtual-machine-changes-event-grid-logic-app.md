---
title: Monitorowanie zmian na maszynach wirtualnych — usługa Azure Event Grid & aplikacje logiki
description: Sprawdzanie zmian w maszynach wirtualnych za pomocą usługi Azure Event Grid i aplikacji logiki
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 10/11/2019
ms.openlocfilehash: f5aac7fe63b2afc997ff69e5d976c755440c1bea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75982567"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Samouczek: Monitorowanie zmian na maszynie wirtualnej przy użyciu usługi Azure Event Grid i aplikacji logiki

Aby monitorować określone zdarzenia i reagować na nie, które mają miejsce w zasobach platformy Azure lub zasobach innych firm, można zautomatyzować i uruchamiać zadania jako przepływ pracy, tworząc [aplikację logiki,](../logic-apps/logic-apps-overview.md) która używa minimalnego kodu. Te zasoby mogą publikować zdarzenia w [siatce zdarzeń platformy Azure](../event-grid/overview.md). Z kolei usługa Event Grid wypycha te zdarzenia do subskrybentów, którzy mają kolejki, elementy webhook lub [centra zdarzeń](../event-hubs/event-hubs-what-is-event-hubs.md) jako punkty końcowe. Jako subskrybent aplikacji logiki można czekać na te zdarzenia z siatki zdarzeń przed uruchomieniem zautomatyzowanych przepływów pracy do wykonywania zadań.

Na przykład poniżej przedstawiono niektóre zdarzenia, które wydawcy mogą wysyłać do subskrybentów za pośrednictwem usługi Azure Event Grid:

* Utworzenie, odczyt, aktualizacja lub usunięcie zasobu. Można na przykład monitorować zmiany, które mogą spowodować naliczenie opłat w ramach subskrypcji platformy Azure i wpłynąć na rachunek.

* Dodanie lub usunięcie osoby z subskrypcji platformy Azure.

* Aplikacja wykonuje określoną akcję.

* Pojawienie się nowego komunikatu w kolejce.

Ten samouczek tworzy aplikację logiki, która monitoruje zmiany na maszynie wirtualnej i wysyła wiadomości e-mail o tych zmianach. Podczas tworzenia aplikacji logiki z subskrypcją zdarzeń dla zasobu platformy Azure zdarzenia przepływają z tego zasobu za pośrednictwem usługi Event Grid do aplikacji logiki. Samouczek przeprowadzi Cię przez tworzenie tej aplikacji logiki:

![Omówienie — monitorowanie maszyny wirtualnej za pomocą usługi Event Grid i aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji logiki, która monitoruje zdarzenia z usługi Event Grid.
> * Dodawanie warunku, który konkretnie wyszukuje zmiany maszyny wirtualnej.
> * Wysyłanie wiadomości e-mail, gdy maszyna wirtualna ulegnie zmianie.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Konto e-mail od dostawcy poczty e-mail obsługiwanego przez aplikacje logiki do wysyłania powiadomień, takich jak Outlook usługi Office 365, Outlook.com lub Gmail. W przypadku innych dostawców [przejrzyj tę listę łączników](/connectors/).

  W tym samouczku użyto konta programu Outlook usługi Office 365. Jeśli korzystasz z innego konta e-mail, ogólne kroki pozostają takie same, ale Twój interfejs użytkownika może wyglądać trochę inaczej.

* [Maszyna wirtualna,](https://azure.microsoft.com/services/virtual-machines) która jest sama we własnej grupie zasobów platformy Azure. Jeśli jeszcze tego nie zrobiono, utwórz maszynę wirtualną za pomocą [samouczka Tworzenie maszyny wirtualnej](../virtual-machines/windows/quick-create-portal.md). Aby maszyna wirtualna publikowała zdarzenia, [nie trzeba nic robić](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Tworzenie pustej aplikacji logiki

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. Z głównego menu platformy Azure wybierz pozycję **Utwórz aplikację** > **logiki****integracji** > zasobów .

   ![Tworzenie aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. W obszarze **Aplikacja logiki**podaj informacje o zasobie aplikacji logiki. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

   ![Podawanie szczegółów aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa** | Tak | <*nazwa aplikacji logiki*> | Podaj unikatową nazwę aplikacji logiki. |
   | **Subskrypcja** | Tak | <*Nazwa subskrypcji platformy Azure*> | Wybierz tę samą subskrypcję platformy Azure dla wszystkich usług w tym samouczku. |
   | **Grupa zasobów** | Tak | <*Grupa zasobów platformy Azure*> | Nazwa grupy zasobów platformy Azure dla aplikacji logiki, którą można wybrać dla wszystkich usług w tym samouczku. |
   | **Lokalizacja** | Tak | <*Region platformy Azure*> | Wybierz ten sam region dla wszystkich usług w tym samouczku. |
   |||

1. Po platformie Azure wdraża aplikację logiki, Logic Apps Designer pokazuje stronę z wprowadzeniem wideo i często używane wyzwalacze. Przewiń poza wideo i wyzwalacze.

1. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Wybierz szablon aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Projektant aplikacji logiki pokazuje teraz [*wyzwalacze,*](../logic-apps/logic-apps-overview.md#logic-app-concepts) których można użyć do uruchomienia aplikacji logiki. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Za każdym razem, gdy wyzwalacz jest uruchamiany, usługa Azure Logic Apps tworzy wystąpienie przepływu pracy, które uruchamia aplikację logiki.

## <a name="add-an-event-grid-trigger"></a>Dodawanie wyzwalacza siatki zdarzeń

Teraz dodaj wyzwalacz siatki zdarzeń, który służy do monitorowania grupy zasobów dla maszyny wirtualnej.

1. Na projektancie w polu wyszukiwania `event grid` wprowadź jako filtr. Z listy wyzwalaczy wybierz wyzwalacz **zdarzenia Po wystąpieniu zasobu.**

   ![Wybierz ten wyzwalacz: "W przypadku zdarzenia zasobu"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Po wyświetleniu monitu zaloguj się do usługi Azure Event Grid przy użyciu poświadczeń konta platformy Azure. Na liście **Dzierżawy,** która pokazuje dzierżawę usługi Azure Active Directory skojarzoną z subskrypcją platformy Azure, sprawdź, czy pojawia się poprawna dzierżawa, na przykład:

   ![Logowanie przy użyciu poświadczeń platformy Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Jeśli logujesz się za pomocą osobistego konta Microsoft, takiego jak @outlook.com lub @hotmail.com, wyzwalacz usługi Event Grid może nie być wyświetlany poprawnie. Aby obejść ten problem, wybierz pozycję [Połącz z podmiotem obsługującym](../active-directory/develop/howto-create-service-principal-portal.md)lub uwierzytelnij jako członek usługi Azure Active Directory skojarzonej z subskrypcją platformy Azure, na przykład *nazwą*@emailoutlook.onmicrosoft.comużytkownika.

1. Teraz subskrybuj aplikację logiki do zdarzeń od wydawcy. Podaj szczegółowe informacje o subskrypcji zdarzeń, zgodnie z opisem w poniższej tabeli, na przykład:

   ![Podaj szczegóły subskrypcji zdarzeń](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Właściwość | Wymagany | Wartość | Opis |
   | -------- | -------- | ----- | ----------- |
   | **Subskrypcja** | Tak | <*event-publisher-Azure-subscription-name*> | Wybierz nazwę subskrypcji platformy Azure skojarzonej z *wydawcą zdarzeń.* W tym samouczku wybierz nazwę subskrypcji platformy Azure dla maszyny wirtualnej. |
   | **Typ zasobu** | Tak | <*event-publisher-Azure-resource-type*> | Wybierz typ zasobu platformy Azure dla wydawcy zdarzeń. Aby uzyskać więcej informacji na temat typów zasobów platformy [Azure,](../azure-resource-manager/management/resource-providers-and-types.md)zobacz dostawców zasobów platformy Azure i typów . W tym samouczku `Microsoft.Resources.ResourceGroups` wybierz wartość do monitorowania grup zasobów platformy Azure. |
   | **Nazwa zasobu** |  Tak | <*event-publisher-Azure-resource-name*> | Wybierz nazwę zasobu platformy Azure dla wydawcy zdarzenia. Ta lista różni się w zależności od wybranego typu zasobu. W tym samouczku wybierz nazwę grupy zasobów platformy Azure, która zawiera maszynę wirtualną. |
   | **Element typu zdarzenia** |  Nie | <*typy zdarzeń*> | Wybierz jeden lub więcej określonych typów zdarzeń do filtrowania i wysyłania do siatki zdarzeń. Na przykład można opcjonalnie dodać te typy zdarzeń, aby wykryć, kiedy zasoby są zmieniane lub usuwane: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Więcej informacji można znaleźć w następujących tematach: <p><p>- [Schemat zdarzeń usługi Azure Event Grid dla grup zasobów](../event-grid/event-schema-resource-groups.md) <br>- [Opis filtrowania zdarzeń](../event-grid/event-filtering.md) <br>- [Filtrowanie zdarzeń dla siatki zdarzeń](../event-grid/how-to-filter-events.md) |
   | Aby dodać właściwości opcjonalne, wybierz pozycję **Dodaj nowy parametr**, a następnie wybierz żądane właściwości. | Nie | {patrz opisy} | * **Filtr prefiksu:** W tym samouczku pozostaw tę właściwość pustą. Zachowanie domyślne dopasowuje wszystkie wartości. Można jednak określić ciąg prefiksu jako filtr, na przykład ścieżkę i parametr dla konkretnego zasobu. <p>* **Filtr sufiksu:** W tym samouczku pozostaw tę właściwość pustą. Zachowanie domyślne dopasowuje wszystkie wartości. Można jednak określić ciąg sufiksu jako filtr, na przykład rozszerzenie nazwy pliku, aby uwzględniać tylko określone typy plików. <p>* **Nazwa subskrypcji:** W tym samouczku można podać unikatową nazwę subskrypcji zdarzeń. |
   |||

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. Aby zwinąć i ukryć szczegóły akcji w aplikacji logiki, wybierz pasek tytułu akcji.

   ![Zapisywanie aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Podczas zapisywania aplikacji logiki z wyzwalaczem usługi Event Grid platforma Azure automatycznie tworzy dla tej aplikacji logiki subskrypcję zdarzeń wybranego zasobu. Dlatego gdy zasób publikuje zdarzenie w usłudze Event Grid, usługa ta automatycznie wypycha je do aplikacji logiki. Zdarzenie to wyzwala aplikację logiki, a następnie tworzy i uruchamia wystąpienie przepływu pracy, który zdefiniujesz w następnych krokach.

Aplikacja logiki jest teraz aktywna i nasłuchuje zdarzeń z usługi Event Grid, ale nie robi nic, dopóki nie dodasz akcji do przepływu pracy.

## <a name="add-a-condition"></a>Dodawanie warunku

Jeśli chcesz, aby aplikacja logiki do uruchomienia tylko wtedy, gdy występuje `Microsoft.Compute/virtualMachines/write` określone zdarzenie lub operacja, dodaj warunek, który sprawdza dla operacji. Jeśli ten warunek zostanie spełniony, aplikacja logiki wyśle Ci wiadomość e-mail zawierającą szczegóły zaktualizowanej maszyny wirtualnej.

1. W logic app designer, w obszarze wyzwalacza siatki zdarzeń, wybierz pozycję **Nowy krok**.

   ![Wybierz "Nowy krok"](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. W obszarze **Wybierz akcję**w polu `condition` wyszukiwania wprowadź jako filtr. Z listy akcje wybierz akcję **Warunek.**

   ![Dodawanie warunku](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Projektant aplikacji logiki dodaje pusty warunek do przepływu pracy, w tym ścieżki akcji do wykonania na podstawie tego, czy warunek jest spełniony (wartość true), czy nie (wartość false).

   ![Pojawi się pusty warunek](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Zmień nazwę tytułu `If a virtual machine in your resource group has changed`warunku na . Na pasku tytułu warunku wybierz przycisk elipsy (**...**) i wybierz **pozycję Zmień nazwę**.

   ![Zmień nazwę warunku](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Utwórz warunek, który `body` sprawdza `data` zdarzenie `operationName` dla obiektu, `Microsoft.Compute/virtualMachines/write` w którym właściwość jest równa operacji. Dowiedz się więcej o [schemacie zdarzeń usługi Event Grid](../event-grid/event-schema.md).

   1. W pierwszym wierszu w obszarze **I** kliknij wewnątrz pola po lewej stronie. Na wyświetlona dynamiczna lista zawartości wybierz pozycję **Wyrażenie**.

      ![Wybierz "Wyrażenie", aby otworzyć edytor wyrażeń](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. W edytorze wyrażeń wprowadź to wyrażenie, które zwraca nazwę operacji z wyzwalacza, i wybierz **przycisk OK:**

      `triggerBody()?['data']['operationName']`

      Przykład:

      ![Wprowadź wyrażenie, aby wyodrębnić nazwę operacji](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. W środkowym polu zachowaj operator **jest równe**.

   1. W prawym polu wprowadź tę wartość, czyli określoną operację, którą chcesz monitorować:

      `Microsoft.Compute/virtualMachines/write`

   Twój gotowy stan wygląda teraz następująco:

   ![Ukończony warunek, który porównuje operację](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Jeśli przełączysz się z widoku projektu do widoku kodu i z powrotem do widoku projektu, wyrażenie określone w warunku zostanie rozpoznane dla tokenu **data.operationName:**

   ![Rozwiązane tokeny w stanie](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Zapisz aplikację logiki.

## <a name="send-email-notifications"></a>Wysyłanie powiadomień w wiadomościach e-mail

Teraz dodaj [*akcję,*](../logic-apps/logic-apps-overview.md#logic-app-concepts) dzięki czemu możesz otrzymać wiadomość e-mail, gdy określony warunek jest spełniony.

1. W polu Warunek **Jeśli prawda** wybierz pozycję Dodaj **akcję**.

   ![Dodawanie akcji do wykonania w przypadku wartości true warunku](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. W obszarze **Wybierz akcję**w polu `send an email` wyszukiwania wprowadź jako filtr. W oparciu o Twojego dostawcę poczty e-mail znajdź i wybierz zgodny łącznik. Następnie wybierz akcję „Wyślij wiadomość e-mail” dla Twojego łącznika. Przykład:

   * Dla konta służbowego platformy Azure wybierz łącznik usługi Office 365 Outlook.

   * Dla osobistych kont Microsoft wybierz łącznik usługi Outlook.com.

   * Dla kont usługi Gmail wybierz łącznik usługi Gmail.

   Ten samouczek jest kontynuowany z łącznikiem programu Outlook usługi Office 365. Jeśli używasz innego dostawcy, kroki pozostają takie same, ale interfejs użytkownika może wyglądać nieco inaczej.

   ![Wybieranie akcji „Wyślij wiadomość e-mail”](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Jeśli nie masz jeszcze połączenia z dostawcą poczty e-mail, zaloguj się na swoje konto poczty e-mail po wyświetleniu prośby o uwierzytelnienie.

1. Zmień nazwę akcji wyślij wiadomość e-mail na ten tytuł:`Send email when virtual machine updated`

1. Podaj informacje o wiadomości e-mail określone w poniższej tabeli:

   ![Podaj informacje o akcji e-mail](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Aby wybrać dane wyjściowe z poprzednich kroków przepływu pracy, kliknij wewnątrz pola edycji, aby wyświetlić dynamiczną listę zawartości, lub wybierz pozycję **Dodaj zawartość dynamiczną**. Aby uzyskać więcej wyników, wybierz pozycję **Zobacz więcej** dla każdej sekcji na liście. Aby zamknąć dynamiczną listę zawartości, wybierz ponownie pozycję **Dodaj zawartość dynamiczną.**

   | Właściwość | Wymagany | Wartość | Opis |
   | -------- | -------- | ----- | ----------- |
   | **Do** | Tak | <*domena adresatów\@*> | Wprowadź adres e-mail adresata. Do celów testowych możesz użyć własnego adresu e-mail. |
   | **Temat** | Tak | `Resource updated:` **Temat** | Wprowadź zawartość w polu tematu wiadomości e-mail. W tym samouczku wprowadź określony tekst i wybierz pole **Temat** zdarzenia. Tutaj temat wiadomości e-mail zawiera nazwę zaktualizowanego zasobu (maszyny wirtualnej). |
   | **Treść** | Tak | `Resource:`**Temat** <p>`Event type:`**Typ zdarzenia**<p>`Event ID:` **ID**<p>`Time:`**Czas zdarzenia** | Wprowadź zawartość w polu treści wiadomości e-mail. W tym samouczku wprowadź określony tekst i wybierz pola **Temat**zdarzenia , **Typ zdarzenia**, **Identyfikator**i **Czas zdarzenia,** aby wiadomość e-mail była zawiera zasób, który został odpalony, typ zdarzenia, sygnaturę czasową zdarzenia i identyfikator zdarzenia dla aktualizacji. W tym samouczku zasób jest grupą zasobów platformy Azure wybranej w wyzwalaczu. <p>Aby dodać puste wiersze w zawartości, naciśnij klawisze Shift + Enter. |
   ||||

   > [!NOTE]
   > Jeśli wybierzesz pole reprezentujące tablicę, projektant automatycznie dodaje pętlę **Dla każdego** wokół akcji, która odwołuje się do tablicy. W ten sposób Twoja aplikacja logiki wykonuje tę akcję dla każdego elementu tablicy.

   Teraz Twoja akcja wiadomości e-mail może wyglądać tak jak w tym przykładzie:

   ![Wybieranie danych wyjściowych do uwzględnienia w wiadomości e-mail](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Gotowa aplikacja logiki może wyglądać jak w tym przykładzie:

   ![Ukończona aplikacja logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Zapisz aplikację logiki. Aby zwinąć i ukryć szczegóły każdej akcji w aplikacji logiki, wybierz pasek tytułu akcji.

   Aplikacja logiki jest teraz aktywna, ale czeka na zmiany maszyny wirtualnej, zanim wykona jakiekolwiek akcje. Aby teraz przetestować Twoją aplikację logiki, przejdź do następnej sekcji.

## <a name="test-your-logic-app-workflow"></a>Testowanie przepływu pracy aplikacji logiki

1. Aby sprawdzić, czy aplikacja logiki otrzymuje określone zdarzenia, zaktualizuj maszynę wirtualną.

   Możesz na przykład zmienić rozmiar maszyny wirtualnej w witrynie Azure Portal lub [zmienić rozmiar maszyny wirtualnej za pomocą programu Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Po kilku chwilach powinna nadejść wiadomość e-mail. Przykład:

   ![Wiadomość e-mail o aktualizacji maszyny wirtualnej](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Aby przejrzeć przebiegi i historię wyzwalaczy dla aplikacji logiki, w menu aplikacji logiki wybierz pozycję **Przegląd**. Aby wyświetlić więcej szczegółów dotyczących przebiegu, wybierz wiersz dla tego uruchomienia.

   ![Historia przebiegów aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Aby wyświetlić dane wejściowe i wyjściowe dla każdego kroku, rozwiń krok, który chcesz przejrzeć. Te informacje ułatwiają diagnozowanie i debugowanie problemów w aplikacji logiki.

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

* Aby trwale usunąć aplikację logiki, w menu aplikacji logiki wybierz pozycję **Przegląd**. Na pasku narzędzi wybierz pozycję **Usuń**. Potwierdź, że chcesz usunąć aplikację logiki, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie i kierowanie zdarzeń niestandardowych za pomocą usługi Event Grid](../event-grid/custom-event-quickstart.md)
