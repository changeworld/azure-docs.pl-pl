---
title: Przewodnik po wstępnie skonfigurowanym rozwiązaniu monitorowania zdalnego | Microsoft Docs
description: Opis wstępnie skonfigurowanego rozwiązania Azure IoT do monitorowania zdalnego wraz z informacjami dotyczącymi architektury rozwiązania.
author: dominicbetts
ms.service: iot-suite
services: iot-suite
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 8cb6c3087bac460069a689f7b109a20aa9125ae9
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43184917"
---
# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Przewodnik po wstępnie skonfigurowanym rozwiązaniu monitorowania zdalnego

[Wstępnie skonfigurowane rozwiązanie][lnk-preconfigured-solutions] do monitorowania zdalnego Pakietu IoT to implementacja kompleksowego rozwiązania do monitorowania wielu maszyn uruchomionych w lokalizacjach zdalnych. Rozwiązanie to łączy najważniejsze usługi platformy Azure, aby umożliwić ogólną implementację scenariusza biznesowego. Możesz go użyć jako punktu wyjścia dla Twojej własnej implementacji i je [dostosować][lnk-customize], aby spełniało Twoje własne określone wymagania biznesowe.

Ten artykuł przeprowadzi Cię przez niektóre kluczowe elementy rozwiązania do monitorowania zdalnego, aby umożliwić Ci zrozumienie jego sposobu działania. Ta wiedza ułatwi Ci:

* Rozwiązywanie problemów w rozwiązaniu.
* Planowanie sposobu dostosowywania rozwiązania, aby spełniało Twoje wymagania. 
* Projektowanie własnego rozwiązania IoT korzystającego z usług Azure.

## <a name="logical-architecture"></a>Architektura logiczna

Poniższy diagram przedstawia składniki logiczne wstępnie skonfigurowanego rozwiązania:

![Architektura logiczna](media/iot-suite-v1-remote-monitoring-sample-walkthrough/remote-monitoring-architecture-updated.png)

## <a name="microservices--docker-containers"></a>Mikrousługi i kontenery platformy Docker
Zdalne monitorowanie to pierwsze z naszych wstępnie skonfigurowanych rozwiązań, które korzysta z architektury mikrousług. Rozwiązanie jest dostępne zarówno na platformie [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet), jak i [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).
Mikrousługi stały się powszechnie stosowanym rozwiązaniem umożliwiającym osiągnięcie skalowalności i elastyczności (przez zezwolenie na osobne skalowanie kontenerów) bez zmniejszenia szybkość tworzenia aplikacji.
Mikrousługi zapewniają podział kodu i udostępniają dobrze zdefiniowane interfejsy, dzięki czemu rozwiązanie jest łatwiejsze do zrozumienia i mniej monolityczne. Zapewniają one również dodatkowe opcje dla partnerów, którzy chcą rozszerzyć nasze bieżące akceleratory rozwiązań w celu utworzenia ukończonych rozwiązań przeznaczonych do sprzedaży.

**Dowiedz się więcej na temat kontenerów platformy Docker**
* [Instalowanie platformy Docker](https://docs.docker.com/engine/installation/)
* [Typowe polecenia platformy Docker na potrzeby zdalnego monitorowania](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#common-docker-commands)
* [Wprowadzenie do platformy Docker](https://docs.docker.com/get-started/)

## <a name="simulated-devices"></a>Symulowane urządzenia

W tym wstępnie skonfigurowanym rozwiązaniu symulowane urządzenie odpowiada urządzeniu chłodzącemu (np. klimatyzatorowi lub centrali wentylacyjnej w budynku). Podczas wdrażania wstępnie skonfigurowanego rozwiązania przeprowadzisz też automatyczną aprowizację czterech symulowanych urządzeń działających w zadaniu [WebJob platformy Azure][lnk-webjobs]. Symulowane urządzenia ułatwiają badanie zachowania rozwiązania bez konieczności wdrażania jakichkolwiek urządzeń fizycznych. Aby wdrożyć rzeczywiste urządzenie fizyczne, zobacz samouczek [Łączenie urządzenia ze wstępnie skonfigurowanym rozwiązaniem do monitorowania zdalnego][lnk-connect-rm].

### <a name="device-to-cloud-messages"></a>Komunikaty z urządzenia do chmury

Każde symulowane urządzenie może wysyłać do usługi IoT Hub następujące typy wiadomości:

| Komunikat | Opis |
| --- | --- |
| Uruchamianie |Po uruchomieniu urządzenie wysyła do zaplecza komunikat **device-info** zawierający informacje o tym urządzeniu. Te dane obejmują identyfikator urządzenia oraz listę poleceń i metod obsługiwanych przez urządzenie. |
| Obecność |Urządzenie okresowo wysyła komunikat **presence** w celu zgłoszenia, czy urządzenie może wykrywać obecność czujnika. |
| Telemetria |Urządzenie okresowo wysyła komunikat **telemetry**, który zgłasza symulowane wartości temperatury i wilgotności zebrane z symulowanych czujników urządzenia. |

> [!NOTE]
> Rozwiązanie przechowuje listę poleceń obsługiwanych przez urządzenie w bazie danych Cosmos DB, a nie w bliźniaczej reprezentacji urządzenia.

### <a name="properties-and-device-twins"></a>Właściwości i bliźniacze reprezentacje urządzeń

Symulowane urządzenia wysyłają poniższe właściwości urządzenia do [bliźniaczej reprezentacji urządzenia][lnk-device-twins] w usłudze IoT Hub jako *zgłaszane właściwości*. Urządzenie wysyła zgłaszane właściwości podczas uruchamiania i w odpowiedzi na polecenie lub metodę **zmiany stanu urządzenia**.

| Właściwość | Przeznaczenie |
| --- | --- |
| Config.TelemetryInterval | Częstotliwość (w sekundach) wysłania przez urządzenie danych telemetrycznych |
| Config.TemperatureMeanValue | Określa średnią wartość symulowanych danych telemetrycznych dotyczących temperatury |
| Device.DeviceID |Ten identyfikator jest udostępniany lub przypisywany podczas tworzenia urządzenia w rozwiązaniu |
| Device.DeviceState | Stan raportowany przez urządzenie |
| Device.CreatedTime |Czas utworzenia urządzenia w rozwiązaniu |
| Device.StartupTime |Czas uruchomienia urządzenia |
| Device.LastDesiredPropertyChange |Numer wersji ostatniej zmiany żądanej właściwości |
| Device.Location.Latitude |Współrzędna szerokości geograficznej lokalizacji urządzenia |
| Device.Location.Longitude |Współrzędna długości geograficznej lokalizacji urządzenia |
| System.Manufacturer |Producent urządzenia |
| System.ModelNumber |Numer modelu urządzenia |
| System.SerialNumber |Numer seryjny urządzenia |
| System.FirmwareVersion |Bieżąca wersja oprogramowania układowego na urządzeniu |
| System.Platform |Architektura platformy urządzenia |
| System.Processor |Procesor działający w urządzeniu |
| System.InstalledRAM |Ilość pamięci RAM zainstalowanej w urządzeniu |

W symulowanych urządzeniach te właściwości są wypełniane przykładowymi wartościami. Za każdym razem, gdy symulator inicjuje symulowane urządzenie, raportuje ono wstępnie zdefiniowane metadane do usługi IoT Hub jako zgłaszane właściwości. Zgłaszane właściwości mogą być aktualizowane tylko przez urządzenie. Aby zmienić zgłoszoną właściwość, należy ustawić żądaną właściwość w portalu rozwiązania. Na urządzeniu są wykonywane następujące czynności:

1. Okresowe pobieranie żądanych właściwości z usługi IoT Hub.
2. Aktualizowanie swojej konfiguracji wartością żądanej właściwości.
3. Wysyłanie nowej wartości z powrotem do usługi IoT Hub jako zgłaszanej właściwości.

Z poziomu pulpitu nawigacyjnego rozwiązania możesz używać *żądanych właściwości* do ustawiania właściwości na urządzeniu za pomocą [bliźniaczej reprezentacji urządzenia][lnk-device-twins]. Zwykle urządzenie odczytuje wartość żądanej właściwości z usługi IoT Hub w celu zaktualizowania swojego stanu wewnętrznego i zgłoszenia tej zmiany z powrotem jako zgłaszanej właściwości.

> [!NOTE]
> W kodzie na symulowanym urządzeniu są używane wyłącznie żądane właściwości **Desired.Config.TemperatureMeanValue** i **Desired.Config.TelemetryInterval** do aktualizowania zgłaszanych właściwości wysyłanych z powrotem do usługi IoT Hub. Wszystkie inne żądania zmiany żądanych właściwości są ignorowane na symulowanym urządzeniu.

### <a name="methods"></a>Metody

Symulowane urządzenia obsługują następujące metody ([metody bezpośrednie][lnk-direct-methods]) wywoływane z poziomu portalu rozwiązania za pomocą usługi IoT Hub:

| Metoda | Opis |
| --- | --- |
| InitiateFirmwareUpdate |Powoduje, że urządzenie przeprowadza aktualizację oprogramowania układowego |
| Ponowne uruchamianie |Powoduje ponowne uruchomienie urządzenia |
| FactoryReset |Powoduje, że na urządzeniu są przywracane ustawienia fabryczne |

Niektóre metody używają zgłaszanych właściwości do raportowania postępu. Na przykład metoda **InitiateFirmwareUpdate** symuluje asynchroniczne uruchomienie aktualizacji na urządzeniu. Efekty tej metody występują bezpośrednio na urządzeniu, natomiast zadanie asynchroniczne nadal wysyła aktualizacje stanu z powrotem na pulpit nawigacyjny rozwiązania za pomocą zgłaszanych właściwości.

### <a name="commands"></a>Polecenia

Symulowane urządzenia obsługują następujące polecenia (komunikaty z chmury do urządzenia) wysyłane z portalu rozwiązania za pośrednictwem usługi IoT Hub:

| Polecenie | Opis |
| --- | --- |
| PingDevice |Sprawdza, czy urządzenie jest aktywne, za pomocą polecenia *ping*. |
| StartTelemetry |Włącza przesyłanie danych telemetrycznych przez urządzenie. |
| StopTelemetry |Wyłącza przesyłanie danych telemetrycznych przez urządzenie. |
| ChangeSetPointTemp |Zmienia ustaloną wartość, na podstawie której są generowane dane losowe. |
| DiagnosticTelemetry |Włącza wysyłanie dodatkowej wartości telemetrycznej (externalTemp — temperatura zewnętrzna) przez symulator urządzenia. |
| ChangeDeviceState |Zmienia właściwość rozszerzonego stanu urządzenia i wysyła komunikat z informacjami o urządzeniu. |

> [!NOTE]
> Porównanie tych poleceń (komunikatów wysyłanych z chmury do urządzenia) i metod (metod bezpośrednich) znajduje się w temacie [Wskazówki dotyczące komunikacji z chmury do urządzenia][lnk-c2d-guidance].

## <a name="iot-hub"></a>Usługa IoT Hub

Usługa [IoT Hub][lnk-iothub] pozyskuje dane wysyłane z urządzeń do chmury i udostępnia je zadaniom usługi Azure Stream Analytics (ASA). Każde zadanie strumieniowe ASA odczytuje strumień komunikatów z urządzeń przy użyciu osobnej grupy konsumentów usługi IoT Hub.

Usługa IoT Hub w rozwiązaniu wykonuje ponadto następujące czynności:

- Obsługuje rejestr tożsamości, w którym są przechowywane identyfikatory i klucze uwierzytelniania wszystkich urządzeń z uprawnieniami do nawiązywania połączeń z portalem. Za pomocą rejestru tożsamości możesz włączać i wyłączać urządzenia.
- Wysyła polecenia do Twoich urządzeń w imieniu portalu rozwiązania.
- Wywołuje metody na Twoich urządzeniach w imieniu portalu rozwiązania.
- Obsługuje bliźniacze reprezentacje wszystkich zarejestrowanych urządzeń. W bliźniaczej reprezentacji urządzenia są przechowywane wartości właściwości zgłaszanych przez urządzenie. W bliźniaczej reprezentacji urządzenia są także przechowywane żądane właściwości ustawione w portalu rozwiązania, które mają zostać pobrane przez urządzanie podczas nawiązywania następnego połączenia.
- Planuje zadania ustawiania właściwości dla wielu urządzeń lub wywoływania metod na wielu urządzeniach.

## <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics

W rozwiązaniu do monitorowania zdalnego usługa [Azure Stream Analytics][lnk-asa] (ASA) wysyła komunikaty urządzeń odebrane przez usługę IoT Hub do innych składników zaplecza w celu ich przetwarzania lub magazynowania. Różne zadania ASA pełnią określone funkcje w zależności od zawartości komunikatów.

**Zadanie 1: Informacje o urządzeniu** odfiltrowuje komunikaty z informacjami o urządzeniu ze strumienia komunikatów przychodzących i wysyła je do punktu końcowego Centrum zdarzeń. Komunikat z informacjami o urządzeniu jest wysyłany podczas uruchamiania urządzenia oraz w odpowiedzi na polecenie **SendDeviceInfo**. To zadanie identyfikuje komunikaty **device-info** przy użyciu następującej definicji zapytania:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

To zadanie wysyła dane wyjściowe do centrum zdarzeń w celu dalszego przetwarzania.

**Zadanie 2: Reguły** porównuje przychodzące dane telemetryczne dotyczące temperatury i wilgotności z wartościami progowymi określonymi dla urządzenia. Wartości progowe są ustawiane za pomocą edytora reguł dostępnego w portalu rozwiązania. Poszczególne pary urządzenie/wartość są uporządkowane według sygnatur czasowych i przechowywane w obiekcie blob, który jest odczytywany przez usługę Stream Analytics jako **dane referencyjne**. W ramach zadania wszystkie niepuste wartości są porównywane względem progu ustalonego dla urządzenia. W przypadku przekroczenia warunku „>” zadanie generuje zdarzenie **alarmu** dotyczące przekroczenia wartości progowej, które udostępnia dane o urządzeniu, wartości i sygnaturze czasowej. To zadanie identyfikuje komunikaty telemetryczne, które powinny wyzwolić alarm, przy użyciu następującej definicji zapytania:

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

Zadanie wysyła dane wyjściowe do centrum zdarzeń w celu dalszego przetwarzania i zapisuje szczegóły każdego alertu w magazynie obiektów blob, z którego portal rozwiązania może odczytać informacje o alertach.

**Zadanie 3: Telemetria** przetwarza strumień przychodzących z urządzeń danych telemetrycznych przy użyciu dwóch procedur. Pierwsza z nich przesyła wszystkie komunikaty telemetryczne z urządzeń do trwałego magazynu obiektów blob w celu ich długotrwałego przechowywania. Druga procedura wyznacza średnią, minimalną i maksymalną wartość wilgotności w ramach przesuwającego się okna czasowego o wielkości pięciu minut i wysyła te dane do magazynu obiektów blob. Portal rozwiązania odczytuje dane telemetryczne z magazynu obiektów blob w celu wypełnienia wykresów. W tym zadaniu jest używana następująca definicja zapytania:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Event Hubs

Zadania ASA **informacje o urządzeniu** i **reguły** przesyłają dane wyjściowe do centrum zdarzeń w celu ich niezawodnego przekazania do **procesora zdarzeń** uruchomionego w zadaniu WebJob.

## <a name="azure-storage"></a>Azure Storage

Rozwiązanie korzysta z magazynu obiektów blob platformy Azure, aby utrwalić wszystkie nieprzetworzone i podsumowane dane telemetryczne z urządzeń w rozwiązaniu. Portal odczytuje dane telemetryczne z magazynu obiektów blob w celu wypełnienia wykresów. Aby wyświetlić alerty, portal rozwiązania odczytuje dane z magazynu obiektów blob, który rejestruje, kiedy wartości telemetryczne przekraczają skonfigurowane wartości progowe. Rozwiązanie używa również magazynu obiektów blob do rejestrowania wartości progowych określonych w portalu rozwiązania.

## <a name="webjobs"></a>Zadania WebJob

Oprócz hostowania symulatorów urządzenia zadania WebJob w rozwiązaniu hostują także **procesor zdarzeń** uruchomiony w zadaniu WebJob platformy Azure, które obsługuje odpowiedzi na polecenia. Używa on komunikatów odpowiedzi na polecenia w celu aktualizacji historii poleceń urządzenia (przechowywanej w bazie danych Cosmos DB).

## <a name="cosmos-db"></a>Cosmos DB

Rozwiązanie przechowuje informacje o połączonych z nim urządzeniach w bazie danych Cosmos DB. Te informacje uwzględniają historię poleceń wysłanych do urządzeń z portalu rozwiązania i metod wywołanych z portalu rozwiązania.

## <a name="solution-portal"></a>Portal rozwiązania

Portal rozwiązania to aplikacja internetowa wdrożona w ramach wstępnie skonfigurowanego rozwiązania. Najważniejsze strony w portalu rozwiązania to pulpit nawigacyjny i lista urządzeń.

### <a name="dashboard"></a>Pulpit nawigacyjny

Na tej stronie aplikacji internetowej są używane kontrolki JavaScript usługi Power BI (zobacz [repozytorium PowerBI-visuals](https://www.github.com/Microsoft/PowerBI-visuals)), które umożliwiają wizualizację danych telemetrycznych z urządzeń. Rozwiązanie zapisuje dane telemetryczne w magazynie obiektów blob, używając zadań telemetrii usługi ASA.

### <a name="device-list"></a>Lista urządzeń

Na tej stronie w portalu rozwiązania możesz wykonywać następujące czynności:

* Aprowizacja nowego urządzenia. Ta czynność obejmuje określenie unikatowego identyfikatora urządzenia i wygenerowanie klucza uwierzytelniania. Zapisuje ona informacje o urządzeniu w rejestrze tożsamości usługi IoT Hub oraz w bazie danych Cosmos DB określonego rozwiązania.
* Zarządzanie właściwościami urządzenia. Ta czynność obejmuje wyświetlanie istniejących właściwości i aktualizowanie ich.
* Wysyłanie poleceń do urządzenia.
* Wyświetlanie historii poleceń dla urządzenia.
* Włączanie i wyłączanie urządzeń.

## <a name="next-steps"></a>Kolejne kroki

Następujące wpisy na blogu w witrynie TechNet zawierają więcej informacji dotyczących wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego:

* [IoT Suite - Under The Hood - Remote Monitoring (Za kulisami pakietu IoT — monitorowanie zdalne)](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
* [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices (Pakiet IoT — monitorowanie zdalne — dodawanie rzeczywistych i symulowanych urządzeń)](https://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

Możesz kontynuować poznawanie Pakietu IoT, czytając następujące artykuły:

* [Łączenie urządzenia ze wstępnie skonfigurowanym rozwiązaniem do monitorowania zdalnego][lnk-connect-rm]
* [Uprawnienia w witrynie azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-v1-connecting-devices.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twins]:  ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
