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
ms.openlocfilehash: 08afdcf91499fdb6f2da6e9ccc82313286f5c964
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43111213"
---
## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub
Utwórz centrum IoT, z którym połączy się symulowana aplikacja urządzenia. Poniższe kroki przedstawiają sposób wykonania tego zadania przy użyciu witryny Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **IoT Hub**.
   
    ![Pasek dostępu witryny Azure Portal](./media/iot-hub-get-started-create-hub/create-iot-hub1.png)

3. W okienku **IoT Hub** wprowadź następujące informacje dotyczące centrum IoT Hub:

   * **Subskrypcja**: wybierz subskrypcję, która ma zostać użyta do utworzenia tego centrum IoT Hub.

   * **Grupa zasobów**: utwórz grupę zasobów do hostowania centrum IoT Hub lub użyj istniejącej grupy zasobów. Więcej informacji można znaleźć w temacie [Używanie grup zasobów do zarządzania zasobami platformy Azure](../articles/azure-resource-manager/resource-group-portal.md).

   * **Region**: wybierz lokalizację znajdującą się najbliżej Ciebie.

   * **Nazwa**: utwórz nazwę centrum IoT Hub. Jeśli wprowadzona nazwa jest dostępna, zostanie wyświetlony zielony znacznik wyboru.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Okno podstawowych informacji o centrum IoT Hub](./media/iot-hub-get-started-create-hub/create-iot-hub2.png)

4. Wybierz pozycję **Dalej: rozmiar i skala**, aby kontynuować tworzenie centrum IoT Hub. 

5. Wybierz wartość pozycji **Warstwa cenowa i warstwa skali**. Na potrzeby tego artykułu wybierz warstwę **F1 — Bezpłatna**, jeśli jest ona nadal dostępna w ramach subskrypcji. Aby uzyskać więcej informacji, zobacz [Ceny i warstwa skalowania](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Okno rozmiaru i skali centrum IoT Hub](./media/iot-hub-get-started-create-hub/create-iot-hub3.png)

6. Wybierz pozycję **Przegląd + utwórz**.

7. Przejrzyj informacje o centrum IoT Hub, a następnie kliknij pozycję **Utwórz**. Proces tworzenia centrum IoT Hub może potrwać kilka minut. Postępy możesz monitorować w okienku **Powiadomienia**.

8. Gdy nowe centrum IoT Hub będzie gotowe, kliknij jego kafelek w witrynie Azure Portal, aby otworzyć jego okno właściwości. Teraz, po utworzeniu centrum IoT Hub, zlokalizuj ważne informacje używane do łączenia urządzeń i aplikacji z centrum IoT Hub. Kliknij pozycję **Zasady dostępu współużytkowanego**.
   
9. W obszarze **Zasady dostępu współużytkowanego** wybierz zasady **iothubowner**. Skopiuj wartość **Parametry połączenia — klucz podstawowy** centrum IoT Hub do użycia w przyszłości. Aby uzyskać więcej informacji, zobacz sekcję [Access control (Kontrola dostępu)](../articles/iot-hub/iot-hub-devguide-security.md) w temacie „IoT Hub developer guide” (Przewodnik deweloperów usługi IoT Hub).
   
    ![Zasady dostępu współużytkowanego](./media/iot-hub-get-started-create-hub/create-iot-hub5.png)