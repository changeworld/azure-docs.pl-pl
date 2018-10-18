---
title: Rozwiązywanie problemów z usługą Azure Files w systemie Linux | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z usługą Azure Files w systemie Linux
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 05/11/2018
ms.author: jeffpatt
ms.component: files
ms.openlocfilehash: 31a0ffc2937f6d93a630bf6ce474d7dcf20c923f
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364391"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Rozwiązywanie problemów z usługą Azure Files w systemie Linux

W tym artykule wymieniono typowe problemy, które są powiązane z plików pakietu Microsoft Azure, po nawiązaniu połączenia z klientami z systemem Linux. Zapewnia także możliwe przyczyny i rozwiązania tych problemów.

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"Przekroczono przydział dysku [odmowa uprawnień]" podczas próby otwarcia pliku

W systemie Linux pojawi się komunikat o błędzie podobny do następującego:

**<filename> [odmowa uprawnień] Przekroczono przydział dysku**

### <a name="cause"></a>Przyczyna

Osiągnięto górny limit współbieżnych otwartych dojść, których można używać do pliku.

### <a name="solution"></a>Rozwiązanie

Zmniejsz liczbę jednoczesnych otwarte dojścia przez zamknięcie niektórych uchwyty, a następnie spróbuj ponownie wykonać operację. Aby uzyskać więcej informacji, zobacz [Lista kontrolna wydajności i skalowalności usługi Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Wolne kopiowania plików do i z usługi Azure Files w systemie Linux

- Jeśli nie masz określonych minimalny wymagany rozmiar operacji We/Wy, zalecamy użycie 1 MiB jako rozmiar operacji We/Wy w pod kątem optymalnej wydajności.
- Jeśli wiesz, końcowy rozmiar pliku, który powiększa się przy użyciu zapisu oraz oprogramowania nie występują problemy ze zgodnością, podczas niepisane tail do pliku zawiera zera wartość rozmiaru pliku wcześniej dokonywane co zapisu rozszerzanie zapisu.
- Użyj metody kopiowania prawa:
    - Użyj [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) wszelkie transferu między dwoma udziałami plików.
    - Użyj [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) między udziałów plików na komputerze lokalnym.

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Instalowanie error(112): Host nie działa" z powodu upływu limitu czasu ponownego łączenia

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

Jednak te zmiany mogą nie być przenoszone jeszcze do wszystkich dystrybucjach systemu Linux. Ta poprawka i inne poprawki ponowne nawiązanie połączenia, które zostały wprowadzone w następujących popularnych jądra systemu Linux: 4.4.40 4.8.16 i 4.9.1. Po uaktualnieniu do wersji jądra zalecane, możesz uzyskać tę poprawkę.

### <a name="workaround"></a>Obejście

Ten problem można obejść, określając twarde instalacji. Wymusza klienta czekać do momentu nawiązaniu połączenia lub jawnie zostało przerwane i może służyć do uniemożliwić błędy z powodu limitów czasu w sieci. Jednak to rozwiązanie może spowodować czeka na czas nieokreślony. Przygotuj się na zatrzymanie połączenia zgodnie z potrzebami.

Jeśli nie można uaktualnić do najnowszej wersji jądra, możesz obejść ten problem, przechowując plik w udziale plików platformy Azure, które piszesz co 30 sekund lub mniej. Musi to być operacji zapisu, takie jak ponowne napisanie Data utworzonego lub zmodyfikowanego pliku. W przeciwnym razie możesz otrzymać wyników z pamięci podręcznej, a operacja nie może wyzwalać ponownego łączenia.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Instalowanie error(115): operacja w toku" podczas instalowania usługi Azure Files przy użyciu protokołu SMB 3.0

### <a name="cause"></a>Przyczyna

Niektórych dystrybucjach systemu Linux nie jest jeszcze obsługiwany funkcje szyfrowania w protokole SMB 3.0, a użytkownicy mogą "115" komunikat o błędzie w przypadku próby instalacji usługi Azure Files przy użyciu protokołu SMB 3.0 ze względu na Brak funkcji. Protokół SMB 3.0 za pomocą pełnego szyfrowania jest obsługiwane tylko w tej chwili podczas korzystania z systemu Ubuntu 16.04 lub nowszej.

### <a name="solution"></a>Rozwiązanie

Funkcja szyfrowania protokołu SMB 3.0 dla systemu Linux została wprowadzona w 4.11 jądra. Ta funkcja umożliwia instalowanie udziału plików platformy Azure ze środowiska lokalnego lub innego regionu platformy Azure. W czasie publikowania ta funkcja została backported Ubuntu 17.04 i Ubuntu 16.10. Jeśli Twój klient SMB w systemie Linux nie obsługuje szyfrowania, instalowanie usługi Azure plików przy użyciu protokołu SMB 2.1 maszyny wirtualnej systemu Linux platformy Azure, który znajduje się w tym samym centrum danych, co plik udostępnianie i sprawdź [Wymagany bezpieczny transfer]( https://docs.microsoft.com/en-us/azure/storage/common/storage-require-secure-transfer) ustawienie jest wyłączone z ilością przestrzeni dyskowej konto. 

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Niska wydajność w udziale plików platformy Azure zainstalowany na maszynie Wirtualnej systemu Linux

### <a name="cause"></a>Przyczyna

Jedną z możliwych przyczyn spadek wydajności jest wyłączone buforowanie.

### <a name="solution"></a>Rozwiązanie

Aby sprawdzić, czy buforowanie jest wyłączone, poszukaj **pamięci podręcznej =** wpisu. 

**Pamięć podręczna = none** wskazuje, że buforowanie jest wyłączone.  Zainstaluj udział, za pomocą polecenia instalacji domyślnej lub przez jawne dodanie **pamięci podręcznej = strict** jest włączona opcja instalacji polecenie, aby zapewnić tej buforowanie domyślne lub "strict" Tryb pamięci podręcznej.

W niektórych scenariuszach **serverino** opcji instalacji może spowodować, że **ls** polecenie, aby uruchomić stat — dla każdego wpisu dotyczącego katalogu. To zachowanie powoduje spadek wydajności podczas one wyświetlania dużych katalogu. Opcje instalacji możesz sprawdzić w swojej **/etc/fstab** wpis:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Możesz również sprawdzić, czy prawidłowe opcje są używane przez uruchomienie **instalacji "sudo" | grep cifs** polecenia i sprawdzanie danych wyjściowych, takich jak następujące przykładowe dane wyjściowe:

`//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Jeśli **pamięci podręcznej = strict** lub **serverino** opcja jest obecne, odinstaluj i ponownie zainstaluj usługi Azure Files za pomocą polecenia instalacji z [dokumentacji](../storage-how-to-use-files-linux.md). Następnie sprawdź ponownie, **/etc/fstab** wpis ma prawidłowe opcje.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Sygnatury czasowe zostały utracone podczas kopiowania plików z Windows z systemem Linux

Na platformach systemu Linux/Unix **cp -p** polecenie zakończy się niepowodzeniem, jeśli plik 1 i 2 pliku należą do różnych użytkowników.

### <a name="cause"></a>Przyczyna

Flagi force **f** w COPYFILE powoduje wykonanie **cp -p -f** w systemach Unix. To polecenie nie powiedzie się także można zachować sygnatury czasowej pliku, który nie jest własnością.

### <a name="workaround"></a>Obejście

Do kopiowania plików przy użyciu poświadczeń użytkownika konta magazynu:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-or-mount-an-azure-file-share"></a>Nie można połączyć lub zainstalować udział plików platformy Azure

### <a name="cause"></a>Przyczyna

Typowe przyczyny tego problemu są:


- Używasz niezgodny klient dystrybucji systemu Linux. Zaleca się, że używasz poniższe dystrybucje systemu Linux do łączenia z udziałem plików platformy Azure:

* **Minimalna zalecana wersji przy użyciu odpowiedniej funkcji instalacji (wersja protokołu SMB 2.1 vs wersja protokołu SMB 3.0)**    
    
    |   | SMB 2.1 <br>(Instaluje na maszynach wirtualnych w tym samym regionie platformy Azure) | SMB 3.0 <br>(Instaluje ze środowiska lokalnego i między regionami) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04 + | 16.04 + |
    | RHEL | 7 + | 7.5+ |
    | CentOS | 7 + |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2 + | 42.3 + |
    | SUSE Linux Enterprise Server | 12 | 12 Z DODATKIEM SP3 + |

- CIFS utils nie są zainstalowane na komputerze klienckim.
- Minimalna SMB/CIFS wersji 2.1 nie jest zainstalowany na komputerze klienckim.
- Protokół SMB 3.0 szyfrowania nie jest obsługiwana na komputerze klienckim. Protokół SMB 3.0 szyfrowanie jest dostępne w Ubuntu 16.4 i nowszej wersji, SUSE 12.3 i nowszej wersji. Inne dystrybucje wymagają jądra 4.11 i nowszej wersji.
- Próbujesz połączyć się z kontem magazynu za pośrednictwem portu TCP 445, która nie jest obsługiwana.
- Próbujesz nawiązać połączenie z udziałem plików platformy Azure z Maszyną wirtualną platformy Azure, a maszyna wirtualna nie znajduje się w tym samym regionie co konto magazynu.
- Jeśli [Wymagany bezpieczny transfer]( https://docs.microsoft.com/en-us/azure/storage/common/storage-require-secure-transfer) ustawienie jest włączone na koncie magazynu, usługi Azure Files będzie Zezwalaj na połączenia tylko z szyfrowaniem przy użyciu protokołu SMB 3.0.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy użyć [narzędzia do rozwiązywania problemów dla usługi Azure Files spowodowanych błędami instalowania w systemie Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). To narzędzie ułatwia sprawdzanie poprawności klienta uruchamiania środowiska, wykrywania konfiguracji niezgodny klient, które spowodują błąd dostępu dla usługi Azure Files, udostępnia wskazówki na samodzielnie rozwiązać i zbieranie śladów diagnostyki.

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: nie można uzyskać dostępu "&lt;ścieżki&gt;": błąd wejścia/wyjścia

Podczas próby listy plików w pliku platformy Azure, udostępnić za pomocą polecenia ls ls, które polecenie zawiesza się podczas listy plików komunikat o błędzie:

**ls: nie można uzyskać dostępu "&lt;ścieżki&gt;": błąd wejścia/wyjścia**


### <a name="solution"></a>Rozwiązanie
Uaktualnienie jądrze systemu Linux do następujących wersji, które mają poprawkę rozwiązującą ten problem:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Wszystkie wersje, które jest większe lub równe 4.13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Nie można utworzyć łącza symbolicznego - ln: nie można utworzyć łącza symbolicznego, t ": operacja nie jest obsługiwana

### <a name="cause"></a>Przyczyna
Domyślnie instalowanie udziałów plików platformy Azure w systemie Linux przy użyciu CIFS nie umożliwia obsługę łączy symbolicznych. Zostanie wyświetlony błąd to łącze:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Rozwiązanie
Klient systemu Linux CIFS nie obsługuje tworzenia linków symbolicznych style Windows za pośrednictwem protokołu SMB2/3. Klient systemu Linux obsługuje obecnie inny styl linki symboliczne o nazwie [Mishall + francuski łączy symbolicznych] (https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) zarówno tworzenie i wykonać operacje. Klienci, którzy muszą linki symboliczne użyć opcji instalacji "mfsymlinks". "mfsymlinks" są zwykle zalecane, ponieważ jest to format używany przez Mac.

Aby można było używać łączy symbolicznych, Dodaj następujący element do końca CIFS polecenia instalacji:

```
,mfsymlinks
```

To polecenie będzie wyglądać następująco:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsynlinks
```

Po dodaniu, można utworzyć łączy symbolicznych jako sugerowane na [Wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybko rozwiązać problem.
