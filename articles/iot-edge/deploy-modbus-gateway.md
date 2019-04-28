---
title: Tłumaczenie protokołów modbus z bramami — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Zezwalanie urządzeniom korzystających z protokołu Modbus TCP na komunikację z usługą Azure IoT Hub poprzez utworzenie urządzenia bramy usługi IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 1c9855f982b888e8e1d68bfe5233983db8c826ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248050"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Łączenie urządzeń Modbus TCP za pośrednictwem bramy urządzeń usługi IoT Edge

Jeśli chcesz połączyć urządzenia IoT korzystające z protokołów Modbus TCP lub RTU z centrum usługi Azure IoT, użyj urządzenia IoT Edge jako bramy. Urządzenie bramy odczytuje dane z urządzeń korzystających z protokołu Modbus, a następnie przekazuje te dane do chmury przy użyciu obsługiwanego protokołu.

![Urządzenia Modbus połączyć z usługą IoT Hub za pośrednictwem bramy usługi IoT Edge](./media/deploy-modbus-gateway/diagram.png)

W tym artykule opisano sposób tworzenia własnego obrazu kontenera dla modułu Modbus (można też użyć wstępnie utworzonej próbki), a następnie wdrożenia go do urządzenia usługi IoT Edge, które będzie pełnić rolę bramy.

W tym artykule założono, że użytkownik korzysta z protokołu Modbus TCP. Aby uzyskać więcej informacji o sposobie konfigurowania modułu obsługi protokołu Modbus RTU, zobacz [moduł Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) projektu w usłudze GitHub.

## <a name="prerequisites"></a>Wymagania wstępne
* Urządzenie usługi Azure IoT Edge. Przewodnik dotyczący sposobu konfigurowania jeden, zobacz [wdrożenia usługi Azure IoT Edge na Windows](quickstart.md) lub [Linux](quickstart-linux.md).
* Parametry połączenia klucza podstawowego dla urządzenia usługi IoT Edge.
* Fizyczne lub symulowane urządzenie Modbus obsługujące protokół Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Przygotowanie kontenera Modbus

Jeśli chcesz przetestować funkcje bramy Modbus, firma Microsoft przygotowała przykładowy moduł, którego możesz użyć. W portalu Azure Marketplace można uzyskać dostęp do modułu [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview), lub przy użyciu obrazu identyfikatora URI, **mcr.microsoft.com/azureiotedge/modbus:1.0**.

Jeśli chcesz utworzyć własny moduł i dostosować go w środowisku jest typu open-source [moduł Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) projektu w usłudze GitHub. Wykonaj instrukcje zawarte w tym projekcie, aby utworzyć własny obraz kontenera. Jeśli tworzysz własny obraz kontenera, zapoznaj się [programowanie C# modułów w programie Visual Studio](how-to-visual-studio-develop-csharp-module.md) lub [opracowanie modułów w programie Visual Studio Code](how-to-vs-code-develop-module.md). Te artykuły zawierają instrukcje dotyczące tworzenia nowych modułów i publikowania obrazów kontenera do rejestru.

## <a name="try-the-solution"></a>Wypróbuj rozwiązanie

W tej sekcji przedstawiono wdrażanie moduł Modbus przykładowej firmy Microsoft do urządzenia usługi IoT Edge.

1. W witrynie [Azure Portal](https://portal.azure.com/) przejdź do centrum IoT Hub.

2. Przejdź do **usługi IoT Edge** i kliknij przycisk na urządzeniu usługi IoT Edge.

3. Wybierz opcję **Ustaw moduły**.

4. Dodaj moduł Modbus:

   1. Kliknij przycisk **Dodaj** i wybierz **moduł usługi IoT Edge**.

   2. W polu **Nazwa** wpisz „modbus”.

   3. W polu **Obraz** wprowadź identyfikator URI obrazu przykładowego kontenera: `mcr.microsoft.com/azureiotedge/modbus:1.0`.

   4. Zaznacz pole **Włącz**, aby zaktualizować żądane właściwości bliźniaczego modułu.

   5. Skopiuj następujące dane JSON do pola tekstowego. Zmień wartość atrybutu **SlaveConnection** na adres IPv4 urządzenia Modbus.

      ```JSON
      {
        "properties.desired":{
          "PublishInterval":"2000",
          "SlaveConfigs":{
            "Slave01":{
              "SlaveConnection":"<IPV4 address>","HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{
                "Op01":{
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"400001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. Wybierz pozycję **Zapisz**.

5. W kroku **Dodawanie modułów** wybierz opcję **Dalej**.

7. W kroku **Określanie tras** skopiuj następujące dane JSON do pola tekstowego. Ta trasa wysyła wszystkie komunikaty zebrane przez moduł Modbus do centrum IoT Hub. W tej trasie "modbusOutput" jest punkt końcowy, który moduł Modbus używa do wydawania danych, a "upstream" jest specjalną lokalizacją docelową, która nakazuje Centrum IoT Edge hub wysyłanie komunikatów do Centrum IoT Hub.
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. Wybierz opcję **Dalej**.

9. W kroku **Przegląd wdrożenia** wybierz pozycję **Prześlij**.

10. Wróć do strony szczegółów urządzenia i wybierz opcję **Odśwież**. Zostanie wyświetlone nowe **modbus** modułu korzystania oraz środowisko uruchomieniowe usługi IoT Edge.

## <a name="view-data"></a>Wyświetlanie danych
Wyświetlanie danych przechodzących przez moduł modbus:
```cmd/sh
iotedge logs modbus
```

Można również wyświetlić dane telemetryczne urządzenie wysyła za pomocą [rozszerzenie Azure IoT Hub Toolkit dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dawniej: rozszerzenie Azure IoT Toolkit).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak urządzenia usługi IoT Edge mogą pełnić rolę bram, zobacz [tworzenie urządzenia usługi IoT Edge, która pełni rolę przezroczystej bramy](./how-to-create-transparent-gateway.md).
- Aby uzyskać więcej informacji o działaniu modułów usługi IoT Edge, zobacz [modułów Omówienie usługi Azure IoT Edge](iot-edge-modules.md).
