---
title: Podsumowanie bloku usługi tablicy wirtualnego StorSimple | Dokumentacja firmy Microsoft
description: Zawiera opis bloku podsumowania usługi Menedżer urządzenia StorSimple i wyjaśniono, jak korzystać z niego do monitorowania kondycji macierzy wirtualne StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 284e404c44505a98d9e0ed5abe87cd945415af56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875739"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Użyj bloku podsumowania usługi Menedżera urządzeń StorSimple podłączonym do wirtualnego StorSimple
## <a name="overview"></a>Omówienie
Podsumowanie bloku usługi Menedżer urządzeń StorSimple zawiera widok podsumowania StorSimple wirtualnego tablic (znanej także jako StorSimple lokalnego urządzenia wirtualne lub urządzeń wirtualnych), które są połączone z usługą, wyróżnianie te, które wymagają systemu Uwagi administratora. W tym samouczku wprowadza bloku podsumowania usługi, opisano zawartości i funkcji oraz opisano zadania, które można wykonać z poziomu tego bloku.

![Pulpit nawigacyjny usług](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Polecenia zarządzania i essentials
W bloku podsumowania StorSimple zobacz temat opcji zarządzania usługi Menedżer StorSimple urządzenia, a także wirtualnego tablic zarejestrowany dla tej usługi. Polecenia zarządzania zostanie wyświetlony w górnej części bloku i po lewej stronie.

Użyj tych opcji, aby wykonywać różne operacje, takie jak dodawanie udziały lub woluminy lub monitor różnych zadań, działają w tablicach wirtualnego.

Obszar essentials przechwytuje niektóre ważne właściwości, takie jak grupy zasobów, lokalizacji i subskrypcji, w której został utworzony Menedżera urządzenia StorSimple.

## <a name="storsimple-device-manager-service-summary"></a>Podsumowanie usługi Menedżer StorSimple urządzenia
* **Alerty** kafelka zawiera migawkę wszystkich aktywnych alertów dla wszystkich urządzeń wirtualnych, pogrupowane według ważności alertu. Kliknięcie kafelka otwiera **alerty** bloku, w przypadku, gdy alert indywidualny, aby wyświetlić dodatkowe szczegóły dotyczące alertu, można kliknąć wraz ze wszystkimi zalecane akcje. Można także wyczyścić alert, jeśli ten problem został rozwiązany.
* **Pojemności** Wyświetla kafelka przedstawia podstawowy magazynu, który jest inicjowana i pozostałe dla wszystkich urządzeń wirtualnych względem magazynu całkowita, która jest dostępna dla wszystkich urządzeń wirtualnych. **Zainicjowano obsługę administracyjną** odwołuje się do ilość miejsca w magazynie, który jest przygotowany i przydzielona do użycia, **pozostała** odwołuje się do pozostałe zasoby, które można udostępnić dla wszystkich urządzeń wirtualnych. **Pozostałych do warstwy** pojemność jest dostępna pojemność, które można udostępnić, łącznie z chmury, podczas **pozostałych lokalnego** pojemność pozostały na dyskach dołączone do wirtualnego tablic.
* W **użycia** wykresu, zobacz temat metryki istotne dla urządzenia wirtualnego. Można wyświetlić podstawowy magazyn używany dla wszystkich urządzeń wirtualnych, jak również w magazynie w chmurze używane przez urządzenia wirtualne w ciągu ostatnich 7 dni, domyślny okres czasu. Użyj **Edytuj** opcję w prawym górnym rogu wykresu, aby wybrać inną zapewniały.
* **Urządzeń** kafelka zawiera podsumowanie liczby wirtualnych tablic w sieci urządzeń Menedżer StorSimple pogrupowane według stanu urządzenia. Kliknij ten Kafelek, aby otworzyć **urządzeń** listy bloku, a następnie kliknij przycisk poszczególnych urządzeń do szczegółowego w podsumowaniu urządzenia specyficzne dla urządzenia. Można również wykonywać określone czynności urządzenia z danym urządzeniem bloku podsumowania. Aby uzyskać więcej informacji o bloku podsumowania urządzeń, przejdź do [bloku podsumowania urządzenia](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Wyświetl dzienniki aktywności
Aby wyświetlić różne operacje przeprowadzane w Menedżerze urządzeń StorSimple, kliknij przycisk **Dzienniki aktywności** link po lewej stronie StorSimple bloku podsumowania usługi. Powoduje to przejście do **Dzienniki aktywności** bloku, w którym można obejrzeć podsumowanie ostatnie operacje przeprowadzane.

![Dzienniki aktywności](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [używać lokalnego interfejsu użytkownika sieci web do administrowania tablica wirtualnego StorSimple](storsimple-ova-web-ui-admin.md).

