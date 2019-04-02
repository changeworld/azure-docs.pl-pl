---
title: Użycie elementu Runbook usługi Azure Automation do zarządzania urządzeniami StorSimple | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zautomatyzować zadania StorSimple za pomocą elementu Runbook usługi Azure Automation
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: 30d70bb7e1f868060e3b287a0cdfb117c585b9ba
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793513"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Używanie elementów runbook usługi Azure Automation do zarządzania urządzeniami StorSimple

W tym artykule opisano, jak elementy runbook usługi Azure Automation są używane do zarządzania urządzenia StorSimple 8000 series w witrynie Azure portal. Przykładowy element runbook jest dołączony przeprowadzi Cię przez kroki konfigurowania środowiska w celu wykonania tego elementu runbook.


## <a name="configure-add-and-run-azure-runbook"></a>Konfigurowanie, dodawanie i uruchamianie elementów runbook platformy Azure

W tej sekcji pobiera przykładowy skrypt programu Windows PowerShell dla usługi StorSimple oraz szczegóły różnych kroków wymaganych do zaimportuj skrypt do elementu runbook, a następnie opublikować i wykonywania elementu runbook.

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:

* aktywna subskrypcja platformy Azure skojarzone z Twoją usługą Menedżera urządzeń StorSimple zarejestrowane przy użyciu urządzenia serii StorSimple 8000.

* Zainstalowany na tym komputerze program Windows PowerShell 5.0 (lub serwera z systemem Windows hosta dla usługi StorSimple, jeśli przy użyciu jednego).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Tworzenie modułu elementu runbook usługi automation w programie Windows PowerShell

Aby utworzyć moduł usługi automation do zarządzania urządzeniami serii StorSimple 8000, wykonaj następujące czynności:

1. Uruchamianie Windows PowerShell. Utwórz nowy folder i zmień katalog do nowego folderu.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Pobierz interfejs wiersza polecenia NuGet](https://www.nuget.org/downloads) w folderze, który został utworzony w poprzednim kroku. Istnieją różne wersje _nuget.exe_. Wybierz wersję odpowiadającą zestawu SDK. Każdy link pobierania punktów bezpośrednio do _.exe_ pliku. Należy koniecznie kliknij prawym przyciskiem myszy, a następnie zapisz plik na komputer, zamiast uruchamiać go za pomocą przeglądarki.

    Można również uruchomić następujące polecenie, aby pobrać i zapisać skrypt w tym samym folderze, który został utworzony wcześniej.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Pobierz zależnego zestawu SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Pobierz skrypt z przykładowego projektu usługi GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Utwórz moduł usługi Azure Automation Runbook do zarządzania urządzeniami StorSimple 8000 Series. W oknie programu Windows Powershell wpisz następujące polecenia:

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

6. Sprawdź, czy plik zip modułu usługi automation został utworzony w `C:\scripts\StorSimpleSDKTools`.

    ![Sprawdź — moduł usługi automation —](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. Następujące dane wyjściowe są prezentowane, gdy moduł usługi automation zostały utworzone za pomocą programu Windows PowerShell.

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

### <a name="import-publish-and-run-automation-runbook"></a>Importowanie i publikowanie i uruchamianie elementu runbook usługi Automation

1. Utwórz konto Uruchom jako platformy Azure automation w witrynie Azure portal. Aby to zrobić, przejdź do **portalu Azure marketplace > wszystko** a następnie wyszukaj **automatyzacji**. Wybierz **kont usługi Automation**.

    ![Wyszukiwanie automatyzacji](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. W **Dodawanie konta usługi Automation** bloku:

   1. Podaj **nazwa** konta usługi Automation.
   2. Wybierz **subskrypcji** połączonego z usługą Menedżera urządzeń StorSimple.
   3. Utwórz nową grupę zasobów lub wybierz istniejącą grupę zasobów.
   4. Wybierz **lokalizacji** (jeśli jest to możliwe taka sama jak gdy usługa jest uruchomiona).
   5. Pozostaw wartość domyślną **Utwórz konto Uruchom jako** wybraną opcją.
   6. Opcjonalnie zaznacz **Przypnij do pulpitu nawigacyjnego**. Kliknij pozycję **Utwórz**.

       ![create-automation-account](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Po pomyślnym utworzeniu konta usługi automation, otrzymasz powiadomienie. Aby uzyskać więcej informacji na temat sposobu tworzenia konta usługi Automation, przejdź do [Tworzenie konta Uruchom jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. Aby upewnić się, że utworzono konto usługi automation można uzyskać dostęp do usługi Menedżer urządzeń StorSimple, należy przypisać odpowiednie uprawnienia do konta usługi automation. Przejdź do **kontroli dostępu** w usłudze Menedżer urządzeń StorSimple. Kliknij przycisk **+ Dodaj** i podaj nazwę konta usługi Azure Automation. **Zapisz** ustawienia.

    ![add-permissions-automation-account](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. W nowo utworzonego konta, przejdź do **zasoby udostępnione > modułów** i kliknij przycisk **+ Dodaj moduł**.

5. W **Dodaj moduł** bloku, przejdź do lokalizacji plików z rozszerzeniem zip modułu i wybierz i otwórz moduł. Kliknij przycisk **OK**.

    ![Dodawanie modułu](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Przejdź do **automatyzacji procesów > elementów Runbook i kliknij pozycję + Dodaj element runbook**. W **Dodaj runbook** bloku kliknij **importowanie istniejącego elementu runbook**. Wskaż plik skryptu programu Windows PowerShell dla **plikowi elementu Runbook**. Typ elementu runbook są wybierane automatycznie. Podaj nazwę i opcjonalny opis dla elementu runbook. Kliknij pozycję **Utwórz**.

    ![Dodawanie modułu](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Element runbook zostanie dodany do listy elementów runbook. Wybierz i kliknij ten element runbook.

    ![Kliknij przycisk — Nowy — element runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Edytuj element runbook, a następnie kliknij przycisk **okienko testowania**. Podaj parametry, takie jak nazwa użytkownika usługi Menedżer urządzeń StorSimple, nazwa urządzenia StorSimple i subskrypcji. **Rozpocznij** testu. Raport jest generowany, gdy uruchomienie zostanie zakończone. Aby uzyskać więcej informacji, przejdź do [jak przetestować element runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Test-runbook](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Sprawdź dane wyjściowe z elementu runbook w okienku testu. Jeśli spełnione, należy zamknąć okienko. Kliknij przycisk **Publikuj** i po wyświetleniu monitu o potwierdzenie, upewnij się i publikowanie elementu runbook.

    ![publikowanie elementu runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Kolejne kroki

[Usługa Menedżer urządzeń StorSimple użycia do zarządzania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).
