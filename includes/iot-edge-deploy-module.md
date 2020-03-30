---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/31/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: abbde0cb8137650faad563248c8fafe7dc0f469a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75564737"
---
Jedną z najważniejszych funkcji usługi Azure IoT Edge jest możliwość wdrażania kodu na urządzeniach usługi IoT Edge z poziomu chmury. **Moduły usługi IoT Edge** to pakiety plików wykonywalnych implementowane jako kontenery. W tej sekcji można wdrożyć wstępnie utworzony moduł z [sekcji Moduły usługi IoT Edge w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) bezpośrednio z usługi Azure IoT Hub.

Moduł, który wdrożysz w tej sekcji, symuluje czujnik i wysyła wygenerowane dane. Ten moduł jest przydatny, gdy rozpoczyna się pracę z usługą IoT Edge, ponieważ symulowane dane można wykorzystać przy programowaniu i testowaniu. Jeśli chcesz zobaczyć, co dokładnie robi ten moduł, możesz wyświetlić [kod źródłowy symulowanego czujnika temperatury](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Aby wdrożyć swój pierwszy moduł z witryny Azure Marketplace, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do centrum IoT Hub.

1. Z menu w lewym okienku w obszarze **Automatyczne zarządzanie urządzeniami**wybierz pozycję **IoT Edge**.

1. Kliknij identyfikator urządzenia docelowego z listy urządzeń.

1. Na górnym pasku wybierz pozycję **Ustaw moduły**.

1. W sekcji **Moduły krawędzi IoT** na stronie kliknij przycisk **Dodaj**.

1. Z menu rozwijanego wybierz pozycję **Moduł Marketplace**.

   ![Termin „Simulated Temperature Sensor” w polu wyszukiwania w witrynie Azure Portal](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

1. W portalu **IoT Edge Module Marketplace**wyszukaj hasło "Symulowany czujnik temperatury" i wybierz ten moduł.

1. Należy zauważyć, że moduł SimulatedTemperatureSensor jest automatycznie wypełniany. W samouczkach ta strona pozwala dodawać do wdrożenia dodatkowe moduły. Na potrzeby tego przewodnika Szybki start wdrożysz tylko ten jeden moduł. Poświadczenia nie są wymagane, ponieważ jest publiczny.

   ![Ustawianie modułów na urządzeniu](./media/iot-edge-deploy-module/set-modules-on-device.png)

   Wybierz **dalej: Trasy,** aby przejść do następnego kroku kreatora.

1. Na karcie **Trasy** kreatora można zdefiniować sposób przekazywania wiadomości między modułami a centrum IoT Hub. Wiadomości są tworzone przy użyciu par nazwy/wartości. W przypadku tego przewodnika Szybki start wszystkie komunikaty ze wszystkich modułów mają trafiać do usługi IoT Hub (`$upstream`). Jeśli nie jest automatycznie wypełniany, dodaj następujący kod **dla wartości** **dla nazwy:** `upstream`

   ```sql
    FROM /messages/* INTO $upstream
   ```

   Wybierz **dalej: Przejrzyj + utwórz,** aby przejść do następnego kroku kreatora.

1. Na karcie **Recenzja + tworzenie** kreatora można wyświetlić podgląd pliku JSON, który definiuje wszystkie moduły, które są wdrażane na urządzeniu IoT Edge. Zauważ, że uwzględniony został moduł **SimulatedTemperatureSensor** oraz dwa dodatkowe moduły systemowe o nazwach **edgeAgent** i **edgeHub**. Wybierz **pozycję Utwórz** po zakończeniu przeglądania.

   Podczas przesłania nowego wdrożenia na urządzenie usługi IoT Edge do urządzenia nie jest wypychane żadne powiadomienie. Nie jest to konieczne, ponieważ urządzenie regularnie wysyła do usługi IoT Hub zapytania w celu odebrania wszelkich nowych instrukcji. Jeśli urządzenie znajdzie zaktualizowany manifest wdrażania, użyje informacji o nowym wdrożeniu, aby ściągnąć obrazy modułów z chmury, a następnie zacznie uruchamiać moduły lokalnie. Ten proces może potrwać kilka minut.

1. Po utworzeniu szczegółów wdrożenia modułu kreator zwraca użytkownika do strony **Usługi IoT Edge** w centrum IoT hub. Wybierz urządzenie z listy urządzeń usługi IoT Edge, aby wyświetlić jego szczegóły.

1. Na stronie szczegółów urządzenia przewiń w dół do karty **Moduły.** Na liście powinny znajdować się trzy moduły: $edgeAgent, $edgeHub i SymulowanyTemperatureSensor. Jeśli co najmniej jeden z modułów jest wyświetlany jako określony we wdrożeniu, ale nie jako zgłaszany przez urządzenie, to urządzenie usługi IoT Edge nadal go uruchamia. Poczekaj chwilę, a następnie wybierz pozycję **Odśwież** w górnej części strony.

   ![Wyświetlanie modułu SimulatedTemperatureSensor na liście wdrożonych modułów](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
