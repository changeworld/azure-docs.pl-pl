---
title: Monitorowanie usługi Azure IoT Hub przy użyciu metryk | Dokumentacja firmy Microsoft
description: Sposób, aby ocenić i monitorowanie ogólnej kondycji usługi IoT Hub przy użyciu metryk usługi Azure IoT Hub.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: nberdy
ms.openlocfilehash: b41458f0201c46b99c09d0bfffd219743a36ad50
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186759"
---
# <a name="understand-iot-hub-metrics"></a>Omówienie usługi IoT Hub metryki
Metryki usługi IoT Hub umożliwiają lepsze dane dotyczące stanu zasobów usługi Azure IoT w subskrypcji platformy Azure. Metryki usługi IoT Hub pozwalają ocenić ogólną kondycję usługi IoT Hub i dołączone do niego urządzenia. Statystyki użytkownika są ważne, ponieważ ułatwiają one sprawdzanie, co się dzieje z IoT hub i pomocy główną przyczynę problemów związanych z bez konieczności skontaktuj się z działem pomocy technicznej platformy Azure.

Metryki są domyślnie włączone. Możesz wyświetlić metryki usługi IoT Hub w witrynie Azure portal.

## <a name="how-to-view-iot-hub-metrics"></a>Jak wyświetlić metryki usługi IoT Hub
1. Tworzenie Centrum IoT. Instrukcje można znaleźć na temat tworzenia Centrum IoT hub w [wprowadzenie] [ lnk-get-started] przewodnik.
2. Otwórz blok Centrum IoT hub. W tym miejscu, kliknij przycisk **metryki**.
   
    ![][1]
3. W bloku metryk możesz wyświetlać metryki dla usługi IoT hub i tworzenie niestandardowych widoków metryk. Użytkownik może wysłać dane metryk do punktu końcowego usługi Event Hubs lub konta usługi Azure Storage, klikając **ustawień diagnostycznych**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Metryki usługi IoT Hub i sposobu ich używania
IoT Hub udostępnia kilka metryk, aby Przegląd kondycji Centrum i całkowita liczba podłączonych urządzeń. Możesz połączyć informacje z wielu metryk do malowania większy obraz stanu usługi IoT hub. W poniższej tabeli opisano metryk, który śledzi każde Centrum IoT hub i jak wszystkie metryki odnosi się do ogólnego stanu usługi IoT hub.

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Próby wysłania komunikatu telemetrii|Licznik|Łącznie|Liczba komunikatów danych telemetrycznych z urządzenia do chmury podjęto próbę wysłania do Centrum IoT hub|
|d2c.telemetry.ingress.success|Komunikaty telemetryczne wysyłane|Licznik|Łącznie|Liczba komunikatów danych telemetrycznych z urządzenia do chmury pomyślnie wysłany do usługi IoT hub|
|c2d.commands.egress.complete.success|Polecenia zakończona|Licznik|Łącznie|Wiele poleceń z chmury do urządzenia zakończyło się powodzeniem, urządzenie|
|c2d.commands.egress.abandon.success|Polecenia porzucone|Licznik|Łącznie|Wiele poleceń chmura urządzenie porzucone przez urządzenie|
|c2d.commands.egress.reject.success|Polecenia odrzucone|Licznik|Łącznie|Wiele poleceń chmura urządzenie odrzucone przez urządzenie|
|devices.totalDevices|Łączna liczba urządzeń|Licznik|Łącznie|Liczba urządzeń zarejestrowanych do Centrum IoT hub|
|devices.connectedDevices.allProtocol|Połączone urządzenia|Licznik|Łącznie|Liczba urządzeń podłączonych do Centrum IoT hub|
|d2c.telemetry.egress.success|Komunikaty telemetryczne dostarczane|Licznik|Łącznie|Liczba wiadomości zostały pomyślnie zapisane do punktów końcowych (łącznie)|
|d2c.telemetry.egress.dropped|Porzucone komunikaty|Licznik|Łącznie|Liczba komunikatów porzucony nie pasowało żadnych tras i trasy rezerwowego zostało wyłączone|
|d2c.telemetry.egress.orphaned|Oddzielone wiadomości|Licznik|Łącznie|Liczba wiadomości, które nie pasują wszystkie trasy, łącznie z rezerwowego trasy|
|d2c.telemetry.egress.invalid|Nieprawidłowe komunikaty|Licznik|Łącznie|Liczba wiadomości, które nie są dostarczane z powodu niezgodności z punktem końcowym|
|d2c.telemetry.egress.fallback|Komunikaty rezerwowego warunek dopasowania|Licznik|Łącznie|Liczba komunikatów zapisanych do rezerwowego punktu końcowego|
|d2c.endpoints.egress.eventHubs|Wiadomości dostarczone do punktów końcowych Centrum zdarzeń|Licznik|Łącznie|Liczba wiadomości zostały pomyślnie zapisane do punktów końcowych Centrum zdarzeń|
|d2c.endpoints.latency.eventHubs|Czas oczekiwania na komunikat dla punktów końcowych Centrum zdarzeń|Milisekundy|Średnia|Średnie opóźnienie między komunikatów przychodzących do usługi IoT hub i ruch przychodzący komunikat do punktu końcowego Centrum zdarzeń, w milisekundach|
|d2c.endpoints.egress.serviceBusQueues|Wiadomości dostarczone do punktów końcowych kolejki usługi Service Bus|Licznik|Łącznie|Liczba wiadomości zostały pomyślnie zapisane do kolejki usługi Service Bus punktów końcowych|
|d2c.endpoints.latency.serviceBusQueues|Czas oczekiwania na komunikat dla punktów końcowych kolejki usługi Service Bus|Milisekundy|Średnia|Średnie opóźnienie między komunikatów przychodzących do usługi IoT hub i ruch przychodzący komunikat do punktu końcowego kolejki usługi Service Bus, w milisekundach|
|d2c.endpoints.egress.serviceBusTopics|Wiadomości dostarczone do punktów końcowych z tematu usługi Service Bus|Licznik|Łącznie|Liczba wiadomości zostały pomyślnie zapisane do punktów końcowych z tematu usługi Service Bus|
|d2c.endpoints.latency.serviceBusTopics|Czas oczekiwania na komunikat dla punktów końcowych z tematu usługi Service Bus|Milisekundy|Średnia|Średnie opóźnienie między komunikatów przychodzących do usługi IoT hub i ruch przychodzący komunikat do punktu końcowego tematu usługi Service Bus, w milisekundach|
|d2c.endpoints.egress.builtIn.events|Komunikaty dostarczane do wbudowany punkt końcowy (komunikaty/zdarzenia)|Licznik|Łącznie|Liczba wiadomości zostały pomyślnie zapisane wbudowany punkt końcowy (komunikaty/zdarzenia)|
|d2c.endpoints.latency.builtIn.events|Komunikat o opóźnieniu wbudowany punkt końcowy (komunikaty/zdarzenia)|Milisekundy|Średnia|Średnie opóźnienie między komunikatów przychodzących do usługi IoT hub i komunikatów przychodzących do wbudowany punkt końcowy (komunikaty/zdarzenia), w milisekundach |
|d2c.twin.read.success|Pomyślne bliźniacze odczyty z urządzenia|Licznik|Łącznie|Liczba wszystkich pomyślnie zainicjował urządzenia bliźniacze odczyty na dysku.|
|d2c.twin.read.failure|Nie powiodło się bliźniacze odczyty z urządzenia|Licznik|Łącznie|Zainicjowane przez urządzenie bliźniacze odczyty liczbę wszystkich, nie powiodło się.|
|d2c.twin.read.size|Rozmiar odpowiedzi bliźniacze odczyty z urządzenia|Bajty|Średnia|Średnią, minimalna i maksymalna elementu wszystkie udane, inicjowane przez urządzenie bliźniaczej reprezentacji odczytów.|
|d2c.twin.update.success|Liczba aktualizacji bliźniaczej reprezentacji pomyślnie z urządzeń|Licznik|Łącznie|Liczba wszystkich pomyślnie zainicjował urządzenia aktualizacji bliźniaczej reprezentacji.|
|d2c.twin.update.failure|Niepowodzenie aktualizacji bliźniaczej reprezentacji urządzenia|Licznik|Łącznie|Liczba wszystkich nie powiodło się aktualizacji inicjowane przez urządzenie bliźniaczej reprezentacji.|
|d2c.twin.update.size|Rozmiar aktualizacji bliźniaczej reprezentacji urządzenia|Bajty|Średnia|Średnia, minimum i maksymalny rozmiar wszystkich pomyślnie, inicjowane przez urządzenie bliźniaczej reprezentacji aktualizacji.|
|c2d.methods.success|Pomyślne bezpośrednie wywołania metod|Licznik|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|
|c2d.Methods.failure|Niepowodzenie wywołania metody bezpośredniej|Licznik|Łącznie|Liczba wszystkich nie wywołania metody bezpośredniej.|
|c2d.methods.requestSize|Rozmiar żądania wywołania metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich pomyślnych żądań metody bezpośredniej.|
|c2d.methods.responseSize|Rozmiar odpowiedzi z wywołania metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich pomyślne odpowiedzi metody bezpośredniej.|
|c2d.twin.read.success|Pomyślne bliźniacze odczyty z zaplecza|Licznik|Łącznie|Liczba wszystkich pomyślnie zainicjował zakończenia kopii bliźniacze odczyty na dysku.|
|c2d.twin.read.failure|Nie powiodło się bliźniacze odczyty z zaplecza|Licznik|Łącznie|Zainicjowano zakończenia kopii bliźniacze odczyty liczbę wszystkich, nie powiodło się.|
|c2d.twin.read.size|Rozmiar odpowiedzi bliźniacze odczyty z zaplecza|Bajty|Średnia|Średnią, minimalna i maksymalna elementu wszystkie udane, zainicjowanych zakończenia wstecz bliźniaczej reprezentacji odczytów.|
|c2d.twin.update.success|Liczba aktualizacji bliźniaczej reprezentacji pomyślnie z zaplecza|Licznik|Łącznie|Liczba wszystkich pomyślnie zainicjował zakończenia kopii aktualizacji bliźniaczej reprezentacji.|
|c2d.twin.update.failure|Liczba aktualizacji bliźniaczej reprezentacji nie powiodło się z zaplecza|Licznik|Łącznie|Liczba wszystkich nie powiodło się aktualizacji inicjowane przez zakończenia wstecz bliźniaczej reprezentacji.|
|c2d.twin.update.size|Rozmiar aktualizacji bliźniaczej reprezentacji z zaplecza|Bajty|Średnia|Średnia, minimum i maksymalny rozmiar wszystkich pomyślnie, zainicjowanych zakończenia wstecz bliźniaczej reprezentacji aktualizacji.|
|twinQueries.success|Zapytania dotyczące bliźniaczych reprezentacji pomyślne|Licznik|Łącznie|Liczba wszystkich zapytań bliźniaczych reprezentacji się pomyślnie.|
|twinQueries.failure|Zapytania dotyczące bliźniaczych reprezentacji nie powiodło się|Licznik|Łącznie|Liczba wszystkich zapytań bliźniaczych reprezentacji nie powiodło się.|
|twinQueries.resultSize|Rozmiar wyników zapytań bliźniaczych reprezentacji|Bajty|Średnia|Średnia, minimum i maksymalny rozmiar wyników wszystkich zapytań bliźniaczych pomyślne.|
|jobs.createTwinUpdateJob.success|Pomyślne utworzenie kont zadań aktualizacji bliźniaczej reprezentacji|Licznik|Łącznie|Liczba wszystkich pomyślne utworzenie zadania aktualizacji bliźniaczej reprezentacji.|
|jobs.createTwinUpdateJob.failure|Nie powiodło się tworzenie zadania aktualizacji bliźniaczej reprezentacji|Licznik|Łącznie|Liczba wszystkich nie powiodło się utworzenie zadania aktualizacji bliźniaczej reprezentacji.|
|jobs.createDirectMethodJob.success|Pomyślne utworzenie kont zadań wywołania metody|Licznik|Łącznie|Liczba wszystkich pomyślnym utworzeniu zadania wywołania metody bezpośredniej.|
|jobs.createDirectMethodJob.failure|Nie powiodło się tworzenie zadań wywołania metody|Licznik|Łącznie|Liczba wszystkich nie powiodło się tworzenie zadań wywołania metody bezpośredniej.|
|jobs.listJobs.success|Zakończonych powodzeniem wywołań do wyświetlania listy zadań|Licznik|Łącznie|Liczba wszystkich zakończonych powodzeniem wywołań do wyświetlania listy zadań.|
|jobs.listJobs.failure|Niepowodzenie wywołania do wyświetlania listy zadań|Licznik|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem wyświetlania listy zadań.|
|jobs.cancelJob.success|Anulowane zadania pomyślne|Licznik|Łącznie|Liczba wszystkich pomyślnych wywołań, aby anulować zadanie.|
|jobs.cancelJob.failure|Anulowane zadania nie powiodło się|Licznik|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem można anulować zadania.|
|jobs.queryJobs.success|Pomyślnie wykonane zadanie zapytań|Licznik|Łącznie|Liczba wszystkich zakończonych powodzeniem wywołań do zadania kwerendy.|
|jobs.queryJobs.failure|Nie powiodło się zadanie odpytuje|Licznik|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem zadania kwerendy.|
|Jobs.Completed|Ukończone zadania|Licznik|Łącznie|Liczba wszystkich zakończonych zadań.|
|Jobs.failed|Zadania zakończone niepowodzeniem|Licznik|Łącznie|Liczba wszystkich zadań zakończonych niepowodzeniem.|

## <a name="next-steps"></a>Kolejne kroki
Teraz, po zapoznaniu z omówieniem usługi IoT Hub metryki skorzystaj z tego linku, aby dowiedzieć się więcej na temat zarządzania usługi Azure IoT Hub:

* [Monitorowanie operacji][lnk-monitor]

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub][lnk-devguide]
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: quickstart-send-telemetry-dotnet.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
