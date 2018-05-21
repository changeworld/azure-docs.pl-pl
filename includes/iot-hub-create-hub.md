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
ms.openlocfilehash: 9a29406b92f7d2e2ce8171974efb5a264e112d1d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
1. Zaloguj się w witrynie [Azure Portal][lnk-portal].
1. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **IoT Hub**.
   
    ![Zrzut ekranu Azure nawigacji w portalu do Centrum IoT][1]

1. W okienku **IoT Hub** wprowadź następujące informacje dotyczące centrum IoT Hub:

   * **Subskrypcja**: Wybierz subskrypcję, która ma być używana do tworzenia tego Centrum IoT.

   * **Grupa zasobów**: utwórz grupę zasobów do hostowania centrum IoT Hub lub użyj istniejącej grupy zasobów. Aby uzyskać więcej informacji, zobacz [używanie grup zasobów do zarządzania zasobami Azure][lnk-resource-groups].

   * **Region**: Wybierz najbliższą lokalizację do Ciebie.

   * **Nazwa**: utwórz nazwę centrum IoT Hub. Jeśli wprowadzona nazwa jest dostępny, zostanie wyświetlony zielony znacznik wyboru.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Centrum IoT podstawy okna][2]

2. Wybierz **dalej: rozmiaru i skali** aby kontynuować tworzenie Centrum IoT. 

3. Wybierz użytkownika **warstwa cenowa i skali**. W tym artykule, wybierz **F1 – wolnego** warstwy, jeśli jest on nadal dostępny w ramach subskrypcji. Aby uzyskać więcej informacji, zobacz [Ceny i warstwa skalowania][lnk-pricing].

   ![Centrum IoT okna rozmiaru i skali][3]

4. Wybierz **przeglądu + Utwórz**.

1. Przejrzyj informacje o Centrum IoT, a następnie kliknij przycisk **Utwórz**. Proces tworzenia centrum IoT Hub może potrwać kilka minut. Postępy możesz monitorować w okienku **Powiadomienia**.
<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md