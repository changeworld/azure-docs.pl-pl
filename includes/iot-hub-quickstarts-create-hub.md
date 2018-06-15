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
ms.openlocfilehash: 7987ee8c9a127c7a41ec2cd1f726450c771d527f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34666970"
---
Pierwszym krokiem jest utworzenie centrum IoT w subskrypcji za pomocą witryny Azure Portal. Centrum IoT umożliwia pozyskiwanie dużych ilości danych telemetrycznych z wielu urządzeń do chmury. Następnie dzięki centrum jedna lub więcej usług zaplecza uruchomionych w chmurze może odczytywać i przetwarzać te dane telemetryczne.

1. Zaloguj się w [Portalu Azure](http://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **IoT Hub**.

    ![Wybierz, aby zainstalować usługę IoT Hub][1]

1. W okienku **IoT Hub** wprowadź następujące informacje dotyczące centrum IoT Hub:

   * **Subskrypcja**: wybierz subskrypcję, która ma zostać użyta do utworzenia tego centrum IoT Hub.

   * **Grupa zasobów**: utwórz grupę zasobów do hostowania centrum IoT Hub lub użyj istniejącej grupy zasobów. Aby uzyskać więcej informacji, zobacz [Używanie grup zasobów do zarządzania zasobami platformy Azure][lnk-resource-groups].

   * **Region**: wybierz lokalizację znajdującą się najbliżej Ciebie.

   * **Nazwa**: utwórz nazwę centrum IoT Hub. Jeśli wprowadzona nazwa jest dostępna, zostanie wyświetlony zielony znacznik wyboru.

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