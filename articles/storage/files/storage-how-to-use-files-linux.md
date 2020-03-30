---
title: Korzystanie z usługi Azure Files z systemem Linux | Dokumenty firmy Microsoft
description: Dowiedz się, jak zainstalować udział plików platformy Azure za pomocą SMB w systemie Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2dc78c25c2cf63a510b9451c8d694795cd8a91eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060944"
---
# <a name="use-azure-files-with-linux"></a>Używanie usługi Azure Files z systemem Linux
[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. Udziały plików platformy Azure mogą być montowane w dystrybucjach systemu Linux przy użyciu [klienta jądra SMB](https://wiki.samba.org/index.php/LinuxCIFS). W tym artykule przedstawiono dwa sposoby instalowania `mount` udziału plików platformy Azure: na `/etc/fstab`żądanie za pomocą polecenia i przy rozruchu, tworząc wpis w programie .

Zalecany sposób instalacji udziału plików platformy Azure w systemie Linux jest przy użyciu SMB 3.0. Domyślnie usługa Azure Files wymaga szyfrowania podczas przesyłania, które jest obsługiwane tylko przez SMB 3.0. Usługa Azure Files obsługuje również protokół SMB 2.1, który nie obsługuje szyfrowania podczas przesyłania, ale ze względów bezpieczeństwa nie można zainstalować udziałów plików platformy Azure z protokołem SMB 2.1 z innego regionu platformy Azure lub lokalnie. Chyba że aplikacja wymaga specjalnie SMB 2.1, nie ma powodu, aby go używać, ponieważ najbardziej popularne, niedawno wydane dystrybucje Linuksa obsługują SMB 3.0:  

| | SMB 2.1 <br>(Instalacje na maszynach wirtualnych w tym samym regionie platformy Azure) | SMB 3.0 <br>(Wierzchowce z pomieszczeń i międzyregionów) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7,5+ |
| CentOS | 7+ |  7,5+ |
| Debian | 8+ | Ponad 10 |
| openSUSE | 13.2+ | 42,3+ |
| SUSE Linux Enterprise Server | 12+ | 12 sp3+ |

Jeśli używasz dystrybucji Linuksa niewymienione w powyższej tabeli, możesz sprawdzić, czy twoja dystrybucja Linuksa obsługuje SMB 3.0 z szyfrowaniem, sprawdzając wersję jądra Linuksa. SMB 3.0 z szyfrowaniem został dodany do jądra Linuksa w wersji 4.11. Polecenie `uname` zwróci używaną wersję jądra Linuksa:

```bash
uname -r
```

## <a name="prerequisites"></a>Wymagania wstępne
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Upewnij się, że pakiet cifs-utils jest zainstalowany.**  
    Pakiet cifs-utils można zainstalować za pomocą menedżera pakietów w wybranej dystrybucji Linuksa. 

    W dystrybucjach opartych na **Ubuntu** `apt` i **Debianie** użyj menedżera pakietów:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    Na **Fedorze**, **Red Hat Enterprise Linux 8 +** i **CentOS 8 +** użyj menedżera `dnf` pakietów:

    ```bash
    sudo dnf install cifs-utils
    ```

    W starszych wersjach **red hat enterprise** `yum` linux i **centos**, użyj menedżera pakietów:

    ```bash
    sudo yum install cifs-utils 
    ```

    Przy **openSUSE**użyj `zypper` menedżera pakietów:

    ```bash
    sudo zypper install cifs-utils
    ```

    W przypadku innych dystrybucji użyj odpowiedniego menedżera pakietów lub [skompiluj je ze źródła](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **Najnowsza wersja interfejsu wiersza polecenia platformy Azure (CLI).** Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) i wybieranie systemu operacyjnego. Jeśli wolisz używać modułu programu Azure PowerShell w programie PowerShell 6+, możesz jednak poniższe instrukcje są prezentowane dla interfejsu wiersza polecenia platformy Azure.

* **Upewnij się, że port 445 jest otwarty:** SMB komunikuje się za pomocą portu TCP 445 - sprawdź, czy zapora nie blokuje portów TCP 445 z komputera klienckiego.  Zastąp **<>grupy zasobów** i<>konta **magazynu**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Jeśli połączenie zakończyło się pomyślnie, powinien zostać wyświetlony coś podobnego do następującego wyjścia:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Jeśli nie możesz otworzyć portu 445 w sieci firmowej lub nie możesz tego zrobić przez usługodawcę internetowego, możesz użyć połączenia VPN lub usługi ExpressRoute do obejść port 445. Aby uzyskać więcej informacji, zobacz [Zagadnienia dotyczące sieci bezpośredniego dostępu do udziału plików platformy Azure](storage-files-networking-overview.md)..

## <a name="mounting-azure-file-share"></a>Instalowanie udziału plików platformy Azure
Aby użyć udziału plików platformy Azure z dystrybucją systemu Linux, należy utworzyć katalog, który będzie służył jako punkt instalacji dla udziału plików platformy Azure. Punkt instalacji można utworzyć w dowolnym miejscu w systemie Linux, ale jest to wspólna konwencja, aby utworzyć to w obszarze /mnt. Po punkcie instalacji użyj `mount` polecenia, aby uzyskać dostęp do udziału plików platformy Azure.

W razie potrzeby można zainstalować ten sam udział plików platformy Azure w wielu punktach instalacji.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Instalowanie udziału plików platformy Azure na żądanie`mount`
1. **Utwórz folder dla punktu instalacji**: Zamień `<your-resource-group>` `<your-storage-account>`i `<your-file-share>` odpowiednie informacje dla danego środowiska:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Użyj polecenia instalacji, aby zainstalować udział plików platformy Azure**. W poniższym przykładzie domyślne uprawnienia do lokalnego pliku i folderu systemu Linux 0755, co oznacza odczyt, zapis i wykonanie dla właściciela (na podstawie właściciela systemu Linux pliku/katalogu), odczytu i wykonania dla użytkowników w grupie właścicieli oraz odczytu i wykonania dla innych osób w systemie. Za pomocą `uid` opcji `gid` i instalacji można ustawić identyfikator użytkownika i identyfikator grupy dla instalacji. Można również `dir_mode` użyć `file_mode` i ustawić uprawnienia niestandardowe zgodnie z potrzebami. Aby uzyskać więcej informacji na temat ustawiania uprawnień, zobacz [notacja numeryczna UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) w Wikipedii. 

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > Powyższe mocowanie polecenia montuje się z SMB 3.0. Jeśli twoja dystrybucja systemu Linux nie obsługuje SMB 3.0 z szyfrowaniem lub jeśli obsługuje tylko SMB 2.1, można zainstalować tylko z maszyny Wirtualnej platformy Azure w tym samym regionie co konto magazynu. Aby zainstalować udział plików platformy Azure w dystrybucji systemu Linux, która nie obsługuje SMB 3.0 z szyfrowaniem, należy [wyłączyć szyfrowanie podczas przesyłania dla konta magazynu](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Po zakończeniu korzystania z udziału plików platformy `sudo umount $mntPath` Azure, można użyć do odinstalowywać udział.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Tworzenie trwałego punktu instalacji dla udziału plików platformy Azure`/etc/fstab`
1. **Utwórz folder dla punktu instalacji:** Folder dla punktu instalacji można utworzyć w dowolnym miejscu w systemie plików, ale jest to wspólna konwencja, aby utworzyć to w /mnt. Na przykład następujące polecenie tworzy nowy katalog, zamień `<your-resource-group>`i `<your-storage-account>` `<your-file-share>` z odpowiednimi informacjami dla danego środowiska:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Utwórz plik poświadczeń do przechowywania nazwy użytkownika (nazwy konta magazynu) i hasła (klucza konta magazynu) dla udziału plików.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **Zmień uprawnienia do pliku poświadczeń, aby tylko katalog główny mógł odczytywać lub modyfikować plik hasła.** Ponieważ klucz konta magazynu jest zasadniczo superadministratorem hasła dla konta magazynu, ustawienie uprawnień do pliku w taki sposób, aby dostęp tylko root może uzyskać, jest ważne, aby użytkownicy o niższych uprawnieniach nie mogli pobrać klucza konta magazynu.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Użyj następującego polecenia, aby dołączyć `/etc/fstab`następujący wiersz do: **W poniższym przykładzie lokalne uprawnienia do pliku i folderu systemu Linux domyślnie 0755, co oznacza odczyt, zapis i wykonanie dla właściciela (na podstawie pliku/katalogu właściciela Linuksa), odczytu i wykonania dla użytkowników w grupie właścicieli oraz odczytu i wykonania dla innych osób w systemie. Za pomocą `uid` opcji `gid` i instalacji można ustawić identyfikator użytkownika i identyfikator grupy dla instalacji. Można również `dir_mode` użyć `file_mode` i ustawić uprawnienia niestandardowe zgodnie z potrzebami. Aby uzyskać więcej informacji na temat ustawiania uprawnień, zobacz [notacja numeryczna UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) w Wikipedii.

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > Powyższe mocowanie polecenia montuje się z SMB 3.0. Jeśli twoja dystrybucja systemu Linux nie obsługuje SMB 3.0 z szyfrowaniem lub jeśli obsługuje tylko SMB 2.1, można zainstalować tylko z maszyny Wirtualnej platformy Azure w tym samym regionie co konto magazynu. Aby zainstalować udział plików platformy Azure w dystrybucji systemu Linux, która nie obsługuje SMB 3.0 z szyfrowaniem, należy [wyłączyć szyfrowanie podczas przesyłania dla konta magazynu](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="securing-linux"></a>Zabezpieczanie linuksa
Aby zainstalować udział plików platformy Azure w systemie Linux, port 445 musi być dostępny. Wiele organizacji blokuje port 445 z powodu zagrożeń bezpieczeństwa związanych z protokołem SMB 1. SMB 1, znany również jako CIFS (Common Internet File System), jest starszy protokół systemu plików zawarte z wielu dystrybucji Linuksa. Protokół SMB 1 jest nieaktualny, nieefektywny i, co najważniejsze, niezabezpieczony. Dobrą wiadomością jest to, że usługa Azure Files nie obsługuje SMB 1, a począwszy od jądra Linuksa w wersji 4.18, Linux umożliwia wyłączenie SMB 1. Zawsze [zalecamy](https://aka.ms/stopusingsmb1) wyłączenie SMB 1 na klientach systemu Linux przed użyciem udziałów plików SMB w produkcji.

Począwszy od jądra Linuksa 4.18, `cifs` moduł jądra SMB, zwany ze względu na starsze, ujawnia nowy `disable_legacy_dialects`parametr modułu (często określany jako *parm* przez różne zewnętrzne dokumenty), zwany . Chociaż wprowadzono jądro Linuksa 4.18, niektórzy dostawcy backported tej zmiany do starszych jąder, które obsługują. Dla wygody w poniższej tabeli opisano dostępność tego parametru modułu w typowych dystrybucjach systemu Linux.

| Dystrybucja | Można wyłączyć SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | Nie |
| Ubuntu 18.04 | Tak |
| Ubuntu 19.04+ | Tak |
| Debian 8-9 | Nie |
| Debian 10+ | Tak |
| Fedora 29+ | Tak |
| CentOS 7 | Nie | 
| CentOS 8+ | Tak |
| Red Hat Enterprise Linux 6.x-7.x | Nie |
| Red Hat Enterprise Linux 8+ | Tak |
| openSUSE Leap 15.0 | Nie |
| openSUSE Leap 15.1+ | Tak |
| openSUSE Tumbleweed | Tak |
| SUSE Linux Enterprise 11.x-12.x | Nie |
| SUSE Linux Enterprise 15 | Nie |
| SUSE Linux Enterprise 15.1 | Nie |

Możesz sprawdzić, czy twoja dystrybucja `disable_legacy_dialects` Linuksa obsługuje parametr modułu za pomocą następującego polecenia.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

To polecenie powinno wysuć następujący komunikat:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Przed wyłączeniem SMB 1 należy sprawdzić, czy moduł SMB nie jest aktualnie załadowany do systemu (dzieje się tak automatycznie, jeśli został zamontowany udział SMB). Można to zrobić za pomocą następującego polecenia, które nie powinno niczego wyprowadzać, jeśli SMB nie jest ładowany:

```bash
lsmod | grep cifs
```

Aby zwolnić moduł, najpierw odinstaluj wszystkie `umount` udziały SMB (używając polecenia opisanego powyżej). Wszystkie zamontowane udziały SMB w systemie można zidentyfikować za pomocą następującego polecenia:

```bash
mount | grep cifs
```

Po odinstalowyniu wszystkich udziałów plików SMB można bezpiecznie zwolnić moduł. W tym celu możesz użyć polecenia `modprobe`:

```bash
sudo modprobe -r cifs
```

Moduł można ładować ręcznie z rozładowanym `modprobe` modułem SMB 1 za pomocą polecenia:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Na koniec można sprawdzić, czy moduł SMB został załadowany parametrem, patrząc na załadowane parametry w: `/sys/module/cifs/parameters`

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Aby trwale wyłączyć SMB 1 w dystrybucjach opartych na Ubuntu i Debianie, musisz utworzyć nowy plik `/etc/modprobe.d/local.conf` (jeśli nie masz jeszcze niestandardowych opcji dla innych modułów) wywoływany z tym ustawieniem. Można to zrobić za pomocą następującego polecenia:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Można sprawdzić, czy to zadziałało, ładując moduł SMB:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>Opinia
Użytkownicy Linuksa, chcemy usłyszeć od Ciebie!

Grupa użytkowników usługi Azure Files for Linux udostępnia forum do udostępniania opinii podczas oceniania i wdrażania magazynu plików w systemie Linux. Wyślij wiadomość e-mail [do użytkowników systemu Azure Files linux,](mailto:azurefiles@microsoft.com) aby dołączyć do grupy użytkowników.

## <a name="next-steps"></a>Następne kroki
Poniższe linki umożliwiają uzyskanie dodatkowych informacji na temat usługi Azure Files:

* [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
* [Najczęściej zadawane pytania](../storage-files-faq.md)
* [Rozwiązywanie problemów](storage-troubleshoot-linux-file-connection-problems.md)
