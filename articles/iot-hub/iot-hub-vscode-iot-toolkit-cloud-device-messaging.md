---
title: Korzystanie z narzędzi Usługi Azure IoT Tools for VSCode w celu menadżeri wiadomości w centrum IT
description: Dowiedz się, jak używać narzędzi Azure IoT Tools for Visual Studio Code do monitorowania komunikatów urządzenia w chmurze i wysyłania wiadomości z chmury do urządzenia w centrum Azure IoT Hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: 0b081229dcb382786fea03dff358b5cc47d77ee7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912014"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Używanie narzędzi Usługi Azure IoT Tools for Visual Studio Code do wysyłania i odbierania wiadomości między urządzeniem a centrum IoT Hub

![Diagram end-to-end](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[Narzędzia Azure IoT to](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) przydatne rozszerzenie kodu programu Visual Studio, które ułatwia zarządzanie centrum IoT i tworzenie aplikacji IoT. W tym artykule skupiono się na tym, jak używać narzędzi Azure IoT Tools for Visual Studio Code do wysyłania i odbierania wiadomości między urządzeniem a centrum IoT hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Czego się nauczysz

Dowiesz się, jak używać narzędzi Azure IoT Tools for Visual Studio Code do monitorowania komunikatów z urządzenia do chmury i wysyłania komunikatów z chmury do urządzenia. Komunikaty między urządzeniami do chmury mogą być danymi z czujników zbieranych przez urządzenie, a następnie wysyła do centrum IoT Hub. Komunikaty z chmury do urządzenia mogą być poleceniami wysyłanym przez centrum IoT hub do urządzenia w celu migania diody LED podłączonej do urządzenia.

## <a name="what-you-will-do"></a>Co zrobisz

* Użyj narzędzia Azure IoT Tools for Visual Studio Code do monitorowania komunikatów z urządzenia do chmury.

* Użyj narzędzia Azure IoT Tools for Visual Studio Code do wysyłania komunikatów z chmury do urządzenia.

## <a name="what-you-need"></a>Co jest potrzebne

* Aktywna subskrypcja platformy Azure.

* Centrum Usługi Azure IoT w ramach subskrypcji.

* [Kod programu Visual Studio](https://code.visualstudio.com/)

* [Narzędzia Usługi Azure IoT dla programu VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) lub otwórz to [łącze w programie Visual Studio Code](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum IoT

1. W **widoku Eksploratora** programu VS Code rozwiń **sekcję Urządzenia usługi Azure IoT Hub** w lewym dolnym rogu.

2. Kliknij **polecenie Wybierz Centrum IoT** w menu kontekstowym.

3. W prawym dolnym rogu pojawi się okno podręczne, które umożliwia zalogowanie się na platformie Azure po raz pierwszy.

4. Po zalogowaniu się zostanie wyświetlona lista subskrypcji platformy Azure, a następnie wybierz pozycję Subskrypcja platformy Azure i Centrum IoT.

5. Lista urządzeń zostanie wyświetlona na karcie **Urządzenia usługi Azure IoT Hub** w ciągu kilku sekund.

   > [!Note]
   > Można również ukończyć konfigurację, wybierając pozycję **Ustaw parametry połączenia centrum IoT Hub**. Wprowadź ciąg połączenia zasad **iothubowner** dla centrum IoT, z którym łączy się urządzenie IoT w wyskakującym oknie.

## <a name="monitor-device-to-cloud-messages"></a>Monitorowanie komunikatów między urządzeniami w chmurze

Aby monitorować wiadomości wysyłane z urządzenia do centrum IoT hub, wykonaj następujące czynności:

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz **pozycję Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

2. Monitorowane komunikaty będą wyświetlane w widoku **usługi OUTPUT** > **Azure IoT Hub.**

3. Aby zatrzymać monitorowanie, kliknij prawym przyciskiem myszy widok **WYJŚCIE** i wybierz pozycję **Zatrzymaj wbudowany punkt końcowy zdarzenia .**

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać wiadomość z centrum IoT hub do urządzenia, wykonaj następujące czynności:

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Wyślij wiadomość C2D do urządzenia**.

2. Wprowadź komunikat w polu wprowadzania.

3. Wyniki będą wyświetlane w widoku **usługi OUTPUT** > **Azure IoT Hub.**

## <a name="next-steps"></a>Następne kroki

Dowiesz się, jak monitorować wiadomości między urządzeniami do chmury i wysyłać komunikaty z chmury do urządzenia między urządzeniem IoT a usługą Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]