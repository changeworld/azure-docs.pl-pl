---
title: Użyj podsumowania urządzenia storsimple serii 8000 | Dokumenty firmy Microsoft
description: W tym artykule opisano podsumowanie urządzenia usługi StorSimple Device Manager i jak go używać do wyświetlania metryk magazynu i podłączonych inicjatorów oraz znajdowania numeru seryjnego i IQN.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 1d88af2c0739c30b2562bad7660015b890e8159c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60578321"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Użyj podsumowania urządzenia w usłudze StorSimple Device Manager

## <a name="overview"></a>Omówienie
Narzędzie StorSimple podsumowanie urządzenia zawiera omówienie informacji dla określonego urządzenia StorSimple, w przeciwieństwie do bloku podsumowania usługi, który zawiera informacje o wszystkich urządzeniach zawartych w rozwiązaniu Microsoft Azure StorSimple.

Blok podsumowania urządzenia zawiera widok podsumowania urządzenia z serii StorSimple 8000 zarejestrowanego w danym Menedżerze urządzeń StorSimple, podkreślając te problemy z urządzeniem, które wymagają uwagi administratora systemu. W tym samouczku przedstawiono blok podsumowania urządzenia, wyjaśniono zawartość i funkcję oraz opisano zadania, które można wykonać z tego bloku.

W bloku podsumowania urządzenia wyświetlane są następujące informacje:

![Ostrze podsumowujące urządzenie](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Pasek poleceń zarządzania

W storsimple urządzenia bloku, zobaczysz opcje zarządzania urządzeniem StorSimple. Polecenia zarządzania są widoczne w górnej części bloku i po lewej stronie. Użyj tych opcji, aby dodać udziały lub woluminy, zaktualizować lub przejażyć w pracy urządzenia.

![Pasek poleceń zarządzania](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Podstawy

Podstawowy obszar przechwytuje niektóre z ważnych właściwości, takich jak stan, model, docelowy IQN i wersja oprogramowania. 

![Podstawowe informacje o urządzeniu](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Monitorowanie

* **Kafelek Alerty** zawiera migawkę wszystkich aktywnych alertów dla urządzenia, pogrupowane według ważności alertu.

    ![Kafelek Alert](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Kliknij kafelek, aby otworzyć blok **Alerty,** a następnie kliknij pojedynczy alert, aby wyświetlić dodatkowe szczegóły dotyczące tego alertu, w tym wszelkie zalecane akcje. Można również wyczyścić alert, jeśli problem został rozwiązany.

    ![Kliknij kafelek alertu](./media/storsimple-8000-device-dashboard/device-summary10.png)

* Kafelek **Stan i kondycja** zapewnia wgląd w kondycję składnika sprzętowego urządzenia, w tym stan urządzenia. Stan urządzenia może być w trybie offline, w trybie online, dezaktywowany lub gotowy do skonfigurowania.

    ![Kafelek Stan i kondycja](./media/storsimple-8000-device-dashboard/device-summary5.png)

* **Kafelek Woluminy** zawiera podsumowanie liczby woluminów w urządzeniu pogrupowanych według stanu.

    ![Kafelek woluminów](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Kliknij kafelek, aby otworzyć blok listy **Woluminy,** a następnie kliknij pojedynczy wolumin, aby wyświetlić lub zmodyfikować jego właściwości.
    
    ![Kliknij kafelek woluminów](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Aby uzyskać więcej informacji, zobacz jak [zarządzać woluminami](storsimple-8000-manage-volumes-u2.md).

* Na wykresie **Użycia** można wyświetlić magazyn podstawowy używany na urządzeniu, a magazyn w chmurze używany w ciągu ostatnich 7 dni, domyślny okres czasu.

     ![Kafelek Użycia](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Aby wybrać inną skalę czasu, użyj opcji **Edytuj** w prawym górnym rogu wykresu.

     ![Edytowanie wykresu użycia](./media/storsimple-8000-device-dashboard/device-summary12.png)

     Na tym wykresie można wyświetlić metryki całkowitej pamięci podstawowej (ilość danych zapisywanych przez hosty na urządzeniu) i całkowitą ilość miejsca w chmurze zużywanego przez urządzenie przez pewien czas.
  
     W tym kontekście *magazyn podstawowy* odnosi się do całkowitej ilości danych zapisanych przez hosta i może być podzielony według typu woluminu: podstawowy *magazyn warstwowy* obejmuje zarówno lokalnie przechowywane dane, jak i dane warstwowe w chmurze. *Podstawowy magazyn przypięty lokalnie* zawiera tylko dane przechowywane lokalnie. Natomiast przechowywanie w *chmurze*to pomiar całkowitej ilości danych przechowywanych w chmurze. Ten magazyn zawiera warstwowe dane i kopie zapasowe. Dane przechowywane w chmurze są deduplikowane i kompresowane, natomiast magazyn podstawowy wskazuje ilość magazynu używanego przed deduplikowaniem i kompresowaniem danych. (Można porównać te dwie liczby, aby zorientować się w szybkości kompresji.) W przypadku magazynu podstawowego i w chmurze wyświetlane kwoty są oparte na skonfigurowanych częstotliwości śledzenia. Jeśli na przykład wybierzesz częstotliwość jednego tygodnia, na wykresie są wyświetlane dane za każdy dzień w poprzednim tygodniu.

     Aby zobaczyć ilość magazynu w chmurze zużywanego w czasie, wybierz opcję **UŻYWANA PAMIĘĆ MASOWA W CHMURZE.** Aby wyświetlić całkowitą ilość magazynu, który został napisany przez hosta, wybierz **opcje UŻYWANEJ PAMIĘCI MASOWEJ WARSTWOWEJ PODSTAWOWEJ** i **PODSTAWOWA PAMIĘĆ MASOWA PRZYPIĘTA LOKALNIE.** 
     Aby uzyskać więcej informacji, zobacz [Monitorowanie urządzenia StorSimple za pomocą usługi StorSimple Device Manager](storsimple-monitor-device.md).


* Kafelek **Capacity** wyświetla magazyn podstawowy, który jest aprowizowany i pozostaje na urządzeniu względem całkowitej dostępnej dla tego samego miejsca. **Aprowizowana** dotyczy ilości magazynu, który jest przygotowany i przydzielony do użycia, **Pozostałe** odnosi się do pozostałej pojemności, które mogą być aprowizacji na tym urządzeniu. 

    ![Kafelek Użycia](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Kliknij ten kafelek, aby zobaczyć, jak pojemność jest aprowizowana przez woluminy warstwowe i przypięte lokalnie. **Pozostała pojemność warstwowa** to dostępna pojemność, która może być aprowizowana, w tym chmura, podczas gdy **pozostała lokalizacja** to pojemność pozostała na dyskach dołączonych do tego urządzenia.

    ![Kliknij wykres użycia](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [bloku podsumowania usługi StorSimple](storsimple-8000-service-dashboard.md).
* Dowiedz się więcej o [administrowaniu urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-8000-manager-service-administration.md)

