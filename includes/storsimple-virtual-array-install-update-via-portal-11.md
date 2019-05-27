---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 65d5a88f7b5d059deb633f062639e455c64ef2f7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66167062"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Aby zainstalować aktualizacje za pośrednictwem witryny Azure Portal

1. Przejdź do menedżera urządzeń StorSimple i wybierz pozycję **Urządzenia**. Na liście urządzeń połączonych z usługą wybierz i kliknij urządzenie, które chcesz zaktualizować.

2. W obszarze **ustawienia**, kliknij przycisk **aktualizacji urządzenia**.  

3. Zostanie wyświetlony komunikat, jeśli aktualizacje oprogramowania są dostępne. Aby sprawdzić dostępność aktualizacji, możesz także kliknąć pozycję **Skanuj**. Zanotuj wersji oprogramowania, które są uruchomione. 

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    Otrzymasz powiadomienie, gdy skanowanie zostanie uruchomione i zakończy się pomyślnie.
 
4. Po przeskanowaniu aktualizacji kliknij pozycję **Pobierz aktualizacje**. W obszarze **nowe aktualizacje**, zapoznaj się z informacjami o wersji. Należy również zauważyć, po pobraniu aktualizacji, należy sprawdzić, czy. Kliknij przycisk **OK**.

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    Otrzymasz powiadomienie, gdy przekazywanie zostanie uruchomione i zakończy się pomyślnie.

5. W obszarze **aktualizacji urządzenia**, kliknij przycisk **zainstalować**.

     ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. W obszarze **nowe aktualizacje**, zostanie wyświetlone ostrzeżenie, że aktualizacja ma szkodliwe. Ponieważ macierz wirtualna to urządzenie o jednym węźle, urządzenie zostanie ponownie uruchomione po zaktualizowaniu. Spowoduje to zakłócenie wszystkich operacji we/wy. Kliknij przycisk **OK**, aby zainstalować aktualizacje.

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    Otrzymasz powiadomienie o uruchomieniu zadania instalacji.

7.  Po pomyślnym ukończeniu działania zadania instalacji, kliknij przycisk **Wyświetl zadanie** łącza. Ta akcja spowoduje przejście do **Zainstaluj aktualizacje** bloku. W tym miejscu możesz wyświetlić szczegółowe informacje o zadaniu. 

    ![aktualizowanie urządzenia](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Jeśli rozpoczęto macierz wirtualna z oprogramowaniem w wersji Update 1 (10.0.10296.0), są uruchomione 1.1 aktualizacji i gotowe. Możesz pominąć pozostałe kroki. 

    Jeśli rozpoczęto macierz wirtualna z oprogramowaniem w wersji Update 0.6 (10.0.10293.0), teraz są aktualizowane na aktualizację 1.0. Wyświetlony inny komunikat wskazujący, że są dostępne aktualizacje. Powtórz kroki 4 – 7, aby zainstalować aktualizację 1.1.

    

