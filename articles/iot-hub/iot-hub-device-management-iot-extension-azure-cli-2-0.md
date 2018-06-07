---
title: Azure IoT zarządzania urządzeniami z rozszerzeniem IoT Azure CLI 2.0 | Dokumentacja firmy Microsoft
description: Użyj rozszerzenia IoT Azure CLI 2.0 narzędzia do zarządzania urządzeniami Centrum IoT Azure, bezpośrednie metod i opcje zarządzania żądaną właściwości dwie.
author: chrissie926
manager: ''
keywords: Zarządzanie urządzeniami iot platformy Azure, zarządzanie urządzeniami Centrum azure iot, urządzenia iot zarządzania, zarządzanie urządzeniami Centrum iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: dc96e70a031d6080217e71b829ec5de3c64e4cf7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632819"
---
# <a name="use-the-iot-extension-for-azure-cli-20-for-azure-iot-hub-device-management"></a>Użyj rozszerzenia IoT 2.0 interfejsu wiersza polecenia platformy Azure do zarządzania urządzeniami Centrum IoT Azure

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Rozszerzenie IoT Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) to nowe open source rozszerzenia IoT, który dodaje do możliwości [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest). Azure CLI 2.0 zawiera polecenia do interakcji z usługi Azure resource manager i zarządzanie punktami końcowymi. Na przykład służy 2.0 interfejsu wiersza polecenia platformy Azure do utworzenia maszyny Wirtualnej platformy Azure lub Centrum IoT. Rozszerzenia interfejsu wiersza polecenia umożliwia usługą platformy Azure rozszerzyć nadanie wiersza polecenia platformy Azure, dostęp do dodatkowych możliwości specyficznych dla usług. Rozszerzenie IoT udostępnia deweloperom IoT wiersza polecenia możliwości Centrum IoT, krawędzi IoT i inicjowania obsługi usługi IoT Hub urządzeń.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opcja zarządzania          | Zadanie                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Metody bezpośrednie             | Należy skonfigurować urządzenie działania, takie jak uruchamianie lub zatrzymywanie wysyłania wiadomości i ponownego uruchamiania urządzenia.                                        |
| Żądany dwie właściwości    | Umieść urządzenia do określonych stanach, takie jak ustawienie LED zielony lub ustawienie interwału wysyłania danych telemetrycznych do 30 minut.         |
| Dwie zgłoszone właściwości   | Pobierz stan zgłoszonego urządzenia. Na przykład urządzenie zgłasza, że LED jest teraz migający.                                    |
| Dwie tagów                  | Przechowywane metadane dotyczące urządzeń w chmurze. Na przykład lokalizacja wdrożenia automaty maszyny.                         |
| Urządzenie dwie zapytań        | Wyślij zapytanie do wszystkich urządzeń twins można pobrać z dowolnego warunki, np. zidentyfikowanie urządzenia, które są dostępne do użycia. |

Aby uzyskać bardziej szczegółowe wyjaśnienia dotyczące różnic oraz wskazówki dotyczące używania tych opcji, zobacz [wskazówki komunikację urządzenia do chmury](iot-hub-devguide-d2c-guidance.md) i [wskazówki dotyczące komunikacji chmury do urządzenia](iot-hub-devguide-c2d-guidance.md).

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia (metadane, konfiguracje i warunki). Centrum IoT utrzymuje dwie urządzenia, dla każdego urządzenia, które nawiązuje z nim połączenie. Aby uzyskać więcej informacji na temat twins urządzenia, zobacz [wprowadzenie twins urządzenia](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiedz się, przy użyciu rozszerzenia IoT Azure CLI 2.0 z różnymi opcjami zarządzania na komputerze deweloperskim.

## <a name="what-you-do"></a>Co zrobić

Uruchom Azure CLI w wersji 2.0 i rozszerzenia IoT Azure CLI 2.0 z różnymi opcjami zarządzania.

## <a name="what-you-need"></a>Co jest potrzebne

- Samouczek [skonfigurować Twoje urządzenie](iot-hub-raspberry-pi-kit-node-get-started.md) ukończone, która obejmuje następujące wymagania:
  - Aktywna subskrypcja platformy Azure.
  - Centrum Azure IoT w ramach Twojej subskrypcji.
  - Aplikacja klienta, która wysyła komunikaty do Centrum Azure IoT.

- Upewnij się, że urządzenie korzysta z aplikacji klienckiej, w tym samouczku.

- [Python 2.7x lub Python 3.x](https://www.python.org/downloads/)

- Zainstaluj interfejs wiersza polecenia platformy Azure 2.0. Prostym sposobem instalowania w systemie Windows jest pobranie i zainstalowanie instalatora [MSI](https://aka.ms/InstallAzureCliWindows). Można również postępuj zgodnie z instrukcjami instalacji [Microsoft Docs](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) można skonfigurować 2.0 interfejsu wiersza polecenia platformy Azure w danym środowisku. Potrzebujesz co najmniej interfejsu wiersza polecenia platformy Azure 2.0 w wersji 2.0.24 lub nowszej. Użyj polecenia `az –version` w celu przeprowadzenia weryfikacji. 

- Zainstaluj rozszerzenie IoT. Najprostszym sposobem jest uruchomienie polecenia `az extension add --name azure-cli-iot-ext`. [Plik readme rozszerzenia IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) opisuje kilka sposobów instalowania rozszerzenia.


## <a name="log-in-to-your-azure-account"></a>Zaloguj się do konta platformy Azure

Zaloguj się do konta platformy Azure, uruchamiając następujące polecenie:

```bash
az login
```

## <a name="direct-methods"></a>Metody bezpośrednie

```bash
az iot hub invoke-device-method --device-id <your device id> --hub-name <your hub name> --method-name <the method name> --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Właściwości dwie żądanego urządzenia

Ustaw interwał żądanej właściwości = 3000, uruchamiając następujące polecenie:

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.desired.interval = 3000
```

Tej właściwości można odczytać z urządzenia.

## <a name="device-twin-reported-properties"></a>Dwie urządzenia zgłoszone właściwości

Pobierz właściwości zgłoszonego urządzenia, uruchamiając następujące polecenie:

```bash
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Jeden dwie podać właściwości $metadata. $lastUpdated przedstawiający ostatniego aplikacji urządzenia zaktualizowano jego zestaw właściwości zgłoszony.

## <a name="device-twin-tags"></a>Tagi dwie urządzenia

Wyświetlanie znaczników i właściwości urządzenia, uruchamiając następujące polecenie:

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Dodaj rolę pola = temperatury i wilgotności na urządzeniu, uruchamiając następujące polecenie:

```bash
az iot hub device-twin update --hub-name <your hub name> --device-id <your device id> --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Urządzenie dwie zapytań

Zapytanie urządzeń przy użyciu tagu roli = "temperatury i wilgotności", uruchamiając następujące polecenie:

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Wszystkie urządzenia z wyjątkiem tych z tagiem roli zapytania = "temperatury i wilgotności", uruchamiając następujące polecenie:

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Kolejne kroki

Kiedy znasz już jak monitorować urządzenia do chmury wiadomości i wysyłanie komunikatów chmury do urządzenia między urządzenia IoT i Centrum IoT Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
