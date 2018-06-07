---
title: Uaktualnij Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Zmień warstwę cenową i wartę skali Centrum IoT uzyskać więcej możliwości zarządzania obsługi wiadomości i urządzeniem.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: 472115f166adc5385b6f46b2f3ac5ef75a6cde92
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637280"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Jak uaktualnić Centrum IoT

Wraz z rozwojem rozwiązania IoT, Centrum IoT Azure jest gotowy ułatwić skalowanie w górę. Centrum IoT Azure oferuje dwie warstwy, basic (B) i standard (S), aby pomieścić klientów, których chcesz użyć różnych funkcji. W każdej warstwie są trzy rozmiary (1, 2 i 3), które określić liczbę wiadomości, które mogą być wysyłane do każdego dnia. 

Gdy ma zbyt wiele urządzeń i wymagają więcej możliwości, istnieją trzy sposoby dostosować do własnych potrzeb Centrum IoT:

* Dodaj jednostki w Centrum IoT. Na przykład każdej dodatkowej jednostki w Centrum B1 IoT umożliwia dodatkowe 400 000 wiadomości na dzień. 
* Zmień rozmiar Centrum IoT. Na przykład migrować warstwy B1 do warstwy B2, aby zwiększyć liczbę komunikatów obsługiwanych przez każdą jednostką na dzień.
* Uaktualnij do wyższego poziomu. Na przykład uaktualnić warstwy B1 do warstwy S1 dla tej samej możliwości obsługi komunikatów, lecz z zaawansowanych funkcji, które są dostępne w warstwie standardowa.

Te zmiany mogą być bez przerywania istniejących.

Jeśli chcesz obniżyć Centrum IoT, można usunąć jednostki i zmniejszyć rozmiar Centrum IoT. Jednak nie można obniżyć do dolnej warstwy. Na przykład można przenosić z warstwy S2 do warstwy S1, ale nie z warstwy S2 do warstwy B1. 

Te przykłady są przeznaczone do pomaga w zrozumieniu sposobu dopasowywania Centrum IoT zmiana rozwiązania. Informacje na temat możliwości poszczególnych warstw zawsze, przejrzyj [cennik Centrum IoT Azure](https://azure.microsoft.com/pricing/details/iot-hub/). 

## <a name="upgrade-your-existing-iot-hub"></a>Uaktualnij istniejący Centrum IoT 

1. Zaloguj się do [portalu Azure](https://portal.azure.com/) i przejdź do Centrum IoT. 
2. Wybierz **cennik i skali**. 

   ![Ceny i skala](./media/iot-hub-upgrade/pricing-scale.png)

3. Aby zmienić warstwę Centrum, wybierz **warstwa cenowa i skali**. Wybierz nową warstwę, a następnie kliknij przycisk **wybierz**.

   ![Ceny i skala](./media/iot-hub-upgrade/select-tier.png)

4. Aby zmienić liczbę jednostek w Centrum, wprowadź nową wartość w obszarze **jednostki usługi IoT Hub**. 
5. Wybierz **zapisać** Aby zapisać zmiany. 

Centrum IoT teraz jest korygowana, a konfiguracje nie uległy zmianie. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej [Wybieranie prawo warstwy Centrum IoT](iot-hub-scaling.md). 

