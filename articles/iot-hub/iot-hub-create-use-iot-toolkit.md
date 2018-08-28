---
title: Tworzenie usługi Azure IoT Hub przy użyciu usługi Azure IoT Toolkit dla programu VS Code | Dokumentacja firmy Microsoft
description: Jak utworzyć Centrum IoT hub za pomocą usługi Azure IoT Toolkit dla programu VS Code.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: 5097d7e1f6a0b9e94919ccc8731702206c0a1568
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047237"
---
# <a name="create-an-iot-hub-using-the-azure-iot-toolkit-for-visual-studio-code"></a>Tworzenie Centrum IoT przy użyciu usługi Azure IoT Toolkit dla programu Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

W tym artykule dowiesz się, jak używać [Azure IoT Toolkit dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) do utworzenia usługi Azure IoT hub. 

Aby ukończyć ten artykuł, potrzebne są następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

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

Teraz możesz wdrożyć usługi IoT hub przy użyciu usługi Azure IoT Toolkit dla programu Visual Studio Code. Do dalszego zbadania, zapoznaj się z następującymi artykułami:

* [Wysyłanie i odbieranie komunikatów między urządzeniem i Centrum IoT Hub za pomocą rozszerzenia Azure IoT Toolkit dla programu Visual Studio Code](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Użyj rozszerzenia Azure IoT Toolkit dla programu Visual Studio Code do zarządzania urządzeniami Azure IoT Hub](iot-hub-device-management-iot-toolkit.md)

* [Strona typu wiki usługi Azure IoT Toolkit](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
