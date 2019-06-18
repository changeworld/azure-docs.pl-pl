---
title: Monitorowanie usługi Azure IoT Hub przy użyciu metryk | Dokumentacja firmy Microsoft
description: Sposób, aby ocenić i monitorowanie ogólnej kondycji usługi IoT Hub przy użyciu metryk usługi Azure IoT Hub.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 8eac70db0c563f102dfa0e3fcece9d4604582cce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65595654"
---
# <a name="understand-iot-hub-metrics"></a>Omówienie usługi IoT Hub metryki

Metryki usługi IoT Hub umożliwiają lepsze dane dotyczące stanu zasobów usługi Azure IoT w subskrypcji platformy Azure. Metryki usługi IoT Hub pozwalają ocenić ogólną kondycję usługi IoT Hub i dołączone do niego urządzenia. Statystyki użytkownika są ważne, ponieważ ułatwiają one sprawdzanie, co się dzieje z IoT hub i pomocy główną przyczynę problemów związanych z bez konieczności skontaktuj się z działem pomocy technicznej platformy Azure.

Metryki są domyślnie włączone. Możesz wyświetlić metryki usługi IoT Hub w witrynie Azure portal.

## <a name="how-to-view-iot-hub-metrics"></a>Jak wyświetlić metryki usługi IoT Hub

1. Utwórz centrum IoT. Instrukcje można znaleźć na temat tworzenia Centrum IoT hub w [wysyłanie danych telemetrycznych z urządzenia do usługi IoT Hub](quickstart-send-telemetry-dotnet.md) przewodnik.

2. Otwórz blok Centrum IoT hub. W tym miejscu, kliknij przycisk **metryki**.
   
    ![Zrzut ekranu przedstawiający, gdzie jest opcja metryki na stronie zasobów usługi IoT Hub](./media/iot-hub-metrics/enable-metrics-1.png)

3. W bloku metryk możesz wyświetlać metryki dla usługi IoT hub i tworzenie niestandardowych widoków metryk. 
   
    ![Zrzut ekranu przedstawiający stronę metryki dla usługi IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Użytkownik może wysłać dane metryk do punktu końcowego usługi Event Hubs lub konta usługi Azure Storage, klikając **ustawień diagnostycznych**, następnie **Dodaj ustawienie diagnostyczne**

   ![Zrzut ekranu przedstawiający, gdzie jest przycisk ustawień diagnostycznych](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Metryki usługi IoT Hub i sposobu ich używania

IoT Hub udostępnia kilka metryk, aby Przegląd kondycji Centrum i całkowita liczba podłączonych urządzeń. Możesz połączyć informacje z wielu metryk do malowania większy obraz stanu usługi IoT hub. W poniższej tabeli opisano metryk, który śledzi każde Centrum IoT hub i jak wszystkie metryki odnosi się do ogólnego stanu usługi IoT hub.

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|d2c<br>.telemetry<br>.ingress.<br>allProtocol|Próby wysłania komunikatu telemetrii|Count|Łącznie|Liczba komunikatów danych telemetrycznych z urządzenia do chmury podjęto próbę wysłania do Centrum IoT hub|Nie wymiarów|
|d2c<br>.telemetry<br>.ingress<br>.SUCCESS|Komunikaty telemetryczne wysyłane|Count|Łącznie|Liczba komunikatów danych telemetrycznych z urządzenia do chmury pomyślnie wysłany do usługi IoT hub|Nie wymiarów|
|c2d<br>.Commands<br>.egress<br>.Complete<br>.SUCCESS|Polecenia zakończona|Count|Łącznie|Wiele poleceń z chmury do urządzenia zakończyło się powodzeniem, urządzenie|Nie wymiarów|
|c2d<br>.Commands<br>.egress<br>.Abandon<br>.SUCCESS|Polecenia porzucone|Licznik|Łącznie|Wiele poleceń chmura urządzenie porzucone przez urządzenie|Nie wymiarów|
|c2d<br>.Commands<br>.egress<br>.reject<br>.SUCCESS|Polecenia odrzucone|Licznik|Łącznie|Wiele poleceń chmura urządzenie odrzucone przez urządzenie|Nie wymiarów|
|urządzenia<br>.totalDevices|Łączna liczba urządzeń (przestarzałe)|Count|Łącznie|Liczba urządzeń zarejestrowanych do Centrum IoT hub|Nie wymiarów|
|urządzenia<br>.connectedDevices<br>.allProtocol|Połączone urządzenia (przestarzałe) |Count|Łącznie|Liczba urządzeń podłączonych do Centrum IoT hub|Nie wymiarów|
|d2c<br>.telemetry<br>.egress<br>.SUCCESS|Routingu: wydana komunikaty telemetryczne|Count|Łącznie|Liczba przypadków, gdy wiadomości zostały pomyślnie dostarczone do wszystkich punktów końcowych przy użyciu routingu w usłudze IoT Hub. Jeśli komunikat jest kierowany do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego skutecznej. Jeśli komunikat jest dostarczane do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego skutecznej.|Nie wymiarów|
|d2c<br>.telemetry<br>.egress<br>.dropped|Routingu: porzucone komunikaty telemetryczne |Count|Łącznie|Liczba przypadków, gdy komunikaty zostały porzucone przez usługę IoT Hub routing z powodu braku punktów końcowych. Ta wartość nie jest liczony komunikaty dostarczane do trasy rezerwowej, gdy porzuconych wiadomości nie są dostarczane istnieje.|Nie wymiarów|
|d2c<br>.telemetry<br>.egress<br>.orphaned|Routingu: porzucone komunikaty telemetryczne |Count|Łącznie|Liczba przypadków, gdy komunikaty zostały oddzielone kierując usługi IoT Hub, ponieważ nie pasują do żadnych reguł routingu (w tym reguły rezerwowego). |Nie wymiarów|
|d2c<br>.telemetry<br>.egress<br>.invalid|Routing: komunikaty telemetryczne niezgodne|Count|Łącznie|Liczba razy routingu w usłudze IoT Hub nie można dostarczać komunikatów z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|Nie wymiarów|
|d2c<br>.telemetry<br>.egress<br>.fallback|Routing: komunikaty dostarczane do rezerwowego|Count|Łącznie|Liczba przypadków, dostarczenia komunikatów w Centrum IoT Hub routingu do skojarzonej z trasą rezerwowego punktu końcowego.|Nie wymiarów|
|d2c<br>.Endpoints<br>.egress<br>.eventHubs|Routing: komunikaty dostarczane do Centrum zdarzeń|Count|Łącznie|Liczba przypadków, gdy Centrum IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych Centrum zdarzeń.|Nie wymiarów|
|d2c<br>.Endpoints<br>.latency<br>.eventHubs|Routing: komunikat o opóźnieniu dla Centrum zdarzeń|MS|Średnia|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i ruch przychodzący komunikat do punktu końcowego Centrum zdarzeń.|Nie wymiarów|
|d2c<br>.Endpoints<br>.egress<br>.serviceBusQueues|Routing: komunikaty dostarczane do kolejki usługi Service Bus|Count|Łącznie|Liczba przypadków, gdy usługi IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych kolejki usługi Service Bus.|Nie wymiarów|
|d2c<br>.Endpoints<br>.latency<br>.serviceBusQueues|Routing: opóźnienie komunikat do kolejki usługi Service Bus|MS|Średnia|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych komunikat do punktu końcowego kolejki usługi Service Bus.|Nie wymiarów|
|d2c<br>.Endpoints<br>.egress<br>.serviceBusTopics|Routing: komunikaty dostarczane do tematu usługi Service Bus|Count|Łącznie|Liczba przypadków, gdy Centrum IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych z tematu usługi Service Bus.|Nie wymiarów|
|d2c<br>.Endpoints<br>.latency<br>.serviceBusTopics|Routing: opóźnienie komunikat do tematu usługi Service Bus|MS|Średnia|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych komunikat do punktu końcowego tematu usługi Service Bus.|Nie wymiarów|
|d2c<br>.Endpoints<br>.egress<br>.builtIn<br>.events|Routing: komunikaty dostarczane do komunikaty/zdarzenia|Count|Łącznie|Liczba przypadków, gdy Centrum IoT Hub routingu pomyślnie dostarczone wiadomości do wbudowany punkt końcowy (komunikaty/zdarzenia).|Nie wymiarów|
|d2c<br>.Endpoints<br>.latency<br>.builtIn.events|Routing: komunikat o opóźnieniu komunikaty/zdarzenia|MS|Średnia|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych wiadomości do wbudowany punkt końcowy (komunikaty/zdarzenia).|Nie wymiarów|
|d2c<br>.Endpoints<br>.egress<br>.storage|Routing: komunikaty dostarczane do magazynu|Count|Łącznie|Liczba przypadków, gdy usługi IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych magazynu.|Nie wymiarów|
|d2c<br>.Endpoints<br>.latency<br>.storage|Routing: komunikat o opóźnieniu magazynu|MS|Średnia|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych komunikat do punktu końcowego magazynu.|Nie wymiarów|
|d2c<br>.Endpoints<br>.egress<br>.storage<br>.Bytes|Routing: dane dostarczane do magazynu|Bajty|Łącznie|Ilość danych (w bajtach) routingu w usłudze IoT Hub dostarczone do punktów końcowych magazynu.|Nie wymiarów|
|d2c<br>.Endpoints<br>.egress<br>.storage<br>.blobs|Routing: obiektów blob jest dostarczane do magazynu|Count|Łącznie|Liczba przypadków, gdy routingu w usłudze IoT Hub dostarczane obiekty BLOB do przechowywania punktów końcowych.|Nie wymiarów|
|EventGridDeliveries|Event Grid dostaw (wersja zapoznawcza)|Count|Łącznie|Liczba żądań usługi IoT Hub emitować zdarzenia do usługi Event Grid. Liczba ta obejmuje udane i nieudane żądania. Wymiar wyniku na użytek numer innego typu odpowiedzi. Aby zobaczyć, gdzie pochodzą żądania, należy użyć w wymiarze typ zdarzenia.|Wynik, typ zdarzenia|
|EventGridLatency|Czas oczekiwania na zdarzenie siatki (wersja zapoznawcza)|MS|Średnia|Średnie opóźnienie (w milisekundach) między danych zdarzeń przychodzących do usługi IoT Hub i zdarzeń przychodzących do usługi Event Grid. Ta liczba jest średnia pomiędzy wszystkich typów zdarzeń. Użyj w wymiarze typ zdarzenia, aby zobaczyć opóźnienie określonych typach zdarzenia.|Typ zdarzenia|
|d2c<br>.Twin<br>.read<br>.SUCCESS|Pomyślne bliźniacze odczyty z urządzenia|Count|Łącznie|Liczba wszystkich pomyślnie zainicjował urządzenia bliźniacze odczyty na dysku.|Nie wymiarów|
|d2c<br>.Twin<br>.read<br>.failure|Nie powiodło się bliźniacze odczyty z urządzenia|Licznik|Łącznie|Zainicjowane przez urządzenie bliźniacze odczyty liczbę wszystkich, nie powiodło się.|Nie wymiarów|
|d2c<br>.Twin<br>.read<br>.size|Rozmiar odpowiedzi bliźniacze odczyty z urządzenia|Bajty|Średnia|Średnią, minimalna i maksymalna elementu wszystkie udane, inicjowane przez urządzenie bliźniaczej reprezentacji odczytów.|Nie wymiarów|
|d2c<br>.Twin<br>.Update<br>.SUCCESS|Liczba aktualizacji bliźniaczej reprezentacji pomyślnie z urządzeń|Licznik|Łącznie|Liczba wszystkich pomyślnie zainicjował urządzenia aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|d2c<br>.Twin<br>.Update<br>.failure|Niepowodzenie aktualizacji bliźniaczej reprezentacji urządzenia|Count|Łącznie|Liczba wszystkich nie powiodło się aktualizacji inicjowane przez urządzenie bliźniaczej reprezentacji.|Nie wymiarów|
|d2c<br>.Twin<br>.Update<br>.size|Rozmiar aktualizacji bliźniaczej reprezentacji urządzenia|Bajty|Średnia|Średnia, minimum i maksymalny rozmiar wszystkich pomyślnie, inicjowane przez urządzenie bliźniaczej reprezentacji aktualizacji.|Nie wymiarów|
|c2d<br>.Methods<br>.SUCCESS|Pomyślne bezpośrednie wywołania metod|Licznik|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|Nie wymiarów|
|c2d<br>.Methods<br>.failure|Niepowodzenie wywołania metody bezpośredniej|Count|Łącznie|Liczba wszystkich nie wywołania metody bezpośredniej.|Nie wymiarów|
|c2d<br>.Methods<br>.requestSize|Rozmiar żądania wywołania metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich pomyślnych żądań metody bezpośredniej.|Nie wymiarów|
|c2d<br>.Methods<br>.responseSize|Rozmiar odpowiedzi z wywołania metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich pomyślne odpowiedzi metody bezpośredniej.|Nie wymiarów|
|c2d<br>.Twin<br>.read<br>.SUCCESS|Pomyślne bliźniacze odczyty z zaplecza|Count|Łącznie|Liczba wszystkich pomyślnie zainicjował zakończenia kopii bliźniacze odczyty na dysku.|Nie wymiarów|
|c2d<br>.Twin<br>.read<br>.failure|Nie powiodło się bliźniacze odczyty z zaplecza|Licznik|Łącznie|Zainicjowano zakończenia kopii bliźniacze odczyty liczbę wszystkich, nie powiodło się.|Nie wymiarów|
|c2d<br>.Twin<br>.read<br>.size|Rozmiar odpowiedzi bliźniacze odczyty z zaplecza|Bajty|Średnia|Średnią, minimalna i maksymalna elementu wszystkie udane, zainicjowanych zakończenia wstecz bliźniaczej reprezentacji odczytów.|Nie wymiarów|
|c2d<br>.Twin<br>.Update<br>.SUCCESS|Liczba aktualizacji bliźniaczej reprezentacji pomyślnie z zaplecza|Count|Łącznie|Liczba wszystkich pomyślnie zainicjował zakończenia kopii aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|c2d<br>.Twin<br>.Update<br>.failure|Liczba aktualizacji bliźniaczej reprezentacji nie powiodło się z zaplecza|Count|Łącznie|Liczba wszystkich nie powiodło się aktualizacji inicjowane przez zakończenia wstecz bliźniaczej reprezentacji.|Nie wymiarów|
|c2d<br>.Twin<br>.Update<br>.size|Rozmiar aktualizacji bliźniaczej reprezentacji z zaplecza|Bajty|Średnia|Średnia, minimum i maksymalny rozmiar wszystkich pomyślnie, zainicjowanych zakończenia wstecz bliźniaczej reprezentacji aktualizacji.|Nie wymiarów|
|TwinQueries<br>.SUCCESS|Zapytania dotyczące bliźniaczych reprezentacji pomyślne|Count|Łącznie|Liczba wszystkich zapytań bliźniaczych reprezentacji się pomyślnie.|Nie wymiarów|
|TwinQueries<br>.failure|Zapytania dotyczące bliźniaczych reprezentacji nie powiodło się|Count|Łącznie|Liczba wszystkich zapytań bliźniaczych reprezentacji nie powiodło się.|Nie wymiarów|
|TwinQueries<br>.resultSize|Rozmiar wyników zapytań bliźniaczych reprezentacji|Bajty|Średnia|Średnia, minimum i maksymalny rozmiar wyników wszystkich zapytań bliźniaczych pomyślne.|Nie wymiarów|
|Zadania<br>.createTwinUpdateJob<br>.SUCCESS|Pomyślne utworzenie kont zadań aktualizacji bliźniaczej reprezentacji|Count|Łącznie|Liczba wszystkich pomyślne utworzenie zadania aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|Zadania<br>.createTwinUpdateJob<br>.failure|Nie powiodło się tworzenie zadania aktualizacji bliźniaczej reprezentacji|Count|Łącznie|Liczba wszystkich nie powiodło się utworzenie zadania aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|Zadania<br>.createDirectMethodJob<br>.SUCCESS|Pomyślne utworzenie kont zadań wywołania metody|Licznik|Łącznie|Liczba wszystkich pomyślnym utworzeniu zadania wywołania metody bezpośredniej.|Nie wymiarów|
|Zadania<br>.createDirectMethodJob<br>.failure|Nie powiodło się tworzenie zadań wywołania metody|Count|Łącznie|Liczba wszystkich nie powiodło się tworzenie zadań wywołania metody bezpośredniej.|Nie wymiarów|
|Zadania<br>.listJobs<br>.SUCCESS|Zakończonych powodzeniem wywołań do wyświetlania listy zadań|Count|Łącznie|Liczba wszystkich zakończonych powodzeniem wywołań do wyświetlania listy zadań.|Nie wymiarów|
|Zadania<br>.listJobs<br>.failure|Niepowodzenie wywołania do wyświetlania listy zadań|Count|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem wyświetlania listy zadań.|Nie wymiarów|
|Zadania<br>.cancelJob<br>.SUCCESS|Anulowane zadania pomyślne|Count|Łącznie|Liczba wszystkich pomyślnych wywołań, aby anulować zadanie.|Nie wymiarów|
|Zadania<br>.cancelJob<br>.failure|Anulowane zadania nie powiodło się|Licznik|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem można anulować zadania.|Nie wymiarów|
|Zadania<br>.queryJobs<br>.SUCCESS|Pomyślnie wykonane zadanie zapytań|Count|Łącznie|Liczba wszystkich zakończonych powodzeniem wywołań do zadania kwerendy.|Nie wymiarów|
|Zadania<br>.queryJobs<br>.failure|Nie powiodło się zadanie odpytuje|Count|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem zadania kwerendy.|Nie wymiarów|
|Zadania<br>.Completed|Ukończone zadania|Count|Łącznie|Liczba wszystkich zakończonych zadań.|Nie wymiarów|
|Zadania<br>.failed|Zadania zakończone niepowodzeniem|Count|Łącznie|Liczba wszystkich zadań zakończonych niepowodzeniem.|Nie wymiarów|
|d2c<br>.telemetry<br>.ingress<br>.sendThrottle|Liczby błędów ograniczania dostępności|Count|Łącznie|Ogranicza liczby błędów ograniczania dostępności z powodu przepływności urządzenia|Nie wymiarów|
|dailyMessage<br>QuotaUsed|Całkowita liczba komunikatów używane|Count|Średnia|Liczba całkowita liczba wiadomości używanych obecnie. Jest to wartość zbiorcza, która jest resetowany do zera UTC 00:00 każdego dnia.|Nie wymiarów|
|deviceDataUsage|Użycie danych łączna liczba urządzeń|Bajty|Łącznie|Bajty przesłane do i z wszelkie urządzenia podłączone do usługi IotHub|Nie wymiarów|
|totalDeviceCount|Łączna liczba urządzeń (wersja zapoznawcza)|Licznik|Średnia|Liczba urządzeń zarejestrowanych do Centrum IoT hub|Nie wymiarów|
|Połączone<br>DeviceCount|Połączone urządzenia (wersja zapoznawcza)|Licznik|Średnia|Liczba urządzeń podłączonych do Centrum IoT hub|Nie wymiarów|
|Konfiguracje|Konfiguracja metryki|Count|Łącznie|Metryki dla operacji konfiguracji|Nie wymiarów|

## <a name="next-steps"></a>Kolejne kroki

Teraz, po zapoznaniu z omówieniem usługi IoT Hub metryki skorzystaj z tego linku, aby dowiedzieć się więcej na temat zarządzania usługi Azure IoT Hub:

* [Monitorowanie operacji](iot-hub-operations-monitoring.md)

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych za pomocą usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
