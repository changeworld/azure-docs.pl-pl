---
title: Używanie Azure Files z systemem Linux | Microsoft Docs
description: Dowiedz się, jak zainstalować udział plików platformy Azure za pośrednictwem protokołu SMB w systemie Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9674df346ff0b5c046df26fe85bf7ba0bbb802e6
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900305"
---
# <a name="use-azure-files-with-linux"></a>Używanie usługi Azure Files z systemem Linux

[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. Udziały plików platformy Azure można instalować w dystrybucjach systemu Linux przy użyciu [klienta jądra SMB](https://wiki.samba.org/index.php/LinuxCIFS). W tym artykule przedstawiono dwa sposoby instalowania udziału plików platformy Azure: na żądanie z `mount` poleceniem i przy rozruchu, tworząc wpis w. `/etc/fstab`

> [!NOTE]  
> Aby można było zainstalować udział plików platformy Azure poza regionem świadczenia usługi Azure, w którym jest on hostowany, na przykład lokalnie lub w innym regionie świadczenia usługi Azure, system operacyjny musi obsługiwać funkcje szyfrowania SMB 3,0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Wymagania wstępne dotyczące instalowania udziału plików platformy Azure z systemem Linux i pakietem CIFS-narzędzia
<a id="smb-client-reqs"></a>

* **Istniejące konto usługi Azure Storage i udział plików**: Aby ukończyć ten artykuł, musisz mieć konto magazynu i udział plików. Jeśli jeszcze tego nie zrobiono, zapoznaj się z jednym z przewodników szybki start dotyczących tematu: [Utwórz udziały plików — interfejs wiersza polecenia](storage-how-to-use-files-cli.md).

* **Nazwa i klucz konta magazynu** Aby można było ukończyć ten artykuł, potrzebna jest nazwa i klucz konta magazynu. Jeśli został utworzony za pomocą przewodnika Szybki start interfejsu wiersza polecenia, należy go już sprawdzić. w przeciwnym razie zapoznaj się z przewodnikiem Szybki start interfejsu wiersza polecenia, który został połączony wcześniej, aby dowiedzieć się, jak pobrać klucz konta magazynu.

* **Wybierz dystrybucję systemu Linux odpowiednio do potrzeb związanych z montażem.**  
      Azure Files można zainstalować za pośrednictwem protokołów SMB 2,1 i SMB 3,0. W przypadku połączeń pochodzących z klientów lokalnych lub w innych regionach świadczenia usługi Azure należy użyć protokołu SMB 3,0; Azure Files spowoduje odrzucenie protokołu SMB 2,1 (lub protokołu SMB 3,0 bez szyfrowania). Jeśli uzyskujesz dostęp do udziału plików platformy Azure z maszyny wirtualnej w tym samym regionie świadczenia usługi Azure, możesz uzyskać dostęp do udziału plików przy użyciu protokołu SMB 2,1, jeśli i tylko wtedy, gdy *wymagany bezpieczny transfer* jest wyłączony dla konta magazynu obsługującego udział plików platformy Azure. Zawsze zalecamy wymaganie bezpiecznego transferu i użycie tylko protokołu SMB 3,0 z szyfrowaniem.

    Obsługa szyfrowania SMB 3,0 została wprowadzona w jądrze systemu Linux w wersji 4,11 i została zaimportowana do starszych wersji jądra dla popularnych dystrybucji systemu Linux. W momencie publikacji tego dokumentu następujące dystrybucje z poziomu galerii platformy Azure obsługują opcję instalowania określoną w nagłówkach tabeli. 

### <a name="minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30"></a>Minimalna zalecana wersja z odpowiednimi możliwościami instalacji (SMB w wersji 2,1 vs SMB wersja 3,0)

|   | SMB 2.1 <br>(Instalacja na maszynach wirtualnych w ramach tego samego regionu platformy Azure) | SMB 3.0 <br>(Instalacje z poziomu lokalnego i obejmującego wiele regionów) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04 + | 16.04 + |
| RHEL | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 9 +<sup>*</sup> |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

<sup>*</sup>Może być wymagane użycie nieportowego jądra. Instalację można wykonać za pomocą polecenia `sudo apt-get install linux-image-cloud-amd64`.

Jeśli dystrybucji systemu Linux nie ma na liście, możesz sprawdzić, czy w systemie Linux jest dostępna wersja jądra z następującego polecenia:

```bash
uname -r
```

* <a id="install-cifs-utils"></a>**Pakiet CIFS-narzędzia jest zainstalowany.**  
    Pakiet CIFS-narzędzia można zainstalować przy użyciu Menedżera pakietów na wybranej dystrybucji systemu Linux. 

    W przypadku `apt-get` dystrybucji opartych na Ubuntu i **Debian** należy użyć Menedżera pakietów:

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    W systemach **RHEL** i **CentOS**Użyj `yum` Menedżera pakietów:

    ```bash
    sudo yum install cifs-utils
    ```

    W systemie **openSUSE**Użyj `zypper` Menedżera pakietów:

    ```bash
    sudo zypper install cifs-utils
    ```

    W przypadku innych dystrybucji Użyj odpowiedniego Menedżera pakietów lub [Skompiluj ze źródła](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **Wybierz uprawnienia katalogu/pliku dla zainstalowanego udziału**: W poniższych przykładach uprawnienie `0777` służy do udzielania uprawnień Odczyt, zapis i wykonywanie wszystkim użytkownikom. Możesz zamienić ją na inne [uprawnienia chmod](https://en.wikipedia.org/wiki/Chmod) , ale oznacza to, że będzie to potencjalnie ograniczyć dostęp. W przypadku korzystania z innych uprawnień należy wziąć pod uwagę także użycie identyfikatorów UID i GID, aby zachować dostęp dla wybranych użytkowników i grup lokalnych.

> [!NOTE]
> Jeśli nie przypiszesz jawnie uprawnień katalogów i plików z dir_mode i file_mode, będą one domyślnie 0755.

* **Otwarty port 445**: Protokół SMB komunikuje się za pośrednictwem portu TCP 445. Upewnij się, że Twoja zapora nie blokuje portów TCP 445 z komputera klienckiego.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Zainstaluj udział plików platformy Azure na żądanie z`mount`

1. **[Zainstaluj pakiet CIFS-narzędzia dla dystrybucji systemu Linux](#install-cifs-utils)** .

1. **Utwórz folder dla punktu instalacji**: Folder dla punktu instalacji można utworzyć w dowolnym miejscu w systemie plików, ale jest to typowa Konwencja do utworzenia tego elementu w ramach nowego folderu. Na przykład następujące polecenie tworzy nowy katalog, zastępuje **< storage_account_name >** i **< file_share_name >** z odpowiednimi informacjami dla środowiska:

    ```bash
    mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Aby zainstalować udział plików platformy Azure, użyj polecenia mount**: Pamiętaj, aby zastąpić **< storage_account_name >** , **< share_name >** , **< smb_version >** , **< storage_account_key >** i **< mount_point >** z odpowiednimi informacjami dla Twojego naturalne. Jeśli dystrybucja systemu Linux obsługuje protokół SMB 3,0 z szyfrowaniem (więcej informacji znajduje się w temacie Informacje o [wymaganiach klienta SMB](#smb-client-reqs) ), należy użyć **3,0** dla **< smb_version >** . W przypadku dystrybucji systemu Linux, które nie obsługują protokołu SMB 3,0 z szyfrowaniem, należy użyć **2,1** dla **< smb_version >** . Udział plików platformy Azure można zainstalować tylko poza regionem świadczenia usługi Azure (w tym lokalnym lub w innym regionie świadczenia usługi Azure) przy użyciu protokołu SMB 3,0. Jeśli chcesz, możesz zmienić uprawnienia katalogów i plików w zainstalowanym udziale, ale oznacza to ograniczenie dostępu.

    ```bash
    sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> <mount_point> -o vers=<smb_version>,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Po zakończeniu korzystania z udziału plików platformy Azure Możesz użyć `sudo umount <mount_point>` programu do odinstalowania udziału.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Utwórz trwały punkt instalacji dla udziału plików platformy Azure`/etc/fstab`

1. **[Zainstaluj pakiet CIFS-narzędzia dla dystrybucji systemu Linux](#install-cifs-utils)** .

1. **Utwórz folder dla punktu instalacji**: Folder dla punktu instalacji można utworzyć w dowolnym miejscu w systemie plików, ale jest to typowa Konwencja do utworzenia tego elementu w ramach nowego folderu. Niezależnie od tego, gdzie tworzysz tę funkcję, należy zanotować ścieżkę bezwzględną folderu. Na przykład następujące polecenie tworzy nowy katalog, zastępuje **< storage_account_name >** i **< file_share_name >** z odpowiednimi informacjami dla danego środowiska.

    ```bash
    sudo mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Utwórz plik poświadczeń, aby zapisać nazwę użytkownika (nazwę konta magazynu) i hasło (klucz konta magazynu) dla udziału plików.** Zastąp **< storage_account_name >** i **< storage_account_key >** z odpowiednimi informacjami dla danego środowiska.

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
    fi
    if [ ! -f "/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred" ]; then
    sudo bash -c 'echo "username=<STORAGE ACCOUNT NAME>" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    sudo bash -c 'echo "password=7wRbLU5ea4mgc<DRIVE LETTER>PIpUCNcuG9gk2W4S2tv7p0cTm62wXTK<DRIVE LETTER>CgJlBJPKYc4VMnwhyQd<DRIVE LETTER>UT<DRIVE LETTER>yR5/RtEHyT/EHtg2Q==" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    fi
    ```

1. **Zmień uprawnienia do pliku poświadczeń, aby tylko element główny mógł odczytać lub zmodyfikować plik hasła.** Ponieważ klucz konta magazynu jest zasadniczo hasłem administratora hasła dla konta magazynu, ustawienie uprawnień do pliku, tak aby tylko dostęp do katalogu głównego miało znaczenie, jest ważne, aby użytkownicy z niższymi uprawnieniami nie mogli pobrać klucza konta magazynu.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage_account_name>.cred
    ```

1. **Użyj poniższego polecenia, aby dołączyć następujący wiersz do `/etc/fstab`** : Pamiętaj, aby zastąpić **< storage_account_name >** , **< share_name >** , **< smb_version >** i **< mount_point >** z odpowiednimi informacjami dla danego środowiska. Jeśli dystrybucja systemu Linux obsługuje protokół SMB 3,0 z szyfrowaniem (więcej informacji znajduje się w temacie Informacje o [wymaganiach klienta SMB](#smb-client-reqs) ), należy użyć **3,0** dla **< smb_version >** . W przypadku dystrybucji systemu Linux, które nie obsługują protokołu SMB 3,0 z szyfrowaniem, należy użyć **2,1** dla **< smb_version >** . Udział plików platformy Azure można zainstalować tylko poza regionem świadczenia usługi Azure (w tym lokalnym lub w innym regionie świadczenia usługi Azure) przy użyciu protokołu SMB 3,0.

    ```bash
    sudo bash -c 'echo "//<STORAGE ACCOUNT NAME>.file.core.windows.net/<FILE SHARE NAME> /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME> cifs _netdev,nofail,vers=3.0,credentials=/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'

    sudo mount /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME>
    ```

> [!Note]  
> Możesz użyć `sudo mount -a` , aby zainstalować udział plików platformy Azure po edycji `/etc/fstab` zamiast ponownie uruchomić.

## <a name="feedback"></a>Opinia

Użytkownicy systemu Linux chcemy poznać Twoją opinię!

Azure Files dla grupy użytkowników systemu Linux zawiera forum umożliwiające udostępnianie opinii podczas oceny i przyjmowanie magazynu plików w systemie Linux. Aby dołączać do grupy użytkowników, należy wysłać pocztą e-mail [Azure Files użytkowników systemu Linux](mailto:azurefileslinuxusers@microsoft.com) .

## <a name="next-steps"></a>Następne kroki

Poniższe linki umożliwiają uzyskanie dodatkowych informacji na temat usługi Azure Files:

* [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
* [Często zadawane pytania](../storage-files-faq.md)
* [Rozwiązywanie problemów](storage-troubleshoot-linux-file-connection-problems.md)
