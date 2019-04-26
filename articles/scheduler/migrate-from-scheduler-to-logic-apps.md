---
title: Migrowanie z usługi Azure Scheduler do usługi Azure Logic Apps
description: Dowiedz się, jak można zastąpić zadania w usłudze Azure Scheduler, która zostanie wycofana, Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/20/2018
ms.openlocfilehash: 25ed66fd75301475542dbac8e8a01670ee37563c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531687"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrowanie zadania usługi Azure Scheduler do usługi Azure Logic Apps

> [!IMPORTANT]
> Usługa Azure Logic Apps jest wymiana usługi Azure Scheduler, która zostanie wycofana. Aby zaplanować zadania, postępuj zgodnie z tym artykułem, aby zamiast przechodzenia do usługi Azure Logic Apps.

Ten artykuł pokazuje, jak można zaplanować jednorazowych lub cykliczne zadania przez tworzenie automatycznych przepływów pracy z usługą Azure Logic Apps, a nie z usługą Azure Scheduler. Po utworzeniu zaplanowane zadania dzięki usłudze Logic Apps można uzyskać następujące korzyści:

* Nie trzeba martwić się więcej na temat koncepcji *kolekcję zadań* ponieważ każda aplikacja logiki jest oddzielne zasoby platformy Azure.

* Za pomocą aplikacji logiki pojedynczego, można uruchamiać wiele jednorazowe zadania.

* Usługa Azure Logic Apps obsługuje strefę czasową i czasu letniego (DST).

Aby dowiedzieć się więcej, zobacz [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md) lub spróbuj utworzyć swoją pierwszą aplikację logiki, w tym przewodniku Szybki Start: [Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* Aby uruchomić aplikację logiki, wysyłania żądań HTTP, należy użyć narzędzia takie jak [aplikacja klasyczna narzędzia Postman](https://www.getpostman.com/apps).

## <a name="schedule-one-time-jobs"></a>Harmonogramów jednorazowych zadań

Możesz uruchomić wiele zadań jednorazowe, tworząc tylko jednego logiki aplikacji w języku. 

### <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. W [witryny Azure portal](https://portal.azure.com), tworzenie pustej aplikacji logiki w Projektancie aplikacji logiki. 

   Podstawowe kroki, postępuj zgodnie z [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. W polu wyszukiwania wprowadź "podczas żądania http" jako filtr. Z listy wyzwalaczy wybierz następujący wyzwalacz: **Po odebraniu żądania HTTP** 

   ![Dodawanie wyzwalacza "żądania"](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Dla wyzwalacza żądania można opcjonalnie podaj schematu JSON, pomaga w Projektancie aplikacji logiki, poznać strukturę dla danych wejściowych z żądania przychodzącego, która ułatwia dane wyjściowe można wybrać później w przepływie pracy.

   Aby określić schemat, wprowadź schemat w **schemat JSON treści żądania** polu, na przykład: 

   ![Schemat żądania](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Jeśli nie masz schematu, ale masz przykładowy ładunek w formacie JSON, można wygenerować schematu z tym ładunku.

   1. W wyzwalaczu żądania wybierz **Użyj przykładowego ładunku do wygenerowania schematu**.

   1. W obszarze **wpisz lub wklej przykładowy ładunek JSON**Podaj swoje przykładowy ładunek, a następnie wybierz **gotowe**, na przykład:

      ![Przykładowy ładunek](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. W obszarze wyzwalacza wybierz **następny krok**. 

1. W polu wyszukiwania wprowadź "opóźnienie do" jako filtr. W obszarze listy akcji wybierz następującą akcję: **Opóźnienie do**

   Ta akcja powoduje wstrzymanie przepływu pracy aplikacji logiki aż do określonej daty i godziny.

   ![Dodawanie akcji "Opóźnienie do"](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Wprowadź sygnaturę czasową dla, jeśli chcesz uruchomić przepływ pracy aplikacji logiki. 

   Po kliknięciu wewnątrz **sygnatura czasowa** , lista zawartości dynamicznej pojawi się okno, dzięki czemu można opcjonalnie wybierz dane wyjściowe z wyzwalacza.

   ![Podaj szczegóły "Opóźnienie do"](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Dodaj wszystkich potrzebnych działań, które chcesz uruchomić, wybierając z [~ ponad 200 łączników](../connectors/apis-list.md). 

   Na przykład można uwzględnić akcji HTTP, która wysyła żądanie do adresu URL lub akcje, które działają z kolejek magazynu, kolejek usługi Service Bus lub tematów usługi Service Bus: 

   ![Akcja HTTP](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Gdy skończysz, Zapisz aplikację logiki.

   ![Zapisywanie aplikacji logiki](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Po zapisaniu aplikacji logiki po raz pierwszy punkt końcowy adres URL dla wyzwalacza żądania aplikacja logiki pojawi się w **HTTP POST URL** pole. 
   Jeśli chcesz wywołać aplikację logiki i Wyślij dane wejściowe do przetworzenia aplikacji logiki, użyj tego adresu URL jako lokalizacji docelowej wywołania.

   ![Zapisz adres URL punktu końcowego wyzwalacza żądania](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Skopiuj i Zapisz ten adres URL punktu końcowego, co umożliwia wysyłanie później ręcznie żądanie, które uruchamia aplikację logiki. 

## <a name="start-a-one-time-job"></a>Uruchom zadanie jednorazowe

Aby ręcznie uruchomić lub wyzwolić zadanie jednorazowe, wysyłanie wywołań do adresu URL punktu końcowego dla aplikacji logiki wyzwalacza żądania. W tym wywołaniu Określ dane wejściowe lub ładunek do wysłania, który ma może opisany w wcześniej przez określenie schematu. 

Na przykład za pomocą aplikacji Postman, należy można utworzyć żądania POST z ustawieniami podobną do poniższego przykładu, a następnie wybierz **wysyłania** do utworzenia żądania.

| Metoda żądania | Adres URL | Treść | Nagłówki |
|----------------|-----|------|---------| 
| **POST** | <*endpoint-URL*> | **nieprzetworzone** <p>**JSON(application/json)** <p>W **pierwotne** wprowadź ładunek, w której chcesz wysłać w żądaniu. <p>**Uwaga**: To ustawienie automatycznie konfiguruje **nagłówki** wartości. | **Klucz**: Content-Type <br>**Wartość**: application/json
 |||| 

![Wyślij żądanie do ręcznego wyzwalania aplikacji logiki](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Po wysłaniu wywołanie, odpowiedź z aplikacji logiki jest wyświetlany w obszarze **pierwotne** polu na **treści** kartę. 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Jeśli chcesz anulować zadanie później, wybierz opcję **nagłówki** kartę. Znajdź i skopiuj **x-ms-przepływ pracy run-id** wartość nagłówka w odpowiedzi. 
>
> ![Odpowiedź](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Anuluj zadanie jednorazowe

W usłudze Logic Apps każde zadanie jednorazowe wykonuje jako aplikację logiki pojedynczego uruchomienia wystąpienia. Aby anulować zadanie jednorazowe, można użyć [anulowanie przebiegów przepływu pracy —](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) w interfejsie API REST aplikacji logiki. Po wysłaniu wywołanie wyzwalacza zapewniają [Identyfikator przebiegu przepływu pracy](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Planowanie zadań cyklicznych

### <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. W [witryny Azure portal](https://portal.azure.com), tworzenie pustej aplikacji logiki w Projektancie aplikacji logiki. 

   Podstawowe kroki, postępuj zgodnie z [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. W polu wyszukiwania wprowadź ciąg "cyklicznie" jako filtr. Z listy wyzwalaczy wybierz następujący wyzwalacz: **Cykl** 

   ![Dodawanie wyzwalacza "Cykl"](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Konfigurowanie harmonogramu bardziej zaawansowanych, jeśli chcesz.

   ![Zaawansowany harmonogram](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Aby uzyskać więcej informacji na temat zaawansowanych opcji planowania zobacz [tworzenia i wykonywania powtarzających się zadań i przepływów pracy z usługą Azure Logic Apps](../connectors/connectors-native-recurrence.md)

1. Dodaj inne akcje, wybierając z [ponad 200 łączników](../connectors/apis-list.md). W obszarze wyzwalacza wybierz **następny krok**. Znajdź i wybierz żądane akcje.

   Na przykład można uwzględnić akcji HTTP, która wysyła żądanie do adresu URL lub akcje, które działają z kolejek magazynu, kolejek usługi Service Bus lub tematów usługi Service Bus: 

   ![Akcja HTTP](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Gdy skończysz, Zapisz aplikację logiki.

   ![Zapisywanie aplikacji logiki](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Zaawansowane ustawienia

Poniżej przedstawiono inne sposoby, które można dostosować swoje zadania.

### <a name="retry-policy"></a>Zasady ponawiania

Aby kontrolować sposób, który próbuje ponownie uruchomić aplikację logiki w przypadku sporadycznych błędów akcję, można ustawić [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies) w ustawieniach każdej akcji, na przykład:

1. Otwórz akcję (**...** ) menu, a następnie wybierz **ustawienia**.

   ![Otwórz ustawienia akcji](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Wybierz żądane zasady ponawiania prób. Aby uzyskać więcej informacji na temat poszczególnych zasad, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Wybierz zasady ponawiania](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Obsługa wyjątków i błędów

W usłudze Azure Scheduler, jeśli domyślna akcja uruchomienie nie powiedzie się, możesz uruchomić akcję alternatywny, odnoszący się do warunku błędu. W usłudze Azure Logic Apps można również wykonać tego samego zadania.

1. W Projektancie aplikacji logiki powyżej akcji dotyczącej chcesz obsługiwać, przesuń kursor myszy nad strzałką znajdującą się między krokami, a następnie wybierz i **Dodaj gałąź równoległą**. 

   ![Dodaj gałąź równoległą](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Znajdź i wybierz akcję, którą chcesz uruchomić zamiast akcji alternatywnej.

   ![Dodawanie akcji równoległe](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Alternatywne akcję, Otwórz (**...** ) menu, a następnie wybierz **konfigurowanie uruchamiania**.

   ![Konfigurowanie uruchamiania po danej akcji](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Wyczyść pole **zakończy się pomyślnie** właściwości. Wybierz następujące właściwości: **nie powiodło się**, **jest pomijana**, i **przekroczyło limit czasu**

   ![Ustawianie właściwości "Uruchom po"](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Po zakończeniu wybierz pozycję **Gotowe**.

Aby dowiedzieć się więcej na temat obsługi wyjątków, zobacz [Obsługa błędów i wyjątków — właściwość RunAfter](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>Często zadawane pytania

<a name="retire-date"></a> 

**Q**: Gdy Trwa wycofywanie usługi Azure Scheduler <br>
**Odp.:** Usługa Azure Scheduler zaplanowano wycofanie na 30 września 2019 r.

**Q**: Co się stanie z moimi kolekcje zadań usługi Scheduler i zadania po wycofaniu usługi? <br>
**Odp.:** Wszystkie zadania i kolekcje zadań usługi Scheduler zostaną usunięte z systemu.

**Q**: Czy muszę wykonać kopię zapasową lub wykonywać inne zadania, przed migracją Moje zadania usługi Scheduler do usługi Logic Apps? <br>
**Odp.:** Najlepszym rozwiązaniem jest zawsze utworzyć kopię zapasową swoją pracę. Upewnij się, że aplikacje logiki, utworzone przez Ciebie działają zgodnie z oczekiwaniami przed usunięciem lub wyłączeniem zadań usługi Scheduler. 

**Q**: Czy istnieje narzędzie, które mogą pomóc mi migracji Moje zadania z harmonogramu do usługi Logic Apps? <br>
**Odp.:** Każde zadanie usługi Scheduler jest unikatowa, więc nie istnieje uniwersalne narzędzie. Jednak różne skryptów będzie można go dostosować do indywidualnych potrzeb. Aby zapewnić dostępność skryptu zajrzyj tu później.

**Q**: Gdzie uzyskać pomoc techniczną dla migracji Moje zadania usługi Scheduler? <br>
**Odp.:** Oto kilka sposobów, aby uzyskać pomoc techniczną: 

**Azure Portal**

Jeśli Twoja subskrypcja platformy Azure ma płatnego planu pomocy technicznej, można utworzyć żądanie pomocy technicznej w witrynie Azure portal. W przeciwnym razie można wybrać opcji pomocy technicznej w różnych.

1. Na [witryny Azure portal](https://portal.azure.com) menu głównego, wybierz opcję **Pomoc i obsługa techniczna**.

1. W obszarze **obsługuje**, wybierz opcję **nowe żądanie obsługi**. Podaj te szczegóły dla tego żądania:

   | Ustawienie | Wartość |
   |---------|-------|
   | **Typ problemu** | **Technical Preview** | 
   | **Subskrypcja** | <*your-Azure-subscription*> | 
   | **Usługa** | W obszarze **monitorowanie i zarządzanie**, wybierz opcję **harmonogramu**. | 
   ||| 

1. Wybierz żądaną opcję pomocy technicznej. Jeśli masz plan płatnej pomocy technicznej, wybierz **dalej**.

**Community**

* [Forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Kolejne kroki

* [Utwórz regularnie uruchomionych zadań i przepływów pracy z usługą Azure Logic Apps](../connectors/connectors-native-recurrence.md)
* [Samouczek: Sprawdzanie ruchu za pomocą aplikacji logiki na podstawie harmonogramu](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)