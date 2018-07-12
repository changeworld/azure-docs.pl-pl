---
title: Usługa Azure zarządzanie urządzeniami IoT za pomocą rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure w wersji 2.0 | Dokumentacja firmy Microsoft
description: Używanie rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure w wersji 2.0 narzędzia do zarządzania urządzeniami Azure IoT Hub, metod bezpośrednich i opcje zarządzania żądane właściwości bliźniaczej reprezentacji.
author: chrissie926
manager: ''
keywords: Zarządzanie urządzeniami iot platformy Azure, zarządzania urządzeniami w usłudze azure iot hub, iot zarządzania urządzeniami, zarządzanie urządzeniami usługi iot hub
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: dc96e70a031d6080217e71b829ec5de3c64e4cf7
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971758"
---
# <a name="use-the-iot-extension-for-azure-cli-20-for-azure-iot-hub-device-management"></a>Używanie rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure w wersji 2.0 do zarządzania urządzeniami Azure IoT Hub

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://github.com/Azure/azure-iot-cli-extension) to nowego typu open source rozszerzenia IoT, który dodaje do funkcji [interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest). Interfejs wiersza polecenia platformy Azure w wersji 2.0 zawiera polecenia służące do interakcji z usługi Azure resource manager i zarządzanie punktami końcowymi. Na przykład można użyć interfejsu wiersza polecenia platformy Azure w wersji 2.0, aby utworzyć Maszynę wirtualną platformy Azure lub usługi IoT hub. Rozszerzenie interfejsu wiersza polecenia umożliwia to usługa platformy Azure rozszerzyć, zapewniając wiersza polecenia platformy Azure, dostęp do dodatkowych możliwości specyficznych dla usługi. Rozszerzenia IoT udostępnia deweloperom IoT wiersza polecenia do funkcji usługi IoT Hub, IoT Edge i IoT Hub Device Provisioning Service.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opcja zarządzania          | Zadanie                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Metody bezpośrednie             | Należy skonfigurować urządzenie działania, takie jak uruchamianie lub zatrzymywanie wysyłanie wiadomości lub ponowne uruchamianie urządzenia.                                        |
| Odpowiednich właściwości bliźniaka    | Urządzenia należy umieścić w określonych stanach, takie jak ustawienie DIODĘ zielony lub ustawienie interwału wysyłania danych telemetrycznych do 30 minut.         |
| Zgłoszonych właściwości bliźniaka   | Pobierz stan zgłoszonego urządzenia. Na przykład urządzenie zgłasza diody LED jest teraz migające.                                    |
| Tagów bliźniaczych reprezentacji                  | Store metadanych dla określonego urządzenia w chmurze. Na przykład lokalizacja wdrożenia Automat.                         |
| Zapytania dotyczące bliźniaczych reprezentacji urządzeń        | Wyślij zapytanie do wszystkich bliźniaków urządzeń można pobrać z dowolnego warunków, takich jak identyfikowanie urządzeń, które są dostępne do użycia. |

Aby uzyskać bardziej szczegółowe objaśnienia na temat różnic oraz wskazówki na temat korzystania z tych opcji, zobacz [wskazówki dotyczące komunikacji urządzenia do chmury](iot-hub-devguide-d2c-guidance.md) i [wskazówki dotyczące komunikacji chmury do urządzenia](iot-hub-devguide-c2d-guidance.md).

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia (metadane, konfiguracje i warunki). Usługa IoT Hub utrzymuje bliźniaczą reprezentację urządzenia dla każdego urządzenia, który nawiązuje z nim połączenie. Aby uzyskać więcej informacji dotyczących bliźniaczych reprezentacji urządzeń, zobacz [wprowadzenie do bliźniaków urządzeń](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, za pomocą rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure w wersji 2.0 z różnymi opcjami zarządzania na komputerze deweloperskim.

## <a name="what-you-do"></a>Co należy zrobić

Uruchom interfejs wiersza polecenia platformy Azure w wersji 2.0 i rozszerzenia usługi IoT dla interfejsu wiersza polecenia platformy Azure w wersji 2.0, z różnymi opcjami zarządzania.

## <a name="what-you-need"></a>Co jest potrzebne

- Samouczek [skonfigurować na twoim urządzeniu](iot-hub-raspberry-pi-kit-node-get-started.md) ukończone, która obejmuje następujące wymagania:
  - Aktywna subskrypcja platformy Azure.
  - Usługi Azure IoT hub w ramach Twojej subskrypcji.
  - Aplikacja kliencka, która wysyła komunikaty do usługi Azure IoT hub.

- Upewnij się, że urządzenie jest uruchomiony z aplikacji klienckiej w ramach tego samouczka.

- [Python 2.7x lub Python 3.x](https://www.python.org/downloads/)

- Zainstaluj interfejs wiersza polecenia platformy Azure 2.0. Prostym sposobem instalowania w systemie Windows jest pobranie i zainstalowanie instalatora [MSI](https://aka.ms/InstallAzureCliWindows). Instrukcje dotyczące instalacji można również wykonać w systemie [Microsoft Docs](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) konfigurowania interfejsu wiersza polecenia platformy Azure w wersji 2.0 w swoim środowisku. Potrzebujesz co najmniej interfejsu wiersza polecenia platformy Azure 2.0 w wersji 2.0.24 lub nowszej. Użyj polecenia `az –version` w celu przeprowadzenia weryfikacji. 

- Instalowanie rozszerzenia IoT. Najprostszym sposobem jest uruchomienie polecenia `az extension add --name azure-cli-iot-ext`. [Plik readme rozszerzenia IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) opisuje kilka sposobów instalowania rozszerzenia.


## <a name="log-in-to-your-azure-account"></a>Zaloguj się do konta platformy Azure

Zaloguj się do konta platformy Azure, uruchamiając następujące polecenie:

```bash
az login
```

## <a name="direct-methods"></a>Metody bezpośrednie

```bash
az iot hub invoke-device-method --device-id <your device id> --hub-name <your hub name> --method-name <the method name> --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Odpowiednich właściwości bliźniaka urządzenia

Ustaw interwał żądaną właściwość = 3000, uruchamiając następujące polecenie:

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.desired.interval = 3000
```

Tej właściwości można odczytać z urządzenia.

## <a name="device-twin-reported-properties"></a>Zgłoszonych właściwości bliźniaka urządzenia

Pobierz zgłaszane właściwości urządzenia, uruchamiając następujące polecenie:

```bash
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Jedną z bliźniaczej reprezentacji zgłaszane właściwości jest $metadata. $lastUpdated, która umożliwia pokazanie czasu ostatniej aplikacji urządzenia zaktualizować swój zestaw zgłaszanej właściwości.

## <a name="device-twin-tags"></a>Tagi bliźniaczej reprezentacji urządzenia

Wyświetlanie tagów i właściwości urządzenia, uruchamiając następujące polecenie:

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Dodaj rolę pole = temperatury i wilgotności z urządzeniem, uruchamiając następujące polecenie:

```bash
az iot hub device-twin update --hub-name <your hub name> --device-id <your device id> --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Zapytania dotyczące bliźniaczych reprezentacji urządzeń

Zapytania urządzeń przy użyciu tagu roli = "temperatury i wilgotności", uruchamiając następujące polecenie:

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Kwerendy wszystkie urządzenia, z wyjątkiem tych z tagiem roli = "temperatury i wilgotności", uruchamiając następujące polecenie:

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Kolejne kroki

Wyjaśniono sposób monitorowania komunikatów wysyłanych z urządzenia do chmury i wysyłanie komunikatów z chmury do urządzeń między urządzeniami IoT i usługi Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
