---
title: Dezaktywowanie i usuwanie urządzenia storsimple serii 8000 | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób usuwania urządzenia StorSimple z usługi, najpierw dezaktywując je, a następnie usuwając.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61481981"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Dezaktywacja i usuwanie urządzenia StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano sposób dezaktywacji i usunięcia urządzenia StorSimple podłączonego do usługi StorSimple Device Manager. Wskazówki zawarte w tym artykule dotyczą tylko urządzeń z serii StorSimple 8000, w tym urządzeń storsimple w chmurze. Jeśli używasz tablicy wirtualnej StorSimple, przejdź do [dezaktywacji i usuń tablicę wirtualną StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Dezaktywacja powoduje zerwanie połączenia między urządzeniem a odpowiednią usługą StorSimple Device Manager. Użytkownik może chcieć wykluczyć urządzenie StorSimple z usługi (na przykład w przypadku wymiany lub uaktualnienia urządzenia lub nieużycia storsimple). Jeśli tak, musisz dezaktywować urządzenie, zanim będzie można je usunąć.

Po dezaktywacji urządzenia wszelkie dane przechowywane lokalnie na urządzeniu nie są już dostępne. Można odzyskać tylko dane skojarzone z urządzeniem, które było przechowywane w chmurze.

> [!WARNING]
> Dezaktywacja jest operacją PERMANENT i nie można jej cofnąć. Dezaktywowanego urządzenia nie można zarejestrować w usłudze StorSimple Device Manager, chyba że zostanie ono zresetowane do ustawień fabrycznych.
>
> Proces przywracania ustawień fabrycznych usuwa wszystkie dane, które były przechowywane lokalnie na urządzeniu. W związku z tym należy zrobić migawkę chmury wszystkich danych przed dezaktywacją urządzenia. Ta migawka w chmurze umożliwia odzyskanie wszystkich danych na późniejszym etapie.

Po przeczytaniu tego samouczka, będzie można:

* Dezaktywuj urządzenie i usuń dane.
* Dezaktywuj urządzenie i zachowaj je.

> [!NOTE]
> Przed dezaktywacją urządzenia fizycznego storsimple lub urządzenia w chmurze, zatrzymać lub usunąć klientów i hostów, które zależą od tego urządzenia.


## <a name="deactivate-and-delete-data"></a>Dezaktywowanie i usuwanie danych

Jeśli chcesz całkowicie usunąć urządzenie i nie chcesz przechowywać danych na urządzeniu, wykonaj następujące kroki.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Aby dezaktywować urządzenie i usunąć dane

1. Przed dezaktywacją urządzenia należy usunąć wszystkie kontenery woluminów (i woluminy) skojarzone z urządzeniem. Kontenery woluminów można usuwać tylko po usunięciu skojarzonych kopii zapasowych.

    > [!NOTE]
    > Przed dezaktywacją urządzenia fizycznego StorSimple lub urządzenia w chmurze upewnij się, że dane z kontenera woluminu usuniętego są faktycznie usuwane z urządzenia. Możesz monitorować wykresy zużycia w chmurze, a gdy zobaczysz spadek użycia chmury z powodu usuniętych kopii zapasowych, możesz przystąpić do dezaktywacji urządzenia. Jeśli dezaktywujesz urządzenie przed wystąpieniem tej kropli, dane zostaną osierocone na koncie magazynu i naliczane są opłaty.

2. Dezaktywuj urządzenie w następujący sposób:
   
   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W bloku **Urządzenia** wybierz urządzenie, które chcesz dezaktywować, kliknij prawym przyciskiem myszy, a następnie kliknij polecenie **Dezaktywuj**.

        ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. W bloku **Dezaktywuj** wpisz nazwę urządzenia, aby potwierdzić, a następnie kliknij przycisk **Dezaktywuj**. Proces dezaktywacji rozpoczyna się i trwa kilka minut.

        ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Po dezaktywacji można całkowicie usunąć urządzenie. Usunięcie urządzenia powoduje usunięcie go z listy urządzeń podłączonych do usługi. Usługa nie może już zarządzać usuniętym urządzeniem. Aby usunąć urządzenie, należy wykonać następujące czynności:
   
   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W bloku **Urządzenia** wybierz dezaktywowane urządzenie, które chcesz usunąć, kliknij prawym przyciskiem myszy, a następnie kliknij polecenie **Usuń**.

        ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. W bloku **Usuń** wpisz nazwę urządzenia, aby potwierdzić, a następnie kliknij przycisk **Usuń**. Usunięcie trwa kilka minut.

        ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Po pomyślnym zakończeniu usuwania użytkownik zostanie o tym powiadomiony. Lista urządzeń jest również aktualizowana w celu odzwierciedlenia usunięcia.

## <a name="deactivate-and-retain-data"></a>Dezaktywowanie i przechowywanie danych

Jeśli chcesz usunąć urządzenie, ale chcesz zachować dane, wykonaj następujące czynności:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Aby dezaktywować urządzenie i zachować dane
1. Dezaktywuj urządzenie. Wszystkie kontenery woluminów i migawki urządzenia pozostają.
   
   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W bloku **Urządzenia** wybierz urządzenie, które chcesz dezaktywować, kliknij prawym przyciskiem myszy, a następnie kliknij polecenie **Dezaktywuj**.

         ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. W bloku **Dezaktywuj** wpisz nazwę urządzenia, aby potwierdzić, a następnie kliknij przycisk **Dezaktywuj**. Proces dezaktywacji rozpoczyna się i trwa kilka minut.

         ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Teraz można w pracy awaryjnej kontenerów woluminów i skojarzonych migawek. Aby uzyskać procedury, przejdź do [trybu failover i odzyskiwania po awarii dla urządzenia StorSimple](storsimple-8000-device-failover-disaster-recovery.md).
3. Po dezaktywacji i pracy awaryjnej można całkowicie usunąć urządzenie. Usunięcie urządzenia powoduje usunięcie go z listy urządzeń podłączonych do usługi. Usługa nie może już zarządzać usuniętym urządzeniem. Aby usunąć urządzenie, wykonaj następujące czynności:
   
   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W bloku **Urządzenia** wybierz dezaktywowane urządzenie, które chcesz usunąć, kliknij prawym przyciskiem myszy, a następnie kliknij polecenie **Usuń**.

       ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. W bloku **Usuń** wpisz nazwę urządzenia, aby potwierdzić, a następnie kliknij przycisk **Usuń**. Usunięcie trwa kilka minut.

       ![Dezaktywacja urządzenia StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Po pomyślnym zakończeniu usuwania użytkownik zostanie o tym powiadomiony. Lista urządzeń jest również aktualizowana w celu odzwierciedlenia usunięcia.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Dezaktywowanie i usuwanie urządzenia w chmurze

W przypadku urządzenia StorSimple Cloud Appliance dezaktywacja z portalu przydziela i usuwa maszynę wirtualną oraz zasoby utworzone podczas jej inicjowania obsługi administracyjnej. Po dezaktywacji urządzenia w chmurze nie można go przywrócić do poprzedniego stanu.

![Dezaktywacja urządzenia StorSimple w chmurze](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Dezaktywacja powoduje następujące działania:

* Urządzenie StorSimple Cloud Appliance zostanie usunięte z usługi.
* Maszyna wirtualna dla urządzenia StorSimple Cloud Appliance zostanie usunięta.
* Dysk systemu operacyjnego i dyski danych utworzone dla urządzenia StorSimple Cloud Appliance są zachowywane. Jeśli nie używasz tych jednostek, należy usunąć je ręcznie.
* Usługa hostowana i sieć wirtualna, które zostały utworzone podczas inicjowania obsługi administracyjnej są zachowywane. Jeśli nie używasz tych jednostek, należy usunąć je ręcznie.
* Migawki chmury utworzone przez Urządzenie StorSimple Cloud Appliance są zachowywane.

Po dezaktywacji urządzenia w chmurze można je usunąć z listy urządzeń. Wybierz dezaktywowane urządzenie, kliknij prawym przyciskiem myszy, a następnie kliknij polecenie **Usuń**. StorSimple powiadamia użytkownika po usunięciu urządzenia i aktualizacji listy urządzeń.

## <a name="next-steps"></a>Następne kroki

* Aby przywrócić ustawienia fabryczne dezaktywowanego urządzenia, przejdź do [pozycji Resetowanie urządzenia do ustawień domyślnych ustawień fabrycznych](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Aby uzyskać pomoc techniczną, [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).
* Aby dowiedzieć się więcej o korzystaniu z usługi StorSimple Device Manager, zobacz [Korzystanie z usługi StorSimple Device Manager do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

