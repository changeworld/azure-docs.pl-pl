---
title: Metryki Azure Service Bus w Azure Monitor | Microsoft Docs
description: W tym artykule wyjaśniono, jak używać Azure Monitor do monitorowania jednostek Service Bus (kolejek, tematów i subskrypcji).
services: service-bus-messaging
documentationcenter: .NET
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 329b930c950ea7c58bdac798fce51af152aa8ff3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396264"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Metryki Azure Service Bus w Azure Monitor

Metryki Service Bus umożliwiają udostępnienie stanu zasobów w ramach subskrypcji platformy Azure. Dzięki bogatemu zestawowi danych metryk można ocenić ogólną kondycję zasobów Service Bus, nie tylko na poziomie przestrzeni nazw, ale również na poziomie jednostki. Te dane statystyczne mogą być ważne, ponieważ ułatwiają monitorowanie stanu Service Bus. Metryki może również pomóc główną przyczynę problemów bez konieczności skontaktuj się z działem pomocy technicznej platformy Azure.

Usługa Azure Monitor zapewnia interfejsy użytkownika ujednolicone monitorowanie z przekraczaniem różne usługi platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowanie w Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) i [pobieranie metryk Azure monitor z przykładem platformy .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) w witrynie GitHub.

> [!IMPORTANT]
> Gdy nie dojdzie do żadnej interakcji z jednostką przez 2 godziny, metryki rozpoczną wyświetlanie "0" jako wartości do momentu, gdy jednostka nie będzie już w stanie bezczynności.

## <a name="access-metrics"></a>Dostęp do metryk

Usługa Azure Monitor zapewnia wiele sposobów, aby dostęp do metryk. Możesz uzyskać dostęp do metryk przy użyciu [Azure Portal](https://portal.azure.com)lub użyć interfejsów API Azure monitor (REST i .NET) oraz rozwiązań do analizy, takich jak dzienniki Azure Monitor i Event Hubs. Aby uzyskać więcej informacji, zobacz [metryki w Azure monitor](../azure-monitor/platform/data-platform-metrics.md).

Metryki są domyślnie włączone i możesz uzyskać dostęp z ostatnich 30 dni danych. Jeśli zachodzi potrzeba Zachowaj dane przez dłuższy okres czasu, można archiwizować dane metryk do konta usługi Azure Storage. Ta wartość jest konfigurowana w [ustawieniach diagnostycznych](../azure-monitor/platform/diagnostic-settings.md) w Azure monitor.

## <a name="access-metrics-in-the-portal"></a>Dostęp do metryk w portalu

Można monitorować metryki w czasie w [Azure Portal](https://portal.azure.com). Jak wyświetlić żądania zakończone powodzeniem i żądania przychodzące na poziomie konta można znaleźć w poniższym przykładzie:

![][1]

Można również uzyskać dostęp do metryk bezpośrednio za pośrednictwem przestrzeni nazw. Aby to zrobić, wybierz przestrzeń nazw, a następnie kliknij pozycję **metryki**. Aby wyświetlić metryki odfiltrowane do zakresu jednostki, wybierz jednostkę, a następnie kliknij pozycję **metryki**.

![][2]

W przypadku metryk obsługujących wymiary należy filtrować według żądanej wartości wymiaru.

## <a name="billing"></a>Rozliczenia

Metryki i alerty dotyczące Azure Monitor są naliczane według poszczególnych alertów. Opłaty te powinny być dostępne w portalu po skonfigurowaniu alertu i przed jego zapisaniem. 

Dodatkowe rozwiązania, w przypadku których dane metryk są rozliczane bezpośrednio przez te rozwiązania. Na przykład opłaty są naliczane przez usługę Azure Storage archiwizowania danych metryk z kontem usługi Azure Storage. Opłaty są naliczane według Log Analytics, jeśli przesyłasz strumieniowo dane metryk do Log Analytics na potrzeby zaawansowanej analizy.

Następujące metryki umożliwiają przegląd kondycji usługi. 

> [!NOTE]
> Firma Microsoft jest wycofano kilka metryk, ponieważ są one przenoszone pod inną nazwą. Może to wymagać aktualizacji odwołaniami. Oznaczona za pomocą słowa kluczowego "przestarzałe" metryk nie będą obsługiwane przyszłości.

Wszystkie wartości metryk są wysyłane do usługi Azure Monitor na minutę. Stopień szczegółowości czasu określa interwał czasu, dla której są prezentowane wartości metryk. Obsługiwany przedział czasu dla wszystkich metryk Service Bus wynosi 1 minutę.

## <a name="request-metrics"></a>Metryki żądania

Zlicza żądania operacji danych i zarządzania.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Żądania przychodzące| Liczba żądań wysyłanych do usługi Service Bus w określonym przedziale czasu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Żądania zakończone powodzeniem|Liczba pomyślnych żądań do usługi Service Bus w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Błędy serwera|Liczba żądań, które nie zostały przetworzone z powodu błędu w usłudze Service Bus w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Błędy użytkowników (zobacz następującą podsekcję)|Liczba żądań, które nie zostały przetworzone z powodu błędów użytkowników w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Ograniczone żądania|Liczba żądań, które zostały ograniczone, ponieważ przekroczono użycie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|

### <a name="user-errors"></a>Błędy użytkownika

Następujące dwa typy błędów są klasyfikowane jako błędy użytkownika:

1. Błędy po stronie klienta (w przypadku protokołu HTTP, które byłyby błędy 400).
2. Błędy, które występują podczas przetwarzania komunikatów, takie jak [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Metryki wiadomości

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Komunikaty przychodzące|Liczba zdarzeń lub komunikatów wysłanych do Service Bus w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Komunikaty wychodzące|Liczba zdarzeń lub komunikatów odebranych z Service Bus w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
| Komunikaty| Liczba komunikatów w kolejce/temacie. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Dimension: EntityName |
| ActiveMessages| Liczba aktywnych komunikatów w kolejce/temacie. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Dimension: EntityName |
| Wiadomości utracone| Liczba utraconych wiadomości w kolejce/temacie. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/>Dimension: EntityName |
| Zaplanowane wiadomości| Liczba zaplanowanych komunikatów w kolejce/temacie. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia  <br/> Dimension: EntityName |

> [!NOTE]
> Wartości dla następujących metryk są wartościami punktu w czasie. Komunikaty przychodzące, które były używane natychmiast po tym punkcie w czasie, mogą nie być odzwierciedlone w tych metrykach. 
> - Komunikaty
> - Aktywne komunikaty 
> - Wiadomości utracone 
> - Zaplanowane wiadomości 

## <a name="connection-metrics"></a>Metryki połączeń

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Połączeń ActiveConnections|Liczba aktywnych połączeń w przestrzeni nazw, a także w jednostce.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|

## <a name="resource-usage-metrics"></a>Metryki użycia zasobów

> [!NOTE] 
> Następujące metryki są dostępne tylko w warstwie **Premium** . 

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Użycie procesora CPU na przestrzeń nazw|Procent użycia procesora w przestrzeni nazw.<br/><br/> Jednostka: procent <br/> Typ agregacji: maksimum <br/> Dimension: EntityName|
|Użycie rozmiaru pamięci na przestrzeń nazw|Procent użycia pamięci w przestrzeni nazw.<br/><br/> Jednostka: procent <br/> Typ agregacji: maksimum <br/> Dimension: EntityName|

## <a name="metrics-dimensions"></a>Wymiary metryki

Azure Service Bus obsługuje następujące wymiary dla metryk w Azure Monitor. Dodawanie wymiarów do metryk jest opcjonalne. Jeśli nie dodasz wymiarów, metryki są określone na poziomie przestrzeni nazw. 

|Nazwa wymiaru|Opis|
| ------------------- | ----------------- |
|EntityName| Service Bus obsługuje jednostki obsługi komunikatów w przestrzeni nazw.|

## <a name="set-up-alerts-on-metrics"></a>Konfigurowanie alertów dotyczących metryk

1. Na karcie **metryki** **obszaru nazw Service Bus** wybierz pozycję **Konfiguruj alerty**. 

    ![Strona metryk — Konfigurowanie alertów](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Wybierz opcję **Wybierz cel** i wykonaj następujące czynności na stronie **Wybierz zasób** : 
    1. Wybierz **Service Bus przestrzenie nazw** dla pola **Filtr według typu zasobu** . 
    2. Wybierz subskrypcję dla pola **Filtruj według subskrypcji** .
    3. Wybierz z listy **przestrzeń nazw usługi Service Bus** . 
    4. Wybierz pozycję **Done** (Gotowe). 
    
        ![Wybierz przestrzeń nazw](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Wybierz pozycję **Dodaj kryteria**i wykonaj następujące czynności na stronie **Konfigurowanie logiki sygnałów** :
    1. Wybierz **metryki** dla **typu sygnału**. 
    2. Wybierz sygnał. Na przykład: **błędy usługi**. 

        ![Wybieranie błędów serwera](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Wybierz opcję **większe niż** w przypadku **warunku**.
    2. Wybierz pozycję **Suma** dla **agregacji czasu**. 
    3. Wprowadź wartość **5** w obszarze **próg**. 
    4. Wybierz pozycję **Done** (Gotowe).    

        ![Określ warunek](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Na stronie **Tworzenie reguły** rozwiń pozycję **Zdefiniuj szczegóły alertu**i wykonaj następujące czynności:
    1. Wprowadź **nazwę** alertu. 
    2. Wprowadź **Opis** alertu.
    3. Wybierz pozycję **ważność** dla alertu. 

        ![Szczegóły alertu](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Na stronie **Tworzenie reguły** rozwiń węzeł **Zdefiniuj grupę akcji**, wybierz pozycję **Nowa grupa akcji**i wykonaj następujące czynności na **stronie Dodaj grupę akcji**. 
    1. Wprowadź nazwę grupy akcji.
    2. Wprowadź krótką nazwę grupy akcji. 
    3. Wybierz subskrypcję. 
    4. Wybierz grupę zasobów. 
    5. W tym instruktażu wprowadź nazwę **Wyślij wiadomość e-mail** dla **akcji**.
    6. Wybierz pozycję **poczta e-mail/SMS/wypychanie/głos** dla **typu akcji**. 
    7. Wybierz pozycję **Edytuj szczegóły**. 
    8. Na stronie **wiadomości e-mail/SMS/wypychanie/głos** wykonaj następujące czynności:
        1. Wybierz pozycję **e-mail**. 
        2. Wpisz **adres e-mail**. 
        3. Kliknij przycisk **OK**.

            ![Szczegóły alertu](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Na stronie **Dodaj grupę akcji** wybierz pozycję **OK**. 
1. Na stronie **Tworzenie reguły** wybierz pozycję **Utwórz regułę alertu**. 

    ![Przycisk tworzenia reguły alertu](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Następne kroki

Zobacz [omówienie Azure monitor](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


