---
title: Użyj urządzenia serii StorSimple 8000 podsumowania | Dokumentacja firmy Microsoft
description: Zawiera opis bloku podsumowania usługi StorSimple i wyjaśniono, jak korzystać z niego do monitorowania kondycji rozwiązania StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: d987a4ae170f21532a886552cbe1eb5a0d25fc3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874829"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Użyj bloku podsumowania usługi dla urządzenia z serii StorSimple 8000

## <a name="overview"></a>Omówienie

Blok podsumowania usługi Menedżer StorSimple urządzenia zawiera widok podsumowania wszystkich urządzeń, które są połączone z usługą Menedżera urządzeń StorSimple, wyróżnianie tych urządzeń, które wymagają uwagi administratora systemu. W tym samouczku wprowadza bloku podsumowania usługi, opisano zawartości pulpitu nawigacyjnego i funkcji oraz opisano zadania, które można wykonywać na tej stronie.

![Podsumowanie usługi](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Polecenia zarządzania

W bloku podsumowania usługi StorSimple można zobaczyć opcje zarządzania usługą Menedżer urządzenia StorSimple i urządzeń z serii StorSimple 8000 zarejestrowany dla tej usługi. Polecenia zarządzania zostanie wyświetlony w górnej części bloku i po lewej stronie.

![Pasek poleceń](./media/storsimple-8000-service-dashboard/service-summary2.png)

Użyj tych opcji, aby wykonywać różne operacje, takie jak dodawanie udziały lub woluminy lub monitor różne zadania uruchomione na urządzeniu StorSimple.


## <a name="essentials"></a>Podstawy

Obszar essentials przechwytuje niektóre ważne właściwości, takie jak grupy zasobów, lokalizacji i subskrypcji, w której został utworzony Menedżera urządzenia StorSimple.

![Podstawy](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Podsumowanie usługi Menedżer StorSimple urządzenia

* **Alerty** kafelka zawiera migawkę wszystkich aktywnych alertów dla wszystkich urządzeń, pogrupowane według ważności alertu.

    ![Kafelek alerty](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Kliknięcie kafelka otwiera **alerty** bloku, w przypadku, gdy alert indywidualny, aby wyświetlić dodatkowe szczegóły dotyczące alertu, można kliknąć wraz ze wszystkimi zalecane akcje. Można także wyczyścić alert, jeśli ten problem został rozwiązany.

    ![Kliknij Kafelek alerty](./media/storsimple-8000-service-dashboard/service-summary8.png)

* **Pojemności** Wyświetla kafelka przedstawia podstawowy magazynu, który jest inicjowana i pozostałe dla wszystkich urządzeń względem magazynu całkowita, która jest dostępna dla wszystkich urządzeń. **Zainicjowano obsługę administracyjną** odwołuje się do ilość miejsca w magazynie, który jest przygotowany i przydzielona do użycia, **pozostała** odwołuje się do pozostałe zasoby, które można udostępnić dla wszystkich urządzeń.

    ![Kafelek pojemności](./media/storsimple-8000-service-dashboard/service-summary6.png)

    **Pozostałych do warstwy** pojemność jest dostępna pojemność, które można udostępnić, łącznie z chmury, podczas **pozostałych lokalnego** jest dostępna na dysków dołączonych do urządzeń z serii StorSimple 8000 wydajność.


* W **użycia** wykresu, zobacz temat metryki istotne dla urządzeń. Można wyświetlić podstawowy magazyn używany dla wszystkich urządzeń i w magazynie w chmurze używane przez urządzenia w ciągu ostatnich 7 dni, domyślny okres czasu. 

    ![Kafelek użycie](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Aby wybrać inną zapewniały, użyj **Edytuj** opcji w prawym górnym rogu wykresu.

     ![Kliknij Kafelek użycie](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Eksportuj dane wykresu](./media/storsimple-8000-service-dashboard/service-summary11.png)

* **Urządzeń** kafelka zawiera podsumowanie liczby urządzeń z serii StorSimple 8000 w sieci urządzeń Menedżer StorSimple pogrupowane według stanu urządzenia. 

    ![Kafelek urządzenia](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Kliknij ten Kafelek, aby otworzyć **urządzeń** listy bloku, a następnie kliknij przycisk poszczególnych urządzeń do szczegółowego w podsumowaniu urządzenia specyficzne dla urządzenia. Można również wykonywać akcje specyficzne dla urządzenia z bloku podsumowania danego urządzenia. Aby uzyskać więcej informacji o bloku podsumowania urządzeń, przejdź do [bloku podsumowania urządzenia](storsimple-8000-device-dashboard.md).

    ![Kliknij Kafelek urządzenia](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Wyświetl dzienniki aktywności

Aby wyświetlić różne operacje przeprowadzane w Menedżerze urządzeń StorSimple, kliknij przycisk **Dzienniki aktywności** link po lewej stronie StorSimple bloku podsumowania usługi. Powoduje to przejście do **Dzienniki aktywności** bloku, w którym można obejrzeć podsumowanie ostatnie operacje przeprowadzane.

![Dzienniki aktywności](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o sposobie [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

