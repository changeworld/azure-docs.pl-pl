---
title: Zarządzanie urządzeniem chmury usługi Azure IoT Hub komunikatów za pomocą Eksploratora chmury dla programu Visual Studio | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować urządzenie pod kątem komunikatów przesyłanych z chmury i wysyłać chmury komunikaty z urządzenia w usłudze Azure IoT Hub za pomocą Eksploratora chmury dla programu Visual Studio.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2018
ms.author: xshi
ms.openlocfilehash: ab3c02d7207bca70a90df8aa08c73c1484cd635d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61440748"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Wysyłanie i odbieranie komunikatów między urządzeniem i Centrum IoT Hub za pomocą Eksploratora chmury dla programu Visual Studio

![Diagram end-to-end](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Eksplorator chmury](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) jest przydatne rozszerzenia Visual Studio, która pozwala na wyświetlanie zasobów platformy Azure, badania ich właściwości i akcje klucza dla deweloperów z poziomu programu Visual Studio. Ten artykuł koncentruje się na temat sposobu wysyłania i odbierania komunikatów między urządzeniem i Centrum IoT Hub za pomocą Eksploratora chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Zawiera informacje

Nauczysz się za pomocą Eksploratora chmury dla programu Visual Studio, do monitorowania komunikatów wysyłanych z urządzenia do chmury i wysyłanie komunikatów z chmury do urządzeń. Komunikaty z urządzenia do chmury może być dane czujników urządzenia umożliwia zbieranie informacji o, a następnie wysyła do Centrum IoT Hub. Może to być komunikatów z chmury do urządzeń polecenia, które usługi IoT Hub wysyła do Twojego urządzenia. Na przykład blink DIODĘ, który jest podłączony do Twojego urządzenia.

## <a name="what-you-will-do"></a>Będziesz robić

- Monitorowania komunikatów wysyłanych z urządzenia do chmury za pomocą Eksploratora chmury dla programu Visual Studio.
- Wysyłanie komunikatów z chmury do urządzenia za pomocą Eksploratora chmury dla programu Visual Studio.

## <a name="what-you-need"></a>Co jest potrzebne

- Aktywna subskrypcja platformy Azure.
- Usługa Azure IoT Hub w ramach Twojej subskrypcji.
- Microsoft Visual Studio 2017 Update 8 lub nowszy
- Składnik Eksploratora chmury z Instalatora programu Visual Studio (wybrane domyślnie obciążenie platformy Azure)

## <a name="update-cloud-explorer-to-latest-version"></a>Eksplorator chmury aktualizację do najnowszej wersji

Składnik programu Cloud Explorer z poziomu Instalatora programu Visual Studio obsługuje tylko monitorowanie komunikatów z urządzenia do chmury i z chmury do urządzeń. Aby wysyłać komunikaty do urządzeń lub w chmurze, Pobierz i zainstaluj najnowszą wersję [programu Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się do dostępu do usługi IoT Hub

1. W programie Visual Studio **programu Cloud Explorer** okna, kliknij ikonę Zarządzanie kontami. Możesz otworzyć okno Eksploratora chmury z **widoku** > **programu Cloud Explorer** menu.

    ![Kliknij pozycję Zarządzanie kontami](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)


2. Kliknij przycisk **Zarządzanie kontami** w programie Cloud Explorer.

3. Kliknij przycisk **Dodaj konto...**  w nowym oknie zalogować się do platformy Azure po raz pierwszy.

4. Po zalogowaniu zostaną wyświetlone listy subskrypcji platformy Azure. Wybieranie subskrypcji platformy Azure, aby wyświetlić, a następnie kliknij przycisk **Zastosuj**.

5. Rozwiń **subskrypcji** > **centra IoT Hub** > **Your IoT Hub**, na liście będą wyświetlane w węźle usługi IoT Hub.

    ![Lista urządzeń](media/iot-hub-visual-studio-cloud-device-messaging/device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Monitorowania komunikatów wysyłanych z urządzenia do chmury

Do monitorowania komunikatów wysyłanych z urządzenia do usługi IoT Hub, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy lub urządzenie usługi IoT Hub, a następnie wybierz pozycję **Rozpocznij monitorowania komunikatu D2C**.

    ![Rozpocznij monitorowania komunikatu D2C](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message.png)

2. Monitorowane komunikaty będą wyświetlane w **usługi IoT Hub** okienko danych wyjściowych.

    ![Wyników monitorowania komunikatu D2C](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result.png)

3. Aby zatrzymać monitorowanie, kliknij prawym przyciskiem myszy na urządzeniu lub usługi IoT Hub i wybierz **Zatrzymaj monitorowanie komunikatu D2C**.

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać wiadomość z usługi IoT Hub do urządzenia, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz pozycję **wysyłania komunikatu C2D**.

    ![Wyślij wiadomość C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message.png)

2. Wprowadź komunikat w polu wejściowym.

3. Wyniki będą wyświetlane w **usługi IoT Hub** okienko danych wyjściowych.

    ![Wyślij C2D wynik komunikatu](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result.png)

## <a name="next-steps"></a>Kolejne kroki

Wyjaśniono sposób monitorowania komunikatów wysyłanych z urządzenia do chmury i wysyłanie komunikatów z chmury do urządzeń między urządzeniami IoT i usługi Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]