---
title: Konfigurowanie urządzenia Microsoft Azure Data Box Disk | Microsoft Docs
description: Użyj tego samouczka, aby dowiedzieć się, jak skonfigurować urządzenie Azure Data Box Disk
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/28/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 6fcc7823a7e2f2f1e280622a1fa05d4417a71546
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143486"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Samouczek: rozpakowywanie, podłączanie i odblokowywanie urządzenia Azure Data Box Disk

W tym samouczku opisano, jak rozpakować, podłączyć i odblokować urządzenie Azure Data Box Disk.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rozpakowywanie urządzenia Data Box Disk
> * Podłączanie i odblokowywanie urządzenia Data Box Disk.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [samouczek dotyczący zamawiania urządzenia Azure Data Box](data-box-disk-deploy-ordered.md).
2. Dyski zostały do Ciebie dostarczone, a stan zadania w portalu zmienił się na **Dostarczono**.
3. Masz komputer hosta, na którym możesz zainstalować narzędzie do odblokowywania dysków Data Box. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-disk-system-requirements.md).
    - [Zainstalowanie programu Windows PowerShell 4](https://www.microsoft.com/download/details.aspx?id=40855).
    - [Zainstalowanie środowiska .NET Framework 4.5.1](https://www.microsoft.com/download/details.aspx?id=30653).
    - Włączona [funkcja BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server).
    - Zainstalowana platforma [Windows Management Framework 4](https://www.microsoft.com/en-us/download/details.aspx?id=40855). 

## <a name="unpack-your-disks"></a>Rozpakowywanie dysków

 Aby rozpakować dyski, wykonaj następujące czynności.

1. Urządzenia Data Box Disk są przesyłane w niewielkim opakowaniu wysyłkowym. Otwórz opakowanie i wyjmij zawartość. Sprawdź, czy opakowanie zawiera od 1 do 5 dysków półprzewodnikowych (SSD) oraz jeden kabel połączeniowy USB dla każdego dysku. Sprawdź, czy opakowanie nie ma widocznych uszkodzeń lub innych śladów naruszenia. 

    ![Opakowanie wysyłkowe urządzeń Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Jeśli opakowanie wysyłkowe nosi ślady naruszenia lub jest poważnie uszkodzone, nie otwieraj go. Skontaktuj się z pracownikiem pomocy technicznej firmy Microsoft, który pomoże Ci ocenić, czy dyski są w dobrym stanie, czy konieczne jest wysłanie nowych.
3. Sprawdź, czy opakowanie zawiera przezroczystą koszulkę z etykietą wysyłkową do wysyłki zwrotnej (pod aktualną etykietą). W przypadku utraty lub uszkodzenia etykiety zwrotnej możesz wydrukować nową etykietę pobraną z witryny Azure Portal. 

    ![Etykieta wysyłkowa urządzenia Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Zachowaj opakowanie i piankę wypełniającą do wysyłki zwrotnej dysków.

## <a name="connect-and-unlock-your-disks"></a>Podłączanie i odblokowywanie dysków

Aby podłączyć i odblokować dyski, wykonaj następujące czynności.

1. Podłącz dysk do komputera z obsługiwaną wersją systemu Windows, wymienioną w wymaganiach wstępnych, za pomocą dołączonego kabla. 

    ![Podłączanie urządzenia Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. W witrynie Azure Portal przejdź do pozycji **Ogólne > Szczegóły urządzenia**. 
3. Kliknij polecenie **Pobierz narzędzie do odblokowywania dysków Data Box**. 

    ![Pobieranie narzędzia do odblokowywania dysków Data Box](media/data-box-disk-deploy-set-up/data-box-disk-unload1.png)     

4. Wyodrębnij narzędzie na komputerze, którego użyjesz do skopiowania danych.
5. Otwórz okno wiersza polecenia lub uruchom program Windows PowerShell jako administrator na tym samym komputerze.
6. (Opcjonalnie) Aby sprawdzić, czy komputer, którego używasz do odblokowania dysku, spełnia wymagania dotyczące systemu operacyjnego, uruchom polecenie SystemCheck. Poniżej pokazano przykładowe dane wyjściowe. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

7. W witrynie Azure Portal przejdź do pozycji **Ogólne > Szczegóły urządzenia**. Skopiuj kod dostępu, używając ikony kopiowania.
8. Uruchom plik `DataBoxDiskUnlock.exe` i podaj klucz dostępu. Zostanie wyświetlona litera dysku przypisana do danego dysku. Poniżej pokazano przykładowe dane wyjściowe.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkeys (format: passkey1;passkey2;passkey3):
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

9. Podłącz każdy z pozostałych dysków i powtórz kroki od 6 do 8. Użyj polecenia „help”, jeśli potrzebujesz pomocy podczas korzystania z narzędzia do odblokowywania dysków Data Box.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKeys:<passkey_list_separated_by_semicolon>
    
    Example: DataBoxUnlock /PassKeys:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKeys:       Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
10. Po odblokowaniu dysku możesz wyświetlić jego zawartość.    

    ![Zawartość urządzenia Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące urządzenia Azure Data Box Disk, takie jak:

> [!div class="checklist"]
> * Rozpakowywanie urządzenia Data Box Disk
> * Podłączanie i odblokowywanie urządzeń Data Box Disk


Przejdź do następnego samouczka, aby dowiedzieć się, jak skopiować dane na urządzenie Data Box Disk.

> [!div class="nextstepaction"]
> [Kopiowanie danych na urządzenie Data Box Disk](./data-box-disk-deploy-copy-data.md)

