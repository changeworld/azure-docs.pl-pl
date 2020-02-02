---
title: Monitorowanie i rozwiązywanie problemów z połączeniami przy użyciu usługi Azure IoT Hub
description: Dowiedz się, jak monitorować typowe błędy i rozwiązywać problemy z łącznością z urządzeniami dla platformy Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: ed1abe3565805810a6a3fe383e1ddfa209950469
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935373"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitorowanie, diagnozowanie i rozwiązywanie problemów z połączeniami z platformą Azure IoT Hub

Problemy z łącznością dla urządzeń IoT mogą być trudne do rozwiązania, ponieważ istnieje wiele możliwych punktów awarii. Aplikacje logiki aplikacji, sieci fizycznych, protokoły, sprzęt, IoT Hub i inne usługi w chmurze mogą spowodować problemy. Możliwość wykrywania i lokalizowania źródła problemu jest krytyczna. Jednak rozwiązanie IoT na dużą skalę może mieć tysiące urządzeń, więc nie jest praktyczne sprawdzanie poszczególnych urządzeń ręcznie. Aby ułatwić wykrywanie, diagnozowanie i rozwiązywanie problemów w odpowiedniej skali, użyj możliwości monitorowania IoT Hub zapewnia Azure Monitor. Te możliwości są ograniczone do tego, co IoT Hub może obserwować, dlatego zalecamy przestrzeganie najlepszych rozwiązań dotyczących monitorowania dla urządzeń i innych usług platformy Azure.

## <a name="get-alerts-and-error-logs"></a>Uzyskiwanie alertów i dzienników błędów

Użyj Azure Monitor, aby otrzymywać alerty i zapisywać dzienniki podczas odłączania urządzeń.

### <a name="turn-on-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

Aby rejestrować zdarzenia i błędy połączeń urządzeń, Włącz diagnostykę dla IoT Hub. Zalecamy włączenie tych dzienników tak szybko, jak to możliwe, ponieważ dzienniki diagnostyczne nie są włączone, gdy nastąpi odłączenie urządzenia, nie będą dostępne żadne informacje umożliwiające rozwiązanie problemu z usługą.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. Przejdź do centrum IoT Hub.

3. Wybierz pozycję **Ustawienia diagnostyki**.

4. Wybierz **Włącz diagnostykę**.

5. Włącz zbieranie dzienników **połączeń** .

6. Aby ułatwić analizę, Włącz opcję **Wyślij do log Analytics** ([Zobacz cennik](https://azure.microsoft.com/pricing/details/log-analytics/)). Zobacz przykład w obszarze [Rozwiązywanie problemów z łącznością](#resolve-connectivity-errors).

   ![Zalecane ustawienia](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Aby dowiedzieć się więcej, zobacz [monitorowanie kondycji usługi Azure IoT Hub i Szybkie diagnozowanie problemów](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Konfigurowanie alertów dotyczących rozłączenia urządzenia w dużej skali

Aby otrzymywać alerty w przypadku rozłączenia urządzeń, skonfiguruj alerty na metrykach **podłączonych urządzeń (wersja zapoznawcza)** .

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. Przejdź do centrum IoT Hub.

3. Wybierz pozycję **alerty**.

4. Wybierz pozycję **Nowa reguła alertu**.

5. Wybierz pozycję **Dodaj warunek**, a następnie wybierz pozycję "połączone urządzenia (wersja zapoznawcza)".

6. Skonfiguruj próg i alerty, postępując zgodnie z poniższymi instrukcjami.

Aby dowiedzieć się więcej, zobacz [co to są alerty w Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

#### <a name="detecting-individual-device-disconnects"></a>Wykrywanie odłączeń poszczególnych urządzeń

Aby wykryć rozłączenia *dla poszczególnych urządzeń* , na przykład gdy trzeba wiedzieć, że fabryka właśnie przeszedł w tryb offline, [Skonfiguruj zdarzenia rozłączenia urządzeń za pomocą Event Grid](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Rozwiązywanie problemów z łącznością

Po włączeniu dzienników diagnostycznych i alertów dla podłączonych urządzeń otrzymujesz alerty w przypadku wystąpienia błędów. W tej sekcji opisano, jak wyszukiwać typowe problemy występujące podczas otrzymywania alertu. W poniższych krokach przyjęto założenie, że skonfigurowano dzienniki Azure Monitor dla dzienników diagnostycznych.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Przejdź do centrum IoT Hub.

1. Wybierz pozycję **Dzienniki**.

1. Aby wyizolować dzienniki błędów łączności dla IoT Hub, wprowadź następujące zapytanie, a następnie wybierz polecenie **Uruchom**:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Jeśli istnieją wyniki, poszukaj `OperationName`, `ResultType` (kod błędu) i `ResultDescription` (komunikat o błędzie), aby uzyskać więcej szczegółów dotyczących błędu.

   ![Przykładowy dziennik błędów](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. Postępuj zgodnie z przewodnikami dotyczącymi rozwiązywania problemów w przypadku najczęstszych błędów:

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 błąd servererror](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Podjęto próbę wykonania czynności, ale nie zadziałały

Jeśli poprzednie kroki nie były pomocne, spróbuj wykonać następujące czynności:

* Jeśli masz dostęp do problematycznych urządzeń, fizycznie lub zdalnie (na przykład SSH), postępuj zgodnie z [przewodnikiem rozwiązywania problemów po stronie urządzenia](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) , aby kontynuować rozwiązywanie problemów.

* Sprawdź, czy urządzenia są **włączone** w Azure Portal > urządzeniu iot Hub > IoT.

* Uzyskaj pomoc dotyczącą [Forum usługi azure IoT Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)lub [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/).

Aby pomóc w ulepszaniu dokumentacji dla wszystkich użytkowników, należy pozostawić komentarz w sekcji opinii poniżej, jeśli ten przewodnik nie pomoże.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o rozwiązywaniu problemów przejściowych, zobacz [Obsługa błędów przejściowych](/azure/architecture/best-practices/transient-faults).

* Aby dowiedzieć się więcej o zestawie SDK usługi Azure IoT i zarządzaniu ponownymi próbami, zobacz [jak zarządzać łącznością i niezawodną obsługą przy użyciu zestawów SDK urządzeń IoT Hub Azure](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
