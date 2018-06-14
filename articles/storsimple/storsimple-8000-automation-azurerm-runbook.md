---
title: Użycie elementu Runbook automatyzacji Azure do zarządzania urządzeniami StorSimple | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Runbook usługi Automatyzacja Azure umożliwia automatyzowanie zadań StorSimple
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
ms.openlocfilehash: cfd0e4dbb6a4f24df5ba42cd45f9c16fbe5b493c
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2017
ms.locfileid: "23934831"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Aby zarządzać urządzeniami StorSimple przy użyciu elementu runbook usługi Automatyzacja Azure

W tym artykule opisano, jak elementy runbook automatyzacji Azure są używane do zarządzania urządzeniem StorSimple 8000 serii w portalu Azure. Przykładowego elementu runbook jest dołączona do prowadzą użytkownika przez kroki konfigurowania środowiska w celu wykonania tego elementu runbook.


## <a name="configure-add-and-run-azure-runbook"></a>Konfigurowanie, Dodaj i uruchamiania elementów runbook platformy Azure

W tej sekcji przyjmuje przykładowy skrypt programu Windows PowerShell dla urządzenia StorSimple i zawiera szczegóły różnych czynności wymagane do importowania skryptu do elementu runbook, a następnie opublikować i wykonania elementu runbook.

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:

* aktywną subskrypcją platformy Azure skojarzone z Twoją usługą Menedżer StorSimple urządzenia zarejestrowane za pomocą urządzenia z serii StorSimple 8000.


* Zainstalowany na tym komputerze program Windows PowerShell 5.0 (lub hostowania dla Twojego urządzenia StorSimple w systemie Windows Server, jeśli przy użyciu jednej).


### <a name="create-automation-runbook-module-in-windows-powershell"></a>Tworzenie modułu elementu runbook usługi Automatyzacja w programie Windows PowerShell

Aby utworzyć moduł usługi Automatyzacja do zarządzania urządzeniami serii StorSimple 8000, wykonaj następujące czynności:

1. Uruchom Windows PowerShell. Utwórz nowy folder i zmień katalog do nowego folderu.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Pobierz interfejs wiersza polecenia NuGet](http://www.nuget.org/downloads) w folderze, który został utworzony w poprzednim kroku. Istnieją różne wersje _nuget.exe_. Wybierz wersję odpowiadający zestawu SDK. Każde łącze pobierania punktów bezpośrednio do _.exe_ pliku. Kliknij prawym przyciskiem myszy i Zapisz plik na komputerze, zamiast uruchomienie jej w przeglądarce.

    Można również uruchomić następujące polecenie, aby pobrać i zapisać skrypt w tym samym folderze, który został utworzony wcześniej.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Pobierz zestaw SDK zależnych.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Skrypt można pobrać przykładowy projekt GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1

    ```
5. Tworzenie modułu Runbook automatyzacji Azure do zarządzania urządzeniami z serii StorSimple 8000. W oknie programu Windows Powershell wpisz następujące polecenia:

    ```
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"
            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip

    ```

6. Sprawdź, czy plik zip modułu automatyzacji został utworzony w `C:\scripts\StorSimpleSDKTools`.

    ![Sprawdź automatyzacji — moduł](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. Następujące dane wyjściowe są prezentowane podczas tworzenia modułu automatyzacji za pomocą programu Windows PowerShell. 

    ```
    -----------------------------------------
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\WINDOWS\system32> mkdir C:\scripts\StorSimpleSDKTools

        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools


    PS C:\WINDOWS\system32> cd c:\scripts\StorSimpleSDKTools
    PS C:\scripts\StorSimpleSDKTools> wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorS
    impleSDKTools\nuget.exe
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8
    000series

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.Active
    Directory -Version 2.28.3

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Aut
    hentication -Version 2.2.9-preview

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTool
    s
    Executing nuget actions took 717.48 ms
    PS C:\scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Mo
    nitor-Backups.ps1 -Out Monitor-Backups.ps1
    PS C:\scripts\StorSimpleSDKTools> # set path variables
    PS C:\scripts\StorSimpleSDKTools>             $downloadDir = "C:\scripts\StorSimpleSDKTools"
    PS C:\scripts\StorSimpleSDKTools>             $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.Sto
    rSimple8000Series"
    PS C:\scripts\StorSimpleSDKTools>
    PS C:\scripts\StorSimpleSDKTools>             #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Se
    ries"
    PS C:\scripts\StorSimpleSDKTools>             mkdir "$moduleDir"

        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule


    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series

    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\
    lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Mic
    rosoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft
    .Rest.ClientRuntime*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $
    moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9
    -preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\l
    ib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>
    PS C:\scripts\StorSimpleSDKTools>             #Don't change the name of the Archive
    PS C:\scripts\StorSimpleSDKTools>             compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Manag
    ement.StorSimple8000Series.zip
    PS C:\scripts\StorSimpleSDKTools>

    -------------------------------------------

    ```

### <a name="import-publish-and-run-automation-runbook"></a>Importuj, publikowanie i uruchomić element runbook automatyzacji

1. Utwórz konto Uruchom jako platformy Azure automatyzacji w portalu Azure. Aby to zrobić, przejdź do **witrynę Azure marketplace > wszystko** , a następnie wyszukaj **automatyzacji**. Wybierz **kont automatyzacji**.

    ![Automatyzacja wyszukiwania](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. W **Dodaj konto automatyzacji** bloku:

    1. Podaj **nazwa** Twojego konta automatyzacji.
    2. Wybierz **subskrypcji** połączony z usługą Menedżera urządzeń StorSimple.
    3. Utwórz nową grupę zasobów lub wybierz istniejącą grupę zasobów.
    4. Wybierz **lokalizacji** (Jeśli to możliwe identyczny z którym jest uruchomiona usługa).
    5. Pozostaw wartość domyślną **utworzyć Uruchom jako konta** wybraną opcją.
    5. Opcjonalnie zaznacz **Przypnij do pulpitu nawigacyjnego**. Kliknij przycisk **Utwórz**.

        ![Utwórz —-konta automatyzacji](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

    Po pomyślnym utworzeniu konta automatyzacji, użytkownik jest powiadamiany. Aby uzyskać więcej informacji na temat sposobu tworzenia konta automatyzacji, przejdź do [Utwórz konto Uruchom jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. Aby upewnić się, że utworzono konto automatyzacji uzyskać dostęp do usługi Menedżer urządzeń StorSimple, należy przypisać odpowiednie uprawnienia do konta automatyzacji. Przejdź do **kontrola dostępu** w usłudze Menedżer StorSimple urządzenia. Kliknij przycisk **+ Dodaj** i podaj nazwę konta usługi Automatyzacja Azure. **Zapisz** ustawienia.

    ![Dodaj uprawnienia —-konta automatyzacji](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. W nowo utworzonych kont, przejdź do **udostępnione zasoby > modułów** i kliknij przycisk **+ Dodaj moduł**.

5. W **Dodaj moduł** bloku, przejdź do lokalizacji modułu zip i wybierz i otwórz moduł. Kliknij przycisk **OK**.

    ![Dodawanie modułu](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Przejdź do **automatyzacji procesu > elementów Runbook i kliknij pozycję + Dodaj element runbook**. W **dodać runbook** bloku, kliknij przycisk **importowanie istniejącego elementu runbook**. Wskaż plik skryptu programu Windows PowerShell dla **pliku Runbook**. Typ elementu runbook jest automatycznie wybierany. Podaj nazwę i opcjonalny opis dla elementu runbook. Kliknij przycisk **Utwórz**.

    ![Dodawanie modułu](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Element runbook jest dodawany do listy elementów runbook. Wybierz i kliknij ten element runbook.

    ![Kliknij — nowy — element runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Edytowanie elementu runbook, a następnie kliknij przycisk **okienku testu**. Podaj parametry, takie jak nazwa użytkownika usługi Menedżer StorSimple urządzenia, nazwę urządzenia StorSimple i subskrypcji. **Uruchom** testu. Raport jest generowany po zakończeniu uruchom. Aby uzyskać więcej informacji, przejdź do [jak przetestować element runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Test-runbook](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Sprawdź dane wyjściowe z elementu runbook w okienku testu. Jeśli spełnione, należy zamknąć okienko. Kliknij przycisk **publikowania** i po wyświetleniu monitu o potwierdzenie, upewnij się i opublikować elementu runbook.

    ![publikowanie elementu runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)


## <a name="next-steps"></a>Następne kroki

[Usługi StorSimple za pomocą Menedżera urządzeń do zarządzania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).