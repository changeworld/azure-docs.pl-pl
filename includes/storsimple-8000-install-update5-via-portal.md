---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d2df7388018c463ba58b57be46945915210b84b6
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66171156"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Aby zainstalować aktualizację z witryny Azure Portal

1. Na stronie usługi StorSimple wybierz swoje urządzenie.

    ![Wybierz urządzenie](./media/storsimple-8000-install-update5-via-portal/update1.png)

2. Przejdź do **ustawienia urządzenia** > **aktualizacji urządzenia**.

    ![Kliknij przycisk aktualizacji urządzenia](./media/storsimple-8000-install-update5-via-portal/update2.png)

2. Pojawi się powiadomienie, jeśli są dostępne nowe aktualizacje. Możesz również w **aktualizacji urządzenia** bloku kliknij **Skanuj aktualizacje**. Zostanie utworzone zadanie skanowania dostępnych aktualizacji. Otrzymasz powiadomienie, gdy zadanie zostanie pomyślnie ukończone.

    ![Kliknij przycisk aktualizacji urządzenia](./media/storsimple-8000-install-update5-via-portal/update3.png)

3. Przed zastosowaniem aktualizacji na urządzeniu zalecamy przejrzenie informacji o wersji. Aby zastosować aktualizacje, kliknij przycisk **instalowania aktualizacji**. W **Potwierdź regularne aktualizacje** bloku Przegląd wymagań wstępnych na ukończenie stosowania aktualizacji. Zaznacz pole wyboru, aby wskazać, że jesteś gotowy do aktualizacji urządzenia, a następnie kliknij przycisk **zainstalować**.

    ![Kliknij przycisk aktualizacji urządzenia](./media/storsimple-8000-install-update5-via-portal/update4.png)

6. Zostanie rozpoczęty zestaw testów wymagań wstępnych. Testy te obejmują:
   
   * **Testy kondycji kontrolera** w celu sprawdzenia, czy oba kontrolery urządzeń są w dobrej kondycji i w trybie online.
   * **Testy kondycji składnika sprzętowego** w celu sprawdzenia, czy wszystkie składniki sprzętowe Twojego urządzenia StorSimple są w dobrej kondycji.
   * **Testy interfejsu DATA 0** w celu sprawdzenia, czy interfejs DATA 0 jest włączony na Twoim urządzeniu. Jeśli ten interfejs nie jest włączony, należy go włączyć i ponowić próbę.

     Aktualizacja jest pobierany i instalowany, tylko wtedy, gdy wszystkie testy zostaną wykonane pomyślnie. Zobaczysz powiadomienie o trwających testach. W przypadku awarii prechecks, następnie otrzymasz z przyczyny niepowodzenia. Rozwiązania tych problemów, a następnie spróbuj ponownie wykonać operację. Jeśli nie będziesz w stanie samodzielnie rozwiązać tych problemów, konieczne może być skontaktowanie się z pomocą techniczną firmy Microsoft.

7. Po pomyślnym ukończeniu prechecks zostanie utworzone zadanie aktualizacji. Otrzymasz powiadomienie o pomyślnym utworzeniu zadania aktualizacji.
   
    ![Tworzenie zadania aktualizacji](./media/storsimple-8000-install-update5-via-portal/update6.png)
   
    Następnie aktualizacja jest stosowania do Twojego urządzenia.

9. Aktualizacja zajmuje kilka godzin. Aby w dowolnym momencie wyświetlić szczegóły zadania, wybierz zadanie aktualizacji i kliknij pozycję **Szczegóły**.

    ![Tworzenie zadania aktualizacji](./media/storsimple-8000-install-update5-via-portal/update8.png)

     Można również monitorować postęp zadania aktualizacji z **ustawienia urządzenia > zadania**. Na **zadań** bloku można sprawdzać postęp aktualizacji.

     ![Tworzenie zadania aktualizacji](./media/storsimple-8000-install-update5-via-portal/update7.png)

10. Po zakończeniu zadania przejdź do **ustawienia urządzenia > aktualizacje urządzeń**. Wersja oprogramowania teraz powinien zostać zaktualizowany.

