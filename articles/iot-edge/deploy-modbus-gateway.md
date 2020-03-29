---
title: Tłumacz protokoły modbus z bramami — Usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Zezwalanie urządzeniom korzystających z protokołu Modbus TCP na komunikację z usługą Azure IoT Hub poprzez utworzenie urządzenia bramy usługi IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 23fbbd87230ea0a0147dc9d90c77729f4d531e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511148"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Łączenie urządzeń Modbus TCP za pośrednictwem bramy urządzeń Usługi IoT Edge

Jeśli chcesz połączyć urządzenia IoT, które używają protokołów Modbus TCP lub RTU do centrum Usługi Azure IoT hub, możesz użyć urządzenia usługi IoT Edge jako bramy. Urządzenie bramy odczytuje dane z urządzeń korzystających z protokołu Modbus, a następnie przekazuje te dane do chmury przy użyciu obsługiwanego protokołu.

![Urządzenia Modbus łączą się z Centrum IoT przez bramę Usługi IoT Edge](./media/deploy-modbus-gateway/diagram.png)

W tym artykule opisano sposób tworzenia własnego obrazu kontenera dla modułu Modbus (można też użyć wstępnie utworzonej próbki), a następnie wdrożenia go do urządzenia usługi IoT Edge, które będzie pełnić rolę bramy.

W tym artykule założono, że użytkownik korzysta z protokołu Modbus TCP. Aby uzyskać więcej informacji na temat konfigurowania modułu do obsługi modbus RTU, zobacz projekt [modułu Usługi Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) w usłudze GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie usługi Azure IoT Edge. Aby zapoznać się z instrukcją konfigurowania jednej z nich, zobacz [Wdrażanie usługi Azure IoT Edge w systemie Windows](quickstart.md) lub [Linux](quickstart-linux.md).
* Parametry połączenia klucza podstawowego dla urządzenia usługi IoT Edge.
* Fizyczne lub symulowane urządzenie Modbus obsługujące protokół Modbus TCP. Musisz znać jego adres IPv4.

## <a name="prepare-a-modbus-container"></a>Przygotowanie kontenera Modbus

Jeśli chcesz przetestować funkcje bramy Modbus, firma Microsoft przygotowała przykładowy moduł, którego możesz użyć. Dostęp do modułu można uzyskać z witryny Azure Marketplace, `mcr.microsoft.com/azureiotedge/modbus:1.0` [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)lub z identyfikatorem URI obrazu.

Jeśli chcesz utworzyć własny moduł i dostosować go do środowiska, istnieje projekt [modułu modbus usługi Azure ioT Edge](https://github.com/Azure/iot-edge-modbus) typu open source w usłudze GitHub. Wykonaj instrukcje zawarte w tym projekcie, aby utworzyć własny obraz kontenera. Aby utworzyć obraz kontenera, zapoznaj się [z tworzeniem modułów C# w programie Visual Studio](how-to-visual-studio-develop-csharp-module.md) lub tworzenie [modułów w programie Visual Studio Code](how-to-vs-code-develop-module.md). Te artykuły zawierają instrukcje dotyczące tworzenia nowych modułów i publikowania obrazów kontenerów w rejestrze.

## <a name="try-the-solution"></a>Wypróbuj rozwiązanie

W tej sekcji otoczono wdrażanie przykładowego modułu Modbus firmy Microsoft na urządzeniu Usługi IoT Edge.

1. W witrynie [Azure Portal](https://portal.azure.com/) przejdź do centrum IoT Hub.

2. Przejdź do **aplikacji IoT Edge** i kliknij urządzenie IoT Edge.

3. Wybierz **opcję Ustaw moduły**.

4. W sekcji **Moduły brzegowe IoT** dodaj moduł Modbus:

   1. Kliknij pozycję **Dodaj** listy rozwijanej i wybierz pozycję **Moduł Marketplace**.
   2. Wyszukaj `Modbus` i wybierz **moduł Modbus TCP** firmy Microsoft.
   3. Moduł jest automatycznie konfigurowany dla centrum IoT Hub i pojawia się na liście modułów brzegowych IoT. Trasy są również konfigurowane automatycznie. Wybierz pozycję **Przegląd + utwórz**.
   4. Przejrzyj manifest wdrożenia i wybierz pozycję **Utwórz**.

5. Wybierz moduł Modbus `ModbusTCPModule`na liście i wybierz kartę **Ustawienia bliźniaczej reprezentacji modułu.** Wymagany JSON dla modułu bliźniaczej reprezentacji żądane właściwości jest automatycznie wypełniane.

6. Poszukaj właściwości **SlaveConnection** w JSON i ustaw jej wartość na adres IPv4 urządzenia Modbus.

7. Wybierz pozycję **Update** (Aktualizuj).

8. Wybierz **pozycję Recenzja + utwórz**, przejrzyj wdrożenie, a następnie wybierz pozycję **Utwórz**.

9. Wróć do strony szczegółów urządzenia i wybierz opcję **Odśwież**. Powinien zostać wyświetlony `ModbusTCPModule` nowy moduł uruchomiony wraz ze środowiska wykonawczego IoT Edge.

## <a name="view-data"></a>Wyświetlanie danych

Zobacz dane przesyłane przez moduł Modbus:

```cmd/sh
iotedge logs modbus
```

Można również wyświetlić dane telemetryczne, które urządzenie wysyła przy użyciu [rozszerzenia usługi Azure IoT Hub dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dawniej rozszerzenie zestawu narzędzi Azure IoT).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o tym, jak urządzenia usługi IoT Edge mogą działać jako bramy, zobacz [Tworzenie urządzenia usługi IoT Edge, które działa jako przezroczysta brama.](./how-to-create-transparent-gateway.md)
* Aby uzyskać więcej informacji na temat działania modułów usługi IoT Edge, zobacz [Opis modułów usługi Azure IoT Edge.](iot-edge-modules.md)
