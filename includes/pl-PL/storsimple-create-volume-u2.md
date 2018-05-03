<!--author=alkohli last changed: 08/16/2016-->

#### <a name="to-create-a-volume"></a>Aby utworzyć wolumin
1. Na stronie **Szybki start** urządzenia kliknij pozycję **Dodaj wolumin**, aby uruchomić Kreatora dodawania woluminu.
2. W Kreatorze dodawania woluminu w obszarze **Ustawienia podstawowe**:
   
   1. Wpisz wartość pola **Nazwa** dla woluminu.
   2. Z listy rozwijanej wybierz wartość **Typ użycia** dla woluminu. W przypadku obciążeń, które wymagają lokalnych gwarancji, małych opóźnień i większej wydajności, wybierz wolumin typu **Przypięty lokalnie**. W przypadku wszystkich innych danych wybierz wolumin typu **Warstwowy**. Jeśli używasz tego woluminu na potrzeby danych archiwalnych, wybierz opcję **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych**. 
      
       Wolumin przypięty lokalnie jest alokowany nieelastycznie i gwarantuje, że główne dane na woluminie pozostaną na urządzeniu lokalnym i nie zostaną przeniesione do chmury.  W przypadku tworzenia woluminu przypiętego lokalnie urządzenie sprawdza dostępne miejsce w warstwach lokalnych, aby zaalokować żądaną ilość miejsca na woluminie. Operacja tworzenia woluminów przypiętych lokalnie może obejmować przenoszenie istniejących danych z urządzenia do chmury i czas tworzenia woluminu może być długi. Łączny czas zależy od rozmiaru alokowanego woluminu, dostępnej przepustowości sieci i danych na urządzeniu. 
      
       Wolumin warstwowy jest alokowany elastycznie i można go szybko utworzyć. W przypadku woluminu warstwowego przeznaczonego na dane archiwalne wybranie opcji **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych** spowoduje zmianę rozmiaru fragmentu deduplikacji dla woluminu na 512 KB. Jeśli to pole nie zostanie zaznaczone, odpowiedni wolumin warstwowy użyje rozmiaru fragmentu wynoszącego 64 KB. Większy rozmiar fragmentu deduplikacji umożliwia urządzeniu usprawnienie transferu dużej ilości danych archiwalnych w chmurze.
   3. Określ **alokowaną pojemność** woluminu. Zanotuj wielkość pojemności dostępnej w oparciu o wybrany typ woluminu. Wybrany rozmiar woluminu nie może przekraczać wielkości dostępnego miejsca.
      
       Na urządzeniu 8100 można alokować woluminy przypięte lokalnie do 8,5 TB lub woluminy warstwowe do 200 TB. Na większym urządzeniu 8600 można alokować woluminy przypięte lokalnie do 22,5 TB lub woluminy warstwowe do 500 TB. Ponieważ lokalne miejsce na urządzeniu jest wymagane do obsługi zestawu roboczego woluminów warstwowych, tworzenie woluminów przypiętych lokalnie ma wpływ na miejsce dostępne do aprowizacji na woluminach warstwowych. Jeśli zatem tworzysz wolumin przypięty lokalnie, miejsce dostępne na potrzeby tworzenia woluminów warstwowych zmniejszy się. Podobnie jeśli utworzysz wolumin warstwowy, miejsce dostępne na potrzeby tworzenia woluminów przypiętych lokalnie zmniejszy się.
      
       W przypadku alokowania woluminu przypiętego lokalnie o rozmiarze 8,5 TB (maksymalny dozwolony rozmiar) na urządzeniu 8100 całe lokalne miejsce dostępne na urządzeniu zostanie wyczerpane. Od tego momentu nie będzie można tworzyć woluminów warstwowych, ponieważ na urządzeniu nie ma już miejsca lokalnego do hostowania roboczego zestawu woluminu warstwowego. Istniejące woluminy warstwowe również wpływają na dostępne miejsce. Jeśli na przykład masz urządzenie 8100 z woluminami warstwowymi o wielkości około 106 TB, tylko 4 TB są dostępne dla woluminów przypiętych lokalnie.
      
       Poniższy obraz przedstawia okno dialogowe **Ustawienia podstawowe** woluminu przypiętego lokalnie.
      
        ![Dodawanie woluminu lokalnego](./media/storsimple-create-volume-u2/add-local-volume-include.png)
      
       Poniższy obraz przedstawia okno dialogowe **Ustawienia podstawowe** woluminu warstwowego.
      
        ![Dodawanie woluminu lokalnego](./media/storsimple-create-volume-u2/add-tiered-volume-include.png)
   
   1. Kliknij ikonę strzałki ![ikona strzałki](./media/storsimple-create-volume-u2/HCS_ArrowIcon-include.png) aby przejść do następnej strony.
3. W oknie dialogowym **Ustawienia dodatkowe** dodaj nowy rekord kontroli dostępu (ACR):
   
   1. Wypełnij pole **Nazwa** dla rekordu ACR.
   2. W obszarze **Nazwa inicjatora iSCSI** podaj kwalifikowaną nazwę iSCSI (IQN) hosta z systemem Windows. Jeśli nie masz nazwy IQN, przejdź do tematu [Pobieranie nazwy IQN hosta z systemem Windows Server](#get-the-iqn-of-a-windows-server-host).
   3. W obszarze **Domyślna kopia zapasowa dla tego woluminu?** zaznacz pole wyboru **Włącz**. Włączenie domyślnego tworzenia kopii zapasowej spowoduje utworzenie zasad wykonywanych codziennie o godz. 22:30 (czas urządzenia) i tworzących migawkę w chmurze dla danego woluminu.
      
      > [!NOTE]
      > Włączenia tworzenia kopii zapasowej na tym etapie nie można cofnąć. W celu zmodyfikowania tego ustawienia trzeba edytować wolumin.
      > 
      > 
      
      ![Dodawanie woluminu](./media/storsimple-create-volume-u2/AddVolumeAdditionalSettings1.png)
4. Po ustawieniu filtrów ![ikona znacznika wyboru](./media/storsimple-create-volume-u2/HCS_CheckIcon-include.png). Wolumin zostanie utworzony przy użyciu wybranych ustawień.

