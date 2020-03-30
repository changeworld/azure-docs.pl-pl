---
title: Rozwiązywanie problemów z plikami platformy Azure w systemie Linux | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z usługą Azure Files w systemie Linux
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 95e220102cba290664a32cb6bbebef881ae4ffde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159493"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Rozwiązywanie problemów z plikami platformy Azure w systemie Linux

W tym artykule wymieniono typowe problemy związane z usługą Azure Files podczas łączenia się z klientami systemu Linux. Zapewnia również możliwe przyczyny i rozwiązania tych problemów. 

Oprócz kroków rozwiązywania problemów w tym artykule można użyć [AzFileDiagnostics,](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) aby upewnić się, że klient systemu Linux ma poprawne wymagania wstępne. AzFileDiagnostics automatyzuje wykrywanie większości objawów wymienionych w tym artykule. Pomaga skonfigurować środowisko, aby uzyskać optymalną wydajność. Informacje te można również znaleźć w [narzędziu do rozwiązywania problemów z udziałami w usłudze Azure Files](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). Narzędzie do rozwiązywania problemów zawiera kroki ułatwiające rozwiązywanie problemów z łączeniem, mapowaniem i instalowaniem udziałów usługi Azure Files.

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Nie można połączyć się z udziałem plików platformy Azure ani go zainstalować

### <a name="cause"></a>Przyczyna

Najczęstsze przyczyny tego problemu to:

- Używasz niezgodnego klienta dystrybucji systemu Linux. Zaleca się użycie następujących dystrybucji systemu Linux do łączenia się z udziałem plików platformy Azure:

|   | SMB 2.1 <br>(Instalacje na maszynach wirtualnych w tym samym regionie platformy Azure) | SMB 3.0 <br>(Wierzchowce z lokalnego i międzyregionowego) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7,5+ |
| CentOS | 7+ |  7,5+ |
| Debian | 8+ |   |
| openSUSE | 13.2+ | 42,3+ |
| SUSE Linux Enterprise Server | 12 | 12 sp3+ |

- Narzędzia CIFS (cifs-utils) nie są zainstalowane na kliencie.
- Minimalna wersja SMB/CIFS, 2.1, nie jest zainstalowana na kliencie.
- Szyfrowanie SMB 3.0 nie jest obsługiwane na kliencie. Poprzednia tabela zawiera listę dystrybucji systemu Linux, które obsługują montaż z lokalnego i międzyregionowego przy użyciu szyfrowania. Inne dystrybucje wymagają jądra 4.11 i nowszych wersji.
- Próbujesz połączyć się z kontem magazynu za pomocą portu TCP 445, który nie jest obsługiwany.
- Próbujesz połączyć się z udziałem plików platformy Azure z maszyny Wirtualnej platformy Azure, a maszyna wirtualna nie znajduje się w tym samym regionie co konto magazynu.
- Jeśli ustawienie [wymagane bezpieczne przeniesienie]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) jest włączone na koncie magazynu, usługa Azure Files zezwala tylko na połączenia korzystające z technologii SMB 3.0 z szyfrowaniem.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj [narzędzia do rozwiązywania problemów z błędami montażu plików Platformy Azure w systemie Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). To narzędzie:

* Pomaga sprawdzić poprawność środowiska uruchomionego klienta.
* Wykrywa niezgodną konfigurację klienta, która może spowodować błąd dostępu dla usługi Azure Files.
* Daje nakazowe wskazówki dotyczące samonamkładania.
* Zbiera ślady diagnostyki.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Błąd instalacji(13): Odmowa uprawnień" podczas instalowania udziału plików platformy Azure

### <a name="cause-1-unencrypted-communication-channel"></a>Przyczyna 1: Niezaszyfrowany kanał komunikacyjny

Ze względów bezpieczeństwa połączenia z udziałami plików platformy Azure są blokowane, jeśli kanał komunikacyjny nie jest szyfrowany i jeśli próba połączenia nie pochodzi z tego samego centrum danych, w którym znajdują się udziały plików platformy Azure. Nieszyfrowane połączenia w tym samym centrum danych też mogą być blokowane, jeśli ustawienie [Wymagany bezpieczny transfer](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) jest włączone na koncie magazynu. Szyfrowany kanał komunikacyjny jest udostępniany tylko wtedy, gdy system operacyjny klienta użytkownika obsługuje szyfrowanie protokołu SMB.

Aby dowiedzieć się więcej, zobacz [Wymagania wstępne dotyczące instalowania udziału plików platformy Azure za pomocą systemu Linux i pakietu cifs utils](storage-how-to-use-files-linux.md#prerequisites). 

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

1. Połącz się z klientem, który obsługuje szyfrowanie SMB lub połączyć się z maszyny wirtualnej w tym samym centrum danych co konto magazynu platformy Azure, który jest używany dla udziału plików platformy Azure.
2. Sprawdź, czy wymagane ustawienie [Bezpiecznego transferu](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) jest wyłączone na koncie magazynu, jeśli klient nie obsługuje szyfrowania SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Przyczyna 2: Reguły sieci wirtualnej lub zapory są włączone na koncie magazynu 

Jeśli reguły sieci wirtualnej i zapory są skonfigurowane na koncie magazynu, dla ruchu sieciowego będzie następować odmowa dostępu, chyba że dostęp będzie dozwolony dla adresu IP klienta lub sieci wirtualnej.

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Sprawdź, czy reguły sieci wirtualnej i zapory są skonfigurowane poprawnie na koncie magazynu. W celu przetestowania, czy reguły sieci wirtualnej lub zapory są przyczyną problemu, tymczasowo zmień ustawienie na koncie magazynu, aby **zezwolić na dostęp ze wszystkich sieci**. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[Odmowa uprawnień] Przekroczono przydział dysku" podczas próby otwarcia pliku

W systemie Linux pojawia się komunikat o błędzie, który przypomina następujące:

**\<nazwa pliku> [odmowa uprawnień] Przekroczono przydział dysku**

### <a name="cause"></a>Przyczyna

Osiągnięto górny limit równoczesnych otwartych dojść, które są dozwolone dla pliku.

Istnieje przydział 2000 otwartych dojść w jednym pliku. Gdy masz 2000 otwartych dojść, wyświetlany jest komunikat o błędzie informujący o osiągnięciu przydziału.

### <a name="solution"></a>Rozwiązanie

Zmniejsz liczbę równoczesnych otwartych dojść, zamykając niektóre uchwyty, a następnie ponów próbę wykonania operacji.

Aby wyświetlić otwarte uchwyty udziału plików, katalogu lub pliku, użyj polecenia cmdlet [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell.  

Aby zamknąć otwarte uchwyty dla udziału plików, katalogu lub pliku, użyj polecenia cmdlet Programu PowerShell [Close-AzStorageFileHandle.](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle)

> [!Note]  
> Polecenia cmdlet Get-AzStorageFileHandle i Close-AzStorageFileHandle są zawarte w module Az PowerShell w wersji 2.4 lub nowszej. Aby zainstalować najnowszy moduł programu Az PowerShell, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Powolne kopiowanie plików do i z usługi Azure Files w systemie Linux

- Jeśli nie masz określonego minimalnego rozmiaru we/wy, zalecamy użycie 1 MiB jako rozmiaru we/wy dla optymalnej wydajności.
- Użyj odpowiedniej metody kopiowania:
    - Użyj [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) dla każdego przeniesienia między dwoma udziałami plików.
    - Za pomocą cp lub dd z równolegle może poprawić szybkość kopiowania, liczba wątków zależy od przypadku użycia i obciążenia. W poniższych przykładach użyto sześciu: 
    - cp przykład (cp użyje domyślnego rozmiaru bloku systemu `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`plików jako rozmiaru fragmentu): .
    - dd przykład (to polecenie jawnie ustawia rozmiar fragmentu na 1 MiB):`find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - Narzędzia innych firm typu open source, takie jak:
        - [GNU Równolegle](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) - Sortuje pliki i pakuje je do partycji.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) - Używa Fpart i narzędzie do kopiowania do zdyskli odlesić wiele wystąpień do migracji danych z src_dir do dst_url.
        - [Multi](https://github.com/pkolano/mutil) - Wielowątkowy cp i md5sum oparte na rdzeniach GNU.
- Ustawienie rozmiaru pliku z wyprzedzeniem, zamiast co zapis rozszerzenie zapisu, pomaga poprawić szybkość kopiowania w scenariuszach, w których rozmiar pliku jest znany. Jeśli należy unikać rozszerzania zapisów, można ustawić rozmiar `truncate - size <size><file>` pliku docelowego za pomocą polecenia. Następnie `dd if=<source> of=<target> bs=1M conv=notrunc`polecenie skopiuje plik źródłowy bez konieczności wielokrotnej aktualizacji rozmiaru pliku docelowego. Na przykład można ustawić rozmiar pliku docelowego dla każdego pliku, który chcesz skopiować (załóżmy, że udział jest zamontowany w obszarze /mnt/share):
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - a następnie - kopiowanie plików bez równoległego rozszerzania zapisów:`$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Mount error(115): Operation now in progress" podczas instalowania plików platformy Azure przy użyciu pliku SMB 3.0

### <a name="cause"></a>Przyczyna

Niektóre dystrybucje systemu Linux nie obsługują jeszcze funkcji szyfrowania w protokole SMB 3.0. Użytkownicy mogą dostawać komunikat o błędzie „115” w przypadku próby instalacji usługi Azure Files przy użyciu protokołu SMB 3.0 z powodu braku funkcji. Protokół SMB 3.0 z pełnym szyfrowaniem jest obsługiwany tylko wtedy, gdy używasz systemu Ubuntu 16.04 lub nowszego.

### <a name="solution"></a>Rozwiązanie

Funkcja szyfrowania dla protokołu SMB 3.0 dla systemu Linux została wprowadzona w jądrze 4.11. Ta funkcja umożliwia instalowanie udziału plików platformy Azure ze środowiska lokalnego lub z innego regionu platformy Azure. Niektóre dystrybucje Linuksa mogą mieć backported zmiany z jądra 4.11 do starszych wersji jądra Linuksa, które utrzymują. Aby pomóc w określeniu, czy twoja wersja systemu Linux obsługuje SMB 3.0 z szyfrowaniem, skonsultuj się z [Użyj plików Azure z systemem Linux](storage-how-to-use-files-linux.md). 

Jeśli Twój klient SMB w systemie Linux nie obsługuje szyfrowania, zainstaluj usługę Azure Files przy użyciu protokołu SMB 2.1 z maszyny wirtualnej platformy Azure z systemem Linux, która znajduje się w tym samym centrum danych co udział plików. Upewnij się, że ustawienie [Wymagany bezpieczny transfer]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) jest wyłączone na koncie magazynu. 

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Błąd "Brak dostępu" podczas próby uzyskania dostępu do udziału plików platformy Azure lub jej usunięcia  
Podczas próby uzyskania dostępu do udziału plików platformy Azure w portalu lub jej usunięcia może pojawić się następujący błąd:

Brak dostępu  
Kod błędu: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Przyczyna 1: Reguły sieci wirtualnej lub zapory są włączone na koncie magazynu

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

Sprawdź, czy reguły sieci wirtualnej i zapory są skonfigurowane poprawnie na koncie magazynu. W celu przetestowania, czy reguły sieci wirtualnej lub zapory są przyczyną problemu, tymczasowo zmień ustawienie na koncie magazynu, aby **zezwolić na dostęp ze wszystkich sieci**. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Przyczyna 2: Twoje konto użytkownika nie ma dostępu do konta magazynu

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Przejdź do konta magazynu, na którym znajduje się udział plików platformy Azure, kliknij pozycję **Kontrola dostępu (IAM)** i sprawdź, czy twoje konto użytkownika ma dostęp do konta magazynu. Aby dowiedzieć się więcej, zobacz [Jak zabezpieczyć konto magazynu za pomocą kontroli dostępu opartej na rolach (RBAC).](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection)

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Nie można usunąć pliku lub katalogu z udziału plików platformy Azure

### <a name="cause"></a>Przyczyna
Ten problem zazwyczaj występuje, jeśli plik lub katalog ma otwarty dojście. 

### <a name="solution"></a>Rozwiązanie

Jeśli klienci SMB zamknęli wszystkie otwarte dojścia i problem nadal występuje, wykonaj następujące czynności:

- Użyj polecenia cmdlet [Programu PowerShell Get-AzStorageFileHandle,](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) aby wyświetlić otwarte uchwyty.

- Użyj polecenia cmdlet Programu PowerShell [Close-AzStorageFileHandle,](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) aby zamknąć otwarte uchwyty. 

> [!Note]  
> Polecenia cmdlet Get-AzStorageFileHandle i Close-AzStorageFileHandle są zawarte w module Az PowerShell w wersji 2.4 lub nowszej. Aby zainstalować najnowszy moduł programu Az PowerShell, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Niska wydajność w udziale usługi Azure File zainstalowanym na maszynie wirtualnej z systemem Linux

### <a name="cause-1-caching"></a>Przyczyna 1: Buforowanie

Jedną z możliwych przyczyn powolnej wydajności jest wyłączenie buforowania. Buforowanie może być przydatne, jeśli uzyskujesz dostęp do pliku wielokrotnie, w przeciwnym razie może to być obciążenie. Sprawdź, czy używasz pamięci podręcznej przed jej wyłączeniem.

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

Aby sprawdzić, czy buforowanie jest wyłączone, poszukaj wpisu **cache=.**

**Cache=none** wskazuje, że buforowanie jest wyłączone. Ponownie zainstaluj udział przy użyciu domyślnego polecenia mount lub jawnie dodając **cache=strict** opcja do polecenia mount, aby upewnić się, że domyślny buforowanie lub "ścisły" tryb buforowania jest włączona.

W niektórych scenariuszach, **serverino** mount opcja może spowodować, że polecenie **ls** uruchomić stat przed każdym wpisem katalogu. To zachowanie powoduje obniżenie wydajności podczas wyświetlania listy dużego katalogu. Możesz sprawdzić opcje montażu w **/etc/fstab** wpisu:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Można również sprawdzić, czy odpowiednie opcje są używane przez uruchomienie **polecenia sudo mount | grep cifs** i sprawdzenie jego danych wyjściowych. Poniżej przedstawiono przykładowe dane wyjściowe:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Jeśli **opcja cache=strict** lub **serverino** nie jest obecna, odinstaluj i zainstaluj ponownie pliki Azure, uruchamiając polecenie mount z [dokumentacji](../storage-how-to-use-files-linux.md). Następnie ponownie sprawdzić, czy **/etc/fstab** wpis ma poprawne opcje.

### <a name="cause-2-throttling"></a>Przyczyna 2: Dławienie

Możliwe, że występują ograniczanie przepustowości, a twoje żądania są wysyłane do kolejki. Można to sprawdzić, korzystając z [metryk usługi Azure Storage w usłudze Azure Monitor.](../common/storage-metrics-in-azure-monitor.md)

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Upewnij się, że aplikacja znajduje się w obrębie [docelowych skali plików Azure](storage-files-scale-targets.md#azure-files-scale-targets).

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Sygnatury czasowe zostały utracone podczas kopiowania plików z systemu Windows do systemu Linux

Na platformach Linux/Unix polecenie **cp -p** kończy się niepowodzeniem, jeśli różni użytkownicy posiadają plik 1 i plik 2.

### <a name="cause"></a>Przyczyna

Flaga siły **f** w COPYFILE powoduje wykonanie **cp -p -f** na Unixie. To polecenie nie może również zachować sygnatury czasowej pliku, który nie jest właścicielem.

### <a name="workaround"></a>Obejście

Użyj użytkownika konta magazynu do kopiowania plików:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: nie&lt;może&gt;uzyskać dostępu do ' path ': Błąd wejścia/wyjścia

Podczas próby listy plików w udziale plików platformy Azure przy użyciu polecenia ls, polecenie zawiesza się podczas wyświetlania plików. Pojawia się następujący błąd:

**ls: nie&lt;można&gt;uzyskać dostępu" ścieżka ": Błąd wejścia/wyjścia**


### <a name="solution"></a>Rozwiązanie
Uaktualnij jądro Linuksa do następujących wersji, które mają rozwiązanie tego problemu:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Wszystkie wersje, które są większe lub równe 4.13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Nie można utworzyć dowiązań symbolicznych - ln: nie można utworzyć dowiązania symbolicznego "t": Operacja nie jest obsługiwana

### <a name="cause"></a>Przyczyna
Domyślnie instalowanie udziałów plików platformy Azure w systemie Linux przy użyciu systemu CIFS nie umożliwia obsługi dowiązań symbolicznych (dowiązania symboliczne). Widzisz błąd w ten sposób:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Rozwiązanie
Klient CIFS systemu Linux nie obsługuje tworzenia dowiązań symbolicznych w stylu systemu Windows za pomocą protokołu SMB 2 lub 3. Obecnie klient Systemu Linux obsługuje inny styl dowiązań symbolicznych o nazwie [Minshall + francuskie dowiązania symboliczne](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) zarówno do tworzenia, jak i śledzenia operacji. Klienci, którzy potrzebują dowiązań symbolicznych, mogą skorzystać z opcji montowania "mfsymlinks". Zalecamy "mfsymlinks", ponieważ jest to również format, którego używają komputery Mac.

Aby użyć dowiązań symbolicznych, dodaj następujące elementy na końcu polecenia dołączenia CIFS:

```
,mfsymlinks
```

Więc komenda wygląda mniej więcej tak:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Następnie można utworzyć dowiązki symboliczne zgodnie z sugestią na [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Błąd instalacji(112): Host jest wyłączany" z powodu przesunienia czasu ponownego połączenia

Błąd instalacji „112” występuje na kliencie z systemem Linux, gdy klient był bezczynny przez długi czas. Po dłuższym czasie bezczynności klient rozłączy się i upłynie limit czasu połączenia.  

### <a name="cause"></a>Przyczyna

Połączenie może być bezczynne z następujących przyczyn:

-   Błędy komunikacji sieciowej, które uniemożliwiają ponowne ustanowienie połączenia TCP z serwerem, gdy jest używana domyślna opcja „miękkiej” instalacji
-   Najnowsze poprawki ponownego nawiązywania połączenia, które nie są obecne w starszych jądrach

### <a name="solution"></a>Rozwiązanie

Ten problem z ponownym nawiązywaniem połączenia w jądrze systemu Linux jest już poprawiony jako część następujących zmian:

- [Poprawka ponownego nawiązywania połączenia w celu nie odraczania ponownego łączenia sesji protokołu smb3 długo po ponownym połączeniu gniazda](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Wywoływanie usługi echo natychmiast po ponownym połączeniu gniazda](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Naprawianie możliwego uszkodzenia pamięci podczas ponownego łączenia](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: Napraw możliwość podwójnego zablokowania obiektu mutex podczas ponownego łączenia (dla jądra v4.9 i nowszego)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Te zmiany mogą jednak nie być jeszcze przeniesione do wszystkich dystrybucji systemu Linux. Jeśli używasz popularnej dystrybucji systemu Linux, możesz sprawdzić na [użyj usługi Azure Files z systemem Linux,](storage-how-to-use-files-linux.md) aby zobaczyć, która wersja dystrybucji ma niezbędne zmiany jądra.

### <a name="workaround"></a>Obejście

Ten problem można obejść, określając twardą instalację. Twarda instalacja wymusza na kliencie oczekiwanie, aż połączenie zostanie nawiązane lub zostanie jawnie przerwane. Można z niego korzystać, aby zapobiegać błędom powodowanym przekroczeniem limitów czasu sieci. To obejście może jednak doprowadzić do niekończących się oczekiwań. Przygotuj się na zatrzymywanie połączeń zgodnie z wymaganiami.

Jeśli nie możesz wykonać uaktualnienia do najnowszych wersji jądra, możesz obejść ten problem, zachowując w udziale plików platformy Azure plik, do którego będziesz zapisywać co 30 sekund lub mniej. Musi to być operacja zapisu, taka jak ponowne zapisanie daty utworzenia lub modyfikacji pliku. W przeciwnym razie możesz otrzymać wyniki z pamięci podręcznej, a Twoja operacja może nie wyzwolić ponownego nawiązywania połączenia.

## <a name="cifs-vfs-error--22-on-ioctl-to-get-interface-list-when-you-mount-an-azure-file-share-by-using-smb-30"></a>"CIFS VFS: błąd -22 w ioctl, aby uzyskać listę interfejsów" podczas instalowania udziału plików platformy Azure przy użyciu SMB 3.0

### <a name="cause"></a>Przyczyna
Ten błąd jest rejestrowany, ponieważ usługa Azure Files [nie obsługuje obecnie wielokanałowego pliku SMB.](https://docs.microsoft.com/rest/api/storageservices/features-not-supported-by-the-azure-file-service)

### <a name="solution"></a>Rozwiązanie
Ten błąd można zignorować.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby szybko rozwiązać problem.
