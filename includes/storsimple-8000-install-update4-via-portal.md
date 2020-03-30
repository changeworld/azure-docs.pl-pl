---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a3ccf76b2722c04a9353fcc7020ff1387bc454c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183434"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Aby zainstalować aktualizację z witryny Azure Portal

1. Na stronie usługi StorSimple wybierz swoje urządzenie.

    ![Wybierz urządzenie](./media/storsimple-8000-install-update4-via-portal/update1.png)

2. Przejdź do **ustawień** > urządzenia**Aktualizacje urządzenia**.

    ![Kliknij pozycję Aktualizacje urządzenia](./media/storsimple-8000-install-update4-via-portal/update2.png)

2. Jeśli dostępne są nowe aktualizacje, zostanie wyświetlone powiadomienie. Alternatywnie w bloku **Aktualizacje urządzenia** kliknij pozycję **Skanuj aktualizacje**. Zostanie utworzone zadanie skanowania dostępnych aktualizacji. Otrzymasz powiadomienie, gdy zadanie zostanie pomyślnie ukończone.

    ![Kliknij pozycję Aktualizacje urządzenia](./media/storsimple-8000-install-update4-via-portal/update3.png)

3. Przed zastosowaniem aktualizacji na urządzeniu zalecamy przejrzenie informacji o wersji. Aby zastosować aktualizacje, kliknij pozycję **Zainstaluj aktualizacje**. W **bloku Potwierdź regularne aktualizacje** przejrzyj wymagania wstępne do ukończenia przed zastosowaniem aktualizacji. Zaznacz to pole wyboru, aby wskazać, że można zaktualizować urządzenie, a następnie kliknij przycisk **Zainstaluj**.

    ![Kliknij pozycję Aktualizacje urządzenia](./media/storsimple-8000-install-update4-via-portal/update4.png)

6. Zostanie rozpoczęty zestaw testów wymagań wstępnych. Testy te obejmują:
   
   * **Testy kondycji kontrolera** w celu sprawdzenia, czy oba kontrolery urządzeń są w dobrej kondycji i w trybie online.
   * **Testy kondycji składnika sprzętowego** w celu sprawdzenia, czy wszystkie składniki sprzętowe Twojego urządzenia StorSimple są w dobrej kondycji.
   * **Testy interfejsu DATA 0** w celu sprawdzenia, czy interfejs DATA 0 jest włączony na Twoim urządzeniu. Jeśli ten interfejs nie jest włączony, należy go włączyć i ponowić próbę.

     Aktualizacja jest pobierana i instalowana tylko wtedy, gdy wszystkie kontrole zostały pomyślnie zakończone. Zobaczysz powiadomienie o trwających testach. Jeśli prechecks nie powiedzie się, a następnie zostaną dostarczone z przyczynami niepowodzenia. Rozwiąż te problemy, a następnie ponów próbę wykonania operacji. Jeśli nie będziesz w stanie samodzielnie rozwiązać tych problemów, konieczne może być skontaktowanie się z pomocą techniczną firmy Microsoft.

7. Po pomyślnym zakończeniu kontroli wstępnej tworzone jest zadanie aktualizacji. Otrzymasz powiadomienie o pomyślnym utworzeniu zadania aktualizacji.
   
    ![Tworzenie zadania aktualizacji](./media/storsimple-8000-install-update4-via-portal/update6.png)
   
    Następnie aktualizacja jest stosowania do Twojego urządzenia.

9. Aktualizacja zajmuje kilka godzin. Aby w dowolnym momencie wyświetlić szczegóły zadania, wybierz zadanie aktualizacji i kliknij pozycję **Szczegóły**.

    ![Tworzenie zadania aktualizacji](./media/storsimple-8000-install-update4-via-portal/update8.png)

     Postęp zadania aktualizacji można również monitorować w **ustawieniach urządzenia > zadaniach**. Na **zadania** bloku, można zobaczyć postęp aktualizacji.

     ![Tworzenie zadania aktualizacji](./media/storsimple-8000-install-update4-via-portal/update7.png)

10. Po zakończeniu zadania przejdź do **pozycji Ustawienia urządzenia > aktualizacje urządzenia**. Wersja oprogramowania powinna zostać zaktualizowana.

   ![Tworzenie zadania aktualizacji](./media/storsimple-8000-install-update4-via-portal/update9.png)

