---
title: Dezaktywacja i usuwanie urządzenia StorSimple 8000 series | Dokumentacja firmy Microsoft
description: Zawiera opis procedury usuwania urządzenia StorSimple z usługą najpierw dezaktywowanie go, a następnie usuwając go.
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
ms.date: 07/23/2018
ms.author: alkohli
ms.openlocfilehash: 116ac5c4efda87b5d16336dd326d516299f6955d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481981"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Dezaktywacja i usuwanie urządzenia StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano sposób Dezaktywacja i usuwanie urządzenia StorSimple, która jest połączona z usługą Menedżera urządzeń StorSimple. Wskazówki zawarte w tym artykule dotyczą tylko urządzeń z serii StorSimple 8000 w tym urządzeń StorSimple w chmurze. Jeśli używasz rozwiązania StorSimple Virtual Array, następnie przejdź do strony [dezaktywowanie i usuwanie rozwiązania StorSimple Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md).

Dezaktywacja wcześniej połączenie między urządzeniem i odpowiednie usługi Menedżer urządzeń StorSimple. Możesz wykonać na urządzeniu StorSimple, usługi, (na przykład, jeśli są zastępowanie lub uaktualnianie urządzenia lub jeśli już używasz usługi StorSimple). Jeśli tak, musisz dezaktywować urządzenie przed jego usunięciem.

Po dezaktywacji urządzenia żadnych danych, który został zapisany lokalnie na urządzeniu nie jest już dostępny. Można odzyskać tylko dane, które są skojarzone z urządzenia, które są przechowywane w chmurze.

> [!WARNING]
> Dezaktywacja jest operacją NIEODWRACALNĄ i nie można cofnąć. Dezaktywowanego urządzenia nie można zarejestrować w usłudze Menedżer urządzeń StorSimple, o ile zostanie zresetowane do ustawień fabrycznych.
>
> Resetowanie do ustawień fabrycznych procesu usuwa wszystkie dane, które są przechowywane lokalnie na urządzeniu z systemem. W związku z tym należy wykonać migawkę wszystkich danych w chmurze, przed dezaktywacją urządzenia. Migawka w chmurze umożliwia odzyskanie wszystkich danych na późniejszym etapie.

Po przeczytaniu tego samouczka, będzie mieć możliwość:

* Dezaktywacja urządzenia i usuwanie danych.
* Dezaktywuj urządzenie i przechowywania danych.

> [!NOTE]
> Przed dezaktywacją urządzenia fizycznego StorSimple lub urządzenia w chmurze, zatrzymać lub usunąć klientów i hosty, które są zależne od tego urządzenia.


## <a name="deactivate-and-delete-data"></a>Dezaktywacja i usuwanie danych

Jeśli interesują Cię całkowicie usunięciem urządzenia, a nie chcesz przechowywać dane na urządzeniu, następnie wykonaj następujące czynności.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Aby dezaktywować urządzenie i usunąć dane

1. Przed dezaktywacją urządzenia, należy usunąć wszystkich woluminów kontenerów (i woluminy) skojarzone z urządzeniem. Kontenery woluminów można usunąć tylko wtedy, gdy została usunięta skojarzonych kopii zapasowych.

    > [!NOTE]
    > Przed dezaktywacją urządzenia fizycznego StorSimple lub urządzenia w chmurze, upewnij się, że dane z kontenera woluminów usunięto faktycznie jest usuwany z urządzenia. Można monitorować, wykresy użycia chmury i wyświetlenie użycia chmury, Porzuć ze względu na kopie zapasowe, które zostały usunięte następnie można przystąpić do dezaktywowania urządzenia. Dezaktywacja urządzenia przed wystąpieniem tego spadku dane jest skrętki na koncie magazynu i zostały naliczone opłaty.

2. Dezaktywacja urządzenia w następujący sposób:
   
   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W **urządzeń** bloku wybierz urządzenie, które chcesz zdezaktywować, kliknij prawym przyciskiem myszy, a następnie kliknij **Dezaktywuj**.

        ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. W **Dezaktywuj** bloku, wpisz nazwę urządzenia, aby upewnić się, a następnie kliknij przycisk **Dezaktywuj**. Rozpocznie się proces Dezaktywuj, w którym zostanie zajmuje kilka minut.

        ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Po dezaktywacji możesz całkowicie usunąć urządzenia. Usuwanie urządzenia spowoduje usunięcie jej z listy urządzeń podłączonych do usługi. Usługa nie będzie można następnie zarządzać usuniętego urządzenia. Aby usunąć urządzenie, wykonaj następujące kroki:
   
   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W **urządzeń** bloku wybranie zdezaktywowanego urządzenia, które chcesz usunąć, kliknij prawym przyciskiem myszy, a następnie kliknij **Usuń**.

        ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. W **Usuń** bloku, wpisz nazwę urządzenia, aby upewnić się, a następnie kliknij przycisk **Usuń**. Usuwanie zajmuje kilka minut.

        ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Po zakończeniu usuwania pomyślnie otrzymasz powiadomienie. Lista urządzeń aktualizuje również w celu odzwierciedlenia usunięcia.

## <a name="deactivate-and-retain-data"></a>Dezaktywacja i przechowywanie danych

Jeśli są zainteresowani usunięciem urządzenia, ale chcesz przechowywać dane, następnie wykonaj następujące czynności:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Aby dezaktywować urządzenie i przechowywania danych
1. Dezaktywacja urządzenia. Wszystkie kontenery woluminów i migawek urządzenia pozostaną.
   
   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W **urządzeń** bloku wybierz urządzenie, które chcesz zdezaktywować, kliknij prawym przyciskiem myszy, a następnie kliknij **Dezaktywuj**.

         ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. W **Dezaktywuj** bloku, wpisz nazwę urządzenia, aby upewnić się, a następnie kliknij przycisk **Dezaktywuj**. Rozpocznie się proces Dezaktywuj, w którym zostanie zajmuje kilka minut.

         ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Możesz teraz przełączać awaryjnie kontenery woluminów i skojarzone migawki. Aby uzyskać procedury, przejdź do [trybu Failover i odzyskiwanie po awarii dla urządzenia StorSimple](storsimple-8000-device-failover-disaster-recovery.md).
3. Po dezaktywacji i trybu failover możesz całkowicie usunąć urządzenia. Usuwanie urządzenia spowoduje usunięcie jej z listy urządzeń podłączonych do usługi. Usługa nie będzie można następnie zarządzać usuniętego urządzenia. Aby usunąć urządzenie, wykonaj następujące czynności:
   
   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W **urządzeń** bloku wybranie zdezaktywowanego urządzenia, które chcesz usunąć, kliknij prawym przyciskiem myszy, a następnie kliknij **Usuń**.

       ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. W **Usuń** bloku, wpisz nazwę urządzenia, aby upewnić się, a następnie kliknij przycisk **Usuń**. Usuwanie zajmuje kilka minut.

       ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Po zakończeniu usuwania pomyślnie otrzymasz powiadomienie. Lista urządzeń aktualizuje również w celu odzwierciedlenia usunięcia.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Dezaktywacja i usuwanie urządzenia w chmurze

Dla urządzenia StorSimple w chmurze dezaktywacji, z poziomu portalu powoduje cofnięcie przydziału i usuwa maszyny wirtualne i zasoby utworzone podczas jej aprowizacji. Po dezaktywacji urządzenia w chmurze nie można go przywrócić do poprzedniego stanu.

![Dezaktywacja urządzenia StorSimple w chmurze](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Powoduje dezaktywację następujące akcje:

* Urządzenie StorSimple w chmurze jest wyłączany.
* Maszyna wirtualna, dla urządzenia StorSimple w chmurze zostanie usunięta.
* Dysk systemu operacyjnego i dyski danych utworzone dla urządzenia StorSimple w chmurze zostaną zachowane. Jeśli nie używasz tych jednostek, należy je usunąć ręcznie.
* Hostowana usługa i sieć wirtualną, które zostały utworzone podczas inicjowania obsługi zostaną zachowane. Jeśli nie używasz tych jednostek, należy je usunąć ręcznie.
* Migawki w chmurze utworzone przez urządzenie StorSimple w chmurze zostaną zachowane.

Po dezaktywacji urządzenia w chmurze, możesz go usunąć z listy urządzeń. Wybierz dezaktywowanego urządzenia, kliknij prawym przyciskiem myszy, a następnie kliknij **Usuń**. StorSimple powiadamia, należy po usunięciu urządzenia i lista urządzeń jest aktualizowana.

## <a name="next-steps"></a>Kolejne kroki

* Aby przywrócić dezaktywowanego urządzenia do ustawień fabrycznych, przejdź do [zresetowania urządzenia do domyślnych ustawień fabrycznych](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Aby uzyskać pomoc techniczną [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Aby dowiedzieć się więcej o sposobie używania usługi Menedżer urządzeń StorSimple, przejdź do [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

