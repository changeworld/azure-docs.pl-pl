---
title: Rozwiązanie Azure VMware — migracja przy użyciu usługi Azure Data Box
description: Jak używać usługi Azure Data Box do zbiorczej migracji danych do rozwiązania Azure VMware.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65167169248d83ebfec2c49c308673ec9315934e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019761"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Migrowanie danych do rozwiązania Azure VMware przy użyciu usługi Azure Data Box

Rozwiązanie w chmurze microsoft azure data box umożliwia szybkie, niedrogie i niezawodne wysyłanie danych na platformę Azure w celu szybkiego, niedrogiego i niezawodnego wysyłania terabajtów danych. Bezpieczny transfer danych jest przyspieszany przez wysłanie do Ciebie należącego do firmy Microsoft urządzenia magazynującego Data Box. Każde urządzenie pamięci masowej ma maksymalną pojemność 80 TB i jest transportowane do centrum danych przez regionalnego operatora. Urządzenie ma wytrzymałą obudowę, która chroni i zabezpiecza dane podczas przesyłania.

Korzystając z pola danych, można zbiorczo migrować dane VMware do chmury prywatnej. Dane z lokalnego środowiska VMware vSphere są kopiowane do pola danych za pośrednictwem protokołu NFS (Network File System). Zbiorcza migracja danych obejmuje zapisywanie kopii maszyn wirtualnych, konfiguracji i skojarzonych danych w punkcie danych w czasie, a następnie ręczne przesyłanie ich na platformę Azure.

W tym artykule dowiesz się o:

* Konfigurowanie pola danych.
* Kopiowanie danych z lokalnego środowiska VMware do pola danych za pośrednictwem nfs.
* Przygotowanie do zwrotu data box.
* Przygotowywanie danych obiektów blob do kopiowania do rozwiązania Azure VMware.
* Kopiowanie danych z platformy Azure do chmury prywatnej.

## <a name="scenarios"></a>Scenariusze

Użyj pola danych w następujących scenariuszach do masowej migracji danych:

* Aby przeprowadzić migrację dużej ilości danych z lokalnego rozwiązania VMware do platformy Azure. Ta metoda ustanawia linię bazową i synchronizuje różnice w sieci.
* Aby przeprowadzić migrację dużej liczby maszyn wirtualnych, które są wyłączone (zimne maszyny wirtualne).
* Aby przeprowadzić migrację danych maszyny wirtualnej do konfigurowania środowisk deweloperskich i testowych.
* Aby przeprowadzić migrację dużej liczby szablonów maszyn wirtualnych, plików ISO i dysków maszyn wirtualnych.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Sprawdź wymagania wstępne i [zamów pole danych](../databox/data-box-deploy-ordered.md) za pośrednictwem witryny Azure portal. Podczas procesu zamawiania należy wybrać konto magazynu, które umożliwia magazyn obiektów Blob. Po odebraniu urządzenia Data Box podłącz je do sieci lokalnej i [skonfiguruj urządzenie](../databox/data-box-deploy-set-up.md) z adresem IP, który jest osiągalny z sieci zarządzania vSphere.

* Utwórz sieć wirtualną i konto magazynu w tym samym regionie, w którym jest aprowizowana usługa Azure VMware Solution.

* Utwórz [połączenie sieci wirtualnej platformy Azure](cloudsimple-azure-network-connection.md) z chmury prywatnej do sieci wirtualnej, w której jest tworzone konto magazynu, wykonując kroki opisane w obszarze [Połącz sieć wirtualną platformy Azure z usługą CloudSimple przy użyciu usługi ExpressRoute](virtual-network-connection.md).

## <a name="set-up-data-box-for-nfs"></a>Konfigurowanie pola danych dla plików NFS

Połącz się z lokalnym interfejsem użytkownika sieci Web data box, wykonując kroki opisane w sekcji "Połącz się z urządzeniem" [w samouczku: Kabel i połącz się z urządzeniem Azure Data Box](../databox/data-box-deploy-set-up.md).  Skonfiguruj pole danych, aby umożliwić dostęp do klientów NFS:

1. W lokalnym interfejsie użytkownika sieci Web przejdź do strony **Połącz i skopiuj.** W obszarze **Ustawienia systemu plików NFS**wybierz pozycję **Dostęp klienta systemu plików NFS**. 

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 1](media/nfs-client-access.png)

2. Wprowadź adres IP hostów VMware ESXi i wybierz pozycję **Dodaj**. Dostęp dla wszystkich hostów w klastrze vSphere można skonfigurować, powtarzając ten krok. Kliknij przycisk **OK**.

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu**. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Nie można kopiować plików bezpośrednio do folderu *głównego* na koncie magazynu.

W obszarze udziałów blokowych obiektów blob i stronicowych obiektów blob jednostki pierwszego poziomu są kontenerami, a jednostki drugiego poziomu są obiektami blob. W obszarze udziały dla usługi Azure Files jednostki pierwszego poziomu są udziałami, a jednostki drugiego poziomu są plikami.

W poniższej tabeli przedstawiono ścieżkę UNC do udziałów na urządzeniu Data Box i adres URL ścieżki w usłudze Azure Storage, pod który przekazywane są dane. Ostateczny adres URL w usłudze Azure Storage można uzyskać ze ścieżki udziału UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Blokowe obiekty blob platformy Azure | <li>Ścieżka UNC do udziałów: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Stronicowe obiekty blob platformy Azure  | <li>Ścieżka UNC do udziałów: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Ścieżka UNC do udziałów: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Użyj obiektów blob bloku platformy Azure do kopiowania danych VMware.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Instalowanie udziału NFS jako magazynu danych w lokalnym klastrze vCenter i kopiowanie danych

Udział nfs z pola danych musi być zainstalowany jako magazyn danych na lokalnym klastrze vCenter lub vMware ESXi hosta w celu skopiowania danych do magazynu danych NFS:

1. Zaloguj się do lokalnego serwera vCenter.

2. Kliknij prawym przyciskiem myszy **pozycję Centrum danych**, wybierz pozycję **Magazyn**, wybierz pozycję Nowy **magazyn danych**, a następnie wybierz pozycję **Dalej**.

   ![Dodawanie nowego magazynu danych](media/databox-migration-add-datastore.png)

3. W kroku 1 kreatora Dodawanie magazynu danych wybierz pozycję **NFS** w obszarze **Typ**.

   ![Dodaj nowy magazyn danych - typ](media/databox-migration-add-datastore-type.png)

4. W kroku 2 kreatora wybierz pozycję **NFS 3** jako wersję systemu plików NFS, a następnie wybierz pozycję **Dalej**.

   ![Dodawanie nowego magazynu danych — wersja systemu plików NFS](media/databox-migration-add-datastore-nfs-version.png)

5. W kroku 3 kreatora określ nazwę magazynu danych, ścieżki i serwera. Można użyć adresu IP pola danych dla serwera. Ścieżka folderu będzie `/<StorageAccountName_BlockBlob>/<ContainerName>/` w formacie.

   ![Dodawanie nowego magazynu danych — konfiguracja systemu plików NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. W kroku 4 kreatora wybierz hosty ESXi, na których ma być zainstalowany magazyn danych, a następnie wybierz pozycję **Dalej**.  W klastrze wybierz wszystkie hosty, aby zapewnić migrację maszyn wirtualnych.

   ![Dodawanie nowego magazynu danych — wybieranie hostów](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. W kroku 5 kreatora przejrzyj podsumowanie i wybierz **pozycję Zakończ**.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Kopiowanie danych do magazynu plików NFS pola danych

Maszyny wirtualne mogą być migrowane lub klonowane do nowego magazynu danych.  Wszystkie nieużywane maszyny wirtualne, które chcesz przeprowadzić migrację, można przeprowadzić migrację do magazynu danych NFS w polu danych przy użyciu opcji **vMotion magazynu.** Aktywne maszyny wirtualne można sklonować do magazynu danych NFS pola danych.

* Identyfikowanie i wyświetlanie listy maszyn wirtualnych, które można **przenosić.**
* Identyfikowanie i wyświetlanie listy maszyn wirtualnych, które muszą zostać **sklonowane**.

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Przenoszenie maszyny wirtualnej do magazynu danych Data Box

1. Kliknij prawym przyciskiem myszy maszynę wirtualną, którą chcesz przenieść do magazynu danych Data Box, a następnie wybierz polecenie **Migruj**.

    ![Migrowanie maszyny wirtualnej](media/databox-migration-vm-migrate.png)

2. Wybierz **pozycję Zmień magazyn tylko** dla typu migracji, a następnie wybierz pozycję **Dalej**.

    ![Migrowanie maszyny wirtualnej — tylko magazyn](media/databox-migration-vm-migrate-change-storage.png)

3. Wybierz **databox-Datastore** jako miejsce docelowe, a następnie wybierz **przycisk Dalej**.

    ![Migrowanie maszyny wirtualnej — wybierz magazyn danych](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Przejrzyj informacje i wybierz pozycję **Zakończ**.

5. Powtórz kroki od 1 do 4 dla dodatkowych maszyn wirtualnych.

> [!TIP]
> Można wybrać wiele maszyn wirtualnych, które są w tym samym stanie zasilania (włączone lub wyłączone) i migrować je zbiorczo.

Maszyna wirtualna zostanie zmigrowana do magazynu danych NFS z pola danych. Po migracji wszystkich maszyn wirtualnych można wyłączyć (zamknąć) aktywne maszyny wirtualne w ramach przygotowań do migracji danych do rozwiązania Azure VMware.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Klonowanie maszyny wirtualnej lub szablonu maszyny wirtualnej do magazynu danych Data Box

1. Kliknij prawym przyciskiem myszy maszynę wirtualną lub szablon maszyny wirtualnej, który chcesz sklonować. Wybierz **opcję Klonuj** > **klonuj na maszynę wirtualną**.

    ![Klon maszyny wirtualnej](media/databox-migration-vm-clone.png)

2. Wybierz nazwę sklonowanej maszyny wirtualnej lub szablonu maszyny wirtualnej.

3. Zaznacz folder, w którym chcesz umieścić sklonowany obiekt, a następnie wybierz pozycję **Dalej**.

4. Wybierz klaster lub pulę zasobów, w której chcesz umieścić sklonowany obiekt, a następnie wybierz pozycję **Dalej**.

5. Wybierz **databox-Datastore** jako lokalizację magazynu, a następnie wybierz pozycję **Dalej**.

    ![Klon maszyny wirtualnej - wybierz magazyn danych](media/databox-migration-vm-clone-select-datastore.png)

6. Jeśli chcesz dostosować wszystkie opcje sklonowanego obiektu, zaznacz opcje dostosowywania, a następnie wybierz przycisk **Dalej**.

7. Przejrzyj konfiguracje i **wybierz**zakończ .

8. Powtórz kroki od 1 do 7 dla dodatkowych maszyn wirtualnych lub szablonów maszyn wirtualnych.

Maszyny wirtualne zostaną sklonowane i przechowywane w magazynie danych NFS z pola danych. Po sklonowanie maszyn wirtualnych upewnij się, że są one zamykane w ramach przygotowań do migracji danych do rozwiązania Azure VMware.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Kopiowanie plików ISO do magazynu danych Data Box

1. Z lokalnego interfejsu użytkownika sieci web vCenter przejdź do **witryny Magazyn**.  Wybierz **pozycję Databox-Datastore,** a następnie wybierz pozycję **Pliki**. Utwórz nowy folder do przechowywania plików ISO.

    ![Kopiuj ISO - utwórz nowy folder](media/databox-migration-create-folder.png)

2. Podaj nazwę folderu, w którym będą przechowywane pliki ISO.

3. Kliknij dwukrotnie nowo utworzony folder, aby go otworzyć.

4. Wybierz **pozycję Przekaż pliki,** a następnie wybierz pliki ISO, które chcesz przekazać.
    
    ![Kopiuj ISO - przekazywanie plików](media/databox-migration-upload-iso.png)

> [!TIP]
> Jeśli w lokalnym magazynie danych znajdują się już pliki ISO, możesz je wybrać i **skopiować, aby skopiować** pliki do magazynu danych NFS.


## <a name="prepare-data-box-for-return"></a>Przygotuj skrzynkę danych do zwrotu

Po skopiowaniu wszystkich danych maszyny wirtualnej, danych szablonu maszyny wirtualnej i wszelkich plików ISO do magazynu danych NFS pola danych można odłączyć magazyn danych od centrum vCenter. Wszystkie maszyny wirtualne i szablony maszyn wirtualnych muszą zostać usunięte z magazynu przed odłączeniem magazynu danych.

### <a name="remove-objects-from-inventory"></a>Usuwanie obiektów z magazynu

1. Z lokalnego interfejsu użytkownika sieci web vCenter przejdź do **witryny Magazyn**. Wybierz **databox-Datastore,** a następnie wybierz maszyn **wirtualnych**.

    ![Usuwanie maszyn wirtualnych z magazynu - wyłączone](media/databox-migration-select-databox-vm.png)

2. Upewnij się, że wszystkie maszyny wirtualne są zamknięte.

3. Zaznacz wszystkie maszyny wirtualne, kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Usuń z magazynu**.

    ![Usuwanie maszyn wirtualnych z magazynu](media/databox-migration-remove-vm-from-inventory.png)

4. Wybierz **pozycję Szablony maszyn wirtualnych w folderach,** a następnie powtórz krok 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Usuwanie magazynu plików NFS pola danych z centrum vCenter

Magazyn danych NFS pola danych musi zostać odłączony od hostów VMware ESXi przed przygotowaniem do powrotu.

1. Z lokalnego interfejsu użytkownika sieci web vCenter przejdź do **witryny Magazyn**.

2. Kliknij prawym przyciskiem myszy **pozycję Databox-Datastore** i wybierz polecenie **Odtwórz magazyn danych**.

    ![Odinstalowywszła skrzynka danych](media/databox-migration-unmount-datastore.png)

3. Wybierz wszystkie hosty ESXi, na których jest zainstalowany magazyn danych, i wybierz **przycisk OK**.

    ![Odinstalowywzdowywłasnie danych — wybierz hosty](media/databox-migration-unmount-datastore-select-hosts.png)

4. Przejrzyj i zaakceptuj wszelkie ostrzeżenia i wybierz **PRZYCISK OK**.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Przygotuj pole danych do zwrotu, a następnie zwróć go

Wykonaj kroki opisane w artykule [Zwracaj pole danych platformy Azure i weryfikuj przekazywanie danych na platformę Azure,](../databox/data-box-deploy-picked-up.md) aby zwrócić pole danych. Sprawdź stan kopii danych na swoje konto magazynu platformy Azure. Po wyświetlenie stanu jako ukończonego można zweryfikować dane na koncie magazynu platformy Azure.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Kopiowanie danych z magazynu platformy Azure do rozwiązania Azure VMware

Dane skopiowane do urządzenia Data Box będą dostępne na koncie magazynu platformy Azure po wyświetleniu stanu zamówienia w polu danych jako ukończonego. Dane można teraz skopiować do rozwiązania Azure VMware. Dane na koncie magazynu muszą być kopiowane do magazynu danych vSAN w chmurze prywatnej przy użyciu protokołu NFS. 

Najpierw skopiuj dane magazynu obiektów Blob na zarządzany dysk na maszynie wirtualnej systemu Linux na platformie Azure przy użyciu **programu AzCopy**. Udostępnij dysk zarządzany za pośrednictwem usługi NFS, zainstaluj udział NFS jako magazyn danych w chmurze prywatnej, a następnie skopiuj dane. Ta metoda umożliwia szybszą kopiowanie danych do chmury prywatnej.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Kopiowanie danych do chmury prywatnej przy użyciu maszyny wirtualnej systemu Linux i dysków zarządzanych, a następnie eksportowanie jako udziału nfs

1. Utwórz [maszynę wirtualną systemu Linux](../virtual-machines/linux/quick-create-portal.md) na platformie Azure w tym samym regionie, w którym jest tworzone konto magazynu i ma połączenie sieci wirtualnej platformy Azure z chmurą prywatną.

2. Utwórz dysk zarządzany, którego pojemność pamięci masowej jest większa niż ilość danych obiektów blob, i [dołącz go do maszyny wirtualnej systemu Linux](../virtual-machines/linux/attach-disk-portal.md).  Jeśli ilość danych obiektów blob jest większa niż pojemność największego dostępnego dysku zarządzanego, dane muszą być kopiowane w wielu krokach lub przy użyciu wielu dysków zarządzanych.

3. Połącz się z maszyną wirtualną systemu Linux i zainstaluj dysk zarządzany.

4. Zainstaluj [AzCopy na swojej maszynie wirtualnej Linux](../storage/common/storage-use-azcopy-v10.md).

5. Pobierz dane z magazynu obiektów blob platformy Azure na dysk zarządzany przy użyciu programu AzCopy.  Składnia polecenia: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  Zastąp `<storage-account-name>` nazwą konta `<container-name>` magazynu platformy Azure i kontenerem, w który przechowuje dane skopiowane za pośrednictwem pola danych.

6. Zainstaluj serwer NFS na swojej maszynie wirtualnej Systemu Linux:

    - W dystrybucji Ubuntu/Debian: `sudo apt install nfs-kernel-server`.
    - W przypadku dystrybucji `sudo yum install nfs-utils`systemu Enterprise Linux: .

7. Zmień uprawnienia folderu na dysku zarządzanym, gdzie dane z magazynu obiektów Blob platformy Azure zostały skopiowane.  Zmień uprawnienia dla wszystkich folderów, które chcesz wyeksportować jako udział NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Przypisz uprawnienia do adresów IP klienta, aby uzyskać `/etc/exports` dostęp do udziału NFS, edytując plik.

    ```bash
    sudo vi /etc/exports
    ```
    
    Wprowadź następujące wiersze w pliku dla każdego adresu IP hosta ESXi w chmurze prywatnej.  Jeśli tworzysz udziały dla wielu folderów, dodaj wszystkie foldery.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Eksportuj udziały NFS `sudo exportfs -a` za pomocą polecenia.

10. Uruchom ponownie serwer jądra NFS za pomocą polecenia. `sudo systemctl restart nfs-kernel-server`


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Instalowanie udziału nfs maszyny wirtualnej systemu Linux jako magazynu danych w klastrze vCenter chmury prywatnej, a następnie kopiowanie danych

Udział NFS z maszyny wirtualnej systemu Linux musi być zainstalowany jako magazyn danych w klastrze vCenter chmury prywatnej. Po zamontowaniu dane mogą być kopiowane z magazynu danych NFS do magazynu vSAN w chmurze prywatnej.

1. Zaloguj się do serwera vCenter w chmurze prywatnej.

2. Kliknij prawym przyciskiem myszy **pozycję Centrum danych**, wybierz pozycję **Magazyn**, wybierz pozycję Nowy **magazyn danych**, a następnie wybierz pozycję **Dalej**.

   ![Dodawanie nowego magazynu danych](media/databox-migration-add-datastore.png)

3. W kroku 1 kreatora Dodawanie magazynu danych wybierz typ **NFS.**

   ![Dodaj nowy magazyn danych - typ](media/databox-migration-add-datastore-type.png)

4. W kroku 2 kreatora wybierz pozycję **NFS 3** jako wersję systemu plików NFS, a następnie wybierz pozycję **Dalej**.

   ![Dodawanie nowego magazynu danych — wersja systemu plików NFS](media/databox-migration-add-datastore-nfs-version.png)

5. W kroku 3 kreatora określ nazwę magazynu danych, ścieżki i serwera.  Można użyć adresu IP maszyny wirtualnej systemu Linux dla serwera.  Ścieżka folderu będzie `/<folder>/<subfolder>/` w formacie.

   ![Dodawanie nowego magazynu danych — konfiguracja systemu plików NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. W kroku 4 kreatora wybierz hosty ESXi, na których ma być zainstalowany magazyn danych, a następnie wybierz pozycję **Dalej**.  W klastrze wybierz wszystkie hosty, aby zapewnić migrację maszyn wirtualnych.

   ![Dodawanie nowego magazynu danych — wybieranie hostów](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. W kroku 5 kreatora przejrzyj podsumowanie, a następnie wybierz pozycję **Zakończ**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Dodawanie maszyn wirtualnych i szablonów maszyn wirtualnych z magazynu plików NFS do magazynu

1. Z interfejsu użytkownika sieci web vCenter w chmurze prywatnej przejdź do **witryny Storage**.  Wybierz magazyn danych NFS maszyny wirtualnej systemu Linux, a następnie wybierz pozycję **Pliki**.

    ![Wybieranie plików z magazynu danych NFS](media/databox-migration-datastore-select-files.png)

2. Wybierz folder zawierający maszynę wirtualną lub szablon maszyny wirtualnej.  W okienku szczegółów wybierz plik .vmx dla maszyny wirtualnej lub pliku vmtx dla szablonu maszyny wirtualnej.

3. Wybierz **zarejestruj maszynę wirtualną,** aby zarejestrować maszynę wirtualną w centrum vCenter chmury prywatnej.

    ![Zarejestruj maszynę wirtualną](media/databox-migration-datastore-register-vm.png)

4. Wybierz centrum danych, folder i pulę klastra/zasobów, w której ma zostać zarejestrowana maszyna wirtualna.

4. Powtórz kroki 3 i 4 dla wszystkich maszyn wirtualnych i szablonów maszyn wirtualnych.

5. Przejdź do folderu zawierającego pliki ISO.  Wybierz pliki ISO, a następnie wybierz **pozycję Kopiuj, aby** skopiować pliki do folderu w magazynie danych vSAN.

Maszyny wirtualne i szablony maszyn wirtualnych są teraz dostępne w centrum vCenter w chmurze prywatnej. Te maszyny wirtualne muszą zostać przeniesione z magazynu danych NFS do magazynu danych vSAN przed ich włączeniem. Można użyć opcji **vMotion magazynu** i wybrać magazyn danych vSAN jako miejsce docelowe dla maszyn wirtualnych.

Szablony maszyn wirtualnych muszą być sklonowane z magazynu danych NFS maszyny wirtualnej systemu Linux do magazynu danych vSAN.

### <a name="clean-up-your-linux-virtual-machine"></a>Oczyść maszynę wirtualną Systemu Linux

Po skopiowaniu wszystkich danych do chmury prywatnej można usunąć magazyn danych NFS z chmury prywatnej:

1. Upewnij się, że wszystkie maszyny wirtualne i szablony są przenoszone i klonowane do magazynu danych vSAN.

2. Usuń z zapasów wszystkie szablony maszyn wirtualnych z magazynu plików NFS.

3. Odinstaluj magazyn danych maszyny wirtualnej systemu Linux z witryny vCenter w chmurze prywatnej.

4. Usuń maszynę wirtualną i dysk zarządzany z platformy Azure.

5. Jeśli nie chcesz przechowywać danych, które zostały przeniesione przez pole danych na koncie magazynu, usuń konto magazynu platformy Azure.  
    


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [polu danych](../databox/data-box-overview.md).
* Dowiedz się więcej o różnych [opcjach migracji obciążeń do chmury prywatnej](migrate-workloads.md).
