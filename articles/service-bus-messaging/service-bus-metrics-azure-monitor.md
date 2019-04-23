---
title: Metryki usługi Azure Service Bus w usłudze Azure Monitor (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Użyj monitorowania platformy Azure do monitorowania jednostek usługi Service Bus
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 11/06/2018
ms.author: aschhab
ms.openlocfilehash: 175d5d5d4495986c29b75427a325088c14279e17
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798520"
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Metryki usługi Azure Service Bus w usłudze Azure Monitor (wersja zapoznawcza)

Metryki usługi Service Bus umożliwiają stan zasobów w ramach subskrypcji platformy Azure. Bogaty zestaw danych metryk możesz ocenić ogólną kondycję zasobów usługi Service Bus, nie tylko na poziomie przestrzeni nazw, ale na poziomie jednostki. Te statystyki może być ważne, ponieważ one pomóc Ci do monitorowania stanu usługi Service Bus. Metryki może również pomóc główną przyczynę problemów bez konieczności skontaktuj się z działem pomocy technicznej platformy Azure.

Usługa Azure Monitor zapewnia interfejsy użytkownika ujednolicone monitorowanie z przekraczaniem różne usługi platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowania na platformie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) i [metryki pobierania usługi Azure Monitor przy użyciu platformy .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) próbki w witrynie GitHub.

> [!IMPORTANT]
> Jeśli nie było żadnych interakcji z jednostką przez 2 godziny, metryki zostanie uruchomiony, przedstawiający jako wartość "0" do momentu jednostka jest już w stanie bezczynności.

## <a name="access-metrics"></a>Dostęp do metryk

Usługa Azure Monitor zapewnia wiele sposobów, aby dostęp do metryk. Możesz albo metryk dostęp za pośrednictwem [witryny Azure portal](https://portal.azure.com), lub użyj rozwiązań analizy, takie jak dzienniki usługi Azure Monitor i usługi Event Hubs i interfejsów API usługi Azure Monitor (REST i .NET). Aby uzyskać więcej informacji, zobacz [metryk w usłudze Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

Metryki są domyślnie włączone i możesz uzyskać dostęp z ostatnich 30 dni danych. Jeśli zachodzi potrzeba Zachowaj dane przez dłuższy okres czasu, można archiwizować dane metryk do konta usługi Azure Storage. Ta wartość jest skonfigurowana w [ustawień diagnostycznych](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) w usłudze Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Dostęp do metryk w portalu

Możesz monitorować metryki, wraz z upływem czasu w [witryny Azure portal](https://portal.azure.com). Jak wyświetlić żądania zakończone powodzeniem i żądania przychodzące na poziomie konta można znaleźć w poniższym przykładzie:

![][1]

Można również uzyskać dostęp do metryk bezpośrednio za pośrednictwem przestrzeni nazw. Aby to zrobić, wybierz przestrzeń nazw, a następnie kliknij przycisk **metryki (wersja zapoznawcza)**. Aby wyświetlić metryki filtrowany, aby zakres jednostki, wybierz jednostkę, a następnie kliknij przycisk **metryki (wersja zapoznawcza)**.

![][2]

Dla metryk, obsługa wymiarów możesz odfiltrować z żądaną wartością.

## <a name="billing"></a>Rozliczenia

Przy użyciu metryk w usłudze Azure Monitor jest w wersji zapoznawczej. Jednak jeśli używasz dodatkowych rozwiązań, które pozyskiwania danych metryk, może zostać naliczona według tych rozwiązań. Na przykład opłaty są naliczane przez usługę Azure Storage archiwizowania danych metryk z kontem usługi Azure Storage. Są również rozliczane przez dzienniki usługi Azure Monitor w przypadku strumienia danych metryk z dziennikami usługi Azure Monitor do zaawansowanej analizy.

Następujące metryki umożliwiają przegląd kondycji usługi. 

> [!NOTE]
> Firma Microsoft jest wycofano kilka metryk, ponieważ są one przenoszone pod inną nazwą. Może to wymagać aktualizacji odwołaniami. Oznaczona za pomocą słowa kluczowego "przestarzałe" metryk nie będą obsługiwane przyszłości.

Wszystkie wartości metryk są wysyłane do usługi Azure Monitor na minutę. Stopień szczegółowości czasu określa interwał czasu, dla której są prezentowane wartości metryk. Interwał czasu obsługiwane wszystkie metryki usługi Service Bus to 1 minuta.

## <a name="request-metrics"></a>Metryki żądania

Zlicza żądania operacji danych i zarządzania.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Przychodzące żądania (wersja zapoznawcza) | Liczba żądań kierowanych do usługi Service Bus w określonym czasie. <br/><br/> Jednostka: Licznik <br/> Typ agregacji: Łącznie <br/> Wymiar: EntityName|
|Liczba pomyślnych żądań (wersja zapoznawcza)|Liczba żądań zakończonych powodzeniem kierowanych do usługi Service Bus w określonym czasie.<br/><br/> Jednostka: Licznik <br/> Typ agregacji: Łącznie <br/> Wymiar: EntityName|
|Błędy serwera (wersja zapoznawcza)|Liczba żądań, które nie zostały przetworzone z powodu błędu w usłudze Service Bus w określonym czasie.<br/><br/> Jednostka: Licznik <br/> Typ agregacji: Łącznie <br/> Wymiar: EntityName|
|Błędy użytkowników (wersja zapoznawcza — można znaleźć w poniższej podsekcji)|Liczba żądań, które nie zostały przetworzone z powodu błędów użytkowników w określonym czasie.<br/><br/> Jednostka: Licznik <br/> Typ agregacji: Łącznie <br/> Wymiar: EntityName|
|Żądania ograniczone (wersja zapoznawcza)|Liczba żądań, które zostały ograniczone z powodu przekroczenia użycia.<br/><br/> Jednostka: Licznik <br/> Typ agregacji: Łącznie <br/> Wymiar: EntityName|

### <a name="user-errors"></a>Błędy użytkowników

Dwa rodzaje błędów sklasyfikowanych jako błędy użytkownika:

1. Błędy po stronie klienta (w protokołu HTTP, który będzie 400 błędów).
2. Błędów występujących podczas przetwarzania komunikatów, takie jak [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Metryki wiadomości

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Komunikaty przychodzące (wersja zapoznawcza)|Liczba zdarzeń lub komunikaty wysyłane do usługi Service Bus w określonym czasie.<br/><br/> Jednostka: Licznik <br/> Typ agregacji: Łącznie <br/> Wymiar: EntityName|
|Komunikaty wychodzące (wersja zapoznawcza)|Liczba zdarzeń lub komunikatów odebranych z usługi Service Bus w określonym czasie.<br/><br/> Jednostka: Licznik <br/> Typ agregacji: Łącznie <br/> Wymiar: EntityName|
| Komunikaty (wersja zapoznawcza) | Liczba komunikatów w kolejce/temacie. <br/><br/> Jednostka: Licznik <br/> Typ agregacji: Średnia <br/> Wymiar: EntityName |
| ActiveMessages (wersja zapoznawcza) | Liczba aktywnych komunikatów w kolejce/temacie. <br/><br/> Jednostka: Licznik <br/> Typ agregacji: Średnia <br/> Wymiar: EntityName |

## <a name="connection-metrics"></a>Metryki połączeń

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Połączeń ActiveConnections (wersja zapoznawcza)|Liczba aktywnych połączeń w przestrzeni nazw, a także w jednostce.<br/><br/> Jednostka: Licznik <br/> Typ agregacji: Łącznie <br/> Wymiar: EntityName|
|Otwartego połączenia (wersja zapoznawcza)|Liczba otwartych połączeń.<br/><br/> Jednostka: Licznik <br/> Typ agregacji: Łącznie <br/> Wymiar: EntityName|
|Połączenia zamknięte (wersja zapoznawcza)|Liczba zamkniętych połączeń.<br/><br/> Jednostka: Licznik <br/> Typ agregacji: Łącznie <br/> Wymiar: EntityName |

## <a name="resource-usage-metrics"></a>Metryki użycia zasobów

> [!NOTE] 
> Następujące metryki są dostępne tylko w przypadku **premium** warstwy. 

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Użycie Procesora na przestrzeń nazw (wersja zapoznawcza)|Procent użycia Procesora przestrzeni nazw.<br/><br/> Jednostka: Procent <br/> Typ agregacji: Maksimum <br/> Wymiar: EntityName|
|Użycie rozmiaru pamięci na przestrzeń nazw (wersja zapoznawcza)|Wartość procentowa użycia pamięci przestrzeni nazw.<br/><br/> Jednostka: Procent <br/> Typ agregacji: Maksimum <br/> Wymiar: EntityName|

## <a name="metrics-dimensions"></a>Wymiary metryki

Usługa Azure Service Bus obsługuje następujące wymiary metryk w usłudze Azure Monitor. Dodawanie wymiarów do metryk jest opcjonalne. Jeśli nie dodasz wymiarów, metryki są określone na poziomie przestrzeni nazw. 

|Nazwa wymiaru|Opis|
| ------------------- | ----------------- |
|EntityName| Usługa Service Bus obsługuje jednostek obsługi komunikatów w przestrzeni nazw.|

## <a name="set-up-alerts-on-metrics"></a>Ustawianie alertów dotyczących metryk

1. Na **metryki** karcie **usługi Service Bus Namespace** wybierz opcję **Konfigurowanie alertów**. 

    ![Metryki stronie — Konfigurowanie menu alerty](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Wybierz **Wybieranie lokalizacji docelowej**, i wykonaj następujące czynności na **wybierz zasób** strony: 
    1. Wybierz **przestrzeniami nazw magistrali usług** dla **Filtruj według typu zasobu** pola. 
    2. Wybierz swoją subskrypcję dla **Filtruj według subskrypcji** pola.
    3. Wybierz **przestrzeń nazw magistrali usług** z listy. 
    4. Wybierz pozycję **Done** (Gotowe). 
    
        ![Wybieranie przestrzeni nazw](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Wybierz **Dodaj kryteria**, i wykonaj następujące czynności na **konfigurowanie logiki sygnału** strony:
    1. Wybierz **metryki** dla **sygnał typu**. 
    2. Wybierz sygnał. Na przykład: **Usługa błędy (wersja zapoznawcza)**. 

        ![Zaznacz błędy serwera](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Wybierz **większa** dla **warunek**.
    2. Wybierz **całkowita** dla **Agregacja czasu**. 
    3. Wprowadź **5** dla **próg**. 
    4. Wybierz pozycję **Done** (Gotowe).    

        ![Określ warunek](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Na **Utwórz regułę** rozwiń **Zdefiniuj szczegóły alertu**, i wykonaj następujące czynności:
    1. Wprowadź **nazwa** alertu. 
    2. Wprowadź **opis** alertu.
    3. Wybierz **ważność** alertu. 

        ![Szczegóły alertu](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Na **Utwórz regułę** rozwiń **zdefiniuj grupę akcji**, wybierz opcję **Nowa grupa akcji**, i wykonaj następujące czynności na **stronę grupy akcji Dodaj**. 
    1. Wprowadź nazwę grupy akcji.
    2. Podaj krótką nazwę grupy akcji. 
    3. Wybierz subskrypcję. 
    4. Wybierz grupę zasobów. 
    5. W ramach tego przewodnika należy wprowadzić **Wyślij wiadomość e-mail** dla **nazwy akcji**.
    6. Wybierz **poczty E-mail/SMS/wypychania/rejestr** dla **typ akcji**. 
    7. Wybierz **Edytuj szczegóły**. 
    8. Na **poczty E-mail/SMS/wypychania/rejestr** wykonaj następujące czynności:
        1. Wybierz **E-mail**. 
        2. Typ **adres e-mail**. 
        3. Kliknij przycisk **OK**.

            ![Szczegóły alertu](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Na **Dodaj grupę akcji** wybierz opcję **OK**. 
1. Na **Utwórz regułę** wybierz opcję **Utwórz regułę alertu**. 

    ![Tworzenie przycisku reguły alertu](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Kolejne kroki

Zobacz [monitorowania platformy Azure — omówienie](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


