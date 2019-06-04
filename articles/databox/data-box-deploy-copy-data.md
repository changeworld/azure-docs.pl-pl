---
title: Samouczek, aby skopiować dane za pośrednictwem protokołu SMB na platformie Azure Data Box | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane na urządzenie Azure Data Box za pośrednictwem protokołu SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: alkohli
ms.openlocfilehash: 6b2a0655173405008e0bccf3e31a8db391da6127
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496292"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Samouczek: Kopiowanie danych na urządzenie Azure Data Box Disk za pośrednictwem protokołu SMB

W tym samouczku opisano sposób nawiązywania połączenia i kopiowania danych z komputera-hosta za pomocą lokalnego internetowego interfejsu użytkownika.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem Data Box
> * Kopiowanie danych na urządzenie Data Box


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: konfigurowanie usługi Azure Data Box](data-box-deploy-set-up.md).
2. Urządzenie Data Box zostało do Ciebie dostarczone, a stan zamówienia w portalu to **Dostarczono**.
3. Masz komputer-host zawierający dane, które mają zostać skopiowane na urządzenie Data Box. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-system-requirements.md).
    - Połączenie z siecią o dużej szybkości. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępne, użyj połączenia danych 1 GbE (będzie to miało negatywny wpływ na szybkość kopiowania).

## <a name="connect-to-data-box"></a>Nawiązywanie połączenia z urządzeniem Data Box

W zależności od wybranego konta magazynu dla urządzenia Data Box są tworzone następujące elementy:
- Maksymalnie trzy udziały dla każdego skojarzonego konta magazynu (GPv1 i GPv2).
- Jeden udział dla usługi premium storage.
- Jeden udział dla konta usługi blob storage.

W obszarze udziałów blokowych obiektów blob i stronicowych obiektów blob jednostki pierwszego poziomu są kontenerami, a jednostki drugiego poziomu są obiektami blob. W obszarze udziałów dla usługi Azure Files jednostki pierwszego poziomu są udziałami, a jednostki drugiego poziomu są plikami.

W poniższej tabeli przedstawiono ścieżkę UNC do udziałów na urządzeniu Data Box i adres URL ścieżki w usłudze Azure Storage, pod który przekazywane są dane. Ostateczny adres URL w usłudze Azure Storage można uzyskać ze ścieżki udziału UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Blokowe obiekty blob platformy Azure | <li>Ścieżka UNC do udziałów: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Stronicowe obiekty blob platformy Azure  | <li>Ścieżka UNC do udziałów: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Ścieżka UNC do udziałów: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Jeśli używasz komputera-hosta z systemem Windows Server, wykonaj następujące kroki, aby nawiązać połączenie z urządzeniem Data Box.

1. Pierwszym krokiem jest uwierzytelnienie i uruchomienie sesji. Przejdź do pozycji **Połącz i skopiuj**. Kliknij przycisk **Pobierz poświadczenia**, aby pobrać poświadczenia dostępu do udziałów skojarzonych z kontem magazynu. 

    ![Pobieranie poświadczeń udziału 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. W oknie dialogowym Uzyskiwanie dostępu do udziału i kopiowanie danych skopiuj wartości pól **Nazwa użytkownika** i **Hasło** odpowiedniego udziału. Kliknij przycisk **OK**.
    
    ![Pobieranie poświadczeń udziału 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Aby uzyskać dostęp do udziałów skojarzonych z kontem magazynu (*devicemanagertest1* w poniższym przykładzie) z komputera-hosta, otwórz okno polecenia. W wierszu polecenia wpisz polecenie:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    W zależności od formatu danych ścieżki udziałów są następujące:
    - Blokowe obiekty blob platformy Azure — `\\10.126.76.172\devicemanagertest1_BlockBlob`
    - Stronicowe obiekty blob platformy Azure — `\\10.126.76.172\devicemanagertest1_PageBlob`
    - Pliki platformy Azure — `\\10.126.76.172\devicemanagertest1_AzFile`
    
4. Po wyświetleniu monitu wprowadź hasło dla udziału. W poniższym przykładzie pokazano nawiązywanie połączenia z udziałem za pomocą poprzedniego polecenia.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Naciśnij klawisze Windows + R. W oknie **Uruchamianie** podaj `\\<device IP address>`. Kliknij przycisk **OK**, aby otworzyć Eksploratora plików.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Teraz udziały powinny być widoczne jako foldery.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)    

    **Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu**. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.
    
W przypadku korzystania z klienta systemu Linux użyj następującego polecenia, aby zainstalować udział SMB. Parametr „vers” poniżej to wersja protokołu SMB obsługiwana przez Twój host z systemem Linux. Podłącz odpowiednią wersję w poleceniu poniżej. W przypadku wersji protokołu SMB obsługiwanych przez urządzenia Data Box zobacz [Obsługiwane systemy plików dla klientów systemu Linux](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients) 

    `sudo mount -t nfs -o vers=2.1 10.126.76.172:/devicemanagertest1_BlockBlob /home/databoxubuntuhost/databox`
    


## <a name="copy-data-to-data-box"></a>Kopiowanie danych na urządzenie Data Box

Po nawiązaniu połączenia z udziałami urządzenia Data Box następnym krokiem jest skopiowanie danych. Przed rozpoczęciem kopiowania danych należy uwzględnić następujące kwestie:

- Upewnij się, że dane są kopiowane do udziałów odpowiadających właściwym formatom danych. To znaczy na przykład, że dane blokowych obiektów blob są kopiowane do udziału dla blokowych obiektów blob. Skopiuj wirtualne dyski twarde do stronicowego obiektu blob. Jeśli format danych nie pasuje do odpowiedniego typu udziału, na późniejszym etapie przekazywanie danych na platformę Azure zakończy się niepowodzeniem.
-  Podczas kopiowania danych upewnij się, że rozmiar danych jest zgodny z ograniczeniami rozmiaru opisanymi w temacie [Limity usługi Azure Storage i urządzenia Data Box](data-box-limits.md).
- Jeśli dane przekazywane przy użyciu urządzenia Data Box będą jednocześnie przekazywane przez inne aplikacje, poza urządzeniem Data Box, skutkiem może być niepowodzenie zadania przekazywania oraz uszkodzenie danych.
- Zalecenia:
    - Nie używaj protokołów SMB i NFS w tym samym czasie.
    - Kopiuj te same dane do tego samego miejsca docelowego na platformie Azure. 
     
  W takich przypadkach nie można określić ostatecznego wyniku.
- Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.

Po nawiązaniu połączenia z udziałem SMB rozpocznij kopiowanie danych. Do kopiowania danych możesz użyć dowolnego narzędzia kopiowania plików zgodnego z protokołem SMB, na przykład narzędzia Robocopy. Za pomocą narzędzia Robocopy można zainicjować wiele zadań kopiowania. Użyj następującego polecenia:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Atrybuty opisano w poniższej tabeli.
    
|Atrybut  |Opis  |
|---------|---------|
|/e     |Kopiuje podkatalogi, włącznie z pustymi katalogami.         |
|/r:     |Określa liczbę ponownych prób w przypadku niepowodzenia kopiowania.         |
|/w:     |Określa czas oczekiwania pomiędzy ponownymi próbami w sekundach.         |
|/is     |Zawiera te same pliki.         |
|/nfl     |Określa, że nazwy plików nie są rejestrowane.         |
|/ndl    |Określa, że nazwy katalogów nie są rejestrowane.        |
|/np     |Określa, że postęp operacji kopiowania (liczba skopiowanych do tej pory plików lub katalogów) nie będzie wyświetlany. Wyświetlanie postępu znacznie obniża wydajność.         |
|/MT     | Użyj wielowątkowości (zaleca się użycie 32 lub 64 wątków). Ta opcja nie jest używana z plikami zaszyfrowanymi. Może być konieczne oddzielne plików zaszyfrowanych od niezaszyfrowanych. Jednak kopiowanie jednowątkowe znacznie obniża wydajność.           |
|/fft     | Użyj, aby zmniejszyć stopień szczegółowości sygnatury czasu dla każdego systemu plików.        |
|/b     | Kopiuje pliki w trybie tworzenia kopii zapasowej.        |
|/z    | Kopiuje pliki w trybie ponownego uruchomienia. Użyj tego atrybutu w przypadku niestabilnego środowiska. Ta opcja powoduje zmniejszenie przepływności ze względu na dodatkowe rejestrowanie.      |
| /zb     | Używa trybu ponownego uruchomienia. W przypadku odmowy dostępu ta opcja używa trybu tworzenia kopii zapasowej. Ta opcja powoduje zmniejszenie przepływności ze względu na tworzenie punktów kontrolnych.         |
|/efsraw     | Kopiuje wszystkie zaszyfrowane pliki w surowym trybie EFS. Używaj tej opcji tylko względem zaszyfrowanych plików.         |
|Dziennik +:\<pliku dziennika >| Dołącza dane wyjściowe do istniejącego pliku dziennika.|    
 
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
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    2 sesje narzędzia Robocopy <br> 16 wątków na sesję    |    3 sesje narzędzia Robocopy <br> 16 wątków na sesję    |    2 sesje narzędzia Robocopy <br> 24 wątki na sesję    |


Aby uzyskać więcej informacji na temat polecenia Robocopy, przejdź do artykułu [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Polecenie Robocopy i kilka przykładów).

Otwórz folder docelowy, aby wyświetlić i zweryfikować skopiowane pliki. Jeśli podczas procesu kopiowania wystąpiły jakiekolwiek błędy, pobierz pliki z błędami, które pomogą w rozwiązywaniu problemów. Aby uzyskać więcej informacji, zobacz [wyświetlić dzienniki błędów podczas kopiowania danych do urządzenia Data Box](data-box-logs.md#view-error-log-during-data-copy). Aby uzyskać szczegółową listę błędów podczas kopiowania danych, zobacz [wystawia Rozwiązywanie problemów z urządzenia Data Box](data-box-troubleshoot.md).

W celu zapewnienia integralności danych podczas kopiowania obliczana jest suma kontrolna. Po zakończeniu kopiowania sprawdź ilość używanego i wolnego miejsca na urządzeniu.
    
   ![Sprawdzanie wolnego i używanego miejsca na pulpicie nawigacyjnym](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)



## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem Data Box
> * Kopiowanie danych na urządzenie Data Box


Przejdź do następnego samouczka, aby dowiedzieć się, jak odesłać urządzenie Data Box do firmy Microsoft.

> [!div class="nextstepaction"]
> [Wysyłka urządzenia Azure Data Box do firmy Microsoft](./data-box-deploy-picked-up.md)

