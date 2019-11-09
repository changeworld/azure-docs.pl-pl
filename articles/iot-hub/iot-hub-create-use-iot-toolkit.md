---
title: Utwórz IoT Hub platformy Azure przy użyciu narzędzi Azure IoT Tools for VS Code | Microsoft Docs
description: Dowiedz się, jak za pomocą narzędzi Azure IoT Tools for Visual Studio Code utworzyć centrum Azure IoT Hub w grupie zasobów.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 3fe4dfae2e86d549056805e87c0fd3927697fe24
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890564"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Tworzenie Centrum IoT Hub przy użyciu narzędzi Azure IoT Tools for Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

W tym artykule pokazano, jak za pomocą [narzędzi usługi Azure iot Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) utworzyć centrum Azure IoT Hub. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby wykonać ten artykuł, potrzebne są następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [Visual Studio Code](https://code.visualstudio.com/)

- [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) dla programu Visual Studio Code.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

1. W Visual Studio Code Otwórz widok **Eksploratora** .

2. W dolnej części Eksploratora rozwiń sekcję **usługi Azure IoT Hub Devices** . 

   ![Rozwiń węzeł Azure IoT Hub Devices](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Kliknij pozycję **...** w nagłówku sekcji **urządzenia IoT Hub Azure** . Jeśli nie widzisz wielokropka, umieść kursor nad nagłówkiem. 

4. Wybierz pozycję **utwórz IoT Hub**.

5. W prawym dolnym rogu zostanie wyświetlone okno podręczne, które umożliwi zalogowanie się do platformy Azure po raz pierwszy.

6. Wybierz pozycję subskrypcja platformy Azure. 

7. Wybierz pozycję Grupa zasobów.

8. Wybierz lokalizację.

9. Wybierz pozycję warstwa cenowa.

10. Wprowadź globalnie unikatową nazwę dla IoT Hub.

11. Poczekaj kilka minut, aż IoT Hub zostanie utworzona.

## <a name="next-steps"></a>Następne kroki

Teraz wdrożono Centrum IoT przy użyciu narzędzi Azure IoT Tools for Visual Studio Code. Aby dowiedzieć się więcej, zapoznaj się z następującymi artykułami:

* [Użyj narzędzi Azure IoT Tools, aby Visual Studio Code wysyłać i odbierać komunikaty między urządzeniem i IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Korzystanie z narzędzi Azure IoT Tools dla Visual Studio Code na potrzeby zarządzania urządzeniami IoT Hub platformy Azure](iot-hub-device-management-iot-toolkit.md)

* [Zobacz stronę typu wiki zestawu narzędzi platformy Azure IoT Hub](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
