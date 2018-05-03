<!--author=alkohli last changed: 9/17/15-->

### <a name="to-take-a-backup"></a>Aby utworzyć kopię zapasową
1. Na stronie **Szybki start** urządzenia kliknij pozycję **Dodaj zasady tworzenia kopii zapasowej**. Spowoduje to uruchomienie Kreatora dodawania zasad tworzenia kopii zapasowej. 
2. Na stronie **Definiowanie zasad tworzenia kopii zapasowej**:
   
   1. Podaj nazwę zasad tworzenia kopii zapasowej składającą się z 3–150 znaków.
   2. Wybierz woluminy do wykonania kopii zapasowej. W przypadku wybrania więcej niż jednego woluminu woluminy te zostaną zgrupowane w celu utworzenia kopii zapasowej spójnej na poziomie awarii.
   3. Kliknij ikonę strzałki ![ikona strzałki](./media/storsimple-take-backup/HCS_ArrowIcon-include.png). 
      
      ![Dodawanie zasad tworzenia kopii zapasowej](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard1M-include.png)
3. Na stronie **Definiowanie harmonogramu**:
   
   1. Wybierz typ kopii zapasowej z listy rozwijanej. Aby szybciej przeprowadzać operacje przywracania, wybierz pozycję **Migawka lokalna**. Aby zachować odporność danych, wybierz pozycję **Migawka w chmurze**.
   2. Określ częstotliwość wykonywania kopii zapasowych w minutach, godzinach, dniach lub tygodniach.
   3. Wybierz czas przechowywania. Opcje przechowywania zależą od częstotliwości tworzenia kopii zapasowej. Na przykład w przypadku zasad dziennych można określić zasady przechowywania w tygodniach, natomiast czas przechowywania w przypadku zasad miesięcznych jest wyrażany w miesiącach.
   4. Wybierz datę i godzinę rozpoczęcia dla zasad tworzenia kopii zapasowej.
   5. Zaznacz pole wyboru **Włącz**, aby włączyć zasady tworzenia kopii zapasowej. 
   6. Kliknij ikonę znacznika wyboru, ![ikona znacznika wyboru](./media/storsimple-take-backup/HCS_CheckIcon-include.png) aby zapisać zasady.
      
      ![Dodawanie zasad tworzenia kopii zapasowej](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard2M-include.png)
      
      Masz teraz zasady tworzenia kopii zapasowej, które spowodują utworzenie zaplanowanych kopii zapasowych danych woluminu.

Konfiguracja urządzenia została ukończona. 

![Zobacz film](./media/storsimple-take-backup/Video_icon.png) **Zobacz film**

Aby obejrzeć wideo demonstrujące sposób wykonania kopii zapasowej danych StorSimple, kliknij [tutaj](https://azure.microsoft.com/documentation/videos/take-a-storsimple-backup/).

