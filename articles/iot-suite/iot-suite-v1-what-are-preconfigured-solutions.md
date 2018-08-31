---
title: Wstępnie skonfigurowane rozwiązania Azure IoT | Microsoft Docs
description: Opis wstępnie skonfigurowanych rozwiązań Azure IoT, w tym informacje dotyczące ich architektury oraz linki prowadzące do dodatkowych zasobów.
author: dominicbetts
ms.service: iot-suite
services: iot-suite
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 9642bfb8bb6b2e6d63082ff8287c1d5b31bbaefa
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43191133"
---
# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Co to są wstępnie skonfigurowane rozwiązania pakietu Azure IoT Suite?

Wstępnie skonfigurowane rozwiązania pakietu Azure IoT Suite to implementacje typowych wzorców rozwiązań IoT, które można wdrożyć na platformie Azure przy użyciu posiadanej subskrypcji. Wstępnie skonfigurowanych rozwiązań można używać w następujący sposób:

* Jako punktu wyjściowego dla własnych rozwiązań IoT.
* Jako platformy umożliwiającej zapoznanie się z typowymi wzorcami projektowania i tworzenia rozwiązań IoT.

Każde wstępnie skonfigurowane rozwiązanie stanowi kompletną implementację, która korzysta z symulowanych urządzeń do generowania danych telemetrycznych.

Możliwe jest pobranie pełnego kodu źródłowego w celu dostosowania i rozbudowania danego rozwiązania pod kątem własnych wymagań dotyczących urządzeń IoT.

> [!NOTE]
> Aby wdrożyć wstępnie skonfigurowane rozwiązanie, odwiedź witrynę [Microsoft Azure IoT Suite][lnk-azureiotsuite]. Więcej informacji dotyczących wdrażania i uruchamiania przykładowego rozwiązania można znaleźć w artykule [Wprowadzenie do wstępnie skonfigurowanych rozwiązań IoT][lnk-getstarted-preconfigured].

W poniższej tabeli przedstawiono odwzorowanie rozwiązań na określone funkcje IoT:

| Rozwiązanie | Wprowadzanie danych | Tożsamość urządzenia | Zarządzanie urządzeniami | Sterowanie i kontrola | Reguły i akcje | Analiza predykcyjna |
| --- | --- | --- | --- | --- | --- | --- |
| [Zdalne monitorowanie][lnk-getstarted-preconfigured] |Yes |Yes |Yes |Yes |Yes |- |
| [Konserwacja zapobiegawcza][lnk-predictive-maintenance] |Yes |Yes |- |Yes |Yes |Yes |
| [Połączona fabryka][lnk-getstarted-factory] |Yes |Yes |Yes |Yes |Yes |- |

* *Wprowadzanie danych*: transfer danych do chmury na dużą skalę.
* *Tożsamość urządzenia*: zarządzanie unikatowymi tożsamościami urządzeń i sterowanie dostępem urządzeń do rozwiązania.
* *Zarządzanie urządzeniami*: zarządzanie metadanymi urządzeń i wykonywanie operacji, takich jak ponowne uruchamianie urządzeń i aktualizacje oprogramowania układowego.
* *Sterowanie i kontrola*: aby spowodować wykonanie akcji przez urządzenie, wysyłanie komunikatów z chmury do urządzenia.
* *Reguły i akcje*: w celu wykonania działania względem określonych danych przesyłanych z urządzenia do chmury zaplecze rozwiązania korzysta z ról.
* *Analiza predykcyjna*: zaplecze rozwiązania analizuje dane przesyłane z urządzenia do chmury i przewiduje czas, kiedy konkretne działania powinny zostać wykonane. Na przykład analiza danych telemetrycznych silnika samolotu umożliwia określenie konieczności przeprowadzenia konserwacji silnika.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Omówienie wstępnie skonfigurowanego rozwiązania monitorowania zdalnego

W tym artykule omówiono wstępnie skonfigurowane rozwiązanie monitorowania zdalnego, ponieważ zawiera ono wiele typowych elementów projektu, które są używane w innych rozwiązaniach.

Na poniższym diagramie przedstawiono najważniejsze elementy rozwiązania monitorowania zdalnego. Następujące sekcje zawierają więcej informacji na temat tych elementów.

![Architektura wstępnie skonfigurowanego rozwiązania monitorowania zdalnego][img-remote-monitoring-arch]

## <a name="devices"></a>Urządzenia

Po wdrożeniu wstępnie skonfigurowanego rozwiązania monitorowania zdalnego następuje wstępna aprowizacja czterech symulowanych urządzeń w ramach rozwiązania, które pozoruje pracę urządzenia chłodzącego. Symulowane urządzenia mają wbudowany model generowania wartości temperatury i wilgotności, który emituje dane telemetryczne. Te symulowane urządzenia mogą wykonywać następujące działania:

- Ilustrować kompleksowy przepływ danych przez rozwiązanie.
- Zapewniać wygodne źródło danych telemetrycznych.
- Udostępniać miejsce docelowe dla metod lub poleceń programistom zaplecza korzystającym z rozwiązania jako punktu początkowego na potrzeby implementacji niestandardowej.

Symulowane urządzenia w rozwiązaniu mogą odpowiadać na następujące rodzaje komunikacji z chmury do urządzenia:

- *Metody ([metody bezpośrednie][lnk-direct-methods])*: dwukierunkowa metoda komunikacji, w przypadku której oczekuje się natychmiastowej odpowiedzi od podłączonego urządzenia.
- *Polecenia (komunikaty z chmury do urządzenia)*: jednokierunkowa metoda komunikacji, w przypadku której urządzenie pobiera polecenie z kolejki trwałej.

Porównanie tych różnych rozwiązań zawiera temat [Wskazówki dotyczące komunikacji z chmury do urządzenia][lnk-c2d-guidance].

Gdy dane urządzenie pierwszy raz łączy się z usługą IoT Hub w ramach wstępnie skonfigurowanego rozwiązania, wysyła do centrum komunikat z informacjami o urządzeniu. Ten komunikat zawiera metody, na które może odpowiedzieć urządzenie. W zdalnym, wstępnie skonfigurowanym rozwiązaniu do monitorowania symulowane urządzenia obsługują następujące metody:

* *Initiate Firmware Update*: ta metoda inicjuje na urządzeniu asynchroniczne zadanie wykonania aktualizacji oprogramowania układowego. Zadanie asynchroniczne używa zgłaszanych właściwości na potrzeby dostarczania aktualizacji stanu do pulpitu nawigacyjnego rozwiązania.
* *Reboot*: ta metoda powoduje ponowne uruchomienie symulowanego urządzenia.
* *FactoryReset*: ta metoda wyzwala resetowanie symulowanego urządzenia do ustawień fabrycznych.

Gdy dane urządzenie pierwszy raz łączy się z usługą IoT Hub w ramach wstępnie skonfigurowanego rozwiązania, wysyła do centrum komunikat z informacjami o urządzeniu. Ten komunikat zawiera polecenia, na które może odpowiedzieć urządzenie. W zdalnym, wstępnie skonfigurowanym rozwiązaniu do monitorowania symulowane urządzenia obsługują następujące polecenia:

* *Ping Device*: urządzenie potwierdza odebranie tego polecenia. To polecenie jest przydatne w przypadku sprawdzania, czy urządzenie jest nadal aktywne i czy działa nasłuchiwanie.
* *Start Telemetry*: nakazuje urządzeniu rozpoczęcie wysyłania danych telemetrycznych.
* *Stop Telemetry*: nakazuje urządzeniu zaprzestanie wysyłania danych telemetrycznych.
* *Change Set Point Temperature*: umożliwia sterowanie symulowanymi wartościami telemetrycznymi temperatury wysyłanymi przez urządzenie. To polecenie jest przydatne w przypadku testowania logiki zaplecza.
* *Diagnostic Telemetry*: umożliwia określenie, czy informacje o temperaturze zewnętrznej mają być wysyłane przez urządzenie w postaci danych telemetrycznych.
* *Change Device State*: określa właściwość metadanych stanu urządzenia zgłaszaną przez urządzenie. To polecenie jest przydatne w przypadku testowania logiki zaplecza.

Do rozwiązania można dodać kolejne symulowane urządzenia, które emitują te same dane telemetryczne oraz obsługują te same metody i polecenia.

Oprócz odpowiadania na polecenia i metody rozwiązanie korzysta z [bliźniaczych reprezentacji urządzeń][lnk-device-twin]. Urządzenia używają bliźniaczych reprezentacji urządzeń na potrzeby zgłaszania wartości właściwości do zaplecza rozwiązania. Pulpit nawigacyjny rozwiązania używa bliźniaczych reprezentacji urządzeń do ustawiania nowych wartości żądanych właściwości na urządzeniach. Na przykład podczas aktualizacji oprogramowania układowego symulowane urządzenie informuje o stanie aktualizacji za pomocą zgłaszanych właściwości.

## <a name="iot-hub"></a>Usługa IoT Hub

W tym wstępnie skonfigurowanym rozwiązaniu wystąpienie usługi IoT Hub odpowiada *bramie chmury* w typowej [architekturze rozwiązania IoT][lnk-what-is-azure-iot].

Usługa IoT Hub odbiera dane telemetryczne z urządzeń w jednym punkcie końcowym. Udostępnia również punkty końcowe umożliwiające poszczególnym urządzeniom pobranie wysyłanych do nich poleceń.

Usługa IoT Hub udostępnia odebrane dane telemetryczne za pośrednictwem punktu końcowego odczytu danych telemetrycznych po stronie usługi.

Funkcja zarządzania urządzeniami w usłudze IoT Hub umożliwia zarządzanie właściwościami urządzenia z portalu rozwiązania i planowanie zadań wykonujących następujące operacje:

- Ponowne uruchamianie urządzenia
- Zmienianie stanów urządzenia
- Przeprowadzanie aktualizacji oprogramowania układowego

## <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics

Filtrowanie strumienia danych telemetrycznych pochodzących z urządzeń we wstępnie skonfigurowanym rozwiązaniu odbywa się za pomocą trzech zadań usługi [Azure Stream Analytics][lnk-asa] (ASA).

* *Zadanie dotyczące informacji o urządzeniach* — wysyła dane do centrum zdarzeń, które kieruje komunikaty dotyczące rejestracji urządzeń do rejestru urządzeń rozwiązania. Rejestr tego urządzenia to baza danych Azure Cosmos DB. Te komunikaty są wysyłane przy pierwszym połączeniu z urządzeniem lub w odpowiedzi na polecenie **Change device state**.
* *Zadanie dotyczące telemetrii* — wysyła wszystkie nieprzetworzone dane telemetryczne do magazynu obiektów blob Azure w celu przechowania i oblicza zagregowane wartości danych telemetrycznych wyświetlane na pulpicie nawigacyjnym rozwiązania.
* *Zadanie dotyczące reguł* — filtruje strumień danych telemetrycznych w poszukiwaniu danych przekraczających wartości progowe reguł i przesyła dane do centrum zdarzeń. Gdy reguła jest wyzwalana, w widoku pulpitu nawigacyjnego portalu rozwiązania to zdarzenie jest wyświetlane jako nowy wiersz w tabeli historii alarmów. Te reguły mogą również wyzwalać akcję na podstawie ustawień zdefiniowanych w widokach **Reguły** i **Akcje** w portalu rozwiązania.

W tym wstępnie skonfigurowanym rozwiązaniu zadania usługi ASA stanowią część **zaplecza rozwiązania IoT** w typowej [architekturze rozwiązania IoT][lnk-what-is-azure-iot].

## <a name="event-processor"></a>Procesor zdarzeń

W tym wstępnie skonfigurowanym rozwiązaniu procesor zdarzeń stanowi część **zaplecza rozwiązania IoT** w typowej [architekturze rozwiązania IoT][lnk-what-is-azure-iot].

Zadania usługi ASA dotyczące **reguł** i **informacji o urządzeniach** wysyłają dane wyjściowe do centrów zdarzeń, z których dane są przekazywane do innych usług zaplecza. Do odczytu komunikatów z centrów zdarzeń jest używane wystąpienie klasy [EventProcessorHost][lnk-event-processor] uruchomione w zadaniu [WebJob][lnk-web-job]. Klasa **EventProcessorHost** korzysta z następujących elementów:
- Dane obiektu **DeviceInfo** do aktualizowania danych urządzenia w bazie danych Cosmos DB.
- Dane obiektu **Rules** do wywoływania aplikacji logiki i aktualizowania alertów wyświetlanych w portalu rozwiązania.

## <a name="device-identity-registry-device-twin-and-cosmos-db"></a>Rejestr tożsamości urządzeń, bliźniacza reprezentacja urządzenia i usługa Cosmos DB

Każde wystąpienie usługi IoT Hub zawiera [rejestr tożsamości urządzeń][lnk-identity-registry], który przechowuje klucze urządzeń. Usługa IoT Hub używa tych informacji do uwierzytelniania urządzeń — dane urządzenie musi być zarejestrowane i mieć prawidłowy klucz, zanim będzie mogło połączyć się z centrum.

[Bliźniacza reprezentacja urządzenia][lnk-device-twin] to dokument JSON zarządzany przez usługę IoT Hub. Bliźniacza reprezentacja urządzenia zawiera następujące elementy:

- Zgłaszane właściwości wysyłane przez urządzenie do centrum. Te właściwości można wyświetlić w portalu rozwiązania.
- Żądane właściwości, które mają być wysyłane do urządzenia. Te właściwości można ustawić w portalu rozwiązania.
- Tagi, które istnieją tylko w bliźniaczej reprezentacji urządzenia, a nie na urządzeniu. Tych tagów można używać do filtrowania list urządzeń w portalu rozwiązania.

To rozwiązanie korzysta z bliźniaczych reprezentacji urządzeń do zarządzania metadanymi urządzenia. Rozwiązanie korzysta również z bazy danych Cosmos DB do przechowywania dodatkowych danych urządzeń specyficznych dla rozwiązania, takich jak polecenia obsługiwane przez poszczególne urządzenia i historia poleceń.

Informacje przechowywane w rejestrze tożsamości urządzeń muszą być także synchronizowane z zawartością bazy danych Cosmos DB. Klasa **EventProcessorHost** zarządza synchronizacją przy użyciu danych z zadania analizy strumienia dotyczącego **informacji o urządzeniach**.

## <a name="solution-portal"></a>Portal rozwiązania

![portal rozwiązania][img-dashboard]

Portal rozwiązania to oparty na sieci Web interfejs użytkownika wdrożony w chmurze w ramach wstępnie skonfigurowanego rozwiązania. Umożliwia on wykonywanie następujących czynności:

* Wyświetlanie historii danych telemetrycznych i alarmów na pulpicie nawigacyjnym.
* Aprowizacja nowych urządzeń.
* Monitorowanie urządzeń i zarządzanie nimi.
* Wysyłanie poleceń do określonych urządzeń.
* Wywoływanie metod na konkretnych urządzeniach.
* Zarządzanie regułami i akcjami.
* Planowanie zadań do uruchomienia na jednym lub większej liczbie urządzeń.

W tym wstępnie skonfigurowanym rozwiązaniu portal rozwiązania stanowi część **zaplecza rozwiązania IoT** oraz **przetwarzania i łączności biznesowej** w typowej [architekturze rozwiązania IoT][lnk-what-is-azure-iot].

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat architektury rozwiązań IoT, zobacz dokument [Microsoft Azure IoT services: Reference Architecture][lnk-refarch] (Usługi Microsoft Azure IoT: architektura referencyjna).

Teraz, kiedy już wiesz, czym jest wstępnie skonfigurowane rozwiązanie, możesz rozpocząć wdrażanie wstępnie skonfigurowanego rozwiązania *monitorowania zdalnego*: [Wprowadzenie do wstępnie skonfigurowanych rozwiązań][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-v1-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-v1-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-web-job]: ../app-service/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]:../iot-accelerators/iot-accelerators-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsolutions.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-v1-getstarted-preconfigured-solutions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-getstarted-factory]:../iot-accelerators/iot-accelerators-connected-factory-overview.md
