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
ms.date: 05/14/2019
ms.openlocfilehash: 791e38f3d15801166f07234648909e03d800f5c0
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604910"
---
# <a name="tutorial-monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Samouczek: Monitorowanie zmian maszyn wirtualnych za pomocą usług Azure Event Grid i Logic Apps

W przypadku wystąpienia określonych zdarzeń w zasobach platformy Azure lub zasobach innych firm możliwe jest uruchamianie zautomatyzowanego [przepływu pracy aplikacji logiki](../logic-apps/logic-apps-overview.md). Zasoby te mogą publikować te zdarzenia w usłudze [Azure Event Grid](../event-grid/overview.md). Z kolei usługa Event Grid wypycha te zdarzenia do subskrybentów, którzy mają kolejki, elementy webhook lub [centra zdarzeń](../event-hubs/event-hubs-what-is-event-hubs.md) jako punkty końcowe. Jako subskrybent aplikacja logiki może czekać na te zdarzenia z usługi Event Grid przed uruchomieniem automatycznych przepływów pracy w celu wykonywania zadań — bez konieczności pisania kodu.

Na przykład poniżej przedstawiono niektóre zdarzenia, które wydawcy mogą wysyłać do subskrybentów za pośrednictwem usługi Azure Event Grid:

* Utworzenie, odczyt, aktualizacja lub usunięcie zasobu. Można na przykład monitorować zmiany, które mogą spowodować naliczenie opłat w ramach subskrypcji platformy Azure i wpłynąć na rachunek. 
* Dodanie lub usunięcie osoby z subskrypcji platformy Azure.
* Aplikacja wykonuje określoną akcję.
* Pojawienie się nowego komunikatu w kolejce.

Ten samouczek tworzy aplikację logiki, która śledzi zmiany w maszynie wirtualnej i wysyła wiadomości e-mail o tych zmianach. Podczas tworzenia aplikacji logiki z subskrypcją zdarzeń dla zasobu platformy Azure zdarzenia przepływają z tego zasobu za pośrednictwem usługi Event Grid do aplikacji logiki. Samouczek przeprowadzi Cię przez tworzenie tej aplikacji logiki:

![Omówienie — monitorowanie maszyny wirtualnej za pomocą usługi Event Grid i aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji logiki, która monitoruje zdarzenia z usługi Event Grid.
> * Dodawanie warunku, który konkretnie wyszukuje zmiany maszyny wirtualnej.
> * Wysyłanie wiadomości e-mail, gdy maszyna wirtualna ulegnie zmianie.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Konto e-mail od dostawcy obsługiwanego przez usługę Logic Apps do wysyłania powiadomień, takich jak Office 365 Outlook, Outlook.com lub Gmail. W przypadku innych dostawców [przejrzyj tę listę łączników](/connectors/). 

  W tym samouczku korzysta z konta usługi Office 365 Outlook. Jeśli korzystasz z innego konta e-mail, ogólne kroki pozostają takie same, ale Twój interfejs użytkownika może wyglądać trochę inaczej.

* [Maszyna wirtualna](https://azure.microsoft.com/services/virtual-machines). Jeśli jeszcze tego nie zrobiono, utwórz maszynę wirtualną za pośrednictwem [samouczek maszyny Wirtualnej Utwórz](../virtual-machines/windows/quick-create-portal.md). Aby maszyna wirtualna publikowała zdarzenia, [nie trzeba nic robić](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Tworzenie pustej aplikacji logiki

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure. 

1. W głównym menu platformy Azure, wybierz **Utwórz zasób** > **integracji** > **aplikacji logiki**.

   ![Tworzenie aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. W obszarze **aplikacji logiki**, podaj informacje o aplikacji logiki. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

   ![Podawanie szczegółów aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Właściwość | Sugerowana wartość | Opis |
   | -------- | --------------- | ----------- |
   | **Nazwa** | <*logic-app-name*> | Podaj unikatową nazwę aplikacji logiki. |
   | **Subskrypcja** | <*Azure-subscription-name*> | Wybierz tę samą subskrypcję platformy Azure dla wszystkich usług w tym samouczku. |
   | **Grupa zasobów** | <*Azure-resource-group*> | Wybierz tę samą grupę zasobów platformy Azure dla wszystkich usług w tym samouczku. |
   | **Lokalizacja** | <*Azure-datacenter-region*> | Wybierz ten sam region dla wszystkich usług w tym samouczku. |
   |||

   Teraz został utworzony zasób platformy Azure dla Twojej aplikacji logiki. 

1. Gdy platforma Azure wdroży aplikację logiki, Projektant aplikacji usługi Logic Apps wyświetlający stronę z wprowadzającym wideo i najczęściej używanymi wyzwalaczami. Przewiń poza wideo i wyzwalacze. 

1. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Wybieranie szablonu aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Projektant aplikacji logiki wyświetla teraz [ *wyzwalaczy* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) służące do uruchomienia aplikacji logiki. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. 
   Każdym aktywowaniu wyzwalacza usługi Azure Logic Apps tworzy wystąpienie przepływu pracy, który uruchamia aplikację logiki.

## <a name="add-event-grid-trigger"></a>Dodawanie wyzwalacza usługi Event Grid 

Teraz Dodaj wyzwalacz usługi Event Grid, który monitoruje grupy zasobów dla maszyny wirtualnej. 

1. W projektancie w polu wyszukiwania wprowadź "event grid" jako filtr. Z listy wyzwalaczy wybierz następujący wyzwalacz: **Gdy wystąpi zdarzenie zasobu — usługi Azure Event Grid**

   ![Wybierz ten wyzwalacz: "W zdarzeniu zasobu"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Po wyświetleniu monitu zaloguj się do usługi Azure Event Grid przy użyciu poświadczeń konta platformy Azure. W **dzierżawy** listę, która pokazuje dzierżawy usługi Azure Active Directory, która jest skojarzony z subskrypcją platformy Azure, upewnij się, że pojawia się poprawne dzierżawy.

   ![Logowanie przy użyciu poświadczeń platformy Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Jeśli logujesz się za pomocą osobistego konta Microsoft, takiego jak @outlook.com lub @hotmail.com, wyzwalacz usługi Event Grid może nie być wyświetlany poprawnie. Aby obejść ten problem, wybierz pozycję [Nawiąż połączenie z jednostką usługi](../active-directory/develop/howto-create-service-principal-portal.md) lub uwierzytelnij się jako członek usługi Azure Active Directory skojarzonej z Twoją subskrypcją platformy Azure, na przykład *nazwa użytkownika*@emailoutlook.onmicrosoft.com.

1. Teraz subskrybuj zdarzenia wydawcy przy użyciu aplikacji logiki. Podaj szczegóły subskrypcji zdarzeń określone w poniższej tabeli:

   ![Podaj szczegóły subskrypcji zdarzeń](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Właściwość | Wymagany | Value | Opis |
   | -------- | -------- | ----- | ----------- |
   | **Subskrypcja** | Tak | <*event-publisher-Azure-subscription-name*> | Wybierz nazwę dla subskrypcji platformy Azure skojarzone z wydawcą zdarzeń. Na potrzeby tego samouczka wybierz nazwę subskrypcji platformy Azure dla maszyny wirtualnej. |
   | **Typ zasobu** | Tak | <*event-publisher-Azure-resource-type*> | Wybierz typ zasobu dla danego wydawcy zdarzeń. Na potrzeby tego samouczka wybierz tę wartość, dzięki czemu Twoja aplikacja logiki monitoruje tylko grupy zasobów: <p><p>**Microsoft.Resources.resourceGroups** |
   | **Nazwa zasobu** |  Tak | <*event-publisher-Azure-resource-name*> | Wybierz nazwę dla zasobów platformy Azure skojarzone z wydawcą zdarzeń. Na przykład ten zasób może być temat usługi Event Grid. Na potrzeby tego samouczka wybierz nazwę grupy zasobów platformy Azure skojarzoną z maszyną wirtualną. |
   | **Element typu zdarzenia** |  Nie | <*typy zdarzeń*> | Wybierz jeden lub więcej określonych typów zdarzeń, które chcesz monitorować. W tym samouczku należy pozostawić tą właściwość pustą. |
   | **Nazwa subskrypcji** | Nie | <*event-subscription-name*> | Podaj unikatową nazwę dla Twojej subskrypcji zdarzeń. |
   | Ustawienia opcjonalne, można wybrać **dodano nowy parametr**. | Nie | {zobacz opisy} | * **Filtr prefiksu**: W tym samouczku należy pozostawić tą właściwość pustą. Zachowanie domyślne dopasowuje wszystkie wartości. Można jednak określić ciąg prefiksu jako filtr, na przykład ścieżkę i parametr dla konkretnego zasobu. <p>* **Filtr sufiksu**: W tym samouczku należy pozostawić tą właściwość pustą. Zachowanie domyślne dopasowuje wszystkie wartości. Można jednak określić ciąg sufiksu jako filtr, na przykład rozszerzenie nazwy pliku, aby uwzględniać tylko określone typy plików. |
   |||

   Gdy wszystko będzie gotowe, wyzwalacza usługi Event Grid może wyglądać następująco:

   ![Szczegóły wyzwalacza usługi Event Grid przykład](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. Aby zwinąć i ukryć szczegóły akcji w aplikacji logiki, wybierz pasek tytułu tej akcji.

   ![Zapisywanie aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Podczas zapisywania aplikacji logiki z wyzwalaczem usługi Event Grid platforma Azure automatycznie tworzy dla tej aplikacji logiki subskrypcję zdarzeń wybranego zasobu. Dlatego gdy zasób publikuje zdarzenie w usłudze Event Grid, usługa ta automatycznie wypycha je do aplikacji logiki. Zdarzenie to wyzwala aplikację logiki, a następnie tworzy i uruchamia wystąpienie przepływu pracy, który zdefiniujesz w następnych krokach.

Aplikacja logiki jest teraz aktywna i nasłuchuje zdarzeń z usługi Event Grid, ale nie robi nic, dopóki nie dodasz akcji do przepływu pracy. 

## <a name="add-condition"></a>Dodaj warunek

Aby uruchamiać przepływ pracy aplikacji logiki tylko w przypadku wystąpienia określonego zdarzenia, dodaj warunek, który sprawdza operacje „zapis” maszyny wirtualnej. Jeśli ten warunek zostanie spełniony, aplikacja logiki wyśle Ci wiadomość e-mail zawierającą szczegóły zaktualizowanej maszyny wirtualnej.

1. W Projektancie aplikacji logiki, w obszarze wyzwalacza usługi event grid, wybierz opcję **nowy krok**.

   ![Wybierz pozycję "Nowy krok"](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. W polu wyszukiwania wprowadź "warunek" jako filtr. Z listy akcji wybierz następującą akcję: **Warunek**

   ![Dodaj warunek](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Projektant aplikacji logiki dodaje pusty warunek do przepływu pracy, w tym ścieżki akcji do wykonania na podstawie tego, czy warunek jest spełniony (wartość true), czy nie (wartość false).

   ![Pusty warunek](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Zmień nazwę tytuł warunku `If a virtual machine in your resource group has changed`. Na pasku tytułu warunku, kliknij przycisk wielokropka (**...** ) i wybierz **Zmień nazwę**.

   ![Zmienianie nazwy warunku](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Utwórz warunek sprawdzający zdarzenia `body` dla `data` obiektu gdzie `operationName` właściwości jest równa `Microsoft.Compute/virtualMachines/write` operacji. Dowiedz się więcej o [schemacie zdarzeń usługi Event Grid](../event-grid/event-schema.md).

   1. W pierwszym wierszu w obszarze **I** kliknij wewnątrz pola po lewej stronie. Na liście zawartości dynamicznej, która jest wyświetlana, wybierz opcję **wyrażenie**.

      ![Wybierz pozycję "Wyrażenie"](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. W edytorze wyrażeń wprowadź następujące wyrażenie, a następnie wybierz **OK**: 

      `triggerBody()?['data']['operationName']`

      Na przykład:

      ![Wybierz pozycję "Wyrażenie"](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. W środkowym polu zachowaj operator **jest równe**.

   1. W polu prawego wprowadź tę wartość:

      `Microsoft.Compute/virtualMachines/write`

   Zakończono warunek teraz będzie wyglądać następująco:

   ![Gotowy warunek](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

1. Zapisz aplikację logiki.

## <a name="send-email-notifications"></a>Wysyłanie powiadomień w wiadomościach e-mail

Teraz dodaj [*akcję*](../logic-apps/logic-apps-overview.md#logic-app-concepts), aby otrzymać wiadomość e-mail, gdy określony warunek jest spełniony (ma wartość true).

1. W polu **W przypadku wartości true** warunku wybierz pozycję **Dodaj akcję**.

   ![Dodawanie akcji do wykonania w przypadku wartości true warunku](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. W polu wyszukiwania wprowadź "Wyślij wiadomość e-mail" jako filtr. W oparciu o Twojego dostawcę poczty e-mail znajdź i wybierz zgodny łącznik. Następnie wybierz akcję „Wyślij wiadomość e-mail” dla Twojego łącznika. Na przykład: 

   * Dla konta służbowego platformy Azure wybierz łącznik usługi Office 365 Outlook. 

   * Dla osobistych kont Microsoft wybierz łącznik usługi Outlook.com. 

   * Dla kont usługi Gmail wybierz łącznik usługi Gmail. 

   W tym samouczku będzie kontynuowane przy użyciu łącznika usługi Office 365 Outlook. 
   Jeśli używasz innego dostawcy, kroki pozostają takie same, ale Twój interfejs użytkownika może wyglądać trochę inaczej. 

   ![Wybieranie akcji „Wyślij wiadomość e-mail”](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Jeśli nie masz jeszcze połączenia z dostawcą poczty e-mail, zaloguj się na swoje konto poczty e-mail po wyświetleniu prośby o uwierzytelnienie.

1. Zmień nazwę tytułu wiadomości e-mail Wyślij ten tytuł: `Send email when virtual machine updated`. 

1. Podaj szczegóły dla wiadomości e-mail określone w poniższej tabeli:

   ![Pusta akcja wiadomości e-mail](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Aby wybrać z wyniki z poprzednich kroków w przepływie pracy, kliknij w polu edycji, aby wyświetlić listę zawartości dynamicznej, lub wybierz **Dodaj zawartość dynamiczną**. Aby uzyskać więcej wyników wybierz **Zobacz więcej** dla każdej sekcji na liście. Aby zamknąć na liście zawartości dynamicznej, wybierz pozycję **Dodaj zawartość dynamiczną** ponownie.

   | Właściwość | Wymagany | Value | Opis |
   | -------- | -------- | ----- | ----------- |
   | **Do** | Tak | <*Odbiorca\@domeny*> | Wprowadź adres e-mail adresata. Do celów testowych możesz użyć własnego adresu e-mail. |
   | **Temat** | Tak | Zaktualizowano zasób: **Temat** | Wprowadź zawartość w polu tematu wiadomości e-mail. Na potrzeby tego samouczka wprowadź określony tekst, a następnie wybierz zdarzenie **podmiotu** pola. Tutaj temat wiadomości e-mail zawiera nazwę zaktualizowanego zasobu (maszyny wirtualnej). |
   | **Body** | Tak | Grupa zasobów: **Temat** <p>Typ zdarzenia: **Typ zdarzenia**<p>Identyfikator zdarzenia: **Identyfikator**<p>Czas: **Czas zdarzenia** | Wprowadź zawartość w polu treści wiadomości e-mail. Na potrzeby tego samouczka wprowadź określony tekst i wybierz zdarzenie **tematu**, **typ zdarzenia**, **identyfikator**, i **czas trwania zdarzenia** pola tak, aby Twoje wiadomość e-mail zawiera nazwę grupy zasobów, typ zdarzenia, sygnatura czasowa zdarzenia i identyfikator zdarzenia dla aktualizacji. <p>Aby dodać puste wiersze w zawartości, naciśnij klawisze Shift + Enter. |
   ||||

   > [!NOTE] 
   > Jeśli wybierzesz pole reprezentujące tablicę, projektant automatycznie dodaje pętlę **Dla każdego** wokół akcji, która odwołuje się do tablicy. W ten sposób Twoja aplikacja logiki wykonuje tę akcję dla każdego elementu tablicy.

   Teraz Twoja akcja wiadomości e-mail może wyglądać tak jak w tym przykładzie:

   ![Wybieranie danych wyjściowych do uwzględnienia w wiadomości e-mail](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Gotowa aplikacja logiki może wyglądać jak w tym przykładzie:

   ![Ukończona aplikacja logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Zapisz aplikację logiki. Aby zwinąć i ukryć szczegóły akcji w aplikacji logiki, wybierz pasek tytułu tej akcji.

   Aplikacja logiki jest teraz aktywna, ale czeka na zmiany maszyny wirtualnej, zanim wykona jakiekolwiek akcje. 
   Aby teraz przetestować Twoją aplikację logiki, przejdź do następnej sekcji.

## <a name="test-your-logic-app-workflow"></a>Testowanie przepływu pracy aplikacji logiki

1. Aby sprawdzić, czy aplikacja logiki otrzymuje określone zdarzenia, zaktualizuj maszynę wirtualną.

   Możesz na przykład zmienić rozmiar maszyny wirtualnej w witrynie Azure Portal lub [zmienić rozmiar maszyny wirtualnej za pomocą programu Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Po kilku chwilach powinna nadejść wiadomość e-mail. Na przykład:

   ![Wiadomość e-mail o aktualizacji maszyny wirtualnej](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Przejrzyj przebiegi i wyzwalać historii dla swojej aplikacji logiki, w menu aplikacji logiki, wybierz **Przegląd**. Aby wyświetlić więcej szczegółów na temat uruchomienia, wybierz wiersz dla tego uruchomienia.

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

* Aby zatrzymać aplikację logiki bez usuwania swojej pracy, wyłącz ją. W menu aplikacji logiki, wybierz **Przegląd**. Na pasku narzędzi wybierz pozycję **Wyłącz**.

  ![Wyłączanie aplikacji logiki](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Jeśli nie widzisz menu aplikacji logiki, spróbuj wrócić do pulpitu nawigacyjnego platformy Azure i ponownie otworzyć aplikację logiki.

* Aby trwale usunąć aplikację logiki, w menu aplikacji logiki wybierz pozycję **Przegląd**. Na pasku narzędzi wybierz pozycję **Usuń**. Upewnij się, że chcesz usunąć aplikację logiki, a następnie wybierz **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie i kierowanie zdarzeń niestandardowych za pomocą usługi Event Grid](../event-grid/custom-event-quickstart.md)
