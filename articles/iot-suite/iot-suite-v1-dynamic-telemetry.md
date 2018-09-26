---
title: Korzystanie z telemetrii dynamicznej | Dokumentacja firmy Microsoft
description: Postępuj zgodnie z tym samouczkiem, aby dowiedzieć się, jak korzystanie z telemetrii dynamicznej z pakietu Azure IoT Suite we wstępnie skonfigurowanym rozwiązaniu monitorowania zdalnego.
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
ms.openlocfilehash: 60e9ee00fabf15a62e782c70bca251b1a8e617c3
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096620"
---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Korzystanie z telemetrii dynamicznej z wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego

Telemetrii dynamicznej umożliwia wizualizację żadnych danych telemetrycznych wysyłanych do wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego. Symulowane urządzenia, które wdrażanie przy użyciu wstępnie skonfigurowanego rozwiązania wysyłania telemetrii temperatury i wilgotności, który możesz wizualizować na pulpicie nawigacyjnym. Dostosowywanie istniejących symulowane urządzenia, tworzenia nowych symulowanych urządzeń, czy Podłączanie urządzeń fizycznych do wstępnie skonfigurowanego rozwiązania można wysyłać inne wartości danych telemetrycznych, takich jak temperatury zewnętrznej, obr. / min lub prędkość wiatru. Następnie można wizualizować tych dodatkowych danych telemetrycznych na pulpicie nawigacyjnym.

Ten samouczek używa prostego symulowanego urządzenia środowiska Node.js, które można łatwo modyfikować, aby eksperymentować z telemetrii dynamicznej.

Do ukończenia tego samouczka będą potrzebne:

* Aktywna subskrypcja platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure][lnk_free_trial].
* [Node.js] [ lnk-node] wersji 0.12.x lub nowszej.

Możesz ukończyć ten samouczek, w dowolnym systemie operacyjnym, takich jak Windows lub Linux, w którym można zainstalować środowisko Node.js.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

## <a name="add-a-telemetry-type"></a>Dodaj typ telemetrii

Następnym krokiem jest zastąpienie danych telemetrycznych generowanych przez urządzenia symulowanego środowiska Node.js za pomocą nowego zestawu wartości:

1. Zatrzymywanie urządzenia symulowanego środowiska Node.js, wpisując **klawisze Ctrl + C** w wierszu polecenia lub powłokę.
2. W pliku remote_monitoring.js widać danych podstawowych wartości temperatury istniejących, wilgotności i temperatury zewnętrznej telemetrii. Dodaj wartość danych podstawowych dla **obr. / min** w następujący sposób:

    ```nodejs
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Node.js symulowane urządzenie używa **generateRandomIncrement** funkcji w pliku remote_monitoring.js do dodawania losowego przyrostu do wartości danych podstawowych. Losowe generowanie **obr. / min** wartość, dodając wiersz kodu po randomizations istniejących w następujący sposób:

    ```nodejs
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Dodaj nową wartość obr. / min do ładunek w formacie JSON, które urządzenie wysyła do usługi IoT Hub:

    ```nodejs
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Uruchamianie urządzenia symulowanego środowiska Node.js przy użyciu następującego polecenia:

    `node remote_monitoring.js`

6. Sprawdź nowy typ danych telemetrycznych obr. / min, którym są wyświetlane na wykresie na pulpicie nawigacyjnym:

![Dodaj obr. / min do pulpitu nawigacyjnego][image3]

> [!NOTE]
> Konieczne może być wyłączone, a następnie włączyć urządzenia środowiska Node.js na **urządzeń** strony w pulpicie nawigacyjnym, aby natychmiast zobaczyć zmiany.

## <a name="customize-the-dashboard-display"></a>Dostosuj wyświetlanie pulpitu nawigacyjnego

**Informacje o urządzeniu** wiadomości mogą obejmować metadane dotyczące telemetrii, urządzenie może wysyłać do Centrum IoT Hub. Te metadane, można określić typy telemetrii, które urządzenie wysyła. Modyfikowanie **deviceMetaData** wartości w pliku remote_monitoring.js, aby uwzględnić **Telemetrii** następujących definicji **polecenia** definicji. Poniższy kod przedstawia fragment **polecenia** definicji (Pamiętaj dodać `,` po **polecenia** definicji):

```nodejs
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [!NOTE]
> Rozwiązania do monitorowania zdalnego używa dopasowanie bez uwzględniania wielkości liter do porównania definicji metadanych z danymi w usłudze stream dane telemetryczne.


Dodawanie **Telemetrii** definicji, jak pokazano w poprzednim fragmencie kodu nie zmienia zachowanie pulpitu nawigacyjnego. Jednak może również obejmować metadane **DisplayName** atrybutu w celu dostosowania wyświetlania na pulpicie nawigacyjnym. Aktualizacja **Telemetrii** definicji metadanych, jak pokazano w poniższym fragmencie kodu:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

Poniższy zrzut ekranu pokazuje, jak ta zmiana modyfikuje legendy wykresu na pulpicie nawigacyjnym:

![Dostosowania legendy wykresu][image4]

> [!NOTE]
> Konieczne może być wyłączone, a następnie włączyć urządzenia środowiska Node.js na **urządzeń** strony w pulpicie nawigacyjnym, aby natychmiast zobaczyć zmiany.

## <a name="filter-the-telemetry-types"></a>Filtruj typy telemetrii

Domyślnie wykresów na pulpicie nawigacyjnym zawiera każdej serii danych w strumieniu danych telemetrycznych. Możesz użyć **informacje o urządzeniu** metadane, aby zapobiec wyświetlaniu typy telemetrii określonych na wykresie. 

Aby utworzyć wykres, pokazują tylko telemetrię temperatury i wilgotności, Pomiń **ExternalTemperature** z **informacje o urządzeniu** **Telemetrii** metadanych w następujący sposób:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

**Aktywność na świeżym powietrzu temperatury** przestanie być wyświetlana na wykresie:

![Filtrowanie danych telemetrycznych na pulpicie nawigacyjnym][image5]

Ta zmiana dotyczy tylko wyświetlania wykresu. **ExternalTemperature** wartości danych nadal są przechowywane i udostępniane dla jakiegokolwiek przetwarzania w wewnętrznej bazie danych.

> [!NOTE]
> Konieczne może być wyłączone, a następnie włączyć urządzenia środowiska Node.js na **urządzeń** strony w pulpicie nawigacyjnym, aby natychmiast zobaczyć zmiany.

## <a name="handle-errors"></a>Obsługa błędów

Dla strumienia danych do wyświetlenia na wykresie jego **typu** w **informacje o urządzeniu** metadanych musi odpowiadać typowi danych wartości danych telemetrycznych. Na przykład, jeśli metadane Określa, że **typu** wilgotność dane są **int** i **double** znajduje się w strumień danych telemetrycznych, a następnie dane telemetryczne dotyczące wilgotności nie są wyświetlane na wykresie. Jednak **wilgotności** wartości są nadal przechowywane i udostępniane dla jakiegokolwiek przetwarzania zaplecza.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak korzystanie z telemetrii dynamicznej, możesz dowiedzieć się więcej o jak wstępnie skonfigurowane rozwiązania używają informacji o urządzeniu: [urządzenia informacji metadanych do zdalnego monitorowania wstępnie skonfigurowanego rozwiązania] [ lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[image3]: media/iot-suite-v1-dynamic-telemetry/image3.png
[image4]: media/iot-suite-v1-dynamic-telemetry/image4.png
[image5]: media/iot-suite-v1-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
