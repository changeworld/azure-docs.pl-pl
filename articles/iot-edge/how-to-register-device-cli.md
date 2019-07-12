---
title: Rejestrowanie nowego urządzenia z wiersza polecenia — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Zarejestrować nowe urządzenie usługi IoT Edge i pobieranie parametrów połączenia za pomocą rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: bbb8dcb410f17ba894210c9c090ec0975f83c0b3
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796135"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Rejestrowanie nowego urządzenia usługi Azure IoT Edge przy użyciu wiersza polecenia platformy Azure

Zanim użyjesz urządzeń IoT za pomocą usługi Azure IoT Edge, musisz zarejestrować ich przy użyciu usługi IoT hub. Po zarejestrowaniu urządzenia, pojawi się parametry połączenia, który może służyć do konfigurowania urządzenia pod kątem obciążeń IoT Edge.

[Interfejs wiersza polecenia Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) jest typu open-source wielu platformach narzędzia wiersza polecenia do zarządzania zasobami platformy Azure, takich jak usługi IoT Edge. Umożliwia zarządzanie zasobami usługi Azure IoT Hub, wystąpieniami usługi device provisioning i połączonymi centrami po gotowych. Nowe rozszerzenie IoT uzupełnia interfejs wiersza polecenia platformy Azure przy użyciu funkcji, takich jak zarządzanie urządzeniami i pełne możliwości usługi IoT Edge.

W tym artykule pokazano, jak zarejestrować nowe urządzenie usługi IoT Edge przy użyciu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* [Usługi IoT hub](../iot-hub/iot-hub-create-using-cli.md) w subskrypcji platformy Azure.
* [Interfejs wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w danym środowisku. Co najmniej z wiersza polecenia platformy Azure musi być w wersji 2.0.24 lub nowszej. Użyj polecenia `az --version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack.
* [Rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Tworzenie urządzenia

Użyj [tworzenie az iot hub — tożsamość urządzenia](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) polecenie, aby utworzyć nową tożsamość urządzenia w usłudze IoT hub. Na przykład:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Tego polecenia obejmuje trzy parametry:

* **Identyfikator urządzenia**: Podaj opisową nazwę, która jest unikatowa w Centrum IoT.

* **Nazwa koncentratora**: Podaj nazwę Centrum IoT hub.

* **włączone usługi Edge**: Ten parametr deklaruje, że urządzenie jest przeznaczona do użytku z usługą IoT Edge.

   ![AZ iot hub — tożsamość urządzenia — Tworzenie danych wyjściowych](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Wyświetl wszystkie urządzenia

Użyj [az iot hub tożsamości urządzenia listy](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) polecenie, aby wyświetlić wszystkie urządzenia w usłudze IoT hub. Przykład:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Każde urządzenie, które są zarejestrowane jako urządzenia usługi IoT Edge będzie mieć ustawioną właściwość **capabilities.iotEdge** równa **true**.

## <a name="retrieve-the-connection-string"></a>Pobieranie parametrów połączenia

Gdy wszystko będzie gotowe skonfigurować urządzenie, należy parametry połączenia, która łączy urządzenie fizyczne za pomocą jej tożsamości w usłudze IoT hub. Użyj [tożsamości urządzenia Centrum az iot — show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) polecenie, aby zwracać parametry połączenia na jednym urządzeniu:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

Wartość `device-id` parametrów jest rozróżniana wielkość liter. Nie należy kopiować znaków cudzysłowu wokół parametry połączenia.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wdrażać moduły na urządzeniu z wiersza polecenia platformy Azure](how-to-deploy-modules-cli.md).
