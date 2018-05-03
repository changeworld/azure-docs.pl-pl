<!--author=alkohli last changed: 02/22/2016-->


### <a name="to-configure-and-register-the-device"></a>Konfigurowanie i rejestrowanie urządzenia
1. Przejdź do interfejsu programu Windows PowerShell na konsoli szeregowej urządzenia StorSimple. Instrukcje można znaleźć w temacie [Nawiązywanie połączenia z konsolą szeregową urządzenia przy użyciu programu PuTTY](#use-putty-to-connect-to-the-device-serial-console). **Pamiętaj, aby dokładnie wykonać procedurę, w przeciwnym wypadku nie uzyskasz dostępu do konsoli.**
2. W otwartej sesji naciśnij jednokrotnie klawisz Enter, aby wyświetlić wiersz polecenia. 
3. Zostanie wyświetlony monit o wybranie języka dla danego urządzenia. Wybierz język, a następnie naciśnij klawisz Enter. 
   
    ![Konfigurowanie i rejestrowanie urządzenia StorSimple 1](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice1-U1-include.png)
4. W przedstawionym menu konsoli szeregowej wybierz opcję 1, aby zalogować się z pełnymi uprawnieniami dostępu. 
   
    ![Rejestrowanie urządzenia StorSimple 2](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice2_U1-include.png)
   
     Wykonaj kroki 5–12, aby skonfigurować minimalne wymagane ustawienia sieciowe urządzenia. **Te kroki konfiguracji należy wykonać na aktywnym kontrolerze urządzenia.** Menu konsoli szeregowej wskazuje stan kontrolera w komunikacie transparentu. Jeśli nie masz połączenia z aktywnym kontrolerem, zakończ połączenie, a następnie nawiąż połączenie z aktywnym kontrolerem.
5. W wierszu polecenia wpisz hasło. Domyślne hasło urządzenia to **Password1**.
6. Wpisz następujące polecenie: `Invoke-HcsSetupWizard`. 
7. Zostanie uruchomiony Kreator instalacji, który ułatwi konfigurowanie ustawień sieciowych urządzenia. Wprowadź następujące informacje: 
   
   * Adres IP dla interfejsu sieciowego protokołu DATA 0
   * Maska podsieci
   * Brama
   * Adres IP podstawowego serwera DNS
     
        Pamiętaj, że system sprawdza poprawność ustawień sieciowych po każdym kroku procesu.
     
     > [!NOTE]
     > Być może trzeba będzie poczekać kilka minut, aż ustawienia maski podsieci i serwera DNS zostaną zastosowane. Jeśli zostanie wyświetlony komunikat o błędzie „Check the network connectivity to Data 0” (Sprawdź łączność sieciową z protokołem Data 0), sprawdź połączenie z siecią fizyczną w interfejsie sieciowym protokołu DATA 0 na aktywnym kontrolerze.
     > 
     > 
8. Opcjonalnie skonfiguruj serwer proxy sieci Web. Konfiguracja serwera proxy sieci Web jest opcjonalna, jednak **warto wiedzieć, że w przypadku korzystania z serwera proxy sieci Web można go skonfigurować tylko w tym miejscu**. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](../articles/storsimple/storsimple-configure-web-proxy.md).
9. Skonfiguruj podstawowy serwer NTP dla urządzenia. Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas do celów uwierzytelniania dostawców usługi w chmurze. Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu. Jeśli nie jest to możliwe, określ wewnętrzny serwer NTP. 
10. Ze względów bezpieczeństwa hasło administratora urządzenia wygasa po pierwszej sesji i należy je teraz zmienić. Po wyświetleniu monitu podaj hasło administratora urządzenia. Prawidłowe hasło administratora urządzenia musi zawierać od 8 do 15 znaków. Hasło musi zawierać trzy z wymienionych elementów: małe litery, wielkie litery, cyfry i znaki specjalne.
    
    <br/>![Rejestrowanie urządzenia StorSimple 5](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice5_U1-include.png)
11. W ostatnim kroku Kreatora instalacji wykonywana jest rejestracja urządzenia w usłudze StorSimple Manager. Do tego celu potrzebny jest klucz rejestracji usługi uzyskany w kroku 2. Po podaniu klucza rejestracji konieczne może być zaczekanie 2-3 minut, zanim urządzenie zostanie zarejestrowane.
    
    > [!NOTE]
    > Możesz nacisnąć klawisze Ctrl + C i zakończyć działanie Kreatora instalacji w dowolnym momencie. Jeżeli wprowadzono wszystkie ustawienia sieciowe (adres IP dla protokołu Data 0, maskę podsieci i bramę), wpisy zostaną zachowane.
    > 
    > 
    
    ![Rejestrowanie urządzenia StorSimple 6](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice6_U1-include.png)
12. Po zarejestrowaniu urządzenia zostanie wyświetlony klucz szyfrowania danych usługi. Skopiuj ten klucz i zapisz go w bezpiecznym miejscu. **Ten klucz będzie wymagany razem z kluczem rejestracji usługi w celu rejestracji dodatkowych urządzeń w usłudze StorSimple Manager.** Więcej informacji na temat tego klucza znajduje się w temacie [Zabezpieczenia usługi StorSimple](../articles/storsimple/storsimple-security.md).
    
    ![Rejestrowanie urządzenia StorSimple 7](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice7_U1-include.png)    
    
    > [!NOTE]
    > Aby skopiować tekst z okna konsoli szeregowej, po prostu zaznacz tekst. Następnie możesz wkleić go do schowka lub w dowolnym edytorze tekstów. NIE używaj klawiszy Ctrl + C do kopiowania klucza szyfrowania danych usługi. Użycie klawiszy Ctrl + C spowoduje zakończenie działania Kreatora instalacji. W efekcie hasło administratora urządzenia nie zostanie zmienione, a na urządzeniu zostanie przywrócone hasło domyślne.
    > 
    > 
13. Zakończ działanie konsoli szeregowej.
14. Wróć do klasycznego portalu Azure i wykonaj następujące czynności:
    
    1. Kliknij dwukrotnie usługę StorSimple Manager, aby przejść do strony **Szybki Start**.
    2. Kliknij pozycję **View connected devices** (Wyświetl połączone urządzenia).
    3. Na stronie **Urządzenia** zweryfikuj, czy urządzenie pomyślnie nawiązało połączenie z usługą, sprawdzając jego stan. Urządzenie powinno mieć stan **Online**.
       
        ![Strona Urządzenia StorSimple](./media/storsimple-configure-and-register-device-u1/HCS_DevicesPageM_U1-include.png) 
       
        Jeśli urządzenie ma stan **Offline**, zaczekaj kilka minut, aż urządzenie przejdzie do trybu online. 
       
        Jeśli po kilku minutach urządzenie jest wciąż w trybie offline, sprawdź, czy sieć zapory została skonfigurowana zgodnie z opisem [wymagań sieciowych dotyczących urządzenia StorSimple](../articles/storsimple/storsimple-system-requirements.md). 
       
        Sprawdź, czy port 9354 jest otwarty dla komunikacji wychodzącej, ponieważ jest on używany przez magistralę usług do komunikacji między usługą i urządzeniem w usłudze StorSimple Manager.

