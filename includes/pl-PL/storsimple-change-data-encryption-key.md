<!--author=SharS last changed: 12/01/15-->

### <a name="step-1-authorize-a-device-to-change-the-service-data-encryption-key-in-the-azure-classic-portal"></a>Krok 1: Autoryzacji urządzeń można zmienić klucza szyfrowania danych usługi w klasycznym portalu Azure
Zazwyczaj administratora urządzenia żądanie, że administrator usługi autoryzacji urządzeń, aby zmienić klucze szyfrowania danych usługi. Następnie autoryzacji urządzeń, aby zmienić wartość klucza administratora usługi.

Wykonanie tego kroku w klasycznym portalu Azure. Administrator usługi można wybrać urządzenie z wyświetloną listę urządzeń, które mogą być autoryzowany. Urządzenie jest następnie autoryzację do uruchomienia procesu zmiany klucza szyfrowania danych usługi.

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Aby zmienić klucze szyfrowania danych usługi może być upoważnionych urządzeń?
Zanim może być upoważnionych do zainicjowania zmiany klucza szyfrowania danych usługi, urządzenie musi spełniać następujące kryteria:

* Urządzenie musi być w trybie online na kwalifikować się do autoryzacji zmiany klucza szyfrowania danych usługi.
* Ponownie po 30 minutach można autoryzować tego samego urządzenia, jeśli nie została zainicjowana zmiany klucza.
* Inne urządzenie, można autoryzować pod warunkiem, że zmiany klucza nie została zainicjowana przez wcześniej upoważnionego urządzenia. Po autoryzacji nowe urządzenie starym urządzeniem nie można zainicjować zmianę.
* Nie można autoryzować urządzenia, gdy trwa Przerzucanie klucza szyfrowania danych usługi.
* W przypadku niektórych urządzeń zarejestrowanych w usłudze ma przerzuceniem szyfrowania, podczas gdy inne nie zawierają można autoryzować urządzenia. W takich przypadkach kwalifikujących się urządzeń to zmienić te, które zostały ukończone klucza szyfrowania danych usługi.

> [!NOTE]
> W klasycznym portalu Azure urządzenia wirtualnego StorSimple nie są wyświetlane na liście urządzeń, które może być upoważnionych do zmiany klucza uruchomienia.
> 
> 

Wykonaj poniższe kroki, aby wybrać i autoryzować urządzenia w celu zainicjowania zmiany klucza szyfrowania danych usługi.

#### <a name="to-authorize-a-device-to-change-the-key"></a>Aby zezwolić na urządzeniu należy zmienić wartość klucza
1. Na stronie pulpitu nawigacyjnego usługi kliknij **klucza szyfrowania danych usługi zmiany**.
   
    ![Klucz szyfrowania usługi zmiany](./media/storsimple-change-data-encryption-key/HCS_ChangeServiceDataEncryptionKey-include.png)
2. W **klucza szyfrowania danych usługi zmiany** oknie dialogowym Wybierz i autoryzować urządzenia w celu zainicjowania zmiany klucza szyfrowania danych usługi. Listy rozwijanej ma wszystkich kwalifikujących się urządzeń, które mogą być autoryzowane.
3. Kliknij ikonę znacznika wyboru ![ikona znacznika wyboru](./media/storsimple-change-data-encryption-key/HCS_CheckIcon-include.png).

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Krok 2: Użyj środowiska Windows PowerShell dla urządzenia StorSimple do zainicjowania zmiany klucza szyfrowania danych usługi
Ten krok jest wykonywane w programie Windows PowerShell dla interfejsu StorSimple na urządzeniu StorSimple na autoryzowanych.

> [!NOTE]
> Żadne operacje nie można wykonać w klasycznym portalu Azure usługi Menedżer StorSimple do czasu ukończenia przerzucania klucza.
> 
> 

Jeśli używasz konsoli szeregowej urządzenia do nawiązania połączenia interfejsu programu Windows PowerShell, wykonaj następujące kroki.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Aby zainicjować zmiany klucza szyfrowania danych usługi
1. Wybierz opcję 1, aby zalogować się z pełnym dostępem.
2. W wierszu polecenia wpisz:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Po pomyślnym ukończeniu polecenia cmdlet otrzymasz nowy klucz szyfrowania danych usługi. Skopiuj i Zapisz ten klucz do użycia w kroku 3 tego procesu. Ten klucz będzie używany do zaktualizowania wszystkich pozostałych urządzeń zarejestrowanych w usłudze Menedżer StorSimple.
   
   > [!NOTE]
   > Ten proces musi zostać zainicjowany w ciągu czterech godzin autoryzowania urządzenia StorSimple.
   > 
   > 
   
   Ten nowy klucz jest następnie wysyłane do usługi, aby zostać przeniesiony do wszystkich urządzeń, które są zarejestrowane w usłudze. Na pulpicie nawigacyjnym usługi pojawi się alert. Usługa wyłączy wszystkie operacje na zarejestrowanych urządzeniach, a następnie należy zaktualizować klucz szyfrowania danych usługi na innych urządzeniach administratora urządzenia. Jednak operacje We/Wy (wysyłanie danych do chmury hostów) nie zostanie zakłócone.
   
   Jeśli masz jednego urządzenia zarejestrowane w usłudze przerzucania proces został już ukończony i można przejść następnego kroku. Jeśli masz wiele urządzeń zarejestrowanych w usłudze, przejdź do kroku 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Krok 3: Zaktualizuj klucz szyfrowania danych usługi na innych urządzeń StorSimple
Te czynności należy wykonać w interfejsie programu Windows PowerShell urządzenia StorSimple, jeśli masz wiele urządzeń zarejestrowanych w usłudze Menedżer StorSimple. Klucz, który został uzyskany w kroku 2: Użyj środowiska Windows PowerShell dla urządzenia StorSimple do zainicjowania zmiany klucza szyfrowania danych usługi musi być używane do aktualizowania wszystkie pozostałe urządzenia StorSimple zarejestrowane w usłudze Menedżer StorSimple.

Wykonaj poniższe kroki, aby zaktualizować usługę szyfrowania danych na urządzeniu.

#### <a name="to-update-the-service-data-encryption-key"></a>Aby zaktualizować klucz szyfrowania danych usługi
1. Umożliwia łączenie z konsolą programu Windows PowerShell dla StorSimple. Wybierz opcję 1, aby zalogować się z pełnym dostępem.
2. W wierszu polecenia wpisz:
   
    `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Podaj klucz szyfrowania danych usługi, które zostały uzyskane w [krok 2: Użyj środowiska Windows PowerShell dla urządzenia StorSimple do zainicjowania zmiany klucza szyfrowania danych usługi](#to-initiate-the-service-data-encryption-key-change).

