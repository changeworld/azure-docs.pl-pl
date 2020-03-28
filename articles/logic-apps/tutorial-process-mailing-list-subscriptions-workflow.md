---
title: Tworzenie zautomatyzowanych przepływów pracy opartych na zatwierdzaniu
description: Samouczek — tworzenie zautomatyzowanego przepływu pracy opartego na zatwierdzeniu, który przetwarza subskrypcje list adresowych przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: 7d7f573e5b18e6e0e63d3275aecefe408a9143fb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75456607"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Samouczek: tworzenie zautomatyzowanych przepływów pracy opartych na zatwierdzaniu przy użyciu aplikacji Azure Logic Apps

W tym samouczku pokazano, jak utworzyć [aplikację logiki,](../logic-apps/logic-apps-overview.md) która automatyzuje przepływ pracy oparty na zatwierdzaniu. W szczególności ta aplikacja logiki przetwarza żądania subskrypcji listy adresowej, która jest zarządzana przez usługę [MailChimp.](https://mailchimp.com/) Ta aplikacja logiki monitoruje konto e-mail pod kątem tych żądań, wysyła te żądania do zatwierdzenia i dodaje zatwierdzone elementy członkowskie do listy adresowej.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenia pustej aplikacji logiki.
> * Dodawania wyzwalacza monitorującego wiadomości e-mail pod kątem żądań subskrypcji.
> * Dodawania akcji, która wysyła wiadomości e-mail do zatwierdzenia lub odrzucenia tych żądań.
> * Dodawania warunku sprawdzającego odpowiedzi na żądania zatwierdzenia.
> * Dodawania akcji dodającej zatwierdzone elementy członkowskie do listy adresowej.
> * Dodawania warunku sprawdzającego, czy te elementy członkowskie zostały pomyślnie dołączone do listy.
> * Dodawania akcji, która wysyła wiadomości e-mail z potwierdzeniem, czy te elementy członkowskie zostały pomyślnie dołączone do listy.

Po ukończeniu aplikacja logiki będzie ogólnie wyglądać jak ten przepływ pracy:

![Omówienie aplikacji logiki na wysokim poziomie](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu uzyskania bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.

* Konto MailChimp, które zawiera listę o nazwie "test-members-ML", gdzie aplikacja logiki można dodawać adresy e-mail dla zatwierdzonych członków. Jeśli nie masz konta, [zarejestruj się na bezpłatne konto,](https://login.mailchimp.com/signup/)a następnie dowiedz się, [jak utworzyć listę MailChimp](https://us17.admin.mailchimp.com/lists/#).

* Konto e-mail w usłudze Office 365 Outlook lub Outlook.com, które obsługuje przepływy pracy zatwierdzania. W tym artykule wykorzystano konto usługi Office 365 Outlook. Jeśli korzystasz z innego konta e-mail, ogólne kroki pozostają takie same, ale Twój interfejs użytkownika może wyglądać trochę inaczej.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. Z głównego menu platformy Azure wybierz pozycję **Utwórz aplikację** > **logiki****integracji** > zasobów .

   ![Tworzenie nowego zasobu aplikacji logiki](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. W obszarze **Tworzenie aplikacji logiki** wprowadź następujące informacje na temat aplikacji logiki. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

   ![Podaj informacje o aplikacji logiki](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | LA-MailingList | Nazwa aplikacji logiki, która może zawierać tylko litery,`-`cyfry,`_`łączniki ( ),`(` `)`podkreślenia`.`( ), nawiasy ( , ), i kropki ( ). W tym przykładzie użyto "LA-MailingList". |
   | **Subskrypcja** | <*twoja nazwa subskrypcji platformy Azure*> | Twoja nazwa subskrypcji platformy Azure |
   | **Grupa zasobów** | LA-MailingList-RG | Nazwa grupy [zasobów platformy Azure](../azure-resource-manager/management/overview.md), która jest używana do organizowania powiązanych zasobów. W tym przykładzie użyto "LA-MailingList-RG". |
   | **Lokalizacja** | Zachodnie stany USA | TRegion, w którym mają być przechowywane informacje o aplikacji logiki. W tym przykładzie użyto "Zachodnie stany USA". |
   | **Analiza dzienników** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. |
   ||||

1. Po wdrożeniu aplikacji przez platformę Azure na pasku narzędzi platformy Azure wybierz pozycję **Powiadomienia** > **Przejdź do zasobu** dla wdrożonej aplikacji logiki.

   ![Przejdź do nowego zasobu aplikacji logiki](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   Możesz też znaleźć i wybrać aplikację logiki, wpisując nazwę w polu wyszukiwania.

   Projektant aplikacji logiki otwiera i pokazuje stronę z wprowadzeniem wideo i często używanych wyzwalaczy i wzorców aplikacji logiki. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Wybieranie pustego szablonu aplikacji logiki](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Następnie dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts), który będzie nasłuchiwać pod kątem przychodzących wiadomości e-mail z żądaniami subskrypcji. Każda aplikacja logiki musi zaczynać się od wyzwalacza, który uruchamia się, gdy występuje określone zdarzenie lub gdy nowe dane spełniają określony warunek. Aby uzyskać więcej informacji, zobacz [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Dodawanie wyzwalacza w celu monitorowania wiadomości e-mail

1. W projektancie aplikacji logiki w `when email arrives` polu wyszukiwania wprowadź jako filtr. Z listy **Wyzwalacze** wybierz wyzwalacz **Po odebraniu nowej wiadomości e-mail** dla dostawcy poczty e-mail.

   W tym przykładzie użyto wyzwalacza programu Office 365 Outlook:

   ![Wybierz wyzwalacz "Po nadejściu nowej wiadomości e-mail" dla dostawcy poczty e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Dla kont służbowych platformy Azure wybierz pozycję Office 365 Outlook.
   * Dla osobistych kont Microsoft wybierz pozycję Outlook.com.

1. Jeśli zostanie wyświetlony monit, zaloguj się na swoje konto e-mail przy użyciu poświadczeń, aby aplikacje logiki mogły utworzyć połączenie z twoim kontem e-mail.

1. W wyzwalaczu podaj kryteria sprawdzania wszystkich nowych wiadomości e-mail.

   1. Określ folder, interwał i częstotliwość sprawdzania wiadomości e-mail.

      ![Określanie folderu, interwału i częstotliwości sprawdzania wiadomości e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Właściwość | Wartość | Opis |
      |----------|-------|-------------|
      | **Folder** | `Inbox` | Folder poczty e-mail do monitorowania |
      | **Interwał** | `1` | Liczba interwałów do odczekania między sprawdzaniami |
      | **Częstotliwości** | `Hour` | Jednostka czasu cyklu |
      ||||

   1. Teraz dodaj kolejną właściwość do wyzwalacza, aby można było filtrować w wierszu tematu wiadomości e-mail. Otwórz **listę Dodaj nowy parametr**i wybierz właściwość **Filtr tematu.**

      ![Dodaj właściwość "Filtr tematu", aby wyzwolić](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Aby uzyskać więcej informacji na temat właściwości tego wyzwalacza, zobacz [odwołanie do łącznika programu Outlook usługi Office 365](https://docs.microsoft.com/connectors/office365/) lub [odwołanie do łącznika Outlook.com](https://docs.microsoft.com/connectors/outlook/).

   1. Po wyświetleniu właściwości w wyzwalaczu wprowadź ten tekst:`subscribe-test-members-ML`

      ![Wprowadź tekst dla właściwości "Filtr tematu"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Aby na razie ukryć szczegóły wyzwalacza, kliknij pasek tytułu wyzwalacza.

   ![Zwinięty kształt w celu ukrycia szczegółów](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Twoja aplikacja logiki jest już aktywna, ale nie robi niczego poza sprawdzaniem przychodzących wiadomości e-mail. Dodaj więc akcję reagującą na aktywowanie wyzwalacza.

## <a name="send-approval-email"></a>Wysyłanie wiadomości e-mail dotyczącej zatwierdzenia

Teraz, gdy wyzwalacz jest gotowy, dodaj [akcję](../logic-apps/logic-apps-overview.md#logic-app-concepts) wysyłającą wiadomość e-mail w celu zatwierdzenia lub odrzucenia żądania.

1. W obszarze wyzwalacza wybierz pozycję **Nowy krok**. 

1. W obszarze **Wybierz akcję**w polu `approval` wyszukiwania wprowadź jako filtr. Z listy akcje wybierz akcję **Wyślij zatwierdzenie wiadomości e-mail** dla dostawcy poczty e-mail. 

   W tym przykładzie użyto akcji programu Outlook usługi Office 365:

   ![Wybierz akcję "Wyślij wiadomość e-mail z zatwierdzeniem"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Podaj informacje o tej akcji w sposób opisany: 

   ![Wysyłanie właściwości wiadomości e-mail z zatwierdzeniem](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Do** | <*twój adres e-mail*> | Adres e-mail osoby zatwierdzającej. Do celów testowych możesz użyć własnego adresu e-mail. W tym przykładziesophia.owen@fabrikam.comużyto fikcyjnego " " adresu e-mail. |
   | **Temat** | `Approve member request for test-members-ML` | Opisowy temat wiadomości e-mail |
   | **Opcje użytkownika** | `Approve, Reject` | Opcje odpowiedzi, które osoba zatwierdzająca może wybrać. Domyślnie osoba zatwierdzająca może wybrać jako odpowiedź opcję "Zatwierdź" lub "Odrzuć". |
   ||||

   Na razie zignoruj dynamiczną listę zawartości, która pojawia się po kliknięciu wewnątrz określonych pól edycji. Ta lista umożliwia wybranie dostępnych danych wyjściowych z poprzednich akcji, których można używać jako danych wejściowych w przepływie pracy.

   Aby uzyskać więcej informacji na temat właściwości tej akcji, zobacz [odwołanie do łącznika programu Outlook usługi Office 365](https://docs.microsoft.com/connectors/office365/) lub [odwołanie do łącznika Outlook.com](https://docs.microsoft.com/connectors/outlook/).
 
1. Zapisz aplikację logiki.

Następnie dodaj warunek, aby sprawdzić wybraną odpowiedź osoby zatwierdzającej.

## <a name="check-approval-response"></a>Sprawdzanie odpowiedzi na żądanie zatwierdzenia

1. W obszarze Akcji **Wyślij wiadomość e-mail z zatwierdzeniem** wybierz pozycję **Nowy krok**".

1. W obszarze **Wybierz akcję**wybierz pozycję **Wbudowane**. W polu wyszukiwania `condition` wprowadź jako filtr. Z listy akcje wybierz akcję **Warunek.**

   ![Znajdź i wybierz akcję "Warunek"](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Zmień nazwę warunku na lepszy opis.

   1. Na pasku tytułu warunku wybierz przycisk **elipsy** (**...**) > **Zmień nazwę**.

      ![Zmień nazwę opisu warunku](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Zmień nazwę warunku na następujący opis: `If request approved`

1. Tworzenie warunku, który sprawdza, czy osoba zatwierdzająca **wybrała opcję Zatwierdź**.

   1. W warunkach kliknij wewnątrz pola **Wybierz wartość** po lewej stronie warunku.

   1. Z wyświetlona dynamiczna lista zawartości w obszarze **Wyślij wiadomość e-mail zatwierdzenia**wybierz właściwość **SelectedOption.**

      ![Z dynamicznej listy zawartości wybierz "SelectedOption"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. W środkowym polu porównania wybierz **jest równa operatorowi.**

   1. W polu **Wybierz wartość** po prawej stronie warunku wprowadź ten tekst:`Approve`

      Po zakończeniu warunek wygląda następująco:

      ![Warunek ukończenia dla zatwierdzonego przykładu](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Zapisz aplikację logiki.

Następnie określ akcję, która będzie wykonywana przez aplikację logiki, gdy osoba sprawdzająca zatwierdzi żądanie. 

## <a name="add-member-to-mailchimp-list"></a>Dodawanie elementu członkowskiego do listy MailChimp

Teraz dodaj akcję, która dodaje zatwierdzonego członka do listy adresowej.

1. W warunku **Jeśli prawdziwa** gałąź wybierz pozycję Dodaj **akcję**.

1. W obszarze **Wybierz** `mailchimp` akcję wprowadź jako filtr i wybierz akcję **Dodaj element członkowski do listy.**

   ![Wybierz akcję "Dodaj członka do listy"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Jeśli zostanie wyświetlony monit o dostęp do konta MailChimp, zaloguj się przy użyciu poświadczeń MailChimp.

1. Podaj informacje o tej akcji, jak pokazano i opisane tutaj:

   ![Podawanie informacji dla pozycji „Dodawanie elementu członkowskiego do listy”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Identyfikator listy** | Tak | `test-members-ML` | Nazwa listy mailingowej MailChimp. W tym przykładzie użyto "test-members-ML". |
   | **Stan** | Tak | `subscribed` | Wybierz stan subskrypcji dla nowego członka. W tym przykładzie użyto "subskrybowane". <p>Aby uzyskać więcej informacji, zobacz [Zarządzanie subskrybentami za pomocą interfejsu API MailChimp](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   | **Adres e-mail** | Tak | <*nowy-członek-adres e-mail*> | Z dynamicznej listy zawartości wybierz **pozycję Od** w obszarze **Po odebraniu nowej poczty**, która przechodzi w adres e-mail nowego członka. |
   ||||

   Aby uzyskać więcej informacji na temat właściwości tej akcji, zobacz [odwołanie do łącznika MailChimp](https://docs.microsoft.com/connectors/mailchimp/).

1. Zapisz aplikację logiki.

Następnie dodaj warunek, który pozwoli sprawdzić, czy nowy element członkowski został pomyślnie dołączony do listy adresowej. W ten sposób aplikacja logiki powiadomi Cię, czy operacja zakończyła się powodzeniem lub niepowodzeniem.

## <a name="check-for-success-or-failure"></a>Sprawdzanie pod kątem powodzenia lub niepowodzenia

1. W obszarze **Jeśli prawdziwa** gałąź w obszarze Dodaj **element członkowski do listy** wybierz pozycję Dodaj **akcję**.

1. W obszarze **Wybierz akcję**wybierz pozycję **Wbudowane**. W polu wyszukiwania `condition` wprowadź jako filtr. Z listy akcje wybierz **pozycję Warunek**.

1. Zmień nazwę warunku na następujący opis: `If add member succeeded`

1. Utwórz warunek sprawdzający, czy dołączenie zatwierdzonego elementu członkowskiego do listy adresowej zakończyło się powodzeniem lub niepowodzeniem:

   1. W warunkach kliknij wewnątrz pola **Wybierz wartość,** która znajduje się po lewej stronie warunku. Z dynamicznej listy zawartości w obszarze **Dodaj członka do listy**wybierz właściwość **Stan.**

      Na przykład stan wygląda następująco:

      ![W obszarze „Dodawanie elementu członkowskiego do listy” wybierz „Stan”](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. W środkowym polu porównania wybierz **jest równa operatorowi.**

   1. W polu **Wybierz wartość** po prawej stronie warunku wprowadź ten tekst:`subscribed`

      Po zakończeniu warunek wygląda następująco:

      ![Gotowy warunek dla przykładu subskrybowanego](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Następnie skonfiguruj wiadomości e-mail, które mają być wysyłane, gdy dołączenie zatwierdzonego elementu członkowskiego do listy adresowej zakończy się powodzeniem lub niepowodzeniem.

## <a name="send-email-if-member-added"></a>Wysyłanie wiadomości e-mail w przypadku dodania elementu członkowskiego

1. W obszarze Warunek **Jeśli dodanie elementu członkowskiego powiodło się** w gałęzi Jeśli **prawda** wybierz pozycję **Dodaj akcję**.

   ![W gałęzi "Jeśli prawda" wybierz "Dodaj akcję"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. W obszarze **Wybierz akcję**w polu `outlook send email` wyszukiwania wprowadź jako filtr i wybierz akcję **Wyślij wiadomość e-mail.**

   ![Dodawanie akcji "Wyślij wiadomość e-mail"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Zmień nazwę akcji na następujący opis: `Send email on success`

1. Podaj informacje dotyczące tej akcji, jak pokazano i opisano:

   ![Podaj informacje dla wiadomości e-mail z informacją o powodzeniu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Do** | Tak | <*twój adres e-mail*> | Adres e-mail, na który ma być wysłana wiadomość e-mail z informacją o powodzeniu. Do celów testowych możesz użyć własnego adresu e-mail. |
   | **Temat** | Tak | <*temat-for-success-email*> | Temat wiadomości e-mail z informacją o powodzeniu. Na potrzeby tego samouczka wprowadź ten tekst: <p>`Success! Member added to "test-members-ML": ` <p>Z dynamicznej listy zawartości w obszarze **Dodaj członka do listy**wybierz właściwość Adres **e-mail.** |
   | **Treść** | Tak | <*body-for-success-email*> | Treść wiadomości e-mail z informacją o powodzeniu. Na potrzeby tego samouczka wprowadź ten tekst: <p>`New member has joined "test-members-ML":` <p>Z dynamicznej listy zawartości wybierz właściwość **Adres e-mail.** <p>W następnym wierszu wprowadź ten tekst:`Member opt-in status: ` <p> Z dynamicznej listy zawartości w obszarze **Dodaj członka do listy**wybierz właściwość **Stan.** |
   |||||

1. Zapisz aplikację logiki.

## <a name="send-email-if-member-not-added"></a>Wysyłanie wiadomość e-mail w przypadku nie dodania elementu członkowskiego

1. W obszarze Warunek **Jeśli dodanie elementu członkowskiego powiodło się** w gałęzi Jeśli **false,** wybierz pozycję **Dodaj akcję**.

   ![W gałęzi "Jeśli fałsz" wybierz "Dodaj akcję"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. W obszarze **Wybierz akcję**w polu `outlook send email` wyszukiwania wprowadź jako filtr i wybierz akcję **Wyślij wiadomość e-mail.**

   ![Dodawanie akcji „Wysłanie wiadomości e-mail”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Zmień nazwę akcji na następujący opis: `Send email on failure`

1. Podaj informacje o tej akcji, jak pokazano i opisane tutaj:

   ![Podaj informacje dla wiadomości e-mail z informacją o niepowodzeniu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Do** | Tak | <*twój adres e-mail*> | Adres e-mail, na który ma być wysłana wiadomość e-mail z informacją o niepowodzeniu. Do celów testowych możesz użyć własnego adresu e-mail. |
   | **Temat** | Tak | <*temat za awarię-e-mail*> | Temat wiadomości e-mail z informacją o niepowodzeniu. Na potrzeby tego samouczka wprowadź ten tekst: <p>`Failed, member not added to "test-members-ML": ` <p>Z dynamicznej listy zawartości w obszarze **Dodaj członka do listy**wybierz właściwość Adres **e-mail.** |
   | **Treść** | Tak | <*body-for-failure-email*> | Treść wiadomości e-mail z informacją o niepowodzeniu. Na potrzeby tego samouczka wprowadź ten tekst: <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Zapisz aplikację logiki. 

Następnie przetestuj aplikację logiki, która powinna wyglądać następująco:

![Przykład ukończonego przepływu pracy aplikacji logiki](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

1. Wyślij do siebie wiadomość e-mail z żądaniem dołączenia do listy adresowej. Poczekaj, aż żądanie pojawi się w skrzynce odbiorczej.

1. Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. 

   Jeśli Twoja wiadomość e-mail posiada temat zgodny z filtrem tematu wyzwalacza, aplikacja logiki wyśle wiadomość e-mail z prośbą o zatwierdzenie żądania subskrypcji.

1. W wiadomości e-mail z zatwierdzeniem wybierz pozycję **Zatwierdź**.

1. Jeśli adres e-mail subskrybenta nie istnieje na liście adresowej, aplikacja logiki dodaje adres e-mail tej osoby i wysyła do Ciebie wiadomość e-mail podobną do przykładowej:

   ![Przykładowy adres e-mail — pomyślna subskrypcja](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Jeśli aplikacja logiki nie może dodać subskrybenta, otrzymasz wiadomość e-mail podobną do przykładowej:

   ![Przykładowa wiadomość e-mail — subskrypcja nie powiodła się](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci. Filtr wiadomości-śmieci Twojej poczty e-mail może przekierowywać tego rodzaju wiadomości. W przeciwnym razie, jeśli nie masz pewności, czy aplikacja logiki została poprawnie uruchomiona, zobacz [Troubleshoot your logic app (Rozwiązywania problemów z aplikacją logiki)](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulacje. Udało Ci się utworzyć i uruchomić aplikację logiki, która integruje informacje z różnych usług platformy Azure, usług firmy Microsoft i innych aplikacji SaaS.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już przykładowej aplikacji logiki, usuń grupę zasobów zawierającą aplikację logiki i powiązane zasoby. 

1. W menu głównym platformy Azure przejdź do pozycji **Grupy zasobów** i wybierz grupę zasobów aplikacji logiki.

1. W menu grupy zasobów wybierz polecenie **Przegląd** > **Usuń grupę zasobów**. 

   ![„Omówienie” > „Usuń grupę zasobów”](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Wprowadź nazwę grupy zasobów jako potwierdzenie i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzyliśmy aplikację logiki, która zarządza zatwierdzaniem żądań dotyczących listy adresowej. Możesz teraz dowiedzieć się, w jaki sposób utworzyć aplikację logiki, która przetwarza i przechowuje załączniki wiadomości e-mail przez integrowanie usług platformy Azure, takich jak Azure Storage i Azure Functions.

> [!div class="nextstepaction"]
> [Przetwarzanie załączników wiadomości e-mail](../logic-apps/tutorial-process-email-attachments-workflow.md)
