---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 63acf0297a694ff442d56e67d52fd9b4e49f812d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008590"
---
Pierwszym krokiem jest utworzenie centrum IoT w subskrypcji za pomocą witryny Azure Portal. Centrum IoT umożliwia pozyskiwanie dużych ilości danych telemetrycznych z wielu urządzeń do chmury. Następnie dzięki centrum jedna lub więcej usług zaplecza uruchomionych w chmurze może odczytywać i przetwarzać te dane telemetryczne.

1. Zaloguj się w [Portalu Azure](http://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **IoT Hub**.

    ![Wybierz, aby zainstalować usługę IoT Hub][1]

1. W okienku **IoT Hub** wprowadź następujące informacje dotyczące centrum IoT Hub:

   * **Subskrypcja**: wybierz subskrypcję, która ma zostać użyta do utworzenia tego centrum IoT Hub.
   * **Grupa zasobów**: utwórz grupę zasobów na potrzeby centrum IoT Hub lub użyj istniejącej grupy zasobów. Dzięki umieszczeniu wszystkich powiązanych zasobów w jednej grupie, na przykład **TestResources**, można nimi zarządzać jednocześnie. Na przykład usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w tej grupie. Aby uzyskać więcej informacji, zobacz [Używanie grup zasobów do zarządzania zasobami platformy Azure][lnk-resource-groups].
   * **Region**: wybierz lokalizację znajdującą się najbliżej Twoich urządzeń.
   * **Nazwa**: utwórz unikatową nazwę centrum IoT Hub. Jeśli wprowadzona nazwa jest dostępna, zostanie wyświetlony zielony znacznik wyboru.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Okno podstawowych informacji o centrum IoT Hub][2]

2. Wybierz pozycję **Dalej: rozmiar i skala**, aby kontynuować tworzenie centrum IoT Hub. 

3. Wybierz wartość pozycji **Warstwa cenowa i warstwa skali**. Na potrzeby tego artykułu wybierz warstwę **F1 — Bezpłatna**, jeśli jest ona nadal dostępna w ramach subskrypcji. Aby uzyskać więcej informacji, zobacz [Ceny i warstwa skalowania][lnk-pricing].

   ![Okno rozmiaru i skali centrum IoT Hub][3]

4. Wybierz pozycję **Przegląd + utwórz**.

1. Przejrzyj informacje o centrum IoT Hub, a następnie kliknij pozycję **Utwórz**. Proces tworzenia centrum IoT Hub może potrwać kilka minut. Postępy możesz monitorować w okienku **Powiadomienia**.


<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md