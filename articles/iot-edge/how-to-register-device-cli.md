---
title: Zarejestruj nowe urządzenie brzegowe IoT Azure (CLI) | Dokumentacja firmy Microsoft
description: Użyj rozszerzenia IoT Azure CLI 2.0 zarejestrować nowe urządzenie brzegowe IoT
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/30/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6935dfe5b32364e402017cd3005ab17969ce1ce
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036383"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli-20"></a>Zarejestrować nowe urządzenie na krawędzi IoT Azure, Azure CLI 2.0

Przed użyciem urządzenia IoT Azure IoT krawędzi, musisz zarejestrować ich przy użyciu Centrum IoT. Po zarejestrowaniu urządzenia, pojawi się ciąg połączenia, który może służyć do konfigurowania Twojego urządzenia pod kątem obciążeń krawędzi. 

[Interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) to międzyplatformowe narzędzie wiersza polecenia typu open-source służące do zarządzania zasobami platformy Azure, takimi jak usługa IoT Edge. Umożliwia zarządzanie zasobami, inicjowania obsługi administracyjnej wystąpień usługi urządzenia i połączone w koncentratory fabrycznej Centrum IoT Azure. Nowe rozszerzenie IoT uzupełnia interfejs wiersza polecenia platformy Azure 2.0 o funkcje, takie jak zarządzanie urządzeniami i pełne możliwości usługi IoT Edge.

W tym artykule pokazano, jak zarejestrować nowe urządzenie brzegowe IoT używa interfejsu wiersza polecenia platformy Azure w wersji 2.0.

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT](../iot-hub/iot-hub-create-using-cli.md) w Twojej subskrypcji platformy Azure. 
* Zainstalowany w środowisku [interfejs wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Potrzebujesz co najmniej interfejsu wiersza polecenia platformy Azure 2.0 w wersji 2.0.24 lub nowszej. Użyj polecenia `az –-version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack. 
* [Rozszerzenia IoT Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Utwórz urządzenie

Aby utworzyć nową tożsamość urządzenia w Centrum IoT, użyj następującego polecenia: 

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

To polecenie obejmuje trzy parametry:
* **Identyfikator urządzenia**: Podaj opisową nazwę, która jest unikatowa dla Centrum IoT.
* **Nazwa koncentratora**: Podaj nazwę Centrum IoT.
* **włączone krawędzi**: ten parametr deklaruje, że urządzenie jest przeznaczona do użytku z krawędzią IoT.

   ![Tworzenie urządzenia usługi IoT Edge](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Wyświetl wszystkie urządzenia

Aby wyświetlić wszystkie urządzenia w Centrum IoT, użyj następującego polecenia:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Dowolne urządzenie, które jest zarejestrowane jako urządzenia IoT ma właściwość **capabilities.iotEdge** ustawioną **true**. 

## <a name="retrieve-the-connection-string"></a>Parametry połączenia

Gdy wszystko jest gotowe skonfigurować urządzenie, należy parametry połączenia, która łączy urządzenie fizyczne z swoją tożsamość w Centrum IoT. Użyj następującego polecenia do zwrócenia ciąg połączenia dla pojedynczego urządzenia:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name] 
   ```

Parametr id urządzenia jest rozróżniana wielkość liter. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wdrożenie modułów na urządzeniu z 2.0 interfejsu wiersza polecenia platformy Azure](how-to-deploy-modules-cli.md)