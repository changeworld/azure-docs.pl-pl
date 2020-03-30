---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b4c3fcb86fb098263840accc561785a40b767952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183419"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Aby zainstalować aktualizacje za pośrednictwem witryny Azure Portal

1. Przejdź do menedżera urządzeń StorSimple i wybierz pozycję **Urządzenia**. Na liście urządzeń połączonych z usługą wybierz i kliknij urządzenie, które chcesz zaktualizować.

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. W bloku **Ustawienia** kliknij pozycję **Aktualizacje urządzeń**.

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Zostanie wyświetlony komunikat, jeśli aktualizacje oprogramowania są dostępne. Aby sprawdzić dostępność aktualizacji, możesz także kliknąć pozycję **Skanuj**. Zanotuj wersję oprogramowania, którą uruchamiasz. 

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    Użytkownik zostanie powiadomiony o pomyślnym rozpoczęciu i pomyślnym zakończeniu skanowania.

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. Po przeskanowaniu aktualizacji kliknij pozycję **Pobierz aktualizacje**.

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. W **new updates** bloku bloku, przejrzyj informacje o wersji. Należy również pamiętać, że po pobraniu aktualizacji należy potwierdzić instalację. Kliknij przycisk **OK**.

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. Otrzymasz powiadomienie, gdy przekazywanie zostanie uruchomione i zakończy się pomyślnie.

     ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. W bloku **Aktualizacje urządzeń** kliknij pozycję **Instaluj**.

     ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. W bloku **Nowe aktualizacje** zostanie wyświetlone ostrzeżenie, że aktualizacja spowoduje utrudnienia. Ponieważ macierz wirtualna to urządzenie o jednym węźle, urządzenie zostanie ponownie uruchomione po zaktualizowaniu. Spowoduje to zakłócenie wszystkich operacji we/wy. Kliknij przycisk **OK**, aby zainstalować aktualizacje.

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. Otrzymasz powiadomienie o uruchomieniu zadania instalacji.

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  Gdy zadanie instalacji zakończy się pomyślnie, kliknij link **Wyświetl zadanie** w bloku **Aktualizacje urządzeń** w celu monitorowania instalacji. 

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Ta akcja spowoduje przejście do bloku **Zainstaluj aktualizacje.** W tym miejscu możesz wyświetlić szczegółowe informacje o zadaniu.

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Jeśli uruchomiono z macierzy wirtualnej z systemem wersji oprogramowania Update 0.6 (10.0.10293.0), teraz jest uruchomiona aktualizacja 1 i są gotowe. Pozostałe kroki można pominąć. Jeśli rozpoczęto od tablicy wirtualnej z uruchomieniem wersji oprogramowania przed aktualizacją 0.6 (10.0.10293.0), zostaniesz zaktualizowany do aktualizacji 0.6. Zostanie wyświetlony inny komunikat informujący, że aktualizacje są dostępne. Powtórz kroki 4-8, aby zainstalować aktualizację 1.

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

