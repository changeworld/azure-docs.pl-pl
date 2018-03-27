---
title: Tworzenie pierwszego zautomatyzowanego przepływu pracy — Azure Logic Apps | Microsoft Docs
description: W tym przewodniku Szybki start pokazano, jak zautomatyzować pierwszy przepływ pracy za pomocą usługi Azure Logic Apps na potrzeby scenariuszy integracji systemów i usług Enterprise Application Integration (EAI), które integrują systemy i usługi w chmurze.
author: ecfan
manager: anneta
editor: ''
services: logic-apps
keywords: workflows, cloud services, system integration, enterprise application integration, EAI
documentationcenter: ''
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 1/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: ccd00fbb3c5dc0f78a31adcaed31bbe6e4a6e785
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
---
# <a name="quickstart-build-your-first-logic-app-workflow---azure-portal"></a>Szybki start: Tworzenie pierwszego przepływu pracy aplikacji logiki — Azure Portal

W tym przewodniku Szybki start wyjaśniono, jak utworzyć pierwszy zautomatyzowany przepływ pracy za pomocą usługi [Azure Logic Apps](../logic-apps/logic-apps-overview.md). W tym artykule opisano, jak utworzyć aplikację logiki regularnie sprawdzającą kanał informacyjny RSS witryny internetowej pod kątem nowych elementów. Jeśli istnieją nowe elementy, aplikacja logiki wysyła wiadomość e-mail dotyczącą każdego elementu. Po ukończeniu aplikacja logiki będzie ogólnie wyglądać jak ten przepływ pracy:

![Omówienie — przykład aplikacji logiki](./media/quickstart-create-first-logic-app-workflow/overview.png)

Aby użyć tego przewodnika Szybki start, potrzebne jest konto e-mail od dostawcy, który jest obsługiwany przez usługę Logic Apps, na przykład Office 365 Outlook, Outlook.com lub Gmail. W przypadku innych dostawców [przejrzyj tę listę łączników](https://docs.microsoft.com/connectors/). Ta aplikacja logiki korzysta z konta Office 365 Outlook. Jeśli korzystasz z innego konta e-mail, ogólne kroki będą takie same, ale interfejs użytkownika może się trochę różnić. 

Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do <a href="https://portal.azure.com" target="_blank">witryny Azure Portal</a> przy użyciu poświadczeń konta Azure.

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki 

1. Z głównego menu platformy Azure wybierz pozycję **Utwórz zasób** > **Integracja dla przedsiębiorstw** > **Aplikacja logiki**.

   ![Tworzenie aplikacji logiki](./media/quickstart-create-first-logic-app-workflow/create-logic-app.png)

3. W obszarze **Tworzenie aplikacji logiki** podaj szczegółowe informacje o aplikacji logiki, jak pokazano poniżej. Po zakończeniu wybierz pozycję **Przypnij do pulpitu nawigacyjnego** > **Utwórz**.

   ![Podawanie szczegółów aplikacji logiki](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Ustawienie | Wartość | Opis | 
   | ------- | ----- | ----------- | 
   | **Nazwa** | MyFirstLogicApp | Nazwa aplikacji logiki | 
   | **Subskrypcja** | <*your-Azure-subscription-name*> | Nazwa subskrypcji platformy Azure | 
   | **Grupa zasobów** | My-First-LA-RG | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) używanej do organizowania powiązanych zasobów | 
   | **Lokalizacja** | Wschodnie stany USA 2 | Region, w którym będą przechowywane informacje o aplikacji logiki | 
   | **Log Analytics** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. | 
   |||| 

3. Gdy platforma Azure wdroży aplikację, zostanie otwarty Projektant aplikacji usługi Logic Apps wyświetlający stronę z wprowadzającym wideo i najczęściej używanymi wyzwalaczami. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Wybieranie szablonu pustej aplikacji logiki](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Następnie dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts), który będzie aktywowany po pojawieniu się nowego elementu w kanale informacyjnym RSS. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Po każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki uruchamiającej i wykonującej przepływ pracy.

<a name="add-rss-trigger"></a>

## <a name="check-rss-feed-with-a-trigger"></a>Sprawdzanie kanału informacyjnego RSS za pomocą wyzwalacza

1. W projektancie wprowadź ciąg „rss” w polu wyszukiwania. Wybierz następujący wyzwalacz: **RSS — Gdy element kanału informacyjnego jest publikowany**

   ![Wybieranie wyzwalacza: „RSS — Gdy element kanału informacyjnego jest publikowany”](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss.png)

2. Podaj następujące informacje dotyczące wyzwalacza, jak pokazano i opisano: 

   ![Konfigurowanie kanału informacyjnego RSS, częstotliwości i interwału dla wyzwalacza](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss-settings.png)

   | Ustawienie | Wartość | Opis | 
   | ------- | ----- | ----------- | 
   | **Adres URL źródła danych RSS** | ```http://feeds.reuters.com/reuters/topNews``` | Link do źródła danych RSS, które chcesz monitorować | 
   | **Interwał** | 1 | Liczba interwałów do odczekania między sprawdzaniami | 
   | **Częstotliwość** | Minuta | Jednostka czasu dla każdego interwału między sprawdzaniami  | 
   |  |  |  | 

   Interwał i częstotliwość określają harmonogram wyzwalacza aplikacji logiki. 
   Ta aplikacja logiki sprawdza kanał informacyjny co minutę.

3. Aby na razie ukryć szczegóły wyzwalacza, kliknij wewnątrz jego paska tytułu.

   ![Zwinięty kształt w celu ukrycia szczegółów](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

4. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. 

Twoja aplikacja logiki jest już aktywna, ale nie robi niczego poza sprawdzaniem źródła danych RSS. Dodaj więc akcję reagującą na aktywowanie wyzwalacza.

## <a name="send-email-with-an-action"></a>Wysyłanie wiadomości e-mail za pomocą akcji

Teraz dodaj [akcję](../logic-apps/logic-apps-overview.md#logic-app-concepts), która wysyła wiadomość e-mail, gdy pojawi się nowy element w kanale informacyjnym RSS. 

1. W obszarze wyzwalacza **Gdy element kanału informacyjnego jest publikowany** wybierz pozycję **+ Nowy krok** > **Dodaj akcję**.

   ![Dodawanie akcji](./media/quickstart-create-first-logic-app-workflow/add-new-action.png)

2. W obszarze **Wybierz akcję** wyszukaj ciąg „wyślij wiadomość e-mail”, a następnie wybierz akcję „wyślij wiadomość e-mail” dla wybranego dostawcy poczty e-mail. Aby na liście akcji wyświetlić tylko konkretną usługę, można najpierw wybrać łącznik w obszarze **Łączniki**.

   ![Wybieranie akcji: „Office 365 Outlook — Wyślij wiadomość e-mail”](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   * Dla kont służbowych platformy Azure wybierz pozycję Office 365 Outlook. 
   * Dla osobistych kont Microsoft wybierz pozycję Outlook.com.

3. Jeśli pojawi się prośba o podanie poświadczeń, zaloguj się do swojego konta e-mail, aby usługa Logic Apps utworzyła połączenie z tym kontem e-mail.

4. W obszarze akcji **Wyślij wiadomość e-mail** podaj dane, które powinny znaleźć się w wiadomości e-mail. 

   1. W polu **Do** wprowadź adres e-mail adresata. 
   Do celów testowych możesz użyć własnego adresu e-mail.

      Jeśli zostanie wyświetlona lista parametrów lub lista **Dodaj zawartość dynamiczną**, na razie zignoruj ją. 
      Ta lista pojawia się po kliknięciu wewnątrz niektórych pól edycji. Znajdują się na niej wszelkie dostępne parametry z poprzedniego kroku, które można dołączyć jako dane wejściowe w przepływie pracy.
      Rodzaj wyświetlanej listy jest określany przez szerokość okna przeglądarki.

   2. W polu **Temat** wprowadź następujący tekst ze spacją na końcu: ```New RSS item: ```

      ![Wprowadzanie tematu wiadomości e-mail](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)
 
   3. Z listy parametrów lub z listy **Dodaj zawartość dynamiczną** wybierz pozycję **Tytuł źródła danych**, aby uwzględnić tytuł elementu kanału RSS.

      W poniższym przykładzie przedstawiono listę parametrów:

      ![Lista parametrów — „Tytuł źródła danych”](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-parameters-list.png)

      A tu widać listę zawartości dynamicznej:

      ![Lista zawartości dynamicznej — „Tytuł źródła danych”](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Gdy wszystko będzie gotowe, temat wiadomości e-mail będzie wyglądać następująco:

      ![Dodany tytuł źródła danych](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Jeśli w projektancie pojawi się pętla „For each”, oznacza to, że wybrano pole zawierające tablicę, na przykład pole **categories-item**. 
      W przypadku takiego typu pól projektant automatycznie dodaje pętlę do akcji, która odwołuje się do tego pola. 
      W ten sposób Twoja aplikacja logiki wykonuje tę samą akcję dla każdego elementu tablicy. 
      Aby usunąć pętlę, wybierz **wielokropek** (**...**) na pasku tytułu pętli, a następnie wybierz pozycję **Usuń**.

   4. W polu **Treść** wprowadź następujący tekst i wybierz widoczne pola dla treści wiadomości e-mail. 
   Aby dodać puste wiersze w polu edycji, naciśnij klawisze Shift + Enter. 

      ![Dodawanie treści wiadomości e-mail](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Ustawienie | Opis | 
      | ------- | ----------- | 
      | **Tytuł źródła danych** | Tytuł elementu | 
      | **Data opublikowania kanału informacyjnego** | Data i godzina opublikowania elementu | 
      | **Link podstawowego źródła danych** | Adres URL elementu | 
      ||| 
   
5. Zapisz aplikację logiki.

Następnie przeprowadź testowanie aplikacji logiki.

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

Aby ręcznie uruchomić swoją aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Możesz też zaczekać, aż aplikacja logiki sprawdzi źródło danych RSS zgodnie z określonym harmonogramem (co minutę). Jeśli źródło danych RSS będzie zawierać nowe elementy, aplikacja logiki wyśle wiadomość e-mail dla każdego nowego elementu. W przeciwnym razie aplikacja logiki będzie czekać z ponownym sprawdzeniem do następnego interwału. 

Oto przykładowa wiadomość e-mail wysyłana przez tę aplikację logiki. Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci.

![Wysyłanie wiadomości e-mail dla nowego elementu źródła danych RSS](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Z technicznego punktu widzenia, gdy wyzwalacz sprawdza źródło danych RSS i znajduje nowe elementy, jest on wyzwalany, a aparat usługi Logic Apps tworzy wystąpienie przepływu pracy aplikacji logiki, które uruchamia akcje w ramach przepływu pracy.
Jeśli wyzwalacz nie znajdzie nowych elementów, to nie zostanie wyzwolony i pominie tworzenie wystąpienia przepływu pracy.

Gratulacje, Twoja pierwsza aplikacja logiki została już pomyślnie skompilowana i uruchomiona za pomocą witryny Azure Portal!

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów zawierająca aplikację logiki i powiązane zasoby nie będzie już potrzebna, usuń ją. W menu głównym platformy Azure przejdź do pozycji **Grupy zasobów** i wybierz grupę zasobów aplikacji logiki. Wybierz pozycję **Usuń grupę zasobów**. Aby potwierdzić, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

![„Grupy zasobów” > „Przegląd” > „Usuń grupę zasobów”](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono pierwszą aplikację logiki, która sprawdza aktualizacje kanału informacyjnego RSS według określonego harmonogramu (co minutę) i jeśli istnieją aktualizacje, wykonuje akcję (wysyła wiadomość e-mail). Aby dowiedzieć się więcej, kontynuuj pracę z tym samouczkiem nad tworzeniem bardziej zaawansowanych przepływów pracy opartych na harmonogramie:

> [!div class="nextstepaction"]
> [Sprawdzanie ruchu za pomocą aplikacji logiki opartej na harmonogramie](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)