---
title: Zarządzanie urządzeniami Usługi Azure IoT z rozszerzeniem IoT dla interfejsu wiersza polecenia platformy Azure | Dokumenty firmy Microsoft
description: Użyj rozszerzenia IoT dla narzędzia interfejsu wiersza polecenia platformy Azure do zarządzania urządzeniami usługi Azure IoT Hub, korzystając z metod bezpośrednich i opcji zarządzania żądanymi właściwościami bliźniaczej reprezentacji.
author: chrissie926
manager: ''
keywords: azure iot device management, azure iot hub device management, device management iot, iot hub device management azure iot device management azure iot device management, azure iot hub device management
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 88c3d1f4213b161d5e322349a7f0e1bc1dd952e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239657"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Użyj rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure dla zarządzania urządzeniami usługi Azure IoT Hub

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) jest rozszerzeniem IoT typu open source, które dodaje do możliwości [interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) Interfejs wiersza polecenia platformy Azure zawiera polecenia do interakcji z usługą Azure Resource Manager i punktów końcowych zarządzania. Na przykład można użyć interfejsu wiersza polecenia platformy Azure, aby utworzyć maszynę wirtualną platformy Azure lub centrum IoT Hub. Rozszerzenie interfejsu wiersza polecenia umożliwia usłudze platformy Azure rozszerzenie interfejsu wiersza polecenia platformy Azure, zapewniając dostęp do dodatkowych funkcji specyficznych dla usługi. Rozszerzenie IoT daje deweloperom IoT dostęp do wiersza polecenia do wszystkich usług Usługi inicjowania obsługi administracyjnej usługi IoT Hub, IoT Edge i IoT Hub.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opcja zarządzania          | Zadanie  |
|----------------------------|-----------|
| Metody bezpośrednie             | Ustaw działanie urządzenia, takie jak uruchamianie lub zatrzymywanie wysyłania wiadomości lub ponowne uruchamianie urządzenia.                                        |
| Dwie pożądanych właściwości    | Umieść urządzenie w określonych stanach, takich jak ustawienie diody LED na zielono lub ustawienie interwału wysyłania danych telemetrycznych na 30 minut.         |
| Bliźniacze zgłaszane właściwości   | Pobierz zgłoszony stan urządzenia. Na przykład urządzenie zgłasza, że dioda LED miga teraz.                                    |
| Podwójne tagi                  | Przechowuj metadane specyficzne dla urządzenia w chmurze. Na przykład lokalizacja wdrożenia automatu.                         |
| Zapytania bliźniaczej reprezentacji urządzenia        | Kwerenda wszystkich bliźniąt bliźniąt urządzeń, aby pobrać te bliźniaczki z dowolnych warunków, takich jak identyfikowanie urządzeń, które są dostępne do użycia. |

Aby uzyskać bardziej szczegółowe wyjaśnienie różnic i wskazówek dotyczących korzystania z tych opcji, zobacz [Wskazówki dotyczące komunikacji między urządzeniami](iot-hub-devguide-d2c-guidance.md) a chmurą oraz [wskazówki dotyczące komunikacji z chmury do urządzenia](iot-hub-devguide-c2d-guidance.md).

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia (metadane, konfiguracje i warunki). Centrum IoT hub utrzymuje bliźniaczej reprezentacji urządzenia dla każdego urządzenia, które łączy się z nim. Aby uzyskać więcej informacji na temat bliźniąt urządzeń, zobacz [Wprowadzenie do bliźniąt urządzeń](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Omawiane zagadnienia

Nauczysz się używać rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure z różnymi opcjami zarządzania na komputerze deweloperskim.

## <a name="what-you-do"></a>Co robisz

Uruchom interfejs wiersza polecenia platformy Azure i rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure z różnymi opcjami zarządzania.

## <a name="what-you-need"></a>Co jest potrzebne

* Ukończ samouczek [symulatora online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) lub jeden z samouczków urządzenia; na przykład [Raspberry Pi z node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Pozycje te obejmują następujące wymagania:

  - Aktywna subskrypcja platformy Azure.
  - Centrum Usługi Azure IoT w ramach subskrypcji.
  - Aplikacja kliencka, która wysyła wiadomości do centrum Usługi Azure IoT Hub.

* Upewnij się, że urządzenie jest uruchomione z aplikacją kliencką podczas tego samouczka.

* [Python 2.7x lub Python 3.x](https://www.python.org/downloads/)

* Interfejsu wiersza polecenia platformy Azure. Jeśli chcesz go zainstalować, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Co najmniej wersja interfejsu wiersza polecenia platformy Azure musi mieć wartość 2.0.70 lub nowszy. Użyj polecenia `az –version` w celu przeprowadzenia weryfikacji.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* Zainstaluj rozszerzenie IoT. Najprostszym sposobem jest uruchomienie polecenia `az extension add --name azure-iot`. [Plik readme rozszerzenia IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) opisuje kilka sposobów instalowania rozszerzenia.

## <a name="sign-in-to-your-azure-account"></a>Zaloguj się do swojego konta platformy Azure

Zaloguj się do konta platformy Azure, uruchamiając następujące polecenie:

```azurecli
az login
```

## <a name="direct-methods"></a>Metody bezpośrednie

```azurecli
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Dwie bliźniacze właściwości urządzenia

Ustaw żądany interwał właściwości = 3000, uruchamiając następujące polecenie:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Tę właściwość można odczytać z urządzenia.

## <a name="device-twin-reported-properties"></a>Właściwości zgłaszane bliźniaczej reprezentacji urządzenia

Pobierz zgłoszone właściwości urządzenia, uruchamiając następujące polecenie:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Jedną z bliźniaczych właściwości zgłoszonych jest $metadata.$lastUpdated, który pokazuje po raz ostatni aplikacja urządzenia zaktualizowała swój zgłoszony zestaw właściwości.

## <a name="device-twin-tags"></a>Podwójne tagi urządzenia

Wyświetl znaczniki i właściwości urządzenia, uruchamiając następujące polecenie:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Dodaj rolę pola = temperatura&wilgotności do urządzenia, uruchamiając następujące polecenie:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Zapytania bliźniaczej reprezentacji urządzenia

Wysyłaj zapytania do urządzeń z tagiem roli = "temperatura&wilgotność" przez uruchomienie następującego polecenia:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Zapytanie wszystkich urządzeń z wyjątkiem tych z tagiem roli = "temperatura&wilgotności" przez uruchomienie następującego polecenia:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Następne kroki

Dowiesz się, jak monitorować wiadomości między urządzeniami do chmury i wysyłać komunikaty z chmury do urządzenia między urządzeniem IoT a usługą Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
