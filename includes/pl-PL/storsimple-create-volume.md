<!--author=SharS last changed: 02/04/2016-->

#### <a name="to-create-a-volume"></a>Aby utworzyć wolumin
1. Na stronie **Szybki start** urządzenia kliknij pozycję **Dodaj wolumin**. Spowoduje to uruchomienie Kreatora dodawania woluminu.
2. W Kreatorze dodawania woluminu w obszarze **Ustawienia podstawowe** wykonaj następujące czynności:
   
   1. Wypełnij pole **Nazwa** dla woluminu.
   2. Określ wartość w GB lub TB w polu **Alokowana pojemność**. Pojemność woluminu musi wynosić od 1 GB do 64 TB dla urządzenia fizycznego.
   3. Z listy rozwijanej wybierz wartość **Typ użycia** dla woluminu. 
   4. Jeśli używasz tego woluminu na potrzeby danych archiwalnych, zaznacz pole wyboru **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych**. W pozostałych przypadkach zaznacz opcję **Wolumin warstwowy**. (Woluminy warstwowe były dawniej nazywane woluminami głównymi).
      
        ![Dodawanie woluminu](./media/storsimple-create-volume/ScreenshotUpdate1VolumeFlow.png)
      
      1. Kliknij ikonę strzałki, ![ikona strzałki](./media/storsimple-create-volume/HCS_ArrowIcon-include.png) aby przejść do następnej strony.
3. W oknie dialogowym **Ustawienia dodatkowe** dodaj nowy rekord kontroli dostępu (ACR):
   
   1. Wypełnij pole **Nazwa** dla rekordu ACR.
   2. W obszarze **Nazwa inicjatora iSCSI** podaj kwalifikowaną nazwę iSCSI (IQN) hosta z systemem Windows. Jeśli nie masz nazwy IQN, przejdź do tematu [Pobieranie nazwy IQN hosta z systemem Windows Server](#get-the-iqn-of-a-windows-server-host).
   3. Zaleca się włączenie domyślnego tworzenia kopii zapasowej przez zaznaczenie pola wyboru **Włącz domyślne tworzenie kopii zapasowej dla tego woluminu**. Włączenie domyślnego tworzenia kopii zapasowej spowoduje utworzenie zasad wykonywanych codziennie o godz. 22:30 (czas urządzenia) i tworzących migawkę chmury dla danego woluminu.
      
      > [!NOTE]
      > Włączenia tworzenia kopii zapasowej na tym etapie nie można cofnąć. W celu zmodyfikowania tego ustawienia trzeba edytować wolumin.
      > 
      > 
      
        ![Dodawanie woluminu](./media/storsimple-create-volume/AddVolume2-include.png)
4. Po ustawieniu filtrów ![ikona znacznika wyboru](./media/storsimple-create-volume/HCS_CheckIcon-include.png). Wolumin zostanie utworzony przy użyciu wybranych ustawień.

![Zobacz film](./media/storsimple-create-volume/Video_icon.png) **Zobacz film**

Aby obejrzeć film wideo, na którym przestawiono tworzenie woluminu StorSimple, kliknij [tutaj](https://azure.microsoft.com/documentation/videos/create-a-storsimple-volume/).

