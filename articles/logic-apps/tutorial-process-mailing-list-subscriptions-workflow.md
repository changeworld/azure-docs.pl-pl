---
title: Tworzenie przepływów pracy zatwierdzania w celu przetwarzania żądań listy adresowej — Azure Logic Apps | Microsoft Docs
description: 'Samouczek: jak tworzyć zautomatyzowane przepływy pracy zatwierdzania na potrzeby przetwarzania subskrypcji listy adresowej przy użyciu usługi Azure Logic Apps'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: b48ecce1c87c0a29996e437d621c3ce396a84856
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60503464"
---
# <a name="manage-mailing-list-requests-with-azure-logic-apps"></a>Zarządzanie żądaniami listy adresowej za pomocą usługi Azure Logic Apps

Usługa Azure Logic Apps pomaga automatyzować przepływy pracy i integrować dane w usługach platformy Azure, usługach firmy Microsoft, innych aplikacjach typu oprogramowanie jako usługa (SaaS) oraz systemach lokalnych. Ten samouczek pokazuje, jak można tworzyć [aplikację logiki](../logic-apps/logic-apps-overview.md), która przetwarza żądania subskrypcji dla listy adresowej zarządzanej przez usługę [MailChimp](https://mailchimp.com/).
Ta aplikacja logiki monitoruje konto e-mail pod kątem tych żądań, wysyła te żądania do zatwierdzenia i dodaje zatwierdzone elementy członkowskie do listy adresowej.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenia pustej aplikacji logiki.
> * Dodawania wyzwalacza monitorującego wiadomości e-mail pod kątem żądań subskrypcji.
> * Dodawania akcji, która wysyła wiadomości e-mail do zatwierdzenia lub odrzucenia tych żądań.
> * Dodawania warunku sprawdzającego odpowiedzi na żądania zatwierdzenia.
> * Dodawania akcji dodającej zatwierdzone elementy członkowskie do listy adresowej.
> * Dodawania warunku sprawdzającego, czy te elementy członkowskie zostały pomyślnie dołączone do listy.
> * Dodawania akcji, która wysyła wiadomości e-mail z potwierdzeniem, czy te elementy członkowskie zostały pomyślnie dołączone do listy.

Po ukończeniu aplikacja logiki będzie ogólnie wyglądać jak ten przepływ pracy:

![Ukończona aplikacja logiki wysokiego poziomu](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-overview.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto MailChimp. Utwórz listę o nazwie „test-members-ML”, do której aplikacja logiki może dodawać adresy e-mail zatwierdzonych elementów członkowskich. Jeśli nie masz konta, [załóż bezpłatne konto](https://login.mailchimp.com/signup/) i dowiedz się, [jak utworzyć listę](https://us17.admin.mailchimp.com/lists/#). 

* Konto e-mail usługi Office 365 Outlook lub Outlook.com obsługujące przepływy pracy zatwierdzania. W tym artykule wykorzystano konto usługi Office 365 Outlook. Jeśli korzystasz z innego konta e-mail, ogólne kroki pozostają takie same, ale Twój interfejs użytkownika może wyglądać trochę inaczej.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do <a href="https://portal.azure.com" target="_blank">witryny Azure Portal</a> przy użyciu poświadczeń konta Azure.

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. Z głównego menu platformy Azure wybierz pozycję **Utwórz zasób** > **Integracja dla przedsiębiorstw** > **Aplikacja logiki**.

   ![Tworzenie aplikacji logiki](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app.png)

2. W obszarze **Tworzenie aplikacji logiki** wprowadź następujące informacje na temat aplikacji logiki. Gdy skończysz, wybierz pozycję **Przypnij do pulpitu nawigacyjnego** > **Utwórz**.

   ![Podawanie informacji na temat aplikacji logiki](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Ustawienie | Wartość | Opis | 
   | ------- | ----- | ----------- | 
   | **Nazwa** | LA-MailingList | Nazwa aplikacji logiki | 
   | **Subskrypcja** | <*your-Azure-subscription-name*> | Nazwa subskrypcji platformy Azure | 
   | **Grupa zasobów** | LA-MailingList-RG | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) używanej do organizowania powiązanych zasobów | 
   | **Lokalizacja** | Wschodnie stany USA 2 | Region, w którym będą przechowywane informacje na temat aplikacji logiki | 
   | **Log Analytics** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. | 
   |||| 

3. Po wdrożeniu aplikacji na platformie Azure zostanie otwarty Projektant aplikacji usługi Logic Apps ze stroną zawierającą wprowadzający film wideo i szablony typowych wzorców aplikacji logiki. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Wybieranie szablonu pustej aplikacji logiki](./media/tutorial-process-mailing-list-subscriptions-workflow/choose-logic-app-template.png)

Następnie dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts), który będzie nasłuchiwać pod kątem przychodzących wiadomości e-mail z żądaniami subskrypcji.
Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku przez nowe dane. Aby uzyskać więcej informacji, zobacz [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Dodawanie wyzwalacza w celu monitorowania wiadomości e-mail

1. W projektancie wprowadź frazę „when email arrives” (po nadejściu wiadomości e-mail) w polu wyszukiwania. Wybierz wyzwalacz dla dostawcy poczty e-mail: **<*Twój-dostawca-poczty-e-mail*> — Po nadejściu nowej wiadomości e-mail**
   
   ![Wybierz ten wyzwalacz dla dostawcy poczty e-mail: „Po nadejściu nowej wiadomości e-mail”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Dla kont służbowych platformy Azure wybierz pozycję Office 365 Outlook.
   * Dla osobistych kont Microsoft wybierz pozycję Outlook.com.

2. Jeśli pojawi się prośba o podanie poświadczeń, zaloguj się do swojego konta e-mail, aby usługa Logic Apps mogła utworzyć połączenie z tym kontem e-mail.

3. Teraz określ kryteria, które wyzwalacz sprawdzi we wszystkich nowych wiadomościach e-mail.

   1. Określ folder, interwał i częstotliwość sprawdzania wiadomości e-mail.

      ![Określanie folderu, interwału i częstotliwości sprawdzania wiadomości e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Ustawienie | Wartość | Opis | 
      | ------- | ----- | ----------- | 
      | **Folder** | Skrzynka odbiorcza | Folder poczty e-mail do monitorowania | 
      | **Interwał** | 1 | Liczba interwałów do odczekania między sprawdzaniami | 
      | **Częstotliwość** | Godzina | Jednostka czasu dla każdego interwału między sprawdzaniami  | 
      |  |  |  | 

   2. Wybierz pozycję **Pokaż opcje zaawansowane**. W polu **Filtr tematu** wpisz następujący tekst, który będzie wyszukiwany przez wyzwalacz w temacie wiadomości e-mail: ```subscribe-test-members-ML```

      ![Ustawianie opcji zaawansowanych](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-advanced-options.png)

4. Aby na razie ukryć szczegóły wyzwalacza, kliknij pasek tytułu wyzwalacza.

   ![Zwinięty kształt w celu ukrycia szczegółów](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

5. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Twoja aplikacja logiki jest już aktywna, ale nie robi niczego poza sprawdzaniem przychodzących wiadomości e-mail. 
   Dodaj więc akcję reagującą na aktywowanie wyzwalacza.

## <a name="send-approval-email"></a>Wysyłanie wiadomości e-mail dotyczącej zatwierdzenia

Teraz, gdy wyzwalacz jest gotowy, dodaj [akcję](../logic-apps/logic-apps-overview.md#logic-app-concepts) wysyłającą wiadomość e-mail w celu zatwierdzenia lub odrzucenia żądania. 

1. W obszarze wyzwalacza wybierz opcję **+ Nowy krok** > **Dodaj akcję**. Wyszukaj frazę „approval” (zatwierdzenie) i wybierz tę akcję: **<*Twój-dostawca-poczty-e-mail*> — Wysłanie wiadomości e-mail dotyczącej zatwierdzenia**

   ![Wybieranie pozycji „<Twój-dostawca-poczty-e-mail> — Wysłanie wiadomości e-mail dotyczącej zatwierdzenia”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

2. Podaj informacje dotyczące tej akcji, jak pokazano i opisano: 

   ![Wybór ustawień wiadomości e-mail dotyczącej zatwierdzenia](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Ustawienie | Wartość | Opis | 
   | ------- | ----- | ----------- | 
   | **Do** | <*adres-e-mail-osoby-zatwierdzającej*> | Adres e-mail osoby zatwierdzającej. Do celów testowych możesz użyć własnego adresu e-mail. | 
   | **Opcje użytkownika** | Zatwierdź, Odrzuć | Opcje odpowiedzi, które może wybrać osoba zatwierdzająca. Domyślnie osoba zatwierdzająca może wybrać jako odpowiedź opcję „Zatwierdź” lub „Odrzuć”. | 
   | **Temat** | Zatwierdzenie żądania elementu członkowskiego dla listy adresowej test-members-ML | Opisowy temat wiadomości e-mail | 
   |  |  |  | 

   Na razie zignoruj listę zawartości dynamicznej oraz śródwierszową listę parametrów wyświetlane po kliknięciu wewnątrz określonych pól edycji. 
   Ta lista pozwala wybrać parametry z poprzednich akcji, których można użyć jako danych wejściowych w przepływie pracy. 
   Rodzaj wyświetlanej listy jest określany przez szerokość okna przeglądarki. 
 
4. Zapisz aplikację logiki.

Następnie dodaj warunek sprawdzający odpowiedź wybieraną przez osobę zatwierdzającą.

## <a name="check-approval-response"></a>Sprawdzanie odpowiedzi na żądanie zatwierdzenia

1. W obszarze akcji **Wysyłanie wiadomości e-mail dotyczącej zatwierdzenia** wybierz pozycję **+ Nowy krok** > **Dodaj warunek**.

   Zostanie wyświetlony kształt warunku wraz z wszystkimi dostępnymi parametrami, które można dodać jako dane wejściowe do przepływu pracy. 

2. Zmień nazwę warunku na lepszy opis.

   1. Na pasku tytułu warunku wybierz przycisk z **wielokropkiem** (**...**) > **Zmień nazwę**.

      Na przykład jeśli okno przeglądarki jest wąskie:

      ![Zmienianie nazwy warunku](./media/tutorial-process-mailing-list-subscriptions-workflow/condition-rename.png)

      Jeśli okno przeglądarki jest szerokie, a lista zawartości dynamicznej blokuje dostęp do przycisku wielokropka, zamknij listę, wybierając pozycję **Dodaj zawartość dynamiczną** wewnątrz warunku.

   2. Zmień nazwę warunku na następujący opis: ```If request approved```

3. Utwórz warunek sprawdzający, czy osoba zatwierdzająca zaznaczyła opcję **Zatwierdź**:

   1. Wewnątrz warunku kliknij w polu **Wybierz wartość** po lewej stronie (szerokie okno przeglądarki) lub na górze (wąskie okno przeglądarki).
   Na liście parametrów lub liście zawartości dynamicznej wybierz pole **SelectedOption** (zaznaczona opcja) w obszarze **Wysyłanie wiadomości e-mail dotyczącej zatwierdzenia**.

      Przykładowo podczas pracy w szerokim oknie warunek wygląda jak w niniejszym przykładzie:

      ![W obszarze „Wysyłanie wiadomości e-mail dotyczącej zatwierdzenia” wybierz pozycję „SelectedOption” (zaznaczona opcja)](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   2. W polu operatora porównania wybierz następujący operator: **jest równe**

   3. W polu **Wybierz wartość** z prawej strony (szerokie okno) lub u dołu (wąskie okno) wprowadź wartość: ```Approve```

      Gdy wszystko będzie gotowe, warunek będzie wyglądać następująco:

      ![Gotowy warunek](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

4. Zapisz aplikację logiki.

Następnie określ akcję, która będzie wykonywana przez aplikację logiki, gdy osoba sprawdzająca zatwierdzi żądanie. 

## <a name="add-member-to-mailchimp-list"></a>Dodawanie elementu członkowskiego do listy MailChimp

Teraz dodaj akcję, która doda zatwierdzony element członkowski do listy adresowej.

1. W gałęzi warunku **W przypadku wartości true** wybierz opcję **Dodaj akcję**.
Wyszukaj frazę "mailchimp" i wybierz następującą akcję: **MailChimp — Dodaj członka do listy**

   ![Wybieranie opcji „MailChimp — Dodawanie elementu członkowskiego do listy”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

3. W przypadku wyświetlenia prośby o zalogowanie się do konta MailChimp zaloguj się przy użyciu swoich poświadczeń MailChimp.

4. Podaj informacje dotyczące tej akcji, jak pokazano i opisano tutaj:

   ![Podawanie informacji dla pozycji „Dodawanie elementu członkowskiego do listy”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Ustawienie | Wartość | Opis | 
   | ------- | ----- | ----------- | 
   | **Identyfikator listy** | test-members-ML | Nazwa listy adresowej MailChimp | 
   | **Stan** | subscribed | Stan subskrypcji dla nowego elementu członkowskiego. Aby uzyskać więcej informacji, zobacz <a href="https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/" target="_blank">Zarządzanie subskrybentami za pomocą interfejsu API MailChimp</a>. | 
   | **Adres e-mail** | <*new-member-email-address*> | Na liście parametrów albo liście zawartości dynamicznej wybierz pozycję **Od** w obszarze **Po nadejściu nowej wiadomości e-mail**, która przekazuje adres e-mail nowego elementu członkowskiego. 
   |  |  |  | 

5. Zapisz aplikację logiki.

Następnie dodaj warunek, który pozwoli sprawdzić, czy nowy element członkowski został pomyślnie dołączony do listy adresowej. W ten sposób aplikacja logiki powiadomi Cię, czy operacja zakończyła się powodzeniem lub niepowodzeniem.

## <a name="check-for-success-or-failure"></a>Sprawdzanie pod kątem powodzenia lub niepowodzenia

1. W gałęzi **W przypadku wartości true**, w obszarze akcji **Dodawanie elementu członkowskiego do listy** wybierz pozycję **Więcej...** > **Dodaj warunek**.

2. Zmień nazwę warunku na następujący opis: ```If add member succeeded```

3. Utwórz warunek sprawdzający, czy dołączenie zatwierdzonego elementu członkowskiego do listy adresowej zakończyło się powodzeniem lub niepowodzeniem:

   1. Wewnątrz warunku kliknij w polu **Wybierz wartość** po lewej stronie (szerokie okno przeglądarki) lub na górze (wąskie okno przeglądarki).
   Na liście parametrów lub liście zawartości dynamicznej wybierz pole **Stan** w obszarze **Dodawanie elementu członkowskiego do listy**.

      Przykładowo podczas pracy w szerokim oknie warunek wygląda jak w niniejszym przykładzie:

      ![W obszarze „Dodawanie elementu członkowskiego do listy” wybierz „Stan”](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   2. W polu operatora porównania wybierz następujący operator: **jest równe**

   3. W polu **Wybierz wartość** z prawej strony (szerokie okno) lub u dołu (wąskie okno) wprowadź wartość: ```subscribed```

   Gdy wszystko będzie gotowe, warunek będzie wyglądać następująco:

   ![Gotowy warunek](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Następnie skonfiguruj wiadomości e-mail, które mają być wysyłane, gdy dołączenie zatwierdzonego elementu członkowskiego do listy adresowej zakończy się powodzeniem lub niepowodzeniem.

## <a name="send-email-if-member-added"></a>Wysyłanie wiadomości e-mail w przypadku dodania elementu członkowskiego

1. W gałęzi **W przypadku wartości true** warunku **Jeśli dodanie elementu członkowskiego zakończyło się pomyślnie** wybierz pozycję **Dodaj akcję**.

   ![W gałęzi „W przypadku wartości true” warunku wybieranie pozycji „Dodaj akcję”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

2. Wyszukaj frazę „outlook send email” (wysłanie wiadomości e-mail z programu Outlook) i wybierz tę akcję: **<*Twój-dostawca-poczty-e-mail*> — Wysłanie wiadomości e-mail**

   ![Dodawanie akcji „Wysłanie wiadomości e-mail”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

3. Zmień nazwę akcji na następujący opis: ```Send email on success```

4. Podaj informacje dotyczące tej akcji, jak pokazano i opisano:

   ![Podaj informacje dla wiadomości e-mail z informacją o powodzeniu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Ustawienie | Wartość | Opis | 
   | ------- | ----- | ----------- | 
   | **Do** | <*your-email-address*> | Adres e-mail, na który ma być wysłana wiadomość e-mail z informacją o powodzeniu. Do celów testowych możesz użyć własnego adresu e-mail. | 
   | **Temat** | <*subject-for-success-email*> | Temat wiadomości e-mail z informacją o powodzeniu. Na potrzeby tego samouczka wprowadź ten tekst, a następnie wybierz określone pole w obszarze **Dodawanie elementu członkowskiego do listy** z listy parametrów lub listy zawartości dynamicznej: <p>„Powodzenie! Elementu członkowskiego dodanego do 'test-members-ML': **Adres e-mail**" | 
   | **Treść** | <*body-for-success-email*> | Treść wiadomości e-mail z informacją o powodzeniu. Na potrzeby tego samouczka wprowadź ten tekst, a następnie wybierz określone pola w obszarze **Dodawanie elementu członkowskiego do listy** z listy parametrów lub listy zawartości dynamicznej:  <p>"Nowy element członkowski dołączył 'test-members-ML': **Adres e-mail**"</br>"Stan akceptacji elementu członkowskiego: **Stan**" | 
   | | | | 

5. Zapisz aplikację logiki.

## <a name="send-email-if-member-not-added"></a>Wysyłanie wiadomość e-mail w przypadku nie dodania elementu członkowskiego

1. W gałęzi **W przypadku wartości false** warunku **Jeśli dodanie elementu członkowskiego zakończyło się pomyślnie** wybierz pozycję **Dodaj akcję**.

   ![W gałęzi „W przypadku wartości false” warunku wybieranie pozycji „Dodaj akcję”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

2. Wyszukaj frazę „outlook send email” (wysłanie wiadomości e-mail z programu Outlook) i wybierz tę akcję: **<*Twój-dostawca-poczty-e-mail*> — Wysłanie wiadomości e-mail**

   ![Dodawanie akcji „Wysłanie wiadomości e-mail”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

3. Zmień nazwę akcji na następujący opis: ```Send email on failure```

4. Podaj informacje dotyczące tej akcji, jak pokazano i opisano tutaj:

   ![Podaj informacje dla wiadomości e-mail z informacją o niepowodzeniu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Ustawienie | Wartość | Opis | 
   | ------- | ----- | ----------- | 
   | **Do** | <*your-email-address*> | Adres e-mail, na który ma być wysłana wiadomość e-mail z informacją o niepowodzeniu. Do celów testowych możesz użyć własnego adresu e-mail. | 
   | **Temat** | <*subject-for-failure-email*> | Temat wiadomości e-mail z informacją o niepowodzeniu. Na potrzeby tego samouczka wprowadź ten tekst, a następnie wybierz określone pole w obszarze **Dodawanie elementu członkowskiego do listy** z listy parametrów lub listy zawartości dynamicznej: <p>"Nie powiodło się, nie dodania elementu członkowskiego na 'test-members-ML': **Adres e-mail**" | 
   | **Treść** | <*body-for-failure-email*> | Treść wiadomości e-mail z informacją o niepowodzeniu. Na potrzeby tego samouczka wprowadź ten tekst: <p>„Element członkowski może już istnieć. Sprawdź swoje konto MailChimp”. | 
   | | | | 

5. Zapisz aplikację logiki. 

Następnie przetestuj aplikację logiki, która powinna wyglądać następująco:

 ![Ukończona aplikacja logiki](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-complete.png)

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

1. Wyślij do siebie wiadomość e-mail z żądaniem dołączenia do listy adresowej.
Poczekaj, aż żądanie pojawi się w skrzynce odbiorczej.

3. Aby ręcznie uruchomić swoją aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. 

   Jeśli Twoja wiadomość e-mail posiada temat zgodny z filtrem tematu wyzwalacza, aplikacja logiki wyśle wiadomość e-mail z prośbą o zatwierdzenie żądania subskrypcji.

4. W wiadomości e-mail dotyczącej zatwierdzenia wybierz pozycję **Zatwierdź**.

5. Jeśli adres e-mail subskrybenta nie istnieje na liście adresowej, aplikacja logiki dodaje adres e-mail tej osoby i wysyła do Ciebie wiadomość e-mail podobną do przykładowej:

   ![Wiadomość e-mail z informacją o powodzeniu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-success.png)

   Jeśli aplikacja logiki nie może dodać subskrybenta, otrzymasz wiadomość e-mail podobną do przykładowej:

   ![Wiadomość e-mail z informacją o niepowodzeniu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-failed.png)

   Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci. 
   Filtr wiadomości-śmieci Twojej poczty e-mail może przekierowywać tego rodzaju wiadomości. 
   W przeciwnym razie, jeśli nie masz pewności, czy aplikacja logiki została poprawnie uruchomiona, zobacz [Troubleshoot your logic app (Rozwiązywania problemów z aplikacją logiki)](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulacje. Udało Ci się utworzyć i uruchomić aplikację logiki, która integruje informacje z różnych usług platformy Azure, usług firmy Microsoft i innych aplikacji SaaS.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów zawierająca aplikację logiki i powiązane zasoby nie będzie już potrzebna, usuń ją. W menu głównym platformy Azure przejdź do pozycji **Grupy zasobów** i wybierz grupę zasobów aplikacji logiki. Wybierz pozycję **Usuń grupę zasobów**. Aby potwierdzić, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

![„Omówienie” > „Usuń grupę zasobów”](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku utworzyliśmy aplikację logiki, która zarządza zatwierdzaniem żądań dotyczących listy adresowej. Możesz teraz dowiedzieć się, w jaki sposób utworzyć aplikację logiki, która przetwarza i przechowuje załączniki wiadomości e-mail przez integrowanie usług platformy Azure, takich jak Azure Storage i Azure Functions.

> [!div class="nextstepaction"]
> [Przetwarzanie załączników wiadomości e-mail](../logic-apps/tutorial-process-email-attachments-workflow.md)