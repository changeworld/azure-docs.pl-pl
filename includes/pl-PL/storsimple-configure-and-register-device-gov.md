<!--author=SharS last changed: 02/22/16-->

### <a name="to-configure-and-register-the-device"></a>Konfigurowanie i rejestrowanie urządzenia
1. Przejdź do interfejsu programu Windows PowerShell na konsoli szeregowej urządzenia StorSimple. Instrukcje można znaleźć w temacie [Nawiązywanie połączenia z konsolą szeregową urządzenia przy użyciu programu PuTTY](#use-putty-to-connect-to-the-device-serial-console). **Pamiętaj, aby dokładnie wykonać procedurę, w przeciwnym wypadku nie uzyskasz dostępu do konsoli.**
2. W otwartej sesji naciśnij jednokrotnie klawisz Enter, aby wyświetlić wiersz polecenia. 
3. Zostanie wyświetlony monit o wybranie języka dla danego urządzenia. Wybierz język, a następnie naciśnij klawisz Enter. 
   
    ![Konfigurowanie i rejestrowanie urządzenia StorSimple 1](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice1-gov-include.png)
4. W przedstawionym menu konsoli szeregowej wybierz opcję 1, aby zalogować się z pełnymi uprawnieniami dostępu. 
   
    ![Rejestrowanie urządzenia StorSimple 2](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice2-gov-include.png)
5. Wykonaj poniższe kroki, aby skonfigurować minimalne wymagane ustawienia sieciowe urządzenia.
   
   > [!IMPORTANT]
   > Te kroki konfiguracji należy wykonać na aktywnym kontrolerze urządzenia. Menu konsoli szeregowej wskazuje stan kontrolera w komunikacie transparentu. Jeśli nie są łączy się z aktywnym kontrolerem, odłącz, a następnie połącz się z aktywnym kontrolerem.
   > 
   > 
   
   1. W wierszu polecenia wpisz hasło. Domyślne hasło urządzenia to **Password1**.
   2. Wpisz następujące polecenie:
      
        `Invoke-HcsSetupWizard`
   3. Zostanie uruchomiony Kreator instalacji, który ułatwi konfigurowanie ustawień sieciowych urządzenia. Podaj następujące informacje: 
      
      * Adres IP interfejsu sieciowego 0 danych
      * Maska podsieci
      * Brama
      * Adres IP podstawowego serwera DNS
      * Adres IP podstawowego serwera NTP
      
      > [!NOTE]
      > Może być konieczne Poczekaj kilka minut, aż maski podsieci i zastosowanie ustawień DNS. 
      > 
      > 
   4. Opcjonalnie Skonfiguruj serwer proxy sieci web.
      
      > [!IMPORTANT]
      > Mimo że konfiguracja serwera proxy sieci web jest opcjonalne, należy pamiętać, że jeśli używasz serwera proxy sieci web, można skonfigurować tylko go tutaj. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](../articles/storsimple/storsimple-configure-web-proxy.md). 
      > 
      > 
6. Naciśnij klawisze Ctrl + C, aby zakończyć działanie Kreatora instalacji.
7. Zainstaluj aktualizacje w następujący sposób:
   
   1. Aby ustawić adresy IP na obu kontrolerów, użyj następującego polecenia cmdlet:
      
      `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`
   2. W wierszu polecenia Uruchom `Get-HcsUpdateAvailability`. Możesz powiadamiania o dostępnych aktualizacjach.
   3. Uruchom polecenie `Start-HcsUpdate`. To polecenie można uruchomić na dowolnym węźle. Aktualizacje zostaną zastosowane do pierwszego kontrolera, kontrolera zostaną przełączone awaryjnie i następnie aktualizacje zostaną zastosowane na innym kontrolerze.
      
      Możesz monitorować postęp aktualizacji, uruchamiając `Get-HcsUpdateStatus`.    
      
      Następujące przykładowe dane wyjściowe pokazują aktualizację w toku.
      
      ````
      Controller0>Get-HcsUpdateStatus
      RunInprogress       : True
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   : 
      ````
      
      Następujące przykładowe dane wyjściowe wskazują, że aktualizacja została zakończona.
      
      ````
      Controller1>Get-HcsUpdateStatus
      
      RunInprogress       : False
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      
      ````
      
      Może upłynąć do 11 godzin zastosowanie wszystkich aktualizacji, w tym aktualizacje systemu Windows.

8. Po pomyślnym zainstalowaniu wszystkich aktualizacji należy uruchomić następujące polecenie cmdlet, aby upewnić się, że aktualizacje oprogramowania zostały prawidłowo zastosowane:
   
     `Get-HcsSystem`
   
    Powinny zostać wyświetlone następujące wersje:
   
   * HcsSoftwareVersion: 6.3.9600.17491
   * CisAgentVersion: 1.0.9037.0
   * MdsAgentVersion: 26.0.4696.1433
9. Uruchom następujące polecenie cmdlet, aby upewnić się, że aktualizacja oprogramowania układowego zostały poprawnie zastosowane:
   
    `Start-HcsFirmwareCheck`.
   
     Stan oprogramowania układowego powinien mieć **UpToDate**.
10. Uruchom następujące polecenie cmdlet, aby wskazywały na urządzeniu do portalu Microsoft Azure dla instytucji rządowych (ponieważ wskazuje publicznego klasycznego portalu Azure domyślnie). Spowoduje to ponowne uruchomienie obu kontrolerów. Zalecane jest użycie dwóch sesji programu PuTTY można jednocześnie połączyć do obu kontrolerów, tak aby były widoczne po uruchomieniu każdego kontrolera.
    
     `Set-CloudPlatform -AzureGovt_US`
    
    Zostanie wyświetlony komunikat potwierdzenia. Zaakceptuj wartość domyślną (**Y**).
11. Uruchom następujące polecenie cmdlet, aby wznowić instalację:
    
     `Invoke-HcsSetupWizard`
    
     ![Kreator instalacji Wznów](./media/storsimple-configure-and-register-device-gov/HCS_ResumeSetup-gov-include.png)
    
    Po wznowieniu Instalatora, Kreator będzie wersję Update 1 (odpowiadający wersji 17469). 
12. Zaakceptuj ustawienia sieciowe. Po zaakceptowaniu każdego ustawienia, zobaczą komunikat dotyczący sprawdzania poprawności.
13. Ze względów bezpieczeństwa hasło administratora urządzenia wygasa po pierwszej sesji i należy je teraz zmienić. Po wyświetleniu monitu podaj hasło administratora urządzenia. Prawidłowe hasło administratora urządzenia musi zawierać od 8 do 15 znaków. Hasło musi zawierać trzy z wymienionych elementów: małe litery, wielkie litery, cyfry i znaki specjalne.
    
    <br/>![Rejestrowanie urządzenia StorSimple 5](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice5_gov-include.png)
14. W ostatnim kroku Kreatora instalacji wykonywana jest rejestracja urządzenia w usłudze StorSimple Manager. W tym celu należy klucz rejestracji usługi uzyskany w [krok 2: pobieranie klucza rejestracji usługi](#step-2-get-the-service-registration-key). Po podaniu klucza rejestracji konieczne może być zaczekanie 2-3 minut, zanim urządzenie zostanie zarejestrowane.
    
    > [!NOTE]
    > Możesz nacisnąć klawisze Ctrl + C i zakończyć działanie Kreatora instalacji w dowolnym momencie. Jeżeli wprowadzono wszystkie ustawienia sieciowe (adres IP dla protokołu Data 0, maskę podsieci i bramę), wpisy zostaną zachowane.
    > 
    > 
    
    ![Postęp rejestracji StorSimple](./media/storsimple-configure-and-register-device-gov/HCS_RegistrationProgress-gov-include.png)
15. Po zarejestrowaniu urządzenia zostanie wyświetlony klucz szyfrowania danych usługi. Skopiuj ten klucz i zapisz go w bezpiecznym miejscu. **Ten klucz będzie wymagany razem z kluczem rejestracji usługi w celu rejestracji dodatkowych urządzeń w usłudze StorSimple Manager.** Więcej informacji na temat tego klucza znajduje się w temacie [Zabezpieczenia usługi StorSimple](../articles/storsimple/storsimple-security.md).
    
    ![Rejestrowanie urządzenia StorSimple 7](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice7_gov-include.png)    
    
    > [!IMPORTANT]
    > Aby skopiować tekst z okna konsoli szeregowej, po prostu zaznacz tekst. Następnie możesz wkleić go do schowka lub w dowolnym edytorze tekstów. 
    > 
    > NIE używaj klawiszy Ctrl + C do kopiowania klucza szyfrowania danych usługi. Użycie klawiszy Ctrl + C spowoduje zakończenie działania Kreatora instalacji. W efekcie hasło administratora urządzenia nie zostanie zmienione, a na urządzeniu zostanie przywrócone hasło domyślne.
    > 
    > 
16. Zakończ działanie konsoli szeregowej.
17. Wróć do portalu Azure dla instytucji rządowych i wykonaj następujące czynności:
    
    1. Kliknij dwukrotnie usługę StorSimple Manager, aby przejść do strony **Szybki Start**.
    2. Kliknij pozycję **View connected devices** (Wyświetl połączone urządzenia).
    3. Na stronie **Urządzenia** zweryfikuj, czy urządzenie pomyślnie nawiązało połączenie z usługą, sprawdzając jego stan. Urządzenie powinno mieć stan **Online**.
       
        ![Strona Urządzenia StorSimple](./media/storsimple-configure-and-register-device-gov/HCS_DeviceOnline-gov-include.png) 
       
        Jeśli urządzenie ma stan **Offline**, zaczekaj kilka minut, aż urządzenie przejdzie do trybu online. 
       
        Jeśli po kilku minutach urządzenie jest wciąż w trybie offline, sprawdź, czy sieć zapory została skonfigurowana zgodnie z opisem [wymagań sieciowych dotyczących urządzenia StorSimple](../articles/storsimple/storsimple-system-requirements.md). 
       
        Sprawdź, czy port 9354 jest otwarty dla komunikacji wychodzącej, ponieważ jest on używany przez magistralę usług do komunikacji z urządzenia usługi Menedżer StorSimple.

