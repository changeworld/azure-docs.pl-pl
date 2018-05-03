<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>Wykonywanie minimalnej konfiguracji urządzenia StorSimple
1. Wybierz urządzenie i kliknij pozycję **Szybki Start**. Kliknij pozycję **Complete device setup** (Wykonaj konfigurację urządzenia), aby uruchomić Kreatora konfiguracji urządzenia.
2. W oknie dialogowym **Ustawienia podstawowe** w Kreatorze konfiguracji urządzenia wykonaj następujące czynności:
   
   1. Określ **przyjazną nazwę** dla urządzenia. Domyślna nazwa urządzenia odzwierciedla takie informacje, jak model urządzenia i numer seryjny. Do celów zarządzania urządzeniem można przypisać przyjazną nazwę o długości maksymalnie 64 znaków.
   2. Ustaw **strefę czasową** na podstawie lokalizacji geograficznej, w której jest wdrażane urządzenie. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.
   3. W obszarze **Ustawienia DNS** podaj adres w polu **Pomocniczy serwer DNS**. Jeśli używasz protokołu IPv6, pole zostanie wypełnione na podstawie prefiksu IPv6 podanego w interfejsie programu Windows PowerShell. 
      Jeśli pomocniczy serwer DNS nie został skonfigurowany, nie będzie można zapisać konfiguracji urządzenia.
   4. W obszarze interfejsów z włączoną obsługą interfejsu iSCSI włącz co najmniej jedną sieć dla interfejsu iSCSI. Co najmniej jeden interfejs sieciowy musi mieć włączoną obsługę chmury i jeden interfejs mieć włączoną obsługę interfejsu iSCSI. Interfejs DATA 0 ma automatycznie włączoną obsługę chmury.
      
      ![Podstawowe ustawienia minimalnej konfiguracji urządzenia StorSimple](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupBasicSettings1-include.png)
3. Kliknij ikonę strzałki. ![Ikona strzałki StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)
4. W oknie dialogowym **Interfejsy sieciowe** podaj stałe adresy IP dla kontrolera 0 i kontrolera 1. **Stałe adresy IP kontrolera muszą być wolnymi adresami IP w obrębie podsieci dostępnej za pośrednictwem adresu IP urządzenia.** Jeśli interfejs DATA 0 został skonfigurowany do obsługi protokołu IPv4, stałe adresy IP muszą być podawane w formacie IPv4. Jeśli podano prefiks podczas konfiguracji protokołu IPv6 , stałe adresy IP zostaną wypełnione automatycznie w tych polach.

    ![Interfejsy sieciowe minimalnej konfiguracji urządzenia StorSimple](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

    Stałe adresy IP kontrolera są używane do obsługi aktualizacji urządzenia i w związku z tym muszą być rutowalne i umożliwiać nawiązanie połączenia z Internetem. Możesz sprawdzić, czy stałe adresy IP kontrolera są routowalne, używając polecenia cmdlet [Test-HcsmConnection][Test]. W poniższym przykładzie przedstawiono stałe adresy IP kontrolera, które są kierowane do Internetu i mogą uzyskać dostęp do serwerów usługi Microsoft Update. 

     ![Polecenie Test-HcsmConnection pokazujące rutowalne adresy IP](./media/storsimple-complete-minimum-device-setup-u1/Test-HcsmConnectionOutputRegisteredDevice.png)

1. Kliknij ikonę znacznika wyboru ![ikona znacznika wyboru StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png).
   Nastąpi powrót do strony **Szybki start** urządzenia.
   
   > [!NOTE]
   > Wszystkie pozostałe ustawienia urządzenia możesz zmodyfikować w dowolnym momencie na stronie **Konfigurowanie**.
   > 
   > 

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx