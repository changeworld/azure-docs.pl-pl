---
title: Samouczek, aby skopiować dane do usługi Azure Data pole mocno za pośrednictwem systemu plików NFS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane do usługi Azure dane pole mocno za pośrednictwem systemu plików NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 4361cee3d07408c3abb5031d2ab18c15c92c5e0a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595749"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-nfs"></a>Samouczek: Kopiowanie danych do usługi Azure Data pole mocno za pośrednictwem systemu plików NFS

W tym samouczku opisano, jak nawiązać połączenie i kopiowanie danych z komputera hosta, za pomocą lokalnego internetowego interfejsu użytkownika, aby Twoje duże pole danych platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wymagania wstępne
> * Nawiązać połączenie z duże pole danych
> * Kopiowanie danych do duże pole danych

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: Konfigurowanie Azure Data Box mocno](data-box-heavy-deploy-set-up.md).
2. Odebrano Twoje duże pole danych, a stan zamówienia w portalu jest **dostarczone**.
3. Masz komputerze hosta, który zawiera dane, które mają do skopiowania do duże pole danych. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-heavy-system-requirements.md).
    - Połączenie z siecią o dużej szybkości. Najszybszy szybkości kopiowania dwa połączenia 40 GbE (po jednej na węzeł) możesz wykorzystać równolegle. Jeśli nie ma dostępnego połączenia 40 GbE, zaleca się, że masz co najmniej dwa połączenia 10 GbE (po jednej na węzeł). 

## <a name="connect-to-data-box-heavy"></a>Nawiązać połączenie z duże pole danych

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
| Blokowe obiekty blob platformy Azure | <li>Ścieżka UNC do udziałów: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Stronicowe obiekty blob platformy Azure  | <li>Ścieżka UNC do udziałów: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Ścieżka UNC do udziałów: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Jeśli używasz komputera hosta z systemem Linux wykonaj poniższe kroki, aby skonfigurować urządzenie, aby umożliwić dostęp do klientów systemu plików NFS.

1. Podaj adresy IP dozwolonych klientów, którzy mogą uzyskiwać dostęp do udziału. W lokalnym internetowym interfejsie użytkownika przejdź do strony **Połącz i skopiuj**. W obszarze **Ustawienia sieciowego systemu plików** kliknij przycisk **Dostęp klienta do sieciowego systemu plików**. 

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Podaj adres IP klienta sieciowego systemu plików i kliknij przycisk **Dodaj**. Powtarzając ten krok, możesz skonfigurować dostęp dla wielu klientów sieciowego systemu plików. Kliknij przycisk **OK**.

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Upewnij się, że na komputerze-hoście z systemem Linux zainstalowano [obsługiwaną wersję](data-box-system-requirements.md) klienta sieciowego systemu plików. Użyj konkretnej wersji dla określonej dystrybucji systemu Linux. 

3. Po zainstalowaniu klienta sieciowego systemu plików użyj następującego polecenia, aby zainstalować udział sieciowego systemu plików na Twoim urządzeniu Data Box:

    `sudo mount <Data Box Heavy device IP>:/<NFS share on Data Box Heavy device> <Path to the folder on local Linux computer>`

    Poniższy przykład pokazuje, jak połączyć za pośrednictwem systemu plików NFS w udziale duże pole danych. Adres IP duże pole danych `10.161.23.130`, udział `Mystoracct_Blob` jest zainstalowany w ubuntuVM, zainstaluj punkt jest `/home/databoxheavyubuntuhost/databoxheavy`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`
    
    W przypadku klientów z komputerami Mac musisz w następujący sposób dodać dodatkową opcję: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`

    **Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu**. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.

## <a name="copy-data-to-data-box-heavy"></a>Kopiowanie danych do duże pole danych

Po nawiązaniu połączenia z akcjami duże pole danych następnym krokiem jest kopiowanie danych. Przed rozpoczęciem kopiowania danych należy uwzględnić następujące kwestie:

- Upewnij się, że dane są kopiowane do udziałów odpowiadających właściwym formatom danych. To znaczy na przykład, że dane blokowych obiektów blob są kopiowane do udziału dla blokowych obiektów blob. Skopiuj wirtualne dyski twarde do stronicowych obiektów blob. Jeśli format danych nie pasuje do odpowiedniego typu udziału, na późniejszym etapie przekazywanie danych na platformę Azure zakończy się niepowodzeniem.
-  Podczas kopiowania danych, upewnij się, że rozmiar danych jest zgodny ze limity rozmiaru opisanego w [usługi Azure storage i dane pole duże limity](data-box-heavy-limits.md). 
- Jeśli dane, które jest przekazywany przez duże pole danych, jednocześnie jest przekazywany przez inne aplikacje poza duże pole danych, następnie może to spowodować przekazywania zadania awarii i uszkodzenia danych.
- Nie zaleca się jednoczesnego używania protokołu SMB i sieciowego systemu plików ani kopiowania tych samych danych do tego samego końcowego miejsca docelowego na platformie Azure. W takich przypadkach nie można określić ostatecznego wyniku.
- **Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu**. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.
- Jeśli wprowadzane uwzględniana wielkość liter nazwy katalogów i plików z udziału NFS do systemu plików NFS na duże pole danych: 
    - Wymagane są zachowywane w nazwie.
    - Pliki jest rozróżniana wielkość liter.
    
    Na przykład, jeśli kopiowanie `SampleFile.txt` i `Samplefile.Txt`, przypadku zostaną zachowane w nazwie podczas kopiowania do urządzenia, ale drugiego pliku spowoduje zastąpienie pierwszy z nich, ponieważ są one traktowane jako ten sam plik.


Jeśli korzystasz z komputera-hosta z systemem Linux, użyj narzędzia do kopiowania podobnego do narzędzia Robocopy. W systemie Linux są dostępne na przykład narzędzia [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) lub [Ultracopier](https://ultracopier.first-world.info/).  

Polecenie `cp` jest jedną z najlepszych opcji do kopiowania katalogów. Aby uzyskać więcej informacji dotyczących użycia, przejdź do [stron man narzędzia cp](http://man7.org/linux/man-pages/man1/cp.1.html).

W przypadku korzystania z opcji rsync na potrzeby kopiowania wielowątkowego należy przestrzegać następujących wytycznych:

 - Zainstaluj pakiet **CIFS Utils** lub **NFS Utils** w zależności od systemu plików używanego przez Twojego klienta systemu Linux.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  Zainstaluj narzędzia **Rsync** i **Parallel** (polecenia różnią się w zależności od wersji dystrybucji systemu Linux).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - Utwórz punkt instalacji.

    `sudo mkdir /mnt/databoxheavy`

 - Zainstaluj wolumin.

    `sudo mount -t NFS4  //Databox-heavy-IP-Address/share_name /mnt/databoxheavy` 

 - Zdubluj strukturę katalogów folderów.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databoxheavy`

 - Skopiuj pliki. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databoxheavy/{}`

     gdzie j określa liczbę przetwarzań równoległych, X = liczba równoległych kopii

     Na początku zaleca się użycie 16 równoległych kopii i zwiększanie liczby wątków w zależności od dostępności zasobów.

> [!IMPORTANT]
> Nie są obsługiwane następujące typy plików systemu Linux: łącza symbolicznego, pliki znak, blokowanie plików, gniazda i potoków. Następujące typy plików będą powodować błędy podczas **przygotowywanie do wysłania** kroku.

Otwórz folder docelowy, aby wyświetlić i zweryfikować skopiowane pliki. Jeśli podczas procesu kopiowania wystąpiły jakiekolwiek błędy, pobierz pliki z błędami, które pomogą w rozwiązywaniu problemów. Aby uzyskać więcej informacji, zobacz [wyświetlić dzienniki błędów podczas kopiowania danych na duże pole danych](data-box-logs.md#view-error-log-during-data-copy). Aby uzyskać szczegółową listę błędów podczas kopiowania danych, zobacz [Rozwiązywanie problemów z dużymi pole danych wystawia](data-box-troubleshoot.md).

W celu zapewnienia integralności danych podczas kopiowania obliczana jest suma kontrolna. Po zakończeniu kopiowania sprawdź ilość używanego i wolnego miejsca na urządzeniu.
    
   ![Sprawdzanie wolnego i używanego miejsca na pulpicie nawigacyjnym](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku omówiono Azure Data Box mocno tematy takie jak:

> [!div class="checklist"]
> * Wymagania wstępne
> * Nawiązać połączenie z duże pole danych
> * Kopiowanie danych do duże pole danych


Przejdź do następnego samouczka, aby dowiedzieć się, jak odesłać urządzenie Data Box do firmy Microsoft.

> [!div class="nextstepaction"]
> [Dostarczaj swoje duże pole danych platformy Azure do firmy Microsoft](./data-box-heavy-deploy-picked-up.md)

