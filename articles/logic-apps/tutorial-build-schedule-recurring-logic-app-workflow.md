---
title: Tworzenie zautomatyzowanych przepływów pracy z użyciem harmonogramu — Azure Logic Apps | Microsoft Docs
description: 'Samouczek: jak utworzyć cykliczny, zautomatyzowany przepływ pracy z użyciem harmonogramu w usłudze Azure Logic Apps'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: ebc6388f1ebc7546ffda07095ead50797bde4e8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60507399"
---
# <a name="check-traffic-on-a-schedule-with-azure-logic-apps"></a>Cykliczne sprawdzanie ruchu przy użyciu usługi Azure Logic Apps

Usługa Azure Logic Apps umożliwia zautomatyzowanie przepływów pracy uruchamianych na podstawie harmonogramu. Ten samouczek pokazuje, jak utworzyć [aplikację logiki](../logic-apps/logic-apps-overview.md) z wyzwalaczem harmonogramu, który jest uruchamiany codziennie rano i sprawdza, ile czasu zajmie podróż pomiędzy dwoma miejscami, z uwzględnieniem natężenia ruchu. Jeśli ten czas przekracza określony limit, aplikacja logiki wysyła wiadomość e-mail z informacją, ile zajmie podróż z uwzględnieniem dodatkowego czasu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenia pustej aplikacji logiki. 
> * Dodawanie wyzwalacza harmonogramu dla aplikacji logiki.
> * Dodawanie akcji, która pobiera czas podróży dla danej trasy.
> * Dodawanie akcji, która tworzy zmienną, konwertuje czas podróży z sekund na minuty i zapisuje wynik w zmiennej.
> * Dodawanie warunku, który porównuje czas podróży z określonym limitem.
> * Dodawanie akcji, która wysyła wiadomość e-mail, jeśli czas podróży przekracza limit.

Po ukończeniu aplikacja logiki będzie ogólnie wyglądać jak ten przepływ pracy:

![Ogólna struktura aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto e-mail od dostawcy obsługiwanego przez usługę Logic Apps, na przykład Office 365 Outlook, Outlook.com lub Gmail. W przypadku innych dostawców [przejrzyj tę listę łączników](https://docs.microsoft.com/connectors/). W tym przewodniku Szybki start jest używane konto usługi Outlook.com. Jeśli korzystasz z innego konta e-mail, ogólne kroki pozostają takie same, ale Twój interfejs użytkownika może wyglądać trochę inaczej.

* Aby pobrać czas podróży dla danej trasy, potrzebny jest klucz dostępu dla interfejsu API usługi Mapy Bing. Aby pobrać ten klucz, postępuj zgodnie z instrukcjami <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">uzyskiwania klucza usługi Mapy Bing</a>. 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do <a href="https://portal.azure.com" target="_blank">witryny Azure Portal</a> przy użyciu poświadczeń konta Azure.

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. Z głównego menu platformy Azure wybierz pozycję **Utwórz zasób** > **Integracja dla przedsiębiorstw** > **Aplikacja logiki**.

   ![Tworzenie aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app.png)

2. W obszarze **Tworzenie aplikacji logiki** wprowadź następujące informacje na temat aplikacji logiki. Gdy skończysz, wybierz pozycję **Przypnij do pulpitu nawigacyjnego** > **Utwórz**.

   ![Podawanie informacji na temat aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Ustawienie | Wartość | Opis | 
   | ------- | ----- | ----------- | 
   | **Nazwa** | LA-TravelTime | Nazwa aplikacji logiki | 
   | **Subskrypcja** | <*your-Azure-subscription-name*> | Nazwa subskrypcji platformy Azure | 
   | **Grupa zasobów** | LA-TravelTime-RG | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) używanej do organizowania powiązanych zasobów | 
   | **Lokalizacja** | Wschodnie stany USA 2 | Region, w którym będą przechowywane informacje na temat aplikacji logiki | 
   | **Log Analytics** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. | 
   |||| 

3. Po wdrożeniu aplikacji na platformie Azure zostanie otwarty Projektant aplikacji usługi Logic Apps ze stroną zawierającą wprowadzający film wideo i szablony typowych wzorców aplikacji logiki. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Wybieranie szablonu pustej aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/choose-logic-app-template.png)

Następnie dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts) cykliczny, który będzie uruchamiany zgodnie z określonym harmonogramem. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku przez nowe dane. Aby uzyskać więcej informacji, zobacz [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-scheduler-trigger"></a>Dodawanie wyzwalacza harmonogramu

1. W Projektancie wprowadź ciąg „cyklicznie” w polu wyszukiwania. Wybierz ten wyzwalacz: **Harmonogram — cyklicznie**

   ![Znajdywanie i dodawanie wyzwalacza „Harmonogram — cyklicznie”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

2. Na kształcie **Cyklicznie** wybierz przycisk **wielokropka** (**...**), a następnie wybierz pozycję **Zmień nazwę**. Zmień nazwę wyzwalacza na następujący opis: ```Check travel time every weekday morning```

   ![Zmiana nazwy wyzwalacza](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

3. Wewnątrz wyzwalacza wybierz pozycję **Pokaż opcje zaawansowane**.

4. Podaj następujące informacje dotyczące harmonogramu i cyklu wyzwalacza:

   ![Podawanie szczegółów harmonogramu i cyklu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/schedule-recurrence-trigger-settings.png)

   | Ustawienie | Wartość | Opis | 
   | ------- | ----- | ----------- | 
   | **Interwał** | 1 | Liczba interwałów do odczekania między sprawdzaniami | 
   | **Częstotliwość** | Tydzień | Jednostka czasu cyklu | 
   | **Strefa czasowa** | Brak | Ma zastosowanie tylko, jeśli określisz godzinę rozpoczęcia. Umożliwia określenie innej strefy czasowej niż lokalna. | 
   | **Godzina rozpoczęcia** | Brak | Umożliwia opóźnienie cyklu do określonej daty i godziny. Aby uzyskać więcej informacji, zobacz [Schedule tasks and workflows that run regularly (Planowanie regularnie uruchamianych zadań i przepływów pracy)](../connectors/connectors-native-recurrence.md). | 
   | **W tych dniach** | poniedziałek,wtorek,środa,czwartek,piątek | Dostępne tylko wtedy, gdy w polu **Częstotliwość** wybrano pozycję Tydzień | 
   | **W tych godzinach** | 7,8,9 | Dostępne tylko wtedy, gdy w polu **Częstotliwość** wybrano pozycję Tydzień lub Dzień. Wybierz, o jakich godzinach ma być uruchamiany cykl. W tym przykładzie jest uruchamiany o 7, 8 i 9. | 
   | **W tych minutach** | 0,15,30,45 | Dostępne tylko wtedy, gdy w polu **Częstotliwość** wybrano pozycję Tydzień lub Dzień. Wybierz, w których minutach dnia ma być uruchamiany cykl. W tym przykładzie jest uruchamiany co 15 minut, rozpoczynając od pełnej godziny. | 
   ||||

   Ten wyzwalacz jest uruchamiany codziennie, co 15 minut, po raz pierwszy o 7:00 i po raz ostatni o 9:45. 
   W oknie **Podgląd** wyświetlany jest harmonogram cyklu. 
   Aby uzyskać więcej informacji, zobacz [Schedule tasks and workflows that run regularly (Planowanie regularnie uruchamianych zadań i przepływów pracy)](../connectors/connectors-native-recurrence.md) i [Workflow actions and triggers (Akcje i wyzwalacze przepływu pracy)](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

5. Aby na razie ukryć szczegóły wyzwalacza, kliknij pasek tytułu kształtu.

   ![Zwinięty kształt w celu ukrycia szczegółów](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

6. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. 

Twoja aplikacja logiki jest już aktywna, ale nie wykonuje żadnych akcji — jest tylko cyklicznie uruchamiana. Dodaj więc akcję reagującą na aktywowanie wyzwalacza.

## <a name="get-the-travel-time-for-a-route"></a>Pobieranie czasu podróży dla trasy

Po utworzeniu wyzwalacza możesz dodać [akcję](../logic-apps/logic-apps-overview.md#logic-app-concepts), która umożliwi pobranie czasu podróży pomiędzy dwoma miejscami. Usługa Logic Apps oferuje łącznik dla interfejsu API usługi Mapy Bing, dzięki czemu można łatwo uzyskać te informacje. Przed rozpoczęciem tego zadania upewnij się, że masz klucz interfejsu API usługi Mapy Bing, zgodnie z wymaganiami wstępnymi opisanymi w tym samouczku.

1. W Projektancie aplikacji logiki w obszarze wyzwalacza wybierz kolejno pozycje **+ Nowy krok** > **Dodaj akcję**.

2. Wyszukaj "mapy" i wybierz następującą akcję: **Mapy Bing — Pobierz trasę**

3. Jeśli nie masz połączenia z usługą Mapy Bing, zostanie wyświetlony monit o utworzenie połączenia. Podaj następujące szczegóły połączenia, a następnie wybierz pozycję **Utwórz**.

   ![Wybieranie akcji „Mapy Bing — Pobierz trasę”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Ustawienie | Wartość | Opis |
   | ------- | ----- | ----------- |
   | **Nazwa połączenia** | BingMapsConnection | Podaj nazwę połączenia. | 
   | **Klucz interfejsu API** | <*klucz_usługi_Mapy_Bing*> | Wprowadź uzyskany wcześniej klucz usługi Mapy Bing. Jeśli nie masz klucza usługi Mapy Bing, dowiedz się <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">jak uzyskać klucz</a>. | 
   | | | |  

4. Zmień nazwę akcji na następujący opis: ```Get route and travel time with traffic```

5. Wprowadź szczegóły akcji **Pobierz trasę** zgodnie z poniższym opisem, na przykład:

   ![Wprowadzanie informacji dla akcji „Mapy Bing — Pobierz trasę”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Ustawienie | Wartość | Opis |
   | ------- | ----- | ----------- |
   | **Punkt nawigacyjny 1** | <*lokalizacja_początkowa*> | Początek trasy | 
   | **Punkt nawigacyjny 2** | <*lokalizacja_końcowa*> | Koniec trasy | 
   | **Unikaj** | Brak | Wszelkie elementy trasy, których należy unikać, na przykład autostrady, drogi płatne i tym podobne | 
   | **Optymalizacja** | timeWithTraffic | Parametr używany do optymalizowania trasy, na przykład odległość, czas podróży z uwzględnieniem aktualnego natężenia ruchu i tak dalej. Wybierz parametr: „timeWithTraffic” | 
   | **Jednostka odległości** | <*według_preferencji*> | Jednostka odległości trasy. W tym artykule używaną jednostką: "Mile"  | 
   | **Tryb podróży** | Jazda samochodem | Tryb podróży dla trasy. Wybierz tryb, w tym: "Samochodem" | 
   | **Transport publiczny — data i godzina** | Brak | Dotyczy tylko trybu „Transport publiczny” | 
   | **Rodzaj daty i godziny** | Brak | Dotyczy tylko trybu „Transport publiczny” | 
   |||| 

   Aby uzyskać więcej informacji na temat tych parametrów, zobacz [Calculate route (Obliczanie trasy)](https://msdn.microsoft.com/library/ff701717.aspx).

6. Zapisz aplikację logiki.

Następnie należy utworzyć zmienną, która umożliwia przekonwertowanie aktualnego czasu podróży i zapisanie go w minutach, a nie sekundach. Dzięki temu można uniknąć powtarzania konwersji i łatwiej używać tej wartości w kolejnych krokach. 

## <a name="create-variable-to-store-travel-time"></a>Tworzenie zmiennej do zapisania czasu podróży

Czasami przepływ pracy wymaga wykonania operacji na danych i użycia wyników w kolejnych akcjach. Aby zapisać te wyniki i łatwiej z nich później korzystać lub się do nich odwoływać, można utworzyć zmienne do przechowywania tych wyników po ich przetworzeniu. Zmienne można utworzyć tylko na najwyższym poziomie w aplikacji logiki.

Domyślnie poprzednia akcja **Pobierz trasę** zwraca aktualny czas podróży z uwzględnieniem natężenia ruchu w sekundach w polu **Czas podróży —natężenie ruchu**. Dzięki przekonwertowaniu tej wartości i zapisaniu jej w minutach łatwiej jest użyć jej ponownie później bez konieczności ponownego konwertowania.

1. W obszarze akcji **Pobierz trasę** akcji wybierz kolejno pozycje **+ Nowy krok** > **Dodaj akcję**.

2. Wyszukaj "zmienne" i wybierz następującą akcję: **Zmienne — inicjowane zmiennej**

   ![Wybieranie akcji „Zmienne - inicjowane zmiennej”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

3. Zmień nazwę akcji na następujący opis: ```Create variable to store travel time```

4. Wprowadź szczegóły zmiennej zgodnie z następującym opisem:

   | Ustawienie | Wartość | Opis | 
   | ------- | ----- | ----------- | 
   | **Nazwa** | travelTime | Nazwa zmiennej | 
   | **Typ** | Liczba całkowita | Typ danych dla zmiennej | 
   | **Wartość** | Wyrażenie, które konwertuje aktualny czas podróży z sekund na minuty (zobacz instrukcje pod tą tabelą). | Początkowa wartość zmiennej | 
   |||| 

   1. Aby utworzyć wyrażenie dla pola **Wartość**, kliknij wewnątrz pola, aby wyświetlić listę zawartości dynamicznej. 
   W razie potrzeby poszerz okno przeglądarki, aż lista będzie widoczna. 
   Na liście zawartości dynamicznej wybierz pozycję **Wyrażenie**. 

      ![Wprowadzanie informacji o akcji „Zmienne — inicjowane zmiennej”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Po kliknięciu wewnątrz niektórych pól edycji zostanie wyświetlona lista zawartości dynamicznej lub śródwierszowa lista parametrów. Ta lista zawiera wszystkie parametry z poprzednich akcji, których można użyć jako danych wejściowych w przepływie pracy. 
      Lista zawartości dynamicznej zawiera edytor wyrażeń, który umożliwia wybranie funkcji w celu wykonania operacji. 
      Edytor wyrażeń jest wyświetlany tylko na liście zawartości dynamicznej.

      Rodzaj wyświetlanej listy jest określany przez szerokość okna przeglądarki. 
      Jeśli okno przeglądarki jest szerokie, zostanie wyświetlona lista zawartości dynamicznej. 
      Jeśli okno przeglądarki jest wąskie, zostanie wyświetlona śródwierszowa lista parametrów pod polem edycji, na którym aktualnie jest ustawiony fokus.

   2. W edytorze wyrażeń wprowadź wyrażenie: ```div(,60)```

      ![Wprowadź wyrażenie: „div(,60)”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   3. Umieść kursor wewnątrz wyrażenia między lewym nawiasem (**(**) a przecinkiem (**,**). 
   Wybierz pozycję **Zawartość dynamiczna**.

      ![Umieszczanie kursora i wybieranie polecenia „Zawartość dynamiczna”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   4. Na liście zawartości dynamicznej wybierz pole **Czas podróży —natężenie ruchu**.

      ![Wybieranie pola „Czas podróży — natężenie ruchu”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   5. Po umieszczeniu pola wewnątrz wyrażenia wybierz pozycję **OK**.

      ![Wybieranie pozycji „OK”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      W polu **Wartość** będzie teraz widoczna następująca zawartość:

      ![Pole „Wartość” z wyrażeniem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

5. Zapisz aplikację logiki.

Następnie dodaj warunek, który sprawdzi, czy aktualny czas podróży jest dłuży niż określony limit.

## <a name="compare-travel-time-with-limit"></a>Porównywanie czasu podróży z limitem

1. W obszarze poprzedniej akcji wybierz kolejno pozycje **+ Nowy krok** > **Dodaj warunek**. 

2. Zmień nazwę warunku na następujący opis: ```If travel time exceeds limit```

3. Utwórz warunek, który sprawdzi, czy wartość **travelTime** przekracza określony limit, zgodnie z poniższym opisem:

   1. Wewnątrz warunku kliknij w polu **Wybierz wartość** po lewej stronie (szerokie okno przeglądarki) lub na górze (wąskie okno przeglądarki).

   2. Na liście zawartości dynamicznej lub liście parametrów wybierz pole **travelTime** w obszarze **Zmienne**.

   3. W polu porównania wybierz operator **większe niż**

   4. W polu **Wybierz wartość** z prawej strony (szerokie okno) lub u dołu (wąskie okno) wprowadź limit: ```15```

   Na przykład jeśli pracujesz w wąskim oknie, warunek jest tworzony następująco:

   ![Tworzenie warunku w wąskim oknie](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time-narrow.png)

4. Zapisz aplikację logiki.

Następnie dodaj akcję, która zostanie wykonana, jeśli czas podróży przekracza limit.

## <a name="send-email-when-limit-exceeded"></a>Wysyłanie wiadomości e-mail po przekroczeniu limitu

Teraz dodaj akcję, która wysyła do Ciebie wiadomość e-mail, jeśli czas podróży przekracza limit. Ta wiadomość e-mail zawiera aktualny czas podróży oraz dodatkowy czas niezbędny do pokonania określonej trasy. 

1. W gałęzi warunku **W przypadku wartości true** wybierz opcję **Dodaj akcję**.

2. Wyszukaj opcję „Wyślij wiadomość e-mail” i wybierz łącznik poczty e-mail oraz akcję wysyłania wiadomości e-mail, której chcesz użyć.

   ![Znajdowanie i wybieranie akcji „Wyślij wiadomość e-mail”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * W przypadku osobistych kont Microsoft wybierz pozycję **Outlook.com**. 
   * W przypadku kont służbowych platformy Azure wybierz pozycję **Office 365 Outlook**.

3. Jeśli nie masz jeszcze połączenia, pojawi się monit o zalogowanie się do swojego konta poczty e-mail.

   Usługa Logic Apps utworzy połączenie z kontem e-mail.

4. Zmień nazwę akcji na następujący opis: ```Send email with travel time```

5. W polu **Do** wprowadź adres e-mail adresata. Do celów testowych możesz użyć własnego adresu e-mail.

6. W polu **Temat** określ temat wiadomości e-mail i dodaj zmienną **travelTime**.

   1. Wprowadź tekst ```Current travel time (minutes):``` ze spacją końcową. 
   
   2. Na liście zawartości dynamicznej lub liście parametrów wybierz zmienną **travelTime** w obszarze **Zmienne**. 
   
      Na przykład jeśli okno przeglądarki jest wąskie:

      ![Wprowadzanie tekstu tematu i wyrażenia zwracającego czas podróży](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-subject-settings.png)

7. W polu **Treść** określ treść wiadomości e-mail. 

   1. Wprowadź tekst ```Add extra travel time (minutes):``` ze spacją końcową. 
   
   2. W razie potrzeby poszerz okno przeglądarki, aby wyświetlić listę zawartości dynamicznej. 
   Na liście zawartości dynamicznej wybierz pozycję **Wyrażenie**.

      ![Tworzenie wyrażenia dla treści wiadomości e-mail](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   3. W edytorze wyrażeń wprowadź następujące wyrażenie, aby obliczyć, o ile minut przekroczony został limit: ```sub(,15)```

      ![Wprowadzanie wyrażenia w celu obliczenia dodatkowego czasu podróży w minutach](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   4. Umieść kursor wewnątrz wyrażenia między lewym nawiasem (**(**) a przecinkiem (**,**). Wybierz pozycję **Zawartość dynamiczna**.

      ![Kontynuacja tworzenia wyrażenia do obliczania dodatkowego czasu podróży w minutach](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   5. W obszarze **Zmienne** wybierz pozycję **travelTime**.

      ![Wybieranie pola „travelTime” do użycia w wyrażeniu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   6. Po umieszczeniu pola wewnątrz wyrażenia wybierz pozycję **OK**.

      ![Pole „Treść” z wyrażeniem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      W polu **Treść** będzie teraz widoczna następująca zawartość:

      ![Pole „Treść” z wyrażeniem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

8. Zapisz aplikację logiki.

Następnie przetestuj aplikację logiki, która powinna wyglądać następująco:

![Ukończona aplikacja logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

Aby ręcznie uruchomić swoją aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Jeśli aktualny czas podróży będzie się mieścił w limicie, aplikacja logiki nie zrobi nic i będzie czekać, aż upłynie ustawiony czas przed ponownym sprawdzeniem.
Jeśli jednak aktualny czas podróży przekroczy limit, otrzymasz wiadomość e-mail z aktualnym czasem podróży i informacją, o ile przekracza on limit. Oto przykładowa wiadomość e-mail wysłana przez aplikację logiki:

![Wiadomości e-mail z czasem podróży](./media/tutorial-build-scheduled-recurring-logic-app-workflow/email-notification.png)

Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci. Filtr wiadomości-śmieci Twojej poczty e-mail może przekierowywać tego rodzaju wiadomości. W przeciwnym razie, jeśli nie masz pewności, czy aplikacja logiki została poprawnie uruchomiona, zobacz [Troubleshoot your logic app (Rozwiązywania problemów z aplikacją logiki)](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulacje, udało Ci się utworzyć i uruchomić cykliczną aplikację logiki z użyciem harmonogramu. 

Aby utworzyć inne aplikacje logiki, które używają wyzwalacza **Harmonogram — cyklicznie**, zapoznaj się z tymi szablonami, które będą dostępne po utworzeniu aplikacji logiki:

* Otrzymywanie codziennych przypomnień pocztą e-mail.
* Usuwanie starszych obiektów blob na platformie Azure.
* Dodawanie komunikatu do kolejki usługi Azure Storage.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów zawierająca aplikację logiki i powiązane zasoby nie będzie już potrzebna, usuń ją. W menu głównym platformy Azure przejdź do pozycji **Grupy zasobów** i wybierz grupę zasobów aplikacji logiki. Wybierz pozycję **Usuń grupę zasobów**. Aby potwierdzić, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

![„Omówienie” > „Usuń grupę zasobów”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

Podczas pracy z tym samouczkiem utworzono aplikację logiki, która sprawdza natężenie ruchu na podstawie określonego harmonogramu (codziennie rano) i podejmuje działania (wysyła wiadomości e-mail), gdy czas podróży przekracza określony limit. Możesz teraz dowiedzieć się, w jaki sposób utworzyć aplikację logiki, która wysyła żądania listy adresowej do zatwierdzenia poprzez zintegrowanie usług platformy Azure, usług firmy Microsoft i innych aplikacji SaaS.

> [!div class="nextstepaction"]
> [Manage mailing list requests (Zarządzanie żądaniami listy adresowej)](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
