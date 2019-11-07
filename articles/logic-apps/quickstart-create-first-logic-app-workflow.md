---
title: Tworzenie pierwszego zautomatyzowanego przepływu pracy — Azure Logic Apps
description: Szybki Start — Tworzenie pierwszego zautomatyzowanego zadania przy użyciu Azure Logic Apps na potrzeby rozwiązań integracji systemu i integracji aplikacji w przedsiębiorstwie (EAI)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/20/2019
ms.openlocfilehash: d5ac23da7b4460211100f1140c24f6ed28ab3e39
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584341"
---
# <a name="quickstart-create-your-first-automated-workflow-with-azure-logic-apps---azure-portal"></a>Szybki start: tworzenie pierwszego zautomatyzowanego przepływu pracy za pomocą usługi Azure Logic Apps — witryna Azure Portal

W tym przewodniku Szybki start wyjaśniono, jak utworzyć pierwszy zautomatyzowany przepływ pracy za pomocą usługi [Azure Logic Apps](../logic-apps/logic-apps-overview.md). W tym artykule opisano, jak utworzyć aplikację logiki regularnie sprawdzającą kanał informacyjny RSS witryny internetowej pod kątem nowych elementów. Jeśli istnieją nowe elementy, aplikacja logiki wysyła wiadomość e-mail dotyczącą każdego elementu. Po ukończeniu aplikacja logiki będzie ogólnie wyglądać jak ten przepływ pracy:

![Przykładowy przepływ pracy aplikacji logiki wysokiego poziomu](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

Aby skorzystać z tego przewodnika Szybki Start, musisz mieć konto e-mail od dostawcy, który jest obsługiwany przez Azure Logic Apps, na przykład Office 365 Outlook, Outlook.com lub gmail. W przypadku innych dostawców [przejrzyj tę listę łączników](https://docs.microsoft.com/connectors/). Ta aplikacja logiki korzysta z konta Office 365 Outlook. Jeśli używasz innego konta e-mail, ogólne kroki są takie same, ale interfejs użytkownika może się nieco różnić.

Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. Na stronie głównej platformy Azure w polu wyszukiwania Znajdź i wybierz **Logic Apps**.

   ![Znajdź i wybierz pozycję "Logic Apps"](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Na stronie **Logic Apps** wybierz pozycję **Dodaj**.

   ![Dodaj nową aplikację logiki](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. W okienku aplikacja logiki podaj szczegółowe informacje o aplikacji logiki, jak pokazano poniżej. Po zakończeniu wybierz pozycję **Utwórz**.

   ![Podaj szczegóły nowej aplikacji logiki](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | <> *logiki — nazwa aplikacji* | Nazwa aplikacji logiki, która może zawierać tylko litery, cyfry, łączniki (`-`), podkreślenia (`_`), nawiasów (`(`, `)`) i okresów (`.`). W tym przykładzie zastosowano "My-First-Logic-App". |
   | **Subskrypcja** | <*Azure-subscription-name*> | Nazwa subskrypcji platformy Azure |
   | **Grupa zasobów** | <*Azure-Resource-Group-name*> | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) używanej do organizowania powiązanych zasobów. W tym przykładzie jest stosowana wartość "My-First-LA-RG". |
   | **Lokalizacja** | <> *platformy Azure* | Region, w którym są przechowywane informacje o aplikacji logiki. W tym przykładzie zastosowano "zachodnie stany USA". |
   | **Log Analytics** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. |
   ||||

1. Po wdrożeniu aplikacji przez platformę Azure na pasku narzędzi platformy Azure wybierz pozycję **powiadomienia** > **Przejdź do zasobu** dla wdrożonej aplikacji logiki.

   ![Przejdź do nowo utworzonego zasobu aplikacji logiki](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Możesz też znaleźć i wybrać aplikację logiki, wpisując nazwę w polu wyszukiwania.

   Zostanie otwarty Projektant aplikacji usługi Logic Apps wyświetlający stronę z wprowadzającym wideo i najczęściej używanymi wyzwalaczami. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Wybieranie pustego szablonu dla aplikacji logiki](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Następnie dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts), który będzie aktywowany po pojawieniu się nowego elementu w kanale informacyjnym RSS. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Za każdym razem, gdy wyzwala wyzwalacz, aparat Azure Logic Apps tworzy wystąpienie aplikacji logiki, które uruchamia i uruchamia przepływ pracy.

<a name="add-rss-trigger"></a>

## <a name="check-rss-feed-with-a-trigger"></a>Sprawdzanie kanału informacyjnego RSS za pomocą wyzwalacza

1. W **Projektancie aplikacji logiki**, w polu wyszukiwania zaznacz **wszystko**.

1. W polu wyszukiwania wpisz `rss`. Z listy Wyzwalacze wybierz ten wyzwalacz: **po opublikowaniu elementu kanału informacyjnego**.

   ![Wybierz wyzwalacz "gdy element kanału informacyjnego jest opublikowany"](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Podaj te informacje dla wyzwalacza, jak pokazano i opisano tutaj:

   ![Konfigurowanie kanału informacyjnego RSS, częstotliwości i interwału dla wyzwalacza](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Adres URL źródła danych RSS** | `http://feeds.reuters.com/reuters/topNews` | Link do źródła danych RSS, które chcesz monitorować |
   | **Interwał** | 1 | Liczba interwałów do odczekania między sprawdzaniami |
   | **Częstotliwość** | Minuta | Jednostka czasu dla każdego interwału między sprawdzaniami  |
   ||||

   Interwał i częstotliwość określają harmonogram wyzwalacza aplikacji logiki. Ta aplikacja logiki sprawdza kanał informacyjny co minutę.

1. Aby na razie ukryć szczegóły wyzwalacza, kliknij wewnątrz jego paska tytułu.

   ![Zwiń kształt aplikacji logiki, aby ukryć szczegóły](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Twoja aplikacja logiki jest już aktywna, ale nie robi niczego poza sprawdzaniem źródła danych RSS. Dodaj więc akcję reagującą na aktywowanie wyzwalacza.

## <a name="send-email-with-an-action"></a>Wysyłanie wiadomości e-mail za pomocą akcji

Teraz Dodaj [akcję](../logic-apps/logic-apps-overview.md#logic-app-concepts) , która wysyła wiadomość e-mail po pojawieniu się nowego elementu w kanale informacyjnym RSS.

1. W obszarze wyzwalacz **po opublikowaniu elementu kanału informacyjnego** wybierz pozycję **nowy krok**.

   ![W obszarze wyzwalacza wybierz pozycję "nowy krok".](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. W obszarze **Wybierz akcję** i pole wyszukiwania wybierz pozycję **wszystkie**.

1. W polu wyszukiwania wpisz `send an email`. Z listy akcji wybierz akcję „wyślij wiadomość e-mail” dla odpowiedniego dostawcy poczty e-mail.

   ![Wybierz akcję "Wyślij wiadomość e-mail" dla pakietu Office 365 Outlook](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Aby na liście akcji wyświetlić akcje tylko dla konkretnej aplikacji lub usługi, możesz najpierw wybrać tę aplikację lub usługę:

   * Dla kont służbowych platformy Azure wybierz pozycję Office 365 Outlook.
   * Dla osobistych kont Microsoft wybierz pozycję Outlook.com.

1. Jeśli pojawi się prośba o podanie poświadczeń, zaloguj się do swojego konta e-mail, aby usługa Logic Apps utworzyła połączenie z tym kontem e-mail.

1. W obszarze akcji **Wyślij wiadomość e-mail** podaj dane, które powinny znaleźć się w wiadomości e-mail.

   1. W polu **Do** wprowadź adres e-mail adresata. Do celów testowych możesz użyć swojego adresu e-mail.

      Na razie zignoruj wyświetloną listę **Dodaj zawartość dynamiczną**. Ta lista pojawia się po kliknięciu wewnątrz niektórych pól edycji. Znajdują się na niej wszelkie dostępne parametry z poprzedniego kroku, które można dołączyć jako dane wejściowe w przepływie pracy.

   1. W polu **Temat** wprowadź następujący tekst ze spacją na końcu: `New RSS item: `

      ![We właściwości "podmiot" Wprowadź temat wiadomości e-mail.](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. Z listy **Dodaj zawartość dynamiczną** wybierz pozycję **Tytuł źródła danych**, aby uwzględnić tytuł elementu kanału RSS.

      ![Z listy zawartości dynamicznej wybierz właściwość "tytuł źródła danych"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Gdy wszystko będzie gotowe, temat wiadomości e-mail będzie wyglądać następująco:

      ![Zakończono przykład tematu wiadomości e-mail dla dodanego tytułu kanału informacyjnego](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Jeśli w projektancie pojawi się pętla „For each”, oznacza to, że wybrano token dla tablicy, na przykład token **categories-Item**. W przypadku takiego typu tokenów projektant automatycznie dodaje pętlę do akcji, która odwołuje się do tego tokenu. W ten sposób Twoja aplikacja logiki wykonuje tę samą akcję dla każdego elementu tablicy. Aby usunąć pętlę, wybierz **wielokropek** ( **...** ) na pasku tytułu pętli, a następnie wybierz pozycję **Usuń**.

   1. W polu **Treść** wprowadź następujący tekst i wybierz widoczne tokeny dla treści wiadomości e-mail. Aby dodać puste wiersze w polu edycji, naciśnij klawisze Shift + Enter.

      ![Wybierz właściwości zawartości wiadomości e-mail](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Właściwość | Opis |
      |----------|-------------|
      | **Tytuł źródła danych** | Tytuł elementu |
      | **Data opublikowania kanału informacyjnego** | Data i godzina opublikowania elementu |
      | **Link podstawowego źródła danych** | Adres URL elementu |
      |||

1. Zapisz aplikację logiki.

Następnie przeprowadź testowanie aplikacji logiki.

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Możesz też zaczekać, aż aplikacja logiki sprawdzi źródło danych RSS zgodnie z określonym harmonogramem (co minutę). Jeśli źródło danych RSS będzie zawierać nowe elementy, aplikacja logiki wyśle wiadomość e-mail dla każdego nowego elementu. W przeciwnym razie aplikacja logiki będzie czekać z ponownym sprawdzeniem do następnego interwału. Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci.

Oto przykładowa wiadomość e-mail wysyłana przez tę aplikację logiki.

![Przykładowa wiadomość e-mail wysłana po wyświetleniu nowego elementu kanału informacyjnego RSS](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Technicznie, gdy wyzwalacz sprawdza kanał informacyjny RSS i odnajduje nowe elementy, uruchamiany jest wyzwalacz, a aparat Azure Logic Apps tworzy wystąpienie przepływu pracy aplikacji logiki, który uruchamia akcje w przepływie pracy. Jeśli wyzwalacz nie znajdzie nowych elementów, to nie zostanie wyzwolony i pominie tworzenie wystąpienia przepływu pracy.

Gratulacje, udało Ci się pomyślnie skompilować i uruchomić swoją pierwszą aplikację logiki przy użyciu Azure Portal.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy ten przykład nie będzie już potrzebny, usuń grupę zasobów zawierającą aplikację logiki i powiązane zasoby.

1. W głównym menu platformy Azure wybierz pozycję **grupy zasobów**, a następnie wybierz grupę zasobów aplikacji logiki. W okienku **Przegląd** wybierz pozycję **Usuń grupę zasobów**.

   ![Znajdowanie, wybieranie i usuwanie grupy zasobów](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Gdy zostanie wyświetlone okienko potwierdzenia, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

   ![Aby potwierdzić usunięcie, wybierz pozycję "Usuń"](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Po usunięciu aplikacji logiki nie są tworzone wystąpienia nowych przebiegów. Wszystkie trwające i oczekujące przebiegi zostają anulowane. Anulowanie kilku tysięcy przebiegów może zająć dużo czasu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono pierwszą aplikację logiki, która sprawdza aktualizacje kanału informacyjnego RSS według określonego harmonogramu (co minutę) i jeśli istnieją aktualizacje, wykonuje akcję (wysyła wiadomość e-mail). Aby dowiedzieć się więcej, kontynuuj pracę z tym samouczkiem nad tworzeniem bardziej zaawansowanych przepływów pracy opartych na harmonogramie:

> [!div class="nextstepaction"]
> [Sprawdzanie ruchu za pomocą aplikacji logiki opartej na harmonogramie](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
