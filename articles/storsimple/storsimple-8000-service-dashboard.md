---
title: Użyj urządzenia StorSimple 8000 series podsumowania | Dokumentacja firmy Microsoft
description: W tym artykule opisano bloku podsumowania usługi StorSimple i wyjaśniono, jak korzystać z niego do monitorowania kondycji rozwiązania StorSimple.
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
ms.openlocfilehash: c174f6ce0fb3d40af953be205a7bfcca60fbfeec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60633145"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Użyj bloku podsumowania usługi dla urządzenia StorSimple 8000 series

## <a name="overview"></a>Omówienie

Bloku podsumowania usługi Menedżer urządzeń StorSimple zapewnia widok podsumowania wszystkich urządzeń, które są podłączone do usługi Menedżer urządzeń StorSimple, wyróżnianie tych urządzeń, które wymagają uwagi administratora systemu. W tym samouczku przedstawiono bloku podsumowania usługi, opisano zawartości pulpitu nawigacyjnego i funkcji, a następnie w tym artykule opisano zadania, które można wykonywać na tej stronie.

![Podsumowanie usługi](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Polecenia zarządzania

W bloku podsumowania usługi StorSimple widać opcji do zarządzania usługi Menedżer urządzeń StorSimple oraz urządzenia serii StorSimple 8000, które są zarejestrowane do tej usługi. Polecenia zarządzania zostanie wyświetlony wzdłuż górnej części bloku i po lewej stronie.

![Pasek poleceń](./media/storsimple-8000-service-dashboard/service-summary2.png)

Użyj tych opcji, aby wykonywać różne operacje, takie jak dodawanie udziałów lub woluminów lub monitor różne zadania, działające na urządzeniach StorSimple.


## <a name="essentials"></a>Podstawy

W obszarze essentials przechwytuje niektóre ważne właściwości, takich jak grupy zasobów, lokalizacji i subskrypcji, w którym utworzono Menedżera urządzeń StorSimple.

![Podstawy](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Podsumowanie usługi Menedżer urządzeń StorSimple

* **Alerty** Kafelek zawiera migawkę wszystkich aktywnych alertów dla wszystkich urządzeń, pogrupowane według ważności alertu.

    ![Kafelek alerty](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Kliknięcie kafelka spowoduje otworzenie **alerty** bloku, gdzie możesz kliknąć alert indywidualny, aby wyświetlić dodatkowe szczegóły dotyczące tego alertu, wraz ze wszystkimi zalecane akcje. Można także wyczyścić ten alert, jeśli problem został rozwiązany.

    ![Kliknij Kafelek alerty](./media/storsimple-8000-service-dashboard/service-summary8.png)

* **Pojemności** Wyświetla Kafelek pokazuje magazynu podstawowego, który jest obsługiwana i pozostałe na wszystkich urządzeniach względem całkowita ilość miejsca dostępna na wszystkich urządzeniach. **Zainicjowano obsługę administracyjną** odwołuje się do miejsca do magazynowania, który jest przygotowany i przydzielona do użycia, **pozostałe** odwołuje się do pozostałych pojemności, które mogą być udostępniane na wszystkich urządzeniach.

    ![Kafelek pojemności](./media/storsimple-8000-service-dashboard/service-summary6.png)

    **Pozostałe warstwy** pojemność to dostępnej pojemności, które mogą być udostępniane w chmurze, w tym podczas **pozostałe lokalnego** jest pojemność na dyski dołączone do StorSimple 8000 urządzenia z serii.


* W **użycia** wykresu, zobacz temat istotne metryki dla urządzeń. Możesz wyświetlić podstawowym magazynem używanym dla wszystkich urządzeń i używane przez urządzenia w ciągu ostatnich 7 dni, okres domyślny magazyn w chmurze. 

    ![Kafelek użycie](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Aby wybrać inny czas skalowania, użyj **Edytuj** opcji w prawym górnym rogu wykresu.

     ![Kliknij Kafelek użycie](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Eksportuj dane wykresu](./media/storsimple-8000-service-dashboard/service-summary11.png)

* **Urządzeń** Kafelek zawiera podsumowanie liczby urządzeń StorSimple serii 8000 w Menedżera urządzeń StorSimple pogrupowane według stanu urządzenia. 

    ![Kafelek urządzenia](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Kliknij ten Kafelek, aby otworzyć **urządzeń** listy bloku, a następnie kliknij przycisk poszczególnych urządzeń, aby przejść do podsumowanie urządzeń specyficznych dla urządzenia. Można również wykonać akcje specyficzne dla urządzenia w bloku podsumowania danego urządzenia. Aby uzyskać więcej informacji na temat w bloku podsumowanie urządzeń, przejdź do [blok podsumowania urządzenia](storsimple-8000-device-dashboard.md).

    ![Kliknij Kafelek urządzenia](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Wyświetlanie dzienników aktywności

Aby wyświetlić różne operacje przeprowadzane w ramach usługi Menedżer urządzeń StorSimple, kliknij **dzienników aktywności** link po lewej stronie bloku podsumowania usługi StorSimple. Spowoduje to przejście do **dzienników aktywności** bloku, w którym będą widoczne podsumowanie ostatnich czynności wykonywane.

![Dzienniki aktywności](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej na temat [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

