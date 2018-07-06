---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/27/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 3e7fcde204966e5a6a8154ab307807652cfa0419
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37086215"
---
Jedną z najważniejszych funkcji usługi Azure IoT Edge jest możliwość wdrażania modułów na urządzeniach usługi IoT Edge z poziomu chmury. Moduł usługi IoT Edge to pakiet wykonywalny wdrażany jako kontener. W tej sekcji zapoznasz się ze sposobem wdrażania modułu generującego dane telemetryczne na symulowanym urządzeniu. 

1. W witrynie Azure Portal przejdź do centrum IoT Hub.
1. Przejdź do pozycji **IoT Edge** i wybierz urządzenie usługi IoT Edge.
1. Wybierz pozycję **Ustaw moduły**.
1. W sekcji **Moduły wdrożenia** na stronie kliknij przycisk **Dodaj**, a następnie wybierz opcję **Moduł usługi IoT Edge**.
1. W polu **Nazwa** wprowadź wartość `tempSensor`. 
1. W polu **Identyfikator URI obrazu** wprowadź wartość `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`. 
1. Pozostaw inne ustawienia bez zmian, a następnie wybierz pozycję **Zapisz**.

   ![Zapisywanie modułu usługi IoT Edge po wprowadzeniu nazwy i identyfikatora URI obrazu](./media/iot-edge-deploy-module/name-image.png)

1. W kroku **Dodawanie modułów** wybierz opcję **Dalej**.
1. W kroku **Określanie tras** powinna być widoczna domyślna trasa, która wysyła wszystkie komunikaty ze wszystkich modułów do centrum IoT Hub. Jeśli tak nie jest, dodaj następujący kod, a następnie wybierz przycisk **Dalej**.

   ```json
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

1. W kroku **Przegląd wdrożenia** wybierz pozycję **Prześlij**.
1. Wróć do strony szczegółów urządzenia i wybierz opcję **Odśwież**. Oprócz modułu edgeAgent, który został utworzony podczas pierwszego uruchomienia usługi, powinien być widoczny inny moduł środowiska uruchomieniowego o nazwie **edgeHub** oraz moduł **tempSensor**. 

   ![Wyświetlanie modułu tempSensor na liście wdrożonych modułów](./media/iot-edge-deploy-module/deployed-modules.png)
