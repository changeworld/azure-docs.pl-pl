---
title: Zarządzanie urządzeniami StorSimple za pomocą systemu Azure Automation
description: Dowiedz się, jak zautomatyzować zadania storsimple za pomocą systemu Azure Automation Runbook
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: 727bebe0c190ed4dff4408884c45fe166ad541a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276973"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Zarządzanie urządzeniami StorSimple za pomocą łańszeń czągo usługi Azure Automation

W tym artykule opisano, jak elementy runbook usługi Azure Automation są używane do zarządzania urządzeniem z serii StorSimple 8000 w witrynie Azure portal. Przykładowy projekt runbook jest dołączony, aby przejść przez kroki konfigurowania środowiska do wykonania tego uruchomieniu. ścieli.


## <a name="configure-add-and-run-azure-runbook"></a>Konfigurowanie, dodawanie i uruchamianie łańszce umień platformy Azure

W tej sekcji bierze się przykład skrypt programu Windows PowerShell dla StorSimple i szczegółowo różne kroki wymagane do zaimportowania skryptu do życiornika, a następnie opublikować i wykonać element runbook.

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:

* aktywna subskrypcja platformy Azure skojarzona z usługą Menedżera urządzeń StorSimple zarejestrowaną na urządzeniu z serii StorSimple 8000.

* Program Windows PowerShell 5.0 zainstalowany na komputerze (lub, host systemu Windows Server dla storsimple, jeśli go używać).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Tworzenie modułu ekscesu automatyzacji w programie Windows PowerShell

Aby utworzyć moduł automatyzacji do zarządzania urządzeniami z serii StorSimple 8000, wykonaj następujące czynności:

1. Uruchom program Windows PowerShell. Utwórz nowy folder i zmień katalog na nowy folder.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Pobierz nuget cli](https://www.nuget.org/downloads) w folderze utworzonym w poprzednim kroku. Istnieją różne wersje _nuget.exe_. Wybierz wersję odpowiadającą sdk. Każdy link do pobrania wskazuje bezpośrednio na plik _.exe._ Pamiętaj, aby kliknąć prawym przyciskiem myszy i zapisać plik na komputerze, zamiast uruchamiać go w przeglądarce.

    Można również uruchomić następujące polecenie, aby pobrać i zapisać skrypt w tym samym folderze, który został utworzony wcześniej.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Pobierz zależny sdk.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Pobierz skrypt z przykładowego projektu GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Utwórz moduł systemu runbook usługi Azure Automation do zarządzania urządzeniami storsimple z serii 8000. W oknie programu Windows Powershell wpisz następujące polecenia:

    ```powershell
        # set path variables
        $downloadDir = "C:\scripts\StorSimpleSDKTools"
        $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

        #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
        mkdir "$moduleDir"
        Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
        Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

        #Don't change the name of the Archive
        compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

6. Sprawdź, czy plik zip modułu automatyzacji jest tworzony w pliku `C:\scripts\StorSimpleSDKTools`.

    ![weryfikacja-automatyzacja-moduł](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. Następujące dane wyjściowe są prezentowane podczas tworzenia modułu automatyzacji za pośrednictwem programu Windows PowerShell.

    ```powershell
    mkdir C:\scripts\StorSimpleSDKTools
    ```

    ```Output
        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools
    ```

    ```powershell
    wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 717.48 ms
    ```

    ```powershell
    wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    # set path variables
    $downloadDir = "C:\scripts\StorSimpleSDKTools"
    $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"
    #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
    mkdir "$moduleDir"
    ```

    ```Output
        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series
    ```

    ```powershell
    Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
    Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    #Don't change the name of the Archive
    compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

### <a name="import-publish-and-run-automation-runbook"></a>Importowanie, publikowanie i uruchamianie systemu runbook automatyzacji

1. Utwórz konto automatyzacji Azure Run As w witrynie Azure portal. Aby to zrobić, przejdź do **witryny Azure marketplace > wszystko,** a następnie wyszukaj w polu **Automatyzacja**. Wybierz **pozycję Konta automatyzacji**.

    ![automatyzacja wyszukiwania](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. W bloku **Dodaj konto automatyzacji:**

   1. Podaj **nazwę** swojego konta automatyzacji.
   2. Wybierz **subskrypcję** połączoną z usługą Menedżera urządzeń StorSimple.
   3. Utwórz nową grupę zasobów lub wybierz z istniejącej grupy zasobów.
   4. Wybierz **lokalizację** (jeśli to możliwe taką samą, jak w przypadku, gdy usługa jest uruchomiona).
   5. Pozostaw domyślną opcję **Utwórz uruchom jako konto.**
   6. Opcjonalnie zaznacz **opcję Przypnij do pulpitu nawigacyjnego**. Kliknij przycisk **Utwórz**.

       ![tworzenie-automatyzacja-konto](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Po pomyślnym utworzeniu konta automatyzacji użytkownik zostanie o tym powiadomiony. Aby uzyskać więcej informacji na temat tworzenia konta automatyzacji, przejdź do [tworzenia konta Uruchom jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. Aby upewnić się, że utworzone konto automatyzacji może uzyskać dostęp do usługi StorSimple Device Manager, należy przypisać odpowiednie uprawnienia do konta automatyzacji. Przejdź do **kontroli dostępu** w usłudze StorSimple Device Manager. Kliknij **+ Dodaj** i podaj nazwę swojego konta usługi Azure Automation. **Zapisz** ustawienia.

    ![dodaj uprawnienia-automatyzacja-konto](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. Na nowo utworzonym koncie przejdź do **witryny Zasoby udostępnione > moduły** i kliknij przycisk **+ Dodaj moduł**.

5. W bloku **Dodaj moduł** przejdź do lokalizacji modułu spakowanego i wybierz i otwórz moduł. Kliknij przycisk **OK**.

    ![moduł dodawania](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Przejdź do **aplikacji Process Automation > Runbooks i kliknij + Dodaj system runbook**. W bloku **Dodawanie umiecie** dodaj kliknij pozycję **Importuj istniejący element runbook**. Wskaż plik skryptu programu Windows PowerShell dla **pliku Runbook**. Typ elementu runbook jest wybierany automatycznie. Podaj nazwę i opcjonalny opis dla wiązki uruchomieniu. Chybień. Kliknij przycisk **Utwórz**.

    ![moduł dodawania](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Projekt runbook zostanie dodany do listy śmięty. Zaznacz i kliknij ten projekt runbook.

    ![kliknij nowy-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Edytuj projekt runbooka i kliknij **okienko testu**. Podaj parametry, takie jak nazwa usługi StorSimple Device Manager, nazwa urządzenia StorSimple i subskrypcji. **Rozpocznij** test. Raport jest generowany po zakończeniu uruchamiania. Aby uzyskać więcej informacji, przejdź do [sposobu testowania śmiętu](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![test-runbook](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Sprawdź dane wyjściowe z uruchomieniu w okienku testowym. Jeśli jest spełniony, zamknij okienko. Kliknij **pozycję Publikuj** i po wyświetleniu monitu o potwierdzenie, potwierdź i opublikuj program runbook.

    ![księga-bieg](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Następne kroki

[Za pomocą usługi StorSimple Device Manager można zarządzać urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).
