---
title: Bloku podsumowania usługi StorSimple Virtual Array | Dokumentacja firmy Microsoft
description: W tym artykule opisano bloku podsumowania usługi Menedżer urządzeń StorSimple i wyjaśniono, jak korzystać z niego do monitorowania kondycji rozwiązania StorSimple Virtual Array.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720712"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Użyj bloku podsumowania usługi Menedżer urządzeń StorSimple połączyć rozwiązania StorSimple Virtual Array
## <a name="overview"></a>Omówienie
Bloku podsumowania usługi Menedżer urządzeń StorSimple zapewnia widok podsumowania macierze wirtualne StorSimple (znany także jako StorSimple w środowisku lokalnym wirtualnych urządzeń lub urządzeń wirtualnych), które są połączone z usługą, wyróżnianie tych, które wymaga systemu operacyjnego Uwagi administratora. W tym samouczku przedstawiono bloku podsumowania usługi, opisano zawartości i funkcji, a następnie w tym artykule opisano zadania, które można wykonywać z tego bloku.

![Pulpit nawigacyjny usług](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Polecenia zarządzania i essentials
W bloku podsumowania usługi StorSimple zobaczysz opcje do zarządzania usługi Menedżer urządzeń StorSimple, a także macierzy wirtualnych zarejestrowany dla tej usługi. Polecenia zarządzania zostanie wyświetlony wzdłuż górnej części bloku i po lewej stronie.

Użyj tych opcji, aby wykonywać różne operacje, takie jak dodawanie udziałów lub woluminów lub monitor różnych zadań uruchomionych na macierzy wirtualnych.

W obszarze essentials przechwytuje niektóre ważne właściwości, takich jak grupy zasobów, lokalizacji i subskrypcji, w którym utworzono Menedżera urządzeń StorSimple.

## <a name="storsimple-device-manager-service-summary"></a>Podsumowanie usługi Menedżer urządzeń StorSimple
* **Alerty** Kafelek zawiera migawkę wszystkich aktywnych alertów dla wszystkich urządzeń wirtualnych, pogrupowane według ważności alertu. Kliknięcie kafelka spowoduje otworzenie **alerty** bloku, gdzie możesz kliknąć alert indywidualny, aby wyświetlić dodatkowe szczegóły dotyczące tego alertu, wraz ze wszystkimi zalecane akcje. Można także wyczyścić ten alert, jeśli problem został rozwiązany.
* **Pojemności** Wyświetla Kafelek pokazuje magazynu podstawowego, który jest obsługiwana i pozostałej dla wszystkich urządzeń wirtualnych względem całkowita ilość miejsca dostępna dla wszystkich urządzeń wirtualnych. **Zainicjowano obsługę administracyjną** odwołuje się do miejsca do magazynowania, który jest przygotowany i przydzielona do użycia, **pozostałe** odwołuje się do pozostałych pojemności, które mogą być udostępniane dla wszystkich urządzeń wirtualnych. **Pozostałe warstwy** pojemność to dostępnej pojemności, które mogą być udostępniane w chmurze, w tym podczas **pozostałe lokalnego** jest pojemność na dyski dołączone do macierzy wirtualnych.
* W **użycia** wykresie widać metryk istotnych dla urządzenia wirtualnego. Można wyświetlić podstawowym magazynem używanym dla wszystkich urządzeń wirtualnych, a także używane przez urządzenia wirtualne w ciągu ostatnich 7 dni, okres domyślny magazyn w chmurze. Użyj **Edytuj** opcję w prawym górnym rogu wykresu, aby wybrać inny czas skalowania.
* **Urządzeń** Kafelek zawiera podsumowanie liczby macierzy wirtualnych w Menedżera urządzeń StorSimple pogrupowane według stanu urządzenia. Kliknij ten Kafelek, aby otworzyć **urządzeń** listy bloku, a następnie kliknij przycisk poszczególnych urządzeń, aby przejść do podsumowanie urządzeń specyficznych dla urządzenia. Można również wykonać konkretne akcje urządzeń w bloku podsumowania danego urządzenia. Aby uzyskać więcej informacji na temat w bloku podsumowanie urządzeń, przejdź do [blok podsumowania urządzenia](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Wyświetlanie dzienników aktywności
Aby wyświetlić różne operacje przeprowadzane w ramach usługi Menedżer urządzeń StorSimple, kliknij **dzienników aktywności** link po lewej stronie bloku podsumowania usługi StorSimple. Spowoduje to przejście do **dzienników aktywności** bloku, w którym będą widoczne podsumowanie ostatnich czynności wykonywane.

![Dzienniki aktywności](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [administrować rozwiązania StorSimple Virtual Array za pomocą lokalnego Interfejsu w przeglądarce](storsimple-ova-web-ui-admin.md).

