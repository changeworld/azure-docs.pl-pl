---
title: Migrowanie z harmonogramu platformy Azure do aplikacji logiki platformy Azure
description: Dowiedz się, jak zastąpić zadania w usłudze Azure Scheduler, która jest wycofywana, za pomocą usługi Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 02/29/2020
ms.openlocfilehash: 90c3cc2e096b9b58465987bc53f718c5d06c6203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899070"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrowanie zadań harmonogramu platformy Azure do aplikacji logiki platformy Azure

> [!IMPORTANT]
> [Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje harmonogram platformy Azure, który jest [wycofywany.](#retire-date) Aby kontynuować pracę z zadaniami skonfigurowane w harmonogramie, należy jak najszybciej przeprowadzić migrację do usługi Azure Logic Apps, wykonując ten artykuł. 
>
> Harmonogram nie jest już dostępny w witrynie Azure portal, ale [interfejsy CMDLET interfejsu REST](/rest/api/scheduler) i [narzędzia cmdlet programu Azure Scheduler PowerShell](scheduler-powershell-reference.md) pozostają dostępne w tej chwili, dzięki czemu można zarządzać zadaniami i kolekcjami zadań.

W tym artykule pokazano, jak można zaplanować zadania jednorazowe i cykliczne, tworząc zautomatyzowane przepływy pracy za pomocą aplikacji Azure Logic Apps, a nie za pomocą harmonogramu platformy Azure. Podczas tworzenia zaplanowanych zadań za pomocą aplikacji logiki, można uzyskać następujące korzyści:

* Skompiluj swoje zadanie przy użyciu projektanta wizualnego i [gotowych do użycia łączników](../connectors/apis-list.md) z setek usług, takich jak Usługa Azure Blob Storage, usługa Azure Service Bus, Office 365 Outlook i SAP.

* Zarządzaj każdym zaplanowanym przepływem pracy jako zasobem platformy Azure pierwszej klasy. Nie musisz się martwić o koncepcję *kolekcji zadań,* ponieważ każda aplikacja logiki jest indywidualnym zasobem platformy Azure.

* Uruchom wiele zadań jednorazowych przy użyciu jednej aplikacji logiki.

* Ustaw harmonogramy, które obsługują strefy czasowe i automatycznie dostosowują się do czasu letniego (DST).

Aby dowiedzieć się więcej, zobacz [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md) lub spróbuj utworzyć pierwszą aplikację logiki w tym przewodniku Szybki start: Tworzenie pierwszej aplikacji [logiki.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aby wyzwolić aplikację logiki, wysyłając żądania HTTP, użyj narzędzia, takiego jak [aplikacja klasyczna Postman](https://www.getpostman.com/apps).

## <a name="migrate-by-using-a-script"></a>Migrowanie przy użyciu skryptu

Każde zadanie harmonogramu jest unikatowe, więc nie istnieje żadne uniwersalne narzędzie do migracji zadań harmonogramu do aplikacji Azure Logic Apps. Można jednak [edytować ten skrypt](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration) zgodnie z potrzebami.

## <a name="schedule-one-time-jobs"></a>Planowanie zadań jednorazowych

Można uruchomić wiele zadań jednorazowych, tworząc tylko jedną aplikację logiki.

1. W [witrynie Azure portal](https://portal.azure.com)utwórz pustą aplikację logiki w projektancie aplikacji logiki.

   Aby uzyskać podstawowe kroki, wykonaj [szybki start: Utwórz pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. W polu wyszukiwania `when a http request` wprowadź, aby znaleźć wyzwalacz żądania. Z listy wyzwalaczy wybierz ten wyzwalacz: **Po odebraniu żądania HTTP**

   ![Dodaj wyzwalacz "Żądanie"](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Dla wyzwalacza żądania, można opcjonalnie podać schemat JSON, który pomaga Logic App Designer zrozumieć strukturę danych wejściowych zawartych w wywołaniu przychodzącym do wyzwalacza żądania i ułatwia dane wyjściowe, aby wybrać później w przepływie pracy.

   W polu **Schemat JSON treści żądania** wprowadź schemat, na przykład:

   ![Schemat żądania](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Jeśli nie masz schematu, ale masz ładunek próbki w formacie JSON, można wygenerować schemat z tego ładunku.

   1. W wyzwalaczu Żądanie wybierz opcję **Użyj przykładowego ładunku do wygenerowania schematu**.

   1. W obszarze **Wprowadzanie lub wklejanie przykładowego ładunku JSON**podaj ładunek próbki i wybierz opcję **Gotowe**, na przykład:

      ![Ładowność próbki](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

      ```json
      {
         "runat": "2012-08-04T00:00Z",
         "endpoint": "https://www.bing.com"
      }
      ```

1. W obszarze wyzwalacza wybierz pozycję **Następny krok**.

1. W polu wyszukiwania `delay until` wprowadź jako filtr. W obszarze listy akcji wybierz tę akcję: **Opóźnij do**

   Ta akcja wstrzymuje przepływ pracy aplikacji logiki do określonej daty i godziny.

   ![Dodaj akcję "Opóźnienie do"](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Wprowadź sygnaturę czasowa, gdy chcesz uruchomić przepływ pracy aplikacji logiki.

   Po kliknięciu wewnątrz pola **Sygnatura czasowa** zostanie wyświetlona lista zawartości dynamicznej, dzięki czemu można opcjonalnie wybrać dane wyjściowe z wyzwalacza.

   ![Podaj szczegóły "Opóźnienie do"](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Dodaj inne akcje, które chcesz uruchomić, wybierając spośród [setek gotowych do użycia łączników](../connectors/apis-list.md).

   Na przykład można dołączyć akcję HTTP, która wysyła żądanie do adresu URL, lub akcje, które działają z kolejkami magazynu, kolejkami usługi Service Bus lub tematami usługi Service Bus:

   ![Akcja HTTP](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Po zakończeniu zapisz aplikację logiki.

   ![Zapisywanie aplikacji logiki](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Po zapisaniu aplikacji logiki po raz pierwszy adres URL punktu końcowego wyzwalacza żądania aplikacji logiki pojawia się w polu **adresu URL HTTP POST.** Jeśli chcesz wywołać aplikację logiki i wysłać dane wejściowe do aplikacji logiki do przetwarzania, użyj tego adresu URL jako miejsca docelowego wywołania.

   ![Zapisz adres URL wyzwalacza żądania końcowego](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Skopiuj i zapisz ten adres URL punktu końcowego, aby później wysłać ręczne żądanie, które wyzwala aplikację logiki.

## <a name="start-a-one-time-job"></a>Rozpoczynanie pracy jednorazowej

Aby ręcznie uruchomić lub wyzwolić zadanie jednorazowe, wyślij wywołanie do adresu URL punktu końcowego wyzwalacza żądania aplikacji logiki. W tym wywołaniu określ dane wejściowe lub ładunek do wysłania, które można było opisać wcześniej, określając schemat.

Na przykład za pomocą aplikacji Postman można utworzyć żądanie POST z ustawieniami podobnymi do tego przykładu, a następnie wybierz **pozycję Wyślij,** aby wysłać żądanie.

| Metoda żądania | Adres URL | Treść | Nagłówki |
|----------------|-----|------|---------|
| **POST** | <*adres URL punktu końcowego*> | **Raw** <p>**JSON(aplikacja/json)** <p>W polu **nieprzetworzonym** wprowadź ładunek, który chcesz wysłać w żądaniu. <p>**Uwaga:** To ustawienie powoduje automatyczną konfigurację wartości **nagłówków.** | **Klucz:** Typ zawartości <br>**Wartość**: application/json |
|||||

![Wyślij żądanie do ręcznego wyzwolenia aplikacji logiki](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Po wysłaniu połączenia odpowiedź z aplikacji logiki pojawia się pod **polem raw** na karcie **Treść.** 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Jeśli chcesz anulować zadanie później, wybierz kartę **Nagłówki.** Znajdź i skopiuj wartość nagłówka **x-ms-workflow-run-id** w odpowiedzi. 
>
> ![Odpowiedź](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Anulowanie zadania jednorazowego

W aplikacji logiki każde zadanie jednorazowe jest wykonywane jako wystąpienie uruchamiania pojedynczej aplikacji logiki. Aby anulować zadanie jednorazowe, można użyć [przebiegów przepływu pracy — anuluj](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) w interfejsie API REST aplikacji logiki. Podczas wysyłania połączenia do wyzwalacza podaj [identyfikator uruchomienia przepływu pracy](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Planowanie zadań cyklicznych

1. W [witrynie Azure portal](https://portal.azure.com)utwórz pustą aplikację logiki w projektancie aplikacji logiki.

   Aby uzyskać podstawowe kroki, wykonaj [szybki start: Utwórz pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. W polu wyszukiwania wpisz "cyklu" jako filtr. Z listy wyzwalaczy wybierz ten wyzwalacz: **Cykl**

   ![Dodaj wyzwalacz "Cykl"](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Jeśli chcesz, skonfiguruj bardziej zaawansowany harmonogram.

   ![Zaawansowany harmonogram](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Aby uzyskać więcej informacji na temat zaawansowanych opcji planowania, zobacz [Tworzenie i uruchamianie zadań cyklicznych i przepływów pracy za pomocą usługi Azure Logic Apps](../connectors/connectors-native-recurrence.md).

1. Dodaj inne akcje, wybierając spośród [setek gotowych do użycia](../connectors/apis-list.md). W obszarze wyzwalacza wybierz pozycję **Następny krok**. Znajdź i wybierz żądane akcje.

   Na przykład można dołączyć akcję HTTP, która wysyła żądanie do adresu URL, lub akcje, które działają z kolejkami magazynu, kolejkami usługi Service Bus lub tematami usługi Service Bus:

   ![Akcja HTTP](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Po zakończeniu zapisz aplikację logiki.

   ![Zapisywanie aplikacji logiki](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Konfiguracja zaawansowana

Oto inne sposoby dostosowywania zadań.

### <a name="retry-policy"></a>Zasady ponawiania

Aby kontrolować sposób, w jaki akcja próbuje ponownie uruchomić w aplikacji logiki, gdy wystąpiły sporadyczne błędy, można ustawić [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies) w ustawieniach każdej akcji, na przykład:

1. Otwórz menu elipsy akcji (**...**) i wybierz **pozycję Ustawienia**.

   ![Otwórz ustawienia akcji](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Wybierz odpowiednią zasadę ponawiania prób. Aby uzyskać więcej informacji na temat każdej zasady, zobacz [Ponawianie zasad .](../logic-apps/logic-apps-exception-handling.md#retry-policies)

   ![Wybieranie zasad ponawiania próby](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Obsługa wyjątków i błędów

W harmonogramie platformy Azure, jeśli akcja domyślna nie powiedzie się, można uruchomić akcję alterative, która rozwiązuje warunek błędu. W usłudze Azure Logic Apps można również wykonać to samo zadanie.

1. W logice App Designer, powyżej akcji, którą chcesz obsłużyć, przesuń wskaźnik myszy na strzałkę między krokami i wybierz pozycję **Dodaj gałąź równoległą**.

   ![Dodawanie gałęzi równoległej](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Znajdź i wybierz akcję, którą chcesz uruchomić jako akcję alternatywną.

   ![Dodaj akcję równoległą](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. W akcji alternatywnej otwórz menu elipsy (**...**) i wybierz **pozycję Konfiguruj uruchom po**.

   ![Skonfiguruj uruchamianie po](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Wyczyść pole dla właściwości **pomyślny.** Wybierz następujące właściwości: **nie powiodło się,** **jest pomijany**i **ma limit czasu**

   ![Konfigurowanie właściwości "run after"](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Po zakończeniu wybierz pozycję **Gotowe**.

Aby dowiedzieć się więcej na temat obsługi [wyjątków, zobacz Obsługa błędów i wyjątków — RunAfter.](../logic-apps/logic-apps-exception-handling.md#control-run-after-behavior)

## <a name="faq"></a>Najczęściej zadawane pytania

<a name="retire-date"></a>

**P:** Kiedy usługa Azure Scheduler przechodzi na emeryturę? <br>
**Odp**.: Harmonogram platformy Azure ma zostać całkowicie wycofany 31 grudnia 2019 r. Aby uzyskać ważne kroki do podjęcia przed tą datą i szczegółowy harmonogram, zobacz [Wydłużanie daty przejścia na emeryturę dla harmonogramu do 31 grudnia 2019](https://azure.microsoft.com/updates/extending-retirement-date-of-scheduler/)r. . Aby uzyskać ogólne aktualizacje, zobacz [aktualizacje platformy Azure — harmonogram](https://azure.microsoft.com/updates/?product=scheduler).

**P:** Co się stanie z moimi zbiorami zadań i zadaniami po przejściu usługi na emeryturę? <br>
**A:** Wszystkie kolekcje zadań harmonogramu i zadania przestają działać i są usuwane z systemu.

**P:** Czy muszę wykonać zapasową kopii zapasowych lub wykonać inne zadania przed migracją moich zadań harmonogramu do aplikacji logiki? <br>
**Odp.:** Najlepszym rozwiązaniem jest zawsze łóżcz zapasowe swojej pracy. Sprawdź, czy utworzone aplikacje logiki są uruchomione zgodnie z oczekiwaniami przed usunięciem lub wyłączeniem zadań harmonogramu.

**P:** Czy istnieje narzędzie, które może pomóc mi mi przenieść moje zadania z harmonogramu do aplikacji logiki? <br>
**A:** Każde zadanie harmonogramu jest unikatowe, więc uniwersalne narzędzie nie istnieje. Jednak na podstawie potrzeb można [edytować ten skrypt, aby przeprowadzić migrację zadań harmonogramu platformy Azure do usługi Azure Logic Apps.](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration)

**P:** Gdzie mogę uzyskać pomoc techniczną w zakresie migracji zadań harmonogramu? <br>
**Odp.:** Oto kilka sposobów uzyskania pomocy technicznej:

**Portal Azure**

Jeśli twoja subskrypcja platformy Azure ma płatny plan pomocy technicznej, możesz utworzyć żądanie pomocy technicznej w witrynie Azure portal. W przeciwnym razie można wybrać inną opcję pomocy technicznej.

1. W menu głównym [portalu Platformy Azure](https://portal.azure.com) wybierz pozycję Pomoc + pomoc **techniczna**.

1. Z menu **Pomoc techniczna** wybierz pozycję **Nowe żądanie pomocy technicznej**. Podaj te informacje dotyczące twojego żądania:

   | Właściwość | Wartość |
   |---------|-------|
   | **Typ problemu** | **Szczegóły techniczne** |
   | **Subskrypcja** | <*twoja subskrypcja platformy Azure*> |
   | **Usługa** | W obszarze **Monitorowanie & Zarządzanie**wybierz pozycję **Harmonogram**. Jeśli nie możesz znaleźć **harmonogramu,** wybierz najpierw **opcję Wszystkie usługi.** |
   ||| 

1. Wybierz odpowiednią opcję pomocy technicznej. Jeśli masz płatny plan pomocy technicznej, wybierz przycisk **Dalej**.

**Społeczność**

* [Forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie regularnie uruchomionych zadań i przepływów pracy za pomocą usługi Azure Logic Apps](../connectors/connectors-native-recurrence.md)