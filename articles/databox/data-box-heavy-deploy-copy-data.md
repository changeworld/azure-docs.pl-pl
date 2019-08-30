---
title: Samouczek do kopiowania danych za pośrednictwem protokołu SMB na Azure Data Box Heavy | Microsoft Docs
description: Dowiedz się, jak kopiować dane do Azure Data Box Heavy za pośrednictwem protokołu SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: 8cb763766ebb151ad1c59b63a33a63493a4f0069
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164372"
---
::: zone target = "docs"

# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Samouczek: Kopiuj dane do Azure Data Box Heavy za pośrednictwem protokołu SMB

::: zone-end

::: zone target = "chromeless"

## <a name="copy-data-to-azure-data-box-heavy"></a>Kopiuj dane do Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

W tym samouczku opisano sposób nawiązywania połączenia i kopiowania danych z komputera-hosta za pomocą lokalnego internetowego interfejsu użytkownika.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Połącz z Data Box Heavy
> * Kopiuj dane do Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Dane z serwera źródłowego można kopiować do urządzenie Data Box za pośrednictwem protokołu SMB, systemu plików NFS, REST, usługi kopiowania danych lub do dysków zarządzanych.

W każdym przypadku upewnij się, że nazwy udziału i folderu oraz rozmiar danych są zgodne z wskazówkami opisanymi w [limitach usługi Azure Storage i Data Box Heavy](data-box-heavy-limits.md).

::: zone-end

::: zone target = "docs"

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: Skonfiguruj Azure Data Box Heavy](data-box-deploy-set-up.md).
2. Otrzymano Data Box Heavy i stan zamówienia w portalu jest **dostarczany**.
3. Dysponujesz komputerem hosta zawierającym dane, które chcesz skopiować do Data Box Heavy. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-system-requirements.md).
    - Połączenie z siecią o dużej szybkości. W przypadku najszybszych szybkości kopiowania połączenia 2 40-GbE (jeden na węzeł) mogą być używane równolegle. Jeśli nie masz dostępnego połączenia 40-GbE, zalecamy korzystanie z co najmniej 2 10-GbE połączeń (jeden na węzeł).
   

## <a name="connect-to-data-box-heavy-shares"></a>Połącz z udziałami Data Box Heavy

Na podstawie wybranego konta magazynu Data Box Heavy tworzy do:
- Maksymalnie trzy udziały dla każdego skojarzonego konta magazynu (GPv1 i GPv2).
- Jeden udział w usłudze Premium Storage.
- Jeden udział dla konta usługi BLOB Storage.

Te udziały są tworzone na obu węzłach urządzenia.

W obszarze blokowy obiekt BLOB i udziały stronicowych obiektów blob:
- Jednostki pierwszego poziomu są kontenerami.
- Jednostki drugiego poziomu to obiekty blob.

W obszarze udziały dla Azure Files:
- Jednostki pierwszego poziomu są udostępniane.
- Jednostki drugiego poziomu są plikami.

W poniższej tabeli przedstawiono ścieżkę UNC do udziałów na Data Box Heavy i adres URL ścieżki usługi Azure Storage, w przypadku których dane są przekazywane. Ostateczny adres URL w usłudze Azure Storage można uzyskać ze ścieżki udziału UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Blokowe obiekty blob platformy Azure | <li>Ścieżka UNC do udziałów: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Stronicowe obiekty blob platformy Azure  | <li>Ścieżka UNC do udziałów: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Ścieżka UNC do udziałów: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Kroki umożliwiające nawiązywanie połączenia przy użyciu klienta systemu Windows lub Linux są inne.

> [!NOTE]
> Wykonaj te same czynności, aby jednocześnie nawiązać połączenie z obydwoma węzłami.

### <a name="connect-on-a-windows-system"></a>Łączenie w systemie Windows

W przypadku korzystania z komputera hosta z systemem Windows Server wykonaj następujące kroki, aby nawiązać połączenie z Data Box Heavy.

1. Pierwszym krokiem jest uwierzytelnienie i uruchomienie sesji. Przejdź do pozycji **Połącz i skopiuj**. Kliknij przycisk **Pobierz poświadczenia**, aby pobrać poświadczenia dostępu do udziałów skojarzonych z kontem magazynu.

    ![Pobieranie poświadczeń udziału 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. W oknie dialogowym Uzyskiwanie dostępu do udziału i kopiowanie danych skopiuj wartości pól **Nazwa użytkownika** i **Hasło** odpowiedniego udziału. Kliknij przycisk **OK**.
    
    ![Pobieranie poświadczeń udziału 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Aby uzyskać dostęp do udziałów skojarzonych z kontem magazynu (*databoxe2etest* w poniższym przykładzie) z komputera hosta, Otwórz okno wiersza polecenia. W wierszu polecenia wpisz polecenie:

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
    
### <a name="connect-on-a-linux-system"></a>Nawiązywanie połączenia z systemem Linux

W przypadku korzystania z klienta systemu Linux użyj następującego polecenia, aby zainstalować udział SMB.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

`vers` Parametr jest wersją protokołu SMB obsługiwaną przez hosta z systemem Linux. Podłącz odpowiednią wersję w powyższym poleceniu.

Wersje protokołu SMB obsługiwane przez Data Box Heavy można znaleźć w temacie [obsługiwane systemy plików dla klientów z systemem Linux](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Kopiuj dane do Data Box Heavy

Po nawiązaniu połączenia z udziałami Data Box Heavy następnym krokiem jest skopiowanie danych.

### <a name="copy-considerations"></a>Zagadnienia dotyczące kopiowania

Przed rozpoczęciem kopiowania danych należy uwzględnić następujące kwestie:

- Upewnij się, że dane są kopiowane do udziałów odpowiadających właściwym formatom danych. To znaczy na przykład, że dane blokowych obiektów blob są kopiowane do udziału dla blokowych obiektów blob. Skopiuj wirtualne dyski twarde do stronicowego obiektu blob.

    Jeśli format danych nie pasuje do odpowiedniego typu udziału, na późniejszym etapie przekazywanie danych na platformę Azure zakończy się niepowodzeniem.
-  Podczas kopiowania danych upewnij się, że rozmiar danych jest zgodny z limitami rozmiaru opisanymi w limitach [usługi Azure Storage i Data Box Heavy](data-box-heavy-limits.md).
- Jeśli dane, które są przekazywane przez Data Box Heavy, są przesyłane współbieżnie przez inne aplikacje poza Data Box Heavy, co może skutkować błędami zadań przekazywania i uszkodzeniem danych.
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
    |log +:\<plik_dziennika >| Dołącza dane wyjściowe do istniejącego pliku dziennika.|
    
 
    Poniższy przykład przedstawia dane wyjściowe polecenia Robocopy, aby skopiować pliki do Data Box Heavy.

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

2. Aby zoptymalizować wydajność, użyj poniższych parametrów polecenia robocopy podczas kopiowania danych. (Poniższe numery reprezentują scenariusze najlepszego przypadku).

    | Platforma    | Przeważnie małe pliki < 512 KB    | Większość średnich plików 512 KB-1 MB  | Przeważnie duże pliki > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 sesji Robocopy <br> 24 wątki na sesję | 6 sesji Robocopy <br> 16 wątków na sesję | 6 sesji Robocopy <br> 16 wątków na sesję |


    Aby uzyskać więcej informacji na temat polecenia Robocopy, przejdź do artykułu [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Polecenie Robocopy i kilka przykładów).

3. Otwórz folder docelowy, aby wyświetlić i zweryfikować skopiowane pliki.

    ![Wyświetl skopiowane pliki](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. W miarę kopiowania danych:

    - Nazwy, rozmiary i format plików są weryfikowane w celu upewnienia się, że spełniają one limity dotyczące obiektów i magazynów platformy Azure, a także Konwencje nazewnictwa plików i kontenerów platformy Azure.
    - Aby zapewnić integralność danych, suma kontrolna jest również obliczana jako wbudowana.

    Jeśli podczas procesu kopiowania wystąpiły jakiekolwiek błędy, pobierz pliki z błędami, które pomogą w rozwiązywaniu problemów. Wybierz ikonę strzałki, aby pobrać pliki błędów.

    ![Pobierz pliki błędów](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Aby uzyskać więcej informacji, zobacz [Wyświetlanie dzienników błędów podczas kopiowania danych do Data Box Heavy](data-box-logs.md#view-error-log-during-data-copy). Aby uzyskać szczegółową listę błędów podczas kopiowania danych, zobacz [Rozwiązywanie problemów z Data Box Heavy](data-box-troubleshoot.md).

5. Otwórz plik błędu w Notatniku. Następujący plik błędu wskazuje, że dane nie są prawidłowo wyrównane.

    ![Otwórz plik błędu](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    W przypadku stronicowego obiektu BLOB dane muszą być wyrównane do 512 bajtów. Po usunięciu tych danych błąd zostanie rozwiązany, jak pokazano na poniższym zrzucie ekranu.

    ![Błąd rozwiązany](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. Po zakończeniu kopiowania przejdź do strony Wyświetl stronę **pulpitu nawigacyjnego** . Sprawdź zajęte miejsce i wolne miejsce na urządzeniu.
    
    ![Sprawdzanie wolnego i używanego miejsca na pulpicie nawigacyjnym](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Powtórz powyższe kroki, aby skopiować dane do drugiego węzła urządzenia.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono informacje dotyczące Azure Data Box Heavy tematów, takich jak:

> [!div class="checklist"]
> * Połącz z Data Box Heavy
> * Kopiuj dane do Data Box Heavy


Przejdź do następnego samouczka, aby dowiedzieć się, jak dostarczyć Data Box Heavy z powrotem do firmy Microsoft.

> [!div class="nextstepaction"]
> [Wyślij Azure Data Box Heavy do firmy Microsoft](./data-box-heavy-deploy-picked-up.md)

::: zone-end

::: zone target = "chromeless"

### <a name="copy-data-via-smb"></a>Kopiowanie danych za pośrednictwem protokołu SMB

1. Jeśli używasz hosta z systemem Windows, użyj następującego polecenia, aby nawiązać połączenie z udziałami SMB:

    `\\<IP address of your device>\ShareName`

2. Aby uzyskać poświadczenia dostępu do udziału, przejdź do strony **Connect & copy** (Połączenie i kopiowanie) w lokalnym internetowym interfejsie użytkownika urządzenia Data Box.

3. Użyj narzędzia do kopiowania plików zgodnego z protokołem SMB, takiego jak Robocopy, aby skopiować dane do udziałów.

Aby uzyskać instrukcje krok po kroku, przejdź do [samouczka: Skopiuj dane do Azure Data Box za pośrednictwem protokołu SMB](data-box-heavy-deploy-copy-data.md).

### <a name="copy-data-via-nfs"></a>Kopiowanie danych za pośrednictwem sieciowego systemu plików

1. W przypadku korzystania z hosta NFS Użyj następującego polecenia, aby zainstalować udziały NFS:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Aby uzyskać poświadczenia dostępu do udziału, przejdź do **strony połącz & Kopiuj** w lokalnym interfejsie użytkownika sieci Web Data Box Heavy.
3. Użyj `cp` polecenia `rsync` lub, aby skopiować dane. 
4. Powtórz te kroki, aby nawiązać połączenie i skopiować dane do drugiego węzła Data Box Heavy.

Aby uzyskać instrukcje krok po kroku, przejdź do [samouczka: Skopiuj dane do Azure Data Box za pośrednictwem systemu plików NFS](data-box-heavy-deploy-copy-data-via-nfs.md).

### <a name="copy-data-via-rest"></a>Kopiuj dane za pomocą REST

1. Aby skopiować dane przy użyciu urządzenie Data Box usługi BLOB Storage za pośrednictwem interfejsów API REST, można nawiązać połączenie za pośrednictwem *protokołu HTTP* lub *https*.
2. Aby skopiować dane do urządzenie Data Box magazynu obiektów blob, można użyć programu AzCopy.
3. Powtórz te kroki, aby nawiązać połączenie i skopiować dane do drugiego węzła Data Box Heavy.

Aby uzyskać instrukcje krok po kroku, przejdź do [samouczka: Skopiuj dane do Azure Data Box magazynu obiektów BLOB za pośrednictwem interfejsów API](data-box-heavy-deploy-copy-data-via-rest.md)Rest.

### <a name="copy-data-via-data-copy-service"></a>Kopiowanie danych za pośrednictwem usługi kopiowania danych

1. Aby skopiować dane przy użyciu usługi kopiowania danych, należy utworzyć zadanie. W lokalnym interfejsie użytkownika sieci Web Data Box Heavy przejdź do pozycji **zarządzaj > Kopiuj dane > Utwórz**.
2. Wypełnij parametry i Utwórz zadanie.
3. Powtórz te kroki, aby nawiązać połączenie i skopiować dane do drugiego węzła Data Box Heavy.

Aby uzyskać instrukcje krok po kroku, przejdź do [samouczka: Użyj usługi kopiowania danych do kopiowania danych do Azure Data Box Heavy](data-box-heavy-deploy-copy-data-via-copy-service.md).

### <a name="copy-data-to-managed-disks"></a>Kopiuj dane do dysków zarządzanych

1. Podczas zamawiania urządzenia Data Box Heavy należy wybrać opcję dyski zarządzane jako miejsce docelowe magazynu.
2. Możesz połączyć się z Data Box Heavy za pośrednictwem udziałów SMB lub NFS.
3. Następnie można skopiować dane za pomocą narzędzi SMB lub NFS.
4. Powtórz te kroki, aby nawiązać połączenie i skopiować dane do drugiego węzła Data Box Heavy.

Aby uzyskać instrukcje krok po kroku, przejdź do [samouczka: Użyj urządzenie Data Box, aby mocno zaimportować dane jako dyski zarządzane na](data-box-heavy-deploy-copy-data-from-vhds.md)platformie Azure.

::: zone-end


