---
title: Rozwiązywanie problemów z usługą Azure Files w systemie Linux | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z usługą Azure Files w systemie Linux
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 09898ac7dd4a6f3ee9cf0ea26ded607a8673b9f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438248"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Rozwiązywanie problemów z usługą Azure Files w systemie Linux

W tym artykule wymieniono typowe problemy, które są powiązane z usługą Azure Files, po nawiązaniu połączenia z klientami z systemem Linux. Zapewnia także możliwe przyczyny i rozwiązania tych problemów. 

Oprócz kroki rozwiązywania problemów, w tym artykule, można użyć [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) aby upewnić się, że klient systemu Linux ma poprawne warunki wstępne. AzFileDiagnostics automatyzuje wykrywania większości objawy wymienionych w tym artykule. Pomaga ono służą do konfigurowania środowiska w celu uzyskania optymalnej wydajności. Można również znaleźć te informacje w [udziałów plików platformy Azure do rozwiązywania problemów z](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). Narzędzie do rozwiązywania problemów zawiera instrukcje do udzielenia odpowiedzi na problemy, łączenie, mapowanie i instalowanie udziałów plików platformy Azure.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Instalowanie error(13): Odmowa uprawnień"podczas instalowania udziału plików platformy Azure

### <a name="cause-1-unencrypted-communication-channel"></a>Przyczyny 1: Nieszyfrowana komunikacja kanału

Ze względów bezpieczeństwa połączenia z udziałami plików platformy Azure są blokowane, jeśli nie jest szyfrowany kanał komunikacyjny, a w tym samym centrum danych nie jest podejmowana próba połączenia, gdzie znajdują się udziałów plików platformy Azure. Również może zostać zablokowany nieszyfrowanego połączenia, w tym samym centrum danych, jeśli [Wymagany bezpieczny transfer](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) ustawienie jest włączone na koncie magazynu. Kanału komunikacji szyfrowanej znajduje się tylko wtedy, gdy system operacyjny klienta użytkownika obsługuje szyfrowanie protokołu SMB.

Aby dowiedzieć się więcej, zobacz [wymagania wstępne dotyczące instalowania pliku Azure Udostępnianie systemu Linux i pakiet cifs utils](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux#prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package). 

### <a name="solution-for-cause-1"></a>Rozwiązanie przyczyny 1

1. Nawiązywanie połączenia z klienta, który obsługuje szyfrowanie protokołu SMB lub połączyć się z maszyną wirtualną, w tym samym centrum danych jako konta usługi Azure storage, które służy do udziału plików platformy Azure.
2. Sprawdź [Wymagany bezpieczny transfer](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) ustawienie jest wyłączone na koncie magazynu, jeśli klient nie obsługuje szyfrowania protokołu SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Przyczyny 2: Wirtualne reguły sieci lub zapory są włączone na koncie magazynu 

Jeśli sieć wirtualną (VNET) i reguły zapory są skonfigurowane na koncie magazynu, ruch sieciowy nastąpi odmowa dostępu, chyba, że adres IP klienta lub sieci wirtualnej ma mieć dostęp.

### <a name="solution-for-cause-2"></a>Rozwiązanie przyczyny 2

Sprawdź, czy wirtualnej sieci i reguł zapory są poprawnie skonfigurowane na koncie magazynu. Aby sprawdzić, czy wirtualny zasady sieci lub zapory jest przyczyną problemu, tymczasowo zmienić ustawienia na koncie magazynu **zezwolić na dostęp ze wszystkich sieci**. Aby dowiedzieć się więcej, zobacz [zapory Konfigurowanie usługi Azure Storage i sieci wirtualne](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"Przekroczono przydział dysku [odmowa uprawnień]" podczas próby otwarcia pliku

W systemie Linux pojawi się komunikat o błędzie podobny do następującego:

**\<Nazwa pliku > Przekroczono przydział dysku [odmowa uprawnień]**

### <a name="cause"></a>Przyczyna

Osiągnięto górny limit współbieżnych otwartych dojść, których można używać do pliku.

### <a name="solution"></a>Rozwiązanie

Zmniejsz liczbę jednoczesnych otwarte dojścia przez zamknięcie niektórych uchwyty, a następnie spróbuj ponownie wykonać operację. Aby uzyskać więcej informacji, zobacz [Lista kontrolna wydajności i skalowalności usługi Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Wolne kopiowania plików do i z usługi Azure Files w systemie Linux

- Jeśli nie masz określonych minimalny wymagany rozmiar operacji We/Wy, zalecamy użycie 1 MiB jako rozmiar operacji We/Wy w pod kątem optymalnej wydajności.
- Jeśli wiesz, końcowy rozmiar pliku, który jest rozszerzanie przy użyciu zapisu oraz oprogramowania nie występują problemy ze zgodnością, podczas niepisane tail do pliku zawiera zera wartość rozmiaru pliku wcześniej dokonywane co zapisu rozszerzanie zapisu.
- Użyj metody kopiowania prawa:
    - Użyj [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) wszelkie transferu między dwoma udziałami plików.
    - Użyj [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) między udziałów plików na komputerze lokalnym.

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Instalowanie error(112): Host nie działa"z powodu upływu limitu czasu ponownego łączenia

Błąd instalacji "112" występuje na komputerze klienckim systemu Linux po przez długi czas klienta. Po dłuższy czas bezczynności klient odłączy się i upłynie limit czasu połączenia.  

### <a name="cause"></a>Przyczyna

Połączenie może być bezczynne, z następujących powodów:

-   Problemy z komunikacją sieci, które uniemożliwiają ponowne ustanowienie połączenia TCP z serwerem, gdy jest używana opcja "elastyczne" instalacji domyślnej
-   Najnowsze poprawki ponowne nawiązanie połączenia, które nie są obecne w starszych jądra

### <a name="solution"></a>Rozwiązanie

Ten problem ponowne nawiązanie połączenia w jądrze systemu Linux jest teraz ustalone jako część następujących zmian:

- [Poprawka połączyć odraczaj sesji protokołu smb3 ponownie gdy ponownego połączenia gniazda](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Wywoływanie usługi echo, natychmiast po zakończeniu ponownego połączenia gniazda](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Naprawić uszkodzenie pamięci możliwe podczas ponownego nawiązania połączenia](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: Napraw możliwe double blokowanie mutex podczas ponownego nawiązania połączenia (dla jądra v4.9 i nowszych)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Jednak te zmiany mogą nie być przenoszone jeszcze do wszystkich dystrybucjach systemu Linux. Ta poprawka i inne poprawki ponowne nawiązanie połączenia znajdują się w następujących popularnych jądra systemu Linux: 4.4.40 4.8.16 i 4.9.1. Po uaktualnieniu do wersji jądra zalecane, możesz uzyskać tę poprawkę.

### <a name="workaround"></a>Obejście

Ten problem można obejść, określając twarde instalacji. Twarde instalacji wymusza to na kliencie, aby czekać do momentu nawiązaniu połączenia lub jawnie zostało przerwane. Aby zapobiec wystąpieniu błędów z powodu limitów czasu w sieci, można użyć go. Jednak to rozwiązanie może spowodować czeka na czas nieokreślony. Przygotuj się na zatrzymanie połączenia zgodnie z potrzebami.

Jeśli nie można uaktualnić do najnowszej wersji jądra, możesz obejść ten problem, przechowując plik w udziale plików platformy Azure, które piszesz co 30 sekund lub mniej. Musi to być operacji zapisu, takie jak ponowne napisanie Data utworzonego lub zmodyfikowanego pliku. W przeciwnym razie możesz otrzymać wyników z pamięci podręcznej, a operacja nie może wyzwalać ponownego łączenia.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Instalowanie error(115): Operacja w toku"podczas instalowania usługi Azure Files przy użyciu protokołu SMB 3.0

### <a name="cause"></a>Przyczyna

Niektórych dystrybucjach systemu Linux nie obsługują funkcji szyfrowania w protokole SMB 3.0. Użytkowników może zostać wyświetlony komunikat o błędzie "115" w przypadku próby instalacji usługi Azure Files przy użyciu protokołu SMB 3.0 ze względu na Brak funkcji. Protokół SMB 3.0 za pomocą pełnego szyfrowania jest obsługiwane tylko wtedy, gdy używasz Ubuntu 16.04 lub nowszej.

### <a name="solution"></a>Rozwiązanie

Funkcja szyfrowania protokołu SMB 3.0 dla systemu Linux została wprowadzona w 4.11 jądra. Ta funkcja umożliwia instalowanie udziału plików platformy Azure ze środowiska lokalnego lub z innego regionu platformy Azure. W czasie publikowania ta funkcja została backported Ubuntu 17.04 i Ubuntu 16.10. 

Jeśli Twój klient SMB w systemie Linux nie obsługuje szyfrowania, instalowanie usługi Azure Files przy użyciu protokołu SMB 2.1 maszyny wirtualnej systemu Linux platformy Azure, która znajduje się w tym samym centrum danych jako udziału plików. Upewnij się, że [Wymagany bezpieczny transfer]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) ustawienie jest wyłączone na koncie magazynu. 

<a id="accessdeniedportal"></a>
## <a name="error-access-denied-when-browsing-to-an-azure-file-share-in-the-portal"></a>Błąd "Odmowa dostępu" podczas przeglądania do udziału plików platformy Azure w portalu

Po przejściu do udziału plików platformy Azure w portalu, może zostać wyświetlony następujący błąd:

Odmowa dostępu  
Nie masz dostępu  
Prawdopodobnie nie masz dostępu do tej zawartości. Aby uzyskać dostęp, skontaktuj się z właścicielem.  

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>Przyczyny 1: Twoje konto użytkownika nie ma dostępu do konta magazynu

### <a name="solution-for-cause-1"></a>Rozwiązanie przyczyny 1

Przejdź do konta magazynu, w którym zlokalizowany jest udział plików platformy Azure, kliknij przycisk **kontrola dostępu (IAM)** i zweryfikować Twoje konto użytkownika ma dostęp do konta magazynu. Aby dowiedzieć się więcej, zobacz [sposób zabezpieczania konta magazynu przy użyciu kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Przyczyny 2: Wirtualne reguły sieci lub zapory są włączone na koncie magazynu

### <a name="solution-for-cause-2"></a>Rozwiązanie przyczyny 2

Sprawdź, czy wirtualnej sieci i reguł zapory są poprawnie skonfigurowane na koncie magazynu. Aby sprawdzić, czy wirtualny zasady sieci lub zapory jest przyczyną problemu, tymczasowo zmienić ustawienia na koncie magazynu **zezwolić na dostęp ze wszystkich sieci**. Aby dowiedzieć się więcej, zobacz [zapory Konfigurowanie usługi Azure Storage i sieci wirtualne](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Niska wydajność w udziale plików platformy Azure zainstalowany na maszynie Wirtualnej systemu Linux

### <a name="cause"></a>Przyczyna

Jedną z możliwych przyczyn spadek wydajności jest wyłączone buforowanie.

### <a name="solution"></a>Rozwiązanie

Aby sprawdzić, czy buforowanie jest wyłączone, poszukaj **pamięci podręcznej =** wpisu. 

**Pamięć podręczna = none** wskazuje, że buforowanie jest wyłączone. Zainstaluj udział, za pomocą polecenia instalacji domyślnej lub przez jawne dodanie **pamięci podręcznej = strict** jest włączona opcja instalacji polecenie, aby zapewnić tej buforowanie domyślne lub "strict" Tryb pamięci podręcznej.

W niektórych scenariuszach **serverino** opcji instalacji może spowodować, że **ls** polecenie, aby uruchomić stat — dla każdego wpisu dotyczącego katalogu. To zachowanie powoduje spadek wydajności podczas one wyświetlania dużych katalogu. Opcje instalacji możesz sprawdzić w swojej **/etc/fstab** wpis:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Możesz również sprawdzić, czy prawidłowe opcje są używane przez uruchomienie **instalacji "sudo" | grep cifs** polecenia i sprawdzanie danych wyjściowych. Poniżej przedstawiono przykładowe dane wyjściowe:

`//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Jeśli **pamięci podręcznej = strict** lub **serverino** opcja jest obecne, odinstaluj i ponownie zainstaluj usługi Azure Files za pomocą polecenia instalacji z [dokumentacji](../storage-how-to-use-files-linux.md). Następnie sprawdź ponownie, **/etc/fstab** wpis ma prawidłowe opcje.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Sygnatury czasowe zostały utracone podczas kopiowania plików z Windows z systemem Linux

Na platformach systemu Linux/Unix **cp -p** polecenie zakończy się niepowodzeniem, jeśli różni użytkownicy właścicielem pliku 1 i 2 pliku.

### <a name="cause"></a>Przyczyna

Flagi force **f** w COPYFILE powoduje wykonanie **cp -p -f** w systemach Unix. To polecenie nie powiedzie się także można zachować sygnatury czasowej pliku, który nie jest własnością.

### <a name="workaround"></a>Obejście

Do kopiowania plików przy użyciu poświadczeń użytkownika konta magazynu:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Nie można nawiązać lub zainstalować udział plików platformy Azure

### <a name="cause"></a>Przyczyna

Typowe przyczyny tego problemu są:


- Używasz niezgodny klient dystrybucji systemu Linux. Zaleca się, że używasz poniższe dystrybucje systemu Linux do łączenia z udziałem plików platformy Azure:

    |   | SMB 2.1 <br>(Instaluje na maszynach wirtualnych w tym samym regionie platformy Azure) | SMB 3.0 <br>(Instaluje ze środowiska lokalnego i między regionami) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3+ |
    | SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- Narzędzia CIFS (cfs utils) nie są zainstalowane na komputerze klienckim.
- Minimalna wersja protokołu SMB/CIFS, 2.1, nie jest zainstalowany na komputerze klienckim.
- Szyfrowanie protokołu SMB 3.0 nie jest obsługiwane na komputerze klienckim. Szyfrowanie protokołu SMB 3.0 jest dostępny w Ubuntu 16.4 i nowsze wersje, wraz z systemem SUSE 12.3 i nowszych wersjach. Inne dystrybucje wymagają jądra 4.11 i nowszych wersjach.
- Próbujesz połączyć się z kontem magazynu za pośrednictwem portu TCP 445, który nie jest obsługiwany.
- Próbujesz nawiązać połączenie z udziałem plików platformy Azure z maszyny Wirtualnej platformy Azure, a maszyna wirtualna nie znajduje się w tym samym regionie co konto magazynu.
- Jeśli [Wymagany bezpieczny transfer]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) ustawienie jest włączone na koncie magazynu i usługi Azure Files zezwala tylko na połączenia, które używają protokołu SMB 3.0 za pomocą szyfrowania.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy użyć [narzędzie do rozwiązywania problemów dla usługi Azure Files spowodowanych błędami instalowania w systemie Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). To narzędzie:

* Pomocne podczas walidowania klienta uruchamiania środowiska.
* Wykrywa konfiguracji niezgodny klienta, spowodowałoby błąd dostępu dla usługi Azure Files.
* Zawiera wskazówki nad własnym rozwiązaniem problemu.
* Służy do zbierania danych diagnostycznych śledzenia.

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: nie można uzyskać dostępu "&lt;ścieżki&gt;": Błąd wejścia/wyjścia

Podczas próby wyświetlanie listy plików w udziale plików platformy Azure za pomocą polecenia ls polecenie zawiesza się podczas wyświetlania listy plików. Zostanie wyświetlony następujący błąd:

**ls: nie można uzyskać dostępu "&lt;ścieżki&gt;": Błąd wejścia/wyjścia**


### <a name="solution"></a>Rozwiązanie
Uaktualnienie jądrze systemu Linux do następujących wersji, które mają poprawkę rozwiązującą ten problem:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Wszystkie wersje, które są większe niż lub równa 4.13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Nie można utworzyć łącza symbolicznego - ln: nie można utworzyć łącza symbolicznego, t ": Operacja nie jest obsługiwana

### <a name="cause"></a>Przyczyna
Domyślnie instalowanie udziałów plików platformy Azure w systemie Linux przy użyciu CIFS nie umożliwia obsługę łączy symbolicznych (linków symbolicznych). Zostanie wyświetlony błąd taki jak ten:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Rozwiązanie
Klient systemu Linux CIFS nie obsługuje tworzenia linków symbolicznych Windows style za pośrednictwem protokołu SMB 2 lub 3 protokołu. Obecnie klient systemu Linux obsługuje innego stylu linki symboliczne o nazwie [Minshall + francuski łączy symbolicznych](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) zarówno tworzenie i wykonać operacje. Klienci, którzy muszą łącza symbolicznego, można użyć opcji instalacji "mfsymlinks". Firma Microsoft zaleca "mfsymlinks", ponieważ jest on także formatu używanego dla komputerów Mac.

Aby używać łączy symbolicznych, Dodaj następujący element do końca CIFS polecenia instalacji:

```
,mfsymlinks
```

Aby polecenie będzie wyglądać mniej więcej tak:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Następnie możesz utworzyć łączy symbolicznych jako sugerowane na [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybko rozwiązać problem.
