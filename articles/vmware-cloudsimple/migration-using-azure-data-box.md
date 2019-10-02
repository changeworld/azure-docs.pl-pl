---
title: Rozwiązanie VMware firmy Azure przez CloudSimple — migracja przy użyciu Azure Data Box
description: Migracja danych zbiorczych do rozwiązania VMware platformy Azure przez CloudSimple przy użyciu Azure Data Box
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5f19b98fbbbad2f43227bfa2f73eab47bf7b1699
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817464"
---
# <a name="migrating-data-to-azure-vmware-solution-by-cloudsimple-using-azure-data-box"></a>Migrowanie danych do rozwiązania VMware firmy Azure przez CloudSimple przy użyciu Azure Data Box

Rozwiązanie chmurowe Microsoft Azure Data Box umożliwia wysyłanie terabajtów danych lokalnych na platformę Azure w szybki, niedrogi i niezawodny sposób. Bezpieczny transfer danych jest przyspieszany przez wysłanie do Ciebie należącego do firmy Microsoft urządzenia magazynującego Data Box. Każde urządzenie ma dostępną pojemność wynoszącą 80 TB i jest dostarczane do Twojego centrum danych przez regionalnego przewoźnika. Urządzenie ma odporną na wstrząsy obudowę chroniącą i zabezpieczającą dane w czasie transportu.

Za pomocą Azure Data Box można migrować zbiorczo dane programu VMware do chmury prywatnej.  Dane z lokalnego środowiska vSphere są kopiowane do urządzenie Data Box przy użyciu protokołu NFS.  Migracja danych zbiorczych polega na skopiowaniu do urządzenie Data Box kopii zapasowych maszyn wirtualnych, konfiguracji i skojarzonych danych w danym momencie.

W tym artykule omówiono następujące informacje:

* Konfigurowanie Azure Data Box.
* Kopiowanie danych z lokalnego środowiska VMware do urządzenie Data Box przy użyciu systemu plików NFS.
* Przygotowywanie do powrotu Azure Data Box.
* Przygotuj dane obiektów BLOB na potrzeby kopiowania do rozwiązania VMware platformy Azure przez CloudSimple.
* Kopiowanie danych z platformy Azure do chmury prywatnej.

## <a name="scenarios"></a>Scenariusze

Azure Data Box należy używać w następujących scenariuszach w przypadku migracji danych zbiorczych.

* Przeprowadź migrację dużej ilości danych z rozwiązań lokalnych do platformy Azure VMware przez CloudSimple, aby były używane jako linie bazowe i synchroniczne różnice w sieci.
* Migruj duże ilości maszyn wirtualnych (zimnych maszyn wirtualnych).
* Przeprowadź migrację danych maszyny wirtualnej w celu skonfigurowania środowisk deweloperskich i testowych.
* Migruj dużą liczbę szablonów maszyn wirtualnych, obrazów ISO, dysków maszyn wirtualnych.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Sprawdź wymagania wstępne i [kolejność urządzenie Data Box](../databox/data-box-deploy-ordered.md) z Azure Portal.  W trakcie procesu zamawiania należy wybrać konto magazynu, które umożliwi korzystanie z magazynu obiektów BLOB.  Po otrzymaniu urządzenie Data Box Połącz je z siecią lokalną i [skonfiguruj urządzenie Data Box](../databox/data-box-deploy-set-up.md) przy użyciu adresu IP dostępnego z sieci zarządzania vSphere.

* Utwórz sieć wirtualną i konto magazynu w tym samym regionie platformy Azure, w którym zainicjowano rozwiązanie Azure VMware według CloudSimple.

* Utwórz [połączenie sieci wirtualnej platformy Azure](cloudsimple-azure-network-connection.md) z chmury prywatnej do sieci wirtualnej, w której utworzono konto magazynu, korzystając z kroków wymienionych w artykule [łączenie usługi Azure Virtual Network do CloudSimple przy użyciu ExpressRoute](virtual-network-connection.md)

## <a name="set-up-azure-data-box-for-nfs"></a>Konfigurowanie Azure Data Box dla systemu plików NFS

Połącz się z Azure Data Box lokalnym interfejsem użytkownika sieci Web, wykonując kroki opisane w sekcji **łączenie się z urządzeniem** w [samouczku: kabel i połącz się z Azure Data Box](../databox/data-box-deploy-set-up.md).  Skonfiguruj urządzenie Data Box, aby umożliwić dostęp do klientów NFS.

1. W lokalnym internetowym interfejsie użytkownika przejdź do strony **Połącz i skopiuj**. W obszarze **Ustawienia sieciowego systemu plików** kliknij przycisk **Dostęp klienta do sieciowego systemu plików**. 

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 1](media/nfs-client-access.png)

2. Wprowadź adres IP hostów VMware ESXi i kliknij przycisk **Dodaj**. Można skonfigurować dostęp dla wszystkich hostów w klastrze vSphere, powtarzając ten krok. Kliknij przycisk **OK**.

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu**. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.

W obszarze udziałów blokowych obiektów blob i stronicowych obiektów blob jednostki pierwszego poziomu są kontenerami, a jednostki drugiego poziomu są obiektami blob. W obszarze udziałów dla usługi Azure Files jednostki pierwszego poziomu są udziałami, a jednostki drugiego poziomu są plikami.

W poniższej tabeli przedstawiono ścieżkę UNC do udziałów na urządzeniu Data Box i adres URL ścieżki w usłudze Azure Storage, pod który przekazywane są dane. Ostateczny adres URL w usłudze Azure Storage można uzyskać ze ścieżki udziału UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Blokowe obiekty blob platformy Azure | <li>Ścieżka UNC do udziałów: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Stronicowe obiekty blob platformy Azure  | <li>Ścieżka UNC do udziałów: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Usługa Pliki systemu Azure       |<li>Ścieżka UNC do udziałów: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Użyj blokowych obiektów blob platformy Azure do kopiowania danych programu VMware.

## <a name="mount-nfs-share-as-a-datastore-on-on-premises-vcenter-cluster-and-copy-data"></a>Instalowanie udziału NFS jako magazynu danych w lokalnym klastrze vCenter i kopiowanie danych

Udziały NFS z Azure Data Box muszą być zainstalowane jako magazyn danych w lokalnym klastrze vCenter lub na hoście VMware ESXi na potrzeby kopiowania danych.  Po zainstalowaniu dane można kopiować do magazynu danych systemu plików NFS.

1. Zaloguj się do lokalnego serwera vCenter.

2. Kliknij prawym przyciskiem **myszy centrum** **danych** , wybierz pozycję Magazyn Dodaj kliknij pozycję **nowe datastore** , a następnie kliknij przycisk **dalej** .

   ![Dodaj nowy magazyn danych](media/databox-migration-add-datastore.png)

3. W kroku 1 Kreatora dodawania magazynu danych wybierz typ **systemu plików NFS**.

   ![Dodawanie nowego magazynu danych — typ](media/databox-migration-add-datastore-type.png)

4. W kroku 2 Wybierz pozycję System **plików NFS 3** jako wersję systemu plików NFS i kliknij przycisk **dalej**.

   ![Dodawanie nowego magazynu danych — wersja systemu plików NFS](media/databox-migration-add-datastore-nfs-version.png)

5. W kroku 3 Określ nazwę magazynu danych, ścieżkę i serwer.  Możesz użyć adresu IP urządzenie Data Box serwera.  Ścieżka folderu będzie w formacie `/<StorageAccountName_BlockBlob>/<ContainerName>/`.

   ![Dodawanie nowego magazynu danych — konfiguracja systemu plików NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. W kroku 4 Wybierz hosty ESXi, w których chcesz zainstalować magazyn danych, a następnie kliknij przycisk **dalej**.  W klastrze wybierz pozycję Wszystkie hosty, aby zapewnić migrację maszyn wirtualnych.

   ![Dodawanie nowego magazynu danych — wybierz hosty](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. W kroku 5 Przejrzyj podsumowanie i kliknij przycisk **Zakończ** .

## <a name="copy-data-to-data-box-nfs-datastore"></a>Kopiuj dane do urządzenie Data Box magazynu danych systemu plików NFS

Maszyny wirtualne można migrować lub sklonować do nowego magazynu danych.  Wszystkie nieużywane maszyny wirtualne, które należy zmigrować, można migrować do urządzenie Data Box magazynu danych NFS przy użyciu usługi **Storage vMotion**.  Aktywne maszyny wirtualne można **sklonować** do magazynu danych systemu urządzenie Data Box NFS.

* Zidentyfikuj listę maszyn wirtualnych, które można **przenieść**.
* Zidentyfikuj listę maszyn wirtualnych, które muszą zostać **sklonowane**.

### <a name="move-virtual-machine-to-data-box-datastore"></a>Przenieś maszynę wirtualną do urządzenie Data Box magazynu danych

1. Kliknij prawym przyciskiem myszy maszynę wirtualną, którą chcesz przenieść do urządzenie Data Box magazynu danych, a następnie wybierz pozycję **Migruj**.

    ![Migruj maszynę wirtualną](media/databox-migration-vm-migrate.png)

2. Wybierz pozycję **Zmień magazyn tylko** dla pozycji Typ migracji, a następnie kliknij przycisk **dalej**.

    ![Migruj tylko magazyn maszyny wirtualnej](media/databox-migration-vm-migrate-change-storage.png)

3. Wybierz pozycję urządzenie Data Box magazyn danych jako lokalizację docelową, a następnie kliknij przycisk **dalej**.

    ![Migrowanie maszyny wirtualnej — Wybieranie magazynu danych](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Przejrzyj informacje i kliknij przycisk **Zakończ**.

5. Powtórz kroki od 1 do 4 dla innych maszyn wirtualnych.

> [!TIP]
> Można wybrać wiele maszyn wirtualnych w tym samym stanie zasilania (włączona lub wyłączona) i przeprowadzić migrację zbiorczą.

Magazyn dla maszyny wirtualnej zostanie zmigrowany do magazynu danych systemu plików NFS z Azure Data Box.  Po przeprowadzeniu migracji wszystkich maszyn wirtualnych można wyłączyć zasilanie maszyn wirtualnych w ramach przygotowania do migracji danych do rozwiązania VMware platformy Azure.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-data-box-datastore"></a>Klonowanie maszyny wirtualnej lub szablonu maszyny wirtualnej w celu urządzenie Data Box magazynu danych

1. Kliknij prawym przyciskiem myszy maszynę wirtualną lub szablon maszyny wirtualnej, który ma zostać sklonowany.  Wybieranie **klonowania**, **klonowania do maszyny wirtualnej**

    ![Klonowanie maszyny wirtualnej](media/databox-migration-vm-clone.png)

2. Wybierz nazwę sklonowanej maszyny wirtualnej lub szablonu maszyny wirtualnej.

3. Wybierz folder, w którym chcesz umieścić Sklonowany obiekt, a następnie kliknij przycisk **dalej**.

4. Wybierz klaster lub pulę zasobów, w której ma zostać Sklonowany obiekt, i kliknij przycisk **dalej**.

5. Wybierz magazyn danych systemu plików NFS Azure Data Box jako lokalizację magazynu, a następnie kliknij przycisk **dalej**.

    ![Klonowanie maszyny wirtualnej — Wybieranie magazynu danych](media/databox-migration-vm-clone-select-datastore.png)

6. Wybierz opcje dostosowywania, jeśli chcesz dostosować wszystkie opcje dla sklonowanego obiektu, a następnie kliknij przycisk **dalej**.

7. Przejrzyj konfiguracje i kliknij przycisk **Zakończ**.

8. Powtórz kroki od 1 do 7 w przypadku dodatkowych maszyn wirtualnych lub szablonów maszyn wirtualnych.

Maszyny wirtualne zostaną sklonowane i zapisane w magazynie danych systemu plików NFS z Azure Data Box.  Po sklonowaniu maszyn wirtualnych upewnij się, że sklonowane maszyny wirtualne są wyłączone w celu migracji danych do rozwiązania VMware platformy Azure.

### <a name="copy-iso-files-to-data-box-datastore"></a>Kopiuj pliki ISO do urządzenie Data Box magazynu danych

1. Z lokalnego interfejsu użytkownika vCenter sieci Web, przejdź do **magazynu**.  Wybierz pozycję urządzenie Data Box magazyn danych systemu plików NFS i kliknij pozycję **pliki**.  Utwórz **Nowy folder** do przechowywania plików ISO.

    ![Kopiuj ISO — Utwórz nowy folder](media/databox-migration-create-folder.png)

2. Nadaj nazwę folderowi, w którym będą przechowywane pliki ISO.

3. Kliknij dwukrotnie nowo utworzony folder, aby uzyskać dostęp do zawartości.

4. Kliknij pozycję **Przekaż pliki** i wybierz obrazów ISO, które chcesz przekazać.
    
    ![Kopiuj pliki z przekazywaniem ISO](media/databox-migration-upload-iso.png)

> [!TIP]
> Jeśli masz już pliki ISO w lokalnym magazynie danych, możesz wybrać pliki i **skopiować je do** magazynu danych systemu plików NFS urządzenie Data Box.


## <a name="prepare-azure-data-box-for-return"></a>Przygotuj Azure Data Box do zwrócenia

Po skopiowaniu wszystkich danych maszyny wirtualnej dane szablonu maszyny wirtualnej i dowolne pliki ISO są kopiowane do magazynu danych systemu urządzenie Data Box NFS. magazyn danych może zostać odłączony od serwera vCenter.  Wszystkie maszyny wirtualne i szablony maszyn wirtualnych muszą zostać usunięte ze spisu, zanim będzie możliwe odłączenie magazynu danych.

### <a name="remove-objects-from-inventory"></a>Usuwanie obiektów ze spisu

1. Z lokalnego interfejsu użytkownika vCenter sieci Web, przejdź do **magazynu**.  Wybierz pozycję urządzenie Data Box magazyn danych systemu plików NFS i kliknij pozycję **maszyny wirtualne**.

2. Upewnij się, że wszystkie maszyny wirtualne są wyłączone.

    ![Usuwanie maszyn wirtualnych z spisu — Zasilanie wyłączone](media/databox-migration-select-databox-vm.png)

3. Wybierz pozycję Wszystkie maszyny wirtualne, kliknij prawym przyciskiem myszy i wybierz pozycję **Usuń ze spisu**.

    ![Usuwanie maszyn wirtualnych ze spisu](media/databox-migration-remove-vm-from-inventory.png)

4. Wybierz pozycję **szablony maszyn wirtualnych w folderach** z górnych przycisków i powtórz krok 3. 

### <a name="remove-azure-data-box-nfs-datastore-from-vcenter"></a>Usuwanie Azure Data Box magazynu danych systemu plików NFS z programu vCenter

Urządzenie Data Box magazynu danych systemu plików NFS należy odłączyć od hostów VMware ESXi przed przygotowaniem do powrotu.

1. Z lokalnego interfejsu użytkownika vCenter sieci Web, przejdź do **magazynu**.

2. Kliknij prawym przyciskiem myszy magazyn danych NFS urządzenie Data Box i wybierz pozycję **Odinstaluj magazyn**danych.

    ![Odinstalowanie urządzenie Data Box magazynu danych](media/databox-migration-unmount-datastore.png)

3. Zaznacz wszystkie hosty ESXi, na których jest zainstalowany magazyn danych, a następnie kliknij przycisk **OK**.

    ![Odinstalowywanie urządzenie Data Box magazynu danych — wybierz hosty](media/databox-migration-unmount-datastore-select-hosts.png)

4. Przejrzyj i Zaakceptuj wszystkie ostrzeżenia, a następnie kliknij przycisk **OK**.

### <a name="prepare-data-box-for-return-and-return-the-data-box"></a>Przygotuj urządzenie Data Box do zwrócenia i zwrócenia urządzenie Data Box

Wykonaj kroki opisane w artykule [zwróć Azure Data Box i Zweryfikuj przekazywanie danych do platformy Azure](../databox/data-box-deploy-picked-up.md) , aby zwrócić urządzenie Data Box.  Sprawdź stan kopiowania danych na konto usługi Azure Storage, a po zakończeniu wyświetlania stanu można sprawdzić dane na koncie usługi Azure Storage.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution-by-cloudsimple"></a>Kopiowanie danych z usługi Azure Storage do rozwiązania VMware platformy Azure przez CloudSimple

Dane skopiowane do Azure Data Box będą dostępne na koncie usługi Azure Storage, gdy stan zamówienia urządzenie Data Box zostanie wyświetlony jako zakończony.  Dane można teraz skopiować do rozwiązania VMware platformy Azure przez CloudSimple.  Dane na koncie magazynu muszą zostać skopiowane przy użyciu protokołu NFS do magazynu danych sieci vSAN w chmurze prywatnej.  Najpierw skopiuj dane magazynu obiektów BLOB na dysk zarządzany na maszynie wirtualnej z systemem Linux na platformie Azure przy użyciu **AzCopy**.  Udostępnij dysk zarządzany za pomocą protokołu NFS i zainstaluj udział NFS jako magazyn danych w chmurze prywatnej i skopiuj dane.  Ta metoda umożliwia szybszą kopię danych w chmurze prywatnej. 

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-export-as-nfs-share"></a>Kopiowanie danych do chmury prywatnej przy użyciu maszyny wirtualnej z systemem Linux i dysków zarządzanych oraz eksportowanie ich jako udziału NFS

1. Utwórz [maszynę wirtualną z systemem Linux](../virtual-machines/linux/quick-create-portal.md) na platformie Azure w tym samym regionie, w którym utworzono konto magazynu i ma połączenie z siecią wirtualną platformy Azure z chmurą prywatną.

2. Utwórz dysk zarządzany, który jest większy niż ilość danych obiektów blob, a następnie [Dołącz go do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/attach-disk-portal.md).  Jeśli ilość danych obiektów BLOB jest większa niż największy dostępny dysk zarządzany, dane muszą zostać skopiowane w wielu krokach lub przy użyciu wielu dysków zarządzanych.

3. Nawiąż połączenie z maszyną wirtualną z systemem Linux i zainstaluj dysk zarządzany.

4. Zainstaluj program [AzCopy na maszynie wirtualnej z systemem Linux](../storage/common/storage-use-azcopy-v10.md).

5. Pobierz dane z usługi Azure Blob Storage na dysk zarządzany przy użyciu AzCopy.  Składnia polecenia: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  Zastąp `<storage-account-name>` nazwą konta usługi Azure Storage i `<container-name>` kontenerem zawierającym dane skopiowane przy użyciu Azure Data Box.

6. Zainstaluj serwer NFS na maszynie wirtualnej z systemem Linux.

    1. W dystrybucji Ubuntu/Debian: `sudo apt install nfs-kernel-server`.
    2. W dystrybucji systemu Linux w przedsiębiorstwie: `sudo yum install nfs-utils`.

7. Zmień uprawnienia folderu na dysku zarządzanym, w którym skopiowano dane z magazynu obiektów blob platformy Azure.  Zmień uprawnienia dla wszystkich folderów, które mają zostać wyeksportowane jako udział NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Przypisz uprawnienia do adresów IP klientów, aby uzyskać dostęp do udziału NFS, edytując plik `/etc/exports`.

    ```bash
    sudo vi /etc/exports
    ```
    
    Wprowadź następujące wiersze w pliku dla każdego adresu IP hosta ESXi w chmurze prywatnej.  Jeśli tworzysz udziały dla wielu folderów, Dodaj wszystkie foldery.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Wyeksportuj udziały NFS przy użyciu polecenia `sudo exportfs -a`.

10. Uruchom ponownie serwer jądra systemu plików NFS przy użyciu polecenia `sudo systemctl restart nfs-kernel-server`.


### <a name="mount-linux-virtual-machine-nfs-share-as-a-datastore-on-private-cloud-vcenter-cluster-and-copy-data"></a>Instalowanie udziału NFS maszyny wirtualnej z systemem Linux jako magazynu danych w klastrze programu vCenter w chmurze prywatnej i kopiowanie danych

Aby można było skopiować dane, udział NFS z maszyny wirtualnej z systemem Linux musi być zainstalowany jako magazyn danych w klastrze programu vCenter w chmurze prywatnej.  Po zainstalowaniu dane można kopiować z magazynu danych systemu plików NFS do magazynu prywatnego usługi Cloud sieci vSAN.

1. Zaloguj się do swojego prywatnego serwera usługi Cloud vCenter.

2. Kliknij prawym przyciskiem **myszy centrum** **danych** , wybierz pozycję Magazyn Dodaj kliknij pozycję **nowe datastore** , a następnie kliknij przycisk **dalej** .

   ![Dodaj nowy magazyn danych](media/databox-migration-add-datastore.png)

3. W kroku 1 Kreatora dodawania magazynu danych wybierz typ **systemu plików NFS**.

   ![Dodawanie nowego magazynu danych — typ](media/databox-migration-add-datastore-type.png)

4. W kroku 2 Wybierz pozycję System **plików NFS 3** jako wersję systemu plików NFS i kliknij przycisk **dalej**.

   ![Dodawanie nowego magazynu danych — wersja systemu plików NFS](media/databox-migration-add-datastore-nfs-version.png)

5. W kroku 3 Określ nazwę magazynu danych, ścieżkę i serwer.  Możesz użyć adresu IP maszyny wirtualnej systemu Linux dla serwera.  Ścieżka folderu będzie w formacie `/<folder>/<subfolder>/`.

   ![Dodawanie nowego magazynu danych — konfiguracja systemu plików NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. W kroku 4 Wybierz hosty ESXi, w których chcesz zainstalować magazyn danych, a następnie kliknij przycisk **dalej**.  W klastrze wybierz pozycję Wszystkie hosty, aby zapewnić migrację maszyn wirtualnych.

   ![Dodawanie nowego magazynu danych — wybierz hosty](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. W kroku 5 Przejrzyj podsumowanie i kliknij przycisk **Zakończ** .

### <a name="add-virtual-machines-and-virtual-machine-templates-from-nfs-datastore-to-the-inventory"></a>Dodawanie maszyn wirtualnych i szablonów maszyn wirtualnych z magazynu danych systemu plików NFS do spisu

1. W prywatnym interfejsie użytkownika sieci Web vCenter w chmurze przejdź do **magazynu**.  Wybierz pozycję Magazyn danych NFS z systemem Linux i kliknij pozycję **pliki**.

    ![Wybieranie plików z magazynu danych NFS](media/databox-migration-datastore-select-files.png)

2. Wybierz folder, który zawiera maszynę wirtualną lub szablon maszyny wirtualnej.  W okienku szczegółów wybierz plik `.vmx` dla maszyny wirtualnej lub pliku `.vmtx` dla szablonu maszyny wirtualnej.

3. Kliknij pozycję **zarejestruj maszynę** wirtualną w celu zarejestrowania maszyny wirtualnej w programie vCenter w chmurze prywatnej.

    ![Zarejestruj maszynę wirtualną](media/databox-migration-datastore-register-vm.png)

4. Wybierz centrum danych, folder i klaster/pulę zasobów, w których maszyna wirtualna ma zostać zarejestrowana.

4. Powtórz kroki 3 i 4 dla wszystkich maszyn wirtualnych i szablonów maszyn wirtualnych.

5. Przejdź do folderu, który zawiera pliki ISO.  Wybierz pozycję pliki ISO i **skopiuj do** folderu w magazynie danych sieci vSAN.

Maszyny wirtualne i szablony maszyn wirtualnych są teraz dostępne w chmurze prywatnej programu vCenter.  Te maszyny wirtualne muszą zostać przeniesione z magazynu danych systemu plików NFS do magazynu danych sieci vSAN przed przystąpieniem do nich.  Można wykonać vMotion magazynu maszyn wirtualnych i wybrać magazyn danych sieci vSAN jako element docelowy dla maszyny wirtualnej.

Szablony maszyn wirtualnych muszą być sklonowane z magazynu danych systemu plików NFS maszyny wirtualnej z systemem Linux do magazynu danych sieci vSAN.

### <a name="clean-up-of-your-linux-virtual-machine"></a>Wyczyść maszynę wirtualną z systemem Linux

Po skopiowaniu wszystkich danych do chmury prywatnej można usunąć magazyn danych systemu plików NFS z chmury prywatnej.

1. Upewnij się, że wszystkie maszyny wirtualne i szablony zostały przeniesione i sklonowane do magazynu danych sieci vSAN.

2. Usuń wszystkie szablony maszyn wirtualnych z magazynu danych systemu plików NFS ze spisu.

3. Odinstalowanie magazynu danych maszyny wirtualnej z systemem Linux z poziomu programu vCenter w chmurze prywatnej.

4. Usuń maszynę wirtualną i dysk zarządzany z platformy Azure.

5. Jeśli nie chcesz przechowywać danych transferowanych przy użyciu Azure Data Box na koncie magazynu, Usuń konto usługi Azure Storage.  
    


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Azure Data Box](../databox/data-box-overview.md)
* Dowiedz się więcej na temat różnych opcji [migrowania obciążeń do chmury prywatnej](migrate-workloads.md)
