---
title: Rejestrowanie nowego urządzenia z programu Visual Studio Code — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Użyj programu Visual Studio Code, aby utworzyć nowe urządzenie usługi IoT Edge w usłudze Azure IoT hub i pobieranie parametrów połączenia
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c8fce104d48acc3a562599c65eb15cb0a66657b7
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495267"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Rejestrowanie nowego urządzenia usługi Azure IoT Edge z programu Visual Studio Code

Zanim użyjesz urządzeń IoT za pomocą usługi Azure IoT Edge, musisz zarejestrować ich przy użyciu usługi IoT hub. Po zarejestrowaniu urządzenia, pojawi się parametry połączenia, który może służyć do konfigurowania urządzenia pod kątem obciążeń IoT Edge.

W tym artykule pokazano, jak zarejestrować nowe urządzenie usługi IoT Edge przy użyciu programu Visual Studio Code (VS Code). Istnieje wiele sposobów, aby wykonać większość operacji w programie VS Code. W tym artykule używa Eksploratora, ale można również użyć paletę poleceń do wykonywania kroków opisanych.

## <a name="prerequisites"></a>Wymagania wstępne

* [Usługi IoT hub](../iot-hub/iot-hub-create-through-portal.md) w subskrypcji platformy Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) for Visual Studio Code

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się do dostępu do usługi IoT hub

Rozszerzenia usługi Azure IoT dla programu Visual Studio Code służy do wykonywania operacji w Centrum IoT. Dla tych operacji do pracy musisz zalogować się do konta platformy Azure i wybierz swoje Centrum IoT.

1. W programie Visual Studio Code Otwórz **Explorer** widoku.

1. W dolnej części Eksploratora rozwiń **usługi Azure IoT Hub** sekcji.

   ![Rozwiń sekcję usługi Azure IoT Hub Devices](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

1. Kliknij pozycję **...**  w **usługi Azure IoT Hub** nagłówek sekcji. Jeśli nie widzisz wielokropka, kliknąć lub Najedź kursorem na nagłówek.

1. Wybierz **wybierz Centrum IoT Hub**.

1. Jeśli użytkownik nie jest zalogowany do konta platformy Azure, postępuj zgodnie z monitami, aby to zrobić.

1. Wybierz swoją subskrypcję platformy Azure.

1. Wybierz Centrum IoT hub.

## <a name="create-a-device"></a>Tworzenie urządzenia

1. W Eksploratorze programu VS Code rozwiń **Azure IoT Hub Devices** sekcji.

1. Kliknij pozycję **...**  w **Azure IoT Hub Devices** nagłówek sekcji. Jeśli nie widzisz wielokropka, kliknąć lub Najedź kursorem na nagłówek.

1. Wybierz **tworzenie urządzenia usługi IoT Edge**.

1. W polu tekstowym nadaj urządzeniu tego identyfikatora.

Dane wyjściowe na ekranie zobaczysz wynik użycia polecenia. Informacje o urządzeniu zostanie wydrukowany, która obejmuje **deviceId** podane i **connectionString** służące do łączenia z urządzenia fizycznego do usługi IoT hub.

## <a name="view-all-devices"></a>Wyświetl wszystkie urządzenia

Wszystkie urządzenia łączące się z Centrum IoT są wymienione w **usługi Azure IoT Hub** sekcji Eksploratorze kodu programu Visual Studio. Urządzenia usługi IoT Edge różnią się od urządzeń-Edge z inną ikonę oraz fakt, że **$edgeAgent** i **$edgeHub** moduły są wdrażane na każde urządzenie usługi IoT Edge.

   ![Wyświetl wszystkie urządzenia usługi IoT Edge w usłudze IoT hub](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>Pobieranie parametrów połączenia

Gdy wszystko będzie gotowe skonfigurować urządzenie, należy parametry połączenia, która łączy urządzenie fizyczne za pomocą jej tożsamości w usłudze IoT hub.

1. Kliknij prawym przyciskiem myszy, identyfikator urządzenia w **usługi Azure IoT Hub** sekcji.

1. Wybierz **skopiuj parametry połączenia urządzenia**.

   Parametry połączenia są kopiowane do Schowka.

Możesz również wybrać **uzyskać informacje o urządzeniu** z menu kliknij prawym przyciskiem myszy, aby sprawdzić wszystkie informacje o urządzeniu, w tym ciągu połączenia w oknie danych wyjściowych.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wdrażać moduły do urządzenia przy użyciu programu Visual Studio Code](how-to-deploy-modules-vscode.md).
