---
title: Dezaktywuj i usuwanie urządzenia z serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Opisuje sposób usuwania urządzenia StorSimple z usługi najpierw dezaktywowanie go, a następnie usuwając go.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 3c00867a29cf8343a57e74e2aabe3971ae6837af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874969"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Dezaktywuj i usuwanie urządzenia StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano sposób zdezaktywować i usunąć urządzenia StorSimple, która jest połączona z usługą Menedżera urządzeń StorSimple. Wskazówki zawarte w tym artykule dotyczą tylko urządzeń z serii StorSimple 8000 tym urządzenia StorSimple w chmurze. Jeśli korzystasz z tablicą wirtualnego StorSimple, następnie przejdź do [Dezaktywuj i delete dla tablicy wirtualnego StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Dezaktywacja serwery połączenia między urządzeniem i odpowiedniej usługi Menedżer StorSimple urządzenia. Możesz wykonać urządzenia StorSimple poza usługi (na przykład, jeśli są zastępowanie lub uaktualnianie urządzenia lub jeśli już używasz StorSimple). Jeśli tak, należy dezaktywować urządzenie, aby można było go usunąć.

Po dezaktywacji urządzenia żadnych danych, które były przechowywane lokalnie na urządzeniu nie jest już dostępny. Można odzyskać tylko dane skojarzone z urządzenia, które były przechowywane w chmurze.

> [!WARNING]
> Dezaktywacja jest operacją trwałe i nie można cofnąć. Nie można zarejestrować dezaktywować urządzenie w usłudze Menedżer urządzeń StorSimple, o ile zostanie zresetowane do ustawień fabrycznych.
>
> Fabryka zresetować proces usuwania wszystkich danych, które były przechowywane lokalnie na urządzeniu. W związku z tym należy utworzyć migawkę chmury wszystkich danych przed dezaktywacją urządzenia. Ta migawka w chmurze umożliwia odzyskanie wszystkich danych w późniejszym terminie.

Po przeczytaniu tego samouczka, będą mieć możliwość:

* Dezaktywować urządzenie i usunięcie danych.
* Dezaktywować urządzenie i zachowywać dane.

> [!NOTE]
> Przed dezaktywacją urządzenia fizycznego StorSimple lub urządzenia chmury zatrzymać lub usunąć klientów i hosty, które są zależne od tego urządzenia.


## <a name="deactivate-and-delete-data"></a>Dezaktywuj i usuwanie danych

Jeśli planuje się całkowicie usuwania urządzenia i nie chcesz zachować dane na urządzeniu, następnie wykonaj następujące kroki.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Aby dezaktywować urządzenie i usuwanie danych

1. Przed dezaktywacją urządzenia, należy usunąć wszystkie wolumin kontenerów (oraz na woluminach) skojarzone z urządzeniem. Kontenery woluminów można usunąć tylko po usunięciu skojarzonych kopii zapasowych.

    > [!NOTE]
    > Przed dezaktywacją urządzenia fizycznego StorSimple lub urządzenia chmury, upewnij się, że dane z kontenera woluminów usunięto faktycznie jest usuwany z urządzenia. Można monitorować, wykresy użycia chmury i po wyświetleniu użycia chmury porzucić ze względu na tworzenie kopii zapasowych, które zostały usunięte, następnie przejdź do dezaktywować urządzenie. Dezaktywacja urządzenia przed wystąpieniem tego Porzuć danych jest skrętki na koncie magazynu i zostały naliczone opłaty.

2. Dezaktywować urządzenie w następujący sposób:
   
   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W **urządzeń** bloku, wybierz urządzenie, które chcesz zdezaktywować, kliknij prawym przyciskiem myszy, a następnie kliknij przycisk **Dezaktywuj**.

        ![Dezaktywuj urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. W **Dezaktywuj** bloku, wpisz nazwę urządzenia, aby upewnić się, a następnie kliknij przycisk **Dezaktywuj**. Proces Dezaktywuj uruchamia i może zająć kilka minut.

        ![Dezaktywuj urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Po dezaktywacji możesz całkowicie usunąć urządzenia. Usuwanie urządzenia spowoduje usunięcie jej z listy urządzeń połączonych z usługą. Usługę można zarządzać nie usunięto urządzenie. Aby usunąć urządzenie, wykonaj następujące kroki:
   
   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W **urządzeń** bloku, wybierz dezaktywować urządzenie, które chcesz usunąć, kliknij prawym przyciskiem myszy, a następnie kliknij przycisk **usunąć**.

        ![Dezaktywuj urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. W **usunąć** bloku, wpisz nazwę urządzenia, aby upewnić się, a następnie kliknij przycisk **usunąć**. Usuwanie może zająć kilka minut.

        ![Dezaktywuj urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Po usunięcie zostało ukończone pomyślnie, zostanie wyświetlony. Lista urządzeń aktualizuje również, aby odzwierciedlić usunięcia.

## <a name="deactivate-and-retain-data"></a>Dezaktywuj i Zachowaj dane

Jeśli są zainteresowani usuwania urządzenia, ale mają być przechowywane dane, następnie wykonaj następujące kroki:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Aby dezaktywować urządzenie i zachowywać dane
1. Dezaktywować urządzenie. Kontenery woluminów i migawek urządzenia pozostają.
   
   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W **urządzeń** bloku, wybierz urządzenie, które chcesz zdezaktywować, kliknij prawym przyciskiem myszy, a następnie kliknij przycisk **Dezaktywuj**.

         ![Dezaktywuj urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. W **Dezaktywuj** bloku, wpisz nazwę urządzenia, aby upewnić się, a następnie kliknij przycisk **Dezaktywuj**. Proces Dezaktywuj uruchamia i może zająć kilka minut.

         ![Dezaktywuj urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Możesz teraz w trybie Failover kontenery woluminów i skojarzone migawki. Procedury, przejdź do [trybu Failover i odzyskiwania po awarii dla urządzenia StorSimple](storsimple-8000-device-failover-disaster-recovery.md).
3. Po dezaktywacji i trybu failover można całkowicie usunąć urządzenie. Usuwanie urządzenia spowoduje usunięcie jej z listy urządzeń połączonych z usługą. Usługę można zarządzać nie usunięto urządzenie. Aby usunąć urządzenie, wykonaj następujące kroki:
   
   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W **urządzeń** bloku, wybierz dezaktywować urządzenie, które chcesz usunąć, kliknij prawym przyciskiem myszy, a następnie kliknij przycisk **usunąć**.

       ![Dezaktywuj urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. W **usunąć** bloku, wpisz nazwę urządzenia, aby upewnić się, a następnie kliknij przycisk **usunąć**. Usuwanie może zająć kilka minut.

       ![Dezaktywuj urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Po usunięcie zostało ukończone pomyślnie, zostanie wyświetlony. Lista urządzeń aktualizuje również, aby odzwierciedlić usunięcia.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Dezaktywuj i usuń urządzenia chmury

Urządzenia StorSimple chmury dezaktywacji z portalu cofa alokację oraz usuwa maszynę wirtualną i zasoby utworzone podczas inicjowania jej obsługi. Po dezaktywacji urządzenia w chmurze nie można go przywrócić do poprzedniego stanu.

![Dezaktywuj urządzenia StorSimple chmury](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Dezaktywacja spowoduje następujące akcje:

* Urządzenia StorSimple w chmurze jest wyłączany.
* Maszyna wirtualna dla urządzenia StorSimple w chmurze została usunięta.
* Dysk systemu operacyjnego i dyski danych utworzone dla urządzenia StorSimple w chmurze są usuwane.
* Usługa hostowana i sieci wirtualnej, które zostały utworzone podczas inicjowania obsługi administracyjnej są zachowywane. Jeśli nie używasz te jednostki, należy usunąć je ręcznie.
* Migawki w chmurze utworzone przez urządzenia StorSimple w chmurze są zachowywane.

Po dezaktywacji urządzenia chmury, należy ją usunąć z listy urządzeń. Wybierz dezaktywować urządzenie, kliknij prawym przyciskiem myszy, a następnie kliknij **usunąć**. StorSimple powiadamia należy po usunięciu urządzenia i listy urządzeń.

## <a name="next-steps"></a>Następne kroki

* Aby przywrócić dezaktywować urządzenie do ustawień fabrycznych, przejdź do [zresetowania urządzenia do domyślnych ustawień fabrycznych](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Aby uzyskać pomoc techniczną [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Aby dowiedzieć się więcej o sposobie używania usługi Menedżer StorSimple urządzeń, przejdź do [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

