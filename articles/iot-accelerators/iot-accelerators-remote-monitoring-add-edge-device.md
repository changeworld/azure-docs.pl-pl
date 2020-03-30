---
title: Rozwiązanie do zdalnego monitorowania dodaje urządzenie Edge - Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób dodawania urządzenia Usługi IoT Edge do akceleratora rozwiązań do zdalnego monitorowania
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 0a42763ff47cccfa506acbbbd95d20d41eb0827f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72965370"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Dodawanie urządzenia Usługi IoT Edge do akceleratora rozwiązań do zdalnego monitorowania

Aby dodać urządzenie [IoT Edge](../iot-edge/about-iot-edge.md) do akceleratora rozwiązań, wykonaj następujące dwa kroki:

1. Dodaj urządzenie Edge na stronie **Eksplorator urządzeń** w interfejsie użytkownika sieci Web akceleratora rozwiązania zdalnego monitorowania.
1. Zainstaluj środowisko wykonawcze Usługi IoT Edge na urządzeniu Edge.

## <a name="add-the-iot-edge-device"></a>Dodawanie urządzenia IoT Edge

Aby dodać urządzenie usługi IoT Edge do akceleratora rozwiązania do monitorowania zdalnego, przejdź do strony **Device Explorer** w internetowym interfejsie użytkownika i kliknij pozycję **+ Nowe urządzenie**.

W panelu **Nowe urządzenie** wybierz pozycję **Urządzenie IoT Edge**. Dla innych właściwości możesz pozostawić ustawienia domyślne. Następnie kliknij pozycję **Zastosuj**:

![Dodawanie urządzenia usługi IoT Edge](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Alternatywne sposoby dodawania urządzenia IoT Edge

Istnieje również możliwość zarejestrowania urządzenia usługi IoT Edge bezpośrednio z wystąpieniem usługi IoT Hub w akceleratorze rozwiązań. Przed zapoznaniem się z tymi instrukcjami należy zapoznać się z nazwą centrum IoT hub w akceleratorze rozwiązań:

- [Rejestrowanie nowego urządzenia usługi Azure IoT Edge w witrynie Azure portal](../iot-edge/how-to-register-device.md#register-in-the-azure-portal)
- [Zarejestruj nowe urządzenie usługi Azure IoT Edge w usłudze Azure CLI](../iot-edge/how-to-register-device.md#register-with-the-azure-cli)
- [Rejestrowanie nowego urządzenia usługi Azure IoT Edge z programu Visual Studio Code](../iot-edge/how-to-register-device.md#register-with-visual-studio-code)

Po zarejestrowaniu urządzenia bezpośrednio z centrum IoT hub w akceleratora rozwiązania zdalnego monitorowania, jest on wymieniony na stronie **Eksploratora urządzeń** w interfejsie użytkownika sieci Web.

## <a name="install-the-iot-edge-runtime"></a>Instalowanie środowiska wykonawczego usługi IoT Edge

Przed wdrożeniem modułów na urządzeniu edge należy zainstalować środowisko wykonawcze usługi IoT Edge na rzeczywistym urządzeniu. Poniższe instrukcje dotyczące przewodników pokazują, jak zainstalować środowisko wykonawcze na typowych platformach urządzeń:

- [Instalowanie środowiska wykonawczego usługi Azure IoT Edge w systemie Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Instalowanie środowiska wykonawczego usługi Azure IoT Edge w systemie Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Instalowanie środowiska wykonawczego usługi Azure IoT Edge w systemie Windows w celu użycia z kontenerami systemu Windows](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Instalowanie środowiska wykonawczego usługi Azure IoT Edge w systemie Windows w celu użycia z kontenerami systemu Linux](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Instalowanie środowiska wykonawczego usługi IoT Edge w systemie Windows IoT Core](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>Następne kroki

Po przygotowaniu urządzenia IoT Edge następnym krokiem jest wdrożenie modułów. Zobacz [Importowanie pakietu usługi IoT Edge do akceleratora rozwiązań do zdalnego monitorowania](iot-accelerators-remote-monitoring-import-edge-package.md)
