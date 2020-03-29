---
title: Blok podsumowania urządzenia StorSimple Virtual Array | Dokumenty firmy Microsoft
description: W tym artykule opisano blok podsumowania urządzenia dla Menedżera urządzeń StorSimple i wyjaśniono, jak go używać do monitorowania kondycji tablicy wirtualnej StorSimple.
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
ms.openlocfilehash: 9edc0b552f5c2f38e646bc4b44dd8df5c16b0457
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61408510"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Użyj bloku podsumowania urządzenia dla Menedżera urządzeń StorSimple podłączonego do tablicy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

Blok urządzenia StorSimple Device Manager zawiera widok podsumowania tablicy wirtualnej StorSimple, która jest zarejestrowana w danym Menedżerze urządzeń StorSimple, podkreślając te problemy z urządzeniem, które wymagają uwagi administratora systemu. W tym samouczku przedstawiono blok podsumowania urządzenia, wyjaśniono zawartość i funkcję oraz opisano zadania, które można wykonać z tego bloku.

W bloku podsumowania urządzenia wyświetlane są następujące informacje:

![Pulpit nawigacyjny urządzenia](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Zarządzanie

W storsimple urządzenia bloku, zobaczysz opcje zarządzania urządzeniem StorSimple. Polecenia zarządzania są widoczne w górnej części bloku i po lewej stronie. Użyj tych opcji, aby dodać udziały lub woluminy, zaktualizować lub przejrzyć po awarii tablicy wirtualnej.

Podstawowy obszar przechwytuje niektóre z ważnych właściwości, takich jak stan, model, wersja oprogramowania, a także łącze do **interfejsu użytkownika sieci Web** tablicy. Jeśli korzystasz z sieci wewnętrznej, możesz bezpośrednio uruchomić [lokalny interfejs użytkownika sieci Web](storsimple-ova-web-ui-admin.md) w celu administrowania macierzą wirtualną.

![Podstawowe informacje o urządzeniu](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>StorSimple podsumowanie urządzenia

* **Kafelek Alerty** zawiera migawkę wszystkich aktywnych alertów dla tablicy wirtualnej, pogrupowane według ważności alertu. Kliknij kafelek, aby otworzyć blok **Alerty,** a następnie kliknij pojedynczy alert, aby wyświetlić dodatkowe szczegóły dotyczące tego alertu, w tym wszelkie zalecane akcje. Można również wyczyścić alert, jeśli problem został rozwiązany.

* Kafelek **Capacity** wyświetla magazyn podstawowy, który jest aprowizowana i pozostają na urządzeniu wirtualnym względem całkowitej dostępnej dla tego samego miejsca. **Aprowizowana** dotyczy ilości magazynu, który jest przygotowany i przydzielony do użycia, **Pozostałe** odnosi się do pozostałej pojemności, które mogą być aprowizacji na tym urządzeniu. **Pozostała pojemność warstwowa** to dostępna pojemność, która może być aprowizowana, w tym chmura, podczas gdy **pozostała lokalizacja** to pojemność pozostała na dyskach dołączonych do tej tablicy wirtualnej.

* Na wykresie **Użycia** można wyświetlić magazyn podstawowy używany w macierzy wirtualnej, a także magazyn w chmurze zużywany w ciągu ostatnich 7 dni, czyli domyślny okres czasu. Użyj opcji **Edytuj** w prawym górnym rogu wykresu, aby wybrać inną skalę czasu.

* Kafelek **Udziały** lub **woluminy** zawiera podsumowanie liczby udziałów lub woluminów w urządzeniu pogrupowanych według stanu. Kliknij kafelek, aby otworzyć blok listy **Udziały** lub **Objętości,** a następnie kliknij pojedynczy udział lub wolumin, aby wyświetlić lub zmodyfikować jego właściwości. Aby uzyskać więcej informacji, zobacz jak [zarządzać udziałami](storsimple-virtual-array-manage-shares.md) lub [zarządzać wolumenami](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Następne kroki
Instrukcje:
- [Zarządzanie udziałami w tablicy wirtualnej StorSimple](storsimple-virtual-array-manage-shares.md)
    
- [Zarządzanie woluminami w tablicy wirtualnej StorSimple](storsimple-virtual-array-manage-volumes.md)

