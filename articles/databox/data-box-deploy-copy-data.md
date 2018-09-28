---
title: Kopiowanie danych na urządzenie Microsoft Azure Data Box | Microsoft Docs
description: Dowiedz się, jak skopiować dane na urządzenie Azure Data Box
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
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 0204445464a9d61b4e25be1d71373ce8394b32f0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957675"
---
# <a name="tutorial-copy-data-to-azure-data-box"></a>Samouczek: kopiowanie danych na urządzenie Azure Data Box 

W tym samouczku opisano sposób nawiązywania połączenia i kopiowania danych z komputera-hosta za pomocą lokalnego interfejsu internetowego użytkownika. Opisano też sposób przygotowywania do wysłania urządzenia Data Box.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawiązywanie połączenia z urządzeniem Data Box
> * Kopiowanie danych na urządzenie Data Box
> * Przygotowywanie do wysłania urządzenia Data Box.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [samouczek dotyczący konfigurowania urządzenia Azure Data Box](data-box-deploy-set-up.md).
2. Urządzenie Data Box zostało do Ciebie dostarczone, a stan zamówienia w portalu to **Dostarczono**.
3. Masz komputer-host zawierający dane, które mają zostać skopiowane na urządzenie Data Box. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-system-requirements.md).
    - Połączenie z siecią o dużej szybkości. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępne, można użyć połączenia danych 1 GbE, ale będzie miało to wpływ na szybkość kopiowania. 

## <a name="connect-to-data-box"></a>Nawiązywanie połączenia z urządzeniem Data Box

W zależności od wybranego konta magazynu dla urządzenia Data Box są tworzone następujące elementy:
- Maksymalnie trzy udziały dla każdego skojarzonego konta magazynu (GPv1 i GPv2).
- Maksymalnie jeden udział dla konta magazynu w warstwie Premium lub konta magazynu obiektów blob. 

W obszarze udziałów blokowych obiektów blob i stronicowych obiektów blob jednostki pierwszego poziomu są kontenerami, a jednostki drugiego poziomu są obiektami blob. W obszarze udziałów dla usługi Azure Files jednostki pierwszego poziomu są udziałami, a jednostki drugiego poziomu są plikami.

Rozważmy następujący przykład. 

- Konto magazynu: *Mystoracct*
- Udział dla blokowych obiektów blob: *Mystoracct_BlockBlob/my-container/blob*
- Udział dla stronicowych obiektów blob: *Mystoracct_PageBlob/my-container/blob*
- Udział pliku: *Mystoracct_AzFile/my-share*

W zależności od tego, czy Twoje urządzenie Data Box jest podłączone do komputera-hosta z systemem Windows Server, czy do hosta z systemem Linux, czynności wymagane do nawiązania połączenia i kopiowania mogą być różne.

### <a name="connect-via-smb"></a>Nawiązywanie połączenia przy użyciu protokołu SMB 

Jeśli używasz komputera-hosta z systemem Windows Server, wykonaj następujące czynności, aby nawiązać połączenie z urządzeniem Data Box.

1. Pierwszym krokiem jest uwierzytelnienie i uruchomienie sesji. Przejdź do pozycji **Połącz i skopiuj**. Kliknij przycisk **Pobierz poświadczenia**, aby pobrać poświadczenia dostępu do udziałów skojarzonych z kontem magazynu. 

    ![Pobieranie poświadczeń udziału 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. W oknie dialogowym Uzyskiwanie dostępu do udziału i kopiowanie danych skopiuj wartości pól **Nazwa użytkownika** i **Hasło** odpowiedniego udziału. Kliknij przycisk **OK**.
    
    ![Pobieranie poświadczeń udziału 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Uzyskaj dostęp do udziałów skojarzonych z Twoim kontem magazynu (Mystoracct w poniższym przykładzie). Aby uzyskać dostęp do udziałów, użyj ścieżki `\\<IP of the device>\ShareName`. W zależności od formatu danych nawiąż połączenie z udziałami (użyj nazwy udziału), korzystając z następującego adresu: 
    - *\\<IP address of the device>\Mystoracct_Blob*
    - *\\<IP address of the device>\Mystoracct_Page*
    - *\\<IP address of the device>\Mystoracct_AzFile*
    
    Aby nawiązać połączenie z udziałami z Twojego komputera-hosta, otwórz okno polecenia. W wierszu polecenia wpisz polecenie:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Po wyświetleniu monitu wprowadź hasło dla udziału. W poniższym przykładzie pokazano nawiązywanie połączenia z udziałem za pomocą poprzedniego polecenia.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Naciśnij klawisze Windows + R. W oknie **Uruchamianie** podaj `\\<device IP address>`. Kliknij przycisk **OK**. Spowoduje to otworzenie Eksploratora plików.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

5. Teraz udziały powinny być widoczne jako foldery. Utwórz folder dla plików, które mają zostać skopiowane (w tym przypadku jest to folder templates). Czasami na ikonie folderu może być wyświetlany szary krzyżyk. Ten krzyżyk nie oznacza warunku błędu. Foldery są oflagowywane przez aplikację w celu śledzenia stanu.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) 

### <a name="connect-via-nfs"></a>Nawiązywanie połączenia przy użyciu sieciowego systemu plików 

Jeśli używasz komputera-hosta z systemem Linux, wykonaj następujące czynności, aby skonfigurować urządzenie Data Box na potrzeby zezwalania na dostęp do klientów sieciowego systemu plików.

1. Podaj adresy IP dozwolonych klientów, którzy mogą uzyskiwać dostęp do udziału. W lokalnym internetowym interfejsie użytkownika przejdź do strony **Połącz i skopiuj**. W obszarze **Ustawienia sieciowego systemu plików** kliknij przycisk **Dostęp klienta do sieciowego systemu plików**. 

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Podaj adres IP klienta sieciowego systemu plików i kliknij przycisk **Dodaj**. Powtarzając ten krok, możesz skonfigurować dostęp dla wielu klientów sieciowego systemu plików. Kliknij przycisk **OK**.

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Upewnij się, że na komputerze-hoście z systemem Linux zainstalowano [obsługiwaną wersję](data-box-system-requirements.md) klienta sieciowego systemu plików. Użyj konkretnej wersji dla określonej dystrybucji systemu Linux. 

3. Po zainstalowaniu klienta sieciowego systemu plików użyj następującego polecenia, aby zainstalować udział sieciowego systemu plików na Twoim urządzeniu Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Poniższy przykład pokazuje, jak nawiązać połączenie z urządzeniem Data Box za pomocą sieciowego systemu plików. Adres IP urządzenia Data Box: IP `10.161.23.130`. Udział `Mystoracct_Blob` jest zainstalowany na maszynie ubuntuVM. Punkt instalacji: `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Kopiowanie danych na urządzenie Data Box

Po nawiązaniu połączenia z udziałami urządzenia Data Box następnym krokiem jest skopiowanie danych. Przed rozpoczęciem kopiowania danych należy uwzględnić następujące kwestie:

- Upewnij się, że dane są kopiowane do udziałów odpowiadających właściwym formatom danych. To znaczy na przykład, że dane blokowych obiektów blob są kopiowane do udziału dla blokowych obiektów blob. Jeśli format danych nie pasuje do odpowiedniego typu udziału, na późniejszym etapie przekazywanie danych na platformę Azure zakończy się niepowodzeniem.
-  Podczas kopiowania danych upewnij się, że rozmiar danych jest zgodny z ograniczeniami rozmiaru opisanymi w temacie [Azure storage and Data Box limits](data-box-limits.md) (Ograniczenia usług Azure Storage i urządzenia Data Box). 
- Jeśli dane przekazywane przy użyciu urządzenia Data Box będą jednocześnie przekazywane przez inne aplikacje, poza urządzeniem Data Box, skutkiem może być niepowodzenie zadania przekazywania oraz uszkodzenie danych.
- Nie zaleca się jednoczesnego używania protokołu SMB i sieciowego systemu plików ani kopiowania tych samych danych do tego samego końcowego miejsca docelowego na platformie Azure. W takich przypadkach nie można określić ostatecznego wyniku.

### <a name="copy-data-via-smb"></a>Kopiowanie danych za pośrednictwem protokołu SMB

Po nawiązaniu połączenia z udziałem SMB zainicjuj kopiowanie danych. 

Do kopiowania danych możesz użyć dowolnego narzędzia kopiowania plików zgodnego z protokołem SMB, na przykład narzędzia Robocopy. Za pomocą narzędzia Robocopy można zainicjować wiele zadań kopiowania. Użyj następującego polecenia:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Atrybuty opisano w poniższej tabeli.
    
|Atrybut  |Opis  |
|---------|---------|
|/e     |Kopiuje podkatalogi, włącznie z pustymi katalogami.         |
|/r:     |Określa liczbę ponownych prób w przypadku niepowodzenia kopiowania.         |
|/w:     |Określa czas oczekiwania pomiędzy ponownymi próbami w sekundach.         |
|/is     |Zawiera te same pliki.         |
|/nfl     |Określa, że nazwy plików nie mają być rejestrowane w dzienniku.         |
|/ndl    |Określa, że nazwy katalogów nie mają być rejestrowane w dzienniku.        |
|/np     |Określa, że postęp operacji kopiowania (liczba skopiowanych do tej pory plików lub katalogów) nie będzie wyświetlany. Wyświetlanie postępu znacznie obniża wydajność.         |
|/MT     | Użyj wielowątkowości (zaleca się użycie 32 lub 64 wątków). Ta opcja nie jest używana z plikami zaszyfrowanymi. Może być konieczne oddzielne plików zaszyfrowanych od niezaszyfrowanych. Jednak kopiowanie jednowątkowe znacznie obniża wydajność.           |
|/fft     | Użyj, aby zmniejszyć stopień szczegółowości sygnatury czasu dla każdego systemu plików.        |
|/b     | Kopiuje pliki w trybie tworzenia kopii zapasowej.        |
|/z    | Kopiuje pliki w trybie ponownego uruchomienia. Użyj tego atrybutu w przypadku niestabilnego środowiska. Ta opcja powoduje zmniejszenie przepływności ze względu na dodatkowe rejestrowanie.      |
| /zb     | Używa trybu ponownego uruchomienia. W przypadku odmowy dostępu ta opcja używa trybu tworzenia kopii zapasowej. Ta opcja powoduje zmniejszenie przepływności ze względu na tworzenie punktów kontrolnych.         |
|/efsraw     | Kopiuje wszystkie zaszyfrowane pliki w surowym trybie EFS. Używaj tej opcji tylko względem zaszyfrowanych plików.         |
|log+:<LogFile>| Dołącza dane wyjściowe do istniejącego pliku dziennika.|    
 
Poniższy przykład przedstawia dane wyjściowe polecenia kopiowania plików na urządzenie Data Box za pomocą narzędzia Robocopy.
    
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
    
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
    C:\Users>
       

Aby zoptymalizować wydajność, użyj poniższych parametrów polecenia robocopy podczas kopiowania danych.

|    Platforma    |    Przeważnie małe pliki < 512 KB                           |    Przeważnie średnie pliki od 512 KB do 1 MB                      |    Przeważnie duże pliki > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 sesje narzędzia Robocopy <br> 16 wątków na sesję    |    3 sesje narzędzia Robocopy <br> 16 wątków na sesję    |    2 sesje narzędzia Robocopy <br> 24 wątki na sesję    |  |


Aby uzyskać więcej informacji na temat polecenia Robocopy, przejdź do artykułu [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Polecenie Robocopy i kilka przykładów).

Otwórz folder docelowy, aby wyświetlić i zweryfikować skopiowane pliki. Jeśli podczas procesu kopiowania wystąpiły jakiekolwiek błędy, pobierz pliki z błędami, które pomogą w rozwiązywaniu problemów.

W celu zapewnienia integralności danych podczas kopiowania obliczana jest suma kontrolna. Po zakończeniu kopiowania sprawdź ilość używanego i wolnego miejsca na urządzeniu.
    
   ![Sprawdzanie wolnego i używanego miejsca na pulpicie nawigacyjnym](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

### <a name="copy-data-via-nfs"></a>Kopiowanie danych za pośrednictwem sieciowego systemu plików

Jeśli korzystasz z komputera-hosta z systemem Linux, użyj narzędzia do kopiowania podobnego do narzędzia Robocopy. W systemie Linux są dostępne na przykład narzędzia [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) lub [Ultracopier](https://ultracopier.first-world.info/).  

Polecenie cp jest jedną z najlepszych opcji do kopiowania katalogów. Aby uzyskać więcej informacji dotyczących użycia, przejdź do [stron man narzędzia cp](http://man7.org/linux/man-pages/man1/cp.1.html).

W przypadku korzystania z opcji rsync na potrzeby kopiowania wielowątkowego należy przestrzegać następujących wytycznych:

 - Zainstaluj pakiet **CIFS Utils** lub **NFS Utils** w zależności od systemu plików używanego przez Twojego klienta systemu Linux.

    `sudo apt-get install cifs-utils` `sudo apt-get install nfs-utils`

 -  Zainstaluj narzędzia **Rsync** i **Parallel** (polecenia różnią się w zależności od wersji dystrybucji systemu Linux).

    `sudo apt-get install rsync`
    `sudo apt-get install parallel` 

 - Utwórz punkt instalacji.

    `sudo mkdir /mnt/databox`

 - Zainstaluj wolumin.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

 - Zdubluj strukturę katalogów folderów.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

 - Skopiuj pliki. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     gdzie j określa liczbę przetwarzań równoległych, X = liczba równoległych kopii

     Na początku zaleca się użycie 16 równoległych kopii i zwiększanie liczby wątków w zależności od dostępności zasobów.

## <a name="prepare-to-ship"></a>Przygotowanie do wysłania

Ostatnim krokiem jest przygotowanie urządzenia do wysłania. W tym kroku wszystkie udziały urządzenia zostają przeniesione do trybu offline. Po rozpoczęciu przygotowywania urządzenia do wysłania dostęp do udziałów nie jest już możliwy.
1. Przejdź do pozycji **Przygotowanie do wysłania** i kliknij przycisk **Rozpocznij przygotowywanie**. 
   
    ![Przygotowanie do wysłania 1](media/data-box-deploy-copy-data/prepare-to-ship1.png)

2. Jeśli nie włączono tworzenia sumy kontrolnej, zostanie wyświetlona opcja umożliwiająca włączenie tej funkcji. W celu zachowania integralności danych zaleca się wykonanie weryfikacji sumy kontrolnej. Wybranie pozycji **Włącz sumę kontrolną** spowoduje rozpoczęcie obliczania sumy kontrolnej. Ten proces może zająć trochę czasu w zależności od rozmiaru danych. Kliknij przycisk **Rozpocznij przygotowywanie**.
    1. Udziały urządzenia zostaną przeniesione w tryb offline, a urządzenie zostanie zablokowane na czas przygotowywania do wysłania.
        
        ![Przygotowanie do wysłania 1](media/data-box-deploy-copy-data/prepare-to-ship2.png) 
   
    2. Po zakończeniu przygotowywania urządzenia jego stan zostanie zaktualizowany do wartości *Gotowe do wysyłki*. 
        
        ![Przygotowanie do wysłania 1](media/data-box-deploy-copy-data/prepare-to-ship3.png)

    3. Pobierz listę plików (manifest), która została skopiowana w ramach tego procesu. Później możesz użyć tej listy, aby zweryfikować pliki przekazane na platformę Azure.
        
        ![Przygotowanie do wysłania 1](media/data-box-deploy-copy-data/prepare-to-ship4.png)

3. Zamknij urządzenie. Przejdź do strony **Zamknij lub uruchom ponownie** i kliknij przycisk **Zamknij**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **OK**, aby kontynuować.
4. Odłącz kable. Następnym krokiem jest wysłanie urządzenia do firmy Microsoft.

 
<!--## Appendix - robocopy parameters

This section describes the robocopy parameters used when copying the data to optimize the performance.

|    Platform    |    Mostly small files < 512 KB                           |    Mostly medium  files 512 KB-1 MB                      |    Mostly large files > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 Robocopy sessions <br> 16 threads per sessions    |    3 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 24 threads per sessions    |  |
|    Data Box Heavy     |    6 Robocopy sessions <br> 24 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |   
|    Data Box Disk         |    4 Robocopy sessions <br> 16 threads per sessions             |    2 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 16 threads per sessions    |   
-->

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
> * Nawiązywanie połączenia z urządzeniem Data Box
> * Kopiowanie danych na urządzenie Data Box
> * Przygotowywanie do wysłania urządzenia Data Box

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować i skopiować dane na urządzenie Data Box.

> [!div class="nextstepaction"]
> [Wysyłka urządzenia Azure Data Box do firmy Microsoft](./data-box-deploy-picked-up.md)

