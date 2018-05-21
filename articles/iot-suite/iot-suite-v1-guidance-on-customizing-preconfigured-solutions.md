---
title: Dostosowywanie wstępnie rozwiązania | Dokumentacja firmy Microsoft
description: Zawiera wskazówki dotyczące sposobu dostosowywania rozwiązań wstępnie pakiet IoT Azure.
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
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="customize-a-preconfigured-solution"></a>Dostosowanie wstępnie skonfigurowanego rozwiązania

Wstępnie skonfigurowanych rozwiązań wyposażone w pakiet IoT Azure przedstawienie usług w obrębie zestawu, które współpracują, aby dostarczać rozwiązanie end-to-end. Z tego punktu początkowego istnieją różnych miejscach, w których można rozszerzać i dostosować rozwiązania dla konkretnych scenariuszy. W poniższych sekcjach opisano typowe punkty dostosowania.

## <a name="find-the-source-code"></a>Znaleźć kodu źródłowego

Kod źródłowy dla wstępnie skonfigurowanych rozwiązań jest dostępna w witrynie GitHub w następujących repozytoria:

* Monitorowania zdalnego: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* Konserwacji predykcyjnej: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)
* Fabryka połączenia: [https://github.com/Azure/azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory)

Aby zademonstrować wzorców i procedur wykorzystywanych do implementowania end-to-end rozwiązania IoT przy użyciu pakietu IoT Azure znajduje się kod źródłowy dla wstępnie skonfigurowanych rozwiązań. Można znaleźć więcej informacji na temat sposobu tworzenia i wdrażania rozwiązań w repozytoriów GitHub.

## <a name="change-the-preconfigured-rules"></a>Zmień wstępnie skonfigurowanych reguł

Rozwiązanie monitorowania zdalnego obejmuje trzy [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) zadania obsługi informacji o urządzeniu, telemetrii i logiki reguły w rozwiązaniu.

Trzy strumienia zadania usługi analiza i ich składni opisano szczegółowo w [monitorowania zdalnego wstępnie skonfigurowane rozwiązanie wskazówki](iot-suite-v1-remote-monitoring-sample-walkthrough.md). 

Edytowanie tych zadań bezpośrednio do alter logiki lub dodać logikę określonych do danego scenariusza. Zadania usługi analiza strumienia można znaleźć w następujący sposób:

1. Przejdź do [portalu Azure](https://portal.azure.com).
2. Przejdź do grupy zasobów o nazwie identycznej z nazwą rozwiązania IoT. 
3. Wybierz zadanie usługi analiza strumienia Azure, którą chcesz zmodyfikować. 
4. Zatrzymaj zadanie, wybierając **zatrzymać** zestawu poleceń. 
5. Edytowanie danych wejściowych, zapytań i danych wyjściowych.
   
    Prostą modyfikację jest zmiana kwerendy dotyczącej **reguły** zadanie, aby użyć **"<"** zamiast **">"**. Portal rozwiązania pozostanie **">"** podczas edycji reguły, ale Zwróć uwagę, jak zachowanie jest odwrócony z powodu zmiany w podstawowym zadania.
6. Uruchamianie zadania

> [!NOTE]
> Zdalnego pulpitu nawigacyjnego monitorowania zależy od określonych danych, więc zmiany zadania może spowodować niepowodzenie pulpitu nawigacyjnego.

## <a name="add-your-own-rules"></a>Dodaj własne reguły

Oprócz zmiana wstępnie skonfigurowane zadania usługi analiza strumienia Azure, można użyć portalu Azure, aby dodać nowe zadania, lub dodawanie nowych zapytań do istniejącego zadania.

## <a name="customize-devices"></a>Dostosowywanie urządzeń

Jedną z najbardziej typowych działań rozszerzenia jest praca z urządzeniami, które są specyficzne dla danego scenariusza. Istnieje kilka metod do pracy z urządzeń. Te metody obejmują zmiany symulowane urządzenie, aby dopasować danego scenariusza lub przy użyciu [SDK urządzenia IoT] [ IoT Device SDK] Podłącz urządzenie fizyczne do rozwiązania.

Przewodnik krok po kroku dotyczący dodawania urządzeń, zobacz [urządzenia łączenie pakiet Iot](iot-suite-v1-connecting-devices.md) artykułu i [zdalnego monitorowania przykład zestawu SDK C](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring). Ten przykład jest przeznaczona do pracy z zdalnego wstępnie skonfigurowane rozwiązanie monitorowania.

### <a name="create-your-own-simulated-device"></a>Utworzyć symulowane urządzenie

Uwzględnione w [zdalnej kontroli kodu źródłowego rozwiązania](https://github.com/Azure/azure-iot-remote-monitoring), jest symulatora .NET. Ta symulator jest udostępniane w ramach rozwiązania i można zmienić go wysłać innych metadanych, telemetrii, i reagowanie na inne polecenia i metody.

Symulator wstępnie skonfigurowane w zdalnym wstępnie skonfigurowane rozwiązanie monitorowania symuluje lodówki urządzenia, który emituje temperatury i wilgotności telemetrii. Można zmodyfikować w symulatorze [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) projektu, gdy zostały rozwidlone repozytorium GitHub.

### <a name="available-locations-for-simulated-devices"></a>Dostępne lokalizacje symulowanego urządzenia

Domyślny zbiór lokalizacji jest w Seattle/Redmond, Washington, USA. Możesz zmienić te lokalizacje w [SampleDeviceFactory.cs][lnk-sample-device-factory].

### <a name="add-a-desired-property-update-handler-to-the-simulator"></a>Dodaj program obsługi aktualizacji żądanej właściwości do symulatora

Można ustawić wartości dla żądanej właściwości dla urządzenia w portalu rozwiązania. Jest obowiązkiem urządzenia do obsługi właściwości żądania zmiany, gdy urządzenie pobiera wartość żądanej właściwości. Aby dodać obsługę zmiany wartości właściwości w ramach żądanej właściwości, należy dodać program obsługi do symulatora.

Symulator zawiera obsługę **SetPointTemp** i **TelemetryInterval** aktualizowanych przez ustawienie właściwości żądane wartości w portalu rozwiązania.

W poniższym przykładzie pokazano programu obsługi **SetPointTemp** wymaganą właściwość w **CoolerDevice** klasy:

```csharp
protected async Task OnSetPointTempUpdate(object value)
{
    var telemetry = _telemetryController as ITelemetryWithSetPointTemperature;
    telemetry.SetPointTemperature = Convert.ToDouble(value);

    await SetReportedPropertyAsync(SetPointTempPropertyName, telemetry.SetPointTemperature);
}
```

Ta metoda aktualizacji temperatury punktu danych telemetrycznych i następnie raportuje zmiany ją do Centrum IoT przez ustawienie właściwości zgłoszone.

Wykonując wzorca w poprzednim przykładzie, można dodać własne programy obsługi dla własnych właściwości.

Należy także powiązać żądanej właściwości do programu obsługi, jak pokazano w poniższym przykładzie z **CoolerDevice** konstruktora:

```csharp
_desiredPropertyUpdateHandlers.Add(SetPointTempPropertyName, OnSetPointTempUpdate);
```

Należy pamiętać, że **SetPointTempPropertyName** jest stałą zdefiniowany jako "Config.SetPointTemp".

### <a name="add-support-for-a-new-method-to-the-simulator"></a>Dodaj obsługę nowej metody do symulatora

Symulator, aby dodać nową obsługę można dostosować [— metoda (metoda bezpośrednia)][lnk-direct-methods]. Istnieją dwa kluczowe kroki wymagane:

- Symulator należy powiadomić Centrum IoT w wstępnie skonfigurowane rozwiązanie szczegółowe informacje o metodzie.
- Symulator musi zawierać kod obsługi wywołania metody, uruchamiając go z **szczegóły urządzenia** panelu w Eksploratorze rozwiązań i za pomocą zadania.

Zdalne monitorowanie wstępnie skonfigurowane rozwiązanie używa *zgłosił właściwości* wysłać szczegóły obsługiwane metody do Centrum IoT. Zaplecze rozwiązania przechowuje listę wszystkich metod obsługiwanych przez poszczególne urządzenia oraz Historia wywołań metod. Możesz wyświetlić te informacje o urządzeniach i wywołania metod w portalu rozwiązania.

Aby powiadomić Centrum IoT, czy urządzenie obsługuje metodę, urządzenia, należy dodać szczegóły metody **SupportedMethods** węzła we właściwościach zgłoszone:

```json
"SupportedMethods": {
  "<method signature>": "<method description>",
  "<method signature>": "<method description>"
}
```

Podpis metody ma następujący format: `<method name>--<parameter #0 name>-<parameter #1 type>-...-<parameter #n name>-<parameter #n type>`. Na przykład, aby określić **InitiateFirmwareUpdate** metoda oczekuje parametru ciągu o nazwie **FwPackageURI**, użyj następujących podpis metody:

```json
InitiateFirmwareUpate--FwPackageURI-string: "description of method"
```

Dla listy typów obsługiwanych parametrów, zobacz **CommandTypes** klasy w projekcie infrastruktury.

Aby usunąć metody, należy ustawić podpis metody `null` we właściwościach zgłoszony.

> [!NOTE]
> Po odebraniu zaplecza rozwiązania tylko aktualizuje informacje o obsługiwanych metod *informacje o urządzeniu* wiadomości z urządzenia.

Poniższy przykładowy kod z **SampleDeviceFactory** klasy w projekcie wspólnej pokazano, jak dodać metodę do listy **SupportedMethods** we właściwościach zgłoszone wysyłane przez urządzenie:

```csharp
device.Commands.Add(new Command(
    "InitiateFirmwareUpdate",
    DeliveryType.Method,
    "Updates device Firmware. Use parameter 'FwPackageUri' to specifiy the URI of the firmware file, e.g. https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin",
    new[] { new Parameter("FwPackageUri", "string") }
));
```

Następujący fragment kodu dodaje szczegóły **InitiateFirmwareUpdate** metody, łącznie z tekstem, które mają być wyświetlane w portalu rozwiązania i szczegółowe informacje o parametrach wymaganej metody.

Symulator wysyła właściwości zgłoszone, łącznie z listą obsługiwanych metod, aby Centrum IoT podczas uruchamiania symulatora.

Dodaj program obsługi do kodu symulatora dla poszczególnych metod obsługiwanych. Widać istniejących programów obsługi w **CoolerDevice** klasy w projekcie Simulator.WebJob. W poniższym przykładzie pokazano programu obsługi **InitiateFirmwareUpdate** metody:

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

Nazwy metod obsługi musi rozpoczynać się od `On` następuje nazwa metody. **MethodRequest** parametr zawiera parametry przekazywane wywołania metody z zaplecza rozwiązania. Zwracana wartość musi być typu **zadań&lt;MethodResponse&gt;**. **BuildMethodResponse** narzędzie metody pomaga utworzyć wartość zwracaną.

Wewnątrz metody obsługi możesz:

- Uruchom zadanie asynchroniczne.
- Pobrać żądanej właściwości z *dwie urządzenia* w Centrum IoT.
- Aktualizuj przy użyciu jednej właściwości zgłoszone **SetReportedPropertyAsync** metody w **CoolerDevice** klasy.
- Zaktualizować wiele właściwości zgłoszonego przez utworzenie **TwinCollection** wystąpienia i wywoływania **Transport.UpdateReportedPropertiesAsync** metody.

W poprzednim przykładzie aktualizacji oprogramowania układowego wykonuje następujące czynności:

- Sprawdza, czy urządzenie jest w stanie zaakceptować żądania aktualizacji oprogramowania układowego.
- Asynchronicznie inicjuje operację aktualizacji oprogramowania układowego i przywraca dane telemetryczne po zakończeniu operacji.
- Natychmiast zwraca komunikat "FirmwareUpdate zaakceptowane", aby wskazać, że żądanie zostało zaakceptowane przez urządzenie.

### <a name="build-and-use-your-own-physical-device"></a>Tworzenie i używanie własnego urządzenia (fizycznych)

[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) Podaj bibliotek podłączania wiele typów urządzeń (języków i systemów operacyjnych) do rozwiązania IoT.

## <a name="modify-dashboard-limits"></a>Modyfikowanie limity pulpitu nawigacyjnego

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Liczba urządzeń wyświetlane na liście rozwijanej pulpitu nawigacyjnego

Wartość domyślna to 200. Można zmienić tego numeru w [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Numer PIN do wyświetlenia w formancie mapy Bing

Wartość domyślna to 200. Można zmienić tego numeru w [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Okres czasu wykresu telemetrii

Wartość domyślna to 10 minut. Można zmienić tę wartość w [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="feedback"></a>Opinia

Masz dostosowanie chcesz wyświetlić w tym dokumencie? Dodaj sugestii funkcji [User Voice](https://feedback.azure.com/forums/321918-azure-iot), lub komentarz w tym artykule. 

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat opcji dostosowywania wstępnie skonfigurowanych rozwiązań, zobacz:

* [Łączenie aplikacji logiki do rozwiązania Azure IoT pakiet monitorowania zdalnego wstępnie][lnk-logicapp]
* [Dynamiczne telemetrii za pomocą zdalnego wstępnie skonfigurowane rozwiązanie monitorowania][lnk-dynamic]
* [Urządzenie informacji metadanych w zdalnym wstępnie skonfigurowane rozwiązanie monitorowania][lnk-devinfo]
* [Dostosuj sposób rozwiązania połączonych fabryki wyświetlania danych z serwerów OPC UA][lnk-cf-customize]

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