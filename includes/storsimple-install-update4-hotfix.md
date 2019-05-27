---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0755c01fe8e13e8e39c0b453198f2b67c51a2bc4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66149600"
---
#### <a name="to-download-hotfixes"></a>Aby pobrać poprawki

Wykonaj następujące kroki, aby pobrać aktualizację oprogramowania z Wykazu usługi Microsoft Update.

1. Uruchom program Internet Explorer i przejdź do [ http://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).
2. Jeśli po raz pierwszy używasz Wykazu usługi Microsoft Update na danym komputerze, po wyświetleniu monitu o zainstalowanie dodatku Wykazu usługi Microsoft Update kliknij pozycję **Zainstaluj**.

    ![Instalowanie wykazu](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. W polu wyszukiwania Wykazu usługi Microsoft Update wprowadź numer w bazie wiedzy (KB) dla poprawki, którą chcesz pobrać, na przykład **4011839**, a następnie kliknij przycisk **Wyszukaj**.
   
    Zostanie wyświetlona lista poprawek zawierająca na przykład pozycję **Cumulative Software Bundle Update 4.0 for StorSimple 8000 Series**.
   
    ![Przeszukiwanie wykazu](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. Kliknij przycisk **Pobierz**. Określ lokalizację lokalną, do której mają trafiać pobrane pliki, albo **przejdź** do takiej lokalizacji. Kliknij opcję pliki do pobrania do określonej lokalizacji i folderu. Folder można też skopiować do udziału sieciowego osiągalnego z urządzenia.
5. Wyszukaj wszystkie dodatkowe poprawki wymienione w powyższej tabeli (**4011841**) i pobrać odpowiadające im pliki do określonych folderów, zgodnie z opisem w powyższej tabeli.

> [!NOTE]
> Poprawki muszą być dostępne z obu kontrolerów, aby wykrywanie wszelkich potencjalnych komunikatów o błędach z kontrolera elementu równorzędnego.
>
> Poprawki muszą zostać skopiowane do 3 oddzielnych folderów. Na przykład aktualizacja oprogramowania/Cis/MDS agent urządzenia mogą być kopiowane w _FirstOrderUpdate_ folderu, wszystkie inne niezakłócającego aktualizacje mógł zostać skopiowany w _SecondOrderUpdate_ folder, a aktualizacje trybu konserwacji w skopiowane _ThirdOrderUpdate_ folderu.

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Aby zainstalować i zweryfikować poprawki przeznaczone do trybu normalnego

Wykonaj następujące kroki, aby zainstalować i zweryfikować poprawki przeznaczone do trybu normalnego. Jeśli zostały już one zainstalowane za pomocą klasycznej witryny Azure Portal, przejdź do [instalowania i weryfikowania poprawek przeznaczonych do trybu konserwacji](#to-install-and-verify-maintenance-mode-hotfixes).

1. Aby zainstalować poprawki, przejdź do interfejsu programu Windows PowerShell na konsoli szeregowej Twojego urządzenia StorSimple. Postępuj zgodnie ze szczegółowymi instrukcjami w części [Nawiązywanie połączenia z konsolą szeregową urządzenia przy użyciu programu PuTTY](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). W wierszy polecenia naciśnij klawisz **Enter**.
2. Wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**. Zalecamy, aby najpierw zainstalować poprawkę na kontrolerze pasywnym.
3. Aby zainstalować poprawkę, w wierszu polecenia wpisz:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    W powyższym poleceniu użyj adresu IP zamiast nazwy DNS w ścieżce udziału. Parametr Credential jest używany tylko wtedy, gdy uzyskuje się dostęp do uwierzytelnionego udziału.
   
    Użycie parametru Credential jest zalecane przy uzyskiwaniu dostępu do udziałów. Nawet udziały otwarte dla wszystkich nie są zwykle otwarte dla użytkowników nieuwierzytelnionych.
   
    Po wyświetleniu monitu podaj hasło.
   
    Przykładowe dane wyjściowe dotyczące instalacji aktualizacji stosowanych w pierwszej kolejności są pokazane poniżej. Pierwszą aktualizacją kolejności należy wskazać określonego pliku.
   
        ```
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \FirstOrderUpdate\HcsSoftwareUpdate.exe -Credential contoso\John
   
        Confirm
   
        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y
   
        ```
4. Wpisz **Y**, gdy zostanie wyświetlony monit o potwierdzenie instalacji poprawki.
5. Monitoruj aktualizację za pomocą polecenia cmdlet `Get-HcsUpdateStatus`. Aktualizacja zakończy się najpierw na kontrolerze pasywnym. Gdy kontroler pasywny zostanie zaktualizowany, nastąpi przejście do trybu failover i aktualizacja zostanie zastosowana na drugim kontrolerze. Aktualizacja zostanie zakończona po zaktualizowaniu obu kontrolerów.
   
    Następujące przykładowe dane wyjściowe pokazują aktualizację w toku. Gdy aktualizacja będzie w toku, parametr `RunInprogress` będzie mieć wartość `True`.

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 02/03/2017 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     Następujące przykładowe dane wyjściowe wskazują, że aktualizacja została zakończona. Gdy aktualizacja zostanie zakończona, parametr `RunInProgress` będzie mieć wartość `False`.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 02/03/2017 9:15:55 AM
    LastUpdateTimestamp : 02/03/2017 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE]
    > Czasem polecenie cmdlet zgłasza wartość `False`, gdy aktualizacja jest ciągle w toku. Aby upewnić się, że instalacja poprawki została zakończona, zaczekaj kilka minut, uruchom ponownie to polecenie i sprawdź, czy parametr `RunInProgress` ma wartość `False`. Jeśli tak jest, instalowanie poprawki zostało zakończone.

6. Po zakończeniu aktualizowania oprogramowania sprawdź wersje oprogramowania systemu. Wpisz:
   
    `Get-HcsSystem`
   
    Powinny zostać wyświetlone następujące wersje:
   
   * `FriendlySoftwareVersion: StorSimple 8000 Series Update 4.0`
   * `HcsSoftwareVersion: 6.3.9600.17820`
   
     Jeśli numer wersji nie zmieni się po zastosowaniu aktualizacji, wskazuje to, że stosowanie poprawki nie powiodło się. Jeśli widzisz coś takiego, skontaktuj się z [pomocą techniczną firmy Microsoft](../articles/storsimple/storsimple-contact-microsoft-support.md) w celu uzyskania dalszej pomocy.
     
     > [!IMPORTANT]
     > Należy uruchomić ponownie kontroler aktywny za pomocą `Restart-HcsController` polecenia cmdlet przed zastosowaniem następnej aktualizacji.
     
7. Powtórz kroki od 3 do 5, aby zainstalować agenta konfiguracji (ci) / MDS pobrane do Twojego _FirstOrderUpdate_ folderu. 
8. Powtórz kroki od 3 do 5, aby zainstalować aktualizacje stosowane w drugiej kolejności. **Drugi kolejność aktualizacji, można zainstalować wiele aktualizacji przez uruchomienie `Start-HcsHotfix cmdlet` i wskazanie folderu, gdzie znajdują się druga aktualizacji zamówień. Polecenie cmdlet uruchomi wszystkie aktualizacje dostępne w folderze.** Jeśli aktualizacja jest już zainstalowana, logika aktualizacji wykryje to i nie zastosuje tej aktualizacji. 

Po zainstalowaniu wszystkich poprawek użyj polecenia cmdlet `Get-HcsSystem`. Wersje powinny być następujące:

   * `CisAgentVersion:  1.0.9441.0`
   * `MdsAgentVersion: 35.2.2.0`
   * `Lsisas2Version: 2.0.78.00`


#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Aby zainstalować i zweryfikować poprawki przeznaczone do trybu konserwacji
Użyj poprawki KB4011837, aby zainstalować aktualizacje oprogramowania układowego dysku. Te aktualizacje wymagają zatrzymania pracy i zajmują około 30 minut. Można zdecydować się na zainstalowanie ich w zaplanowanym oknie obsługi, łącząc się z konsolą szeregową urządzenia.

Pamiętaj, że jeśli oprogramowanie układowe dysku jest już aktualne, tych aktualizacji nie trzeba instalować. Uruchom polecenie cmdlet `Get-HcsUpdateAvailability` z konsoli szeregowej urządzenia, aby sprawdzić, czy aktualizacje są dostępne i czy wymagają przerwania pracy (są przeznaczone do trybu konserwacji), czy nie (są przeznaczone do trybu normalnego).

Aby zainstalować aktualizacje oprogramowania układowego dysku, postępuj zgodnie z instrukcjami poniżej.

1. Przełącz urządzenie do trybu konserwacji. **Pamiętaj, że łącząc się z urządzeniem w trybie konserwacji, nie należy łączyć się zdalnie z programu Windows PowerShell. Zamiast tego należy uruchomić to polecenie cmdlet na kontrolerze urządzenia podczas połączenia za pośrednictwem konsoli szeregowej urządzenia.** Wpisz:
   
    `Enter-HcsMaintenanceMode`
   
    Poniżej pokazano przykładowe dane wyjściowe.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8600
        Name: Update4-8600-mystorsimple
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
   
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    Oba kontrolery są następnie uruchamiane ponownie w trybie konserwacji.
2. Aby zainstalować aktualizację oprogramowania układowego dysku, wpisz:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Poniżej pokazano przykładowe dane wyjściowe.
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\ThirdOrderUpdates\ -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. Monitoruj postęp instalacji za pomocą polecenia `Get-HcsUpdateStatus`. Aktualizacja będzie zakończona, gdy parametr `RunInProgress` zmieni wartość na `False`.
4. Po zakończeniu instalacji kontroler, na którym została zainstalowana poprawka przeznaczona do trybu konserwacji, zostanie uruchomiony ponownie. Zaloguj się w opcji 1, **Zaloguj się przy użyciu pełnego dostępu**i sprawdź wersję oprogramowania układowego dysku. Wpisz:
   
   `Get-HcsFirmwareVersion`
   
   Oczekiwane wersje oprogramowania układowego dysku to:
   
   `XMGJ, XGEG, KZ50, F6C2, VR08, N002, 0106`
   
   Poniżej pokazano przykładowe dane wyjściowe.
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8600
       Name: Update4-8600-mystorsimple
       Software Version: 6.3.9600.17820
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
           ActiveBIOS:0.45.0010
              BackupBIOS:0.45.0006
              MainCPLD:17.0.000b
              ActiveBMCRoot:2.0.001F
              BackupBMCRoot:2.0.001F
              BMCBoot:2.0.0002
              LsiFirmware:20.00.04.00
              LsiBios:07.37.00.00
              Battery1Firmware:06.2C
              Battery2Firmware:06.2C
              DomFirmware:X231600
              CanisterFirmware:3.5.0.56
              CanisterBootloader:5.03
              CanisterConfigCRC:0x9134777A
              CanisterVPDStructure:0x06
              CanisterGEMCPLD:0x19
              CanisterVPDCRC:0x142F7DC2
              MidplaneVPDStructure:0x0C
              MidplaneVPDCRC:0xA6BD4F64
              MidplaneCPLD:0x10
              PCM1Firmware:1.00|1.05
              PCM1VPDStructure:0x05
              PCM1VPDCRC:0x41BEF99C
              PCM2Firmware:1.00|1.05
              PCM2VPDStructure:0x05
              PCM2VPDCRC:0x41BEF99C

           EbodFirmware
              CanisterFirmware:3.5.0.56
              CanisterBootloader:5.03
              CanisterConfigCRC:0xB23150F8
              CanisterVPDStructure:0x06
              CanisterGEMCPLD:0x14
              CanisterVPDCRC:0xBAA55828
              MidplaneVPDStructure:0x0C
              MidplaneVPDCRC:0xA6BD4F64
              MidplaneCPLD:0x10
              PCM1Firmware:3.11
              PCM1VPDStructure:0x03
              PCM1VPDCRC:0x6B58AD13
              PCM2Firmware:3.11
              PCM2VPDStructure:0x03
              PCM2VPDCRC:0x6B58AD13

           DisksFirmware
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
   
    Uruchom polecenie `Get-HcsFirmwareVersion` na drugim kontrolerze, aby sprawdzić, czy wersja oprogramowania została zaktualizowana. Następnie możesz wyjść z trybu konserwacji. W tym celu wpisz następujące polecenie dla każdego kontrolera urządzenia:
   
   `Exit-HcsMaintenanceMode`

5. Po wyjściu z trybu konserwacji kontrolery zostaną ponownie uruchomione. Gdy aktualizacje oprogramowania układowego dysku zostaną pomyślnie zastosowane i urządzenie wyjdzie z trybu konserwacji, wróć do klasycznej witryny Azure Portal. Pamiętaj, że fakt zainstalowania aktualizacji przeznaczonych do trybu konserwacji może nie być widoczny w portalu nawet przez 24 godziny.

