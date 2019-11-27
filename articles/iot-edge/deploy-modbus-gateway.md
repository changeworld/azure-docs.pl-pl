---
title: Tłumaczenie protokołów modbus z bramami — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Zezwalanie urządzeniom korzystających z protokołu Modbus TCP na komunikację z usługą Azure IoT Hub poprzez utworzenie urządzenia bramy usługi IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: kgremban
ms.openlocfilehash: 649c7f620b83464d1bb56cf4b8191b0747105f01
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457211"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Łączenie urządzeń Modbus TCP za pośrednictwem bramy urządzeń usługi IoT Edge

Jeśli chcesz połączyć urządzenia IoT korzystające z protokołów Modbus TCP lub RTU z centrum usługi Azure IoT, użyj urządzenia IoT Edge jako bramy. Urządzenie bramy odczytuje dane z urządzeń korzystających z protokołu Modbus, a następnie przekazuje te dane do chmury przy użyciu obsługiwanego protokołu.

![Urządzenia Modbus łączą się z IoT Hub za poorednictwem bramy IoT Edge](./media/deploy-modbus-gateway/diagram.png)

W tym artykule opisano sposób tworzenia własnego obrazu kontenera dla modułu Modbus (można też użyć wstępnie utworzonej próbki), a następnie wdrożenia go do urządzenia usługi IoT Edge, które będzie pełnić rolę bramy.

W tym artykule założono, że użytkownik korzysta z protokołu Modbus TCP. Więcej informacji o sposobie konfigurowania modułu do obsługi Modbus RTU można znaleźć w projekcie [modułu Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne
* Urządzenie usługi Azure IoT Edge. Aby zapoznać się z przewodnikiem dotyczącym konfigurowania jednego z nich, zobacz [wdrażanie Azure IoT Edge w systemie Windows](quickstart.md) lub [Linux](quickstart-linux.md).
* Parametry połączenia klucza podstawowego dla urządzenia usługi IoT Edge.
* Fizyczne lub symulowane urządzenie Modbus obsługujące protokół Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Przygotowanie kontenera Modbus

Jeśli chcesz przetestować funkcje bramy Modbus, firma Microsoft przygotowała przykładowy moduł, którego możesz użyć. Możesz uzyskać dostęp do modułu z witryny Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)lub za pomocą identyfikatora URI obrazu **MCR.Microsoft.com/azureiotedge/Modbus:1.0**.

Jeśli chcesz utworzyć własny moduł i dostosować go do swojego środowiska, istnieje [Azure IoT Edge projektu modułu](https://github.com/Azure/iot-edge-modbus) "open source" w witrynie GitHub. Wykonaj instrukcje zawarte w tym projekcie, aby utworzyć własny obraz kontenera. Aby utworzyć obraz kontenera, zapoznaj się z tematem [ C# opracowywanie modułów w programie Visual Studio](how-to-visual-studio-develop-csharp-module.md) lub [opracowywanie modułów w Visual Studio Code](how-to-vs-code-develop-module.md). Te artykuły zawierają instrukcje dotyczące tworzenia nowych modułów i publikowania obrazów kontenerów w rejestrze.

## <a name="try-the-solution"></a>Wypróbuj rozwiązanie

Ta sekcja zawiera instrukcje wdrażania przykładowego modułu Modbus firmy Microsoft na urządzeniu IoT Edge.

1. W witrynie [Azure Portal](https://portal.azure.com/) przejdź do centrum IoT Hub.

2. Przejdź do **IoT Edge** i kliknij urządzenie IoT Edge.

3. Wybierz opcję **Ustaw moduły**.

4. Dodaj moduł Modbus:

   1. Kliknij przycisk **Dodaj** i wybierz **moduł IoT Edge**.

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
                  "StartAddress":"40001",
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

7. W kroku **Określanie tras** skopiuj następujące dane JSON do pola tekstowego. Ta trasa wysyła wszystkie komunikaty zebrane przez moduł Modbus do centrum IoT Hub. W tej trasie **modbusOutput** jest punktem końcowym, który używa modułu Modbus do danych wyjściowych, a **$upstream** jest specjalnym miejscem docelowym, które informuje Centrum IoT Edge o wysyłaniu komunikatów do IoT Hub.

   ```JSON
   {
     "routes": {
       "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
     }
   }
   ```

8. Wybierz opcję **Dalej**.

9. W kroku **Przegląd wdrożenia** wybierz pozycję **Prześlij**.

10. Wróć do strony szczegółów urządzenia i wybierz opcję **Odśwież**. Powinien zostać wyświetlony nowy moduł **Modbus** oraz środowisko uruchomieniowe IoT Edge.

## <a name="view-data"></a>Wyświetlanie danych
Wyświetlanie danych przechodzących przez moduł modbus:
```cmd/sh
iotedge logs modbus
```

Możesz również wyświetlić dane telemetryczne wysyłane przez urządzenie przy użyciu [rozszerzenia zestawu azure IoT Hub Toolkit dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dawniej rozszerzenie usługi Azure IoT Toolkit).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o tym, jak urządzenia IoT Edge mogą działać jako bramy, zobacz [tworzenie IoT Edge urządzenia, które działa jako niejawna Brama](./how-to-create-transparent-gateway.md).
- Aby uzyskać więcej informacji na temat sposobu działania modułów IoT Edge, zobacz [Opis modułów Azure IoT Edge](iot-edge-modules.md).
