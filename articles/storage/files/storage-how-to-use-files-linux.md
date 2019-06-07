---
title: Używać usługi Azure Files z systemem Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować udział plików platformy Azure za pośrednictwem protokołu SMB w systemie Linux.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 375d0de60b916becc8e86a1e33cf4ed46f12c077
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754822"
---
# <a name="use-azure-files-with-linux"></a>Używanie usługi Azure Files z systemem Linux

[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. Udziały plików platformy Azure można instalować w dystrybucjach systemu Linux przy użyciu [klienta jądra SMB](https://wiki.samba.org/index.php/LinuxCIFS). W tym artykule przedstawiono dwa sposoby instalowania udziału plików platformy Azure: na żądanie przy użyciu `mount` polecenia oraz na rozruch, tworząc wpis w `/etc/fstab`.

> [!NOTE]  
> Aby móc zainstalować udział plików platformy Azure poza regionem platformy Azure, z którym jest on hostowany, na przykład lokalnie lub w innym regionie platformy Azure, system operacyjny musi obsługiwać funkcje szyfrowania protokołu SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Wymagania wstępne dotyczące instalowania udziału plików platformy Azure z systemem Linux i pakiet cifs utils
<a id="smb-client-reqs"></a>

* **Istniejącego konta i pliku udziału magazynu platformy Azure**: Aby można było ukończyć w tym artykule, musisz mieć konto magazynu i udział plików. Jeśli nie utworzono jeszcze jeden, zobacz jeden z naszych przewodników Szybki Start dotyczącą tego tematu: [Tworzenie udziałów plików — interfejs wiersza polecenia](storage-how-to-use-files-cli.md).

* **Nazwa konta magazynu i klucz usługi** konieczne będzie nazwa konta magazynu i klucz do ukończenia tego artykułu. Jeśli utworzono ją przy użyciu interfejsu wiersza polecenia Przewodnik Szybki Start, należy je, a w przeciwnym razie zapoznaj się tego przewodnika Szybki Start interfejs wiersza polecenia, który został połączony wcześniej, aby dowiedzieć się, jak pobrać klucz konta magazynu.

* **Wybierz dystrybucję systemu Linux do swoich potrzeb instalowania.**  
      Usługa pliki systemu Azure mogą być instalowane za pośrednictwem protokołu SMB 2.1, jak i protokołu SMB 3.0. W przypadku połączeń pochodzących od klientów w środowisku lokalnym lub w innych regionach platformy Azure należy użyć protokołu SMB 3.0; Usługa Azure Files odrzuci SMB 2.1 (lub SMB 3.0 bez szyfrowania). Jeśli uzyskujesz dostęp do udziału plików platformy Azure na maszynie wirtualnej w tym samym regionie platformy Azure, użytkownik może uzyskiwać dostęp do Twojego udziału plików przy użyciu protokołu SMB 2.1, jeśli i tylko wtedy, gdy, *Wymagany bezpieczny transfer* jest wyłączona w przypadku konto magazynu hostujące udział plików platformy Azure. Zawsze zalecamy Wymaganie bezpiecznego transferu i używania tylko protokołu SMB 3.0 za pomocą szyfrowania.

    Obsługa szyfrowania protokołu SMB 3.0 została wprowadzona w systemie Linux wersja jądra 4.11 i został backported do starszych wersji jądra dla popularnych dystrybucji systemu Linux. W chwili opublikowania tego dokumentu poniższe dystrybucje z poziomu galerii Azure obsługuje opcję instalowania określonego w nagłówki tabeli. 

* **Minimalna zalecana wersji przy użyciu odpowiedniej funkcji instalacji (wersja protokołu SMB 2.1 vs wersja protokołu SMB 3.0)**    

    |   | SMB 2.1 <br>(Instaluje na maszynach wirtualnych w tym samym regionie platformy Azure) | SMB 3.0 <br>(Instaluje z lokalnie i między regionami) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3+ |
    | SUSE Linux Enterprise Server | 12 | 12 SP3+ |

    Jeśli danej dystrybucji systemu Linux nie ma na liście, można sprawdzić wersji jądra systemu Linux za pomocą następującego polecenia:

   ```bash
   uname -r
   ```

* <a id="install-cifs-utils"></a>**Pakiet cifs utils jest zainstalowany.**  
    Można zainstalować pakiet cifs utils przy użyciu Menedżera pakietów w wybranej dystrybucji systemu Linux. 

    Na **Ubuntu** i **oparta na rozwiązaniu Debian** dystrybucji, użyj `apt-get` Menedżera pakietów:

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    Na **RHEL** i **CentOS**, użyj `yum` Menedżera pakietów:

    ```bash
    sudo yum install cifs-utils
    ```

    Na **openSUSE**, użyj `zypper` Menedżera pakietów:

    ```bash
    sudo zypper install cifs-utils
    ```

    Inne dystrybucje używać Menedżera odpowiedniego pakietu lub [kompilacji ze źródła](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **Podejmij decyzję uprawnień pliku lub katalogu zainstalowanego udziału**: W przykładach poniżej uprawnienia `0777` jest używany, aby dać odczytu, zapisu i wykonywania uprawnień dla wszystkich użytkowników. Można zastąpić go z innymi [uprawnienia chmod](https://en.wikipedia.org/wiki/Chmod) zgodnie z potrzebami, mimo że oznacza to, potencjalnie ograniczanie dostępu. Jeśli używasz innych uprawnień, należy rozważyć również, aby zachować dostęp do grup lokalnych w wybranym przy użyciu identyfikatorów uid i gid.

> [!NOTE]
> Jeśli nie przypisuj jawne uprawnienia plików i katalogów o dir_mode i file_mode, zostaną domyślnie 0755.

* **Otwarty port 445**: Protokół SMB komunikuje się za pośrednictwem portu TCP 445. Upewnij się, że Twoja zapora nie blokuje portów TCP 445 z komputera klienckiego.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Instalowanie plików platformy Azure udział na żądanie przy użyciu `mount`

1. **[Zainstaluj pakiet cifs utils dla dystrybucji systemu Linux](#install-cifs-utils)** .

1. **Utwórz folder na potrzeby punktu instalacji**: Folder dla punktu instalacji można utworzyć dowolne miejsce w systemie plików, ale jest typową Konwencją do utworzenia nowego folderu. Na przykład następujące polecenie tworzy nowy katalog, Zastąp **< nazwa_konta_magazynu >** i **< file_share_name >** odpowiednimi informacjami dla danego środowiska:

    ```bash
    mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Instalowanie udziału plików platformy Azure za pomocą polecenia instalacji**: Pamiętaj, aby zastąpić **< nazwa_konta_magazynu >** , **< nazwa_udziału >** , **< smb_version >** , **< klucz_konta_magazynu >** , i **< mount_point >** odpowiednimi informacjami dla danego środowiska. Jeśli danej dystrybucji systemu Linux obsługuje protokół SMB 3.0 za pomocą szyfrowania (zobacz [wymagania dotyczące klienta SMB zrozumieć](#smb-client-reqs) Aby uzyskać więcej informacji), użyj **3.0** dla **< smb_version >** . Dystrybucje systemu Linux, które nie obsługują przy użyciu szyfrowania protokołu SMB 3.0, należy użyć **2.1** dla **< smb_version >** . Udział plików platformy Azure można instalować tylko poza region platformy Azure (w tym w środowisku lokalnym lub w innym regionie platformy Azure) z protokołem SMB 3.0. Jeśli chcesz możesz zmienić uprawnienia plików i katalogów zainstalowanego udziału, ale może oznaczać to ograniczenie dostępu.

    ```bash
    sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> <mount_point> -o vers=<smb_version>,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Po zakończeniu przy użyciu udziału plików platformy Azure, możesz użyć `sudo umount <mount_point>` odinstalował udziału.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Utwórz punkt instalacji trwałego udziału plików platformy Azure za pomocą `/etc/fstab`

1. **[Zainstaluj pakiet cifs utils dla dystrybucji systemu Linux](#install-cifs-utils)** .

1. **Utwórz folder na potrzeby punktu instalacji**: Folder dla punktu instalacji można utworzyć dowolne miejsce w systemie plików, ale jest typową Konwencją do utworzenia nowego folderu. Wszędzie tam, gdzie tworzysz, należy zwrócić uwagę na bezwzględną ścieżkę folderu. Na przykład następujące polecenie tworzy nowy katalog, Zastąp **< nazwa_konta_magazynu >** i **< file_share_name >** odpowiednimi informacjami dla danego środowiska.

    ```bash
    sudo mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Utwórz plik poświadczeń do przechowywania nazwy użytkownika (nazwę konta magazynu) i hasło (klucz konta magazynu) dla udziału plików.** Zastąp **< nazwa_konta_magazynu >** i **< klucz_konta_magazynu >** odpowiednimi informacjami dla danego środowiska.

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
    fi
    if [ ! -f "/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred" ]; then
    sudo bash -c 'echo "username=<STORAGE ACCOUNT NAME>" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    sudo bash -c 'echo "password=7wRbLU5ea4mgc<DRIVE LETTER>PIpUCNcuG9gk2W4S2tv7p0cTm62wXTK<DRIVE LETTER>CgJlBJPKYc4VMnwhyQd<DRIVE LETTER>UT<DRIVE LETTER>yR5/RtEHyT/EHtg2Q==" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    fi
    ```

1. **Zmień uprawnienia do pliku poświadczeń, aby tylko główny może odczytać lub zmodyfikować pliku hasła.** Ponieważ klucz konta magazynu jest zasadniczo hasło właścicielami administratora dla konta magazynu, ustawienie uprawnień dla pliku, który w taki sposób, że tylko główny mogą uzyskiwać dostęp do ważne jest, aby niższe uprawnienia użytkowników nie można pobrać klucza konta magazynu.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage_account_name>.cred
    ```

1. **Użyj następującego polecenia, aby dołączyć następujący wiersz do `/etc/fstab`** : Pamiętaj, aby zastąpić **< nazwa_konta_magazynu >** , **< nazwa_udziału >** , **< smb_version >** , i **< mount_point >** odpowiednimi informacjami dla danego środowiska. Jeśli danej dystrybucji systemu Linux obsługuje protokół SMB 3.0 za pomocą szyfrowania (zobacz [wymagania dotyczące klienta SMB zrozumieć](#smb-client-reqs) Aby uzyskać więcej informacji), użyj **3.0** dla **< smb_version >** . Dystrybucje systemu Linux, które nie obsługują przy użyciu szyfrowania protokołu SMB 3.0, należy użyć **2.1** dla **< smb_version >** . Udział plików platformy Azure można instalować tylko poza region platformy Azure (w tym w środowisku lokalnym lub w innym regionie platformy Azure) z protokołem SMB 3.0.

    ```bash
    sudo bash -c 'echo "//<STORAGE ACCOUNT NAME>.file.core.windows.net/<FILE SHARE NAME> /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME> cifs nofail,vers=3.0,credentials=/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'

    sudo mount /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME>
    ```

> [!Note]  
> Możesz użyć `sudo mount -a` Aby zainstalować udział plików platformy Azure po wprowadzeniu zmian w `/etc/fstab` zamiast ponownego uruchamiania.

## <a name="feedback"></a>Opinia

Użytkownicy systemu Linux, chcemy poznać Twoją opinię!

Usługi Azure Files dla grupy użytkowników systemu Linux udostępnia forum opinią, jak ocenić i przyjmuje magazynu plików w systemie Linux. Adres e-mail [użytkowników systemu Linux plików Azure](mailto:azurefileslinuxusers@microsoft.com) do dołączenia do grupy użytkowników.

## <a name="next-steps"></a>Kolejne kroki

Poniższe linki umożliwiają uzyskanie dodatkowych informacji na temat usługi Azure Files:

* [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
* [Często zadawane pytania](../storage-files-faq.md)
* [Rozwiązywanie problemów](storage-troubleshoot-linux-file-connection-problems.md)
