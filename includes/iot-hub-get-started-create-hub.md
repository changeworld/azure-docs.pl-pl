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
ms.openlocfilehash: d586ca18953b12045fbbaa4a656d78a7192eb88e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371239"
---
## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT
Utwórz centrum IoT, z którym połączy się symulowana aplikacja urządzenia. Poniższe kroki przedstawiają sposób wykonania tego zadania przy użyciu witryny Azure Portal.

1. Zaloguj się w witrynie [Azure Portal][lnk-portal].

1. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **IoT Hub**.
   
    ![Pasek dostępu witryny Azure Portal][1]

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

1. Gdy nowe centrum IoT Hub będzie gotowe, kliknij jego kafelek w witrynie Azure Portal, aby otworzyć jego okno właściwości. Teraz, po utworzeniu centrum IoT Hub, zlokalizuj ważne informacje używane do łączenia urządzeń i aplikacji z centrum IoT Hub. Kliknij pozycję **Zasady dostępu współużytkowanego**.
   
1. W obszarze **Zasady dostępu współużytkowanego** wybierz zasady **iothubowner**. Skopiuj wartość **Parametry połączenia — klucz podstawowy** centrum IoT Hub do użycia w przyszłości. Aby uzyskać więcej informacji, zobacz sekcję [Access control][lnk-access-control] (Kontrola dostępu) w temacie „IoT Hub developer guide” (Przewodnik deweloperów usługi IoT Hub).
   
    ![Zasady dostępu współużytkowanego][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
