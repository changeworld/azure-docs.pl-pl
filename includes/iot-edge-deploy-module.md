---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/14/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 06e2be14cf7592edf373b6f679ac5d9ff3c87269
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "40161460"
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
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

1. W kroku **Przegląd wdrożenia** wybierz pozycję **Prześlij**.
1. Wróć do strony szczegółów urządzenia i wybierz opcję **Odśwież**. Oprócz modułu edgeAgent, który został utworzony podczas pierwszego uruchomienia usługi, powinien być widoczny inny moduł środowiska uruchomieniowego o nazwie **edgeHub** oraz moduł **tempSensor**. 

   ![Wyświetlanie modułu tempSensor na liście wdrożonych modułów](./media/iot-edge-deploy-module/deployed-modules.png)
