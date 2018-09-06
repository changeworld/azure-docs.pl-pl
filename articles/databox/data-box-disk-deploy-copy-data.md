---
title: Kopiowanie danych na urządzenie Microsoft Azure Data Box Disk | Microsoft Docs
description: Z tego samouczka dowiesz się, jak skopiować dane na urządzenie Azure Data Box Disk
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
ms.date: 09/05/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: f25d0b3522658d5fcd4b34110cb03b624dd9e7b1
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841509"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Samouczek: kopiowanie danych na urządzenie Azure Data Box Disk i ich weryfikacja

W tym samouczku opisano, jak skopiować dane z komputera hosta, a następnie wygenerować sumy kontrolne, aby sprawdzić integralność danych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Kopiowanie danych na urządzenie Data Box Disk
> * Weryfikowanie integralności danych

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:
- Ukończono samouczek [Instalowanie i konfigurowanie urządzenia Azure Data Box Disk](data-box-disk-deploy-set-up.md).
- Dyski są rozpakowane i włączone.
- Masz komputer hosta, z którego chcesz skopiować dane na dyski. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-disk-system-requirements.md).
    - [Zainstalowanie programu Windows PowerShell 4](https://www.microsoft.com/download/details.aspx?id=40855).
    - [Zainstalowanie środowiska .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653).


## <a name="copy-data-to-disks"></a>Kopiowanie danych na dyski

Aby podłączyć urządzenia Data Box Disk do komputera i skopiować na nie dane, wykonaj poniższe czynności.

1. Wyświetl zawartość odblokowanego dysku. 

    ![Wyświetlanie zawartości dysku](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Skopiuj dane, które mają zostać zaimportowane jako blokowe obiekty blob, do folderu BlockBlob. Analogicznie skopiuj dane w formatach takich jak VHD/VHDX do folderu PageBlob. 

    Dla każdego podfolderu w folderach BlockBlob i PageBlob zostanie utworzony kontener na koncie usługi Azure Storage. Wszystkie pliki w folderach BlockBlob i PageBlob zostaną skopiowane do domyślnego kontenera `$root` na koncie usługi Azure Storage. Pliki w kontenerze `$root` są zawsze przekazywane jako blokowe obiekty blob.

    Jeśli w katalogu głównym istnieją jakiekolwiek pliki lub foldery, należy przenieść je do innego folderu przed rozpoczęciem kopiowania danych.

    Nazwy kontenerów i obiektów blob muszą być zgodne z konwencją nazewnictwa platformy Azure.

    |Jednostka   |Konwencja  |
    |---------|---------|
    |Nazwy kontenerów blokowych i stronicowych obiektów blob     |Muszą zaczynać się literą lub cyfrą i mogą zawierać tylko małe litery, cyfry oraz łączniki (-). Bezpośrednio przed łącznikiem (-) i bezpośrednio po nim musi znajdować się cyfra lub litera. Nazwy nie mogą zawierać sąsiadujących ze sobą łączników. <br>Muszą być prawidłowymi nazwami DNS o długości od 3 do 63 znaków.          |
    |Nazwy blokowych i stronicowych obiektów blob    |W nazwach obiektów blob jest rozróżniana wielkość liter. Mogą zawierać dowolną kombinację znaków. <br>Nazwa obiektu blob musi zawierać od 1 do 1024 znaków.<br>Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki.<br>Liczba segmentów ścieżki w nazwie obiektu blob nie może przekraczać 254. Segment ścieżki to ciąg znajdujący się pomiędzy następującymi po sobie znakami ogranicznika (na przykład ukośnikami „/”), co odpowiada nazwie katalogu wirtualnego.         |

    > [!IMPORTANT] 
    > Wszystkie kontenery i obiekty blob powinny mieć nazwy zgodne z [konwencją nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions). W przypadku niezgodności z tymi regułami przekazywanie danych na platformę Azure zakończy się niepowodzeniem.

3. Rozmiar żadnego z kopiowanych plików nie może przekraczać ~4.7 TiB w przypadku blokowych obiektów blob, a ~8 TiB w przypadku stronicowych obiektów blob. 
4. Możesz skopiować dane, przeciągając je i upuszczając w Eksploratorze plików. Możesz też skopiować dane za pomocą dowolnego narzędzia kopiowania danych zgodnego z SMB, na przykład narzędzia Robocopy. Aby rozpocząć wiele zadań kopiowania, użyj następującego polecenia narzędzia Robocopy:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    Parametry i opcje tego polecenia przedstawiono w poniższej tabeli:
    
    |Parametry/opcje  |Opis |
    |--------------------|------------|
    |Element źródłowy            | Określa ścieżkę do katalogu źródłowego.        |
    |Element docelowy       | Określa ścieżkę do katalogu docelowego.        |
    |/E                  | Kopiuje podkatalogi, włącznie z pustymi katalogami. |
    |/MT[:N]             | Tworzy kopie wielowątkowe zawierające N wątków, gdzie N to liczba całkowita od 1 do 128. <br>Domyślna wartość N to 8.        |
    |/R: <N>             | Określa liczbę ponownych prób w przypadku niepowodzenia kopiowania. Domyślna wartość N to 1 000 000 (milion ponownych prób).        |
    |/W: <N>             | Określa czas oczekiwania pomiędzy ponownymi próbami w sekundach. Domyślna wartość N to 30 (30 sekund oczekiwania).        |
    |/NFL                | Określa, że nazwy plików nie mają być rejestrowane w dzienniku.        |
    |/NDL                | Określa, że nazwy katalogów nie mają być rejestrowane w dzienniku.        |
    |/FFT                | Zakłada czas plików w formacie FAT (dokładność do dwóch sekund).        |
    |/Log:<Log File>     | Zapisuje dane wyjściowe stanu w pliku dziennika (zastępuje istniejący plik dziennika).         |

    Można użyć równolegle wielu dysków, uruchamiając wiele zadań na każdym z nich. 

6. Gdy zadanie będzie w toku, sprawdzaj stan kopiowania. Poniższy przykład przedstawia dane wyjściowe polecenia kopiowania plików na urządzenie Data Box Disk za pomocą narzędzia Robocopy.

    ```
    C:\Users>robocopy
        -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:53 PM
           Simple Usage :: ROBOCOPY source destination /MIR
    
                 source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                   /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                   Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
       Files :        17        17         0         0         0         0
       Bytes :     3.9 k     3.9 k         0         0         0         0
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    
7. Otwórz folder docelowy, aby wyświetlić i zweryfikować skopiowane pliki. Jeśli podczas procesu kopiowania wystąpiły jakiekolwiek błędy, pobierz pliki dziennika, które pomogą w rozwiązywaniu problemów. Pliki dziennika znajdują się w lokalizacji określonej w narzędziu Robocopy.
 


> [!IMPORTANT]
> - Twoim obowiązkiem jest zapewnienie, że dane zostały skopiowane do folderów odpowiadających właściwym formatom danych. To znaczy na przykład, że dane blokowych obiektów blob zostały skopiowane do folderu BlockBlob. Jeśli format danych nie pasuje do folderu (typu magazynu), na późniejszym etapie przekazywanie danych na platformę Azure zakończy się niepowodzeniem.
> -  Podczas kopiowania danych upewni się, że rozmiar danych jest zgodny z ograniczeniami rozmiaru opisanymi w temacie [Azure storage and Data Box Disk limits (Ograniczenia usług Azure Storage i Data Box Disk)](data-box-disk-limits.md). 
> - Jeśli dane przekazywane przy użyciu usługi Data Box Disk będą jednocześnie przekazywane przez inne aplikacje, poza usługą Data Box Disk, skutkiem może być niepowodzenie zadania przekazywania oraz uszkodzenie danych.

## <a name="verify-data-integrity"></a>Weryfikowanie integralności danych

Aby sprawdzić integralność danych, wykonaj następujące czynności.

1. Uruchom plik `AzureExpressDiskService.ps1` w celu walidacji sum kontrolnych. W Eksploratorze plików przejdź do folderu *AzureImportExport* na odpowiednim dysku. Kliknij plik prawym przyciskiem myszy i wybierz polecenie **Uruchom za pomocą programu PowerShell**. 

    ![Generowanie sum kontrolnych](media/data-box-disk-deploy-copy-data/data-box-disk-checksum.png)

2. Czas wykonywania tej operacji zależy od rozmiaru danych. Po zakończeniu wykonywania skryptu zostanie wyświetlone podsumowanie procesu sprawdzania integralności danych wraz z czasem trwania tego procesu. Naciśnij klawisz **Enter**, aby zamknąć okno polecenia.

    ![Dane wyjściowe sumy kontrolnej](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

3. Jeśli używasz wielu dysków, uruchom to polecenie dla każdego dysku.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące urządzenia Azure Data Box Disk, takie jak:

> [!div class="checklist"]
> * Kopiowanie danych na urządzenie Data Box Disk
> * Weryfikowanie integralności danych

Przejdź do następnego samouczka, aby dowiedzieć się, jak odesłać urządzenie Data Box Disk i zweryfikować przekazanie danych na platformę Azure.

> [!div class="nextstepaction"]
> [Wysyłka zwrotna urządzenia Azure Data Box do firmy Microsoft](./data-box-disk-deploy-picked-up.md)

