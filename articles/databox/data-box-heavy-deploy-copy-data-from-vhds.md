---
title: 'Samouczek: Kopiowanie z dysków VHD na dyski zarządzane'
titleSuffix: Azure Data Box Heavy
description: Dowiedz się, jak kopiować dane z wirtualnychderów z lokalnych obciążeń maszyn wirtualnych do środowiska Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 01031159d1894c7cb5f36b48f268186dff21fd22
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77471333"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Samouczek: Importowanie danych jako dysków zarządzanych na platformie Azure za pomocą narzędzia Data Box Heavy

W tym samouczku opisano, jak używać narzędzia Azure Data Box Heavy do migracji lokalnych dysków wirtualnych do dysków zarządzanych na platformie Azure. Dyski VHD z lokalnych maszyn wirtualnych są kopiowane do środowiska Data Box Heavy jako obiekty blob stron i są przekazywane na platformę Azure jako dyski zarządzane. Te dyski zarządzane można następnie dołączyć do maszyn wirtualnych platformy Azure.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przegląd wymagań wstępnych
> * Nawiązywanie połączenia z urządzeniem Data Box Heavy
> * Kopiowanie danych na urządzenie Data Box Heavy


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. [Ukończono samouczek: Konfigurowanie narzędzia Azure Data Box Heavy](data-box-heavy-deploy-set-up.md).
2. Urządzenie Data Box Heavy zostało do Ciebie dostarczone, a stan zamówienia w portalu to **Dostarczono**.
3. Dysponujesz szybkim połączeniem sieciowym. Aby uzyskać największe szybkości kopiowania, można użyć dwóch równoległych połączeń 40-GbE (po jednym na węzeł). Jeśli nie masz dostępnego połączenia 40-GbE, zalecamy skorzystanie z co najmniej dwóch połączeń 10-GbE (po jednym na węzeł). 
4. Masz przegląd:

    - Obsługiwane [rozmiary dysków zarządzanych w limitach rozmiaru obiektu platformy Azure](data-box-heavy-limits.md#azure-object-size-limits).
    - [Wprowadzenie do dysków zarządzanych platformy Azure](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box-heavy"></a>Nawiązywanie połączenia z urządzeniem Data Box Heavy

Na podstawie określonych grup zasobów data box gruba tworzy jeden udział dla każdej skojarzonej grupy zasobów na węzeł. Na przykład, `mydbmdrg1` `mydbmdrg2` jeśli i zostały utworzone podczas składania zamówienia, tworzone są następujące udziały:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

W ramach każdego udziału tworzone są następujące trzy foldery, które odpowiadają kontenerom na koncie magazynu.

- Dysk SSD w warstwie Premium
- Dysk HDD w warstwie Standardowa
- Dysk SSD w warstwie Standardowa

W poniższej tabeli przedstawiono ścieżki UNC do udziałów w polu danych Grubych.
 
|        Protokół połączenia           |             Unc droga do udziału                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

W zależności od tego, czy do łączenia się z udziałami data box heavy używasz SMB lub NFS, kroki połączenia są różne.

> [!NOTE]
> - Połączenie za pośrednictwem REST nie jest obsługiwane dla tej funkcji.
> - Powtórz instrukcje połączenia, aby połączyć się z drugim węzłem Data Box Heavy.

### <a name="connect-to-data-box-heavy-via-smb"></a>Łączenie się z data box heavy przez SMB

Jeśli używasz komputera-hosta z systemem Windows Server, wykonaj następujące kroki, aby nawiązać połączenie z urządzeniem Data Box Heavy.

1. Pierwszym krokiem jest uwierzytelnienie i uruchomienie sesji. Przejdź do pozycji **Połącz i skopiuj**. Kliknij **przycisk Pobierz poświadczenia,** aby uzyskać poświadczenia dostępu dla udziałów skojarzonych z grupą zasobów. Poświadczenia dostępu można również uzyskać ze **szczegółów urządzenia** w witrynie Azure portal.

    > [!NOTE]
    > Poświadczenia dla wszystkich udziałów dla dysków zarządzanych są identyczne.

    ![Pobieranie poświadczeń udziału 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. W oknie dialogowym Udostępnianie i kopiowanie danych programu Access skopiuj **nazwę użytkownika** i **hasło** udziału. Kliknij przycisk **OK**.
    
    ![Pobieranie poświadczeń udziału 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Aby uzyskać dostęp do udziałów skojarzonych z zasobem *(mydbmdrg1* w poniższym przykładzie) z komputera-hosta, otwórz okno polecenia. W wierszu polecenia wpisz polecenie:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Ścieżki udostępniania UNC w tym przykładzie są następujące:

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

    W ramach każdego udziału powinny być teraz widoczne następujące wstępnie stwórzone foldery.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Łączenie się z data box heavy przez NFS

Jeśli używasz komputera-hosta systemu Linux, wykonaj następujące czynności, aby skonfigurować urządzenie tak, aby zezwolić na dostęp do klientów systemu plików NFS.

1. Podaj adresy IP dozwolonych klientów, którzy mogą uzyskiwać dostęp do udziału. W lokalnym internetowym interfejsie użytkownika przejdź do strony **Połącz i skopiuj**. W obszarze **Ustawienia sieciowego systemu plików** kliknij przycisk **Dostęp klienta do sieciowego systemu plików**.

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Podaj adres IP klienta sieciowego systemu plików i kliknij przycisk **Dodaj**. Powtarzając ten krok, możesz skonfigurować dostęp dla wielu klientów sieciowego systemu plików. Kliknij przycisk **OK**.

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Upewnij się, że na komputerze-hoście z systemem Linux zainstalowano [obsługiwaną wersję](data-box-system-requirements.md) klienta sieciowego systemu plików. Użyj konkretnej wersji dla określonej dystrybucji systemu Linux.

3. Po zainstalowaniu klienta systemu plików NFS użyj następującego polecenia, aby zainstalować udział NFS na urządzeniu:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    W poniższym przykładzie pokazano, jak połączyć się za pośrednictwem systemu plików NFS z udziałem Data Box lub Data Box Heavy. Data Box lub Data Box `169.254.250.200`Heavy ip `mydbmdrg1_MDisk` urządzenia jest , udział jest zamontowany `/home/databoxubuntuhost/databox`na ubuntuVM, punkt montażu jest .

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Kopiowanie danych na urządzenie Data Box Heavy

Po nawiązaniu połączenia z serwerem danych następnym krokiem jest skopiowanie danych. Plik VHD jest kopiowany do przemieszczania konta magazynu jako obiekt blob strony. Obiekt blob strony jest następnie konwertowany na dysk zarządzany i przenoszony do grupy zasobów.

Przed rozpoczęciem kopiowania danych zapoznaj się z następującymi zagadnieniami:

- Zawsze kopiuj wirtualne dyski twarde do jednego ze wstępnie utworzonych folderów. Jeśli skopiujesz dyski VHD poza tymi folderami lub w folderze, który został utworzony, dyski VHD zostaną przekazane do konta usługi Azure Storage jako obiekty blob stron, a nie dyski zarządzane.
- Na potrzeby tworzenia dysków zarządzanych można przekazywać tylko stałe wirtualne dyski twarde. Pliki VHDX lub dynamiczne i różnicujące dyski VHD nie są obsługiwane.
- We wszystkich wstępnie skreasowanych folderach można mieć tylko jeden dysk zarządzany o danej nazwie. Oznacza to, że wirtualne dyski twarde przekazane do wstępnie utworzonych folderów powinny mieć unikatowe nazwy. Upewnij się, że dana nazwa nie jest zgodna z nazwą już istniejącego dysku zarządzanego w grupie zasobów.
- Przejrzyj limity dysku zarządzanego w [limitach rozmiaru obiektu platformy Azure](data-box-heavy-limits.md#azure-object-size-limits).

W zależności od tego, czy łączysz się za pośrednictwem SMB lub NFS, możesz użyć:

- [Kopiowanie danych za pośrednictwem protokołu SMB](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Kopiowanie danych za pośrednictwem sieciowego systemu plików](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

Poczekaj na zakończenie zadań kopiowania. Upewnij się, że zadania kopiowania zostały zakończone bez błędów przed przejść do następnego kroku.

![Brak błędów na stronie **Połącz i skopiuj**](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Jeśli podczas procesu kopiowania występują błędy, pobierz dzienniki ze strony **Połącz i skopuj.**

- Jeśli skopiowano plik, który nie jest wyrównany do 512 bajtów, plik nie zostanie przekazany jako obiekt blob strony do tymczasowego konta magazynu. Zostanie wyświetlony błąd w dziennikach. Usuń plik i skopiuj plik, który jest wyrównany do 512 bajtów.

- Jeśli skopiowano VHDX (te pliki nie są obsługiwane) z długą nazwą, zobaczysz błąd w dziennikach.

    ![Błąd w dziennikach ze strony **Połącz i skopiuj**](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Rozwiąż błędy przed przejściem do następnego kroku.

W celu zapewnienia integralności danych podczas kopiowania obliczana jest suma kontrolna. Po zakończeniu kopiowania sprawdź ilość używanego i wolnego miejsca na urządzeniu.
    
![Sprawdzanie wolnego i używanego miejsca na pulpicie nawigacyjnym](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Po zakończeniu zadania kopiowania możesz przejść do sekcji **Przygotowanie do wysłania**.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące urządzenia Azure Data Box Heavy, takie jak:

> [!div class="checklist"]
> * Przegląd wymagań wstępnych
> * Nawiązywanie połączenia z urządzeniem Data Box Heavy
> * Kopiowanie danych na urządzenie Data Box Heavy


Przejdź do następnego samouczka, aby dowiedzieć się, jak odesłać urządzenie Data Box Heavy do firmy Microsoft.

> [!div class="nextstepaction"]
> [Wysyłka urządzenia Azure Data Box Heavy do firmy Microsoft](./data-box-heavy-deploy-picked-up.md)

