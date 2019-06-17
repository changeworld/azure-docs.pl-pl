---
title: Dezaktywacja i usuwanie Microsoft Azure StorSimple Virtual Array | Dokumentacja firmy Microsoft
description: Zawiera opis procedury usuwania urządzenia StorSimple z usługą najpierw dezaktywowanie go, a następnie usuwając go.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: bb1a56d204a46f89213f20e317494120f0ea565e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60580602"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Dezaktywacja i usuwanie macierzy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

Po dezaktywowaniu rozwiązania StorSimple Virtual Array jest przerwanie połączenia między urządzeniem i odpowiednie usługi Menedżer urządzeń StorSimple. W tym samouczku wyjaśniono:

* Dezaktywacja urządzenia 
* Usuń dezaktywowanego urządzenia

Informacje przedstawione w tym artykule dotyczy tylko macierze wirtualne StorSimple. Aby uzyskać informacji na temat serii 8000, przejdź do sposobu [dezaktywować lub usunąć urządzenie](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Kiedy należy dezaktywować?

Dezaktywacja jest operacją NIEODWRACALNĄ i nie można cofnąć. Nie można ponownie zarejestrować dezaktywowanego urządzenia w usłudze Menedżer urządzeń StorSimple. Może być konieczne Dezaktywacja i usuwanie rozwiązania StorSimple Virtual Array w następujących scenariuszach:

* **Zaplanowanym powrocie po awarii** : Urządzenie jest w trybie online i planowane jest w trybie Failover urządzenia. Jeśli planowane jest uaktualnienie na większym urządzeniu, może być konieczne w trybie Failover urządzenia. Po przekazaniu prawa własności do danych i pracy w trybie failover zostało ukończone, urządzenie źródłowe jest automatycznie usuwany.
* **Nieplanowany tryb failover** : Urządzenie jest w trybie offline i należy do trybu failover na urządzeniu. Ten scenariusz może wystąpić podczas awarii, gdy nastąpi awaria w centrum danych, a urządzenie podstawowej nie działa. Planowane przełączanie urządzenia do dodatkowej urządzenia trybu failover. Po przekazaniu prawa własności do danych i pracy w trybie failover zostało ukończone, urządzenie źródłowe jest automatycznie usuwany.
* **Likwidowanie** : Użytkownik chce zlikwidować urządzenia. Należy najpierw dezaktywować urządzenie, a następnie usuń go. Po dezaktywacji urządzenia można nie uzyskać dostępu do żadnych danych, który jest przechowywany lokalnie. Można jedynie dostępu i odzyskanie danych przechowywanych w chmurze. Jeśli planujesz przechowywać dane urządzenie po dezaktywacji, następnie należy podjąć migawkę w chmurze wszystkie swoje dane przed dezaktywacją urządzenia. Migawka w chmurze umożliwia odzyskanie wszystkich danych na późniejszym etapie.

## <a name="deactivate-a-device"></a>Dezaktywacja urządzenia

Aby dezaktywować urządzenie, wykonaj następujące czynności.

#### <a name="to-deactivate-the-device"></a>Aby dezaktywować urządzenie

1. W usłudze, przejdź do **zarządzania > urządzenia**. W **urządzeń** bloku kliknij opcję i wybierz urządzenie, które chcesz zdezaktywować.
   
    ![Wybierz urządzenie, aby zdezaktywować](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. W swojej **pulpit nawigacyjny urządzeń** bloku kliknij **... Więcej** i wybierz z listy, **Dezaktywuj**.
   
    ![Kliknij przycisk Dezaktywuj](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. W **Dezaktywuj** bloku, wpisz nazwę urządzenia, a następnie kliknij przycisk **Dezaktywuj**. 
   
    ![Upewnij się, Dezaktywuj](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Rozpocznie się proces Dezaktywuj, w którym zostanie zajmuje kilka minut.
   
    ![Dezaktywuj w toku](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Po dezaktywacji, lista urządzeń zostanie odświeżona.
   
    ![Dezaktywuj ukończone](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Możesz teraz usunąć to urządzenie.

## <a name="delete-the-device"></a>Usuń urządzenie

Urządzenie ma go najpierw dezaktywować go usunąć. Usuwanie urządzenia spowoduje usunięcie jej z listy urządzeń podłączonych do usługi. Usługa nie będzie można następnie zarządzać usuniętego urządzenia. Dane skojarzone z urządzeniem, jednak pozostaje w chmurze. Te dane zostały następnie naliczone opłaty.

Aby usunąć urządzenie, wykonaj następujące czynności.

#### <a name="to-delete-the-device"></a>Aby usunąć urządzenie

1. W Menedżerze urządzeń StorSimple, przejdź do **zarządzania > urządzenia**. W **urządzeń** bloku wybranie zdezaktywowanego urządzenia, które chcesz usunąć.
2. W **pulpit nawigacyjny urządzeń** bloku kliknij **... Więcej** a następnie kliknij przycisk **Usuń**.
   
   ![Wybierz urządzenie, aby usunąć](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. W **Usuń** bloku, wpisz nazwę urządzenia, aby potwierdzić usunięcie, a następnie kliknij przycisk **Usuń**. Usunięcie urządzenia nie powoduje usunięcia danych skojarzone z urządzeniem w chmurze. 
   
   ![Potwierdzenie usunięcia](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Usuwanie zaczyna się i zajmuje kilka minut.
   
   ![Usuwanie w toku](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Po usunięciu urządzenia można wyświetlić zaktualizowaną listę urządzeń.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać informacje na temat sposobu pracy awaryjnej, przejdź do [trybu Failover i odzyskiwanie po awarii z rozwiązania StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md).

* Aby dowiedzieć się więcej o sposobie używania usługi Menedżer urządzeń StorSimple, przejdź do [korzystać z usługi Menedżer urządzeń StorSimple do administrowania rozwiązania StorSimple Virtual Array](storsimple-virtual-array-manager-service-administration.md). 

