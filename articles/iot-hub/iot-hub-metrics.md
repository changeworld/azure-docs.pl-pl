---
title: Używanie metryk do monitorowania IoT Hub platformy Azure | Microsoft Docs
description: Jak oceniać i monitorować ogólną kondycję centrów IoT przy użyciu metryk IoT Hub platformy Azure.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: ec8a00460b4a750339f929eb6879ac6eb63cac8e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284436"
---
# <a name="understand-iot-hub-metrics"></a>Informacje o metrykach IoT Hub

Metryki IoT Hub zapewniają lepsze dane dotyczące stanu zasobów usługi Azure IoT w ramach subskrypcji platformy Azure. Metryki IoT Hub umożliwiają ocenę ogólnej kondycji usługi IoT Hub i podłączonych do niej urządzeń. Dane statystyczne związane z użytkownikiem są ważne, ponieważ pomagają zobaczyć, co się dzieje z Centrum IoT Hub, i pomóc w wykorzystaniu problemów głównych, bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

Metryki są domyślnie włączone. Możesz wyświetlić metryki IoT Hub z Azure Portal.

> [!NOTE]
> Za pomocą metryk IoT Hub można wyświetlać informacje o urządzeniach Plug and Play IoT podłączonych do IoT Hub. Urządzenia Plug and Play IoT są częścią [publicznej wersji zapoznawczej iot Plug and Play](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>Jak wyświetlić metryki IoT Hub

1. Utwórz centrum IoT. Instrukcje dotyczące sposobu tworzenia Centrum IoT można znaleźć w przewodniku wysyłanie danych [telemetrycznych z urządzenia do IoT Hub](quickstart-send-telemetry-dotnet.md) .

2. Otwórz blok Centrum IoT. Z tego miejsca kliknij pozycję **metryki**.
   
    ![Zrzut ekranu przedstawiający, gdzie znajduje się opcja metryki na stronie zasobów IoT Hub](./media/iot-hub-metrics/enable-metrics-1.png)

3. W bloku metryk można wyświetlić metryki Centrum IoT i utworzyć niestandardowe widoki metryk. 
   
    ![Zrzut ekranu przedstawiający stronę metryki dla IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Możesz wysłać dane metryk do punktu końcowego Event Hubs lub konta usługi Azure Storage, klikając pozycję **Ustawienia diagnostyczne**, a następnie **Dodaj ustawienie diagnostyczne**

   ![Zrzut ekranu przedstawiający przycisk ustawień diagnostycznych](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub metryki i sposoby ich używania

IoT Hub zawiera kilka metryk, które umożliwiają przedstawienie informacji o kondycji centrum oraz łącznej liczbie połączonych urządzeń. Możesz połączyć informacje z wielu metryk, aby malować większy obraz stanu Centrum IoT. W poniższej tabeli opisano metryki poszczególnych ścieżek Centrum IoT oraz sposób, w jaki każda Metryka odnosi się do ogólnego stanu Centrum IoT Hub.

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Próby wysłania komunikatów telemetrycznych|Licznik|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury, które próbowano wysłać do centrum IoT Hub|None|
|d2c.telemetry.ingress.success|Wysłane komunikaty telemetryczne|Licznik|Łącznie|Liczba pomyślnie wysłanych komunikatów telemetrycznych z urządzenia do chmury do centrum IoT Hub|None|
|c2d.commands.egress.complete.success|Zakończono dostarczanie komunikatów C2D|Licznik|Łącznie|Liczba dostaw komunikatów z chmury do urządzenia ukończonych pomyślnie przez urządzenie|None|
|c2d.commands.egress.abandon.success|Porzucone komunikaty C2D|Licznik|Łącznie|Liczba komunikatów z chmury do urządzenia porzuconych przez urządzenie|None|
|c2d.commands.egress.reject.success|Odrzucone komunikaty C2D|Licznik|Łącznie|Liczba komunikatów z chmury do urządzenia odrzuconych przez urządzenie|None|
|C2DMessagesExpired|Komunikaty C2D wygasły (wersja zapoznawcza)|Licznik|Łącznie|Liczba wygasłych komunikatów z chmury do urządzenia|None|
|Devices. totalDevices|Łączna liczba urządzeń (przestarzałe)|Licznik|Łącznie|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|None|
|devices.connectedDevices.allProtocol|Podłączone urządzenia (przestarzałe) |Licznik|Łącznie|Liczba urządzeń podłączonych do centrum IoT Hub|None|
|d2c.telemetry.egress.success|Routing: dostarczono komunikaty telemetryczne|Licznik|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wszystkich punktów końcowych używających routingu IoT Hub. Jeśli komunikat jest kierowany do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia. Jeśli wiadomość jest przekazywana do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia.|None|
|d2c.telemetry.egress.dropped|Routing: porzucone komunikaty telemetryczne |Licznik|Łącznie|Liczba porzuconych komunikatów przez IoT Hub Routing ze względu na martwe punkty końcowe. Ta wartość nie zlicza komunikatów dostarczonych do trasy rezerwowej, ponieważ opuszczone wiadomości nie są tam dostarczane.|None|
|d2c.telemetry.egress.orphaned|Routing: oddzielone komunikaty telemetryczne |Licznik|Łącznie|Liczba oddzielonych komunikatów przez IoT Hub Routing, ponieważ nie są one zgodne z żadną regułą routingu (w tym regułą rezerwową). |None|
|d2c.telemetry.egress.invalid|Routing: komunikaty telemetryczne są niezgodne|Licznik|Łącznie|Liczba przypadków, gdy Routing IoT Hub nie mógł dostarczyć komunikatów z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|None|
|d2c.telemetry.egress.fallback|Routing: komunikaty dostarczane do powrotu|Licznik|Łącznie|Liczba IoT Hub komunikatów dostarczonych przez funkcję routingu do punktu końcowego skojarzonego z trasą rezerwową.|None|
|d2c.endpoints.egress.eventHubs|Routing: komunikaty dostarczane do centrum zdarzeń|Licznik|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych centrum zdarzeń w usłudze IoT Hub Routing.|None|
|d2c.endpoints.latency.eventHubs|Routing: opóźnienie komunikatu dla centrum zdarzeń|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących w punkcie końcowym centrum zdarzeń.|None|
|d2c.endpoints.egress.serviceBusQueues|Routing: komunikaty dostarczone do kolejki Service Bus|Licznik|Łącznie|Liczba pomyślnie dostarczonych komunikatów do Service Bus punktów końcowych kolejki w usłudze IoT Hub Routing.|None|
|d2c.endpoints.latency.serviceBusQueues|Routing: opóźnienie komunikatu dla kolejki Service Bus|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i telemetrii komunikatów przychodzących do punktu końcowego kolejki Service Bus.|None|
|d2c.endpoints.egress.serviceBusTopics|Routing: komunikaty dostarczane do Service Bus tematu|Licznik|Łącznie|Liczba pomyślnie dostarczonych komunikatów przez IoT Hub Routing do Service Bus punktów końcowych tematu.|None|
|d2c.endpoints.latency.serviceBusTopics|Routing: opóźnienie komunikatu dla Service Bus tematu|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym tematu Service Bus.|None|
|d2c.endpoints.egress.builtIn.events|Routing: komunikaty dostarczane do komunikatów/zdarzeń|Licznik|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wbudowanego punktu końcowego (komunikaty/zdarzenia) IoT Hub Routing.|None|
|d2c.endpoints.latency.builtIn.events|Routing: opóźnienie komunikatów dla komunikatów/zdarzeń|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w wbudowanym punkcie końcowym (komunikaty/zdarzenia).|None|
|d2c.endpoints.egress.storage|Routing: komunikaty dostarczane do magazynu|Licznik|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych usługi Routing IoT Hub.|None|
|d2c.endpoints.latency.storage|Routing: opóźnienie komunikatu dla magazynu|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym magazynu.|None|
|d2c.endpoints.egress.storage.bytes|Routing: dane dostarczane do magazynu|Bajty|Łącznie|Ilość danych (w bajtach) IoT Hub Routing dostarczany do punktów końcowych magazynu.|None|
|d2c.endpoints.egress.storage.blobs|Routing: obiekty blob dostarczane do magazynu|Licznik|Łącznie|Ile razy usługa Routing IoT Hub dostarczać obiekty blob do punktów końcowych magazynu.|None|
|EventGridDeliveries|Dostawy Event Grid (wersja zapoznawcza)|Licznik|Łącznie|Liczba zdarzeń IoT Hub opublikowanych do Event Grid. Użyj wymiaru wynik dla liczby żądań zakończonych powodzeniem i niepowodzeniem. Wymiar EventType przedstawia typ zdarzenia (https://aka.ms/ioteventgrid).|Identyfikator<br/>Wynika<br/>Klasę|
|EventGridLatency|Opóźnienie Event Grid (wersja zapoznawcza)|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) od momentu wygenerowania zdarzenia usługi IoT Hub po opublikowaniu zdarzenia w Event Grid. Ta liczba jest średnia między wszystkimi typami zdarzeń. Użyj wymiaru EventType, aby zobaczyć opóźnienie określonego typu zdarzenia.|Identyfikator<br/>Klasę|
|d2c.twin.read.success|Pomyślne odczyty sznurów z urządzeń|Licznik|Łącznie|Liczba wszystkich udanych operacji zainicjowanych przez urządzenie.|None|
|d2c.twin.read.failure|Nieudane odczyty sznurów z urządzeń|Licznik|Łącznie|Liczba wszystkich nieudanych operacji zainicjowanych przez urządzenie.|None|
|d2c.twin.read.size|Rozmiar odpowiedzi dla sznurów odczytanych z urządzeń|Bajty|Średnia|Średnia, minimum i maksimum wszystkich udanych zainicjowanych przez urządzenia sznurów.|None|
|d2c.twin.update.success|Pomyślne aktualizacje bliźniaczych urządzeń|Licznik|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych przez urządzenie.|None|
|d2c.twin.update.failure|Niepowodzenie aktualizacji bliźniaczych z urządzeń|Licznik|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji dwuosiowych zainicjowanych przez urządzenie.|None|
|d2c.twin.update.size|Rozmiar aktualizacji bliźniaczych z urządzeń|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu dwuosiowego zainicjowanego przez urządzenie.|None|
|c2d.methods.success|Pomyślne wywołania metody bezpośredniej|Licznik|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|None|
|C2D. Methods. Failure|Nieudane wywołania metody bezpośredniej|Licznik|Łącznie|Liczba wszystkich wywołań metod bezpośrednich zakończonych niepowodzeniem.|None|
|c2d.methods.requestSize|Rozmiar żądania wywołań metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich pomyślnych żądań metody bezpośredniej.|None|
|c2d.methods.responseSize|Rozmiar odpowiedzi wywołań metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich zakończonych powodzeniem odpowiedzi metody bezpośredniej.|None|
|c2d.twin.read.success|Pomyślne odczyty sznurów z zaplecza|Licznik|Łącznie|Liczba wszystkich zakończonych powodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|None|
|c2d.twin.read.failure|Nieudane odczyty sznurów z zaplecza|Licznik|Łącznie|Liczba wszystkich zakończonych niepowodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|None|
|c2d.twin.read.size|Rozmiar odpowiedzi na odwrocie od zaplecza|Bajty|Średnia|Średnia, minimum i maksimum wszystkich udanych zainicjowanych z powrotem sznurów danych.|None|
|c2d.twin.update.success|Pomyślne aktualizacje bliźniaczych z zaplecza|Licznik|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych z powrotem.|None|
|c2d.twin.update.failure|Niepowodzenie aktualizacji bliźniaczych z zaplecza|Licznik|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji typu bliźniaczych zainicjowanych z powrotem.|None|
|c2d.twin.update.size|Rozmiar aktualizacji przędzy od zaplecza|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu sznurka zainicjowanych z powrotem.|None|
|twinQueries.success|Pomyślne zapytania bliźniaczy|Licznik|Łącznie|Liczba wszystkich udanych zapytań bliźniaczych.|None|
|twinQueries.failure|Niepowodzenie zapytań bliźniaczych|Licznik|Łącznie|Liczba wszystkich zakończonych niepowodzeniem zapytań bliźniaczych.|None|
|twinQueries.resultSize|Rozmiar wyniku zapytań bliźniaczych|Bajty|Średnia|Średnia, minimalna i maksymalna wielkość wyniku wszystkich udanych zapytań bliźniaczych.|None|
|jobs.createTwinUpdateJob.success|Pomyślne utworzenie dwuosiowych zadań aktualizacji|Licznik|Łącznie|Liczba wszystkich udanych tworzenia zadań aktualizacji z przędzą.|None|
|jobs.createTwinUpdateJob.failure|Nie można utworzyć dwuosiowych zadań aktualizacji|Licznik|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań aktualizacji z przędzą.|None|
|jobs.createDirectMethodJob.success|Pomyślne utworzenie zadań wywołania metody|Licznik|Łącznie|Liczba wszystkich pomyślnych operacji tworzenia zadań wywołania metody bezpośredniej.|None|
|jobs.createDirectMethodJob.failure|Nie można utworzyć zadań wywołania metody|Licznik|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań wywołania metody bezpośredniej.|None|
|jobs.listJobs.success|Pomyślne wywołania do zadań na liście|Licznik|Łącznie|Liczba wszystkich udanych wywołań do listy zadań.|None|
|jobs.listJobs.failure|Wywołania zakończone niepowodzeniem do listy zadań|Licznik|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wyświetlenia listy zadań.|None|
|jobs.cancelJob.success|Pomyślne anulowania zadań|Licznik|Łącznie|Liczba wszystkich udanych wywołań do anulowania zadania.|None|
|jobs.cancelJob.failure|Nieudane anulowania zadań|Licznik|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu anulowania zadania.|None|
|jobs.queryJobs.success|Pomyślne zapytania dotyczące zadań|Licznik|Łącznie|Liczba wszystkich udanych wywołań do zadań zapytań.|None|
|jobs.queryJobs.failure|Nieudane kwerendy zadań|Licznik|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wykonywania zapytań dotyczących zadań.|None|
|zadania. ukończone|Ukończone zadania|Licznik|Łącznie|Liczba wszystkich ukończonych zadań.|None|
|zadania. Niepowodzenie|Zadania zakończone niepowodzeniem|Licznik|Łącznie|Liczba wszystkich zadań zakończonych niepowodzeniem.|None|
|d2c.telemetry.ingress.sendThrottle|Liczba błędów ograniczania|Licznik|Łącznie|Liczba błędów ograniczania z powodu ograniczeń przepływności urządzenia|None|
|dailyMessageQuotaUsed|Całkowita liczba użytych komunikatów|Licznik|Średnia|Całkowita liczba użytych komunikatów. Jest to skumulowana wartość, która jest resetowana do zera o godzinie 00:00 czasu każdego dnia.|None|
|deviceDataUsage|Całkowite użycie danych urządzenia|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|None|
|deviceDataUsageV2|Całkowite użycie danych urządzenia (wersja zapoznawcza)|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|None|
|totalDeviceCount|Łączna liczba urządzeń (wersja zapoznawcza)|Licznik|Średnia|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|None|
|connectedDeviceCount|Podłączone urządzenia (wersja zapoznawcza)|Licznik|Średnia|Liczba urządzeń podłączonych do centrum IoT Hub|None|
|konfiguracje|Metryki konfiguracji|Licznik|Łącznie|Metryki dla operacji konfiguracji|None|

## <a name="next-steps"></a>Następne kroki

Teraz, po zapoznaniu się z omówieniem metryk IoT Hub, Skorzystaj z tego linku, aby dowiedzieć się więcej na temat zarządzania usługą Azure IoT Hub:

* [Konfigurowanie dzienników diagnostycznych](iot-hub-monitor-resource-health.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Przewodnik dla deweloperów IoT Hub](iot-hub-devguide.md)

* [Wdrażanie AI na urządzeniach brzegowych za pomocą Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
