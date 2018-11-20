---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b1f1f8144ec5b65c21c5ddcecd278a018260047c
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516027"
---
W tej sekcji opisano, jak utworzyć centrum IoT przy użyciu witryny [Azure Portal](https://portal.azure.com).

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). 

2. Wybierz pozycję +**Utwórz zasób**, następnie pozycję **Internet rzeczy**.

3. Kliknij pozycję **Iot Hub** na liście po prawej stronie. Pojawi się pierwszy ekran do tworzenia centrum IoT.

   ![Zrzut ekranu przedstawiający tworzenie centrum w witrynie Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

   Wypełnij pola.

   **Subskrypcja**: wybierz subskrypcję, która ma być używana z centrum IoT.

   **Grupa zasobów**: możesz utworzyć nową grupę zasobów lub użyć istniejącej. Aby utworzyć nową, kliknij pozycję **Utwórz nową** i wprowadź nazwę, której chcesz używać. Aby użyć istniejącej grupy zasobów, kliknij pozycję **Użyj istniejącej**, a następnie wybierz grupę zasobów z listy rozwijanej. Więcej informacji można znaleźć w temacie [Używanie grup zasobów do zarządzania zasobami platformy Azure](../articles/azure-resource-manager/resource-group-portal.md).

   **Region**: jest to region, w którym ma znajdować się centrum. Wybierz najbliższą swojej lokalizację z listy rozwijanej.

   **Nazwa centrum IoT**: podaj nazwę dla tego centrum IoT. Ta nazwa musi być unikatowa w skali globalnej. Jeśli wprowadzona nazwa jest dostępna, zostanie wyświetlony zielony znacznik wyboru.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. Kliknij pozycję **Dalej: rozmiar i skala**, aby kontynuować tworzenie centrum IoT Hub.

   ![Zrzut ekranu przedstawiający ustawianie rozmiaru i skali dla nowego centrum IoT przy użyciu witryny Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

   Na tym ekranie możesz przyjąć wartości domyślne, klikając pozycję **Przejrzyj i utwórz** u dołu. 

   **Warstwa cenowa i warstwa skali**: do wyboru jest kilka warstw, w zależności od tego, ilu funkcji chcesz używać oraz ile komunikatów dziennie chcesz wysyłać za pośrednictwem rozwiązania. Warstwa Bezpłatna służy do testowania i oceny. Obsługuje ona 500 urządzeń połączonych z centrum IoT i do 8000 komunikatów dziennie. Każda subskrypcja platformy Azure umożliwia utworzenie jednego centrum IoT w ramach warstwy Bezpłatna. 

   **Jednostki centrum IoT**: liczba dozwolonych komunikatów na jednostkę dziennie zależy od warstwy cenowej centrum. Jeśli na przykład centrum IoT ma obsługiwać 700 000 komunikatów przychodzących, należy wybrać dwie jednostki warstwy S1.

   Aby uzyskać szczegółowe informacje na temat innych opcji warstw, zobacz [Choosing the right IoT Hub tier](../articles/iot-hub/iot-hub-scaling.md) (Wybieranie właściwej warstwy usługi IoT Hub).

   **Zaawansowane/Partycje na potrzeby komunikacji między urządzeniem i chmurą**: ta właściwość dotyczy relacji między komunikatami urządzenie-chmura, a liczbą czytników jednocześnie odczytujących te komunikaty. W przypadku większości centrów IoT wystarczą tylko cztery partycje. 

5. Kliknij pozycję **Przejrzyj i utwórz**, aby przejrzeć wybrane opcje. Zostanie wyświetlony ekran podobny do następującego.

   ![Zrzut ekranu z przeglądem informacji dotyczących tworzenia nowego centrum IoT](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

6. Kliknij przycisk **Utwórz**, aby utworzyć nowe centrum IoT. Utworzenie centrum zajmuje kilka minut.
