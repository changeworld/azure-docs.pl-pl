---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/31/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: dd4873017105db190f9a468ec1f1f77f4e8c9c0e
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977131"
---
Jedną z najważniejszych funkcji usługi Azure IoT Edge jest możliwość wdrażania modułów na urządzeniach usługi IoT Edge z poziomu chmury. Moduł usługi IoT Edge to pakiet wykonywalny wdrażany jako kontener. W tej sekcji wdrożymy wstępnie skompilowany moduł z [sekcji Moduły usługi IoT Edge w portalu Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Moduł ten generuje symulowane dane telemetryczne dla urządzenia usługi IoT Edge.

1. W witrynie [Azure Portal](https://portal.azure.com) wprowadź w polu wyszukiwania tekst **Simulated Temperature Sensor** i otwórz wynik z portalu Marketplace.

   ![Termin „Simulated Temperature Sensor” w polu wyszukiwania w witrynie Azure Portal](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. Wybierz urządzenie usługi IoT Edge, które ma odebrać ten moduł. W sekcji **Urządzenia docelowe dla modułu usługi IoT Edge** podaj następujące informacje:

   1. **Subskrypcja**: wybierz subskrypcję, która zawiera centrum IoT, którego używasz.

   2. **Centrum IoT**: wybierz nazwę centrum IoT, którego używasz.

   3. **Nazwa urządzenia usługi IoT Edge**: jeśli we wcześniejszej części tego przewodnika Szybki start użyto sugerowanej nazwy urządzenia, wpisz **myEdgeDevice**. Możesz też wybrać opcję **Znajdź urządzenie**, aby wybrać urządzenie z listy urządzeń w centrum IoT. 
   
   4. Wybierz pozycję **Utwórz**.

3. Po wybraniu modułu usługi IoT Edge w witrynie Microsoft Azure Marketplace oraz wybraniu urządzenia usługi IoT Edge, które ma odebrać moduł, nastąpi przekierowanie do trzyetapowego kreatora, który ułatwia dokładne określenie sposobu wdrożenia modułu. Zwróć uwagę, że w kroku kreatora **Dodawanie modułów** moduł **SimulatedTemperatureSensor** jest wypełniany automatycznie. W samouczkach będziesz używać tej strony, by dodawać do wdrożenia dodatkowe moduły. Na potrzeby tego przewodnika Szybki start wdrożysz ten pojedynczy moduł. Wybierz **Dalej**, aby przejść do następnego kroku w kreatorze.

4. W kroku kreatora **Określanie tras** należy zdefiniować sposób przekazywania komunikatów między modułami i usługą IoT Hub. W przypadku tego przewodnika Szybki start wszystkie komunikaty ze wszystkich modułów mają trafiać do usługi IoT Hub (`$upstream`). Jeśli odpowiednie wartości nie zostały automatycznie uzupełnione, dodaj następujący kod, a następnie naciśnij przycisk **Dalej**:

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

5. W kroku kreatora **Przegląd wdrożenia** możesz wyświetlić podgląd pliku JSON określającego wszystkie moduły, które zostaną wdrożone na urządzeniu usługi IoT Edge. Zauważ, że uwzględniony został moduł **SimulatedTemperatureSensor** oraz dwa dodatkowe moduły systemowe o nazwach **edgeAgent** i **edgeHub**. Po wykonaniu przeglądu wybierz opcję **Prześlij**.

   Podczas przesłania nowego wdrożenia na urządzenie usługi IoT Edge do urządzenia nie jest wypychane żadne powiadomienie. Nie jest to konieczne, ponieważ urządzenie regularnie wysyła do usługi IoT Hub zapytania w celu odebrania wszelkich nowych instrukcji. Po wykryciu nowych informacji dotyczących wdrożenia urządzenie użyje ich do ściągnięcia obrazów modułów z chmury i zacznie uruchamiać moduły lokalnie. Ten proces może potrwać kilka minut. 

6. Po przesłaniu szczegółów wdrożenia modułu kreator przekieruje Cię z powrotem na stronę **IoT Edge** Twojego centrum IoT. Wybierz urządzenie z listy urządzeń usługi IoT Edge, aby wyświetlić jego szczegóły. 

7. Na stronie szczegółów urządzenia przewiń w dół do sekcji **Moduły**. Powinny zostać wyświetlone trzy moduły: $edgeAgent $edgeHub i SimulatedTemperatureSensor. Jeśli co najmniej jeden z modułów jest wyświetlany jako określony we wdrożeniu, ale nie jako zgłaszany przez urządzenie, oznacza to, że urządzenie usługi IoT Edge nadal go uruchamia. Poczekaj chwilę, a następnie wybierz pozycję **Odśwież** w górnej części strony. 

   ![Wyświetlanie modułu SimulatedTemperatureSensor na liście wdrożonych modułów](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
