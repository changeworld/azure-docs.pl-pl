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
ms.openlocfilehash: b1c9854d794c88c28c82a4b6e40c81a29552223a
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984079"
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

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Próby wysłania komunikatu telemetrii|Licznik|Łącznie|Liczba komunikatów danych telemetrycznych z urządzenia do chmury podjęto próbę wysłania do Centrum IoT hub|Nie wymiarów|
|d2c.telemetry.ingress.success|Komunikaty telemetryczne wysyłane|Licznik|Łącznie|Liczba komunikatów danych telemetrycznych z urządzenia do chmury pomyślnie wysłany do usługi IoT hub|Nie wymiarów|
|c2d.commands.egress.complete.success|Polecenia zakończona|Licznik|Łącznie|Wiele poleceń z chmury do urządzenia zakończyło się powodzeniem, urządzenie|Nie wymiarów|
|c2d.commands.egress.abandon.success|Polecenia porzucone|Licznik|Łącznie|Wiele poleceń chmura urządzenie porzucone przez urządzenie|Nie wymiarów|
|c2d.commands.egress.reject.success|Polecenia odrzucone|Licznik|Łącznie|Wiele poleceń chmura urządzenie odrzucone przez urządzenie|Nie wymiarów|
|devices.totalDevices|Łączna liczba urządzeń (przestarzałe)|Licznik|Łącznie|Liczba urządzeń zarejestrowanych do Centrum IoT hub|Nie wymiarów|
|devices.connectedDevices.allProtocol|Połączone urządzenia (przestarzałe) |Licznik|Łącznie|Liczba urządzeń podłączonych do Centrum IoT hub|Nie wymiarów|
|d2c.telemetry.egress.success|Routingu: wydana komunikaty telemetryczne|Licznik|Łącznie|Liczba przypadków, gdy wiadomości zostały pomyślnie dostarczone do wszystkich punktów końcowych przy użyciu routingu w usłudze IoT Hub. Jeśli komunikat jest kierowany do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego skutecznej. Jeśli komunikat jest dostarczane do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego skutecznej.|Nie wymiarów|
|d2c.telemetry.egress.dropped|Routingu: porzucone komunikaty telemetryczne |Licznik|Łącznie|Liczba przypadków, gdy komunikaty zostały porzucone przez usługę IoT Hub routing z powodu braku punktów końcowych. Ta wartość nie jest liczony komunikaty dostarczane do trasy rezerwowej, gdy porzuconych wiadomości nie są dostarczane istnieje.|Nie wymiarów|
|d2c.telemetry.egress.orphaned|Routingu: porzucone komunikaty telemetryczne |Licznik|Łącznie|Liczba przypadków, gdy komunikaty zostały oddzielone kierując usługi IoT Hub, ponieważ nie pasują do żadnych reguł routingu (w tym reguły rezerwowego). |Nie wymiarów|
|d2c.telemetry.egress.invalid|Routing: komunikaty telemetryczne niezgodne|Licznik|Łącznie|Liczba razy routingu w usłudze IoT Hub nie można dostarczać komunikatów z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|Nie wymiarów|
|d2c.telemetry.egress.fallback|Routing: komunikaty dostarczane do rezerwowego|Licznik|Łącznie|Liczba przypadków, dostarczenia komunikatów w Centrum IoT Hub routingu do skojarzonej z trasą rezerwowego punktu końcowego.|Nie wymiarów|
|d2c.endpoints.egress.eventHubs|Routing: komunikaty dostarczane do Centrum zdarzeń|Licznik|Łącznie|Liczba przypadków, gdy Centrum IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych Centrum zdarzeń.|Nie wymiarów|
|d2c.endpoints.latency.eventHubs|Routing: komunikat o opóźnieniu dla Centrum zdarzeń|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i ruch przychodzący komunikat do punktu końcowego Centrum zdarzeń.|Nie wymiarów|
|d2c.endpoints.egress.serviceBusQueues|Routing: komunikaty dostarczane do kolejki usługi Service Bus|Licznik|Łącznie|Liczba przypadków, gdy usługi IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych kolejki usługi Service Bus.|Nie wymiarów|
|d2c.endpoints.latency.serviceBusQueues|Routing: opóźnienie komunikat do kolejki usługi Service Bus|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych komunikat do punktu końcowego kolejki usługi Service Bus.|Nie wymiarów|
|d2c.endpoints.egress.serviceBusTopics|Routing: komunikaty dostarczane do tematu usługi Service Bus|Licznik|Łącznie|Liczba przypadków, gdy Centrum IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych z tematu usługi Service Bus.|Nie wymiarów|
|d2c.endpoints.latency.serviceBusTopics|Routing: opóźnienie komunikat do tematu usługi Service Bus|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych komunikat do punktu końcowego tematu usługi Service Bus.|Nie wymiarów|
|d2c.endpoints.egress.builtIn.events|Routing: komunikaty dostarczane do komunikaty/zdarzenia|Licznik|Łącznie|Liczba przypadków, gdy Centrum IoT Hub routingu pomyślnie dostarczone wiadomości do wbudowany punkt końcowy (komunikaty/zdarzenia).|Nie wymiarów|
|d2c.endpoints.latency.builtIn.events|Routing: komunikat o opóźnieniu komunikaty/zdarzenia|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych wiadomości do wbudowany punkt końcowy (komunikaty/zdarzenia).|Nie wymiarów|
|d2c.endpoints.egress.storage|Routing: komunikaty dostarczane do magazynu|Licznik|Łącznie|Liczba przypadków, gdy usługi IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych magazynu.|Nie wymiarów|
|d2c.endpoints.latency.storage|Routing: komunikat o opóźnieniu magazynu|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych komunikat do punktu końcowego magazynu.|Nie wymiarów|
|d2c.endpoints.egress.storage.bytes|Routing: dane dostarczane do magazynu|Bajty|Łącznie|Ilość danych (w bajtach) routingu w usłudze IoT Hub dostarczone do punktów końcowych magazynu.|Nie wymiarów|
|d2c.endpoints.egress.storage.blobs|Routing: obiektów blob jest dostarczane do magazynu|Licznik|Łącznie|Liczba przypadków, gdy routingu w usłudze IoT Hub dostarczane obiekty BLOB do przechowywania punktów końcowych.|Nie wymiarów|
|d2c.twin.read.success|Pomyślne bliźniacze odczyty z urządzenia|Licznik|Łącznie|Liczba wszystkich pomyślnie zainicjował urządzenia bliźniacze odczyty na dysku.|Nie wymiarów|
|d2c.twin.read.failure|Nie powiodło się bliźniacze odczyty z urządzenia|Licznik|Łącznie|Zainicjowane przez urządzenie bliźniacze odczyty liczbę wszystkich, nie powiodło się.|Nie wymiarów|
|d2c.twin.read.size|Rozmiar odpowiedzi bliźniacze odczyty z urządzenia|Bajty|Średnia|Średnią, minimalna i maksymalna elementu wszystkie udane, inicjowane przez urządzenie bliźniaczej reprezentacji odczytów.|Nie wymiarów|
|d2c.twin.update.success|Liczba aktualizacji bliźniaczej reprezentacji pomyślnie z urządzeń|Licznik|Łącznie|Liczba wszystkich pomyślnie zainicjował urządzenia aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|d2c.twin.update.failure|Niepowodzenie aktualizacji bliźniaczej reprezentacji urządzenia|Licznik|Łącznie|Liczba wszystkich nie powiodło się aktualizacji inicjowane przez urządzenie bliźniaczej reprezentacji.|Nie wymiarów|
|d2c.twin.update.size|Rozmiar aktualizacji bliźniaczej reprezentacji urządzenia|Bajty|Średnia|Średnia, minimum i maksymalny rozmiar wszystkich pomyślnie, inicjowane przez urządzenie bliźniaczej reprezentacji aktualizacji.|Nie wymiarów|
|c2d.methods.success|Pomyślne bezpośrednie wywołania metod|Licznik|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|Nie wymiarów|
|c2d.Methods.failure|Niepowodzenie wywołania metody bezpośredniej|Licznik|Łącznie|Liczba wszystkich nie wywołania metody bezpośredniej.|Nie wymiarów|
|c2d.methods.requestSize|Rozmiar żądania wywołania metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich pomyślnych żądań metody bezpośredniej.|Nie wymiarów|
|c2d.methods.responseSize|Rozmiar odpowiedzi z wywołania metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich pomyślne odpowiedzi metody bezpośredniej.|Nie wymiarów|
|c2d.twin.read.success|Pomyślne bliźniacze odczyty z zaplecza|Licznik|Łącznie|Liczba wszystkich pomyślnie zainicjował zakończenia kopii bliźniacze odczyty na dysku.|Nie wymiarów|
|c2d.twin.read.failure|Nie powiodło się bliźniacze odczyty z zaplecza|Licznik|Łącznie|Zainicjowano zakończenia kopii bliźniacze odczyty liczbę wszystkich, nie powiodło się.|Nie wymiarów|
|c2d.twin.read.size|Rozmiar odpowiedzi bliźniacze odczyty z zaplecza|Bajty|Średnia|Średnią, minimalna i maksymalna elementu wszystkie udane, zainicjowanych zakończenia wstecz bliźniaczej reprezentacji odczytów.|Nie wymiarów|
|c2d.twin.update.success|Liczba aktualizacji bliźniaczej reprezentacji pomyślnie z zaplecza|Licznik|Łącznie|Liczba wszystkich pomyślnie zainicjował zakończenia kopii aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|c2d.twin.update.failure|Liczba aktualizacji bliźniaczej reprezentacji nie powiodło się z zaplecza|Licznik|Łącznie|Liczba wszystkich nie powiodło się aktualizacji inicjowane przez zakończenia wstecz bliźniaczej reprezentacji.|Nie wymiarów|
|c2d.twin.update.size|Rozmiar aktualizacji bliźniaczej reprezentacji z zaplecza|Bajty|Średnia|Średnia, minimum i maksymalny rozmiar wszystkich pomyślnie, zainicjowanych zakończenia wstecz bliźniaczej reprezentacji aktualizacji.|Nie wymiarów|
|twinQueries.success|Zapytania dotyczące bliźniaczych reprezentacji pomyślne|Licznik|Łącznie|Liczba wszystkich zapytań bliźniaczych reprezentacji się pomyślnie.|Nie wymiarów|
|twinQueries.failure|Zapytania dotyczące bliźniaczych reprezentacji nie powiodło się|Licznik|Łącznie|Liczba wszystkich zapytań bliźniaczych reprezentacji nie powiodło się.|Nie wymiarów|
|twinQueries.resultSize|Rozmiar wyników zapytań bliźniaczych reprezentacji|Bajty|Średnia|Średnia, minimum i maksymalny rozmiar wyników wszystkich zapytań bliźniaczych pomyślne.|Nie wymiarów|
|jobs.createTwinUpdateJob.success|Pomyślne utworzenie kont zadań aktualizacji bliźniaczej reprezentacji|Licznik|Łącznie|Liczba wszystkich pomyślne utworzenie zadania aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|jobs.createTwinUpdateJob.failure|Nie powiodło się tworzenie zadania aktualizacji bliźniaczej reprezentacji|Licznik|Łącznie|Liczba wszystkich nie powiodło się utworzenie zadania aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|jobs.createDirectMethodJob.success|Pomyślne utworzenie kont zadań wywołania metody|Licznik|Łącznie|Liczba wszystkich pomyślnym utworzeniu zadania wywołania metody bezpośredniej.|Nie wymiarów|
|jobs.createDirectMethodJob.failure|Nie powiodło się tworzenie zadań wywołania metody|Licznik|Łącznie|Liczba wszystkich nie powiodło się tworzenie zadań wywołania metody bezpośredniej.|Nie wymiarów|
|jobs.listJobs.success|Zakończonych powodzeniem wywołań do wyświetlania listy zadań|Licznik|Łącznie|Liczba wszystkich zakończonych powodzeniem wywołań do wyświetlania listy zadań.|Nie wymiarów|
|jobs.listJobs.failure|Niepowodzenie wywołania do wyświetlania listy zadań|Licznik|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem wyświetlania listy zadań.|Nie wymiarów|
|jobs.cancelJob.success|Anulowane zadania pomyślne|Licznik|Łącznie|Liczba wszystkich pomyślnych wywołań, aby anulować zadanie.|Nie wymiarów|
|jobs.cancelJob.failure|Anulowane zadania nie powiodło się|Licznik|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem można anulować zadania.|Nie wymiarów|
|jobs.queryJobs.success|Pomyślnie wykonane zadanie zapytań|Licznik|Łącznie|Liczba wszystkich zakończonych powodzeniem wywołań do zadania kwerendy.|Nie wymiarów|
|jobs.queryJobs.failure|Nie powiodło się zadanie odpytuje|Licznik|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem zadania kwerendy.|Nie wymiarów|
|Jobs.Completed|Ukończone zadania|Licznik|Łącznie|Liczba wszystkich zakończonych zadań.|Nie wymiarów|
|Jobs.failed|Zadania zakończone niepowodzeniem|Licznik|Łącznie|Liczba wszystkich zadań zakończonych niepowodzeniem.|Nie wymiarów|
|d2c.telemetry.ingress.sendThrottle|Liczby błędów ograniczania dostępności|Licznik|Łącznie|Ogranicza liczby błędów ograniczania dostępności z powodu przepływności urządzenia|Nie wymiarów|
|dailyMessageQuotaUsed|Całkowita liczba komunikatów używane|Licznik|Średnia|Liczba całkowita liczba wiadomości używanych obecnie. Jest to wartość zbiorcza, która jest resetowany do zera UTC 00:00 każdego dnia.|Nie wymiarów|
|deviceDataUsage|Łączna liczba devicedata użycie (przestarzałe)|Bajty|Łącznie|Bajty przesłane do i z wszelkie urządzenia podłączone do usługi IotHub|Nie wymiarów|
|deviceDataUsageV2|Użycie danych łączna liczba urządzeń (wersja zapoznawcza)|Bajty|Łącznie|Bajty przesłane do i z wszelkie urządzenia podłączone do usługi IotHub|Nie wymiarów|
|totalDeviceCount|Łączna liczba urządzeń (wersja zapoznawcza)|Licznik|Średnia|Liczba urządzeń zarejestrowanych do Centrum IoT hub|Nie wymiarów|
|connectedDeviceCount|Połączone urządzenia (wersja zapoznawcza)|Licznik|Średnia|Liczba urządzeń podłączonych do Centrum IoT hub|Nie wymiarów|
|Konfiguracje|Konfiguracja metryki|Licznik|Łącznie|Metryki dla operacji konfiguracji|Nie wymiarów|

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
