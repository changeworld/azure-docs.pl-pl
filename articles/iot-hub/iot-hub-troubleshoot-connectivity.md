---
title: Diagnozowanie i rozwiązywanie problemów z zamknie połączenie z usługą Azure IoT Hub
description: Dowiedz się, jak diagnozowanie i rozwiązywanie typowych problemów z łącznością urządzeń do usługi Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: eb186f4b6e1d742c9cae51e68b3d3dbda1bb751c
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39400420"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Wykrywanie i rozwiązywanie problemów z zamknie połączenie z usługą Azure IoT Hub

Problemy z łącznością w przypadku urządzeń IoT może być trudne do rozwiązania, ponieważ istnieje wiele możliwych punktów awarii. Logika aplikacji po stronie urządzenia, sieciach fizycznych, protokołów, sprzętu i usługi Azure IoT Hub wszystkie może powodować problemy. Ten dokument zawiera zalecenia dotyczące wykrywania i urządzenia Rozwiązywanie problemów z łącznością ze strony chmury (w przeciwieństwie do po stronie urządzenia).

## <a name="get-alerts-and-error-logs"></a>Otrzymuj alerty i dzienniki błędów

Usługa Azure Monitor umożliwia otrzymywanie alertów i zapisują dzienniki w przypadku, gdy porzucić połączenia urządzenia.

### <a name="turn-on-diagnostic-logs"></a>Włączanie dzienników diagnostycznych 

Aby rejestrować zdarzenia połączenia urządzenia i błędy, Włącz diagnostykę usługi IoT Hub. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do centrum IoT Hub.
1. Wybierz **ustawień diagnostycznych**.
1. Następnie wybierz pozycję **Włącz diagnostykę**.
1. Upewnij się, możesz włączyć **połączeń** dzienników, które mają być zbierane. 
1. Aby ułatwić analizę, należy włączyć **wysyłanie do usługi Log Analytics** ([Zobacz cennik](https://azure.microsoft.com/pricing/details/log-analytics/)). Przykładem w dalszej części tego artykułu korzysta z usługi Log Analytics.

   ![Zalecane ustawienia][2]

Aby dowiedzieć się więcej, zobacz [monitorowania kondycji usługi Azure IoT Hub i szybkie diagnozowanie problemów](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>Konfigurowanie alertów dotyczących metryk liczba połączonych urządzeń

Uzyskiwanie alertów w przypadku rozłączenia urządzeń, Konfigurowanie alertów dotyczących *połączone urządzenia* metryki. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do Centrum IoT Hub.
1. Wybierz **alerty (klasyczne)**.
1. Kliknij przycisk **Dodaj alert dotyczący metryki (wersja klasyczna)**.
1. Wypełnij formularz i wybierz pozycję **OK**. 

   ![Zalecane alertu metryki][3]

Aby dowiedzieć się więcej, zobacz [co to są alertów klasycznych na platformie Microsoft Azure?](../monitoring-and-diagnostics/monitoring-overview-alerts.md).

## <a name="resolve-common-connectivity-errors"></a>Rozwiąż typowe błędy związane z łącznością

Dzienniki diagnostyczne i alerty dla połączonych urządzeń są włączone, możesz otrzymywać alerty, gdy coś pójdzie źle. W tej sekcji opisano sposób rozwiązać typowe problemy, gdy zostanie wyświetlony alert. W poniższych krokach przyjęto, że po skonfigurowaniu usługi Log Analytics dla dzienników diagnostycznych. 

1. Szukać obszaru roboczego **usługi Log Analytics** w witrynie Azure portal.
1. Kliknij przycisk **wyszukiwania w dzienniku**.
1. Aby wyizolować dzienniki błędów łączności dla usługi IoT Hub, wpisz to zapytanie w oknie dialogowym naciśnij **Uruchom**.

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. W przypadku wyników poszukaj `OperationName`, `ResultType` (kod błędu) i `ResultDescription` (komunikat), aby uzyskać więcej informacji na temat błędu.

   ![Przykładowy dziennik błędów][4]

1. Ta tabela służy do zrozumienia i rozwiązania typowych problemów.

    | Błąd | Główna przyczyna | Rozwiązanie |
    |---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 404104 DeviceConnectionClosedRemotely | Połączenie zostało zamknięte przez urządzenie, ale nie może ustalić, dlaczego usługi IoT Hub. Typowe przyczyny MQTT/AMQP limitu czasu i internet utraty łączności. | Upewnij się, że urządzenie może połączyć się do usługi IoT Hub za [Trwa testowanie połączenia](tutorial-connectivity.md). Jeśli połączenie jest poprawne, ale urządzenie jest sporadycznie rozłączane, upewnij się zaimplementować logikę aktywne urządzenie Zachowaj właściwe dla wybranego protokołu (MQTT/AMPQ). |
    | 401003 IoTHubUnauthorized | Usługi IoT Hub nie można uwierzytelnić połączenia. | Upewnij się, że sygnatury dostępu Współdzielonego lub innych token zabezpieczający, którego używasz nie jest uznawane za wygasłe. [Usługa Azure IoT SDKs](iot-hub-devguide-sdks.md) automatycznego generowania tokenów bez specjalnej konfiguracji. |
    | 409002 LinkCreationConflict | Istnieje więcej niż jednego połączenia dla tego samego urządzenia. Po przejściu do trybu nowego żądania połączenia dla urządzenia, usłudze IoT Hub zamyka poprzedniemu, z powodu następującego błędu. | Upewnij się, że wystawiania nowego żądania połączenia, tylko wtedy, gdy połączenie spada. |
    | Błąd ServerError 500001 | Usługa IoT Hub wystąpił problem po stronie serwera. Najprawdopodobniej tego problemu jest przejściowy. Podczas działania zespołu usługi IoT Hub trudne do utrzymania [umowy SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/), mały podzbiór węzłów usługi IoT Hub od czasu do czasu mogą występować błędy przejściowe. Gdy urządzenie próbuje połączyć się z węzłem, który występują problemy, ten błąd jest wyświetlany. | Aby uniknąć błędów przejściowych, należy wydać ponawiania z urządzenia. Aby [automatycznie zarządzała ponownych prób](iot-hub-reliability-features-in-sdks.md), upewnij się, że używasz najnowszej wersji [Azure IoT SDKs](iot-hub-devguide-sdks.md).<br><br>Ze względów na liczbę ponownych prób i obsługi błędów przejściowych, zobacz [obsługi błędów przejściowych](/azure/architecture/best-practices/transient-faults.md).  <br><br>Jeśli problem będzie się powtarzać, ponowne próby, sprawdź [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) i [stanu platformy Azure](https://azure.microsoft.com/status/history/) można sprawdzić, czy w usłudze IoT Hub to znany problem. Jeśli ma żadnych znanych problemów, a problem będzie się powtarzać, [się z pomocą techniczną](https://azure.microsoft.com/support/options/) celu bliższego zbadania problemu. |
    | 500008 GenericTimeout | Usługa IoT Hub, nie można ukończyć żądania połączenia przed przekroczeniem limitu czasu. Błąd ServerError 500001, np. Ten błąd jest prawdopodobnie przejściowy. | Wykonaj kroki rozwiązywania problemów dla 500001 błąd ServerError główna przyczyna i rozwiązać ten problem.|

## <a name="other-steps-to-try"></a>Pozostałe kroki do wypróbowania

Jeśli powyższe kroki nie pomogły, poniżej przedstawiono kilka więcej możliwości do wypróbowania:

* Jeśli masz dostęp do urządzeń problematyczne fizycznie lub zdalnie (na przykład protokołu SSH), postępuj zgodnie z [przewodnik rozwiązywania problemów po stronie urządzenia](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) aby kontynuować rozwiązywanie problemów.
* Sprawdź, czy urządzenia są **włączone** w witrynie Azure portal > usługi IoT Hub > urządzeń IoT.
* Uzyskaj pomoc od [forum usługi Azure IoT Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub), lub [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/).

Aby pomóc udoskonalić dokumentację dla wszystkich użytkowników, pozostaw komentarz poniżej, jeśli nie być pomocny ten przewodnik.

<!-- Images -->
[1]: ../../includes/media/iot-hub-diagnostics-settings/turnondiagnostics.png
[2]: ./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png
[3]: ./media/iot-hub-troubleshoot-connectivity/metric-alert.png
[4]: ./media/iot-hub-troubleshoot-connectivity/diag-logs.png
