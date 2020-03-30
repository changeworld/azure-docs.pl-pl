---
title: Tworzenie usługi Azure IoT Hub przy użyciu narzędzi Usługi Azure IoT dla vs code | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć centrum Usługi Azure IoT w grupie zasobów za pomocą narzędzi Azure IoT code dla programu Visual Studio.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 668087ae596688e86b7b84a16bc5c0fd0f9fcef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912246"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Tworzenie centrum IoT przy użyciu narzędzi Usługi Azure IoT Tools for Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

W tym artykule pokazano, jak utworzyć centrum Usługi Azure [IoT Tools for Visual Studio za pomocą narzędzi Azure IoT Tools for Visual Studio.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby ukończyć ten artykuł, potrzebujesz następujących elementów:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

- [Kod programu Visual Studio](https://code.visualstudio.com/)

- [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) dla programu Visual Studio Code.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

1. W programie Visual Studio Code otwórz widok **Eksploratora.**

2. U dołu Eksploratora rozwiń sekcję **Urządzenia usługi Azure IoT Hub.** 

   ![Rozwijanie urządzeń usługi Azure IoT Hub](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Kliknij **...** w nagłówku sekcji **Urządzenia usługi Azure IoT Hub.** Jeśli wielokropek nie jest widoczny, umieść wskaźnik myszy na nagłówku. 

4. Wybierz **pozycję Utwórz Centrum IoT**.

5. W prawym dolnym rogu pojawi się okno podręczne, które umożliwia zalogowanie się na platformie Azure po raz pierwszy.

6. Wybierz subskrypcję platformy Azure. 

7. Wybierz grupę zasobów.

8. Wybierz lokalizację.

9. Wybierz warstwę cenową.

10. Wprowadź unikatową globalnie nazwę centrum IoT Hub.

11. Poczekaj kilka minut, aż zostanie utworzony Centrum IoT Hub.

## <a name="next-steps"></a>Następne kroki

Teraz wdrożono centrum IoT przy użyciu narzędzia Azure IoT Tools for Visual Studio Code. Aby dowiedzieć się więcej, zapoznaj się z następującymi artykułami:

* [Użyj narzędzia Azure IoT Tools for Visual Studio Code do wysyłania i odbierania wiadomości między urządzeniem a Centrum IoT.](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)

* [Korzystanie z narzędzi Usługi Azure IoT Tools for Visual Studio Code for Azure IoT Hub device management](iot-hub-device-management-iot-toolkit.md)

* [Zobacz stronę wiki usługi Azure IoT Hub for VS Code](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
