---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/7/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 1a750a97cdc940c0f0a3d7e33d6be0d33f811425
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53108026"
---
Jedną z najważniejszych funkcji usługi Azure IoT Edge jest możliwość wdrażania modułów na urządzeniach usługi IoT Edge z poziomu chmury. Moduł usługi IoT Edge to pakiet wykonywalny wdrażany jako kontener. W tej sekcji wdrożymy wstępnie skompilowany moduł z [sekcji Moduły usługi IoT Edge w portalu Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Moduł ten generuje dane telemetryczne dla symulowanego urządzenia.

1. W witrynie Microsoft Azure Portal wprowadź `Simulated Temperature Sensor` w polu wyszukiwania i otwórz wynik z portalu Marketplace.

   ![Termin „Simulated Temperature Sensor” w polu wyszukiwania w witrynie Azure Portal](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. W polu **Subskrypcja** wybierz subskrypcję usługi IoT Hub, z której korzystasz, jeśli nie została jeszcze wybrana.

3. W polu **IoT Hub** wybierz nazwę usługi IoT Hub, z której korzystasz, jeśli nie została jeszcze wybrana.

4. Kliknij pozycję **Znajdź urządzenie**, wybierz urządzenie usługi IoT Edge (o nazwie `myEdgeDevice`), a następnie wybierz pozycję **Utwórz**.

5. W kroku **Dodawanie modułów** kreatora kliknij moduł **SimulatedTemperatureSensor**, aby sprawdzić jego ustawienia konfiguracji, kliknij przycisk **Zapisz**, a następnie **Dalej**.

6. W kroku **Określanie tras** sprawdź, czy prawidłowo ustawiono domyślną trasę, która wysyła wszystkie komunikaty ze wszystkich modułów do usługi IoT Hub (`$upstream`). Jeśli tak nie jest, dodaj następujący kod, a następnie wybierz przycisk **Dalej**.

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

7. W kroku **Przegląd wdrożenia** kreatora wybierz pozycję **Prześlij**.

8. Wróć do strony szczegółów urządzenia i wybierz opcję **Odśwież**. Oprócz modułu edgeAgent, który został utworzony podczas pierwszego uruchomienia usługi, powinien być widoczny inny moduł środowiska uruchomieniowego (o nazwie **edgeHub**) oraz moduł **SimulatedTemperatureSensor**.

   Wyświetlenie nowych modułów może zająć kilka minut. Urządzenie usługi IoT Edge musi pobrać z chmury nowe informacje o wdrożeniu, uruchomić kontenery i zgłosić swój nowy stan usłudze IoT Hub. 

   ![Wyświetlanie modułu SimulatedTemperatureSensor na liście wdrożonych modułów](./media/iot-edge-deploy-module/deployed-modules-marketplace-temp.png)
