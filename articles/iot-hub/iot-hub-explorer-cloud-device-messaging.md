---
title: Zarządzanie urządzeniem chmury Centrum IoT Azure messaging z Centrum iothub explorer | Dokumentacja firmy Microsoft
description: Dowiedz się, jak narzędzie explorer Centrum iothub interfejsu wiersza polecenia do urządzenia monitor wiadomości (D2C) w chmurze i wysyłanie chmury do urządzenia (C2D) wiadomości w usłudze Azure IoT Hub.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: Centrum iothub Eksploratorze chmury urządzenia wiadomości chmury Centrum iot na urządzeniu, chmury do urządzenia do obsługi komunikatów
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: ed15f7749676d8a7f0c2ef8b23e238f7bc90e341
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Użyj Eksploratora Centrum iothub do wysyłania i odbierania komunikatów między urządzeniem a Centrum IoT

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Centrum iothub explorer](https://github.com/azure/iothub-explorer) ma kilka poleceń, który ułatwia zarządzanie Centrum IoT. Ten samouczek koncentruje się na temat korzystania z Centrum iothub explorer do wysyłania i odbierania komunikatów między urządzeniem i Centrum IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Co dowiesz się

Sposób używania Eksploratora Centrum iothub do monitorowania urządzenia do chmury wiadomości i wysyłanie wiadomości chmury do urządzenia. Komunikaty urządzenia do chmury można danych czujnika urządzenia zbiera dane, a następnie wysyła do Centrum IoT. Komunikaty chmury do urządzenia może być poleceń, które Centrum IoT wysyła do Twojego urządzenia miga LED, która jest połączona z urządzeniem.

## <a name="what-you-will-do"></a>Będzie wykonywać

- Użyj Centrum iothub explorer, aby monitorować wiadomości urządzenia do chmury.
- Użyj Eksploratora Centrum iothub wysyłać chmury do urządzenia.

## <a name="what-you-need"></a>Co jest potrzebne

- Samouczek [skonfigurować Twoje urządzenie](iot-hub-raspberry-pi-kit-node-get-started.md) ukończone, która obejmuje następujące wymagania:
  - Aktywna subskrypcja platformy Azure.
  - Centrum Azure IoT w ramach Twojej subskrypcji.
  - Aplikacja klienta, która wysyła komunikaty do Centrum Azure IoT.
- iothub-explorer. ([Zainstalować explorer Centrum iothub](https://github.com/azure/iothub-explorer))

## <a name="monitor-device-to-cloud-messages"></a>Monitorowanie wiadomości urządzenia do chmury

Aby monitorować wiadomości, które są wysyłane z urządzenia do Centrum IoT, wykonaj następujące kroki:

1. Otwórz okno konsoli.
1. Uruchom następujące polecenie:

   ```bash
   iothub-explorer monitor-events <device-id> --login "<IoTHubConnectionString>"
   ```

   > [!Note]
   > Pobierz `<device-id>` i `<IoTHubConnectionString>` z Centrum IoT. Upewnij się, że po zakończeniu samouczka poprzedniej. Możesz użyć `iothub-explorer monitor-events <device-id> --login "HostName=<my-hub>.azure-devices.net;SharedAccessKeyName=<my-policy>;SharedAccessKey=<my-policy-key>"` Jeśli masz `HostName`, `SharedAccessKeyName` i `SharedAccessKey`.

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać wiadomość z Centrum IoT na urządzenie, wykonaj następujące kroki:

1. Otwórz okno konsoli.
1. Uruchom sesję w Centrum IoT, uruchamiając następujące polecenie:

   ```bash
   iothub-explorer login `<IoTHubConnectionString>`
   ```

1. Wyślij wiadomość do urządzenia, uruchamiając następujące polecenie:

   ```bash
   iothub-explorer send <device-id> <message>
   ```

Polecenie miga LED, który jest połączony z urządzeniem i wysyła wiadomość do Twojego urządzenia.

> [!Note]
> Nie istnieje potrzeba urządzenia do odesłania do Centrum IoT po otrzymaniu komunikatu potwierdzenia oddzielne polecenia.

## <a name="next-steps"></a>Kolejne kroki

Kiedy znasz już jak monitorować urządzenia do chmury wiadomości i wysyłanie komunikatów chmury do urządzenia między urządzenia IoT i Centrum IoT Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
