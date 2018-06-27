### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Zainstaluj aktualizacje tryb konserwacji za pomocą środowiska Windows PowerShell dla urządzenia StorSimple

Po zastosowaniu aktualizacji trybu konserwacji dla urządzenia StorSimple, wszystkie żądania We/Wy są wstrzymane. Usługi, takie jak trwałej pamięci (NVRAM) lub usługę klastrowania zostały zatrzymane. Zarówno kontrolery ponowny rozruch po wprowadzeniu lub opuścić ten tryb. Po zakończeniu pracy w tym trybie, wszystkie usługi Wznów i są w dobrej kondycji. (Może to potrwać kilka minut).

> [!IMPORTANT]
> * Przed trybu konserwacji, sprawdź, czy obu kontrolerów urządzeń są w dobrej kondycji w portalu Azure. Jeśli kontroler nie jest w dobrej kondycji, [skontaktuj się z pomocą techniczną firmy Microsoft](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) dalsze czynności.
> * Podczas pracy w trybie konserwacji, należy najpierw zaktualizować jednego kontrolera, a następnie inny kontroler.

1. Łączenie z konsolą szeregową przy użyciu programu PuTTY. Postępuj zgodnie ze szczegółowymi instrukcjami w części [Nawiązywanie połączenia z konsolą szeregową urządzenia przy użyciu programu PuTTY](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). W wierszy polecenia naciśnij klawisz **Enter**. Wybierz opcję 1 **Zaloguj się przy użyciu pełnego dostępu**.

2. Ustaw kontroler w trybie konserwacji, wpisz:
    
    `Enter-HcsMaintenanceMode`

    Obu kontrolerów zostanie ponownie uruchomiony w trybie konserwacji.

3. Zainstaluj aktualizacje tryb konserwacji. Wpisz:

    `Start-HcsUpdate`

    Zostanie wyświetlony monit o potwierdzenie. Po potwierdzeniu aktualizacje są zainstalowane na kontrolerze, który obecnie używasz. Po zainstalowaniu aktualizacji, uruchamia ponownie kontrolera.

4. Monitorowanie stanu aktualizacji. Zaloguj się do kontrolera elementu równorzędnego jako bieżący kontroler jest aktualizowana i nie będzie mógł przetworzyć innych poleceń. Wpisz:

    `Get-HcsUpdateStatus`

    Jeśli `RunInProgress` jest `True`, aktualizacja jest nadal w toku. Jeśli `RunInProgress` jest `False`, oznacza to, że aktualizacja została ukończona.

5. Po skutecznym zastosowaniu wszystkich aktualizacji oprogramowania układowego dysku i ponownie uruchomiono kontroler zaktualizowany, sprawdź wersja oprogramowania układowego dysku. Na kontrolerze zaktualizowane wpisz:

    `Get-HcsFirmwareVersion`
   
    Dostępne są następujące wersje oprogramowania układowego oczekiwanego dysku:  `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Wyjść z trybu konserwacji. Wpisz następujące polecenie dla każdego kontrolera urządzenia:

    `Exit-HcsMaintenanceMode`

    Po wyjściu z trybu konserwacji kontrolery zostaną ponownie uruchomione.

7. Wróć do portalu Azure. Portal mogą nie pokazywać, czy zainstalowane aktualizacje trybu konserwacji na 24 godziny.