---
title: Utwórz regułę niestandardową w pakiecie Azure IoT Suite | Dokumentacja firmy Microsoft
description: Jak utworzyć niestandardową regułę w pakiecie IoT Suite wstępnie skonfigurowanego rozwiązania.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 9bf2a13035de141766fd935966ce18459dccdaab
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092632"
---
# <a name="create-a-custom-rule-in-the-remote-monitoring-preconfigured-solution"></a>Utwórz regułę niestandardową w wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego

## <a name="introduction"></a>Wprowadzenie

W przypadku wstępnie skonfigurowanych rozwiązań można skonfigurować [wartość reguły, które mogą powodować podczas telemetrii urządzenia osiągnie określony próg][lnk-builtin-rule]. [Użyj telemetrii dynamicznej dzięki zdalnemu monitorowaniu wstępnie skonfigurowanego rozwiązania] [ lnk-dynamic-telemetry] opisuje sposób dodawania wartości niestandardowych danych telemetrycznych, takich jak *ExternalTemperature* do rozwiązania. W tym artykule pokazano, jak utworzyć niestandardową regułę dla typów telemetrii dynamicznej w rozwiązaniu.

W tym samouczku używa prostego urządzenia symulowanego środowiska Node.js do wygenerowania telemetrii dynamicznej do wysłania do wstępnie skonfigurowanego rozwiązania zaplecza. Następnie dodaj niestandardowe reguły w **RemoteMonitoring** rozwiązania Visual Studio i wdrażanie tego dostosowane zaplecza do subskrypcji platformy Azure.

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Aktywna subskrypcja platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure][lnk_free_trial].
* [Node.js] [ lnk-node] wersji 0.12.x lub nowszej, aby utworzyć symulowane urządzenie.
* Visual Studio 2015 lub Visual Studio 2017, aby zmodyfikować ponownie wstępnie skonfigurowanego rozwiązania kończy się Twoje nowe zasady.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

Zanotuj nazwę rozwiązania, który został wybrany dla danego wdrożenia. Nazwa tego rozwiązania są potrzebne w dalszej części tego samouczka.

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

Po upewnieniu się, że jest wysyłany, można zatrzymać aplikacji konsoli środowiska Node.js **ExternalTemperature** danych telemetrycznych do wstępnie skonfigurowanego rozwiązania. Nie zamykaj okna konsoli ponieważ ponownie uruchomić tę aplikację konsoli Node.js po dodaniu niestandardowej reguły do rozwiązania.

## <a name="rule-storage-locations"></a>Lokalizacje przechowywania reguły

Informacje o regułach są utrwalane w dwóch miejscach:

* **DeviceRulesNormalizedTable** tabeli — znormalizowane odwołanie od reguł zdefiniowanych w portalu rozwiązania są przechowywane w tej tabeli. Reguły urządzeń są wyświetlane w portalu rozwiązania, wysyła zapytanie tej tabeli do definicji reguł.
* **DeviceRules** obiektów blob — ten obiekt blob przechowuje wszystkie reguły zdefiniowane dla wszystkich urządzeń zarejestrowanych i jest definiowany jako dane wejściowe do zadania usługi Azure Stream Analytics odwołania.
 
Aktualizuj istniejącą regułę, lub zdefiniować nową regułę w portalu rozwiązania, tabelę i obiektów blob są aktualizowane zgodnie ze zmianami. Definicja reguły wyświetlana w portalu pochodzą z magazynu tabel i definicji reguły przywoływane przez zadania usługi Stream Analytics pochodzą z obiektu blob. 

## <a name="update-the-remotemonitoring-visual-studio-solution"></a>Aktualizacja RemoteMonitoring rozwiązania Visual Studio

Poniższe kroki pokazują sposób modyfikowania RemoteMonitoring rozwiązanie programu Visual Studio do uwzględnienia nową regułę, która używa **ExternalTemperature** telemetrii wysyłanych z symulowanego urządzenia:

1. Jeśli jeszcze tego nie zrobiono, sklonuj **azure-iot-remote-monitoring** repozytorium do odpowiedniej lokalizacji na komputerze lokalnym przy użyciu następującego polecenia Git:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. W programie Visual Studio, otwórz plik RemoteMonitoring.sln lokalną kopię **azure-iot-remote-monitoring** repozytorium.

3. Otwórz plik Infrastructure\Models\DeviceRuleBlobEntity.cs i Dodaj **ExternalTemperature** właściwości w następujący sposób:

    ```csharp
    public double? Temperature { get; set; }
    public double? Humidity { get; set; }
    public double? ExternalTemperature { get; set; }
    ```

4. W tym samym pliku Dodaj **ExternalTemperatureRuleOutput** właściwości w następujący sposób:

    ```csharp
    public string TemperatureRuleOutput { get; set; }
    public string HumidityRuleOutput { get; set; }
    public string ExternalTemperatureRuleOutput { get; set; }
    ```

5. Otwórz plik Infrastructure\Models\DeviceRuleDataFields.cs i Dodaj następujący kod **ExternalTemperature** właściwości po istniejącej **wilgotności** właściwości:

    ```csharp
    public static string ExternalTemperature
    {
        get { return "ExternalTemperature"; }
    }
    ```

6. W tym samym pliku, należy zaktualizować **_availableDataFields** metodę, aby uwzględnić **ExternalTemperature** w następujący sposób:

    ```csharp
    private static List<string> _availableDataFields = new List<string>
    {                    
        Temperature, Humidity, ExternalTemperature
    };
    ```

7. Otwórz plik Infrastructure\Repository\DeviceRulesRepository.cs i modyfikować **BuildBlobEntityListFromTableRows** metody w następujący sposób:

    ```csharp
    else if (rule.DataField == DeviceRuleDataFields.Humidity)
    {
        entity.Humidity = rule.Threshold;
        entity.HumidityRuleOutput = rule.RuleOutput;
    }
    else if (rule.DataField == DeviceRuleDataFields.ExternalTemperature)
    {
      entity.ExternalTemperature = rule.Threshold;
      entity.ExternalTemperatureRuleOutput = rule.RuleOutput;
    }
    ```

## <a name="rebuild-and-redeploy-the-solution"></a>Ponownie skompiluj i wdróż rozwiązanie.

Teraz można wdrożyć zaktualizowane rozwiązanie z subskrypcją platformy Azure.

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień i przejdź do katalogu głównego lokalnej kopii repozytorium azure-iot-remote-monitoring.

2. Aby wdrożyć zaktualizowany rozwiązania, uruchom polecenie następujących, zastępując **{Nazwa wdrożenia}** o nazwie zanotowaną wcześniej wdrażania wstępnie skonfigurowanego rozwiązania:

    ```
    build.cmd cloud release {deployment name}
    ```

## <a name="update-the-stream-analytics-job"></a>Aktualizacja zadania usługi Stream Analytics

Po zakończeniu wdrożenia należy zaktualizować zadania usługi Stream Analytics, aby użyć nowych definicji reguły.

1. W witrynie Azure portal przejdź do grupy zasobów, zawierającej zasobami we wstępnie skonfigurowanym rozwiązaniu. Ta grupa zasobów ma taką samą nazwę, wskazana dla rozwiązania podczas wdrażania.

2. Przejdź do {Nazwa wdrożenia} — zadanie reguły Stream Analytics. 

3. Kliknij przycisk **zatrzymać** można zatrzymać zadania usługi Stream Analytics uruchamianie. (Należy poczekać zadania przesyłania strumieniowego zatrzymać, zanim będzie można edytować zapytania).

4. Kliknij przycisk **zapytania**. Edytuj zapytanie, aby uwzględnić **wybierz** poufności informacji dotyczące **ExternalTemperature**. Poniższy przykład pokazuje kompletny zapytania przy użyciu nowego **wybierz** instrukcji:

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
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'ExternalTemperature' as ReadingType,
         Stream.ExternalTemperature as Reading,
         Ref.ExternalTemperature as Threshold,
         Ref.ExternalTemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.ExternalTemperature IS NOT null AND Stream.ExternalTemperature > Ref.ExternalTemperature
    )
     
    SELECT *
    INTO DeviceRulesMonitoring
    FROM AlarmsData
     
    SELECT *
    INTO DeviceRulesHub
    FROM AlarmsData
    ```

5. Kliknij przycisk **Zapisz** zmienić kwerendę Zaktualizowano reguły.

6. Kliknij przycisk **Start** można uruchomić zadania usługi Stream Analytics, ponowne uruchomienie.

## <a name="add-your-new-rule-in-the-dashboard"></a>Dodaj nową regułę na pulpicie nawigacyjnym

Teraz możesz dodać **ExternalTemperature** zasadę, aby urządzenia na pulpicie nawigacyjnym rozwiązania.

1. Przejdź do portalu rozwiązania.

2. Przejdź do **urządzeń** panelu.

3. Zlokalizuj urządzenie niestandardowe utworzone, które wysyła **ExternalTemperature** telemetrii i **szczegóły urządzenia** panelu, kliknij przycisk **Dodaj regułę**.

4. Wybierz **ExternalTemperature** w **pola danych**.

5. Ustaw **próg** do 56. Następnie kliknij przycisk **Zapisz i Wyświetl reguły**.

6. Wróć do pulpitu nawigacyjnego w celu wyświetlenia historii alarmów.

7. W oknie konsoli, użytkownik pozostanie otwarte, należy uruchomić aplikację konsoli środowiska Node.js, aby rozpocząć wysyłanie **ExternalTemperature** dane telemetryczne.

8. Należy zauważyć, że **Historia alarmów** tabeli przedstawiono nowe alarmy, gdy nowa reguła zostanie wyzwolony.
 
## <a name="additional-information"></a>Dodatkowe informacje

Zmiana operator **>** jest bardziej złożona i wykracza poza kroki opisane w tym samouczku. Możesz zmienić zadania usługi Stream Analytics, niezależnie od operatora chcesz używać, odzwierciedlający tego operatora w portalu rozwiązania jest bardziej złożone zadania. 

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy wiesz jak utworzyć reguły niestandardowe, można dowiedzieć się więcej o wstępnie skonfigurowanych rozwiązań:

- [Łączenie aplikacji logiki do usługi Azure IoT Suite zdalne monitorowanie wstępnie skonfigurowanego rozwiązania][lnk-logic-app]
- [Urządzenie informacji metadanych do zdalnego monitorowania wstępnie skonfigurowanego rozwiązania][lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[lnk-builtin-rule]: iot-suite-v1-getstarted-preconfigured-solutions.md#view-alarms
[lnk-dynamic-telemetry]: iot-suite-v1-dynamic-telemetry.md
[lnk-logic-app]: iot-suite-v1-logic-apps-tutorial.md