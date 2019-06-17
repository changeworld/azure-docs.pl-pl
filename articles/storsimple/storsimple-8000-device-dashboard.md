---
title: Użyj urządzenia StorSimple 8000 series podsumowania | Dokumentacja firmy Microsoft
description: Opisuje podsumowania urządzenia usługi Menedżer urządzeń StorSimple oraz używać go do wyświetlania metryk usługi storage i połączone inicjatorów i znaleźć numer seryjny i IQN.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60578321"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Korzystanie z urządzenia podsumowania w usłudze Menedżer urządzeń StorSimple

## <a name="overview"></a>Omówienie
Blok podsumowania urządzenia StorSimple zapewnia przegląd informacji dla określonego urządzenia StorSimple, w przeciwieństwie do bloku podsumowania usługi, który zawiera informacje o wszystkich urządzeniach, które są zawarte w Twoim rozwiązaniu Microsoft Azure StorSimple.

W bloku podsumowanie urządzeń zawiera widok podsumowania urządzenia StorSimple 8000 series, która jest zarejestrowana przy użyciu danego Menedżera urządzeń StorSimple, wyróżnianie tych problemów z urządzeniami, które wymagają uwagi administratora systemu. W tym samouczku przedstawiono bloku podsumowanie urządzenia, opisano zawartości i funkcji, a następnie w tym artykule opisano zadania, które można wykonywać z tego bloku.

W bloku podsumowanie urządzeń zawiera następujące informacje:

![Blok podsumowania urządzenia](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Pasek poleceń zarządzania

W bloku urządzenia StorSimple zobaczysz opcje służące do zarządzania urządzeniem StorSimple. Polecenia zarządzania zostanie wyświetlony wzdłuż górnej części bloku i po lewej stronie. Te opcje umożliwiają dodawanie udziałów lub woluminów, zaktualizować lub w trybie Failover urządzenia.

![Pasek poleceń zarządzania](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Podstawy

W obszarze essentials przechwytuje część ważne właściwości, takie jak stan, modelu, nazwa IQN i wersji oprogramowania. 

![Podstawowe informacje dotyczące urządzeń](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Monitorowanie

* **Alerty** Kafelek zawiera migawkę wszystkie aktywne alerty dla danego urządzenia, pogrupowane według ważności alertu.

    ![Alert kafelka](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Kliknij Kafelek, aby otworzyć **alerty** bloku, a następnie kliknij alert indywidualny, aby wyświetlić dodatkowe szczegóły dotyczące tego alertu, wraz ze wszystkimi zalecane akcje. Można także wyczyścić ten alert, jeśli problem został rozwiązany.

    ![Kliknij Kafelek alertu](./media/storsimple-8000-device-dashboard/device-summary10.png)

* **Stanu i kondycji** tabliczka zapewnia wgląd w kondycję składników sprzętowych dla urządzenia, w tym stan urządzenia. Stan urządzenia może być w trybie offline, online, dezaktywowane lub gotowe do skonfigurowania.

    ![Kafelek stanu i kondycji](./media/storsimple-8000-device-dashboard/device-summary5.png)

* **Woluminów** Kafelek zawiera podsumowanie liczby woluminów na urządzeniu pogrupowane według stanu.

    ![Kafelka woluminy](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Kliknij Kafelek, aby otworzyć **woluminów** listy bloku, a następnie kliknij na pojedynczy wolumin, aby wyświetlić lub zmodyfikować jego właściwości.
    
    ![Kliknij Kafelek woluminów](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Aby uzyskać więcej informacji, zobacz instrukcje [Zarządzanie woluminami](storsimple-8000-manage-volumes-u2.md).

* W **użycia** wykresu, można wyświetlić podstawowym magazynem używanym przez urządzenia i magazynu w chmurze używane w ciągu ostatnich 7 dni, domyślny okres czasu.

     ![Kafelek użycie](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Aby wybrać inny czas skalowania, użyj **Edytuj** opcji w prawym górnym rogu wykresu.

     ![Edytuj wykres użycia](./media/storsimple-8000-device-dashboard/device-summary12.png)

     Na tym wykresie możesz wyświetlić metryki dla całkowitej magazynu podstawowego (ilości danych zapisanych przez hosty do Twojego urządzenia) i magazynu w chmurze całkowita używane przez urządzenie za pośrednictwem przez pewien czas.
  
     W tym kontekście *magazynu podstawowego* odnosi się do całkowitej ilości danych zapisanych przez hosta i mogą być podzielone według typu woluminu: *głównej warstwowego magazynu* obejmuje zarówno lokalnie przechowywane dane i dane warstwowe Chmura. *Podstawowy przypięty lokalnie magazynu* zawiera po prostu dane przechowywane lokalnie. *Magazyn w chmurze*, z drugiej strony jest miara łączna ilość danych przechowywanych w chmurze. Ten magazyn zawiera dane warstwowe i kopie zapasowe. Dane przechowywane w chmurze jest deduplikowany i skompresowane magazynu podstawowego wskazuje ilość miejsca używanego, zanim dane są deduplikowane, a skompresowane. (Porównanie tych dwóch liczb, aby poznać częstotliwość kompresji). Dla obu lokacji podstawowej i Magazyn w chmurze, kwoty podane są oparte na częstotliwość śledzenia, należy skonfigurować. Na przykład jeśli częstotliwość jeden tydzień, wykres pokazuje dane dla każdego dnia w poprzednim tygodniu.

     Aby wyświetlić ilość magazynu w chmurze używane wraz z upływem czasu, wybierz **używany Magazyn w CHMURZE** opcji. Aby wyświetlić całkowita ilość miejsca, który został napisany przez hosta, wybierz **głównej WARSTWOWEGO MAGAZYNU używane** i **głównej LOKALNIE PRZYPIĘTE używany magazyn** opcje. 
     Aby uzyskać więcej informacji, zobacz [monitorować urządzenie StorSimple przy użyciu usługi Menedżer urządzeń StorSimple](storsimple-monitor-device.md).


* **Pojemności** Kafelek Wyświetla magazynu podstawowego, który jest obsługiwana i pozostałe na urządzeniu względem całkowita ilość miejsca dostępna dla tego samego. **Zainicjowano obsługę administracyjną** odwołuje się do miejsca do magazynowania, który jest przygotowany i przydzielona do użycia, **pozostałe** odwołuje się do pozostałych pojemności, które mogą być udostępniane na tym urządzeniu. 

    ![Kafelek użycie](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Kliknij ten Kafelek, aby wyświetlić, jak zainicjowano pojemność na woluminach warstwowych i przypiętego lokalnie. **Pozostałe warstwy** pojemność to dostępnej pojemności, które mogą być udostępniane w chmurze, w tym podczas **pozostałe lokalnego** jest pojemność na dyski dołączone do tego urządzenia.

    ![Kliknij wykres użycia](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [bloku podsumowania usługi StorSimple](storsimple-8000-service-dashboard.md).
* Dowiedz się więcej o [przy użyciu usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

