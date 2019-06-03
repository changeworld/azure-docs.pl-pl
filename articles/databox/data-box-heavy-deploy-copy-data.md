---
title: Samouczka, aby skopiować dane za pośrednictwem protokołu SMB na Azure Data Box mocno | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane do usługi Azure Data pole duże za pośrednictwem protokołu SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: 6bc7af30d409fb7add321953aa2e956a0a3c1840
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427782"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb-preview"></a>Samouczek: Kopiowanie danych do usługi Azure Data pole mocno za pośrednictwem protokołu SMB (wersja zapoznawcza)

W tym samouczku opisano sposób nawiązywania połączenia i kopiowania danych z komputera-hosta za pomocą lokalnego internetowego interfejsu użytkownika.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawiązać połączenie z duże pole danych
> * Kopiowanie danych do duże pole danych


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: Konfigurowanie Azure Data Box mocno](data-box-deploy-set-up.md).
2. Otrzymał swoje duże pole danych, a stan zamówienia w portalu jest **dostarczone**.
3. Masz komputerze hosta, który zawiera dane, które mają do skopiowania do duże pole danych. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-system-requirements.md).
    - Połączenie z siecią o dużej szybkości. Najszybszy szybkości kopiowania dwa połączenia 40 GbE (po jednej na węzeł) możesz wykorzystać równolegle. Jeśli nie ma dostępnego połączenia 40 GbE, zaleca się, że masz co najmniej dwa połączenia 10 GbE (po jednej na węzeł).

## <a name="connect-to-data-box-heavy-shares"></a>Łączyć się z udziałami duże pole danych

Oparte na wybranym koncie magazynu, duże pole danych tworzy do:
- Maksymalnie trzy udziały dla każdego skojarzonego konta magazynu (GPv1 i GPv2).
- Jeden udział dla usługi premium storage.
- Jeden udział dla konta usługi blob storage.

Te akcje są tworzone w węzłach urządzenia.

W obszarze bloku obiektów blob i strony udziałów obiektów blob:
- Pierwszego poziomu jednostki są kontenery.
- Drugiego poziomu jednostki są obiektami blob.

W obszarze udziały plików platformy Azure:
- Pierwszego poziomu jednostki są udziały.
- Drugiego poziomu jednostki są pliki.

W poniższej tabeli przedstawiono ścieżkę UNC do udziału na adres URL ścieżki duże pole dane i usługi Azure Storage, gdzie dane są przekazywane. Ostateczny adres URL w usłudze Azure Storage można uzyskać ze ścieżki udziału UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Blokowe obiekty blob platformy Azure | <li>Ścieżka UNC do udziałów: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Stronicowe obiekty blob platformy Azure  | <li>Ścieżka UNC do udziałów: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Ścieżka UNC do udziałów: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Kroki, aby nawiązać połączenie przy użyciu klienta systemu Linux lub Windows różnią się.

> [!NOTE]
> Wykonaj te same czynności, połączyć się z węzłami urządzenia równolegle.

### <a name="connect-on-a-windows-system"></a>Łączenie w systemie Windows

Korzystania z komputera-hosta Windows Server, wykonaj następujące kroki, aby nawiązać połączenie z dużymi pola danych.

1. Pierwszym krokiem jest uwierzytelnienie i uruchomienie sesji. Przejdź do pozycji **Połącz i skopiuj**. Kliknij przycisk **Pobierz poświadczenia**, aby pobrać poświadczenia dostępu do udziałów skojarzonych z kontem magazynu.

    ![Pobieranie poświadczeń udziału 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. W oknie dialogowym Uzyskiwanie dostępu do udziału i kopiowanie danych skopiuj wartości pól **Nazwa użytkownika** i **Hasło** odpowiedniego udziału. Kliknij przycisk **OK**.
    
    ![Pobieranie poświadczeń udziału 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Dostępu do udziałów skojarzone z kontem usługi storage (*databoxe2etest* w poniższym przykładzie) z komputera hosta, Otwórz okno polecenia. W wierszu polecenia wpisz polecenie:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    W zależności od formatu danych ścieżki udziałów są następujące:
    - Blokowe obiekty blob platformy Azure — `\\10.100.10.100\databoxe2etest_BlockBlob`
    - Stronicowe obiekty blob platformy Azure — `\\10.100.10.100\databoxe2etest_PageBlob`
    - Pliki platformy Azure — `\\10.100.10.100\databoxe2etest_AzFile`
    
4. Po wyświetleniu monitu wprowadź hasło dla udziału. W poniższym przykładzie pokazano nawiązywanie połączenia z udziałem za pomocą poprzedniego polecenia.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Naciśnij klawisze Windows + R. W oknie **Uruchamianie** podaj `\\<device IP address>`. Kliknij przycisk **OK**, aby otworzyć Eksploratora plików.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Teraz udziały powinny być widoczne jako foldery.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu**. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.
    
### <a name="connect-on-a-linux-system"></a>Łączenie w systemie Linux

W przypadku korzystania z klienta systemu Linux użyj następującego polecenia, aby zainstalować udział SMB.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

`vers` Parametru jest wersja protokołu SMB, który obsługuje hosta z systemem Linux. Podłącz odpowiednią wersję w poleceniu powyżej.

Dla wersji protokołu SMB, który obsługuje duże pole danych, zobacz [obsługiwane systemy plików dla klientów systemu Linux](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Kopiowanie danych do duże pole danych

Po nawiązaniu połączenia z akcjami duże pole danych, następnym krokiem jest kopiowanie danych.

### <a name="copy-considerations"></a>Zagadnienia dotyczące kopiowania

Przed rozpoczęciem kopiowania danych należy uwzględnić następujące kwestie:

- Upewnij się, że dane są kopiowane do udziałów odpowiadających właściwym formatom danych. To znaczy na przykład, że dane blokowych obiektów blob są kopiowane do udziału dla blokowych obiektów blob. Skopiuj wirtualne dyski twarde do stronicowego obiektu blob.

    Jeśli format danych nie pasuje do odpowiedniego typu udziału, na późniejszym etapie przekazywanie danych na platformę Azure zakończy się niepowodzeniem.
-  Podczas kopiowania danych, upewnij się, że rozmiar danych jest zgodny ze limity rozmiaru opisanego w [usługi Azure storage i dane pole duże limity](data-box-heavy-limits.md).
- Jeśli dane, które jest przekazywany przez duże pole danych, jednocześnie jest przekazywany przez inne aplikacje poza duże pole danych, następnie może to spowodować przekazywania zadania awarii i uszkodzenia danych.
- Zalecenia:
    - Nie używaj protokołów SMB i NFS w tym samym czasie.
    - Kopiuj te same dane do tego samego miejsca docelowego na platformie Azure.
     
  W takich przypadkach nie można określić ostatecznego wyniku.
- Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.

Po nawiązaniu połączenia z udziałem SMB rozpocznij kopiowanie danych.

1. Do kopiowania danych możesz użyć dowolnego narzędzia kopiowania plików zgodnego z protokołem SMB, na przykład narzędzia Robocopy. Za pomocą narzędzia Robocopy można zainicjować wiele zadań kopiowania. Użyj następującego polecenia:
    
    ```
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
    ```
    Atrybuty opisano w poniższej tabeli.
    
    |Atrybut  |Opis  |
    |---------|---------|
    |/e      |Kopiuje podkatalogi, włącznie z pustymi katalogami.         |
    |/r:     |Określa liczbę ponownych prób w przypadku niepowodzenia kopiowania.         |
    |/w:     |Określa czas oczekiwania pomiędzy ponownymi próbami w sekundach.         |
    |/is     |Zawiera te same pliki.         |
    |/nfl    |Określa, że nazwy plików nie są rejestrowane.         |
    |/ndl    |Określa, że nazwy katalogów nie są rejestrowane.        |
    |/np     |Określa, że postęp operacji kopiowania (liczba skopiowanych do tej pory plików lub katalogów) nie będzie wyświetlany. Wyświetlanie postępu znacznie obniża wydajność.         |
    |/MT     | Użyj wielowątkowości (zaleca się użycie 32 lub 64 wątków). Ta opcja nie jest używana z plikami zaszyfrowanymi. Może być konieczne oddzielne plików zaszyfrowanych od niezaszyfrowanych. Jednak kopiowanie jednowątkowe znacznie obniża wydajność.           |
    |/fft    | Użyj, aby zmniejszyć stopień szczegółowości sygnatury czasu dla każdego systemu plików.        |
    |/b      | Kopiuje pliki w trybie tworzenia kopii zapasowej.        |
    |/z      | Kopiuje pliki w trybie ponownego uruchomienia. Użyj tego atrybutu w przypadku niestabilnego środowiska. Ta opcja powoduje zmniejszenie przepływności ze względu na dodatkowe rejestrowanie.      |
    | /zb    | Używa trybu ponownego uruchomienia. W przypadku odmowy dostępu ta opcja używa trybu tworzenia kopii zapasowej. Ta opcja powoduje zmniejszenie przepływności ze względu na tworzenie punktów kontrolnych.         |
    |/efsraw | Kopiuje wszystkie zaszyfrowane pliki w surowym trybie EFS. Używaj tej opcji tylko względem zaszyfrowanych plików.         |
    |Dziennik +:\<pliku dziennika >| Dołącza dane wyjściowe do istniejącego pliku dziennika.|
    
 
    Poniższy przykład pokazuje dane wyjściowe polecenia robocopy, aby skopiować pliki na duże pole danych.

    ```   
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.100.10.100\devicemanagertest1_AzFile\templates /MT:24
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
        Started : Thursday, April 4, 2019 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
        Dest : \\10.100.10.100\devicemanagertest1_AzFile\templates\
        Files : *.*
        Options : *.* /DCOPY:DA /COPY:DAT /MT:24 /R:5 /W:60
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
    ```       

2. Aby zoptymalizować wydajność, użyj poniższych parametrów polecenia robocopy podczas kopiowania danych. (Numery poniżej reprezentują najlepsze przypadków).

    | Platforma    | Przeważnie małe pliki < 512 KB    | Przede wszystkim średnie pliki 512 KB - 1 MB  | Przeważnie duże pliki > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 sesji Robocopy <br> 24 wątki na sesję | 6 sesji Robocopy <br> 16 wątków na sesję | 6 sesji Robocopy <br> 16 wątków na sesję |


    Aby uzyskać więcej informacji na temat polecenia Robocopy, przejdź do artykułu [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Polecenie Robocopy i kilka przykładów).

3. Otwórz folder docelowy, aby wyświetlić i zweryfikować skopiowane pliki.

    ![Wyświetl kopiowanych plików](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. Ponieważ dane są kopiowane:

    - Nazwy plików, rozmiary i format są weryfikowane, aby upewnić się, że spełniają te limity obiektu i magazynu platformy Azure, a także plików platformy Azure i kontenerów, konwencje nazewnictwa.
    - W celu zapewnienia integralności danych, suma kontrolna jest również wbudowane obliczanej.

    Jeśli podczas procesu kopiowania wystąpiły jakiekolwiek błędy, pobierz pliki z błędami, które pomogą w rozwiązywaniu problemów. Wybierz ikonę strzałki Aby pobrać plików błędów.

    ![Pobierz pliki błędów](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Aby uzyskać więcej informacji, zobacz [wyświetlić dzienniki błędów podczas kopiowania danych na duże pole danych](data-box-logs.md#view-error-log-during-data-copy-to-data-box). Aby uzyskać szczegółową listę błędów podczas kopiowania danych, zobacz [Rozwiązywanie problemów z dużymi pole danych wystawia](data-box-troubleshoot.md).

5. Otwórz plik błędu w Notatniku. Następujący plik błąd wskazuje, że dane nie są prawidłowo wyrównane.

    ![Błąd przy otwieraniu plików](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    Dla stronicowych obiektów blob dane muszą być 512 bajtów wyrównane. Po usunięciu tych danych błąd rozwiązuje, jak pokazano na poniższym zrzucie ekranu.

    ![Błąd rozwiązane](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. Po zakończeniu kopiowania należy przejść do **Wyświetl pulpit nawigacyjny** strony. Sprawdź używane miejsce i wolnego miejsca na urządzeniu.
    
    ![Sprawdzanie wolnego i używanego miejsca na pulpicie nawigacyjnym](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Powtórz powyższe kroki na skopiowanie danych do drugiego węzła urządzenia.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku omówiono Azure Data Box mocno tematy takie jak:

> [!div class="checklist"]
> * Nawiązać połączenie z duże pole danych
> * Kopiowanie danych do duże pole danych


Przejdź do następnego samouczka, aby dowiedzieć się, jak i dostarczaj swoje dane pole mocno do firmy Microsoft.

> [!div class="nextstepaction"]
> [Dostarczaj swoje duże pole danych platformy Azure do firmy Microsoft](./data-box-heavy-deploy-picked-up.md)

