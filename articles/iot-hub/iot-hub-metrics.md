---
title: Używanie metryk do monitorowania IoT Hub platformy Azure | Microsoft Docs
description: Jak oceniać i monitorować ogólną kondycję centrów IoT przy użyciu metryk IoT Hub platformy Azure.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 948cdb2ab1af3fe93566497186c025f7f8f39b2e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877091"
---
# <a name="understand-iot-hub-metrics"></a>Informacje o metrykach IoT Hub

Metryki IoT Hub zapewniają lepsze dane dotyczące stanu zasobów usługi Azure IoT w ramach subskrypcji platformy Azure. Metryki IoT Hub umożliwiają ocenę ogólnej kondycji usługi IoT Hub i podłączonych do niej urządzeń. Dane statystyczne związane z użytkownikiem są ważne, ponieważ pomagają zobaczyć, co się dzieje z Centrum IoT Hub, i pomóc w wykorzystaniu problemów głównych, bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

Metryki są domyślnie włączone. Możesz wyświetlić metryki IoT Hub z Azure Portal.

> [!NOTE]
> Za pomocą metryk IoT Hub można wyświetlać informacje o urządzeniach Plug and Play IoT podłączonych do IoT Hub. Urządzenia Plug and Play IoT są częścią [publicznej wersji zapoznawczej iot Plug and Play](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>Jak wyświetlić metryki IoT Hub

1. Utwórz centrum IoT. Instrukcje dotyczące sposobu tworzenia Centrum IoT można znaleźć w przewodniku wysyłanie danych [telemetrycznych z urządzenia do IoT Hub](quickstart-send-telemetry-dotnet.md) .

2. Otwórz blok Centrum IoT. Z tego miejsca kliknijpozycję metryki.
   
    ![Zrzut ekranu przedstawiający, gdzie znajduje się opcja metryki na stronie zasobów IoT Hub](./media/iot-hub-metrics/enable-metrics-1.png)

3. W bloku metryk można wyświetlić metryki Centrum IoT i utworzyć niestandardowe widoki metryk. 
   
    ![Zrzut ekranu przedstawiający stronę metryki dla IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Możesz wysłać dane metryk do punktu końcowego Event Hubs lub konta usługi Azure Storage, klikając pozycję **Ustawienia diagnostyczne**, a następnie **Dodaj ustawienie diagnostyczne**

   ![Zrzut ekranu przedstawiający przycisk ustawień diagnostycznych](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub metryki i sposoby ich używania

IoT Hub zawiera kilka metryk, które umożliwiają przedstawienie informacji o kondycji centrum oraz łącznej liczbie połączonych urządzeń. Możesz połączyć informacje z wielu metryk, aby malować większy obraz stanu Centrum IoT. W poniższej tabeli opisano metryki poszczególnych ścieżek Centrum IoT oraz sposób, w jaki każda Metryka odnosi się do ogólnego stanu Centrum IoT Hub.

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|d2c<br>. Telemetria<br>ruch przychodzący.<br>allProtocol|Próby wysłania komunikatów telemetrycznych|Count|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury, które próbowano wysłać do centrum IoT Hub|Nie wymiarów|
|d2c<br>. Telemetria<br>ruch przychodzący<br>. powodzenie|Wysłane komunikaty telemetryczne|Count|Łącznie|Liczba pomyślnie wysłanych komunikatów telemetrycznych z urządzenia do chmury do centrum IoT Hub|Nie wymiarów|
|c2d<br>. polecenia<br>ruch wychodzący<br>. Complete<br>. powodzenie|Polecenia ukończone|Count|Łącznie|Liczba poleceń z chmury do urządzenia ukończonych pomyślnie przez urządzenie|Nie wymiarów|
|c2d<br>. polecenia<br>ruch wychodzący<br>. Abandon<br>. powodzenie|Polecenia porzucone|Count|Łącznie|Liczba poleceń z chmury do urządzenia porzuconych przez urządzenie|Nie wymiarów|
|c2d<br>. polecenia<br>ruch wychodzący<br>. Odrzuć<br>. powodzenie|Polecenia odrzucone|Count|Łącznie|Liczba poleceń z chmury do urządzenia odrzuconych przez urządzenie|Nie wymiarów|
|urządzenia<br>.totalDevices|Łączna liczba urządzeń (przestarzałe)|Count|Łącznie|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Nie wymiarów|
|urządzenia<br>.connectedDevices<br>.allProtocol|Podłączone urządzenia (przestarzałe) |Count|Łącznie|Liczba urządzeń podłączonych do centrum IoT Hub|Nie wymiarów|
|d2c<br>. Telemetria<br>ruch wychodzący<br>. powodzenie|Routing: dostarczono komunikaty telemetryczne|Count|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wszystkich punktów końcowych używających routingu IoT Hub. Jeśli komunikat jest kierowany do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia. Jeśli wiadomość jest przekazywana do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia.|Nie wymiarów|
|d2c<br>. Telemetria<br>ruch wychodzący<br>. upuszczone|Routing: porzucone komunikaty telemetryczne |Count|Łącznie|Liczba porzuconych komunikatów przez IoT Hub Routing ze względu na martwe punkty końcowe. Ta wartość nie zlicza komunikatów dostarczonych do trasy rezerwowej, ponieważ opuszczone wiadomości nie są tam dostarczane.|Nie wymiarów|
|d2c<br>. Telemetria<br>ruch wychodzący<br>. oddzielone|Routing: oddzielone komunikaty telemetryczne |Count|Łącznie|Liczba oddzielonych komunikatów przez IoT Hub Routing, ponieważ nie są one zgodne z żadną regułą routingu (w tym regułą rezerwową). |Nie wymiarów|
|d2c<br>. Telemetria<br>ruch wychodzący<br>. nieprawidłowy|Routing: komunikaty telemetryczne są niezgodne|Count|Łącznie|Liczba przypadków, gdy Routing IoT Hub nie mógł dostarczyć komunikatów z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|Nie wymiarów|
|d2c<br>. Telemetria<br>ruch wychodzący<br>. Fallback|Routing: komunikaty dostarczane do powrotu|Count|Łącznie|Liczba IoT Hub komunikatów dostarczonych przez funkcję routingu do punktu końcowego skojarzonego z trasą rezerwową.|Nie wymiarów|
|d2c<br>. punkty końcowe<br>ruch wychodzący<br>. eventHubs|Routing: komunikaty dostarczane do centrum zdarzeń|Count|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych centrum zdarzeń w usłudze IoT Hub Routing.|Nie wymiarów|
|d2c<br>. punkty końcowe<br>. opóźnienie<br>. eventHubs|Routing: opóźnienie komunikatu dla centrum zdarzeń|MS|Average|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących w punkcie końcowym centrum zdarzeń.|Nie wymiarów|
|d2c<br>. punkty końcowe<br>ruch wychodzący<br>.serviceBusQueues|Routing: komunikaty dostarczone do kolejki Service Bus|Count|Łącznie|Liczba pomyślnie dostarczonych komunikatów do Service Bus punktów końcowych kolejki w usłudze IoT Hub Routing.|Nie wymiarów|
|d2c<br>. punkty końcowe<br>. opóźnienie<br>.serviceBusQueues|Routing: opóźnienie komunikatu dla kolejki Service Bus|MS|Average|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i telemetrii komunikatów przychodzących do punktu końcowego kolejki Service Bus.|Nie wymiarów|
|d2c<br>. punkty końcowe<br>ruch wychodzący<br>.serviceBusTopics|Routing: komunikaty dostarczane do Service Bus tematu|Count|Łącznie|Liczba pomyślnie dostarczonych komunikatów przez IoT Hub Routing do Service Bus punktów końcowych tematu.|Nie wymiarów|
|d2c<br>. punkty końcowe<br>. opóźnienie<br>.serviceBusTopics|Routing: opóźnienie komunikatu dla Service Bus tematu|MS|Average|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym tematu Service Bus.|Nie wymiarów|
|d2c<br>. punkty końcowe<br>ruch wychodzący<br>. wbudowane<br>. Events|Routing: komunikaty dostarczane do komunikatów/zdarzeń|Count|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wbudowanego punktu końcowego (komunikaty/zdarzenia) IoT Hub Routing. Ta Metryka uruchamia się tylko wtedy, gdy Routing https://aka.ms/iotrouting) jest włączony (dla Centrum IoT Hub).|Nie wymiarów|
|d2c<br>. punkty końcowe<br>. opóźnienie<br>.builtIn.events|Routing: opóźnienie komunikatów dla komunikatów/zdarzeń|MS|Average|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w wbudowanym punkcie końcowym (komunikaty/zdarzenia). Ta Metryka uruchamia się tylko wtedy, gdy Routing https://aka.ms/iotrouting) jest włączony (dla Centrum IoT Hub).|Nie wymiarów|
|d2c<br>. punkty końcowe<br>ruch wychodzący<br>Magazyn.|Routing: komunikaty dostarczane do magazynu|Count|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych usługi Routing IoT Hub.|Nie wymiarów|
|d2c<br>. punkty końcowe<br>. opóźnienie<br>Magazyn.|Routing: opóźnienie komunikatu dla magazynu|MS|Average|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym magazynu.|Nie wymiarów|
|d2c<br>. punkty końcowe<br>ruch wychodzący<br>Magazyn.<br>. bajtów|Routing: dane dostarczane do magazynu|Bajty|Łącznie|Ilość danych (w bajtach) IoT Hub Routing dostarczany do punktów końcowych magazynu.|Nie wymiarów|
|d2c<br>. punkty końcowe<br>ruch wychodzący<br>Magazyn.<br>. obiekty blob|Routing: obiekty blob dostarczane do magazynu|Count|Łącznie|Ile razy usługa Routing IoT Hub dostarczać obiekty blob do punktów końcowych magazynu.|Nie wymiarów|
|EventGridDeliveries|Dostawy Event Grid (wersja zapoznawcza)|Count|Łącznie|Liczba zdarzeń IoT Hub opublikowanych do Event Grid. Użyj wymiaru wynik dla liczby żądań zakończonych powodzeniem i niepowodzeniem. Wymiar EventType przedstawia typ zdarzenia (https://aka.ms/ioteventgrid). Aby zobaczyć, skąd pochodzą żądania, użyj wymiaru EventType.|Wynik, typ zdarzenia|
|EventGridLatency|Opóźnienie Event Grid (wersja zapoznawcza)|MS|Average|Średnie opóźnienie (w milisekundach) od momentu wygenerowania zdarzenia usługi IoT Hub po opublikowaniu zdarzenia w Event Grid. Ta liczba jest średnia między wszystkimi typami zdarzeń. Użyj wymiaru EventType, aby zobaczyć opóźnienie określonego typu zdarzenia.|EventType|
|d2c<br>. sznurki<br>. Przeczytaj<br>. powodzenie|Pomyślne odczyty sznurów z urządzeń|Count|Łącznie|Liczba wszystkich udanych operacji zainicjowanych przez urządzenie.|Nie wymiarów|
|d2c<br>. sznurki<br>. Przeczytaj<br>. Niepowodzenie|Nieudane odczyty sznurów z urządzeń|Count|Łącznie|Liczba wszystkich nieudanych operacji zainicjowanych przez urządzenie.|Nie wymiarów|
|d2c<br>. sznurki<br>. Przeczytaj<br>. rozmiar|Rozmiar odpowiedzi dla sznurów odczytanych z urządzeń|Bajty|Average|Średnia, minimum i maksimum wszystkich udanych zainicjowanych przez urządzenia sznurów.|Nie wymiarów|
|d2c<br>. sznurki<br>. Update<br>. powodzenie|Pomyślne aktualizacje bliźniaczych urządzeń|Count|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych przez urządzenie.|Nie wymiarów|
|d2c<br>. sznurki<br>. Update<br>. Niepowodzenie|Niepowodzenie aktualizacji bliźniaczych z urządzeń|Count|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji dwuosiowych zainicjowanych przez urządzenie.|Nie wymiarów|
|d2c<br>. sznurki<br>. Update<br>. rozmiar|Rozmiar aktualizacji bliźniaczych z urządzeń|Bajty|Average|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu dwuosiowego zainicjowanego przez urządzenie.|Nie wymiarów|
|c2d<br>. metody<br>. powodzenie|Pomyślne wywołania metody bezpośredniej|Count|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|Nie wymiarów|
|c2d<br>. metody<br>. Niepowodzenie|Nieudane wywołania metody bezpośredniej|Count|Łącznie|Liczba wszystkich wywołań metod bezpośrednich zakończonych niepowodzeniem.|Nie wymiarów|
|c2d<br>. metody<br>.requestSize|Rozmiar żądania wywołań metody bezpośredniej|Bajty|Average|Średnia, minimum i maksimum wszystkich pomyślnych żądań metody bezpośredniej.|Nie wymiarów|
|c2d<br>. metody<br>.responseSize|Rozmiar odpowiedzi wywołań metody bezpośredniej|Bajty|Average|Średnia, minimum i maksimum wszystkich zakończonych powodzeniem odpowiedzi metody bezpośredniej.|Nie wymiarów|
|c2d<br>. sznurki<br>. Przeczytaj<br>. powodzenie|Pomyślne odczyty sznurów z zaplecza|Count|Łącznie|Liczba wszystkich zakończonych powodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Nie wymiarów|
|c2d<br>. sznurki<br>. Przeczytaj<br>. Niepowodzenie|Nieudane odczyty sznurów z zaplecza|Count|Łącznie|Liczba wszystkich zakończonych niepowodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Nie wymiarów|
|c2d<br>. sznurki<br>. Przeczytaj<br>. rozmiar|Rozmiar odpowiedzi na odwrocie od zaplecza|Bajty|Average|Średnia, minimum i maksimum wszystkich udanych zainicjowanych z powrotem sznurów danych.|Nie wymiarów|
|c2d<br>. sznurki<br>. Update<br>. powodzenie|Pomyślne aktualizacje bliźniaczych z zaplecza|Count|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Nie wymiarów|
|c2d<br>. sznurki<br>. Update<br>. Niepowodzenie|Niepowodzenie aktualizacji bliźniaczych z zaplecza|Count|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Nie wymiarów|
|c2d<br>. sznurki<br>. Update<br>. rozmiar|Rozmiar aktualizacji przędzy od zaplecza|Bajty|Average|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu sznurka zainicjowanych z powrotem.|Nie wymiarów|
|twinQueries<br>. powodzenie|Pomyślne zapytania bliźniaczy|Count|Łącznie|Liczba wszystkich udanych zapytań bliźniaczych.|Nie wymiarów|
|twinQueries<br>. Niepowodzenie|Niepowodzenie zapytań bliźniaczych|Count|Łącznie|Liczba wszystkich zakończonych niepowodzeniem zapytań bliźniaczych.|Nie wymiarów|
|twinQueries<br>.resultSize|Rozmiar wyniku zapytań bliźniaczych|Bajty|Average|Średnia, minimalna i maksymalna wielkość wyniku wszystkich udanych zapytań bliźniaczych.|Nie wymiarów|
|zadania<br>.createTwinUpdateJob<br>. powodzenie|Pomyślne utworzenie dwuosiowych zadań aktualizacji|Count|Łącznie|Liczba wszystkich udanych tworzenia zadań aktualizacji z przędzą.|Nie wymiarów|
|zadania<br>.createTwinUpdateJob<br>. Niepowodzenie|Nie można utworzyć dwuosiowych zadań aktualizacji|Count|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań aktualizacji z przędzą.|Nie wymiarów|
|zadania<br>.createDirectMethodJob<br>. powodzenie|Pomyślne utworzenie zadań wywołania metody|Count|Łącznie|Liczba wszystkich pomyślnych operacji tworzenia zadań wywołania metody bezpośredniej.|Nie wymiarów|
|zadania<br>.createDirectMethodJob<br>. Niepowodzenie|Nie można utworzyć zadań wywołania metody|Count|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań wywołania metody bezpośredniej.|Nie wymiarów|
|zadania<br>.listJobs<br>. powodzenie|Pomyślne wywołania do zadań na liście|Count|Łącznie|Liczba wszystkich udanych wywołań do listy zadań.|Nie wymiarów|
|zadania<br>.listJobs<br>. Niepowodzenie|Wywołania zakończone niepowodzeniem do listy zadań|Count|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wyświetlenia listy zadań.|Nie wymiarów|
|zadania<br>.cancelJob<br>. powodzenie|Pomyślne anulowania zadań|Count|Łącznie|Liczba wszystkich udanych wywołań do anulowania zadania.|Nie wymiarów|
|zadania<br>.cancelJob<br>. Niepowodzenie|Nieudane anulowania zadań|Count|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu anulowania zadania.|Nie wymiarów|
|zadania<br>.queryJobs<br>. powodzenie|Pomyślne zapytania dotyczące zadań|Count|Łącznie|Liczba wszystkich udanych wywołań do zadań zapytań.|Nie wymiarów|
|zadania<br>.queryJobs<br>. Niepowodzenie|Nieudane kwerendy zadań|Count|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wykonywania zapytań dotyczących zadań.|Nie wymiarów|
|zadania<br>. ukończone|Ukończone zadania|Count|Łącznie|Liczba wszystkich ukończonych zadań.|Nie wymiarów|
|zadania<br>. Niepowodzenie|Zadania zakończone niepowodzeniem|Count|Łącznie|Liczba wszystkich zadań zakończonych niepowodzeniem.|Nie wymiarów|
|d2c<br>. Telemetria<br>ruch przychodzący<br>.sendThrottle|Liczba błędów ograniczania|Count|Łącznie|Liczba błędów ograniczania z powodu ograniczeń przepływności urządzenia|Nie wymiarów|
|dailyMessage<br>QuotaUsed|Całkowita liczba użytych komunikatów|Count|Average|Całkowita liczba użytych komunikatów. Jest to skumulowana wartość, która jest resetowana do zera o godzinie 00:00 czasu każdego dnia.|Nie wymiarów|
|deviceDataUsage|Całkowite użycie danych urządzenia|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|Nie wymiarów|
|totalDeviceCount|Łączna liczba urządzeń (wersja zapoznawcza)|Count|Average|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Nie wymiarów|
|łączona<br>Liczba urządzeń|Podłączone urządzenia (wersja zapoznawcza)|Count|Average|Liczba urządzeń podłączonych do centrum IoT Hub|Nie wymiarów|
|komputerów|Metryki konfiguracji|Count|Łącznie|Metryki dla operacji konfiguracji|Nie wymiarów|

## <a name="next-steps"></a>Następne kroki

Teraz, po zapoznaniu się z omówieniem metryk IoT Hub, Skorzystaj z tego linku, aby dowiedzieć się więcej na temat zarządzania usługą Azure IoT Hub:

* [Monitorowanie operacji](iot-hub-operations-monitoring.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Przewodnik dla deweloperów IoT Hub](iot-hub-devguide.md)

* [Wdrażanie AI na urządzeniach brzegowych za pomocą Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
