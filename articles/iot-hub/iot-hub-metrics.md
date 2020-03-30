---
title: Monitorowanie usługi Azure IoT Hub za pomocą metryk | Dokumenty firmy Microsoft
description: Jak używać metryk usługi Azure IoT Hub do oceny i monitorowania ogólnej kondycji centrów IoT.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: ec8a00460b4a750339f929eb6879ac6eb63cac8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284436"
---
# <a name="understand-iot-hub-metrics"></a>Opis metryk usługi IoT Hub

Metryki usługi IoT Hub zapewniają lepsze dane dotyczące stanu zasobów usługi Azure IoT w ramach subskrypcji platformy Azure. Metryki usługi IoT Hub umożliwiają ocenę ogólnej kondycji usługi IoT Hub i podłączonych do niej urządzeń. Statystyki skierowane do użytkownika są ważne, ponieważ pomagają zobaczyć, co dzieje się z centrum IoT hub i pomóc przyczyn podstawowych problemów bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

Metryki są domyślnie włączone. Możesz wyświetlić metryki Usługi IoT Hub z witryny Azure portal.

> [!NOTE]
> Za pomocą metryk Usługi IoT Hub można wyświetlać informacje o urządzeniach Typu Plug and Play IoT podłączonych do centrum IoT Hub. Urządzenia IoT Plug and Play są częścią [publicznego podglądu IoT Plug and Play.](../iot-pnp/overview-iot-plug-and-play.md)

## <a name="how-to-view-iot-hub-metrics"></a>Jak wyświetlić metryki Usługi IoT Hub

1. Utwórz centrum IoT. Instrukcje dotyczące tworzenia centrum IoT hub można znaleźć w przewodniku [Wyślij dane telemetryczne z urządzenia do centrum IoT Hub.](quickstart-send-telemetry-dotnet.md)

2. Otwórz ostrzu piasty IoT. Stamtąd kliknij **metryki**.
   
    ![Zrzut ekranu przedstawiający miejsce, w którym znajduje się opcja metryki na stronie zasobów Usługi IoT Hub](./media/iot-hub-metrics/enable-metrics-1.png)

3. Na metryki bloku, można wyświetlić metryki dla centrum IoT i utworzyć niestandardowe widoki metryki. 
   
    ![Zrzut ekranu przedstawiający stronę metryki dla usługi IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Możesz wysłać dane metryk do punktu końcowego usługi Event Hubs lub konta usługi Azure Storage, klikając **pozycję Ustawienia diagnostyki**, a następnie **Dodaj ustawienie diagnostyczne**

   ![Zrzut ekranu przedstawiający, gdzie znajduje się przycisk ustawień diagnostycznych](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Metryki Usługi IoT Hub i sposób ich używania

Centrum IoT hub udostępnia kilka metryk, które zapewniają przegląd kondycji koncentratora i całkowitej liczby podłączonych urządzeń. Można połączyć informacje z wielu metryk, aby namalować większy obraz stanu centrum IoT hub. W poniższej tabeli opisano metryki każdego centrum IoT utworów i jak każda metryka odnosi się do ogólnego stanu centrum IoT hub.

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Próby wysyłania wiadomości telemetrycznych|Liczba|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury, które próbowano wysłać do centrum IoT hub|Brak|
|d2c.telemetry.ingress.success|Wysłane wiadomości telemetryczne|Liczba|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury wysłanych pomyślnie do centrum IoT hub|Brak|
|c2d.commands.egress.complete.success|Zakończono dostarczanie wiadomości C2D|Liczba|Łącznie|Liczba dostaw komunikatów z chmury do urządzenia zakończonych pomyślnie przez urządzenie|Brak|
|c2d.commands.egress.abandon.success|Porzucone wiadomości C2D|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia porzuconych przez urządzenie|Brak|
|c2d.commands.egress.reject.success|Wiadomości C2D odrzucone|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia odrzuconych przez urządzenie|Brak|
|C2DMessagesExpired|Wiadomości C2D wygasły (wersja zapoznawcza)|Liczba|Łącznie|Liczba wygasłych komunikatów z chmury do urządzenia|Brak|
|devices.totalUrządzenia|Łączna liczba urządzeń (przestarzała)|Liczba|Łącznie|Liczba urządzeń zarejestrowanych w centrum IoT|Brak|
|devices.connectedDevices.allProtocol|Podłączone urządzenia (przestarzałe) |Liczba|Łącznie|Liczba urządzeń podłączonych do centrum IoT|Brak|
|d2c.telemetry.egress.success|Routing: dostarczane wiadomości telemetryczne|Liczba|Łącznie|Liczba wiadomości zostały pomyślnie dostarczone do wszystkich punktów końcowych przy użyciu routingu Usługi IoT Hub. Jeśli wiadomość jest kierowana do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczania. Jeśli wiadomość jest dostarczana do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczania.|Brak|
|d2c.telemetry.egress.dropped d2c.telemetry.egress.dropped d2c.telemetry.egress.dropped d2|Routing: wiadomości telemetryczne porzucone |Liczba|Łącznie|Liczba wiadomości zostały usunięte przez routing usługi IoT Hub z powodu martwych punktów końcowych. Ta wartość nie zlicza wiadomości dostarczonych do trasy rezerwowej, ponieważ porzucone wiadomości nie są tam dostarczane.|Brak|
|d2c.telemetry.egress.orphaned|Routing: wiadomości telemetryczne oddzielone |Liczba|Łącznie|Liczba wiadomości były oddzielone przez routing usługi IoT Hub, ponieważ nie pasują do żadnych reguł routingu (w tym reguły rezerwowej). |Brak|
|d2c.telemetry.egress.invalid d2c.telemetry.egress.invalid d2c.telemetry.egress.invalid d2|Routing: komunikaty telemetryczne niezgodne|Liczba|Łącznie|Liczba razy routingu Usługi IoT Hub nie można dostarczyć wiadomości z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|Brak|
|d2c.telemetry.egress.fallback|Routing: wiadomości dostarczane do rezerwowego|Liczba|Łącznie|Liczba razy routing usługi IoT Hub dostarczane wiadomości do punktu końcowego skojarzonego z trasy rezerwowej.|Brak|
|d2c.endpoints.egress.eventHubs|Routing: wiadomości dostarczane do Centrum zdarzeń|Liczba|Łącznie|Ile razy routing usługi IoT Hub pomyślnie dostarczał wiadomości do punktów końcowych usługi Event Hub.|Brak|
|d2c.endpoints.latency.eventHubs|Routing: opóźnienie komunikatów dla Centrum zdarzeń|Milisekund|Średnia|Średnie opóźnienie (milisekundy) między wejściem w błąd i transferu komunikatu do usługi IoT Hub i transferu komunikatu przychodzącego do punktu końcowego usługi Event Hub.|Brak|
|d2c.endpoints.egress.serviceBusQueues|Routing: wiadomości dostarczane do kolejki magistrali usług|Liczba|Łącznie|Ile razy routing usługi IoT Hub pomyślnie dostarczył wiadomości do punktów końcowych kolejki usługi Service Bus.|Brak|
|d2c.endpoints.latency.serviceBusQueues|Routing: opóźnienie komunikatu dla kolejki magistrali usług|Milisekund|Średnia|Średnie opóźnienie (milisekundy) między transferem przychodzącym wiadomości do usługi IoT Hub a transferem danych wejściowych wiadomości telemetrycznych do punktu końcowego kolejki usługi Service Bus.|Brak|
|d2c.endpoints.egress.serviceBusTopics|Routing: wiadomości dostarczane do tematu magistrali usług|Liczba|Łącznie|Ile razy routing usługi IoT Hub pomyślnie dostarczył wiadomości do punktów końcowych tematu usługi Service Bus.|Brak|
|d2c.endpoints.latency.serviceBusTopics|Routing: opóźnienie komunikatu dla tematu magistrali usług|Milisekund|Średnia|Średnie opóźnienie (milisekundy) między transferem przychodzącym wiadomości do usługi IoT Hub a transferem danych wejściowych wiadomości telemetrycznych do punktu końcowego tematu usługi Service Bus.|Brak|
|d2c.endpoints.egress.builtIn.events|Routing: wiadomości dostarczane do wiadomości/zdarzeń|Liczba|Łącznie|Ile razy routing usługi IoT Hub pomyślnie dostarczył wiadomości do wbudowanego punktu końcowego (wiadomości/zdarzenia).|Brak|
|d2c.endpoints.latency.builtIn.events|Routing: opóźnienie wiadomości/zdarzeń|Milisekund|Średnia|Średnie opóźnienie (milisekundy) między wejściem w błąd i transferu danych przychodzących do usługi IoT Hub i transferu danych wejściowych do wbudowanego punktu końcowego (wiadomości/zdarzenia).|Brak|
|d2c.endpoints.egress.storage|Routing: wiadomości dostarczane do magazynu|Liczba|Łącznie|Ile razy routing usługi IoT Hub pomyślnie dostarczał wiadomości do punktów końcowych magazynu.|Brak|
|d2c.endpoints.latency.storage|Routing: opóźnienie komunikatu dla magazynu|Milisekund|Średnia|Średnie opóźnienie (milisekundy) między wejściem w błąd do usługi IoT Hub a wejściem transferu danych wejściowych do punktu końcowego magazynu.|Brak|
|d2c.endpoints.egress.storage.bytes|Routing: dane dostarczane do magazynu|Bajty|Łącznie|Ilość routingu usługi IoT Hub danych dostarczonych do punktów końcowych magazynu.|Brak|
|d2c.endpoints.egress.storage.blob|Routing: obiekty blob dostarczane do magazynu|Liczba|Łącznie|Liczba czasów routingu usługi IoT Hub dostarczane obiekty blob do punktów końcowych magazynu.|Brak|
|EventGridDeliveries|Dostawy w siatce zdarzeń (wersja zapoznawcza)|Liczba|Łącznie|Liczba zdarzeń usługi IoT Hub opublikowanych w usztywnieniu zdarzeń. Użyj wymiaru Wynik dla liczby pomyślnych i nieudanych żądań. Wymiar EventType pokazuje typhttps://aka.ms/ioteventgrid)zdarzenia ( .|Resourceid<br/>Wynik<br/>Typ zdarzenia|
|EventGridLatency|Opóźnienie siatki zdarzeń (wersja zapoznawcza)|Milisekund|Średnia|Średnie opóźnienie (milisekundy) od momentu wygenerowania zdarzenia Iot Hub do momentu opublikowania zdarzenia w uścisku zdarzeń. Liczba ta jest średnią między wszystkimi typami zdarzeń. Użyj eventtype wymiar, aby zobaczyć opóźnienie określonego typu zdarzenia.|Resourceid<br/>Typ zdarzenia|
|d2c.twin.read.success|Udane bliźniacze odczyty z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych odczytów bliźniaczych zainicjowanych przez urządzenie.|Brak|
|d2c.twin.read.failure|Nieudane bliźniacze odczyty z urządzeń|Liczba|Łącznie|Liczba wszystkich odczytów bliźniaczej inicjowanych przez urządzenie nie powiodło się.|Brak|
|d2c.twin.read.size|Rozmiar odpowiedzi bliźniaczych odczytów z urządzeń|Bajty|Średnia|Średnia, min i max wszystkich udanych urządzeń zainicjowane bliźniaczej odsłony.|Brak|
|d2c.twin.update.success|Pomyślne bliźniacze aktualizacje z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji bliźniaczych zainicjowanych przez urządzenie.|Brak|
|d2c.twin.update.failure|Nieudane bliźniacze aktualizacje z urządzeń|Liczba|Łącznie|Liczba wszystkich aktualizacji bliźniaczych zainicjowanych przez urządzenie nie powiodło się.|Brak|
|d2c.twin.update.size|Rozmiar bliźniaczych aktualizacji z urządzeń|Bajty|Średnia|Średni, min i maksymalny rozmiar wszystkich pomyślnych aktualizacji bliźniaczych zainicjowanych przez urządzenie.|Brak|
|c2d.methods.success|Pomyślne wywołanie metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich udanych wywołań metody bezpośredniej.|Brak|
|c2d.methods.failure|Wywołania metod bezpośrednich nie powiodły się|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań metody bezpośredniej.|Brak|
|c2d.methods.requestSize|Rozmiar żądania wywołań metody bezpośredniej|Bajty|Średnia|Średnia, min i maks.|Brak|
|c2d.methods.responseZmializowanie|Rozmiar odpowiedzi wywołań metody bezpośredniej|Bajty|Średnia|Średnia, min i maks.|Brak|
|c2d.twin.read.success|Udane bliźniacze odczyty z zaplecza|Liczba|Łącznie|Liczba wszystkich pomyślnych wstecznych zainicjowanych odczytów bliźniaczych.|Brak|
|c2d.twin.read.failure|Nieudane bliźniacze odczyty z zaplecza|Liczba|Łącznie|Liczba wszystkich nieudanych odczytów bliźniaczych zainicjowanych zapleczem.|Brak|
|c2d.twin.read.size|Rozmiar odpowiedzi bliźniaczych odczytów z zaplecza|Bajty|Średnia|Średnia, min i max wszystkich udanych back-end inicjowane bliźniacze odczyty.|Brak|
|c2d.twin.update.success|Pomyślne podwójne aktualizacje z zaplecza|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji bliźniaczych zainicjowanych zapleczem.|Brak|
|c2d.twin.update.failure|Nieudane bliźniacze aktualizacje z zaplecza|Liczba|Łącznie|Liczba wszystkich aktualizacji bliźniaczych zainicjowanych zapleczem nie powiodło się.|Brak|
|c2d.twin.update.size|Rozmiar bliźniaczych aktualizacji z zaplecza|Bajty|Średnia|Średni, min i maksymalny rozmiar wszystkich pomyślnych aktualizacji bliźniaczych zainicjowanych zapleczem.|Brak|
|twinQueries.success|Pomyślne zapytania bliźniaczej reprezentacji|Liczba|Łącznie|Liczba wszystkich pomyślnych zapytań bliźniaczych reprezentacji.|Brak|
|twinQueries.failure|Nieudane zapytania bliźniaczej reprezentacji|Liczba|Łącznie|Liczba wszystkich nieudanych zapytań bliźniaczych reprezentacji.|Brak|
|twinQueries.resultSize|Rozmiar wyników zapytań bliźniaczych|Bajty|Średnia|Średnia, min i max rozmiaru wyniku wszystkich pomyślnych zapytań bliźniaczych.|Brak|
|jobs.createTwinUpdateJob.success|Udane tworzenie zadań bliźniaczej aktualizacji|Liczba|Łącznie|Liczba wszystkich pomyślnego tworzenia zadań aktualizacji bliźniaczej.|Brak|
|zadania.createTwinUpdateJob.failure|Nieudane tworzenie zadań aktualizacji bliźniaczej|Liczba|Łącznie|Liczba wszystkich nie powiodło się utworzenie zadań aktualizacji bliźniaczej.|Brak|
|jobs.createDirectMethodJob.success|Pomyślne tworzenie zadań wywoływania metody|Liczba|Łącznie|Liczba wszystkich pomyślnego tworzenia zadań wywołania metody bezpośredniej.|Brak|
|zadania.createDirectMethodJob.failure|Nieudane tworzenie zadań wywoływania metody|Liczba|Łącznie|Liczba wszystkich nieudanych tworzenia zadań wywołania metody bezpośredniej.|Brak|
|jobs.listJobs.success|Pomyślne wywołania listy zadań|Liczba|Łącznie|Liczba wszystkich udanych wywołań do listy zadań.|Brak|
|zadania.listJobs.failure|Nieudane wywołania listy zadań|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań do listy zadań.|Brak|
|jobs.cancelJob.success|Pomyślne anulowanie zadania|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań anulowania zadania.|Brak|
|jobs.cancelJob.failure|Anulowanie zadania nie powiodło się|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań anulowania zadania.|Brak|
|jobs.queryJobs.success|Pomyślne kwerendy o zadania|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań zadań zapytań.|Brak|
|zadania.queryJobs.failure|Nieudane kwerendy zadań|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań zadań zapytań.|Brak|
|zadania.ukończone|Ukończone zadania|Liczba|Łącznie|Liczba wszystkich ukończonych zadań.|Brak|
|zadania.failed|Zadania nie powiodły się|Liczba|Łącznie|Liczba wszystkich zadań nie powiodło się.|Brak|
|d2c.telemetry.ingress.sendTrottle|Liczba błędów ograniczania przepustowości|Liczba|Łącznie|Liczba błędów ograniczania przepustowości z powodu przepustów przepływności urządzenia|Brak|
|dailyMessageQuotaUżywane|Łączna liczba używanych wiadomości|Liczba|Średnia|Liczba wszystkich wiadomości używanych obecnie. Jest to skumulowana wartość, która jest resetowana do zera o 00:00 UTC każdego dnia.|Brak|
|deviceDataUsage|Całkowite użycie danych urządzenia|Bajty|Łącznie|Bajty przesyłane do i z wszystkich urządzeń podłączonych do usługi IotHub|Brak|
|deviceDataUsageV2|Całkowite użycie danych urządzenia (podgląd)|Bajty|Łącznie|Bajty przesyłane do i z wszystkich urządzeń podłączonych do usługi IotHub|Brak|
|totalDeviceCount|Łączna liczba urządzeń (wersja zapoznawcza)|Liczba|Średnia|Liczba urządzeń zarejestrowanych w centrum IoT|Brak|
|connectedDeviceCount|Podłączone urządzenia (wersja zapoznawcza)|Liczba|Średnia|Liczba urządzeń podłączonych do centrum IoT|Brak|
|Konfiguracji|Metryki konfiguracji|Liczba|Łącznie|Metryki dla operacji konfiguracyjnych|Brak|

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz przegląd metryk usługi IoT Hub, kliknij ten link, aby dowiedzieć się więcej o zarządzaniu usługą Azure IoT Hub:

* [Konfigurowanie dzienników diagnostycznych](iot-hub-monitor-resource-health.md)

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
