---
title: Migrowanie z usługi Azure Scheduler do Azure Logic Apps
description: Dowiedz się, w jaki sposób można zastąpić zadania w usłudze Azure Scheduler, które są wycofywane z Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/20/2018
ms.openlocfilehash: 0225a9f34e016a4b1de51c06ba982d384e41007c
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302084"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrowanie zadań usługi Azure Scheduler do Azure Logic Apps

> [!IMPORTANT]
> Azure Logic Apps zastępuje usługę Azure Scheduler, która jest wycofywana. W celu zaplanowania zadań postępuj zgodnie z tym artykułem, aby przenieść się do Azure Logic Apps zamiast tego.

W tym artykule pokazano, jak można zaplanować jednorazowe i cykliczne zadania, tworząc zautomatyzowane przepływy pracy za pomocą Azure Logic Apps, a nie za pomocą usługi Azure Scheduler. Podczas tworzenia zaplanowanych zadań z Logic Apps uzyskasz następujące korzyści:

* Nie musisz martwić się o koncepcję *kolekcji zadań* , ponieważ każda aplikacja logiki jest osobnym zasobem platformy Azure.

* Można uruchomić wiele jednorazowych zadań przy użyciu pojedynczej aplikacji logiki.

* Usługa Azure Logic Apps obsługuje strefę czasową i oszczędność czasu letniego (DST).

Aby dowiedzieć się więcej, zobacz [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md) lub spróbuj utworzyć swoją pierwszą aplikację logiki w tym przewodniku szybki start: [Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* Aby wyzwolić aplikację logiki przez wysyłanie żądań HTTP, użyj narzędzia, takiego jak [aplikacja dla aplikacji klasycznych](https://www.getpostman.com/apps).

## <a name="schedule-one-time-jobs"></a>Planowanie zadań jednorazowych

Można uruchomić wiele jednorazowych zadań, tworząc tylko jedną aplikację logiki. 

### <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. W [Azure Portal](https://portal.azure.com)Utwórz pustą aplikację logiki w Projektancie aplikacji logiki. 

   Aby uzyskać podstawowe kroki, skorzystaj [z przewodnika Szybki Start: Utwórz swoją pierwszą aplikację](../logic-apps/quickstart-create-first-logic-app-workflow.md)logiki.

1. W polu wyszukiwania wprowadź ciąg "When a żądanie HTTP" jako filtr. Z listy Wyzwalacze wybierz następujący wyzwalacz: **Po odebraniu żądania HTTP** 

   ![Dodaj wyzwalacz "żądanie"](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Dla wyzwalacza żądania można opcjonalnie dostarczyć schemat JSON, który pomaga projektantowi aplikacji logiki zrozumieć strukturę danych wejściowych z żądania przychodzącego i sprawia, że dane wyjściowe są łatwiejsze do wyboru w dalszej części przepływu pracy.

   Aby określić schemat, wprowadź schemat w polu **schemat JSON treści żądania** , na przykład: 

   ![Schemat żądania](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Jeśli nie masz schematu, ale masz przykładowy ładunek w formacie JSON, możesz wygenerować schemat z tego ładunku.

   1. W wyzwalaczu żądania wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

   1. W obszarze **wprowadzanie lub wklejanie przykładowego ładunku JSON**Podaj przykładowy ładunek, a następnie wybierz pozycję **gotowe**, na przykład:

      ![Przykładowy ładunek](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. W obszarze wyzwalacza wybierz pozycję **Następny krok**. 

1. W polu wyszukiwania wprowadź "opóźnienie do" jako filtr. Na liście Akcje wybierz tę akcję: **Opóźnij do**

   Ta akcja wstrzymuje przepływ pracy aplikacji logiki do określonej daty i godziny.

   ![Dodaj akcję "Opóźnij do"](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Wprowadź sygnaturę czasową, aby uruchomić przepływ pracy aplikacji logiki. 

   Po kliknięciu wewnątrz pola **sygnatury czasowej** zostanie wyświetlona lista zawartości dynamicznej, dzięki czemu możesz opcjonalnie wybrać dane wyjściowe z wyzwalacza.

   ![Podaj szczegóły "opóźnienie do"](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Dodaj inne akcje, które chcesz uruchomić, wybierając z [setek łączników gotowe do użycia](../connectors/apis-list.md). 

   Można na przykład dołączyć akcję HTTP, która wysyła żądanie do adresu URL, lub akcje, które działają z kolejkami magazynu, kolejkami Service Bus lub tematami Service Bus: 

   ![Akcja HTTP](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Gdy skończysz, Zapisz aplikację logiki.

   ![Zapisywanie aplikacji logiki](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Po zapisaniu aplikacji logiki po raz pierwszy, adres URL punktu końcowego wyzwalacza żądania aplikacji logiki zostanie wyświetlony w polu **adres URL post protokołu HTTP** . 
   Jeśli chcesz wywołać aplikację logiki i wysłać dane wejściowe do aplikacji logiki w celu przetworzenia, użyj tego adresu URL jako miejsca docelowego wywołania.

   ![Zapisz adres URL punktu końcowego wyzwalacza żądania](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Skopiuj i Zapisz ten adres URL punktu końcowego, aby później można było wysyłać żądanie ręczne, które wyzwala aplikację logiki. 

## <a name="start-a-one-time-job"></a>Rozpocznij zadanie jednorazowe

Aby ręcznie uruchomić lub wyzwolić jednorazowe zadanie, Wyślij wywołanie do adresu URL punktu końcowego dla wyzwalacza żądania aplikacji logiki. W tym wywołaniu Określ dane wejściowe lub ładunki do wysłania, które można wcześniej opisać, określając schemat. 

Na przykład przy użyciu aplikacji post można utworzyć żądanie POST z ustawieniami podobnymi do tego przykładu, a następnie wybrać polecenie Wyślij, aby **wysłać** żądanie.

| Metoda żądania | URL | Treść | Nagłówki |
|----------------|-----|------|---------| 
| **POST** | <*endpoint-URL*> | **surowców** <p>**JSON (Application/JSON)** <p>W polu **nieprzetworzony** wprowadź ładunek, który ma zostać wysłany w żądaniu. <p>**Uwaga**: To ustawienie powoduje automatyczne skonfigurowanie wartości **nagłówków** . | **Klucz**: Content-Type <br>**Wartość**: Application/JSON
 |||| 

![Wyślij żądanie ręcznego wyzwalania aplikacji logiki](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Po wysłaniu wywołania odpowiedź z aplikacji logiki zostanie wyświetlona w polu **nieprzetworzone** na karcie **treść** . 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Jeśli chcesz anulować zadanie później, wybierz kartę **nagłówki** . Znajdź i skopiuj wartość nagłówka **x-MS-Workflow-Run-ID** w odpowiedzi. 
>
> ![Odpowiedź](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Anulowanie zadania jednorazowego

W Logic Apps każde zadanie jednorazowe wykonuje jako pojedyncze wystąpienie uruchomienia aplikacji logiki. Aby anulować jednorazowe zadanie, można użyć [przebiegów przepływu pracy — Anuluj](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) w interfejsie API REST Logic Apps. Po wysłaniu wywołania do wyzwalacza Podaj [Identyfikator przebiegu przepływu pracy](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Planowanie zadań cyklicznych

### <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. W [Azure Portal](https://portal.azure.com)Utwórz pustą aplikację logiki w Projektancie aplikacji logiki. 

   Aby uzyskać podstawowe kroki, skorzystaj [z przewodnika Szybki Start: Utwórz swoją pierwszą aplikację](../logic-apps/quickstart-create-first-logic-app-workflow.md)logiki.

1. W polu wyszukiwania wprowadź wartość "cykl" jako filtr. Z listy Wyzwalacze wybierz następujący wyzwalacz: **Cykl** 

   ![Dodaj wyzwalacz "cykl"](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Skonfiguruj bardziej zaawansowany harmonogram, jeśli chcesz.

   ![Harmonogram zaawansowany](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Aby uzyskać więcej informacji na temat zaawansowanych opcji planowania, zobacz [Tworzenie i uruchamianie cyklicznych zadań i przepływów pracy za pomocą Azure Logic Apps](../connectors/connectors-native-recurrence.md)

1. Dodaj inne żądane akcje, wybierając spośród [setek gotowych do użycia](../connectors/apis-list.md). W obszarze wyzwalacza wybierz pozycję **Następny krok**. Znajdź i wybierz żądane akcje.

   Można na przykład dołączyć akcję HTTP, która wysyła żądanie do adresu URL, lub akcje, które działają z kolejkami magazynu, kolejkami Service Bus lub tematami Service Bus: 

   ![Akcja HTTP](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Gdy skończysz, Zapisz aplikację logiki.

   ![Zapisywanie aplikacji logiki](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Konfiguracja zaawansowana

Oto inne sposoby dostosowywania zadań.

### <a name="retry-policy"></a>Zasady ponawiania

Aby kontrolować sposób, w jaki akcja podejmuje próbę ponownego uruchomienia w aplikacji logiki w przypadku wystąpienia sporadycznych awarii, można ustawić [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies) w ustawieniach każdej akcji, na przykład:

1. Otwórz menu akcji ( **...** ), a następnie wybierz pozycję **Ustawienia**.

   ![Otwórz ustawienia akcji](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Wybierz żądane zasady ponawiania. Aby uzyskać więcej informacji na temat każdej z zasad, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Wybierz zasady ponawiania](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Obsługa wyjątków i błędów

Jeśli nie uda się uruchomić akcji domyślnej w usłudze Azure Scheduler, można uruchomić akcję alterative, która odnosi się do warunku błędu. W Azure Logic Apps można także wykonać to samo zadanie.

1. W Projektancie aplikacji logiki, nad akcją, którą chcesz obsłużyć, Przenieś wskaźnik myszy nad strzałkę między krokami, a następnie wybierz i **Dodaj gałąź równoległą**. 

   ![Dodaj gałąź równoległą](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Znajdź i wybierz akcję, która ma zostać uruchomiona zamiast akcji alternatywnej.

   ![Dodaj równoległą akcję](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. W akcji alternatywnej Otwórz menu ( **...** ), a następnie wybierz pozycję **Konfiguruj Uruchom po**.

   ![Skonfiguruj przebieg po](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Wyczyść pole wyboru dla właściwości **is powiodło się** . Wybierz te właściwości: **zakończyło się niepowodzeniem**, **pominięto**i przekroczono **limit czasu**

   ![Skonfiguruj właściwości "Uruchom po"](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Po zakończeniu wybierz pozycję **Gotowe**.

Aby dowiedzieć się więcej o obsłudze wyjątków, zobacz temat [Obsługa błędów i wyjątków — Właściwość RunAfter](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>Często zadawane pytania

<a name="retire-date"></a> 

**P**: Kiedy trwa wycofywanie usługi Azure Scheduler? <br>
**Odp.:** Usługa Azure Scheduler została zaplanowana do wycofania 30 września 2019.

**P**: Co się stanie z moimi kolekcjami zadań i zadaniami usługi Scheduler? <br>
**Odp.:** Wszystkie kolekcje zadań i zadania usługi Scheduler zostaną usunięte z systemu.

**P**: Czy muszę wykonać kopię zapasową lub wykonać inne zadania przed migracją zadań harmonogramu do Logic Apps? <br>
**Odp.:** Najlepszym rozwiązaniem jest zawsze Tworzenie kopii zapasowej pracy. Przed usunięciem lub wyłączaniem zadań harmonogramu należy sprawdzić, czy utworzone aplikacje logiki działają zgodnie z oczekiwaniami. 

**P**: Czy istnieje narzędzie, które może pomóc mi migrować moje zadania z usługi Scheduler do Logic Apps? <br>
**Odp.:** Każde zadanie usługi Scheduler jest unikatowe, dlatego nie istnieje narzędzie z jednym rozmiarem. Dostępne są jednak różne skrypty, które można modyfikować w zależności od potrzeb. Aby uzyskać dostęp do skryptu, sprawdź ponownie później.

**P**: Gdzie mogę uzyskać pomoc techniczną dotyczącą migrowania moich zadań harmonogramu? <br>
**Odp.:** Oto kilka sposobów uzyskania pomocy technicznej: 

**Azure Portal**

Jeśli Twoja subskrypcja platformy Azure ma płatny Plan pomocy technicznej, możesz utworzyć żądanie pomocy technicznej w Azure Portal. W przeciwnym razie możesz wybrać inną opcję pomocy technicznej.

1. W menu głównym [Azure Portal](https://portal.azure.com) wybierz pozycję **Pomoc i obsługa techniczna**.

1. W obszarze **Pomoc techniczna**wybierz pozycję **nowe żądanie obsługi**. Podaj te szczegóły żądania:

   | Ustawienie | Value |
   |---------|-------|
   | **Typ problemu** | **Naukow** | 
   | **Subskrypcja** | <*your-Azure-subscription*> | 
   | **Usługa** | W obszarze **monitorowanie & zarządzanie**wybierz pozycję **harmonogram**. | 
   ||| 

1. Wybierz żądaną opcję pomocy technicznej. Jeśli masz płatny Plan pomocy technicznej, wybierz pozycję **dalej**.

**Community**

* [Forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Następne kroki

* [Twórz regularnie uruchomione zadania i przepływy pracy za pomocą Azure Logic Apps](../connectors/connectors-native-recurrence.md)
* [Samouczek: Sprawdzanie ruchu za pomocą aplikacji logiki opartej na harmonogramie](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
