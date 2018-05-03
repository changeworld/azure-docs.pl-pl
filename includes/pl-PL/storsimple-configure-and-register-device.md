<!--author=alkohli last changed: 12/01/15-->


#### <a name="to-configure-and-register-the-device"></a>Konfigurowanie i rejestrowanie urządzenia
1. Przejdź do interfejsu programu Windows PowerShell na konsoli szeregowej urządzenia StorSimple. Instrukcje można znaleźć w temacie [Nawiązywanie połączenia z konsolą szeregową urządzenia przy użyciu programu PuTTY](#use-putty-to-connect-to-the-device-serial-console). **Pamiętaj, aby dokładnie wykonać procedurę, w przeciwnym wypadku nie uzyskasz dostępu do konsoli.**
2. W otwartej sesji naciśnij jednokrotnie klawisz Enter, aby wyświetlić wiersz polecenia. 
3. Zostanie wyświetlony monit o wybranie języka dla danego urządzenia. Wybierz język, a następnie naciśnij klawisz Enter. 
   
    ![Konfigurowanie i rejestrowanie urządzenia StorSimple 1](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice1-include.png)
4. W przedstawionym menu konsoli szeregowej wybierz opcję 1, aby zalogować się z pełnymi uprawnieniami dostępu. 
   
    ![Rejestrowanie urządzenia StorSimple 2](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice2-include.png)
   
     Wykonaj kroki 5–12, aby skonfigurować minimalne wymagane ustawienia sieciowe urządzenia. **Te kroki konfiguracji należy wykonać na aktywnym kontrolerze urządzenia.** Menu konsoli szeregowej wskazuje stan kontrolera w komunikacie transparentu. Jeśli nie masz połączenia z aktywnym kontrolerem, zakończ połączenie, a następnie nawiąż połączenie z aktywnym kontrolerem.
5. W wierszu polecenia wpisz hasło. Domyślne hasło urządzenia to **Password1**.
6. Wpisz następujące polecenie:
   
     `Invoke-HcsSetupWizard` 
7. Zostanie uruchomiony Kreator instalacji, który ułatwi konfigurowanie ustawień sieciowych urządzenia. Wprowadź następujące informacje: 
   
   * Adres IP dla interfejsu sieciowego protokołu DATA 0
   * Maska podsieci
   * Brama
   * Adres IP podstawowego serwera DNS
   * Adres IP podstawowego serwera NTP
     
     > [!NOTE]
     > Być może trzeba będzie poczekać kilka minut, aż ustawienia maski podsieci i serwera DNS zostaną zastosowane. Jeśli zostanie wyświetlony komunikat o błędzie „Urządzenie nie jest gotowe”, sprawdź połączenie z siecią fizyczną w interfejsie sieciowym protokołu DATA 0 na aktywnym kontrolerze.
     > 
     > 
8. Opcjonalnie skonfiguruj serwer proxy sieci Web. Konfiguracja serwera proxy sieci Web jest opcjonalna, jednak **warto wiedzieć, że w przypadku korzystania z serwera proxy sieci Web można go skonfigurować tylko w tym miejscu**. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](../articles/storsimple/storsimple-configure-web-proxy.md). Jeśli napotkasz jakieś problemy w tym kroku, zobacz wskazówki dotyczące rozwiązywania problemów w temacie [Błędy podczas konfigurowania serwera proxy sieci Web](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-the-optional-web-proxy-settings).

     > [!NOTE]
     > Możesz nacisnąć klawisze Ctrl + C i zakończyć działanie Kreatora instalacji w dowolnym momencie. Wszystkie ustawienia zastosowane przed wydaniem tego polecenia zostaną zachowane.

1. Ze względów bezpieczeństwa hasło administratora urządzenia wygasa po pierwszej sesji i należy je zmienić w kolejnych sesjach. Po wyświetleniu monitu podaj hasło administratora urządzenia. Prawidłowe hasło administratora urządzenia musi zawierać od 8 do 15 znaków. Hasło musi zawierać kombinację małych liter, wielkich liter, cyfr i znaków specjalnych.
2. Hasło do programu StorSimple Snapshot Manager również należy ustawić w tym miejscu. To hasło jest używane podczas uwierzytelniania urządzenia na hoście systemu Windows z uruchomionym programem StorSimple Snapshot Manager. Po wyświetleniu monitu podaj hasło o długości od 14 do 15 znaków. Hasło musi zawierać kombinację trzech z wymienionych elementów: małe litery, wielkie litery, cyfry i znaki specjalne. 
   
   ![Rejestrowanie urządzenia StorSimple 4](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)
   
   Hasło do programu StorSimple Snapshot Manager można zresetować w interfejsie usługi StorSimple Manager. Szczegółowy opis kroków znajduje się w temacie [Zmienianie haseł usługi StorSimple przy użyciu usługi StorSimple Manager](../articles/storsimple/storsimple-change-passwords.md).
   
   Aby rozwiązać problemy, które wystąpiły w tym kroku, zobacz wskazówki dotyczące rozwiązywania problemów w temacie [Błędy związane z hasłami](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords).
3. W ostatnim kroku Kreatora instalacji wykonywana jest rejestracja urządzenia w usłudze StorSimple Manager. Do tego celu potrzebny jest klucz rejestracji usługi uzyskany w kroku 2. Po podaniu klucza rejestracji konieczne może być zaczekanie 2-3 minut, zanim urządzenie zostanie zarejestrowane.
   
   Aby rozwiązać problemy związane z ewentualnymi błędami rejestracji urządzenia, zobacz temat [Błędy podczas rejestracji urządzenia](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-device-registration). Szczegółowe informacje dotyczące rozwiązywania problemów można również znaleźć w temacie [Przykład rozwiązywania problemów krok po kroku](../articles/storsimple/storsimple-troubleshoot-deployment.md#step-by-step-storsimple-troubleshooting-example).
4. Po zarejestrowaniu urządzenia zostanie wyświetlony klucz szyfrowania danych usługi. Skopiuj ten klucz i zapisz go w bezpiecznym miejscu.
   
   > [!WARNING]
   > Ten klucz będzie wymagany razem z kluczem rejestracji usługi w celu rejestracji dodatkowych urządzeń w usłudze StorSimple Manager. Więcej informacji na temat tego klucza znajduje się w temacie [Zabezpieczenia usługi StorSimple](../articles/storsimple/storsimple-security.md).
   > 
   > 
   
    ![Rejestrowanie urządzenia StorSimple 6](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)
   
    Aby skopiować tekst z okna konsoli szeregowej, po prostu zaznacz tekst. Następnie możesz wkleić go do schowka lub w dowolnym edytorze tekstów. NIE używaj klawiszy Ctrl + C do kopiowania klucza szyfrowania danych usługi. Użycie klawiszy Ctrl + C spowoduje zakończenie działania Kreatora instalacji. W efekcie hasło administratora urządzenia i hasło programu StorSimple Snapshot Manager nie zostaną zmienione, a na urządzeniu zostanie przywrócone hasło domyślne.
5. Zakończ działanie konsoli szeregowej.
6. Wróć do klasycznego portalu Azure i wykonaj następujące czynności:
   
   1. Kliknij dwukrotnie usługę StorSimple Manager, aby przejść do strony **Szybki Start**.
   2. Kliknij pozycję **View connected devices** (Wyświetl połączone urządzenia).
   3. Na stronie **Urządzenia** zweryfikuj, czy urządzenie pomyślnie nawiązało połączenie z usługą, sprawdzając jego stan. Urządzenie powinno mieć stan **Online**. Jeśli urządzenie ma stan **Offline**, zaczekaj kilka minut, aż urządzenie przejdzie do trybu online.
   
   ![Strona Urządzenia StorSimple](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png) 
   
   > [!IMPORTANT]
   > Gdy urządzenie przejdzie w tryb online, podłącz kable sieciowe, które zostały odłączone na początku tego kroku.
   > 
   > 

Jeśli urządzenie zostało pomyślnie zarejestrowane i nie przeszło do trybu online, możesz uruchomić polecenie `Test-HcsmConnection -Verbose`, aby upewnić się, że połączenie sieciowe działa prawidłowo. Aby uzyskać szczegółowe informacje na temat korzystania z tego polecenia cmdlet, zobacz [informacje dotyczące polecenia cmdlet Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx).

![Zobacz film](./media/storsimple-configure-and-register-device/Video_icon.png) **Zobacz film**

Aby obejrzeć film przedstawiający konfigurowanie i rejestrowanie urządzenia za pośrednictwem programu Windows PowerShell dla usługi StorSimple, kliknij [tutaj](https://azure.microsoft.com/documentation/videos/initialize-the-storsimple-appliance/).

