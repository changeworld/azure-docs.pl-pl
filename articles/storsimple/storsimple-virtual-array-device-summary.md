---
title: Blok podsumowania urządzenia StorSimple Virtual Array | Dokumentacja firmy Microsoft
description: W tym artykule opisano blok podsumowania urządzenia dla Menedżera urządzeń StorSimple i wyjaśniono, jak korzystać z niego do monitorowania kondycji rozwiązania StorSimple Virtual Array.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61408510"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Użyj bloku podsumowanie urządzenia dla Menedżera urządzeń StorSimple połączyć rozwiązania StorSimple Virtual Array

## <a name="overview"></a>Omówienie

Blok urządzeń Menedżer urządzeń StorSimple zapewnia widok podsumowania macierzy wirtualnej StorSimple, która jest zarejestrowana przy użyciu danego Menedżera urządzeń StorSimple, wyróżnianie tych problemów z urządzeniami, które wymagają uwagi administratora systemu. W tym samouczku przedstawiono bloku podsumowanie urządzenia, opisano zawartości i funkcji, a następnie w tym artykule opisano zadania, które można wykonywać z tego bloku.

W bloku podsumowanie urządzeń zawiera następujące informacje:

![Pulpit nawigacyjny urządzenia](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Zarządzanie

W bloku urządzenia StorSimple zobaczysz opcje służące do zarządzania urządzeniem StorSimple. Polecenia zarządzania zostanie wyświetlony wzdłuż górnej części bloku i po lewej stronie. Te opcje umożliwiają dodawanie udziałów lub woluminów, zaktualizować lub macierz wirtualna w trybie Failover.

W obszarze essentials przechwytuje niektóre ważne właściwości, takie jak stan, modelu, wersji oprogramowania, a także łącze do **interfejs użytkownika sieci Web** tablicy. Jeśli w sieci wewnętrznej, możesz bezpośrednio uruchomić [lokalnego Interfejsu w przeglądarce](storsimple-ova-web-ui-admin.md) do administrowania macierz wirtualna.

![Podstawowe informacje dotyczące urządzeń](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Podsumowanie urządzenia StorSimple

* **Alerty** Kafelek zawiera migawkę wszystkich aktywnych alertów macierz wirtualna pogrupowane według ważności alertu. Kliknij Kafelek, aby otworzyć **alerty** bloku, a następnie kliknij alert indywidualny, aby wyświetlić dodatkowe szczegóły dotyczące tego alertu, wraz ze wszystkimi zalecane akcje. Można także wyczyścić ten alert, jeśli problem został rozwiązany.

* **Pojemności** Kafelek Wyświetla magazynu podstawowego, która jest obsługiwana i pozostałe w obrębie urządzenia wirtualnego względem całkowita ilość miejsca dostępna dla tego samego. **Zainicjowano obsługę administracyjną** odwołuje się do miejsca do magazynowania, który jest przygotowany i przydzielona do użycia, **pozostałe** odwołuje się do pozostałych pojemności, które mogą być udostępniane na tym urządzeniu. **Pozostałe warstwy** pojemność to dostępnej pojemności, które mogą być udostępniane w chmurze, w tym podczas **pozostałe lokalnego** jest pojemność na dyski dołączone do tej macierzy wirtualnej.

* W **użycia** wykresu, można wyświetlić podstawowym magazynem używanym przez macierz wirtualna, a także magazynu w chmurze używane w ciągu ostatnich 7 dni, domyślny okres czasu. Użyj **Edytuj** opcję w prawym górnym rogu wykresu, aby wybrać inny czas skalowania.

* **Udziałów** lub **woluminów** Kafelek zawiera podsumowanie liczby udział lub wolumin w urządzeniu, pogrupowane według stanu. Kliknij Kafelek, aby otworzyć **udziałów** lub **woluminów** listy bloku, a następnie kliknij na dany udział lub wolumin, aby wyświetlić lub zmodyfikować jego właściwości. Aby uzyskać więcej informacji, zobacz instrukcje [Zarządzanie udziałami](storsimple-virtual-array-manage-shares.md) lub [Zarządzanie woluminami](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Kolejne kroki
Instrukcje:
- [Zarządzanie udziałami w macierzy wirtualnej StorSimple](storsimple-virtual-array-manage-shares.md)
    
- [Zarządzanie woluminami w macierzy wirtualnej StorSimple](storsimple-virtual-array-manage-volumes.md)

