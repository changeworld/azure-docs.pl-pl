---
title: Tworzenie usługi Azure IoT Hub przy użyciu usługi Azure IoT Toolkit dla programu VS Code | Dokumentacja firmy Microsoft
description: Jak utworzyć Centrum IoT hub za pomocą usługi Azure IoT Toolkit dla programu VS Code.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: 71b188443ee48d742b14753cd5526edac493d9e3
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369135"
---
# <a name="create-an-iot-hub-using-the-azure-iot-toolkit-for-visual-studio-code"></a>Tworzenie Centrum IoT przy użyciu usługi Azure IoT Toolkit dla programu Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Możesz użyć [Azure IoT Toolkit dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) do utworzenia usługi Azure IoT Hub. W tym artykule pokazano, jak utworzyć Centrum IoT hub za pomocą usługi Azure IoT Toolkit.

Aby ukończyć ten artykuł, potrzebne są następujące elementy:

* Aktywne konto platformy Azure.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Zestaw narzędzi usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

1. W programie Visual Studio Code Otwórz **Explorer** widoku.

2. W dolnej części Eksploratora rozwiń **Azure IoT Hub Devices** sekcji. 

   ![Rozwiń węzeł urządzenia w usłudze Azure IoT Hub](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Kliknij pozycję **...**  w **Azure IoT Hub Devices** nagłówek sekcji. Jeśli nie widzisz wielokropka, umieść kursor nad nagłówka. 

4. Wybierz **utworzyć Centrum IoT Hub**.

5. Okno podręczne będzie wyświetlana w prawym dolnym rogu w umożliwia logowanie do platformy Azure po raz pierwszy.

6. Wybierz subskrypcję platformy Azure. 

7. Wybierz grupę zasobów.

8. Wybierz lokalizację.

9. Wybierz warstwę cenową.

10. Wprowadź unikatową w skali globalnej nazwę Centrum IoT.

11. Poczekaj kilka minut, dopiero po utworzeniu Centrum IoT Hub.

## <a name="next-steps"></a>Kolejne kroki

Po wdrożeniu usługi IoT hub przy użyciu usługi Azure IoT Toolkit dla programu Visual Studio Code, warto dokładniej:

* [Rozszerzenie Azure IoT Toolkit dla programu Visual Studio Code służy do wysyłania i odbierania komunikatów między urządzeniem i Centrum IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).
* [Strona typu wiki](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki) dla usługi Azure IoT Toolkit.
