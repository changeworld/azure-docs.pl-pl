---
title: Dezaktywowanie i usuwanie tablicy wirtualnej Microsoft Azure StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób usuwania urządzenia StorSimple z usługi, najpierw dezaktywując je, a następnie usuwając.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580602"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Dezaktywacja i usuwanie macierzy wirtualnej usługi StorSimple

## <a name="overview"></a>Omówienie

Dezaktywacja tablicy wirtualnej StorSimple umożliwia przerwanie połączenia między urządzeniem a odpowiednią usługą StorSimple Device Manager. W tym samouczku wyjaśniono:

* Dezaktywowanie urządzenia 
* Usuwanie dezaktywowanego urządzenia

Informacje zawarte w tym artykule dotyczą tylko tablic wirtualnych StorSimple. Aby uzyskać informacje na temat serii 8000, przejdź do sposobu [dezaktywacji lub usunięcia urządzenia](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Kiedy dezaktywować?

Dezaktywacja jest operacją PERMANENT i nie można jej cofnąć. Nie można ponownie zarejestrować dezaktywowanego urządzenia w usłudze StorSimple Device Manager. Może być konieczne dezaktywację i usunięcie tablicy wirtualnej StorSimple w następujących scenariuszach:

* **Planowane tryb failover:** Urządzenie jest w trybie online i planujesz awaryjnie nad urządzeniem. Jeśli planujesz uaktualnienie do większego urządzenia, może być konieczne przewijanie awaryjne urządzenia. Po przeniesieniu własności danych i zakończeniu pracy awaryjnej urządzenie źródłowe jest automatycznie usuwane.
* **Nieplanowane tryb failover:** Urządzenie jest w trybie offline i musisz wykonać urządzenie w trybie failover. Ten scenariusz może wystąpić podczas awarii, gdy występuje awaria w centrum danych i urządzenia podstawowego jest w dół. Planujesz awaryjnie urządzenie do urządzenia pomocniczego. Po przeniesieniu własności danych i zakończeniu pracy awaryjnej urządzenie źródłowe jest automatycznie usuwane.
* **Likwidacja:** Chcesz zlikwidować urządzenie. Wymaga to najpierw dezaktywacji urządzenia, a następnie usunięcia go. Po dezaktywacji urządzenia nie można już uzyskać dostępu do żadnych danych przechowywanych lokalnie. Dostęp do danych przechowywanych w chmurze i odzyskiwanie ich jest możliwe. Jeśli planujesz zachować dane urządzenia po dezaktywacji, przed dezaktywacją urządzenia należy zrobić migawkę wszystkich danych w chmurze. Ta migawka w chmurze umożliwia odzyskanie wszystkich danych na późniejszym etapie.

## <a name="deactivate-a-device"></a>Dezaktywowanie urządzenia

Aby dezaktywować urządzenie, wykonaj następujące czynności.

#### <a name="to-deactivate-the-device"></a>Aby wyłączyć urządzenie

1. W usłudze przejdź do **witryny Zarządzanie > urządzeniami**. W bloku **Urządzenia** kliknij i wybierz urządzenie, które chcesz dezaktywować.
   
    ![Wybierz urządzenie, aby je dezaktywować](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. W bloku **pulpitu nawigacyjnego urządzenia** kliknij **... Więcej** i z listy wybierz pozycję **Dezaktywuj**.
   
    ![Kliknij dezaktywuj](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. W bloku **Dezaktywuj** wpisz nazwę urządzenia, a następnie kliknij przycisk **Dezaktywuj**. 
   
    ![Potwierdź dezaktywację](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Proces dezaktywacji rozpoczyna się i trwa kilka minut.
   
    ![Dezaktywacja w toku](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Po dezaktywacji lista urządzeń zostanie odświeżona.
   
    ![Dezaktywacja kompletna](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Teraz możesz usunąć to urządzenie.

## <a name="delete-the-device"></a>Usuwanie urządzenia

Urządzenie musi najpierw zostać dezaktywowane, aby je usunąć. Usunięcie urządzenia powoduje usunięcie go z listy urządzeń podłączonych do usługi. Usługa nie może już zarządzać usuniętym urządzeniem. Dane skojarzone z urządzeniem pozostają jednak w chmurze. Te dane następnie naliczane są opłaty.

Aby usunąć urządzenie, wykonaj następujące czynności.

#### <a name="to-delete-the-device"></a>Aby usunąć urządzenie

1. W Menedżerze urządzeń StorSimple przejdź do **witryny Zarządzanie > urządzeniami**. W bloku **Urządzenia** wybierz wyłączone urządzenie, które chcesz usunąć.
2. W bloku **pulpitu nawigacyjnego urządzenia** kliknij **... Więcej,** a następnie kliknij przycisk **Usuń**.
   
   ![Wybierz urządzenie do usunięcia](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. W bloku **Usuń** wpisz nazwę urządzenia, aby potwierdzić usunięcie, a następnie kliknij przycisk **Usuń**. Usunięcie urządzenia nie powoduje usunięcia danych w chmurze skojarzonych z urządzeniem. 
   
   ![Potwierdzenie usunięcia](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Usunięcie rozpoczyna się i trwa kilka minut.
   
   ![Usuwanie w toku](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Po usunięciu urządzenia można wyświetlić zaktualizowaną listę urządzeń.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje dotyczące pracy awaryjnej, przejdź do [trybu failover i odzyskiwania po awarii tablicy wirtualnej StorSimple](storsimple-virtual-array-failover-dr.md).

* Aby dowiedzieć się więcej o korzystaniu z usługi StorSimple Device Manager, przejdź do [usługi Menedżer urządzeń StorSimple do administrowania macierzą wirtualną StorSimple](storsimple-virtual-array-manager-service-administration.md). 

