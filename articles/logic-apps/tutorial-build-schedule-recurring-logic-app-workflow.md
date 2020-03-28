---
title: Tworzenie zautomatyzowanych przepływów pracy opartych na harmonogramie
description: Samouczek — tworzenie opartego na harmonogramie, cyklicznego i zautomatyzowanego przepływu pracy przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 17802228c8f08e3c8f1533296e2d39080f6f8b7a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75456626"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Samouczek: Tworzenie zautomatyzowanych, opartych na harmonogramie, cyklicznych przepływów pracy przy użyciu aplikacji Azure Logic Apps

W tym samouczku pokazano, jak utworzyć [aplikację logiki](../logic-apps/logic-apps-overview.md) i zautomatyzować cykliczny przepływ pracy, który działa zgodnie z harmonogramem. W szczególności w tym przykładzie logiki aplikacja działa co dzień powszedni rano i sprawdza czas podróży, w tym ruchu, między dwoma miejscami. Jeśli ten czas przekracza określony limit, aplikacja logiki wysyła wiadomość e-mail z informacją, ile zajmie podróż z uwzględnieniem dodatkowego czasu.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenia pustej aplikacji logiki.
> * Dodaj wyzwalacz cyklu, który określa harmonogram aplikacji logiki.
> * Dodaj akcję Mapy Bing, która pobiera czas podróży dla trasy.
> * Dodaj akcję, która tworzy zmienną, konwertuje czas podróży z sekund na minuty i przechowuje, które powodują zmienną.
> * Dodawanie warunku, który porównuje czas podróży z określonym limitem.
> * Dodaj akcję, która wysyła wiadomość e-mail, jeśli czas podróży przekracza limit.

Po ukończeniu aplikacja logiki będzie ogólnie wyglądać jak ten przepływ pracy:

![Omówienie przepływu pracy aplikacji logiki wysokiego poziomu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu uzyskania bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.

* Konto e-mail od dostawcy poczty e-mail obsługiwanego przez aplikacje logiki, takie jak Office 365 Outlook, Outlook.com lub Gmail. W przypadku innych dostawców [przejrzyj tę listę łączników](https://docs.microsoft.com/connectors/). Ten przewodnik Szybki start korzysta z konta programu Outlook usługi Office 365. Jeśli używasz innego konta e-mail, ogólne kroki pozostają takie same, ale interfejs użytkownika może się nieznacznie różnić.

* Aby pobrać czas podróży dla danej trasy, potrzebny jest klucz dostępu dla interfejsu API usługi Mapy Bing. Aby pobrać ten klucz, postępuj zgodnie z instrukcjami [uzyskiwania klucza usługi Mapy Bing](https://docs.microsoft.com/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. Z głównego menu platformy Azure wybierz pozycję **Utwórz aplikację** > **logiki****integracji** > zasobów .

   ![Tworzenie zasobu aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. W obszarze **Tworzenie aplikacji logiki** wprowadź następujące informacje na temat aplikacji logiki. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

   ![Podaj informacje o aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | LA-TravelTime | Nazwa aplikacji logiki, która może zawierać tylko litery,`-`cyfry,`_`łączniki ( ),`(` `)`podkreślenia`.`( ), nawiasy ( , ), i kropki ( ). W tym przykładzie użyto "LA-TravelTime". |
   | **Subskrypcja** | <*twoja nazwa subskrypcji platformy Azure*> | Twoja nazwa subskrypcji platformy Azure |
   | **Grupa zasobów** | LA-TravelTime-RG | Nazwa grupy [zasobów platformy Azure](../azure-resource-manager/management/overview.md), która jest używana do organizowania powiązanych zasobów. W tym przykładzie użyto "LA-TravelTime-RG". |
   | **Lokalizacja** | Zachodnie stany USA | TRegion, w którym mają być przechowywane informacje o aplikacji logiki. W tym przykładzie użyto "Zachodnie stany USA". |
   | **Analiza dzienników** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. |
   ||||

1. Po wdrożeniu aplikacji przez platformę Azure na pasku narzędzi platformy Azure wybierz pozycję **Powiadomienia** > **Przejdź do zasobu** dla wdrożonej aplikacji logiki.

   ![Przejdź do nowego zasobu aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   Możesz też znaleźć i wybrać aplikację logiki, wpisując nazwę w polu wyszukiwania.

   Projektant aplikacji logiki otwiera i pokazuje stronę z wprowadzeniem wideo i często używanych wyzwalaczy i wzorców aplikacji logiki. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Wybieranie pustego szablonu aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Następnie dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts)cyklu , który jest uruchamiany na podstawie określonego harmonogramu. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku przez nowe dane. Aby uzyskać więcej informacji, zobacz [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Dodawanie wyzwalacza cyklu

1. W projektancie aplikacji logiki w polu wyszukiwania wprowadź "cykl" jako filtr. Z listy **Wyzwalacze** wybierz wyzwalacz **cyklu.**

   ![Dodaj wyzwalacz "Cykl"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. W **kształcie** **Cykl** wybierz przycisk **elipsy** (**...**), a następnie wybierz pozycję Zmień nazwę . Zmień nazwę wyzwalacza na następujący opis: `Check travel time every weekday morning`

   ![Zmienianie nazwy opisu wyzwalacza cyklu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Wewnątrz wyzwalacza zmień te właściwości.

   ![Zmienianie interwału i częstotliwości wyzwalacza cyklu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Interwał** | Tak | 1 | Liczba interwałów do odczekania między sprawdzaniami |
   | **Częstotliwości** | Tak | Tydzień | Jednostka czasu cyklu |
   |||||

1. W obszarze **Interwał** i **częstotliwość**otwórz listę **Dodaj nowy parametr** i wybierz te właściwości, które chcesz dodać do wyzwalacza.

   * **W tych dniach**
   * **W tych godzinach**
   * **W tych minutach**

   ![Dodawanie właściwości wyzwalacza cyklu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Teraz ustaw wartości dla dodatkowych właściwości, jak pokazano i opisane w tym miejscu.

   ![Podawanie szczegółów harmonogramu i cyklu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **W tych dniach** | poniedziałek,wtorek,środa,czwartek,piątek | Dostępne tylko wtedy, gdy w polu **Częstotliwość** wybrano pozycję Tydzień |
   | **W tych godzinach** | 7,8,9 | Dostępne tylko wtedy, gdy w polu **Częstotliwość** wybrano pozycję Tydzień lub Dzień. Wybierz, o jakich godzinach ma być uruchamiany cykl. W tym przykładzie jest uruchamiany o 7, 8 i 9. |
   | **W tych minutach** | 0,15,30,45 | Dostępne tylko wtedy, gdy w polu **Częstotliwość** wybrano pozycję Tydzień lub Dzień. Wybierz, w których minutach dnia ma być uruchamiany cykl. W tym przykładzie jest uruchamiany co 15 minut, rozpoczynając od pełnej godziny. |
   ||||

   Ten wyzwalacz jest uruchamiany codziennie, co 15 minut, po raz pierwszy o 7:00 i po raz ostatni o 9:45. W oknie **Podgląd** wyświetlany jest harmonogram cyklu. Aby uzyskać więcej informacji, zobacz [Schedule tasks and workflows that run regularly (Planowanie regularnie uruchamianych zadań i przepływów pracy)](../connectors/connectors-native-recurrence.md) i [Workflow actions and triggers (Akcje i wyzwalacze przepływu pracy)](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Aby na razie ukryć szczegóły wyzwalacza, kliknij pasek tytułu kształtu.

   ![Zwinięty kształt w celu ukrycia szczegółów](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Twoja aplikacja logiki jest już aktywna, ale nie wykonuje żadnych akcji — jest tylko cyklicznie uruchamiana. Dodaj więc akcję reagującą na aktywowanie wyzwalacza.

## <a name="get-the-travel-time-for-a-route"></a>Pobieranie czasu podróży dla trasy

Po utworzeniu wyzwalacza możesz dodać [akcję](../logic-apps/logic-apps-overview.md#logic-app-concepts), która umożliwi pobranie czasu podróży pomiędzy dwoma miejscami. Usługa Logic Apps oferuje łącznik dla interfejsu API usługi Mapy Bing, dzięki czemu można łatwo uzyskać te informacje. Przed rozpoczęciem tego zadania upewnij się, że masz klucz interfejsu API usługi Mapy Bing, zgodnie z wymaganiami wstępnymi opisanymi w tym samouczku.

1. W projektancie aplikacji logiki w obszarze wyzwalacz wybierz pozycję **Nowy krok**.

1. W obszarze **Wybierz akcję**wybierz pozycję **Standardowy**. W polu wyszukiwania wpisz "mapy bing" jako filtr i wybierz akcję **Pobierz trasę.**

   ![Wybierz akcję "Pobierz trasę"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Jeśli nie masz połączenia z usługą Mapy Bing, zostanie wyświetlony monit o utworzenie połączenia. Podaj te szczegóły połączenia i wybierz pozycję **Utwórz**.

   ![Tworzenie połączenia z interfejsem API map Bing](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa połączenia** | Tak | BingMapsConnection | Podaj nazwę połączenia. W tym przykładzie użyto "BingMapsConnection". |
   | **Klucz INTERFEJSU API** | Tak | <*twój-Bing-Mapy-klucz*> | Wprowadź uzyskany wcześniej klucz usługi Mapy Bing. Jeśli nie masz klucza usługi Mapy Bing, dowiedz się [jak uzyskać klucz](https://msdn.microsoft.com/library/ff428642.aspx). |
   |||||

1. Zmień nazwę akcji na następujący opis: `Get route and travel time with traffic`

1. Wewnątrz akcji otwórz **listę Dodaj nowy parametr**i wybierz te właściwości, aby dodać je do akcji.

   * **Optymalizacji**
   * **Jednostka odległości**
   * **Tryb podróży**

   ![Dodaj właściwości do akcji "Pobierz trasę"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Teraz ustaw wartości właściwości akcji, jak pokazano i opisane w tym miejscu.

   ![Podaj szczegółowe informacje o akcji "Uzyskaj trasę"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Punkt nawigacyjny 1** | Tak | <*lokalizacja startu*> | Początek trasy |
   | **Punkt nawigacyjny 2** | Tak | <*lokalizacja końcowa*> | Koniec trasy |
   | **Optymalizacji** | Nie | timeWithTraffic | Parametr używany do optymalizowania trasy, na przykład odległość, czas podróży z uwzględnieniem aktualnego natężenia ruchu i tak dalej. Wybierz parametr "timeWithTraffic". |
   | **Jednostka odległości** | Nie | <*twoje preferencje*> | Jednostka odległości trasy. W tym przykładzie użyto "Mile" jako jednostki. |
   | **Tryb podróży** | Nie | Jazda samochodem | Tryb podróży dla trasy. Wybierz tryb "Jazda". |
   ||||

   Aby uzyskać więcej informacji na temat tych parametrów, zobacz [Calculate route (Obliczanie trasy)](https://docs.microsoft.com/bingmaps/rest-services/routes/calculate-a-route).

1. Zapisz aplikację logiki.

Następnie należy utworzyć zmienną, która umożliwia przekonwertowanie aktualnego czasu podróży i zapisanie go w minutach, a nie sekundach. Dzięki temu można uniknąć powtarzania konwersji i łatwiej używać tej wartości w kolejnych krokach. 

## <a name="create-a-variable-to-store-travel-time"></a>Tworzenie zmiennej do przechowywania czasu podróży

Czasami można uruchomić operacje na danych w przepływie pracy, a następnie użyć wyników w późniejszych akcjach. Aby zapisać te wyniki i łatwiej z nich później korzystać lub się do nich odwoływać, można utworzyć zmienne do przechowywania tych wyników po ich przetworzeniu. Zmienne można utworzyć tylko na najwyższym poziomie w aplikacji logiki.

Domyślnie poprzednia akcja **Pobierz trasę** zwraca bieżący czas podróży z ruchem w sekundach z właściwości **Ruch czasowy podróży.** Dzięki przekonwertowaniu tej wartości i zapisaniu jej w minutach łatwiej jest użyć jej ponownie później bez konieczności ponownego konwertowania.

1. W obszarze Akcja **Pobierz trasę** wybierz pozycję **Nowy krok**.

1. W obszarze **Wybierz akcję**wybierz pozycję **Wbudowane**. W polu wyszukiwania wprowadź "zmienne" i wybierz akcję **Inicjuj zmienną.**

   ![Wybierz akcję "Inicjuj zmienną"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Zmień nazwę akcji na następujący opis: `Create variable to store travel time`

1. Wprowadź szczegóły zmiennej zgodnie z następującym opisem:

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa** | Tak | travelTime | Nazwa zmiennej. W tym przykładzie użyto "travelTime". |
   | **Typ** | Tak | Liczba całkowita | Typ danych dla zmiennej |
   | **Wartość** | Nie| Wyrażenie, które konwertuje aktualny czas podróży z sekund na minuty (zobacz instrukcje pod tą tabelą). | Początkowa wartość zmiennej |
   ||||

   1. Aby utworzyć wyrażenie właściwości **Wartość,** kliknij wewnątrz pola, aby wyświetlić dynamiczną listę zawartości. W razie potrzeby poszerz okno przeglądarki, aż lista będzie widoczna. Na dynamicznej liście zawartości wybierz pozycję **Wyrażenie**.

      ![Podaj informacje dotyczące akcji "Inicjuj zmienną"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Po kliknięciu wewnątrz niektórych pól edycji pojawi się dynamiczna lista zawartości. Ta lista zawiera wszystkie właściwości z poprzednich akcji, których można użyć jako danych wejściowych w przepływie pracy. Dynamiczna lista zawartości zawiera edytor wyrażeń, w którym można wybrać funkcje do uruchamiania operacji. Edytor wyrażeń jest wyświetlany tylko na liście zawartości dynamicznej.

   1. W edytorze wyrażeń wprowadź wyrażenie: `div(,60)`

      ![Wprowadź wyrażenie: „div(,60)”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Umieść kursor wewnątrz wyrażenia między lewym nawiasem (**(**) a przecinkiem (**,**). 
   wybierz **opcję Zawartość dynamiczna**.

      ![Kursor pozycji, wybierz "Zawartość dynamiczna"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Na liście zawartości dynamicznej wybierz pole **Czas podróży —natężenie ruchu**.

      ![Wybierz właściwość "Ruch czas trwania podróży"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Po rozpoznaniu wartości właściwości wewnątrz wyrażenia wybierz przycisk **OK**.

      ![Aby zakończyć tworzenie wyrażenia, wybierz "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Właściwość **Value** jest teraz wyświetlana w następujący sposób:

      ![Właściwość "Wartość" pojawia się z rozwiązanym wyrażeniem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Zapisz aplikację logiki.

Następnie dodaj warunek, który sprawdzi, czy aktualny czas podróży jest dłuży niż określony limit.

## <a name="compare-the-travel-time-with-limit"></a>Porównaj czas podróży z limitem

1. W poprzedniej akcji wybierz pozycję **Nowy krok**.

1. W obszarze **Wybierz akcję**wybierz pozycję **Wbudowane**. W polu wyszukiwania wpisz "condition" jako filtr. Z listy akcje wybierz akcję **Warunek.**

   ![Wybierz akcję "Warunek"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Zmień nazwę warunku na następujący opis: `If travel time exceeds limit`

1. Skompiluj warunek, który sprawdza, czy wartość właściwości **travelTime** przekracza określony limit, jak opisano i pokazano poniżej:

   1. W warunkach kliknij wewnątrz pola **Wybierz wartość** po lewej stronie warunku.

   1. Z wyświetlona dynamiczna lista zawartości w obszarze **Zmienne**wybierz właściwość **travelTime.**

      ![Zbuduj lewą stronę warunku](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. W środkowym polu porównania wybierz **jest większy niż** operator.

   1. W polu **Wybierz wartość** po prawej stronie warunku wprowadź ten limit:`15`

      Po zakończeniu warunek wygląda następująco:

      ![Gotowy stan, aby sprawdzić czas podróży](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Zapisz aplikację logiki.

Następnie dodaj akcję do uruchomienia, gdy czas podróży przekroczy limit.

## <a name="send-email-when-limit-exceeded"></a>Wysyłanie wiadomości e-mail po przekroczeniu limitu

Teraz dodaj akcję, która wysyła do Ciebie wiadomość e-mail, jeśli czas podróży przekracza limit. Ta wiadomość e-mail zawiera aktualny czas podróży oraz dodatkowy czas niezbędny do pokonania określonej trasy.

1. W warunku **Jeśli prawdziwa** gałąź wybierz pozycję Dodaj **akcję**.

1. W obszarze **Wybierz akcję**wybierz pozycję **Standardowy**. W polu wyszukiwania wpisz "wyślij wiadomość e-mail". Lista zwraca wiele wyników, więc najpierw wybierz łącznik wiadomości e-mail, który chcesz, na przykład:

   ![Wybierz łącznik wiadomości e-mail, który chcesz](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * W przypadku kont służbowych platformy Azure wybierz pozycję **Office 365 Outlook**.
   * W przypadku osobistych kont Microsoft wybierz pozycję **Outlook.com**.

1. Gdy pojawią się akcje łącznika, wybierz opcję "Wyślij akcję e-mail", której chcesz użyć, na przykład:

   ![Wybieranie akcji „Wyślij wiadomość e-mail”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Jeśli nie masz jeszcze połączenia, pojawi się monit o zalogowanie się do swojego konta poczty e-mail.

   Usługa Logic Apps utworzy połączenie z kontem e-mail.

1. Zmień nazwę akcji na następujący opis: `Send email with travel time`

1. W polu **Do** wprowadź adres e-mail adresata. Do celów testowych możesz użyć własnego adresu e-mail.

1. W polu **Temat** określ temat wiadomości e-mail i dodaj zmienną **travelTime**.

   1. Wprowadź tekst `Current travel time (minutes):` ze spacją końcową. 

   1. Na dynamicznej liście zawartości w obszarze **Zmienne**wybierz pozycję **Zobacz więcej**.

      ![Znajdź zmienną "travelTime"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. Po **podróżyCzas** pojawia się w **obszarze Zmienne**, wybierz **travelTime**.

      ![Wprowadzanie tekstu tematu i wyrażenia zwracającego czas podróży](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. W polu **Treść** określ treść wiadomości e-mail.

   1. Wprowadź tekst `Add extra travel time (minutes):` ze spacją końcową.

   1. Na dynamicznej liście zawartości wybierz pozycję **Wyrażenie**.

      ![Tworzenie wyrażenia dla treści wiadomości e-mail](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. W edytorze wyrażeń wprowadź następujące wyrażenie, aby obliczyć, o ile minut przekroczony został limit: ```sub(,15)```

      ![Wprowadzanie wyrażenia w celu obliczenia dodatkowego czasu podróży w minutach](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Umieść kursor wewnątrz wyrażenia między lewym nawiasem (**(**) a przecinkiem (**,**). Wybierz **opcję Zawartość dynamiczna**.

      ![Kontynuacja tworzenia wyrażenia do obliczania dodatkowego czasu podróży w minutach](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. W obszarze **Zmienne** wybierz pozycję **travelTime**.

      ![Wybierz właściwość "travelTime", która ma być używana w wyrażeniu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Po rozpoznaniu właściwości wewnątrz wyrażenia wybierz przycisk **OK**.

      ![Po rozwiązaniu właściwości "Body" wybierz "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      **Właściwość Body** jest teraz wyświetlana w następujący sposób:

      ![Rozwiązana właściwość "Treść" w wyrażeniu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Zapisz aplikację logiki.

Następnie przetestuj aplikację logiki, która powinna wyglądać następująco:

![Ukończony przykładowy przepływ pracy aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

* Jeśli bieżący czas podróży pozostaje poniżej limitu, aplikacja logiki nie robi nic innego i czeka lub następny interwał przed sprawdzeniem ponownie. 

* Jeśli aktualny czas podróży przekracza limit, otrzymasz wiadomość e-mail z bieżącym czasem podróży i liczbą minut powyżej limitu. Oto przykładowa wiadomość e-mail wysłana przez aplikację logiki:

![Przykładowa wysłana wiadomość e-mail z czasem podróży](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci. Filtr wiadomości-śmieci Twojej poczty e-mail może przekierowywać tego rodzaju wiadomości. W przeciwnym razie, jeśli nie masz pewności, czy aplikacja logiki została poprawnie uruchomiona, zobacz [Troubleshoot your logic app (Rozwiązywania problemów z aplikacją logiki)](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulacje, udało Ci się utworzyć i uruchomić cykliczną aplikację logiki z użyciem harmonogramu. 

Aby utworzyć inne aplikacje logiki korzystające z wyzwalacza **cyklu,** zapoznaj się z tymi szablonami, które są dostępne po utworzeniu aplikacji logiki:

* Otrzymywanie codziennych przypomnień pocztą e-mail.
* Usuwanie starszych obiektów blob na platformie Azure.
* Dodawanie komunikatu do kolejki usługi Azure Storage.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już przykładowej aplikacji logiki, usuń grupę zasobów zawierającą aplikację logiki i powiązane zasoby. 

1. W menu głównym platformy Azure przejdź do pozycji **Grupy zasobów** i wybierz grupę zasobów aplikacji logiki.

1. W menu grupy zasobów wybierz polecenie **Przegląd** > **Usuń grupę zasobów**. 

   ![„Omówienie” > „Usuń grupę zasobów”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Wprowadź nazwę grupy zasobów jako potwierdzenie i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem utworzono aplikację logiki, która sprawdza natężenie ruchu na podstawie określonego harmonogramu (codziennie rano) i podejmuje działania (wysyła wiadomości e-mail), gdy czas podróży przekracza określony limit. Możesz teraz dowiedzieć się, w jaki sposób utworzyć aplikację logiki, która wysyła żądania listy adresowej do zatwierdzenia poprzez zintegrowanie usług platformy Azure, usług firmy Microsoft i innych aplikacji SaaS.

> [!div class="nextstepaction"]
> [Manage mailing list requests (Zarządzanie żądaniami listy adresowej)](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
