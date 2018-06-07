---
title: Użyj metryk do monitorowania Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Jak używać Centrum IoT Azure metryk do oceny i monitorowania ogólnej kondycji Twojej centra IoT.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: nberdy
ms.openlocfilehash: 754901bb9c851f66708771346cbb7fe2e42688cd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634067"
---
# <a name="understand-iot-hub-metrics"></a>Zrozumienie metryki Centrum IoT
Centrum IoT metryk umożliwiają lepsze dane dotyczące stanu zasobów Azure IoT w Twojej subskrypcji platformy Azure. Centrum IoT metryk umożliwiają oceny ogólnej kondycji usługi IoT Hub i dołączone do niego urządzenia. Statystyki dla użytkownika są ważne, ponieważ pomagają Zobacz, co się dzieje z IoT hub i pomocy głównej przyczyny problemów związanych z bez konieczności skontaktuj się z pomocą techniczną platformy Azure.

Metryki są domyślnie włączone. Można wyświetlić metryki Centrum IoT w portalu Azure.

## <a name="how-to-view-iot-hub-metrics"></a>Jak wyświetlić metryki Centrum IoT
1. Tworzenie Centrum IoT. Instrukcje można znaleźć na temat sposobu tworzenia Centrum IoT w [wprowadzenie] [ lnk-get-started] przewodnik.
2. Otwarcie bloku Centrum IoT. Z tego miejsca, kliknij przycisk **metryki**.
   
    ![][1]
3. W bloku metryki można wyświetlić metryki Centrum IoT i tworzenie niestandardowych widoków Twoje metryki. Użytkownik może wysłać dane metryk do punktu końcowego usługi Event Hubs lub konta usługi Azure Storage, klikając **ustawień diagnostycznych**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Centrum IoT metryki i sposobu ich użycia
Centrum IoT zawiera kilka miar do zawiera przegląd kondycji Centrum i łączna liczba podłączonych urządzeń. Możesz łączyć informacji z wielu metryki namalować pełny obraz stanu Centrum IoT. W poniższej tabeli opisano metryki, który śledzi każdego centrum IoT i jak wszystkie metryki odnosi się do ogólnego stanu Centrum IoT.

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Próby wysłania wiadomości telemetrii|Licznik|Łącznie|Liczba komunikatów telemetrii urządzenia do chmury próby wysłania do Centrum IoT|
|d2c.telemetry.ingress.success|Wysłane wiadomości telemetrii|Licznik|Łącznie|Liczba komunikatów telemetrii urządzenia do chmury pomyślnie wysłane do Centrum IoT|
|c2d.commands.egress.complete.success|Polecenia zakończona|Licznik|Łącznie|Liczba poleceń chmury do urządzenia, które zakończyło się powodzeniem, urządzenia|
|c2d.commands.egress.abandon.success|Porzucone poleceń|Licznik|Łącznie|Liczba poleceń chmury do urządzenia porzucone przez urządzenie|
|c2d.commands.egress.reject.success|Polecenia odrzucone|Licznik|Łącznie|Liczba odrzuconych przez urządzenie poleceń chmury do urządzenia|
|devices.totalDevices|Łączna liczba urządzeń|Licznik|Łącznie|Liczba urządzeń zarejestrowanych do Centrum IoT|
|devices.connectedDevices.allProtocol|Połączonych urządzeń|Licznik|Łącznie|Liczba urządzeń połączonych z Centrum IoT|
|d2c.telemetry.egress.success|Dostarczane wiadomości telemetrii|Licznik|Łącznie|Liczba komunikatów pomyślnie zostały zapisane do punktów końcowych (łącznie)|
|d2c.telemetry.egress.dropped|Porzucone komunikaty|Licznik|Łącznie|Liczba komunikatów porzuconych, ponieważ nie odpowiadało żadnych tras i trasy rezerwowy został wyłączony|
|d2c.telemetry.egress.orphaned|Oddzielone wiadomości|Licznik|Łącznie|Aktualna liczba wiadomości nie zgodnych żadnych tras, łącznie z rezerwowego trasy|
|d2c.telemetry.egress.invalid|Nieprawidłowy wiadomości|Licznik|Łącznie|Aktualna liczba wiadomości nie są dostarczane z powodu niezgodności z punktu końcowego|
|d2c.telemetry.egress.fallback|Wiadomości pasujące rezerwowy warunku|Licznik|Łącznie|Liczba komunikatów zapisanych do rezerwowego punktu końcowego|
|d2c.endpoints.egress.eventHubs|Komunikaty dostarczone do Centrum zdarzeń punkty końcowe|Licznik|Łącznie|Liczba wiadomości zostały pomyślnie zapisane punkty końcowe Centrum zdarzeń|
|d2c.endpoints.latency.eventHubs|Opóźnienie komunikat dla punktów końcowych Centrum zdarzeń|Milisekundy|Średnia|Średnie opóźnienie między komunikat przychodzący z Centrum IoT i komunikat przychodzący do punktu końcowego Centrum zdarzeń, w milisekundach|
|d2c.endpoints.egress.serviceBusQueues|Komunikaty dostarczone do punktów końcowych z kolejką usługi Service Bus|Licznik|Łącznie|Liczba wiadomości kolejką usługi Service Bus punkty końcowe zostały pomyślnie zapisane|
|d2c.endpoints.latency.serviceBusQueues|Czas oczekiwania na wiadomość dla punktów końcowych z kolejką usługi Service Bus|Milisekundy|Średnia|Średnie opóźnienie między komunikat przychodzący z Centrum IoT i komunikat przychodzący do punktu końcowego kolejką usługi Service Bus, w milisekundach|
|d2c.endpoints.egress.serviceBusTopics|Komunikaty dostarczone do punktów końcowych temat magistrali usług|Licznik|Łącznie|Liczba wiadomości zostały pomyślnie zapisane punkty końcowe temat magistrali usług|
|d2c.endpoints.latency.serviceBusTopics|Opóźnienie komunikat dla punktów końcowych temat magistrali usług|Milisekundy|Średnia|Średnie opóźnienie między komunikat przychodzący z Centrum IoT i komunikat przychodzący do punktu końcowego Service Bus tematu w milisekundach|
|d2c.endpoints.egress.builtIn.events|Wiadomość dostarczona do wbudowanej punktu końcowego (wiadomości/zdarzeń)|Licznik|Łącznie|Liczba komunikatów wbudowanym punktem końcowym (wiadomości/zdarzeń) zostały pomyślnie zapisane|
|d2c.endpoints.latency.builtIn.events|Czas oczekiwania wiadomości na wbudowanym punktem końcowym (wiadomości/zdarzeń)|Milisekundy|Średnia|Średnie opóźnienie między komunikat przychodzący z Centrum IoT i transfer danych przychodzących wiadomości na wbudowanym punktem końcowym (wiadomości/zdarzeń), w milisekundach |
|d2c.twin.read.success|Pomyślne dwie odczytuje z urządzeń|Licznik|Łącznie|Liczba wszystkich pomyślnych odczyty dwie inicjowanych przez urządzenie.|
|d2c.twin.read.failure|Nie powiodło się dwie odczyty z urządzeń|Licznik|Łącznie|Liczba wszystkich nie odczyty dwie inicjowanych przez urządzenie.|
|d2c.twin.read.size|Rozmiar odpowiedzi odczytów dwie z urządzeń|Bajty|Średnia|Średnia, min i max z wszystkie udane, inicjowanych przez urządzenie dwie odczytów.|
|d2c.twin.update.success|Pomyślne dwie aktualizacje z urządzeń|Licznik|Łącznie|Liczba wszystkich pomyślnych aktualizacji dwie inicjowanych przez urządzenie.|
|d2c.twin.update.failure|Nie powiodło się dwie aktualizacje z urządzeń|Licznik|Łącznie|Liczba wszystkich nie aktualizacje dwie inicjowanych przez urządzenie.|
|d2c.twin.update.size|Rozmiar aktualizacji dwie z urządzeń|Bajty|Średnia|Średnia, minimum i maksymalny rozmiar wszystkich pomyślnych, inicjowanych przez urządzenie dwie aktualizacji.|
|c2d.methods.success|Pomyślne bezpośrednie wywołania metod|Licznik|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniego.|
|c2d.Methods.failure|Nie powiodło się bezpośrednie wywołania metod|Licznik|Łącznie|Liczba wszystkich nie wywołania metody bezpośredniego.|
|c2d.methods.requestSize|Rozmiar żądania bezpośrednie wywołania metod|Bajty|Średnia|Średnia, min i max wszystkich pomyślnych żądań metoda bezpośrednia.|
|c2d.methods.responseSize|Rozmiar odpowiedzi bezpośrednie wywołania metod|Bajty|Średnia|Średnia, minimum i maksimum wszystkie udane metoda bezpośrednia odpowiedzi.|
|c2d.twin.read.success|Pomyślne dwie odczytuje z zaplecza|Licznik|Łącznie|Liczba wszystkich pomyślnie zainicjował zakończenia wstecz dwie odczytuje.|
|c2d.twin.read.failure|Nie powiodło się dwie odczytuje z zaplecza|Licznik|Łącznie|Liczba wszystkich nie odczyty wstecz zakończenia inicjowane przez dwie.|
|c2d.twin.read.size|Rozmiar odpowiedzi odczytów dwie z wewnętrznego|Bajty|Średnia|Średnia, min i max z wszystkie udane, Wstecz zakończenia inicjowane przez dwie odczytów.|
|c2d.twin.update.success|Pomyślne dwie aktualizacje z wewnętrznego|Licznik|Łącznie|Liczba wszystkich pomyślnych aktualizacji inicjowane zakończenia wstecz dwie.|
|c2d.twin.update.failure|Nie powiodło się dwie aktualizacje z wewnętrznego|Licznik|Łącznie|Liczba wszystkich nie powiodło się wstecz zakończenia inicjowane przez dwie aktualizacje.|
|c2d.twin.update.size|Rozmiar aktualizacji dwie z wewnętrznego|Bajty|Średnia|Średnia, minimum i maksymalny rozmiar wszystkich pomyślnych, Wstecz zakończenia inicjowane przez dwie aktualizacji.|
|twinQueries.success|Dwie pomyślne zapytania|Licznik|Łącznie|Liczba wszystkich zapytań dwie powiodło się.|
|twinQueries.failure|Nie powiodło się dwie zapytań|Licznik|Łącznie|Liczba wszystkich zapytań dwie nie powiodło się.|
|twinQueries.resultSize|Rozmiar wyników zapytania dwie|Bajty|Średnia|Średnia, minimum i maksymalny rozmiar wyników wszystkich zapytań dwie powiodło się.|
|jobs.createTwinUpdateJob.success|Pomyślne utworzenie kont dwie aktualizacji zadań|Licznik|Łącznie|Liczba wszystkich pomyślnym utworzeniu zadania aktualizacji dwie.|
|jobs.createTwinUpdateJob.failure|Nie powiodło się tworzenie dwie aktualizacji zadań|Licznik|Łącznie|Liczba wszystkich nie powiodło się tworzenie zadań aktualizacji dwie.|
|jobs.createDirectMethodJob.success|Pomyślne tworzenia zadań wywołania — metoda|Licznik|Łącznie|Liczba wszystkich pomyślnym utworzeniu zadania wywołania metody bezpośredniego.|
|jobs.createDirectMethodJob.failure|Nie powiodło się tworzenie zadań wywołania — metoda|Licznik|Łącznie|Liczba wszystkich nie powiodło się tworzenie zadań wywołania metody bezpośredniego.|
|jobs.listJobs.success|Pomyślnych wywołań do listy zadań|Licznik|Łącznie|Liczba wszystkich pomyślnych wywołań do listy zadań.|
|jobs.listJobs.failure|Nie powiodło się wywołania do listy zadań|Licznik|Łącznie|Liczba zakończonych niepowodzeniem wywołania do listy zadań.|
|jobs.cancelJob.success|Anulowane zadania powiodło się|Licznik|Łącznie|Liczba wszystkich pomyślnych wywołań, aby anulować zadanie.|
|jobs.cancelJob.failure|Anulowanie zadania nie powiodło się|Licznik|Łącznie|Liczba wszystkich wywołań nie powiodło się, aby anulować zadanie.|
|jobs.queryJobs.success|Pomyślnie wykonane zadanie zapytań|Licznik|Łącznie|Liczba wszystkich pomyślnych wywołań do zadań zapytania.|
|jobs.queryJobs.failure|Nie powiodło się zadanie odpytuje|Licznik|Łącznie|Liczba wszystkie wywołania zakończone niepowodzeniem zadania zapytania.|
|Jobs.Completed|Ukończone zadania|Licznik|Łącznie|Liczba wszystkich zakończonych zadań.|
|Jobs.failed|Zadania zakończone niepowodzeniem|Licznik|Łącznie|Liczba wszystkie zadania zakończone niepowodzeniem.|

## <a name="next-steps"></a>Kolejne kroki
Teraz, przedstawiono omówienie Centrum IoT metryki, skorzystaj z tego łącza, aby dowiedzieć się więcej o zarządzaniu Centrum IoT Azure:

* [Operacje monitorowania][lnk-monitor]

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Przewodnik dewelopera Centrum IoT][lnk-devguide]
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
