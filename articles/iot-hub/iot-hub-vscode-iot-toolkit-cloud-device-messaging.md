---
title: Zarządzanie urządzeniem chmury usługi Azure IoT Hub, obsługa komunikatów za pomocą narzędzia IoT platformy Azure dla programu Visual Studio Code | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować urządzenie pod kątem komunikatów przesyłanych z chmury i wysyłać chmury komunikaty z urządzenia w usłudze Azure IoT Hub za pomocą narzędzia IoT platformy Azure dla programu Visual Studio Code.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: 5b74524a05317cf22160561a4a001e88f9215953
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440089"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Narzędzia Azure IoT dla programu Visual Studio Code do wysyłania i odbierania komunikatów między urządzeniem i Centrum IoT Hub

![Diagram end-to-end](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) jest przydatne rozszerzenia programu Visual Studio Code, które ułatwia usługi IoT Hub zarządzania i programowania aplikacji IoT. Ten artykuł koncentruje się na temat korzystania z usługi Azure IoT Tools for Visual Studio Code do wysyłania i odbierania komunikatów między urządzeniem i Centrum IoT hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Zawiera informacje

Dowiesz się, jak używać usługi Azure IoT Tools for Visual Studio Code do monitorowania komunikatów wysyłanych z urządzenia do chmury i wysyłanie komunikatów z chmury do urządzeń. Komunikaty z urządzenia do chmury może być dane czujników urządzenia umożliwia zbieranie informacji o, a następnie wysyła do Centrum IoT hub. Może to być komunikatów z chmury do urządzeń polecenia, które usługi IoT hub wysyła do Twojego urządzenia miga DIODĘ, który jest podłączony do Twojego urządzenia.

## <a name="what-you-will-do"></a>Będziesz robić

* Narzędzia usługi Azure IoT dla programu Visual Studio Code do monitorowania komunikatów wysyłanych z urządzenia do chmury.

* Narzędzia usługi Azure IoT dla programu Visual Studio Code do wysyłania komunikatów z chmury do urządzeń.

## <a name="what-you-need"></a>Co jest potrzebne

* Aktywna subskrypcja platformy Azure.

* Usługi Azure IoT hub w ramach Twojej subskrypcji.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Narzędzia IoT platformy Azure dla programu VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) lub [otwierania tego linku w programie Visual Studio Code](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się do dostępu do usługi IoT hub

1. W **Explorer** widoku programu VS Code, rozwiń **Azure IoT Hub Devices** sekcji w lewym dolnym rogu.

2. Kliknij przycisk **wybierz Centrum IoT Hub** w menu kontekstowym.

3. Okno podręczne będzie wyświetlana w prawym dolnym rogu w umożliwia logowanie do platformy Azure po raz pierwszy.

4. Po zalogowaniu się w Twoja lista subskrypcji platformy Azure zostaną wyświetlone, a następnie wybierz subskrypcję platformy Azure i usługa IoT Hub.

5. Na liście będą wyświetlane w **Azure IoT Hub Devices** kartę w ciągu kilku sekund.

   > [!Note]
   > Można również ukończyć konfigurację, wybierając pozycję **Ustaw parametry połączenia centrum IoT Hub**. Wprowadź parametry połączenia dla Centrum IoT, które urządzenia IoT, łączy w oknie podręcznym.

## <a name="monitor-device-to-cloud-messages"></a>Monitorowania komunikatów wysyłanych z urządzenia do chmury

Do monitorowania komunikatów wysyłanych z urządzenia do usługi IoT hub, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz pozycję **Rozpocznij monitorowania komunikatu D2C**.

2. Monitorowane komunikaty będą wyświetlane w **dane wyjściowe** > **Azure IoT Hub Toolkit** widoku.

3. Aby zatrzymać monitorowanie, kliknij prawym przyciskiem myszy **dane wyjściowe** wyświetlać i wybierać **Zatrzymaj monitorowanie komunikatu D2C**.

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać wiadomość z usługi IoT hub do urządzenia, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz pozycję **wysyłania komunikatu C2D urządzeniu**.

2. Wprowadź komunikat w polu wejściowym.

3. Wyniki będą wyświetlane w **dane wyjściowe** > **Azure IoT Hub Toolkit** widoku.

## <a name="next-steps"></a>Kolejne kroki

Wyjaśniono sposób monitorowania komunikatów wysyłanych z urządzenia do chmury i wysyłanie komunikatów z chmury do urządzeń między urządzeniami IoT i usługi Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]