---
title: dołączanie pliku (strumienie urządzeń — wersja zapoznawcza)
description: dołączanie pliku (strumienie urządzeń — wersja zapoznawcza)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: ede897054a6cbef254c06bd1d810b933ec09016a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158655"
---
W tej sekcji opisano, jak utworzyć centrum IoT przy użyciu witryny [Azure Portal](https://portal.azure.com).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz pozycję +**Utwórz zasób**, następnie pozycję **Internet rzeczy**.

3. Kliknij pozycję **Iot Hub** na liście po prawej stronie. Pojawi się pierwszy ekran do tworzenia centrum IoT.

   ![Zrzut ekranu przedstawiający tworzenie centrum w witrynie Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Wypełnij następujące pola:

   **Subskrypcja**: wybierz subskrypcję, która ma być używana dla centrum IoT.

   **Grupa zasobów**: istnieje możliwość utworzenia nowej lub użycia istniejącej grupy zasobów. Aby utworzyć nową, kliknij pozycję **Utwórz nową** i wprowadź nazwę, której chcesz używać. Aby użyć istniejącej grupy zasobów, kliknij pozycję **Użyj istniejącej**, a następnie wybierz grupę zasobów z listy rozwijanej. Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami zasobów usługi Azure Resource Manager](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   **Region**: jest to region, w którym ma znajdować się centrum. Wybierz region, który obsługuje usługi IoT Hub urządzenia strumieni wersji zapoznawczej, środkowe stany USA lub centralna stany USA — EUAP.

   **Nazwa centrum IoT**: podaj nazwę dla centrum IoT Hub. Ta nazwa musi być unikatowa w skali globalnej. Jeśli wprowadzona nazwa jest dostępna, zostanie wyświetlony zielony znacznik wyboru.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. Kliknij pozycję **Dalej: rozmiar i skala**, aby kontynuować tworzenie centrum IoT.

   ![Zrzut ekranu przedstawiający ustawianie rozmiaru i skali dla nowego centrum IoT przy użyciu witryny Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   Na tym ekranie możesz przyjąć wartości domyślne, klikając pozycję **Przejrzyj i utwórz** u dołu.

   **Warstwa cenowa i warstwa skali**: wybierz warstwę Standardowa (S1, S2, S3) lub Bezpłatna (F1). Podstawą tego wyboru może być także liczba urządzeń i rozmiar obciążeń innych niż przesyłania strumieniowego (np. komunikatów telemetrii) oczekiwanych dla centrum. Na przykład warstwa Bezpłatna służy do testowania i oceny. Obsługuje ona 500 urządzeń połączonych z centrum IoT i do 8000 komunikatów dziennie. Każda subskrypcja platformy Azure umożliwia utworzenie jednego centrum IoT w ramach warstwy Bezpłatna. 

   **Jednostki usługi IoT Hub**: ten wybór zależy od obciążeń innych niż przesyłania strumieniowego oczekiwanych dla centrum — na tę chwilę można wybrać wartość 1.

   Aby uzyskać szczegółowe informacje na temat innych opcji warstw, zobacz [Choosing the right IoT Hub tier](../articles/iot-hub/iot-hub-scaling.md) (Wybieranie właściwej warstwy usługi IoT Hub).

5. Kliknij pozycję **Przejrzyj i utwórz**, aby przejrzeć wybrane opcje. Zostanie wyświetlony ekran podobny do następującego.

   ![Zrzut ekranu z przeglądem informacji dotyczących tworzenia nowego centrum IoT](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

6. Kliknij przycisk **Utwórz**, aby utworzyć nowe centrum IoT. Utworzenie centrum zajmuje kilka minut.
