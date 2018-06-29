---
title: Wdrażanie protokołu Modbus w usłudze Azure IoT Edge | Microsoft Docs
description: Zezwalanie urządzeniom korzystających z protokołu Modbus TCP na komunikację z usługą Azure IoT Hub poprzez utworzenie urządzenia bramy usługi IoT Edge
author: kgremban
manager: timlt
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: kgremban
ms.openlocfilehash: 9e69faf1b07af0e60cfd21cd6eb9f00e211ab91e
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031757"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Podłącz urządzenia Modbus TCP za pośrednictwem bramy urządzenia IoT krawędzi

Jeśli chcesz połączyć urządzenia IoT korzystające z protokołów Modbus TCP lub RTU z centrum usługi Azure IoT, użyj urządzenia IoT Edge jako bramy. Urządzenie bramy odczytuje dane z urządzeń korzystających z protokołu Modbus, a następnie przekazuje te dane do chmury przy użyciu obsługiwanego protokołu. 

![Urządzenia korzystające z protokołu Modbus łączą się z usługą IoT Hub za pośrednictwem bramy granicznej](./media/deploy-modbus-gateway/diagram.png)

W tym artykule opisano sposób tworzenia własnego obrazu kontenera dla modułu Modbus (można też użyć wstępnie utworzonej próbki), a następnie wdrożenia go do urządzenia usługi IoT Edge, które będzie pełnić rolę bramy. 

W tym artykule założono, że użytkownik korzysta z protokołu Modbus TCP. Aby uzyskać więcej informacji o sposobie konfigurowania modułu na potrzeby obsługi protokołu Modbus RTU, zobacz projekt [Moduł Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) w witrynie Github. 

## <a name="prerequisites"></a>Wymagania wstępne
* Urządzenie usługi Azure IoT Edge. Aby skorzystać z przewodnika dotyczącego konfiguracji takiego urządzenia, zobacz [Wdrażanie usługi Azure IoT Edge na symulowanym urządzeniu w systemie Windows](quickstart.md) lub [Linux](quickstart-linux.md). 
* Parametry połączenia klucza podstawowego dla urządzenia usługi IoT Edge.
* Fizyczne lub symulowane urządzenie Modbus obsługujące protokół Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Przygotowanie kontenera Modbus

Jeśli chcesz przetestować funkcje bramy Modbus, firma Microsoft przygotowała przykładowy moduł, którego możesz użyć. Aby użyć przykładowego modułu, przejdź do sekcji [Uruchamianie rozwiązania](#run-the-solution) i wprowadź następujący identyfikator URI obrazu: 

```URL
microsoft/azureiotedge-modbus-tcp:1.0-preview
```

Jeśli chcesz utworzyć własny moduł i dostosować go pod kątem środowiska użytkownika, istnieje projekt [Moduł Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) typu open source w witrynie Github. Wykonaj instrukcje zawarte w tym projekcie, aby utworzyć własny obraz kontenera. Jeśli tworzysz własny obraz kontenera, zobacz [Opracowanie i wdrożenie modułu IoT Edge w języku C#](tutorial-csharp-module.md), aby uzyskać instrukcje dotyczące publikowania obrazów kontenera w rejestrze oraz wdrażania niestandardowego modułu na urządzeniu. 


## <a name="run-the-solution"></a>Uruchamianie rozwiązania
1. W witrynie [Azure Portal](https://portal.azure.com/) przejdź do centrum IoT Hub.
2. Przejdź do **krawędzi IoT** i kliknij na urządzeniu IoT krawędzi.
3. Wybierz opcję **Ustaw moduły**.
4. Dodaj moduł Modbus:
   1. Kliknij przycisk **Dodaj** i wybierz **krawędzi IoT modułu**.
   2. W polu **Nazwa** wpisz „modbus”.
   3. W polu **Obraz** wprowadź identyfikator URI obrazu przykładowego kontenera: `microsoft/azureiotedge-modbus-tcp:1.0-preview`.
   4. Zaznacz pole **Włącz**, aby zaktualizować żądane właściwości bliźniaczego modułu.
   5. Skopiuj następujące dane JSON do pola tekstowego. Zmień wartość atrybutu **SlaveConnection** na adres IPv4 urządzenia Modbus.

      ```JSON
      {  
        "properties.desired":{  
          "PublishInterval":"2000",
          "SlaveConfigs":{  
            "Slave01":{  
              "SlaveConnection":"<IPV4 address>",
              "HwId":"PowerMeter-0a:01:01:01:01:01",
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
7. W kroku **Określanie tras** skopiuj następujące dane JSON do pola tekstowego. Ta trasa wysyła wszystkie komunikaty zebrane przez moduł Modbus do centrum IoT Hub. W tej trasie „modbusOutput” jest punktem końcowym, którego moduł Modbus używa do wydawania danych, a „upstream” jest specjalną lokalizacją docelową, która nakazuje centrum Edge Hub wysyłanie komunikatów do centrum IoT Hub. 
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. Wybierz opcję **Dalej**. 
9. W **przeglądu wdrożenia** krok, wybierz opcję **przesyłania**. 
10. Wróć do strony szczegółów urządzenia i wybierz opcję **Odśwież**. Powinien zostać wyświetlony nowy **modbus** moduł uruchomiona wraz ze środowiska uruchomieniowego IoT krawędzi.

## <a name="view-data"></a>Wyświetlanie danych
Wyświetlanie danych przechodzących przez moduł modbus:
```cmd/sh
docker logs -f modbus
```

Możesz też wyświetlić telemetrię wysyłaną przez urządzenie przy użyciu [narzędzia eksploratora centrum IoT Hub](https://github.com/azure/iothub-explorer). 

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat sposobu urządzenia IoT brzegowe może działać jako bram, zobacz [Utwórz urządzenie brzegowe IoT, który działa jako brama przezroczyste][lnk-transparent-gateway-linux]
- Aby uzyskać więcej informacji o działaniu modułów usługi IoT Edge, zobacz [Opis modułów usługi Azure IoT Edge](iot-edge-modules.md)

<!-- Links -->
[lnk-transparent-gateway-linux]: ./how-to-create-transparent-gateway-linux.md