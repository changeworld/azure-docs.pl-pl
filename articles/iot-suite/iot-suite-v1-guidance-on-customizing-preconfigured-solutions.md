---
title: Dostosowywanie wstępnie skonfigurowanych rozwiązań | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące sposobu dostosowywania rozwiązania Azure IoT Suite, wstępnie skonfigurowane.
services: ''
suite: iot-suite
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 4653ae53-4110-4a10-bd6c-7dc034c293a8
ms.service: iot-suite
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: cb5955111cb3954f71f11602042b5153ccee3473
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47106920"
---
# <a name="customize-a-preconfigured-solution"></a>Dostosowanie wstępnie skonfigurowanego rozwiązania

Wstępnie skonfigurowane rozwiązania, które są dostarczane z pakietem Azure IoT Suite pokazują usług w pakiecie wspólnie pracują, aby dostarczać rozwiązania end-to-end. Istnieją różnych miejscach, w których można rozszerzyć i dostosowywać rozwiązań dla konkretnych scenariuszy, z tego punktu wyjścia. W poniższych sekcjach opisano te wspólnych punktów dostosowywania.

## <a name="find-the-source-code"></a>Znajdowanie kodu źródłowego

Kod źródłowy wstępnie skonfigurowanego rozwiązania jest dostępna w witrynie GitHub w repozytoriach następujące:

* Zdalne monitorowanie: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* Konserwacja predykcyjna: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)
* Połączona fabryka: [https://github.com/Azure/azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory)

Aby zademonstrować, wzorców i praktyk, używane do implementowania funkcji end-to-end rozwiązania IoT przy użyciu usługi Azure IoT Suite znajduje się kod źródłowy wstępnie skonfigurowanego rozwiązania. Można znaleźć więcej informacji na temat sposobu tworzenia i wdrażania rozwiązań w repozytoriach usługi GitHub.

## <a name="change-the-preconfigured-rules"></a>Zmień wstępnie skonfigurowanych reguł

Rozwiązania do monitorowania zdalnego obejmuje trzy [usługi Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) zadania do obsługi informacji o urządzeniu, telemetrii i logiki reguły w rozwiązaniu.

Trzy zadania usługi stream analytics i ich składnię są opisane szczegółowo w [zdalne monitorowanie wstępnie skonfigurowanym rozwiązaniu](iot-suite-v1-remote-monitoring-sample-walkthrough.md). 

Możesz edytować te zadania bezpośrednio, aby zmienić logiki lub dodać logikę określoną dla scenariusza. Zadania usługi Stream Analytics można znaleźć w następujący sposób:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
2. Przejdź do grupy zasobów o nazwie identycznej z nazwą swojego rozwiązania IoT. 
3. Wybierz zadania usługi Azure Stream Analytics, które chcesz zmodyfikować. 
4. Zatrzymaj zadanie, wybierając **zatrzymać** zestawu poleceń. 
5. Edytowanie danych wejściowych, zapytań i danych wyjściowych.
   
    Jest prostą modyfikację, aby zmienić kwerendę dla **reguły** zadanie, aby użyć **"<"** zamiast **">"**. W portalu rozwiązania jest nadal wyświetlany **">"** podczas edytowania reguły, ale Zwróć uwagę, jak zachowanie jest odwrócony z powodu zmiany w podstawowej zadania.
6. Uruchamianie zadania

> [!NOTE]
> Pulpit nawigacyjny monitorowania zdalnego jest zależna od określonych danych, tak zmieniając zadania może spowodować, że pulpit nawigacyjny, aby zakończyć się niepowodzeniem.

## <a name="add-your-own-rules"></a>Dodać własne reguły

Validated wstępnie skonfigurowane zadania usługi Azure Stream Analytics, można użyć witryny Azure portal Dodaj nowe zadania lub dodanie nowych zapytań do istniejących zadań.

## <a name="customize-devices"></a>Dostosowywanie urządzeń

Jedno z najbardziej typowych działań rozszerzenie współpracuje z urządzeniami specyficzne dla danego scenariusza. Istnieje kilka metod do pracy z urządzenia. Te metody obejmują Zmienianie symulowane urządzenie, aby dopasować do danego scenariusza, oraz przy użyciu [zestawu SDK urządzenia IoT] [ IoT Device SDK] do łączenia z urządzenia fizycznego do rozwiązania.

Przewodnik krok po kroku do dodawania urządzenia można zobaczyć [Iot Suite łączenie urządzeń](iot-suite-v1-connecting-devices.md) artykułu i [zdalne monitorowanie Przykładowy zestaw SDK C](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring). W tym przykładzie jest przeznaczona do pracy z wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego.

### <a name="create-your-own-simulated-device"></a>Tworzenie symulowanego urządzenia

Uwzględnione w [kod źródłowy rozwiązania do monitorowania zdalnego](https://github.com/Azure/azure-iot-remote-monitoring), jest symulator .NET. Ten symulator jest aprowizowany w ramach rozwiązania i zmieniać, Wyślij innych metadanych i telemetrii, i reagować na różnych poleceń i metod.

Wstępnie skonfigurowane symulatora w zdalnym wstępnie skonfigurowanego rozwiązania monitorowania symuluje chłodniejszej urządzenia, który emituje dane telemetryczne dotyczące temperatury i wilgotności. Możesz zmodyfikować symulatora w [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) projektu, gdy został utworzony rozwidlenie repozytorium GitHub.

### <a name="available-locations-for-simulated-devices"></a>Dostępne lokalizacje symulowanych urządzeń

Domyślny zestaw lokalizacji jest w Seattle/Redmond, Washington, USA. Możesz zmienić te lokalizacje w [SampleDeviceFactory.cs][lnk-sample-device-factory].

### <a name="add-a-desired-property-update-handler-to-the-simulator"></a>Dodaj program obsługi aktualizacji żądaną właściwość w symulatorze

Można ustawić wartość żądanej właściwości urządzenia w portalu rozwiązania. Jest odpowiedzialność urządzenia w celu obsługi właściwości żądania zmiany, kiedy urządzenie pobiera wartość żądanej właściwości. Aby dodać obsługę zmiana wartości właściwości, za pośrednictwem żądaną właściwość, należy dodać program obsługi do symulatora.

Symulator zawiera programy obsługi dla **SetPointTemp** i **TelemetryInterval** aktualizowanych przez ustawienie właściwości żądane wartości w portalu rozwiązania.

W poniższym przykładzie pokazano program obsługi dla **SetPointTemp** żądaną właściwość w **CoolerDevice** klasy:

```csharp
protected async Task OnSetPointTempUpdate(object value)
{
    var telemetry = _telemetryController as ITelemetryWithSetPointTemperature;
    telemetry.SetPointTemperature = Convert.ToDouble(value);

    await SetReportedPropertyAsync(SetPointTempPropertyName, telemetry.SetPointTemperature);
}
```

Ta metoda aktualizuje temperatury punktów danych telemetrycznych i następnie raportuje zmiany ją do usługi IoT Hub, ustawiając zgłaszanej właściwości.

Możesz dodać własne programy obsługi dla własnych właściwości, postępując zgodnie ze wzorcem w poprzednim przykładzie.

Należy również powiązać żądaną właściwość do programu obsługi, jak pokazano w następującym przykładzie **CoolerDevice** Konstruktor:

```csharp
_desiredPropertyUpdateHandlers.Add(SetPointTempPropertyName, OnSetPointTempUpdate);
```

Należy pamiętać, że **SetPointTempPropertyName** jest stałą, który został zdefiniowany jako "Config.SetPointTemp".

### <a name="add-support-for-a-new-method-to-the-simulator"></a>Dodanie obsługi nowej metody w symulatorze

Można dostosować symulator, aby dodać nową obsługę [metody (metoda bezpośrednia)][lnk-direct-methods]. Istnieją dwa kluczowe kroki wymagane:

- Symulator musi powiadomić usługi IoT hub we wstępnie skonfigurowanym rozwiązaniu szczegółowe informacje o metodzie.
- Symulator mogą zawierać kod obsługujący wywołania metody, gdy wywołujesz ją z **szczegóły urządzenia** panelu w Eksploratorze rozwiązań lub przy użyciu zadania.

Zdalne monitorowanie wstępnie skonfigurowane rozwiązanie używa *zgłaszanych właściwości* wysłać szczegółowe informacje o obsługiwanych metod do usługi IoT hub. Zaplecze rozwiązania przechowuje listę wszystkich metod, które są obsługiwane przez poszczególne urządzenia wraz z historii wywołań metod. Można wyświetlić te informacje o urządzeniach i wywoływanie metod w portalu rozwiązania.

Aby powiadomić usługi IoT hub, czy urządzenie obsługuje metodę, urządzenia, należy dodać szczegóły metody **SupportedMethods** węzła w zgłoszonych właściwości:

```json
"SupportedMethods": {
  "<method signature>": "<method description>",
  "<method signature>": "<method description>"
}
```

Podpis metody ma następujący format: `<method name>--<parameter #0 name>-<parameter #1 type>-...-<parameter #n name>-<parameter #n type>`. Na przykład, aby określić **InitiateFirmwareUpdate** metoda oczekuje jako parametr ciągu o nazwie **FwPackageURI**, użyj następujący podpis metody:

```json
InitiateFirmwareUpate--FwPackageURI-string: "description of method"
```

Aby uzyskać listę obsługiwane typy parametrów, zobacz **CommandTypes** klasy w projekcie infrastruktury.

Aby usunąć metodę, Ustaw podpis metody `null` zgłoszonych właściwości.

> [!NOTE]
> Zaplecze rozwiązania tylko zaktualizowanie informacji o obsługiwanych metodach po odebraniu *informacje o urządzeniu* komunikatów z urządzenia.

Poniższy przykładowy kod z **SampleDeviceFactory** klasy w projekcie wspólnej pokazuje, jak dodać metodę do listy **SupportedMethods** w zgłaszane właściwości wysyłane przez urządzenie:

```csharp
device.Commands.Add(new Command(
    "InitiateFirmwareUpdate",
    DeliveryType.Method,
    "Updates device Firmware. Use parameter 'FwPackageUri' to specifiy the URI of the firmware file, e.g. https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin",
    new[] { new Parameter("FwPackageUri", "string") }
));
```

Ten fragment kodu dodaje szczegóły **InitiateFirmwareUpdate** metody, w tym tekst do wyświetlenia w portalu rozwiązania i szczegółowe informacje dotyczące parametrów wymaganej metody.

Symulator wysyła zgłaszane właściwości, łącznie z listą obsługiwanych metod do usługi IoT Hub podczas uruchamiania symulatora.

Dodaj program obsługi do kod symulatora dla poszczególnych metod, które obsługuje. Możesz zobaczyć istniejące programy obsługi w **CoolerDevice** klasy w projekcie Simulator.WebJob. W poniższym przykładzie pokazano program obsługi dla **InitiateFirmwareUpdate** metody:

```csharp
public async Task<MethodResponse> OnInitiateFirmwareUpdate(MethodRequest methodRequest, object userContext)
{
    if (_deviceManagementTask != null && !_deviceManagementTask.IsCompleted)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "Device is busy"
        }, 409));
    }

    try
    {
        var operation = new FirmwareUpdate(methodRequest);
        _deviceManagementTask = operation.Run(Transport).ContinueWith(async task =>
        {
            // after firmware completed, we reset telemetry
            var telemetry = _telemetryController as ITelemetryWithTemperatureMeanValue;
            if (telemetry != null)
            {
                telemetry.TemperatureMeanValue = 34.5;
            }

            await UpdateReportedTemperatureMeanValue();
        });

        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "FirmwareUpdate accepted",
            Uri = operation.Uri
        }));
    }
    catch (Exception ex)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = ex.Message
        }, 400));
    }
}
```

Metoda nazwy programów obsługi musi rozpoczynać się `On` następuje nazwa metody. **MethodRequest** parametr zawiera parametry przekazywane wywołanie metody z zapleczem rozwiązania. Zwracana wartość musi być typu **zadań&lt;MethodResponse&gt;**. **BuildMethodResponse** metoda narzędzie pomoże Ci w utworzeniu wartość zwracaną.

Wewnątrz metody obsługi można wykonać następujące akcje:

- Uruchom zadanie asynchroniczne.
- Pobieranie żądanych właściwości z *bliźniaczej reprezentacji urządzenia* w usłudze IoT Hub.
- Zaktualizować za pomocą pojedynczego zgłaszanych właściwości **SetReportedPropertyAsync** method in Class metoda **CoolerDevice** klasy.
- Aktualizowanie wielu zgłaszanych właściwości, tworząc **TwinCollection** wystąpienie i wywołania **Transport.UpdateReportedPropertiesAsync** metody.

W poprzednim przykładzie aktualizacji oprogramowania układowego wykonuje następujące czynności:

- Sprawdza, czy urządzenie jest w stanie zaakceptować żądanie aktualizacji oprogramowania układowego.
- Asynchronicznie inicjuje operację aktualizacji oprogramowania układowego i przywraca dane telemetryczne po zakończeniu operacji.
- Natychmiast zwraca komunikat "FirmwareUpdate zaakceptowane", aby wskazać, że żądanie zostało zaakceptowane przez urządzenie.

### <a name="build-and-use-your-own-physical-device"></a>Tworzenie i używanie urządzenia (fizycznego)

[Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks) udostępniają biblioteki łączenia wielu typów urządzeń (języków i systemów operacyjnych) w rozwiązaniach IoT.

## <a name="modify-dashboard-limits"></a>Modyfikowanie limity pulpitu nawigacyjnego

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Liczba urządzeń wyświetlane na liście rozwijanej pulpitu nawigacyjnego

Wartość domyślna to 200. Możesz zmienić tę wartość w [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Liczby kodów PIN wyświetlanie w formancie mapy Bing

Wartość domyślna to 200. Możesz zmienić tę wartość w [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Okres telemetrii wykresu

Wartość domyślna to 10 minut. Można zmienić tę wartość w [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="feedback"></a>Opinia

Czy masz dostosowania chcesz zobaczyć został opisany w tym dokumencie? Dodaj funkcję sugestii w zakresie [User Voice](https://feedback.azure.com/forums/321918-azure-iot), lub komentarza, w tym artykule. 

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o opcjach dostosowywania wstępnie skonfigurowanych rozwiązań, zobacz:

* [Łączenie aplikacji logiki do usługi Azure IoT Suite zdalne monitorowanie wstępnie skonfigurowanego rozwiązania][lnk-logicapp]
* [Korzystanie z telemetrii dynamicznej z wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego][lnk-dynamic]
* [Urządzenie informacji o metadanych w wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego][lnk-devinfo]
* [Dostosowywanie sposobu wyświetlania danych z serwerów OPC UA w rozwiązaniu połączonej fabryki][lnk-cf-customize]

[lnk-logicapp]: iot-suite-v1-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-cf-customize]:../iot-accelerators/iot-accelerators-connected-factory-customize.md