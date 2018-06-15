---
title: Użyj urządzenia serii StorSimple 8000 podsumowania | Dokumentacja firmy Microsoft
description: Opisuje podsumowania urządzenia usługi Menedżer StorSimple urządzenia i jak z niego korzystać, aby wyświetlić metryki magazynu i inicjatorów połączonych i znaleźć numer seryjny i IQN.
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
ms.openlocfilehash: 784d3ce9d8f926b00ac1c6fbf48a05c0b04f900a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874857"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Podsumowanie w usłudze Menedżer StorSimple urządzenia urządzenia

## <a name="overview"></a>Omówienie
Blok podsumowania urządzenia StorSimple zapewnia przegląd informacji dla określonego urządzenia StorSimple, w przeciwieństwie do usługi podsumowania bloku, który zawiera informacje o wszystkich urządzeniach, które są zawarte w rozwiązaniu Microsoft Azure StorSimple.

Blok podsumowania urządzenia zawiera widok podsumowania urządzenia serii StorSimple 8000, który jest zarejestrowany z danym urządzeniem Menedżer StorSimple, wyróżnianie tych problemów z urządzeniami, które wymagają uwagi administratora systemu. W tym samouczku wprowadza blok podsumowania urządzenia, opisano zawartości i funkcji oraz opisano zadania, które można wykonać z poziomu tego bloku.

Blok podsumowania urządzeń zawiera następujące informacje:

![Podsumowanie bloku urządzenia](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Pasek poleceń zarządzania

W bloku urządzenia StorSimple zobacz temat opcje służące do zarządzania urządzeniem StorSimple. Polecenia zarządzania zostanie wyświetlony w górnej części bloku i po lewej stronie. Te opcje umożliwiają Dodaj udziały lub woluminy lub zaktualizować w tryb failover urządzenia.

![Pasek poleceń zarządzania](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Podstawy

Obszar essentials przechwytuje niektóre ważne właściwości, takie jak, stan modelu, docelowy IQN i wersji oprogramowania. 

![Podstawowe informacje dotyczące urządzeń](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Monitorowanie

* **Alerty** kafelka zawiera migawkę wszystkie aktywne alerty dla danego urządzenia, pogrupowane według ważności alertu.

    ![Kafelek alertu](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Kliknij Kafelek, aby otworzyć **alerty** bloku, a następnie kliknij alert indywidualny, aby wyświetlić dodatkowe szczegóły dotyczące alertu, wraz ze wszystkimi zalecane akcje. Można także wyczyścić alert, jeśli ten problem został rozwiązany.

    ![Kliknij Kafelek alertu](./media/storsimple-8000-device-dashboard/device-summary10.png)

* **Stanu i kondycji** kafelka zapewnia wgląd w kondycję składnika sprzętu dla urządzeń, w tym stan urządzenia. Stan urządzenia mogą być w trybie offline, online, dezaktywować lub przejść do konfigurowania.

    ![Kafelek stanu i kondycji](./media/storsimple-8000-device-dashboard/device-summary5.png)

* **Woluminów** kafelka zawiera podsumowanie liczby woluminów w urządzeniu pogrupowane według stanu.

    ![Kafelka woluminy](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Kliknij Kafelek, aby otworzyć **woluminów** listy bloku, a następnie kliknij poszczególne woluminu, aby wyświetlić lub zmodyfikować jego właściwości.
    
    ![Kliknij Kafelek woluminów](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Aby uzyskać więcej informacji, zobacz temat jak [Zarządzanie woluminami](storsimple-8000-manage-volumes-u2.md).

* W **użycia** wykresu, można wyświetlić podstawowy magazyn używany przez urządzenie i magazynu w chmurze używane w ciągu ostatnich 7 dni, domyślny okres czasu.

     ![Kafelek użycie](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Aby wybrać inną zapewniały, użyj **Edytuj** opcji w prawym górnym rogu wykresu.

     ![Edytuj wykres użycia](./media/storsimple-8000-device-dashboard/device-summary12.png)

     Na tym wykresie można wyświetlić metryki całkowita ilość miejsca głównej (ilość danych zapisanych przez hosty z urządzeniem) i magazynu w chmurze całkowita liczba zużywane przez urządzenie w danym okresie czasu.
  
     W tym kontekście *magazynu głównego* odwołuje się do całkowitej ilości danych zapisanych przez hosta i mogą być podzielone przez typ woluminu: *podstawowego do warstwy magazynu* obejmuje zarówno lokalnie przechowywanych danych i warstwy do chmury. *Podstawowy przypięty lokalnie magazynu* zawiera tylko dane przechowywane lokalnie. *Magazyn w chmurze*, z drugiej strony, jest wartość całkowita ilość danych przechowywanych w chmurze. Ten magazyn zawiera warstwowych danych i tworzenie kopii zapasowych. Dane przechowywane w chmurze jest deduplikowany i skompresowane magazynu podstawowego wskazuje ilość miejsca w magazynie używana, zanim dane jest deduplikowany i skompresowane. (Porównanie tych dwóch liczb, aby poznać częstotliwość kompresji). Dla obu lokacji podstawowej i magazynu w chmurze, kwoty wyświetlane są oparte na częstotliwość śledzenia, należy skonfigurować. Na przykład jeśli częstotliwość tydzień, wykres przedstawia dane dla poszczególnych dni w poprzednim tygodniu.

     Aby sprawdzić ilość magazynu w chmurze używane wraz z upływem czasu, wybierz **używany Magazyn w CHMURZE** opcji. Aby wyświetlić całkowita ilość miejsca, które zostały napisane przez hosta, wybierz **podstawowego do warstwy MAGAZYNU używane** i **głównej LOKALNIE PRZYPIĘTY Magazyn używany** opcje. 
     Aby uzyskać więcej informacji, zobacz [monitorować urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-monitor-device.md).


* **Pojemności** kafelka Wyświetla podstawowego magazynu, który uzyskuje elastycznie i pozostałe urządzenia względem magazynu całkowita, która jest dostępna dla tego samego. **Zainicjowano obsługę administracyjną** odwołuje się do ilość miejsca w magazynie, który jest przygotowany i przydzielona do użycia, **pozostała** odwołuje się do pozostałych wydajności, którą można udostępnić w tym urządzeniu. 

    ![Kafelek użycie](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Kliknij ten Kafelek, aby wyświetlić sposób pojemność zostanie zainicjowana na woluminach warstwowych i przypiętych lokalnie. **Pozostałych do warstwy** pojemność jest dostępna pojemność, które można udostępnić, łącznie z chmury, podczas **pozostałych lokalnego** jest pojemności na dyski podłączone do tego urządzenia.

    ![Kliknij wykres użycia](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [bloku podsumowania usługi StorSimple](storsimple-8000-service-dashboard.md).
* Dowiedz się więcej o [przy użyciu usługi Menedżer StorSimple urządzenia do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

