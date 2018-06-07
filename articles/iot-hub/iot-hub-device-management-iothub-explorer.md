---
title: Azure IoT zarządzanie urządzeniami z Centrum iothub explorer | Dokumentacja firmy Microsoft
description: Narzędzie do zarządzania urządzeniami Centrum IoT Azure CLI Centrum iothub explorer, bezpośrednie metod i dwie potrzeby opcji zarządzania właściwości.
author: rangv
manager: nasing
keywords: Zarządzanie urządzeniami iot platformy Azure, zarządzanie urządzeniami Centrum azure iot, urządzenia iot zarządzania, zarządzanie urządzeniami Centrum iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 0603976312ecab4f6974f51f0e181d2438681255
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634203"
---
# <a name="use-iothub-explorer-for-azure-iot-hub-device-management"></a>Użyj Eksploratora Centrum iothub do zarządzania urządzeniami Centrum IoT Azure

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Centrum iothub explorer](https://github.com/azure/iothub-explorer) to narzędzie interfejsu wiersza polecenia, które jest uruchamiane na hoście komputera do zarządzania tożsamościami urządzenia w rejestrze Centrum IoT. Pochodzi on z opcjami zarządzania, które służy do wykonywania różnych zadań.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opcja zarządzania          | Zadanie                                                                                                                            |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------|
| Metody bezpośrednie             | Należy skonfigurować urządzenie działania, takie jak uruchamianie lub zatrzymywanie wysyłania wiadomości i ponownego uruchamiania urządzenia.                                        |
| Żądany dwie właściwości    | Umieść urządzenia do określonych stanach, takie jak ustawienie LED zielony lub ustawienie interwału wysyłania danych telemetrycznych do 30 minut.         |
| Dwie zgłoszone właściwości   | Pobierz stan zgłoszonego urządzenia. Na przykład urządzenie zgłasza, że LED jest teraz migający.                                    |
| Dwie tagów                  | Przechowywane metadane dotyczące urządzeń w chmurze. Na przykład lokalizacja wdrożenia automaty maszyny.                         |
| Komunikaty chmury do urządzenia   | Wyślij powiadomienia do urządzenia. Na przykład "jest bardzo prawdopodobne fartuchów dzisiaj. Nie zapomnij Przełącz parasola."              |
| Urządzenie dwie zapytań        | Wyślij zapytanie do wszystkich urządzeń twins można pobrać z dowolnego warunki, np. zidentyfikowanie urządzenia, które są dostępne do użycia. |

Aby uzyskać bardziej szczegółowe wyjaśnienia dotyczące różnic oraz wskazówki dotyczące używania tych opcji, zobacz [wskazówki komunikację urządzenia do chmury](iot-hub-devguide-d2c-guidance.md) i [wskazówki dotyczące komunikacji chmury do urządzenia](iot-hub-devguide-c2d-guidance.md).

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia (metadane, konfiguracje i warunki). Centrum IoT utrzymuje dwie urządzenia, dla każdego urządzenia, które nawiązuje z nim połączenie. Aby uzyskać więcej informacji na temat twins urządzenia, zobacz [wprowadzenie twins urządzenia](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiedz się, za pomocą Eksploratora Centrum iothub z różnymi opcjami zarządzania na komputerze deweloperskim.

## <a name="what-you-do"></a>Co zrobić

Uruchom Eksploratora Centrum iothub z różnymi opcjami zarządzania.

## <a name="what-you-need"></a>Co jest potrzebne

- Samouczek [skonfigurować Twoje urządzenie](iot-hub-raspberry-pi-kit-node-get-started.md) ukończone, która obejmuje następujące wymagania:
- Aktywna subskrypcja platformy Azure.
- Centrum Azure IoT w ramach Twojej subskrypcji.
- Aplikacja klienta, która wysyła komunikaty do Centrum Azure IoT.
- Upewnij się, że urządzenie korzysta z aplikacji klienckiej, w tym samouczku.
- Centrum iothub Eksploratorze [zainstalować explorer Centrum iothub](https://github.com/azure/iothub-explorer) na komputerze deweloperskim.

## <a name="connect-to-your-iot-hub"></a>Nawiązać połączenia z Centrum IoT

Podłącz do Centrum IoT, uruchamiając następujące polecenie:

```bash
iothub-explorer login <your IoT hub connection string>
```

## <a name="use-iothub-explorer-with-direct-methods"></a>Użyj Eksploratora Centrum iothub za pomocą metod bezpośredniego

Wywołanie `start` metody w aplikacji urządzenia do wysyłania komunikatów do Centrum IoT, uruchamiając następujące polecenie:

```bash
iothub-explorer device-method <your device Id> start
```

Wywołanie `stop` metody w aplikacji urządzenia, aby zatrzymać wysyłanie komunikatów do Centrum IoT, uruchamiając następujące polecenie:

```bash
iothub-explorer device-method <your device Id> stop
```

## <a name="use-iothub-explorer-with-twins-desired-properties"></a>Użyj Centrum iothub explorer z właściwościami żądany przez dwie

Ustaw interwał żądanej właściwości = 3000, uruchamiając następujące polecenie:

```bash
iothub-explorer update-twin <your device id> {\"properties\":{\"desired\":{\"interval\":3000}}}
```

Ta właściwość może zostać odczytany przez urządzenie.

## <a name="use-iothub-explorer-with-twins-reported-properties"></a>Użyj Centrum iothub explorer z właściwościami zgłoszonego przez dwie

Pobierz właściwości zgłoszonego urządzenia, uruchamiając następujące polecenie:

```bash
iothub-explorer get-twin <your device id>
```

Jedna z właściwości jest $metadata. $lastUpdated przedstawiającego urządzenie tym czas ostatniego wysłania lub odebrania wiadomości.

## <a name="use-iothub-explorer-with-twins-tags"></a>Użyj Eksploratora Centrum iothub tagów w dwie

Wyświetlanie znaczników i właściwości urządzenia, uruchamiając następujące polecenie:

```bash
iothub-explorer get-twin <your device id>
```

Dodaj rolę pola = temperatury i wilgotności na urządzeniu, uruchamiając następujące polecenie:

```bash
iothub-explorer update-twin <your device id> "{\"tags\":{\"role\":\"temperature&humidity\"}}"
```

## <a name="use-iothub-explorer-with-cloud-to-device-messages"></a>Użyj Centrum iothub explorer z komunikatami z chmury do urządzenia

Wyślij komunikat "Hello World" na urządzeniu, uruchamiając następujące polecenie:

```bash
iothub-explorer send <device-id> "Hello World"
```

Zobacz [Centrum iothub explorer umożliwia wysyłanie i odbieranie komunikatów między urządzeniem a Centrum IoT](iot-hub-explorer-cloud-device-messaging.md) dla scenariusza rzeczywistego użycia tego polecenia.

## <a name="use-iothub-explorer-with-device-twins-queries"></a>Użyj Centrum iothub explorer z zapytaniami twins urządzenia

Zapytanie urządzeń przy użyciu tagu roli = "temperatury i wilgotności", uruchamiając następujące polecenie:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Wszystkie urządzenia z wyjątkiem tych z tagiem roli zapytania = "temperatury i wilgotności", uruchamiając następujące polecenie:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Kolejne kroki

Znasz sposobu korzystania z Centrum iothub explorer z różnymi opcjami zarządzania.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
