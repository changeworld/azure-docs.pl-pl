---
title: Tworzenie usługi Azure IoT Hub za pomocą narzędzia IoT platformy Azure dla programu VS Code | Dokumentacja firmy Microsoft
description: Jak utworzyć Centrum IoT hub za pomocą narzędzia IoT Microsoft Azure dla programu VS Code.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: c37eeec6429e8367ade12b58bb4e20022423edf6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66166251"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Tworzenie Centrum IoT przy użyciu narzędzi usługi Azure IoT dla programu Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

W tym artykule dowiesz się, jak używać [narzędzia usługi Azure IoT dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) do utworzenia usługi Azure IoT hub. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby ukończyć ten artykuł, potrzebne są następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [Visual Studio Code](https://code.visualstudio.com/)

- [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) dla programu Visual Studio Code.

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

Teraz możesz wdrożyć usługi IoT hub przy użyciu narzędzi usługi Azure IoT dla programu Visual Studio Code. Do dalszego zbadania, zapoznaj się z następującymi artykułami:

* [Narzędzia usługi Azure IoT dla programu Visual Studio Code do wysyłania i odbierania komunikatów między urządzeniem i Centrum IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Narzędzia usługi Azure IoT dla programu Visual Studio Code do zarządzania urządzeniami Azure IoT Hub](iot-hub-device-management-iot-toolkit.md)

* [Strona typu wiki usługi Azure IoT Hub Toolkit](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
