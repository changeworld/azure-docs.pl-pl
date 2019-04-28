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
ms.openlocfilehash: a107689796c58b17c445e7a9cf7c6f0402ef6005
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440152"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Wykrywanie i rozwiązywanie problemów z zamknie połączenie z usługą Azure IoT Hub

Problemy z łącznością w przypadku urządzeń IoT może być trudne do rozwiązania, ponieważ istnieje wiele możliwych punktów awarii. Logika aplikacji po stronie urządzenia, sieciach fizycznych, protokołów, sprzętu i usługi Azure IoT Hub wszystkie może powodować problemy. Ten artykuł zawiera zalecenia dotyczące sposobu wykrywania i urządzenia Rozwiązywanie problemów z łącznością ze strony chmury (w przeciwieństwie do urządzeń po stronie).

## <a name="get-alerts-and-error-logs"></a>Otrzymuj alerty i dzienniki błędów

Usługa Azure Monitor umożliwia otrzymywanie alertów i zapisują dzienniki w przypadku, gdy porzucić połączenia urządzenia.

### <a name="turn-on-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

Aby rejestrować zdarzenia połączenia urządzenia i błędy, Włącz diagnostykę usługi IoT Hub.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do Centrum IoT hub.

3. Wybierz **ustawień diagnostycznych**.

4. Wybierz **Włącz diagnostykę**.

5. Włącz **połączeń** dzienników, które mają być zbierane.

6. Ułatwia analizowanie włączyć **wysyłanie do usługi Log Analytics** ([Zobacz cennik](https://azure.microsoft.com/pricing/details/log-analytics/)). Zobacz przykład w obszarze [rozwiązać błędy związane z łącznością](#resolve-connectivity-errors).

   ![Zalecane ustawienia](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Aby dowiedzieć się więcej, zobacz [monitorowania kondycji usługi Azure IoT Hub i szybkie diagnozowanie problemów](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>Konfigurowanie alertów dotyczących _połączone urządzenia_ liczba metryki

Uzyskiwanie alertów w przypadku rozłączenia urządzeń, Konfigurowanie alertów dotyczących **połączone urządzenia (wersja zapoznawcza)** metryki.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do Centrum IoT hub.

3. Wybierz **alerty**.

4. Wybierz **Nowa reguła alertu**.

5. Wybierz **Dodaj warunek**, następnie wybierz pozycję "Połączone urządzenia (wersja zapoznawcza)".

6. Zakończ konfigurowanie Twojego odpowiednie progi i alerty opcje, określ poniższe informacje.

Aby dowiedzieć się więcej, zobacz [co to są alertów klasycznych na platformie Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

## <a name="resolve-connectivity-errors"></a>Usuń błędy łączności

Włącz dzienniki diagnostyczne i alerty dla połączonych urządzeń, możesz otrzymywać alerty, jeśli wystąpią błędy. W tej sekcji opisano sposób rozwiązać typowe problemy, gdy zostanie wyświetlony alert. W poniższych krokach przyjęto, że po skonfigurowaniu usługi Azure Monitor dzienników dla dzienników diagnostycznych.

1. Szukać obszaru roboczego **usługi Log Analytics** w witrynie Azure portal.

2. Wybierz **wyszukiwania w dzienniku**.

3. Aby wyizolować dzienniki błędów łączności dla usługi IoT Hub, wprowadź następujące zapytanie, a następnie wybierz pozycję **Uruchom**:

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. W przypadku wyników poszukaj `OperationName`, `ResultType` (kod błędu) i `ResultDescription` (komunikat), aby uzyskać więcej informacji na temat błędu.

   ![Przykładowy dziennik błędów](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

2. Ta tabela służy do zrozumienia i rozwiązania typowych problemów.

    | Błąd | Główna przyczyna | Rozwiązanie |
    |-------|------------|------------|
    | 404104 DeviceConnectionClosedRemotely | Połączenie zostało zamknięte przez urządzenie, ale nie może ustalić, dlaczego usługi IoT Hub. Typowe przyczyny MQTT/AMQP limitu czasu i internet utraty łączności. | Upewnij się, że urządzenie może połączyć się do usługi IoT Hub za [Trwa testowanie połączenia](tutorial-connectivity.md). Jeśli połączenie jest poprawne, ale urządzenie jest sporadycznie rozłączane, upewnij się zaimplementować logikę aktywne urządzenie Zachowaj właściwe dla wybranego protokołu (MQTT/AMPQ). |
    | 401003 IoTHubUnauthorized | Usługi IoT Hub nie można uwierzytelnić połączenia. | Upewnij się, że sygnatury dostępu Współdzielonego lub innych token zabezpieczający, którego używasz nie jest uznawane za wygasłe. [Usługa Azure IoT SDKs](iot-hub-devguide-sdks.md) automatycznego generowania tokenów bez specjalnej konfiguracji. |
    | 409002 LinkCreationConflict | Urządzenie ma więcej niż jedno połączenie. Po przejściu do trybu nowego żądania połączenia dla urządzenia, usłudze IoT Hub zamyka poprzedniemu, z powodu następującego błędu. | W typowych przypadkach urządzenia wykrywa rozłączenia i próbuje ponownie nawiązać połączenie, ale usługi IoT Hub nadal uważa się urządzenie podłączone. Usługi IoT Hub zamyka poprzednie połączenie i rejestruje tego błędu. Zazwyczaj ten błąd pojawia się jako efekt uboczny różnych przejściowy problem, więc poszukaj inne błędy w dziennikach do dalszego rozwiązywania. W przeciwnym razie upewnij się, że wystawiania nowego żądania połączenia, tylko wtedy, gdy połączenie spada. |
    | Błąd ServerError 500001 | Usługa IoT Hub wystąpił problem po stronie serwera. Najprawdopodobniej tego problemu jest przejściowy. Podczas działania zespołu usługi IoT Hub trudne do utrzymania [umowy SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/), mały podzbiór węzłów usługi IoT Hub od czasu do czasu mogą występować błędy przejściowe. Gdy urządzenie próbuje połączyć się z węzłem, który występują problemy, ten błąd jest wyświetlany. | Aby uniknąć błędów przejściowych, należy wydać ponawiania z urządzenia. Aby [automatycznie zarządzała ponownych prób](iot-hub-reliability-features-in-sdks.md#connection-and-retry), upewnij się, że używasz najnowszej wersji [Azure IoT SDKs](iot-hub-devguide-sdks.md).<br><br>Ze względów na liczbę ponownych prób i obsługi błędów przejściowych, zobacz [obsługi błędów przejściowych](/azure/architecture/best-practices/transient-faults).  <br><br>Jeśli problem będzie się powtarzać, ponowne próby, sprawdź [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) i [stanu platformy Azure](https://azure.microsoft.com/status/history/) można sprawdzić, czy w usłudze IoT Hub to znany problem. Jeśli ma żadnych znanych problemów, a problem będzie się powtarzać, [się z pomocą techniczną](https://azure.microsoft.com/support/options/) celu bliższego zbadania problemu. |
    | 500008 GenericTimeout | Usługa IoT Hub, nie można ukończyć żądania połączenia przed przekroczeniem limitu czasu. Podobnie jak 500001 błąd ServerError ten błąd jest prawdopodobnie przejściowy. | Wykonaj kroki rozwiązywania problemów dla 500001 błąd ServerError w na główną przyczynę i rozwiązać ten problem.|

## <a name="other-steps-to-try"></a>Pozostałe kroki do wypróbowania

Jeśli poprzednie kroki nie pomogły, możesz wypróbować:

* Jeśli masz dostęp do urządzeń problematyczne fizycznie lub zdalnie (na przykład protokołu SSH), postępuj zgodnie z [przewodnik rozwiązywania problemów po stronie urządzenia](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) aby kontynuować rozwiązywanie problemów.

* Sprawdź, czy urządzenia są **włączone** w witrynie Azure portal > usługi IoT hub > urządzeń IoT.

* Uzyskaj pomoc od [forum usługi Azure IoT Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub), lub [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/).

Aby pomóc udoskonalić dokumentację dla wszystkich użytkowników, pozostaw komentarz w poniższej sekcji opinii, jeśli to nie być pomocny ten przewodnik.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat rozwiązywania problemów przejściowych, zobacz [obsługi błędów przejściowych](/azure/architecture/best-practices/transient-faults).

* Dowiedz się więcej o usłudze Azure IoT SDK do zarządzania ponowieniami, zobacz [sposobu zarządzania łącznością i niezawodna obsługa komunikatów za pomocą zestawów SDK urządzeń Azure IoT Hub](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
