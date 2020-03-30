---
title: Użyj podsumowania urządzenia storsimple serii 8000 | Dokumenty firmy Microsoft
description: W tym artykule opisano StorSimple bloku podsumowania usługi i wyjaśnia, jak go używać do monitorowania kondycji rozwiązania StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267653"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Użyj bloku podsumowania usługi dla urządzenia storsimple serii 8000

## <a name="overview"></a>Omówienie

Blok podsumowania usługi StorSimple Device Manager zawiera widok podsumowania wszystkich urządzeń podłączonych do usługi StorSimple Device Manager, podkreślając te urządzenia, które wymagają uwagi administratora systemu. W tym samouczku przedstawiono blok podsumowania usługi, wyjaśniono zawartość i funkcję pulpitu nawigacyjnego oraz opisano zadania, które można wykonać na tej stronie.

![Podsumowanie usługi](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Polecenia zarządzania

W bloku podsumowania usługi StorSimple są widoczne opcje zarządzania usługą StorSimple Device Manager oraz urządzenia z serii StorSimple 8000 zarejestrowane w tej usłudze. Polecenia zarządzania są widoczne w górnej części bloku i po lewej stronie.

![Pasek poleceń](./media/storsimple-8000-service-dashboard/service-summary2.png)

Te opcje służy do wykonywania różnych operacji, takich jak dodać udziały lub woluminy lub monitorować różne zadania uruchomione na urządzeniach StorSimple.


## <a name="essentials"></a>Podstawy

Podstawowy obszar przechwytuje niektóre z ważnych właściwości, takich jak grupa zasobów, lokalizacja i subskrypcja, w której został utworzony Menedżer urządzeń StorSimple.

![Podstawy](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Podsumowanie usługi StorSimple Device Manager

* **Kafelek Alerty** zawiera migawkę wszystkich aktywnych alertów na wszystkich urządzeniach, pogrupowane według ważności alertu.

    ![Kafelek Alerty](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Kliknięcie kafelka powoduje otwarcie bloku **Alerty,** w którym można kliknąć pojedynczy alert, aby wyświetlić dodatkowe szczegóły dotyczące tego alertu, w tym wszelkie zalecane akcje. Można również wyczyścić alert, jeśli problem został rozwiązany.

    ![Kliknij kafelek alertów](./media/storsimple-8000-service-dashboard/service-summary8.png)

* Kafelek **Capacity** wyświetla podstawowy magazyn, który jest aprowizowana i pozostają na wszystkich urządzeniach względem całkowitej pamięci masowej dostępnej na wszystkich urządzeniach. **Aprowizowana** dotyczy ilości magazynu, który jest przygotowany i przydzielony do użycia, **Pozostałe** odnosi się do pozostałej pojemności, które mogą być aprowizacji na wszystkich urządzeniach.

    ![Pojemność płytki](./media/storsimple-8000-service-dashboard/service-summary6.png)

    **Pozostała pojemność warstwowa** to dostępna pojemność, która może być aprowizowana, w tym chmura, podczas gdy **pozostała lokalizacja** to pojemność pozostała na dyskach dołączonych do urządzeń z serii StorSimple 8000.


* Na **wykresie Użycie** możesz zobaczyć odpowiednie dane dla swoich urządzeń. Możesz wyświetlić magazyn podstawowy używany na wszystkich urządzeniach, a magazyn w chmurze używany przez urządzenia w ciągu ostatnich 7 dni, czyli domyślny okres. 

    ![Kafelek Użycia](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Aby wybrać inną skalę czasu, użyj opcji **Edytuj** w prawym górnym rogu wykresu.

     ![Kliknij kafelek użycia](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Eksportowanie danych wykresu](./media/storsimple-8000-service-dashboard/service-summary11.png)

* Kafelek **Urządzenia** zawiera podsumowanie liczby urządzeń z serii StorSimple 8000 w Menedżerze urządzeń StorSimple pogrupowanych według stanu urządzenia. 

    ![Kafelek Urządzenia](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Kliknij ten kafelek, aby otworzyć blok Listy **Urządzeń,** a następnie kliknij pojedyncze urządzenie, aby przejść do podsumowania urządzenia specyficznego dla urządzenia. Można również wykonać akcje specyficzne dla urządzenia z danego bloku podsumowania urządzenia. Aby uzyskać więcej informacji na temat bloku podsumowania urządzenia, przejdź do [bloku podsumowanie urządzenia](storsimple-8000-device-dashboard.md).

    ![Kliknij kafelek urządzenia](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Wyświetlanie dzienników aktywności

Aby wyświetlić różne operacje wykonywane w Menedżerze urządzeń StorSimple, kliknij **łącze Dzienniki aktywności** po lewej stronie bloku podsumowania usługi StorSimple. Spowoduje to przejście do **bloku Dzienniki aktywności,** gdzie można wyświetlić podsumowanie ostatnich operacji przeprowadzonych.

![Dzienniki aktywności](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o tym, jak [administrować urządzeniem StorSimple za pomocą usługi StorSimple Device Manager](storsimple-8000-manager-service-administration.md).

