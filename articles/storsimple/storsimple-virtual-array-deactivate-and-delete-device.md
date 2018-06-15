---
title: Dezaktywuj i Usuń Microsoft Azure StorSimple wirtualnego tablicą | Dokumentacja firmy Microsoft
description: Opisuje sposób usuwania urządzenia StorSimple z usługi najpierw dezaktywowanie go, a następnie usuwając go.
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
ms.openlocfilehash: 8dea36f92b034f8c6cdb6875634848d37f4c6606
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875830"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Dezaktywuj i Usuń macierz wirtualnego StorSimple

## <a name="overview"></a>Omówienie

Po dezaktywowaniu tablicą wirtualnego StorSimple przerwaniu połączenia między urządzeniem i odpowiedniej usługi Menedżer StorSimple urządzenia. Ten samouczek wyjaśnia, jak:

* Dezaktywować urządzenie 
* Usuwanie urządzenia dezaktywowany

Informacje przedstawione w tym artykule dotyczą tylko tablice wirtualnego StorSimple. Informacje w serii 8000, przejdź do sposobu [dezaktywację lub usunięcie urządzenia](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Kiedy należy dezaktywować?

Dezaktywacja jest operacją trwałe i nie można cofnąć. Nie można zarejestrować ponownie dezaktywować urządzenie w usłudze Menedżer StorSimple urządzenia. Może być konieczne, Dezaktywuj i Usuń macierz wirtualnego StorSimple w następujących scenariuszach:

* **Planowana praca awaryjna** : urządzenie jest w trybie online i jest planowane w tryb failover urządzenia. Jeśli planowane jest uaktualnienie do większych urządzenia, konieczne może być awaryjnie urządzenia. Po przekazania własności danych i przejścia w tryb failover, urządzenia są automatycznie usuwane.
* **Nieplanowane przełączenie awaryjne** : urządzenie jest w trybie offline, należy przełączyć na urządzeniu. W tym scenariuszu mogą wystąpić podczas awarii, gdy istnieje przerwa w centrum danych i urządzenie podstawowej nie działa. Zaplanuj urządzenia na urządzeniu dodatkowej pracy awaryjnej. Po przekazania własności danych i przejścia w tryb failover, urządzenia są automatycznie usuwane.
* **Likwidowanie** : Aby zlikwidować urządzenia. Należy najpierw dezaktywować urządzenie, a następnie usuń go. Po dezaktywacji urządzenia może już uzyskać dostępu do żadnych danych, który jest przechowywany lokalnie. Można jedynie dostępu i odzyskiwanie danych przechowywanych w chmurze. Jeśli planujesz przechowywanie danych urządzenia po dezaktywacji, następnie należy podjąć migawkę chmury dla wszystkich danych przed dezaktywacją urządzenia. Ta migawka w chmurze umożliwia odzyskanie wszystkich danych w późniejszym terminie.

## <a name="deactivate-a-device"></a>Dezaktywować urządzenie

Aby dezaktywować urządzenie, wykonaj następujące kroki.

#### <a name="to-deactivate-the-device"></a>Aby dezaktywować urządzenie

1. W usłudze, przejdź do **zarządzania > urządzenia**. W **urządzeń** bloku, kliknij i wybierz urządzenia, które chcesz zdezaktywować.
   
    ![Wybierz urządzenie, aby zdezaktywować](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. W Twojej **pulpitu nawigacyjnego urządzenia** bloku, kliknij przycisk **... Więcej** i wybierz z listy, **Dezaktywuj**.
   
    ![Kliknij przycisk Dezaktywuj](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. W **Dezaktywuj** bloku, wpisz nazwę urządzenia, a następnie kliknij przycisk **Dezaktywuj**. 
   
    ![Upewnij się, Dezaktywuj](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Proces Dezaktywuj uruchamia i może zająć kilka minut.
   
    ![Dezaktywuj w toku](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Po dezaktywacji Odświeża listę urządzeń.
   
    ![Dezaktywuj ukończone](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Można teraz usunąć to urządzenie.

## <a name="delete-the-device"></a>Usuwanie urządzenia

Urządzenie ma najpierw są nieaktywne go usunąć. Usuwanie urządzenia spowoduje usunięcie jej z listy urządzeń połączonych z usługą. Usługę można zarządzać nie usunięto urządzenie. Dane skojarzone z danym urządzeniem jednak pozostaje w chmurze. Te dane zostały następnie naliczone opłaty.

Aby usunąć urządzenie, wykonaj następujące kroki.

#### <a name="to-delete-the-device"></a>Aby usunąć urządzenie

1. W Menedżerze urządzeń StorSimple, przejdź do **zarządzania > urządzenia**. W **urządzeń** bloku, wybierz dezaktywować urządzenie, które chcesz usunąć.
2. W **pulpitu nawigacyjnego urządzenia** bloku, kliknij przycisk **... Więcej** , a następnie kliknij przycisk **usunąć**.
   
   ![Wybierz urządzenie, aby usunąć](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. W **usunąć** bloku, wpisz nazwę urządzenia, aby potwierdzić zamiar usunięcia, a następnie kliknij przycisk **usunąć**. Usunięcie urządzenia nie powoduje usunięcia danych powiązanych z urządzeniem w chmurze. 
   
   ![Potwierdzenie usunięcia](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Usuwanie uruchamia i może zająć kilka minut.
   
   ![Usuwanie w toku](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Po usunięciu urządzenia, można wyświetlić zaktualizowaną listę urządzeń.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat sposobu pracy awaryjnej, przejdź do [trybu Failover i odzyskiwania po awarii macierzy wirtualnego StorSimple](storsimple-virtual-array-failover-dr.md).

* Aby dowiedzieć się więcej o sposobie używania usługi Menedżer StorSimple urządzeń, przejdź do [zarządzać tablica wirtualnego StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-virtual-array-manager-service-administration.md). 

