---
title: Metryki usługi Azure Service Bus w usłudze Azure Monitor| Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak używać usługi Azure Monitor do monitorowania jednostek usługi Service Bus (kolejki, tematy i subskrypcje).
services: service-bus-messaging
documentationcenter: .NET
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 227dfaff211eb60c5c2b25b5c76ecc82b6ce3edc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240787"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Metryki usługi Azure Service Bus w usłudze Azure Monitor

Metryki usługi Service Bus zapewniają stan zasobów w ramach subskrypcji platformy Azure. Za pomocą bogatego zestawu danych metryk można ocenić ogólną kondycję zasobów usługi Service Bus, nie tylko na poziomie obszaru nazw, ale także na poziomie jednostki. Te statystyki mogą być ważne, ponieważ pomagają one monitorować stan usługi Service Bus. Metryki mogą również pomóc w rozwiązywaniu problemów z przyczynami źródłowymi bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

Usługa Azure Monitor udostępnia ujednolicone interfejsy użytkownika do monitorowania w różnych usługach platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowanie na platformie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) i pobieranie [metryki usługi Azure Monitor z .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) próbki w usłudze GitHub.

> [!IMPORTANT]
> Jeśli nie było żadnej interakcji z jednostką przez 2 godziny, metryki zaczną pokazywać "0" jako wartość, dopóki jednostka nie będzie już bezczynna.

## <a name="access-metrics"></a>Dostęp do danych

Usługa Azure Monitor udostępnia wiele sposobów dostępu do metryk. Można uzyskać dostęp do metryk za pośrednictwem [witryny Azure portal](https://portal.azure.com)lub użyć interfejsów API usługi Azure Monitor (REST i .NET) i rozwiązań analitycznych, takich jak dzienniki usługi Azure Monitor i usługi Event Hubs. Aby uzyskać więcej informacji, zobacz [metryki w usłudze Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

Metryki są domyślnie włączone i można uzyskać dostęp do ostatnich 30 dni danych. Jeśli chcesz zachować dane przez dłuższy okres czasu, możesz archiwizować dane metryk na koncie usługi Azure Storage. Ta wartość jest konfigurowana w [ustawieniach diagnostycznych](../azure-monitor/platform/diagnostic-settings.md) w usłudze Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Dostęp do danych w portalu

Metryki można monitorować w czasie w [witrynie Azure portal](https://portal.azure.com). W poniższym przykładzie pokazano, jak wyświetlić pomyślne żądania i przychodzące żądania na poziomie konta:

![][1]

Można również uzyskać dostęp do metryk bezpośrednio za pośrednictwem obszaru nazw. Aby to zrobić, wybierz obszar nazw, a następnie kliknij **pozycję Metryki**. Aby wyświetlić metryki filtrowane do zakresu encji, wybierz encję, a następnie kliknij pozycję **Metryki**.

![][2]

W przypadku metryk pomocniczych wymiarów należy filtrować z żądaną wartością wymiaru.

## <a name="billing"></a>Rozliczenia

Metryki i alerty w usłudze Azure Monitor są naliczane na podstawie alertów. Opłaty te powinny być dostępne w portalu po skonfigurowaniu alertu i przed jego zapisaniem. 

Dodatkowe rozwiązania, które pochłoają dane metryki, są rozliczane bezpośrednio przez te rozwiązania. Na przykład są rozliczane przez usługę Azure Storage, jeśli archiwum danych metryk do konta usługi Azure Storage. Są również rozliczane przez usługi Log Analytics, jeśli strumień danych metryk do usługi Log Analytics do analizy zaawansowanej.

Poniższe metryki zapewniają przegląd kondycji usługi. 

> [!NOTE]
> Jesteśmy przestarzałe kilka metryk, ponieważ są one przenoszone pod inną nazwą. Może to wymagać aktualizacji odwołań. Dane oznaczone słowem kluczowym "przestarzałe" nie będą obsługiwane w przyszłości.

Wszystkie wartości metryk są wysyłane do usługi Azure Monitor co minutę. Ziarnistość czasu definiuje przedział czasu, dla którego są prezentowane wartości metryk. Obsługiwany przedział czasu dla wszystkich metryk usługi Service Bus wynosi 1 minutę.

## <a name="request-metrics"></a>Żądania metryki

Zlicza liczbę żądań operacji danych i zarządzania.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Żądania przychodzące| Liczba żądań złożonych do usługi Service Bus w określonym czasie. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|Pomyślne żądania|Liczba pomyślnych żądań złożonych do usługi Service Bus w określonym okresie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|Błędy serwera|Liczba żądań, które nie zostały przetworzone z powodu błędu w usłudze Service Bus w określonym okresie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|Błędy użytkownika (patrz poniższa podsekcja)|Liczba żądań nie przetworzonych z powodu błędów użytkownika w określonym czasie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|Ograniczone żądania|Liczba żądań, które zostały ograniczone, ponieważ użycie zostało przekroczone.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|

### <a name="user-errors"></a>Błędy użytkownika

Następujące dwa typy błędów są klasyfikowane jako błędy użytkownika:

1. Błędy po stronie klienta (w HTTP, który byłby 400 błędów).
2. Błędy występujące podczas przetwarzania wiadomości, takie jak [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Metryki wiadomości

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Wiadomości przychodzące|Liczba zdarzeń lub wiadomości wysyłanych do usługi Service Bus w określonym czasie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|Wiadomości wychodzące|Liczba zdarzeń lub komunikatów odebranych z usługi Service Bus w określonym okresie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
| Komunikaty| Liczba wiadomości w kolejce/temacie. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: średnia <br/> Wymiar: Nazwa elementu |
| ActiveMessages (Aktywne"| Liczba aktywnych wiadomości w kolejce/temacie. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: średnia <br/> Wymiar: Nazwa elementu |
| Wiadomości z martwymi literami| Liczba wiadomości utraconych w kolejce/temacie. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: średnia <br/>Wymiar: Nazwa elementu |
| Zaplanowane wiadomości| Liczba zaplanowanych wiadomości w kolejce/temacie. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: średnia  <br/> Wymiar: Nazwa elementu |

> [!NOTE]
> Wartości dla następujących metryk są wartościami punktu w czasie. Przychodzące wiadomości, które zostały zużyte natychmiast po tym punkt w czasie może nie być odzwierciedlone w tych metryki. 
> - Komunikaty
> - Aktywne wiadomości 
> - Wiadomości z martwymi literami 
> - Zaplanowane wiadomości 

## <a name="connection-metrics"></a>Metryki połączeń

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|ActiveConnections (Aktywne połączenia)|Liczba aktywnych połączeń w obszarze nazw, a także w jednostce.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|

## <a name="resource-usage-metrics"></a>Metryki użycia zasobów

> [!NOTE] 
> Poniższe dane są dostępne tylko w warstwie **premium.** 
> 
> Ważne metryki do monitorowania wszelkich awarii dla obszaru nazw warstwy premium to: **użycie procesora CPU na obszar nazw** i rozmiar pamięci na obszar **nazw**. [Skonfiguruj alerty](../azure-monitor/platform/alerts-metric.md) dla tych metryk przy użyciu usługi Azure Monitor.
> 
> Inną metryką, którą można monitorować, są: **żądania ograniczone.** Nie powinno być problemu, choć tak długo, jak obszar nazw pozostaje w pamięci, procesora CPU i pośredniczy limitów połączeń. Aby uzyskać więcej informacji, zobacz [Ograniczanie przepustowości w warstwie Premium usługi Azure Service Bus](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Użycie procesora CPU na obszar nazw|Procent użycia procesora CPU obszaru nazw.<br/><br/> Jednostka: Procent <br/> Typ agregacji: maksymalna <br/> Wymiar: Nazwa elementu|
|Użycie rozmiaru pamięci na obszar nazw|Procent użycia pamięci obszaru nazw.<br/><br/> Jednostka: Procent <br/> Typ agregacji: maksymalna <br/> Wymiar: Nazwa elementu|

## <a name="metrics-dimensions"></a>Wymiary metryki

Usługa Azure Service Bus obsługuje następujące wymiary metryk w usłudze Azure Monitor. Dodawanie wymiarów do danych jest opcjonalne. Jeśli wymiary nie zostaną dodane, metryki są określane na poziomie obszaru nazw. 

|Nazwa wymiaru|Opis|
| ------------------- | ----------------- |
|Nazwa elementu| Usługa Service Bus obsługuje jednostki obsługi wiadomości w obszarze nazw.|

## <a name="set-up-alerts-on-metrics"></a>Konfigurowanie alertów dotyczących metryk

1. Na karcie **Metryki** na stronie **Obszar nazw magistrali usług** wybierz pozycję **Konfiguruj alerty**. 

    ![Strona Metryki — menu Konfigurowanie alertów](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Wybierz opcję **Wybierz obiekt docelowy** i wykonaj następujące czynności na stronie **Wybierz zasób:** 
    1. Wybierz **pozycję Obszary nazw magistrali usług** dla pola **Filtruj według typu zasobu.** 
    2. Wybierz subskrypcję pola **Filtruj według subskrypcji.**
    3. Wybierz **obszar nazw magistrali usług** z listy. 
    4. Wybierz pozycję **Done** (Gotowe). 
    
        ![Wybierz obszar nazw](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Wybierz **pozycję Dodaj kryteria**i wykonaj następujące czynności na stronie **Konfigurowanie logiki sygnału:**
    1. Wybierz **Metryki** dla **typu sygnału**. 
    2. Wybierz sygnał. Na przykład: **Błędy usługi**. 

        ![Wybieranie błędów serwera](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Wybierz **opcję Większa niż** dla **warunku**.
    2. Wybierz **opcję Suma** dla **agregacji czasu**. 
    3. Wprowadź **5** dla **progu**. 
    4. Wybierz pozycję **Done** (Gotowe).    

        ![Określ warunek](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Na stronie **Tworzenie reguły** rozwiń pozycję **Definiuj szczegóły alertu**i wykonaj następujące czynności:
    1. Wprowadź **nazwę** alertu. 
    2. Wprowadź **opis** alertu.
    3. Wybierz **ważność** alertu. 

        ![Szczegóły alertu](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Na stronie **Tworzenie reguły** rozwiń pozycję **Definiuj grupę akcji**, wybierz **pozycję Nowa grupa akcji**i wykonaj następujące czynności na stronie **Dodaj grupę akcji**. 
    1. Wprowadź nazwę grupy akcji.
    2. Wprowadź krótką nazwę grupy akcji. 
    3. Wybierz subskrypcję. 
    4. Wybierz grupę zasobów. 
    5. W tym instruktażu wprowadź pozycję **Wyślij wiadomość e-mail** do **nazwy akcji**.
    6. Wybierz **opcję E-mail/SMS/Push/Voice** dla **typu AKCJI**. 
    7. Wybierz pozycję **Edytuj szczegóły**. 
    8. Na stronie **E-mail/SMS/Push/Voice** wykonaj następujące czynności:
        1. Wybierz pozycję **Adres e-mail**. 
        2. Wpisz **adres e-mail**. 
        3. Kliknij przycisk **OK**.

            ![Szczegóły alertu](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Na stronie **Dodawanie grupy akcji** wybierz pozycję **OK**. 
1. Na stronie **Tworzenie reguły** wybierz pozycję **Utwórz regułę alertu**. 

    ![Przycisk Utwórz regułę alertu](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Następne kroki

Zobacz [omówienie usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


