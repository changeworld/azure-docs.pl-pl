---
title: Rejestrowanie nowego urządzenia usługi Azure IoT Edge (VS Code) | Dokumentacja firmy Microsoft
description: Użyj programu Visual Studio Code, aby utworzyć nowe urządzenie usługi IoT Edge w usłudze Azure IoT hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/14/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cf9603c65454f076a494789e784c9352fb7bef33
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578709"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Rejestrowanie nowego urządzenia usługi Azure IoT Edge z programu Visual Studio Code

Zanim użyjesz urządzeń IoT za pomocą usługi Azure IoT Edge, musisz zarejestrować ich przy użyciu usługi IoT hub. Po zarejestrowaniu urządzenia, pojawi się parametry połączenia, który może służyć do konfigurowania urządzenia pod kątem obciążeń krawędzi. 

W tym artykule pokazano, jak zarejestrować nowe urządzenie usługi IoT Edge przy użyciu programu Visual Studio Code (VS Code). Istnieje wiele sposobów, aby wykonać większość operacji w programie VS Code. W tym artykule używa Eksploratora, ale również umożliwia paletę poleceń uruchom większość czynności. 

## <a name="prerequisites"></a>Wymagania wstępne

* [Usługi IoT hub](../iot-hub/iot-hub-create-through-portal.md) w subskrypcji platformy Azure
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie usługi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) dla programu Visual Studio Code

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się do dostępu do usługi IoT hub

Rozszerzenia usługi Azure IoT dla programu Visual Studio Code służy do wykonywania operacji w Centrum IoT. Dla tych operacji do pracy musisz zalogować się do konta platformy Azure i wybierz Centrum IoT hub, który użytkownik pracuje.

1. W programie Visual Studio Code Otwórz **Explorer** widoku.

2. W dolnej części Eksploratora rozwiń **Azure IoT Hub Devices** sekcji. 

   ![Rozwiń węzeł urządzenia w usłudze Azure IoT Hub](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

3. Kliknij pozycję **...**  w **Azure IoT Hub Devices** nagłówek sekcji. Jeśli nie widzisz wielokropka, kliknąć lub Najedź kursorem na nagłówek. 

4. Wybierz **wybierz Centrum IoT Hub**.

5. Jeśli nie jest zalogowany do konta platformy Azure, postępuj zgodnie z monitami, aby to zrobić. 

6. Wybierz swoją subskrypcję platformy Azure. 

7. Wybierz Centrum IoT hub. 

## <a name="create-a-device"></a>Tworzenie urządzenia

1. W Eksploratorze programu VS Code rozwiń **Azure IoT Hub Devices** sekcji. 

2. Kliknij pozycję **...**  w **Azure IoT Hub Devices** nagłówek sekcji. Jeśli nie widzisz wielokropka, kliknąć lub Najedź kursorem na nagłówek. 

3. Wybierz **tworzenie urządzenia usługi IoT Edge**. 

4. W polu tekstowym nadaj urządzeniu tego identyfikatora. 

Dane wyjściowe na ekranie zobaczysz wynik użycia polecenia. Informacje o urządzeniu zostanie wydrukowany, która obejmuje **deviceId** podane i **connectionString** służące do łączenia z urządzenia fizycznego do usługi IoT hub. 

## <a name="view-all-devices"></a>Wyświetl wszystkie urządzenia

Wszystkie urządzenia łączące się z Centrum IoT są wymienione w **Azure IoT Hub Devices** sekcji Eksploratorze kodu programu Visual Studio. Urządzenia usługi IoT Edge różnią się od urządzeń-Edge z inną ikonę i fakt, że ich można rozszerzyć, aby pokazać moduły wdrożone do każdego urządzenia. 

   ![Wyświetl urządzenia w programie VS Code](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>Pobieranie parametrów połączenia

Gdy wszystko będzie gotowe skonfigurować urządzenie, należy parametry połączenia, która łączy urządzenie fizyczne za pomocą jej tożsamości w usłudze IoT hub.

1. Kliknij prawym przyciskiem myszy, identyfikator urządzenia w **Azure IoT Hub Devices** sekcji. 
2. Wybierz **skopiuj parametry połączenia urządzenia**.

   Parametry połączenia są kopiowane do Schowka. 

Możesz również wybrać **uzyskać informacje o urządzeniu** z menu kliknij prawym przyciskiem myszy, aby sprawdzić wszystkie informacje o urządzeniu, w tym ciągu połączenia w oknie danych wyjściowych. 


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wdrażać moduły do urządzenia przy użyciu programu Visual Studio Code](how-to-deploy-modules-vscode.md).
