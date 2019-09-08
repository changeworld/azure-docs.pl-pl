---
title: Diagnozowanie i rozwiązywanie problemów z połączeniami za pomocą usługi Azure IoT Hub
description: Dowiedz się, jak diagnozować i rozwiązywać typowe błędy przy użyciu połączenia z urządzeniami dla platformy Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: 3904c6390cfe8de197bae470c4ae32d22605ae6a
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801430"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Wykrywanie i rozwiązywanie problemów z połączeniami przy użyciu usługi Azure IoT Hub

Problemy z łącznością dla urządzeń IoT mogą być trudne do rozwiązania, ponieważ istnieje wiele możliwych punktów awarii. Logika aplikacji po stronie urządzenia, sieci fizyczne, protokoły, sprzęt i usługa Azure IoT Hub mogą spowodować problemy. Ten artykuł zawiera zalecenia dotyczące wykrywania i rozwiązywania problemów z łącznością urządzenia ze strony chmury (w przeciwieństwie do urządzenia).

## <a name="get-alerts-and-error-logs"></a>Uzyskiwanie alertów i dzienników błędów

Użyj Azure Monitor, aby uzyskać alerty i zapisywać dzienniki podczas usuwania połączeń z urządzeniem.

### <a name="turn-on-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

Aby rejestrować zdarzenia i błędy połączeń urządzeń, Włącz diagnostykę dla IoT Hub.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do centrum IoT Hub.

3. Wybierz pozycję **Ustawienia diagnostyki**.

4. Wybierz **Włącz diagnostykę**.

5. Włącz zbieranie dzienników **połączeń** .

6. Aby ułatwić analizę, Włącz opcję **Wyślij do log Analytics** ([Zobacz cennik](https://azure.microsoft.com/pricing/details/log-analytics/)). Zobacz przykład w obszarze [Rozwiązywanie problemów z łącznością](#resolve-connectivity-errors).

   ![Zalecane ustawienia](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Aby dowiedzieć się więcej, zobacz [monitorowanie kondycji usługi Azure IoT Hub i Szybkie diagnozowanie problemów](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-_connected-devices_-count-metric"></a>Konfigurowanie alertów dotyczących metryki liczby _połączonych urządzeń_

Aby otrzymywać alerty w przypadku rozłączenia urządzeń, skonfiguruj alerty na metrykach **podłączonych urządzeń (wersja zapoznawcza)** .

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do centrum IoT Hub.

3. Wybierz pozycję **alerty**.

4. Wybierz pozycję **Nowa reguła alertu**.

5. Wybierz pozycję **Dodaj warunek**, a następnie wybierz pozycję "połączone urządzenia (wersja zapoznawcza)".

6. Zakończ Konfigurowanie żądanych progów i opcji alertów, postępując zgodnie z poniższymi instrukcjami.

Aby dowiedzieć się więcej, zobacz [co to są klasyczne alerty w Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

## <a name="resolve-connectivity-errors"></a>Rozwiązywanie problemów z łącznością

Po włączeniu dzienników diagnostycznych i alertów dla podłączonych urządzeń otrzymujesz alerty w przypadku wystąpienia błędów. W tej sekcji opisano sposób rozwiązywania typowych problemów występujących w przypadku otrzymania alertu. W poniższych krokach przyjęto założenie, że skonfigurowano dzienniki Azure Monitor dla dzienników diagnostycznych.

1. Przejdź do obszaru roboczego, aby uzyskać **log Analytics** w Azure Portal.

2. Wybierz pozycję **przeszukiwanie dzienników**.

3. Aby wyizolować dzienniki błędów łączności dla IoT Hub, wprowadź następujące zapytanie, a następnie wybierz polecenie **Uruchom**:

    ```kusto
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Jeśli istnieją wyniki, Wyszukaj `OperationName`, `ResultType` (kod błędu) i `ResultDescription` (komunikat o błędzie), aby uzyskać więcej szczegółów dotyczących błędu.

   ![Przykładowy dziennik błędów](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

2. Skorzystaj z tej tabeli, aby zrozumieć i rozwiązać typowe błędy.

    | Błąd | Główna przyczyna | Rozwiązanie |
    |-------|------------|------------|
    | 404104 DeviceConnectionClosedRemotely | Połączenie zostało zamknięte przez urządzenie, ale IoT Hub nie wie dlaczego. Typowe przyczyny obejmują limit czasu MQTT/AMQP i utratę łączności z Internetem. | Upewnij się, że urządzenie może nawiązać połączenie z IoT Hub, [testując połączenie](tutorial-connectivity.md). Jeśli połączenie jest prawidłowe, ale urządzenie rozłącza się sporadycznie, pamiętaj, aby zaimplementować właściwą logikę urządzenia utrzymywania aktywności dla wybranego protokołu (MQTT/AMPQ). |
    | 401003 IoTHubUnauthorized | IoT Hub nie może uwierzytelnić połączenia. | Upewnij się, że używasz sygnatury dostępu współdzielonego lub innego tokenu zabezpieczającego, który nie wygasł. [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md) automatycznie generują tokeny bez konieczności konfigurowania specjalnej konfiguracji. |
    | 409002 LinkCreationConflict | Urządzenie ma więcej niż jedno połączenie. Gdy nowe żądanie połączenia pochodzi z urządzenia, IoT Hub zamyka poprzednią wartość tego błędu. | W najbardziej typowym przypadku urządzenie wykrywa rozłączenie i próbuje ponownie nawiązać połączenie, ale IoT Hub nadal uważa, że urządzenie jest połączone. IoT Hub zamyka poprzednie połączenie i rejestruje ten błąd. Ten błąd zazwyczaj występuje jako efekt uboczny innego, przejściowego problemu, dlatego wyszukasz inne błędy w dziennikach, aby rozwiązać problem. W przeciwnym razie należy wydać nowe żądanie połączenia tylko wtedy, gdy połączenie zostanie porzucane. |
    | 500001 ServerError | IoT Hub działały w ramach problemu po stronie serwera. Najprawdopodobniej problem jest przejściowy. Mimo że zespół IoT Hub działa trudno, aby zachować umowę [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/), małe podzestawy węzłów IoT Hub mogą czasami powodować błędy przejściowe. Gdy urządzenie próbuje nawiązać połączenie z węzłem, który ma problemy, zostanie wyświetlony ten błąd. | Aby wyeliminować błąd przejściowy, należy wydać ponowną próbę urządzenia. Aby [automatycznie zarządzać ponownymi próbami](iot-hub-reliability-features-in-sdks.md#connection-and-retry), upewnij się, że korzystasz z najnowszej wersji [zestawów SDK usługi Azure IoT](iot-hub-devguide-sdks.md).<br><br>Najlepszym rozwiązaniem w przypadku przejściowej obsługi błędów i ponownych prób zapoznaj się z tematem [Obsługa błędów przejściowych](/azure/architecture/best-practices/transient-faults).  <br><br>Jeśli problem będzie nadal występować po ponownych próbach, sprawdź [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) i [Stan platformy Azure](https://azure.microsoft.com/status/history/) , aby sprawdzić, czy IoT Hub ma znany problem. Jeśli nie występują żadne znane problemy, a problem będzie się powtarzać, [skontaktuj się z pomocą techniczną](https://azure.microsoft.com/support/options/) w celu przeprowadzenia dalszych badań. |
    | 500008 GenericTimeout | IoT Hub nie może zakończyć żądania połączenia przed upływem limitu czasu. Podobnie jak w przypadku 500001 błąd servererror, ten błąd prawdopodobnie jest przejściowy. | Wykonaj kroki rozwiązywania problemów dla błąd servererror 500001 do głównej przyczyny i Usuń ten błąd.|

## <a name="other-steps-to-try"></a>Inne kroki do wypróbowania

Jeśli poprzednie kroki nie były pomocne, możesz spróbować:

* Jeśli masz dostęp do problematycznych urządzeń, fizycznie lub zdalnie (na przykład SSH), postępuj zgodnie z [przewodnikiem rozwiązywania problemów po stronie urządzenia](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) , aby kontynuować rozwiązywanie problemów.

* Sprawdź, czy urządzenia są **włączone** w Azure Portal > urządzeniu iot Hub > IoT.

* Uzyskaj pomoc dotyczącą [Forum usługi azure IoT Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)lub [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/).

Aby pomóc w ulepszaniu dokumentacji dla wszystkich użytkowników, należy pozostawić komentarz w sekcji opinii poniżej, jeśli ten przewodnik nie pomoże.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o rozwiązywaniu problemów przejściowych, zobacz [Obsługa błędów przejściowych](/azure/architecture/best-practices/transient-faults).

* Aby dowiedzieć się więcej o zestawie SDK usługi Azure IoT i zarządzaniu ponownymi próbami, zobacz [jak zarządzać łącznością i niezawodną obsługą przy użyciu zestawów SDK urządzeń IoT Hub Azure](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
