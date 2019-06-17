---
title: Rozwiązania do zdalnego monitorowania Dodaj urządzenie usługi Edge — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób dodawania urządzenia usługi IoT Edge do monitorowania zdalnego akceleratora rozwiązań
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: d34ac159a216c5c77214b4c8b799a233c3671235
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447378"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Dodaj urządzenia usługi IoT Edge na akceleratorze rozwiązanie monitorowania zdalnego

Aby dodać [usługi IoT Edge](../iot-edge/about-iot-edge.md) urządzenia, aby Twój akcelerator rozwiązań, wykonaj następujące dwa kroki:

1. Dodaj urządzenie usługi Edge na **Device Explorer** strony w zdalne monitorowanie rozwiązań akceleratora internetowego interfejsu użytkownika.
1. Zainstaluj środowisko uruchomieniowe usługi IoT Edge na urządzeniu usługi Edge.

## <a name="add-the-iot-edge-device"></a>Dodaj urządzenie usługi IoT Edge

Aby dodać urządzenie usługi IoT Edge do akceleratora rozwiązania do monitorowania zdalnego, przejdź do strony **Device Explorer** w internetowym interfejsie użytkownika i kliknij pozycję **+ Nowe urządzenie**.

W **nowe urządzenie** panelu, wybierz polecenie **urządzenie usługi IoT Edge**. Dla innych właściwości możesz pozostawić ustawienia domyślne. Następnie kliknij pozycję **Zastosuj**:

![Dodaj urządzenie usługi IoT Edge](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Alternatywne sposoby dodawania urządzenia usługi IoT Edge

Istnieje również możliwość zarejestrowania urządzenia usługi IoT Edge bezpośrednio z wystąpieniem usługi IoT Hub w akceleratorze rozwiązań. Musisz znać nazwę Centrum IoT, w tym akceleratorze rozwiązań, zanim wykonasz którąkolwiek z tych przewodników instruktażowych:

- [Rejestrowanie nowego urządzenia usługi Azure IoT Edge w witrynie Azure portal](../iot-edge/how-to-register-device-portal.md)
- [Rejestrowanie nowego urządzenia usługi Azure IoT Edge przy użyciu wiersza polecenia platformy Azure](../iot-edge/how-to-register-device-cli.md)
- [Rejestrowanie nowego urządzenia usługi Azure IoT Edge z programu Visual Studio Code](../iot-edge/how-to-register-device-vscode.md)

Po zarejestrowaniu urządzenia bezpośrednio za pomocą usługi IoT hub w akceleratora rozwiązania monitorowania zdalnego, ta opcja jest wyświetlana na **Device Explorer** strony w internetowym interfejsie użytkownika.

## <a name="install-the-iot-edge-runtime"></a>Zainstalować środowisko uruchomieniowe usługi IoT Edge

Przed wdrożeniem modułów na urządzenia brzegowe, należy zainstalować środowisko uruchomieniowe usługi IoT Edge na rzeczywistych urządzeniach. Następujące przewodniki z instrukcjami pokazują, jak zainstalować środowisko uruchomieniowe na wspólnej platformach urządzeń:

- [Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge w systemie Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge w systemie Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Zainstaluj środowisko uruchomieniowe usługi Azure IoT Edge na Windows do korzystania z kontenerów Windows](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Zainstaluj środowisko uruchomieniowe usługi Azure IoT Edge na Windows do korzystania z kontenerów systemu Linux](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Zainstaluj środowisko uruchomieniowe usługi IoT Edge w systemie Windows IoT Core](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy przygotowania urządzenia usługi IoT Edge, następnym krokiem jest wdrożenie modułów. Zobacz [importowania pakietu IoT Edge w akceleratorze rozwiązanie monitorowania zdalnego](iot-accelerators-remote-monitoring-import-edge-package.md)
