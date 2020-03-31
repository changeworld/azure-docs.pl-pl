---
title: Tworzenie pierwszego zautomatyzowanego przepływu pracy
description: Szybki start — tworzenie pierwszego zautomatyzowanego przepływu pracy przy użyciu aplikacji Azure Logic Apps do integracji systemów i integracji aplikacji korporacyjnych (EAI)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/20/2019
ms.openlocfilehash: c2a26f2f40e0312fbfa0962e69ddfd0cfdabad5f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77191278"
---
# <a name="quickstart-create-your-first-workflow-by-using-azure-logic-apps---azure-portal"></a>Szybki start: tworzenie pierwszego przepływu pracy przy użyciu usługi Azure Logic Apps — Witryna Azure portal

W tym przewodniku Szybki start przedstawiono podstawowe ogólne pojęcia dotyczące tworzenia pierwszego przepływu pracy przy użyciu [aplikacji Azure Logic Apps,](../logic-apps/logic-apps-overview.md)takich jak tworzenie pustej aplikacji logiki, dodawanie wyzwalacza i akcji, a następnie testowanie aplikacji logiki. W tym przewodniku Szybki start tworzysz aplikację logiki, która regularnie sprawdza kanał RSS witryny pod kątem nowych elementów. Jeśli istnieją nowe elementy, aplikacja logiki wysyła wiadomość e-mail dotyczącą każdego elementu. Po ukończeniu aplikacja logiki będzie ogólnie wyglądać jak ten przepływ pracy:

![Przepływ pracy aplikacji logiki na wysokim poziomie](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

W tym scenariuszu potrzebujesz konta e-mail z usługi obsługiwanej przez usługi Azure Logic Apps, takiej jak Office 365 Outlook, Outlook.com lub Gmail. W przypadku innych obsługiwanych usług poczty e-mail [przejrzyj listę łączników tutaj](https://docs.microsoft.com/connectors/). W tym przykładzie aplikacja logiki używa usługi Office 365 Outlook. Jeśli używasz innej usługi poczty e-mail, ogólne kroki są takie same, ale interfejs użytkownika może się nieznacznie różnić.

Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. Na stronie głównej platformy Azure w polu wyszukiwania znajdź i wybierz pozycję **Aplikacje logiki**.

   ![Znajdź i wybierz "Aplikacje logiki"](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Na stronie **Aplikacje logiki** wybierz pozycję **Dodaj**.

   ![Dodawanie nowej aplikacji logiki](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. W okienku **aplikacji logiki** podaj szczegółowe informacje o aplikacji logiki, jak pokazano poniżej. Po zakończeniu wybierz pozycję **Utwórz**.

   ![Podaj szczegóły dotyczące nowej aplikacji logiki](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | <*nazwa aplikacji logiki*> | Nazwa aplikacji logiki, która może zawierać tylko litery, cyfry, łączniki`-`( ), podkreślenia (`_`), nawiasy (`(`, `)`), i kropki (`.`). W tym przykładzie użyto "My-First-Logic-App". |
   | **Subskrypcja** | <*Nazwa subskrypcji platformy Azure*> | Twoja nazwa subskrypcji platformy Azure |
   | **Grupa zasobów** | <*Nazwa-grupa zasobów platformy Azure*> | Nazwa grupy [zasobów platformy Azure](../azure-resource-manager/management/overview.md) używana do organizowania powiązanych zasobów. W tym przykładzie użyto "My-First-LA-RG". |
   | **Lokalizacja** | <*Region platformy Azure*> | Region, w którym mają być przechowywane informacje o aplikacji logiki. W tym przykładzie użyto "Zachodnie stany USA". |
   | **Analiza dzienników** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. |
   ||||

1. Po wdrożeniu aplikacji przez platformę Azure na pasku narzędzi platformy Azure wybierz pozycję **Powiadomienia** > **Przejdź do zasobu** dla wdrożonej aplikacji logiki.

   ![Przejdź do nowo utworzonego zasobu aplikacji logiki](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Możesz też znaleźć i wybrać aplikację logiki, wpisując nazwę w polu wyszukiwania.

   Zostanie otwarty Projektant aplikacji usługi Logic Apps wyświetlający stronę z wprowadzającym wideo i najczęściej używanymi wyzwalaczami. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Wybieranie pustego szablonu dla aplikacji logiki](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Następnie dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts), który będzie aktywowany po pojawieniu się nowego elementu w kanale informacyjnym RSS. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Za każdym razem, gdy wyzwalacz jest uruchamiany, aparat usługi Azure Logic Apps tworzy wystąpienie aplikacji logiki, które uruchamia i uruchamia przepływ pracy.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Dodawanie wyzwalacza RSS

1. W **projektancie aplikacji logiki**w polu wyszukiwania wybierz pozycję **Wszystkie**.

1. W polu wyszukiwania `rss` wprowadź, aby znaleźć łącznik RSS. Z listy wyzwalaczy wybierz wyzwalacz **Po opublikowaniu elementu pliku danych.**

   ![Wybierz wyzwalacz "Po opublikowaniu elementu kanału informacyjnego"](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Podaj te informacje dla wyzwalacza, jak pokazano i opisano tutaj:

   ![Konfigurowanie kanału informacyjnego RSS, częstotliwości i interwału dla wyzwalacza](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Adres URL źródła danych RSS** | `http://feeds.reuters.com/reuters/topNews` | Link do źródła danych RSS, które chcesz monitorować |
   | **Interwał** | 1 | Liczba interwałów do odczekania między sprawdzaniami |
   | **Częstotliwości** | Minuta | Jednostka czasu dla każdego interwału między sprawdzaniami  |
   ||||

   Interwał i częstotliwość określają harmonogram wyzwalacza aplikacji logiki. Ta aplikacja logiki sprawdza kanał informacyjny co minutę.

1. Aby teraz zwinąć szczegóły wyzwalacza, kliknij wewnątrz paska tytułu wyzwalacza.

   ![Zwijanie kształtu aplikacji logiki w celu ukrycia szczegółów](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Twoja aplikacja logiki jest już aktywna, ale nie robi niczego poza sprawdzaniem źródła danych RSS. Dodaj więc akcję reagującą na aktywowanie wyzwalacza.

## <a name="add-the-send-email-action"></a>Dodawanie akcji "Wyślij wiadomość e-mail"

Teraz dodaj [akcję,](../logic-apps/logic-apps-overview.md#logic-app-concepts) która wysyła wiadomość e-mail, gdy nowy element pojawi się w kanale RSS.

1. W obszarze **Wyzwalacz Po opublikowaniu elementu kanału informacyjnego** wybierz pozycję **Nowy krok**.

   ![W obszarze wyzwalacz wybierz "Nowy krok"](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. W obszarze **Wybierz akcję** i pole wyszukiwania wybierz pozycję **Wszystkie**.

1. W polu wyszukiwania `send an email` wprowadź, aby znaleźć łączniki, które oferują tę akcję. Z listy akcji wybierz akcję "Wyślij wiadomość e-mail" dla usługi poczty e-mail, której chcesz użyć. W tym przykładzie użyto łącznika programu Outlook usługi Office 365, który ma akcję **Wyślij wiadomość e-mail.**

   ![Wybieranie akcji "Wyślij wiadomość e-mail" dla usługi Office 365 Outlook](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Aby na liście akcji wyświetlić akcje tylko dla konkretnej aplikacji lub usługi, możesz najpierw wybrać tę aplikację lub usługę:

   * Dla kont służbowych platformy Azure wybierz pozycję Office 365 Outlook.
   * Dla osobistych kont Microsoft wybierz pozycję Outlook.com.

1. Jeśli wybrany łącznik poczty e-mail monituje o uwierzytelnienie tożsamości, wykonaj ten krok teraz, aby utworzyć połączenie między aplikacją logiki a usługą poczty e-mail.

   > [!NOTE]
   > W tym konkretnym przykładzie ręcznie uwierzytelnić swoją tożsamość. Jednak łączniki, które wymagają uwierzytelniania różnią się w typach uwierzytelniania, które obsługują. Dostępne są również opcje konfigurowania sposobu obsługi uwierzytelniania. Na przykład podczas korzystania z szablonów usługi Azure Resource Manager do wdrożenia, można parametryzować i poprawić zabezpieczenia danych wejściowych, które mają być często lub łatwo zmieniać, takie jak informacje o połączeniu. Więcej informacji można znaleźć w następujących tematach:
   >
   > * [Parametry szablonu dla wdrożenia](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [Autoryzowanie połączeń OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Uwierzytelnij dostęp za pomocą tożsamości zarządzanych](../logic-apps/create-managed-service-identity.md)
   > * [Uwierzytelnianie połączeń do wdrażania aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. W obszarze akcji **Wyślij wiadomość e-mail** podaj dane, które powinny znaleźć się w wiadomości e-mail.

   1. W polu **Do** wprowadź adres e-mail adresata. Do celów testowych możesz użyć swojego adresu e-mail.

      Na razie zignoruj wyświetloną listę **Dodaj zawartość dynamiczną**. Ta lista pojawia się po kliknięciu wewnątrz niektórych pól edycji. Znajdują się na niej wszelkie dostępne parametry z poprzedniego kroku, które można dołączyć jako dane wejściowe w przepływie pracy.

   1. W polu **Temat** wprowadź następujący tekst ze spacją na końcu: `New RSS item: `

      ![We właściwości "Temat" wpisz temat wiadomości e-mail](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. Z listy **Dodaj zawartość dynamiczną** wybierz pozycję **Tytuł źródła danych**, aby uwzględnić tytuł elementu kanału RSS.

      ![Z dynamicznej listy zawartości wybierz właściwość "Tytuł kanału informacyjnego"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Gdy wszystko będzie gotowe, temat wiadomości e-mail będzie wyglądać następująco:

      ![Przykład tematu gotowego adresu e-mail dla dodanego tytułu kanału informacyjnego](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Jeśli w projektancie pojawi się pętla „For each”, oznacza to, że wybrano token dla tablicy, na przykład token **categories-Item**. W przypadku takiego typu tokenów projektant automatycznie dodaje pętlę do akcji, która odwołuje się do tego tokenu. W ten sposób Twoja aplikacja logiki wykonuje tę samą akcję dla każdego elementu tablicy. Aby usunąć pętlę, wybierz **elipsy** (**...**) na pasku tytułu pętli, a następnie wybierz pozycję **Usuń**.

   1. W polu **Treść** wprowadź następujący tekst i wybierz widoczne tokeny dla treści wiadomości e-mail. Aby dodać puste wiersze w polu edycji, naciśnij klawisze Shift + Enter.

      ![Wybierz właściwości zawartości treści wiadomości e-mail](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

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

Technicznie, gdy wyzwalacz sprawdza źródło danych RSS i znajdzie nowe elementy, wyzwalacz jest uruchamiany, a aparat usługi Azure Logic Apps tworzy wystąpienie przepływu pracy aplikacji logiki, który uruchamia akcje w przepływie pracy. Jeśli wyzwalacz nie znajdzie nowych elementów, to nie zostanie wyzwolony i pominie tworzenie wystąpienia przepływu pracy.

Gratulacje, teraz pomyślnie sbudowano i uruchomisz pierwszą aplikację logiki za pomocą witryny Azure portal.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy ten przykład nie będzie już potrzebny, usuń grupę zasobów zawierającą aplikację logiki i powiązane zasoby.

1. W głównym menu platformy Azure wybierz pozycję **Grupy zasobów**, a następnie wybierz grupę zasobów aplikacji logiki. W okienku **Przegląd** wybierz pozycję **Usuń grupę zasobów**.

   ![Znajdowanie, wybieranie i usuwanie grupy zasobów](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Po wyświetleniu okienka potwierdzenia wprowadź nazwę grupy zasobów i wybierz pozycję **Usuń**.

   ![Aby potwierdzić usunięcie, wybierz "Usuń"](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Po usunięciu aplikacji logiki nie są tworzone wystąpienia nowych przebiegów. Wszystkie trwające i oczekujące przebiegi zostają anulowane. Anulowanie kilku tysięcy przebiegów może zająć dużo czasu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono pierwszą aplikację logiki, która sprawdza aktualizacje kanału informacyjnego RSS według określonego harmonogramu (co minutę) i jeśli istnieją aktualizacje, wykonuje akcję (wysyła wiadomość e-mail). Aby dowiedzieć się więcej, kontynuuj pracę z tym samouczkiem nad tworzeniem bardziej zaawansowanych przepływów pracy opartych na harmonogramie:

> [!div class="nextstepaction"]
> [Sprawdzanie ruchu za pomocą aplikacji logiki opartej na harmonogramie](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
