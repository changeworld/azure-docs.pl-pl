---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/14/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 536857a5fe3ec3cc037f21835a4152f93197bcb8
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977407"
---
Jedną z najważniejszych funkcji usługi Azure IoT Edge jest możliwość wdrażania modułów na urządzeniach usługi IoT Edge z poziomu chmury. Moduł usługi IoT Edge to pakiet wykonywalny wdrażany jako kontener. W tej sekcji zapoznasz się ze sposobem wdrażania modułu generującego dane telemetryczne na symulowanym urządzeniu.

1. W witrynie Azure Portal przejdź do centrum IoT Hub.

2. Przejdź do usługi **IoT Edge** w obszarze **Automatyczne zarządzanie urządzeniami** i wybierz urządzenie usługi IoT Edge.

3. Wybierz pozycję **Ustaw moduły**. W portalu zostanie otwarty kreator obejmujący trzy kroki. Przeprowadzi Cię on przez procesy dodawania modułów, określania tras i przeglądania wdrożenia. 

4. W kroku **Dodawanie modułów** kreatora znajdź sekcję **Moduły wdrażania**. Kliknij pozycję **Dodaj**, a następnie wybierz pozycję **Moduł usługi IoT Edge**.

   ![Dodawanie nowego modułu usługi IoT Edge](./media/iot-edge-deploy-module/add-module.png)

5. W polu **Nazwa** wprowadź wartość `tempSensor`.

6. W polu **Identyfikator URI obrazu** wprowadź wartość `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.

7. Pozostaw inne ustawienia bez zmian, a następnie wybierz pozycję **Zapisz**.

   ![Zapisywanie modułu usługi IoT Edge po wprowadzeniu nazwy i identyfikatora URI obrazu](./media/iot-edge-deploy-module/name-image.png)

8. Po powrocie do pierwszego kroku kreatora wybierz przycisk **Dalej**.

9. W kroku **Określanie tras** kreatora powinna być widoczna domyślna trasa, która wysyła wszystkie komunikaty ze wszystkich modułów do centrum IoT Hub. Jeśli tak nie jest, dodaj następujący kod, a następnie wybierz przycisk **Dalej**.

   ```json
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

10. W kroku **Przegląd wdrożenia** kreatora wybierz pozycję **Prześlij**.

11. Wróć do strony szczegółów urządzenia i wybierz opcję **Odśwież**. Oprócz modułu edgeAgent, który został utworzony podczas pierwszego uruchomienia usługi, powinien być widoczny inny moduł środowiska uruchomieniowego o nazwie **edgeHub** oraz moduł **tempSensor**.

   Wyświetlenie nowych modułów może zająć kilka minut. Urządzenie usługi IoT Edge musi pobrać z chmury nowe informacje o wdrożeniu, uruchomić kontenery i zgłosić swój nowy stan usłudze IoT Hub. 

   ![Wyświetlanie modułu tempSensor na liście wdrożonych modułów](./media/iot-edge-deploy-module/deployed-modules.png)
