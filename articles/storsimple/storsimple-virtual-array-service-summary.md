---
title: StorSimple Virtual Array service summary blade | Dokumenty firmy Microsoft
description: W tym artykule opisano blok podsumowania usługi dla Menedżera urządzeń StorSimple i wyjaśniono, jak go używać do monitorowania kondycji tablicy wirtualnej StorSimple.
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
ms.openlocfilehash: 9c05bddaeb3c34400db1ec75c624ef00a85d9444
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64720712"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Użyj bloku podsumowania usługi dla Menedżera urządzeń StorSimple podłączonego do tablicy wirtualnej StorSimple
## <a name="overview"></a>Omówienie
Blok podsumowania usługi dla Menedżera urządzeń StorSimple udostępnia widok podsumowania tablic wirtualnych StorSimple (znanych również jako lokalne urządzenia wirtualne lub urządzenia wirtualne StorSimple), które są podłączone do usługi, podświetlając te, które potrzebują systemu uwagę administratora. W tym samouczku przedstawiono bloku podsumowania usługi, wyjaśniono zawartość i funkcję oraz opisano zadania, które można wykonać z tego bloku.

![Pulpit nawigacyjny usługi](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Polecenia zarządzania i podstawowe elementy
W storsimple podsumowanie bloku, można zobaczyć opcje zarządzania usługą StorSimple Device Manager, jak również tablice wirtualne zarejestrowane w tej usłudze. Polecenia zarządzania są widoczne w górnej części bloku i po lewej stronie.

Te opcje służy do wykonywania różnych operacji, takich jak dodawanie udziałów lub woluminów lub monitorować różne zadania uruchomione w tablicach wirtualnych.

Podstawowy obszar przechwytuje niektóre z ważnych właściwości, takich jak grupa zasobów, lokalizacja i subskrypcja, w której został utworzony Menedżer urządzeń StorSimple.

## <a name="storsimple-device-manager-service-summary"></a>Podsumowanie usługi StorSimple Device Manager
* **Kafelek Alerty** zawiera migawkę wszystkich aktywnych alertów na wszystkich urządzeniach wirtualnych, pogrupowane według ważności alertu. Kliknięcie kafelka powoduje otwarcie bloku **Alerty,** w którym można kliknąć pojedynczy alert, aby wyświetlić dodatkowe szczegóły dotyczące tego alertu, w tym wszelkie zalecane akcje. Można również wyczyścić alert, jeśli problem został rozwiązany.
* Kafelek **Capacity** wyświetla podstawowy magazyn, który jest aprowizowana i pozostają na wszystkich urządzeniach wirtualnych względem całkowitej pamięci masowej dostępnej na wszystkich urządzeniach wirtualnych. **Aprowizowana** dotyczy ilości magazynu, który jest przygotowany i przydzielony do użycia, **Remaining** odnosi się do pozostałej pojemności, które mogą być aprowizacji na wszystkich urządzeniach wirtualnych. **Pozostała pojemność warstwowa** to dostępna pojemność, która może być aprowizowana, w tym chmura, podczas gdy **pozostała lokalizacja** to pojemność pozostała na dyskach dołączonych do macierzy wirtualnych.
* Na **wykresie Użycia** można wyświetlić odpowiednie metryki dla urządzeń wirtualnych. Można wyświetlić magazyn podstawowy używany na wszystkich urządzeniach wirtualnych, a także magazyn w chmurze używany przez urządzenia wirtualne w ciągu ostatnich 7 dni, domyślny okres czasu. Użyj opcji **Edytuj** w prawym górnym rogu wykresu, aby wybrać inną skalę czasu.
* Kafelek **Urządzenia** zawiera podsumowanie liczby tablic wirtualnych w Menedżerze urządzeń StorSimple pogrupowanych według stanu urządzenia. Kliknij ten kafelek, aby otworzyć blok Listy **Urządzeń,** a następnie kliknij pojedyncze urządzenie, aby przejść do podsumowania urządzenia specyficznego dla urządzenia. Można również wykonać akcje specyficzne dla urządzenia z danego bloku podsumowania urządzenia. Aby uzyskać więcej informacji na temat bloku podsumowania urządzenia, przejdź do [bloku podsumowanie urządzenia](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Wyświetlanie dzienników aktywności
Aby wyświetlić różne operacje wykonywane w Menedżerze urządzeń StorSimple, kliknij **łącze Dzienniki aktywności** po lewej stronie bloku podsumowania usługi StorSimple. Spowoduje to przejście do **bloku Dzienniki aktywności,** gdzie można wyświetlić podsumowanie ostatnich operacji przeprowadzonych.

![Dzienniki aktywności](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [zarządzać tablicą wirtualną StorSimple za pomocą lokalnego interfejsu użytkownika sieci Web.](storsimple-ova-web-ui-admin.md)

