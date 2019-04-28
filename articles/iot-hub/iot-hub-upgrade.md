---
title: Uaktualnianie usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Zmień ceny i warstwa skalowania dla usługi IoT Hub uzyskać więcej możliwości zarządzania dotyczące obsługi komunikatów i urządzenia.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440237"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Jak uaktualnić Centrum IoT hub

Wraz z rozwojem rozwiązania IoT Azure IoT Hub jest gotowy pomóc Ci skalowanie w górę. Usługa Azure IoT Hub oferuje dwie warstwy, basic (B) i standardowa (S), aby pomieścić klientów, które mają być używać różnych funkcji. W każdej warstwie są trzy rozmiary (1, 2 i 3), które określają liczbę wiadomości, które mogą być wysyłane codziennie.

Gdy masz większą liczbę urządzeń i potrzebujesz więcej możliwości, istnieją trzy sposoby, aby dostosować do własnych potrzeb Centrum IoT hub:

* Dodaj jednostki w ramach usługi IoT hub. Na przykład każdej dodatkowej jednostki w Centrum B1 IoT umożliwia dodatkowe 400 000 komunikatów dziennie.

* Zmień rozmiar usługi IoT hub. Na przykład migrować warstwy B1 do warstwy B2, aby zwiększyć liczbę wiadomości, które każda jednostka może obsługiwać dziennie.

* Uaktualnij do wyższego poziomu. Na przykład uaktualnić z warstwy B1 warstwę S1, aby uzyskać dostęp do zaawansowanych funkcji o tej samej pojemności obsługi komunikatów.

Te zmiany może wystąpić bez przerywania istniejące operacje.

Jeśli chcesz obniżyć wersję usługi IoT hub, można usunąć jednostki i zmniejszyć rozmiar usługi IoT hub, ale nie można obniżyć do niższej warstwy. Na przykład można przenieść z warstwy S2 do S1 warstwy, ale nie z poziomu S2 do warstwy B1. Tylko jeden typ [Iot Hub w wersji](https://azure.microsoft.com/pricing/details/iot-hub/) w obrębie warstwy można wybrać dla każdej usługi IoT Hub. Na przykład można utworzyć usługi IoT Hub przy użyciu wielu jednostek S1, ale nie kombinację jednostek z różnych wersji, takich jak S1 i B3 lub warstwy S1 i S2.

Te przykłady są przeznaczone do pomóc Ci zrozumieć, jak dostosować zmiany rozwiązań usługi IoT hub. Aby uzyskać szczegółowe informacje o możliwości poszczególnych warstw, należy zawsze odwołasz się do [cennika usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="upgrade-your-existing-iot-hub"></a>Uaktualnianie istniejącego Centrum IoT hub

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i przejdź do Centrum IoT hub.

2. Wybierz **ceny i Skala**.

   ![Ceny i skala](./media/iot-hub-upgrade/pricing-scale.png)

3. Aby zmienić warstwę Centrum, wybierz **ceny i warstwa skalowania**. Wybierz nową warstwę, a następnie kliknij przycisk **wybierz**.

   ![Warstwa cenowa i warstwa skali](./media/iot-hub-upgrade/select-tier.png)

4. Aby zmienić liczbę jednostek w Centrum, wprowadź nową wartość w obszarze **jednostek usługi IoT Hub**.

5. Wybierz **Zapisz** Aby zapisać zmiany.

Teraz jest korygowane w Twoim Centrum IoT hub, a konfiguracje nie ulegną zmianie.

Limit maksymalny partycji dla warstwy podstawowa usługi IoT Hub i IoT Hub w warstwie standardowa wynosi 32. Większość centrów IoT potrzebuje tylko 4 partycjami. Limit partycji jest wybierany, gdy usługa IoT Hub zostanie utworzona i odnosi się komunikaty urządzenie chmura z liczbą jednoczesnych czytników tych komunikatów. Ta wartość zmienia się podczas migracji z warstwy podstawowa do warstwy standardowa.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej [jak wybrać właściwą warstwę usługi IoT Hub](iot-hub-scaling.md).