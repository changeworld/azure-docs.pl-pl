<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Aby zainstalować poprawki trybu konserwacji programu Windows PowerShell dla urządzenia StorSimple
> [!IMPORTANT]
> W trybie konserwacji należy najpierw zastosować poprawkę na jeden kontroler, a następnie na innym kontrolerze.
> 
> 

1. Umieść urządzenia w trybie konserwacji. Zobacz [krok 2: tryb konserwacji wprowadź](../articles/storsimple/storsimple-update-device.md#step2) instrukcje na temat trybu konserwacji.
2. Aby zastosować poprawkę, wpisz:
   
     `Start-HcsHotfix` 
3. Po wyświetleniu monitu podaj ścieżkę do udostępnionego folderu sieciowego, który zawiera pliki poprawek.
4. Pojawi się monit o potwierdzenie. Typ **Y** kontynuować instalacji poprawki.
5. Po zastosowaniu tej poprawki na jeden kontroler, zaloguj się do innego kontrolera. Zastosuj poprawkę tak samo jak dla poprzedniego kontrolera.
6. Po zastosowaniu poprawki, wyjść z trybu konserwacji. Zobacz [krok 4: tryb konserwacji zakończenia](../articles/storsimple/storsimple-update-device.md#step4) instrukcje.

