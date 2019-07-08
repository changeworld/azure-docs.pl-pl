---
title: Samouczek, aby skopiować dane z wirtualnych dysków twardych do usługi managed disks przy użyciu Azure Data Box mocno | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z wirtualnych dysków twardych z lokalnych obciążeń maszyny Wirtualnej do Twojej duże pole danych platformy Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: Heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 8065d29c0cb984244178d49fe8c8c5aa853ee682
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595763"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Samouczek: Użyj danych pole mocno do zaimportowania danych jako usługa managed disks na platformie Azure

W tym samouczku opisano sposób użycia duże pole danych platformy Azure do migracji w środowisku lokalnym wirtualnych dysków twardych do usługi managed disks na platformie Azure. Wirtualne dyski twarde z maszyn wirtualnych w środowisku lokalnym są kopiowane do duże pole danych jako stronicowe obiekty BLOB i są przekazywane na platformę Azure jako dyski zarządzane. Te zarządzanych dysków może następnie być dołączone do maszyn wirtualnych platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przegląd wymagań wstępnych
> * Nawiązać połączenie z duże pole danych
> * Kopiowanie danych do duże pole danych


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: Konfigurowanie Azure Data Box mocno](data-box-heavy-deploy-set-up.md).
2. Otrzymał swoje duże pole danych, a stan zamówienia w portalu jest **dostarczone**.
3. Dysponujesz szybkim połączeniem sieciowym. Najszybszy szybkości kopiowania dwa połączenia 40 GbE (po jednej na węzeł) możesz wykorzystać równolegle. Jeśli nie ma dostępnego połączenia 40 GbE, zaleca się, że masz co najmniej dwa połączenia 10 GbE (po jednej na węzeł). 
4. Po przejrzeniu:

    - Obsługiwane [zarządzane rozmiary dysków w limity rozmiaru obiektów platformy Azure](data-box-heavy-limits.md#azure-object-size-limits).
    - [Wprowadzenie do usługi Azure managed disks](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box-heavy"></a>Nawiązać połączenie z duże pole danych

Na podstawie zasobów grup określone, duże pole danych tworzy jeden udział dla każdej grupy skojarzony zasób w każdym węźle. Na przykład jeśli `mydbmdrg1` i `mydbmdrg2` zostały utworzone podczas wprowadzania do zamówienia, są tworzone następujące akcje:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

W ramach każdego udziału następujące trzy foldery są tworzone, które odnoszą się do kontenerów na koncie magazynu.

- Premium, SSD
- Standardowa, dysk twardy
- Standardowa, SSD

W poniższej tabeli przedstawiono ścieżki UNC do udziałów na Twoje duże pole danych.
 
|        Protokół połączenia           |             Ścieżka UNC do udziału                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Czynności, aby połączyć oparte na tego, czy używasz protokołu SMB lub NFS można łączyć się z udziałami duże pole danych, są różne.

> [!NOTE]
> - Łączenie za pośrednictwem interfejsu REST nie jest obsługiwane dla tej funkcji.
> - Powtórz instrukcje connect połączyć się z drugiego węzła z dużymi pola danych.

### <a name="connect-to-data-box-heavy-via-smb"></a>Nawiązać połączenie z duże pole danych za pośrednictwem protokołu SMB

Korzystania z komputera-hosta Windows Server, wykonaj następujące kroki, aby nawiązać połączenie z dużymi pola danych.

1. Pierwszym krokiem jest uwierzytelnienie i uruchomienie sesji. Przejdź do pozycji **Połącz i skopiuj**. Kliknij przycisk **Uzyskiwanie poświadczeń** można uzyskać poświadczenia dostępu do udziałów skojarzone z grupy zasobów. Można także uzyskać poświadczenia dostępu z **szczegóły urządzenia** w witrynie Azure portal.

    > [!NOTE]
    > Poświadczenia dla wszystkich udziałów za dyski zarządzane są identyczne.

    ![Pobieranie poświadczeń udziału 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Z dostępu do udziału i kopiowanie danych, okno dialogowe, kopia **Username** i **hasło** udziału. Kliknij przycisk **OK**.
    
    ![Pobieranie poświadczeń udziału 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Dostępu do udziałów, skojarzony zasób (*mydbmdrg1* w poniższym przykładzie) z komputera hosta, Otwórz okno polecenia. W wierszu polecenia wpisz polecenie:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Twoje ścieżki udziału UNC, w tym przykładzie są następujące:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Po wyświetleniu monitu wprowadź hasło dla udziału. W poniższym przykładzie pokazano nawiązywanie połączenia z udziałem za pomocą poprzedniego polecenia.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Naciśnij klawisze Windows + R. W oknie **Uruchamianie** podaj `\\<device IP address>\<ShareName>`. Kliknij przycisk **OK**, aby otworzyć Eksploratora plików.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Powinien zostać wyświetlony następujący precreated folderach w ramach każdego udziału.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Nawiązać połączenie z duże pole danych za pośrednictwem systemu plików NFS

Jeśli używasz komputera hosta z systemem Linux wykonaj poniższe kroki, aby skonfigurować urządzenie, aby umożliwić dostęp do klientów systemu plików NFS.

1. Podaj adresy IP dozwolonych klientów, którzy mogą uzyskiwać dostęp do udziału. W lokalnym internetowym interfejsie użytkownika przejdź do strony **Połącz i skopiuj**. W obszarze **Ustawienia sieciowego systemu plików** kliknij przycisk **Dostęp klienta do sieciowego systemu plików**.

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Podaj adres IP klienta sieciowego systemu plików i kliknij przycisk **Dodaj**. Powtarzając ten krok, możesz skonfigurować dostęp dla wielu klientów sieciowego systemu plików. Kliknij przycisk **OK**.

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Upewnij się, że na komputerze-hoście z systemem Linux zainstalowano [obsługiwaną wersję](data-box-system-requirements.md) klienta sieciowego systemu plików. Użyj konkretnej wersji dla określonej dystrybucji systemu Linux.

3. Po zainstalowaniu klienta systemu plików NFS, użyj następującego polecenia, aby zainstalować udział systemu plików NFS na twoim urządzeniu:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    Poniższy przykład pokazuje, jak nawiązać połączenia przy użyciu systemu plików NFS udostępniania urządzenia Data Box lub duże pole danych. Urządzenie Data Box lub duże pole danych, adres IP `169.254.250.200`, udział `mydbmdrg1_MDisk` jest zainstalowany w ubuntuVM, zainstaluj punkt jest `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Kopiowanie danych do duże pole danych

Po połączeniu z serwerem danych, następnym krokiem jest kopiowanie danych. Plik wirtualnego dysku twardego jest kopiowany do konta magazynu przejściowego jako stronicowy obiekt blob. Stronicowy obiekt blob jest następnie konwertowana na dysk zarządzany i przeniesione do grupy zasobów.

Przed przystąpieniem do wykonywania kopii danych, należy przejrzeć następujące zagadnienia:

- Zawsze Kopiuj wirtualne dyski twarde do jednego z precreated folderów. W przypadku kopiowania wirtualnych dysków twardych poza te foldery lub w folderze, który został utworzony, wirtualne dyski twarde zostaną przekazane do konta usługi Azure Storage jako stronicowe obiekty BLOB i dyski zarządzane nie.
- Do utworzenia dysków zarządzanych można przekazać tylko stałych dysków VHD. Pliki VHDX lub dynamicznych i różnicowych wirtualnych dysków twardych nie są obsługiwane.
- Możesz może mieć tylko jeden dysk zarządzany o określonej nazwie w grupie zasobów we wszystkich folderach precreated. Oznacza to, że wirtualne dyski twarde przekazany do folderów precreated powinny mieć unikatowe nazwy. Upewnij się, że dana nazwa jest niezgodna już istniejącego dysku zarządzanego w grupie zasobów.
- Sprawdź limity dysku zarządzanego w [limity rozmiaru obiektów platformy Azure](data-box-heavy-limits.md#azure-object-size-limits).

W zależności od tego, czy jest nawiązywane za pośrednictwem protokołu SMB lub NFS można użyć:

- [Kopiowanie danych za pośrednictwem protokołu SMB](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Kopiowanie danych za pomocą systemu plików NFS](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

Poczekaj na zakończenie zadań kopiowania. Upewnij się, że zadania kopiowania została zakończona bez błędów, zanim przejdziesz do kolejnego kroku.

![Brak błędów na stronie **Połącz i skopiuj**](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Jeśli występują błędy podczas procesu kopiowania, Pobierz dzienniki z **Połącz i skopiuj** strony.

- Jeśli plik, który jest wyrównany nie 512 bajtów jest kopiowany, plik nie jest przekazywany jako stronicowy obiekt blob na koncie magazynu przejściowego. Zostanie wyświetlony błąd w dziennikach. Usuń plik, a następnie skopiuj plik, który wynosi 512 bajtów wyrównane.

- Jeśli plik VHDX (te pliki nie są obsługiwane) jest kopiowany z długiej nazwy, zobaczysz błąd w dziennikach.

    ![Błąd w dziennikach z ** strony nawiązywanie połączenia i kopiowanie **](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Napraw błędy, zanim przejdziesz do kolejnego kroku.

W celu zapewnienia integralności danych podczas kopiowania obliczana jest suma kontrolna. Po zakończeniu kopiowania sprawdź ilość używanego i wolnego miejsca na urządzeniu.
    
![Sprawdzanie wolnego i używanego miejsca na pulpicie nawigacyjnym](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Po zakończeniu zadania kopiowania możesz przejść do sekcji **Przygotowanie do wysłania**.


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku omówiono Azure Data Box mocno tematy takie jak:

> [!div class="checklist"]
> * Przegląd wymagań wstępnych
> * Nawiązać połączenie z duże pole danych
> * Kopiowanie danych do duże pole danych


Przejdź do następnego samouczka, aby dowiedzieć się, jak i dostarczaj swoje dane pole mocno do firmy Microsoft.

> [!div class="nextstepaction"]
> [Dostarczaj swoje duże pole danych platformy Azure do firmy Microsoft](./data-box-heavy-deploy-picked-up.md)

