---
title: Uaktualnianie usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Zmień warstwę cen i skalowania usługi IoT Hub, aby uzyskać więcej funkcji obsługi wiadomości i zarządzania urządzeniami.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61440237"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Jak uaktualnić centrum IoT

Wraz z rozwojem rozwiązania IoT usługa Azure IoT Hub jest gotowa do skalowania w górę. Usługa Azure IoT Hub oferuje dwie warstwy, podstawowe (B) i standardowe (S), aby pomieścić klientów, którzy chcą korzystać z różnych funkcji. W każdej warstwie są trzy rozmiary (1, 2 i 3), które określają liczbę wiadomości, które mogą być wysyłane każdego dnia.

Jeśli masz więcej urządzeń i potrzebujesz więcej funkcji, możesz dostosować centrum IoT hub do własnych potrzeb:

* Dodaj jednostki w centrum IoT hub. Na przykład każda dodatkowa jednostka w centrum IoT B1 umożliwia dodatkowe 400 000 wiadomości dziennie.

* Zmień rozmiar centrum IoT Hub. Na przykład migracji z warstwy B1 do warstwy B2, aby zwiększyć liczbę komunikatów, które każda jednostka może obsługiwać dziennie.

* Uaktualnij do wyższej warstwy. Na przykład uaktualnić z warstwy B1 do warstwy S1, aby uzyskać dostęp do zaawansowanych funkcji o tej samej pojemności obsługi wiadomości.

Wszystkie te zmiany mogą wystąpić bez przerywania istniejących operacji.

Jeśli chcesz obniżyć poziom usługi IoT Hub, możesz usunąć jednostki i zmniejszyć rozmiar centrum IoT hub, ale nie można obniżyć do niższej warstwy. Na przykład można przejść z warstwy S2 do warstwy S1, ale nie z warstwy S2 do poziomu B1. Tylko jeden typ [wersji Iot Hub](https://azure.microsoft.com/pricing/details/iot-hub/) w warstwie można wybrać dla usługi IoT Hub. Na przykład można utworzyć Centrum IoT z wieloma jednostkami S1, ale nie z mieszanką jednostek z różnych wersji, takich jak S1 i B3 lub S1 i S2.

Te przykłady mają na celu pomóc zrozumieć, jak dostosować centrum IoT hub w miarę zmian rozwiązania. Aby uzyskać szczegółowe informacje na temat możliwości każdej warstwy, należy zawsze zapoznać się z [cennikiem usługi Azure IoT Hub.](https://azure.microsoft.com/pricing/details/iot-hub/)

## <a name="upgrade-your-existing-iot-hub"></a>Uaktualnianie istniejącego centrum IoT

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i przejdź do centrum IoT Hub.

2. Wybierz **Cennik i skalę**.

   ![Ceny i skala](./media/iot-hub-upgrade/pricing-scale.png)

3. Aby zmienić warstwę centrum, wybierz **pozycję Cennik i warstwa skalowania**. Wybierz nową warstwę, a następnie kliknij **przycisk Wybierz**.

   ![Warstwa cenowa i warstwa skali](./media/iot-hub-upgrade/select-tier.png)

4. Aby zmienić liczbę jednostek w centrum, wprowadź nową wartość w obszarze **Jednostki Usługi IoT Hub**.

5. Wybierz przycisk **Zapisz**, aby zapisać zmiany.

Centrum IoT jest teraz dostosowane, a konfiguracje pozostają niezmienione.

Maksymalny limit partycji dla centrum IoT warstwy podstawowej i centrum IoT w warstwie standardowej wynosi 32. Większość centrów IoT potrzebuje tylko 4 partycji. Limit partycji jest wybierany podczas tworzenia usługi IoT Hub i odnosi się do komunikatów urządzenia do chmury do liczby jednoczesnych czytników tych wiadomości. Ta wartość pozostaje niezmieniona podczas migracji z warstwy podstawowej do warstwy standardowej.

## <a name="next-steps"></a>Następne kroki

Uzyskaj więcej informacji o [tym, jak wybrać odpowiednią warstwę Usługi IoT Hub](iot-hub-scaling.md).