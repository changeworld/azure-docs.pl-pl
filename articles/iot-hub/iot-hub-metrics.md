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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705738"
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
|d2c.telemetry.ingress.allProtocol|Próby wysłania komunikatów telemetrycznych|Liczba|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury, które próbowano wysłać do centrum IoT Hub|Brak|
|d2c.telemetry.ingress.success|Wysłane komunikaty telemetryczne|Liczba|Łącznie|Liczba pomyślnie wysłanych komunikatów telemetrycznych z urządzenia do chmury do centrum IoT Hub|Brak|
|c2d.commands.egress.complete.success|Zakończono dostarczanie komunikatów C2D|Liczba|Łącznie|Liczba dostaw komunikatów z chmury do urządzenia ukończonych pomyślnie przez urządzenie|Brak|
|c2d.commands.egress.abandon.success|Porzucone komunikaty C2D|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia porzuconych przez urządzenie|Brak|
|c2d.commands.egress.reject.success|Odrzucone komunikaty C2D|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia odrzuconych przez urządzenie|Brak|
|C2DMessagesExpired|Komunikaty C2D wygasły (wersja zapoznawcza)|Liczba|Łącznie|Liczba wygasłych komunikatów z chmury do urządzenia|Brak|
|Devices. totalDevices|Łączna liczba urządzeń (przestarzałe)|Liczba|Łącznie|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Brak|
|devices.connectedDevices.allProtocol|Połączone urządzenia (przestarzałe) |Liczba|Łącznie|Liczba urządzeń podłączonych do centrum IoT Hub|Brak|
|d2c.telemetry.egress.success|Routing: dostarczono komunikaty telemetryczne|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wszystkich punktów końcowych używających routingu IoT Hub. Jeśli komunikat jest kierowany do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia. Jeśli wiadomość jest przekazywana do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia.|Brak|
|d2c.telemetry.egress.dropped|Routing: porzucone komunikaty telemetryczne |Liczba|Łącznie|Liczba porzuconych komunikatów przez IoT Hub Routing ze względu na martwe punkty końcowe. Ta wartość nie zlicza komunikatów dostarczonych do trasy rezerwowej, ponieważ opuszczone wiadomości nie są tam dostarczane.|Brak|
|d2c.telemetry.egress.orphaned|Routing: oddzielone komunikaty telemetryczne |Liczba|Łącznie|Liczba oddzielonych komunikatów przez IoT Hub Routing, ponieważ nie są one zgodne z żadną regułą routingu (w tym regułą rezerwową). |Brak|
|d2c.telemetry.egress.invalid|Routing: komunikaty telemetryczne są niezgodne|Liczba|Łącznie|Liczba przypadków, gdy Routing IoT Hub nie mógł dostarczyć komunikatów z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|Brak|
|d2c.telemetry.egress.fallback|Routing: komunikaty dostarczane do powrotu|Liczba|Łącznie|Liczba IoT Hub komunikatów dostarczonych przez funkcję routingu do punktu końcowego skojarzonego z trasą rezerwową.|Brak|
|d2c.endpoints.egress.eventHubs|Routing: komunikaty dostarczane do centrum zdarzeń|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych centrum zdarzeń w usłudze IoT Hub Routing.|Brak|
|d2c.endpoints.latency.eventHubs|Routing: opóźnienie komunikatu dla centrum zdarzeń|MS|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących w punkcie końcowym centrum zdarzeń.|Brak|
|d2c.endpoints.egress.serviceBusQueues|Routing: komunikaty dostarczone do kolejki Service Bus|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do Service Bus punktów końcowych kolejki w usłudze IoT Hub Routing.|Brak|
|d2c.endpoints.latency.serviceBusQueues|Routing: opóźnienie komunikatu dla kolejki usługi Service Bus|MS|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i telemetrii komunikatów przychodzących do punktu końcowego kolejki Service Bus.|Brak|
|d2c.endpoints.egress.serviceBusTopics|Routing: komunikaty dostarczane do Service Bus tematu|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów przez IoT Hub Routing do Service Bus punktów końcowych tematu.|Brak|
|d2c.endpoints.latency.serviceBusTopics|Routing: opóźnienie komunikatu dla Service Bus tematu|MS|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym tematu Service Bus.|Brak|
|d2c.endpoints.egress.builtIn.events|Routing: komunikaty dostarczane do komunikatów/zdarzeń|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wbudowanego punktu końcowego (komunikaty/zdarzenia) IoT Hub Routing.|Brak|
|d2c.endpoints.latency.builtIn.events|Routing: opóźnienie komunikatów dla komunikatów/zdarzeń|MS|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w wbudowanym punkcie końcowym (komunikaty/zdarzenia).|Brak|
|d2c.endpoints.egress.storage|Routing: komunikaty dostarczane do magazynu|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych usługi Routing IoT Hub.|Brak|
|d2c.endpoints.latency.storage|Routing: opóźnienie komunikatu dla magazynu|MS|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym magazynu.|Brak|
|d2c.endpoints.egress.storage.bytes|Routing: dane dostarczone do magazynu|Bajty|Łącznie|Ilość danych (w bajtach) IoT Hub Routing dostarczany do punktów końcowych magazynu.|Brak|
|d2c.endpoints.egress.storage.blobs|Routing: obiekty blob dostarczone do magazynu|Liczba|Łącznie|Ile razy usługa Routing IoT Hub dostarczać obiekty blob do punktów końcowych magazynu.|Brak|
|EventGridDeliveries|Dostawy Event Grid (wersja zapoznawcza)|Liczba|Łącznie|Liczba zdarzeń IoT Hub opublikowanych do Event Grid. Użyj wymiaru wynik dla liczby żądań zakończonych powodzeniem i niepowodzeniem. Wymiar EventType przedstawia typ zdarzenia (https://aka.ms/ioteventgrid).|Identyfikator<br/>Wynika<br/>Typ zdarzenia|
|EventGridLatency|Opóźnienie Event Grid (wersja zapoznawcza)|MS|Średnia|Średnie opóźnienie (w milisekundach) od momentu wygenerowania zdarzenia usługi IoT Hub po opublikowaniu zdarzenia w Event Grid. Ta liczba jest średnia między wszystkimi typami zdarzeń. Użyj wymiaru EventType, aby zobaczyć opóźnienie określonego typu zdarzenia.|Identyfikator<br/>Typ zdarzenia|
|d2c.twin.read.success|Pomyślne odczyty sznurów z urządzeń|Liczba|Łącznie|Liczba wszystkich udanych operacji zainicjowanych przez urządzenie.|Brak|
|d2c.twin.read.failure|Zakończone niepowodzeniem odczyty bliźniaczej reprezentacji z urządzeń|Liczba|Łącznie|Liczba wszystkich nieudanych operacji zainicjowanych przez urządzenie.|Brak|
|d2c.twin.read.size|Rozmiar odpowiedzi odczytów bliźniaczej reprezentacji z urządzeń|Bajty|Średnia|Średnia, minimum i maksimum wszystkich udanych zainicjowanych przez urządzenia sznurów.|Brak|
|d2c.twin.update.success|Pomyślne aktualizacje bliźniaczych urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych przez urządzenie.|Brak|
|d2c.twin.update.failure|Zakończone niepowodzeniem aktualizacje bliźniaczej reprezentacji z urządzeń|Liczba|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji dwuosiowych zainicjowanych przez urządzenie.|Brak|
|d2c.twin.update.size|Rozmiar aktualizacji bliźniaczych z urządzeń|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu dwuosiowego zainicjowanego przez urządzenie.|Brak|
|c2d.methods.success|Pomyślne wywołania metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|Brak|
|C2D. Methods. Failure|Zakończone niepowodzeniem wywołania metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich wywołań metod bezpośrednich zakończonych niepowodzeniem.|Brak|
|c2d.methods.requestSize|Rozmiar żądania wywołań metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich pomyślnych żądań metody bezpośredniej.|Brak|
|c2d.methods.responseSize|Rozmiar odpowiedzi wywołań metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich zakończonych powodzeniem odpowiedzi metody bezpośredniej.|Brak|
|c2d.twin.read.success|Pomyślne odczyty sznurów z zaplecza|Liczba|Łącznie|Liczba wszystkich zakończonych powodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Brak|
|c2d.twin.read.failure|Zakończone niepowodzeniem odczyty bliźniaczej reprezentacji z zaplecza|Liczba|Łącznie|Liczba wszystkich zakończonych niepowodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Brak|
|c2d.twin.read.size|Rozmiar odpowiedzi odczytów bliźniaczej reprezentacji z zaplecza|Bajty|Średnia|Średnia, minimum i maksimum wszystkich udanych zainicjowanych z powrotem sznurów danych.|Brak|
|c2d.twin.update.success|Pomyślne aktualizacje bliźniaczej reprezentacji z zaplecza|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Brak|
|c2d.twin.update.failure|Zakończone niepowodzeniem aktualizacje bliźniaczej reprezentacji z zaplecza|Liczba|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Brak|
|c2d.twin.update.size|Rozmiar aktualizacji przędzy od zaplecza|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu sznurka zainicjowanych z powrotem.|Brak|
|twinQueries.success|Pomyślne zapytania bliźniaczy|Liczba|Łącznie|Liczba wszystkich udanych zapytań bliźniaczych.|Brak|
|twinQueries.failure|Zakończone niepowodzeniem zapytania o bliźniacze reprezentacje|Liczba|Łącznie|Liczba wszystkich zakończonych niepowodzeniem zapytań bliźniaczych.|Brak|
|twinQueries.resultSize|Rozmiar wyniku zapytań bliźniaczych|Bajty|Średnia|Średnia, minimalna i maksymalna wielkość wyniku wszystkich udanych zapytań bliźniaczych.|Brak|
|jobs.createTwinUpdateJob.success|Pomyślne utworzenie dwuosiowych zadań aktualizacji|Liczba|Łącznie|Liczba wszystkich udanych tworzenia zadań aktualizacji z przędzą.|Brak|
|jobs.createTwinUpdateJob.failure|Zakończone niepowodzeniem zadania utworzenia bliźniaczych aktualizacji|Liczba|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań aktualizacji z przędzą.|Brak|
|jobs.createDirectMethodJob.success|Pomyślne utworzenie zadań wywołania metody|Liczba|Łącznie|Liczba wszystkich pomyślnych operacji tworzenia zadań wywołania metody bezpośredniej.|Brak|
|jobs.createDirectMethodJob.failure|Zakończone niepowodzeniem zadania utworzenia wywołania metody|Liczba|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań wywołania metody bezpośredniej.|Brak|
|jobs.listJobs.success|Pomyślne wywołania do zadań na liście|Liczba|Łącznie|Liczba wszystkich udanych wywołań do listy zadań.|Brak|
|jobs.listJobs.failure|Zakończone niepowodzeniem zadania wywołania listy|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wyświetlenia listy zadań.|Brak|
|jobs.cancelJob.success|Pomyślne anulowania zadań|Liczba|Łącznie|Liczba wszystkich udanych wywołań do anulowania zadania.|Brak|
|jobs.cancelJob.failure|Zakończone niepowodzeniem anulowania zadań|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu anulowania zadania.|Brak|
|jobs.queryJobs.success|Pomyślne zapytania dotyczące zadań|Liczba|Łącznie|Liczba wszystkich udanych wywołań do zadań zapytań.|Brak|
|jobs.queryJobs.failure|Zakończone niepowodzeniem zapytania o zadania|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wykonywania zapytań dotyczących zadań.|Brak|
|zadania. ukończone|Zadania zakończone|Liczba|Łącznie|Liczba wszystkich ukończonych zadań.|Brak|
|zadania. Niepowodzenie|Zadania zakończone niepowodzeniem|Liczba|Łącznie|Liczba wszystkich zadań zakończonych niepowodzeniem.|Brak|
|d2c.telemetry.ingress.sendThrottle|Liczba błędów ograniczania przepustowości|Liczba|Łącznie|Liczba błędów ograniczania z powodu ograniczeń przepływności urządzenia|Brak|
|dailyMessageQuotaUsed|Całkowita liczba użytych komunikatów|Liczba|Średnia|Całkowita liczba użytych komunikatów. Jest to skumulowana wartość, która jest resetowana do zera o godzinie 00:00 czasu każdego dnia.|Brak|
|deviceDataUsage|Całkowite użycie danych urządzenia|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|Brak|
|deviceDataUsageV2|Całkowite użycie danych urządzenia (wersja zapoznawcza)|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|Brak|
|totalDeviceCount|Łączna liczba urządzeń (wersja zapoznawcza)|Liczba|Średnia|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Brak|
|connectedDeviceCount|Połączone urządzenia (wersja zapoznawcza)|Liczba|Średnia|Liczba urządzeń podłączonych do centrum IoT Hub|Brak|
|konfiguracje|Metryki konfiguracji|Liczba|Łącznie|Metryki dla operacji konfiguracji|Brak|

## <a name="next-steps"></a>Następne kroki

Teraz, po zapoznaniu się z omówieniem metryk IoT Hub, Skorzystaj z tego linku, aby dowiedzieć się więcej na temat zarządzania usługą Azure IoT Hub:

* [Konfigurowanie dzienników diagnostycznych](iot-hub-monitor-resource-health.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Przewodnik dla deweloperów IoT Hub](iot-hub-devguide.md)

* [Wdrażanie AI na urządzeniach brzegowych za pomocą Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
