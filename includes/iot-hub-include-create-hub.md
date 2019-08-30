---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e01bb13b904aaf8671512daa3ef3cbad182f01e3
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70161961"
---
W tej sekcji opisano, jak utworzyć centrum IoT przy użyciu witryny [Azure Portal](https://portal.azure.com).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób**, a następnie wprowadź *IoT Hub* w polu **Wyszukaj w witrynie Marketplace** .

1. Wybierz **IoT Hub** z wyników wyszukiwania, a następnie wybierz pozycję **Utwórz**.

1. Na karcie **podstawowe** Wypełnij pola w następujący sposób:

   - **Subskrypcja**: Wybierz subskrypcję, która ma być używana przez centrum.

   - **Grupa zasobów**: Wybierz grupę zasobów lub Utwórz nową. Aby utworzyć nowy, wybierz pozycję **Utwórz nową** i wprowadź nazwę, której chcesz użyć. Aby użyć istniejącej grupy zasobów, wybierz tę grupę zasobów. Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami zasobów usługi Azure Resource Manager](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   - **Region**: Wybierz region, w którym ma się znajdować centrum. Wybierz lokalizację znajdującą się najbliżej siebie.

   - **Nazwa centrum IoT**: Wprowadź nazwę centrum. Ta nazwa musi być unikatowa w skali globalnej. Jeśli wprowadzona nazwa jest dostępna, zostanie wyświetlony zielony znacznik wyboru.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Tworzenie centrum w Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics-vs2019.png)

1. Wybierz opcję **Dalej: Rozmiar i skalowanie** , aby kontynuować tworzenie centrum.

   ![Ustawianie rozmiaru i skali nowego centrum przy użyciu Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

    Ten ekran umożliwia ustawienie następujących wartości:

    - **Warstwa cenowa i warstwa skali**: Wybrana warstwa. Możesz wybrać jedną z wielu warstw, w zależności od liczby potrzebnych funkcji i liczby komunikatów wysyłanych przez Twoje rozwiązanie dziennie. Warstwa Bezpłatna służy do testowania i oceny. Umożliwia podłączenie 500 urządzeń do centrum oraz do 8 000 komunikatów dziennie. Każda subskrypcja platformy Azure umożliwia utworzenie jednego centrum IoT w ramach warstwy Bezpłatna.

    - **Jednostki usługi IoT Hub**: liczba dozwolonych komunikatów na jednostkę dziennie zależy od warstwy cenowej centrum. Na przykład jeśli chcesz, aby koncentrator obsługiwał ruch przychodzący z 700 000 komunikatów, wybierz dwie jednostki warstwy S1.
    Aby uzyskać szczegółowe informacje na temat innych opcji warstw, zobacz [Choosing the right IoT Hub tier](../articles/iot-hub/iot-hub-scaling.md) (Wybieranie właściwej warstwy usługi IoT Hub).

    - **Ustawienia zaawansowane —** **partycje urządzenia-chmura:**  >  ta właściwość dotyczy relacji między komunikatami urządzenie-chmura, a liczbą czytników jednocześnie odczytujących te komunikaty. Większość centrów potrzebuje tylko czterech partycji.

1. Na potrzeby tego artykułu Zaakceptuj opcje domyślne, a następnie wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć wybrane opcje. Zostanie wyświetlony ekran podobny do następującego.

   ![Przejrzyj informacje dotyczące tworzenia nowego centrum](./media/iot-hub-include-create-hub/iot-hub-create-review-vs2019.png)

1. Wybierz pozycję **Utwórz** , aby utworzyć nowe centrum. Utworzenie centrum zajmuje kilka minut.
