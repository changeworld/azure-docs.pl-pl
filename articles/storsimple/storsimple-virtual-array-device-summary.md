---
title: Blok podsumowania urządzenia wirtualnego tablicy StorSimple | Dokumentacja firmy Microsoft
description: Zawiera opis bloku podsumowania urządzenia Menedżera urządzeń StorSimple i wyjaśniono, jak korzystać z niego do monitorowania kondycji macierzy wirtualne StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 35413d597c3b6b1c7600241a78572b63f982d175
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875683"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Użyj bloku podsumowania urządzenia do Menedżera urządzeń StorSimple podłączonym do wirtualnego StorSimple

## <a name="overview"></a>Omówienie

Menedżer urządzeń StorSimple bloku urządzenia zawiera widok podsumowania tablicy wirtualnego StorSimple, która jest zarejestrowana przy użyciu danego StorSimple Menedżera urządzeń, wyróżnianie tych problemów z urządzeniami, które wymagają uwagi administratora systemu. W tym samouczku wprowadza blok podsumowania urządzenia, opisano zawartości i funkcji oraz opisano zadania, które można wykonać z poziomu tego bloku.

Blok podsumowania urządzeń zawiera następujące informacje:

![Pulpit nawigacyjny urządzenia](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Zarządzanie

W bloku urządzenia StorSimple zobacz temat opcje służące do zarządzania urządzeniem StorSimple. Polecenia zarządzania zostanie wyświetlony w górnej części bloku i po lewej stronie. Te opcje umożliwiają dodawanie udziały lub woluminy, lub zaktualizować lub tablica wirtualnej w tryb failover.

Obszar essentials przechwytuje niektóre ważne właściwości, takie jak stan, modelu, wersji oprogramowania, a także łącze do **interfejsu użytkownika sieci Web** tablicy. Jeśli w sieci wewnętrznej, możesz bezpośrednio uruchomić [lokalnego interfejsu użytkownika sieci web](storsimple-ova-web-ui-admin.md) do administrowania tablica wirtualnego.

![Podstawowe informacje dotyczące urządzeń](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Podsumowanie urządzenia StorSimple

* **Alerty** kafelka zawiera migawkę wszystkich aktywnych alertów dla sieci wirtualnej tablicy pogrupowane według ważności alertu. Kliknij Kafelek, aby otworzyć **alerty** bloku, a następnie kliknij alert indywidualny, aby wyświetlić dodatkowe szczegóły dotyczące alertu, wraz ze wszystkimi zalecane akcje. Można także wyczyścić alert, jeśli ten problem został rozwiązany.

* **Pojemności** kafelka Wyświetla podstawowego magazynu, który jest inicjowana i pozostałe przez urządzenie wirtualne względem magazynu całkowita, która jest dostępna dla tego samego. **Zainicjowano obsługę administracyjną** odwołuje się do ilość miejsca w magazynie, który jest przygotowany i przydzielona do użycia, **pozostała** odwołuje się do pozostałych wydajności, którą można udostępnić w tym urządzeniu. **Pozostałych do warstwy** pojemność jest dostępna pojemność, które można udostępnić, łącznie z chmury, podczas **pozostałych lokalnego** jest pojemności na dysków dołączonych do tej wirtualnej tablicy.

* W **użycia** wykresu, można wyświetlić podstawowy magazyn używany przez wirtualne tablica, jak również w magazynie w chmurze używane w ciągu ostatnich 7 dni, domyślny okres czasu. Użyj **Edytuj** opcję w prawym górnym rogu wykresu, aby wybrać inną zapewniały.

* **Udziałów** lub **woluminów** kafelka zawiera podsumowanie liczby udziały lub woluminy w urządzeniu pogrupowane według stanu. Kliknij Kafelek, aby otworzyć **udziałów** lub **woluminów** listy bloku, a następnie kliknij w poszczególnych udziale lub woluminie, aby wyświetlić lub zmodyfikować jego właściwości. Aby uzyskać więcej informacji, zobacz temat jak [Zarządzanie udziałami](storsimple-virtual-array-manage-shares.md) lub [Zarządzanie woluminami](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Następne kroki
Instrukcje:
- [Zarządzanie udziałami w macierzy wirtualnego StorSimple](storsimple-virtual-array-manage-shares.md)
    
- [Zarządzanie woluminami w macierzy wirtualnego StorSimple](storsimple-virtual-array-manage-volumes.md)

