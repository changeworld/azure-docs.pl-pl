---
title: Rozwiązywanie problemów z plikami platformy Azure w systemie Windows | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z usługą Azure Files w systemie Windows
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 17ecc80fee3b024c334b8d36533663f1f3cebe4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136909"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Rozwiązywanie problemów z usługą Azure Files w systemie Windows

W tym artykule wymieniono typowe problemy związane z plikami microsoft azure podczas łączenia się z klientami systemu Windows. Zapewnia również możliwe przyczyny i rozwiązania tych problemów. Oprócz kroków rozwiązywania problemów w tym artykule można również użyć [AzFileDiagnostics,](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) aby upewnić się, że środowisko klienckie systemu Windows ma poprawne wymagania wstępne. AzFileDiagnostics automatyzuje wykrywanie większości objawów wymienionych w tym artykule i pomaga skonfigurować środowisko, aby uzyskać optymalną wydajność. Informacje te można również znaleźć w narzędziu [do rozwiązywania problemów z udziałami w usłudze Azure Files,](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) które zawiera kroki ułatwiające rozwiązywanie problemów z łączeniem/mapowaniem/montowaniem udziałów usługi Azure Files.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Błąd 5 podczas instalowania udziału plików platformy Azure

Podczas próby zainstalowania udziału plików może pojawić się następujący błąd:

- Wystąpił błąd systemowy 5. Odmowa dostępu.

### <a name="cause-1-unencrypted-communication-channel"></a>Przyczyna 1: Niezaszyfrowany kanał komunikacyjny

Ze względów bezpieczeństwa połączenia z udziałami plików platformy Azure są blokowane, jeśli kanał komunikacyjny nie jest szyfrowany i jeśli próba połączenia nie pochodzi z tego samego centrum danych, w którym znajdują się udziały plików platformy Azure. Nieszyfrowane połączenia w tym samym centrum danych też mogą być blokowane, jeśli ustawienie [Wymagany bezpieczny transfer](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) jest włączone na koncie magazynu. Szyfrowany kanał komunikacyjny jest udostępniany tylko wtedy, gdy system operacyjny klienta użytkownika obsługuje szyfrowanie protokołu SMB.

Systemy Windows 8, Windows Server 2012 i nowsze wersje negocjują żądania obejmujące protokół SMB 3.0, który obsługuje szyfrowanie.

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

1. Połącz się z klientem obsługującym szyfrowanie SMB (Windows 8, Windows Server 2012 lub nowszy) lub połącz się z maszyny wirtualnej w tym samym centrum danych, co konto magazynu platformy Azure, które jest używane dla udziału plików platformy Azure.
2. Sprawdź, czy wymagane ustawienie [Bezpiecznego transferu](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) jest wyłączone na koncie magazynu, jeśli klient nie obsługuje szyfrowania SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Przyczyna 2: Reguły sieci wirtualnej lub zapory są włączone na koncie magazynu 

Jeśli reguły sieci wirtualnej i zapory są skonfigurowane na koncie magazynu, dla ruchu sieciowego będzie następować odmowa dostępu, chyba że dostęp będzie dozwolony dla adresu IP klienta lub sieci wirtualnej.

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Sprawdź, czy reguły sieci wirtualnej i zapory są skonfigurowane poprawnie na koncie magazynu. W celu przetestowania, czy reguły sieci wirtualnej lub zapory są przyczyną problemu, tymczasowo zmień ustawienie na koncie magazynu, aby **zezwolić na dostęp ze wszystkich sieci**. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Przyczyna 3: Uprawnienia na poziomie udziału są niepoprawne podczas korzystania z uwierzytelniania opartego na tożsamości

Jeśli użytkownicy uzyskują dostęp do udziału plików platformy Azure przy użyciu usługi Active Directory (AD) lub uwierzytelniania Usług domenowych Usługi Azure Active Directory (Usługi Azure AD DS), dostęp do udziału plików zakończy się niepowodzeniem z błędem "Odmowa dostępu", jeśli uprawnienia na poziomie udziału są niepoprawne. 

### <a name="solution-for-cause-3"></a>Roztwór dla przyczyny 3

Aby zaktualizować uprawnienia na poziomie udziału, zobacz [Przypisywanie uprawnień dostępu do tożsamości](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#assign-access-permissions-to-an-identity).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Błąd 53, błąd 67 lub błąd 87 podczas instalowania lub odinstalowyjenia udziału plików platformy Azure

Podczas próby zainstalowania udziału plików lokalnie lub z innego centrum danych mogą pojawić się następujące błędy:

- Wystąpił błąd systemowy 53. Nie można znaleźć ścieżki sieciowej.
- Wystąpił błąd systemowy 67. Nie można znaleźć nazwy sieciowej.
- Wystąpił błąd systemowy 87. Parametr jest nieprawidłowy.

### <a name="cause-1-port-445-is-blocked"></a>Przyczyna 1: Port 445 jest zablokowany

Błąd systemu 53 lub błąd systemu 67 może wystąpić, jeśli port 445 komunikacji wychodzącej do centrum danych usługi Azure Files jest zablokowany. Aby zobaczyć podsumowanie usługodawców internetowych, którzy nie zezwalają na dostęp z portu 445, przejdź do witryny [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Aby sprawdzić, czy zapora lub usługodawca internetowych blokuje port 445, `Test-NetConnection` użyj narzędzia [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) lub polecenia cmdlet. 

Aby użyć `Test-NetConnection` polecenia cmdlet, musi zostać zainstalowany moduł programu Azure PowerShell, zobacz [Instalowanie modułu programu Azure PowerShell,](/powershell/azure/install-Az-ps) aby uzyskać więcej informacji. Pamiętaj, aby zastąpić wyrażenia `<your-storage-account-name>` i `<your-resource-group-name>` nazwami odpowiednimi dla konta magazynu.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
Jeśli połączenie zostało pomyślnie nawiązane, powinny pojawić się następujące dane wyjściowe:
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> Powyższe polecenie zwraca bieżący adres IP konta magazynu. Nie ma żadnej gwarancji, że ten adres IP pozostanie niezmieniony. Może on ulec zmianie w dowolnym momencie. Nie umieszczaj tego adresu IP w żadnym kodzie skryptu ani konfiguracji zapory.

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

#### <a name="solution-1---use-azure-file-sync"></a>Rozwiązanie 1 — użycie usługi Azure File Sync
Usługa Azure File Sync może przekształcić lokalny system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Można użyć dowolnego protokołu dostępnego w systemie Windows Server, aby uzyskać dostęp do danych lokalnie, w tym SMB, NFS i FTPS. Usługa Azure File Sync działa za pomocą portu 443 i dlatego może służyć jako obejście dostępu do usługi Azure Files od klientów, którzy mają zablokowany port 445. [Dowiedz się, jak skonfigurować usługę Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Rozwiązanie 2 — użycie sieci VPN
Po skonfigurowaniu sieci VPN na określonym koncie pamięci masowej ruch będzie przebiegał przez bezpieczny tunel, a nie przez Internet. Postępuj zgodnie z [instrukcjami, aby skonfigurować sieć VPN,](storage-files-configure-p2s-vpn-windows.md) aby uzyskać dostęp do usługi Azure Files z systemu Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Rozwiązanie 3 — odblokowanie portu 445 przy pomocy usługodawcy internetowego/administratora IT
Skontaktuj się z działem IT lub usługodawcą bardzo usługowym, aby otworzyć wychodzące porty 445 do [zakresów adresów IP platformy Azure.](https://www.microsoft.com/download/details.aspx?id=41653)

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Rozwiązanie 4 — użycie narzędzi opartych na interfejsie API REST, takich jak Eksplorator usługi Storage/Powershell
Usługa Azure Files obsługuje również REST oprócz SMB. Dostęp REST działa nad portem 443 (standard tcp). Istnieją różne narzędzia, które są zapisywane przy użyciu interfejsu API REST, które umożliwiają bogate środowisko interfejsu użytkownika. [Eksplorator magazynu](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) jest jednym z nich. [Pobierz i zainstaluj Eksploratora magazynu](https://azure.microsoft.com/features/storage-explorer/) i połącz się z udziałem plików wspieranym przez usługi Azure Files. Można również użyć [programu PowerShell,](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) który również interfejs API REST użytkownika.

### <a name="cause-2-ntlmv1-is-enabled"></a>Przyczyna 2: NTLMv1 jest włączona

Błąd systemu 53 lub błąd systemu 87 może wystąpić, jeśli komunikacja NTLMv1 jest włączona na kliencie. Usługa Azure Files obsługuje tylko uwierzytelnianie NTLMv2. Włączenie komunikacji NTLMv1 powoduje, że klient jest mniej bezpieczny. Dlatego komunikacja jest blokowana dla usługi Azure Files. 

Aby ustalić, czy jest to przyczyną błędu, sprawdź, czy następujący podklucz rejestru jest ustawiony na wartość 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Aby uzyskać więcej informacji, zobacz temat [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) w witrynie TechNet.

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Przywróć wartość **LmCompatibilityLevel** do wartości domyślnej równej 3 w następującym podkluczu rejestru:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Błąd 1816 "Za mało przydziału jest dostępna do przetworzenia tego polecenia" podczas kopiowania do udziału plików platformy Azure

### <a name="cause"></a>Przyczyna

Błąd 1816 występuje po osiągnięciu górnej granicy równoczesnych otwartych uchwytów, które są dozwolone dla pliku na komputerze, na którym jest montowany udział plików.

### <a name="solution"></a>Rozwiązanie

Zmniejsz liczbę równoczesnych otwartych dojść, zamykając niektóre uchwyty, a następnie ponów próbę. Aby uzyskać więcej informacji, zobacz [Lista kontrolna wydajności i skalowalności usługi Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Aby wyświetlić otwarte uchwyty udziału plików, katalogu lub pliku, użyj polecenia cmdlet [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell.  

Aby zamknąć otwarte uchwyty dla udziału plików, katalogu lub pliku, użyj polecenia cmdlet Programu PowerShell [Close-AzStorageFileHandle.](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle)

> [!Note]  
> Polecenia cmdlet Get-AzStorageFileHandle i Close-AzStorageFileHandle są zawarte w module Az PowerShell w wersji 2.4 lub nowszej. Aby zainstalować najnowszy moduł programu Az PowerShell, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

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
Podczas próby usunięcia pliku może pojawić się następujący błąd:

Określony zasób jest oznaczony do usunięcia przez klienta SMB.

### <a name="cause"></a>Przyczyna
Ten problem zazwyczaj występuje, jeśli plik lub katalog ma otwarty dojście. 

### <a name="solution"></a>Rozwiązanie

Jeśli klienci SMB zamknęli wszystkie otwarte dojścia i problem nadal występuje, wykonaj następujące czynności:

- Użyj polecenia cmdlet [Programu PowerShell Get-AzStorageFileHandle,](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) aby wyświetlić otwarte uchwyty.

- Użyj polecenia cmdlet Programu PowerShell [Close-AzStorageFileHandle,](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) aby zamknąć otwarte uchwyty. 

> [!Note]  
> Polecenia cmdlet Get-AzStorageFileHandle i Close-AzStorageFileHandle są zawarte w module Az PowerShell w wersji 2.4 lub nowszej. Aby zainstalować najnowszy moduł programu Az PowerShell, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Slow file copying to and from Azure Files in Windows (Wolne kopiowanie plików do i z usługi Azure Files w systemie Windows)

Podczas próby przeniesienia plików do usługi Azure File może być widoczna niska wydajność.

- Jeśli nie masz określonego minimalnego rozmiaru we/wy, zalecamy użycie 1 MiB jako rozmiaru we/wy dla optymalnej wydajności.
-   Jeśli znasz ostateczny rozmiar pliku, który rozszerzasz za pomocą zapisów, a oprogramowanie nie ma problemów ze zgodnością, gdy niepisany ogon w pliku zawiera zera, należy ustawić rozmiar pliku z wyprzedzeniem, zamiast dokonywać każdego zapisu zapisu rozszerzającego.
-   Użyj odpowiedniej metody kopiowania:
    -   Użyj [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) dla każdego przeniesienia między dwoma udziałami plików.
    -   [Robocopy](/azure/storage/files/storage-files-deployment-guide#robocopy) między udziałami plików na komputerze lokalnym.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Zagadnienia dotyczące systemu Windows 8.1 lub Windows Server 2012 R2

W przypadku klientów z systemem Windows 8.1 lub Windows Server 2012 R2 upewnij się, że jest zainstalowana poprawka [KB3114025.](https://support.microsoft.com/help/3114025) Ta poprawka zwiększa wydajność tworzenia i zamykania uchwytów.

Można uruchomić następujący skrypt, aby sprawdzić, czy poprawka została zainstalowana:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Jeśli poprawka jest zainstalowana, wyświetlane jest następujące dane wyjściowe:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Obrazy systemu Windows Server 2012 R2 w portalu Azure Marketplace mają domyślnie zainstalowaną poprawkę KB3114025, począwszy od grudnia 2015 r.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Brak folderu z literą dysku w "Mój komputer" lub "Ten komputer"

Jeśli mapujesz udział plików platformy Azure jako administrator przy użyciu sieci, wydaje się, że brakuje udziału.

### <a name="cause"></a>Przyczyna

Domyślnie Eksplorator plików Windows nie działa jako administrator. Po uruchomieniu użycia sieci z wiersza polecenia administracyjnego można mapować dysk sieciowy jako administrator. Ponieważ zamapowane dyski są zorientowane na użytkownika, konto użytkownika, które jest zalogowane, nie wyświetla dysków, jeśli są one zainstalowane w ramach innego konta użytkownika.

### <a name="solution"></a>Rozwiązanie
Zainstaluj udział z wiersza polecenia niebędącego administratorem. Alternatywnie można wykonać [ten temat w uciesci w uciesci,](https://technet.microsoft.com/library/ee844140.aspx) aby skonfigurować wartość rejestru **EnableLinkedConnections.**

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Polecenie Net use kończy się niepowodzeniem, jeśli konto magazynu zawiera ukośnik do przodu

### <a name="cause"></a>Przyczyna

Polecenie net use interpretuje ukośnik do przodu (/) jako opcję wiersza polecenia. Jeśli nazwa konta użytkownika zaczyna się od ukośnika do przodu, mapowanie dysku kończy się niepowodzeniem.

### <a name="solution"></a>Rozwiązanie

Aby obejść ten problem, można wykonać jedną z następujących czynności:

- Uruchom następujące polecenie programu PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Z pliku wsadowego można uruchomić polecenie w ten sposób:

  `Echo new-smbMapping ... | powershell -command –`

- Umieść podwójne cudzysłowy wokół klucza, aby obejść ten problem - chyba że ukośnik do przodu jest pierwszym znakiem. Jeśli tak jest, użyj trybu interaktywnego i wprowadź hasło oddzielnie lub ponownie wygeneruj klucze, aby uzyskać klucz, który nie zaczyna się od ukośnika do przodu.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Aplikacja lub usługa nie może uzyskać dostępu do zainstalowanego dysku Usługi Azure Files

### <a name="cause"></a>Przyczyna

Dyski są montowane na użytkownika. Jeśli aplikacja lub usługa jest uruchomiona na innym koncie użytkownika niż to, które zamontowano dysk, aplikacja nie zobaczy dysku.

### <a name="solution"></a>Rozwiązanie

Użyj jednego z następujących rozwiązań:

-   Zainstaluj dysk z tego samego konta użytkownika, które zawiera aplikację. Można użyć narzędzia, takiego jak PsExec.
- Przekaż nazwę konta magazynu i klucz w parametrach nazwy użytkownika i hasła polecenia net use.
- Polecenie cmdkey służy do dodawania poświadczeń do Menedżera poświadczeń. Wykonaj to z wiersza polecenia w kontekście konta usługi, `runas`za pomocą interaktywnego logowania lub za pomocą programu .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mapuj udział bezpośrednio bez użycia zamapowanych liter dysku. Niektóre aplikacje mogą nie połączyć się ponownie z literą dysku poprawnie, więc przy użyciu pełnej ścieżki UNC może być bardziej wiarygodne. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Po wykonaniu tych instrukcji po uruchomieniu konta usługi systemowej/sieciowej może zostać wyświetlony następujący komunikat o błędzie: "Wystąpił błąd systemu 1312. Określona sesja logowania nie istnieje. Być może została ona już zakończona." W takim przypadku upewnij się, że nazwa użytkownika przekazywana do użytku sieciowego zawiera informacje o domenie (na przykład: "[nazwa konta magazynu].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Błąd "Kopiowanie pliku do miejsca docelowego, które nie obsługuje szyfrowania"

Gdy plik jest kopiowany przez sieć, plik jest odszyfrowywany na komputerze źródłowym, przesyłany w postaci zwykłego tekstu i ponownie szyfrowany w miejscu docelowym. Jednak podczas próby skopiowania zaszyfrowanego pliku może zostać wyświetlony następujący błąd: "Kopiujesz plik do miejsca docelowego, które nie obsługuje szyfrowania".

### <a name="cause"></a>Przyczyna
Ten problem może wystąpić, jeśli używasz systemu szyfrowania plików (EFS). Pliki zaszyfrowane funkcji BitLocker można kopiować do usługi Azure Files. Jednak usługa Azure Files nie obsługuje systemu szyfrowania plików NTFS.

### <a name="workaround"></a>Obejście
Aby skopiować plik przez sieć, należy go najpierw odszyfrować. Użyj jednej z następujących metod:

- Użyj polecenia **kopiuj /d.** Umożliwia zapisanie zaszyfrowanych plików jako odszyfrowanych plików w miejscu docelowym.
- Ustaw następujący klucz rejestru:
  - Ścieżka = HKLM\Software\Policies\Microsoft\Windows\System
  - Typ wartości = DWORD
  - Nazwa = CopyFileAllowDecryptedRemoteDestination
  - Wartość = 1

Należy pamiętać, że ustawienie klucza rejestru wpływa na wszystkie operacje kopiowania, które są dokonywane w udziałach sieciowych.

## <a name="slow-enumeration-of-files-and-folders"></a>Powolne wyliczanie plików i folderów

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli nie ma wystarczającej ilości pamięci podręcznej na komputerze klienckim dla dużych katalogów.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, dostosowanie wartości rejestru **DirectoryCachEntrySizeMax** w celu umożliwienia buforowania większych list katalogów na komputerze klienckim:

- Lokalizacja: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Grzywa wartości: DirectoryCachEntrySizeMax 
- Typ wartości:DWORD
 
 
Na przykład można ustawić go na 0x100000 i sprawdzić, czy wydajność stała się lepsza.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Błąd AadDsTenantNotFound podczas włączania uwierzytelniania usługi domenowej Active Directory (AAD DS) dla plików platformy Azure "Nie można zlokalizować aktywnych dzierżaw o identyfikatorze dzierżawy aad-tenant-id"

### <a name="cause"></a>Przyczyna

Błąd AadDsTenantNotFound występuje podczas próby [włączenia uwierzytelniania usług domenowych Usługi active directory azure (Usługi Azure AD DS) w plikach Azure na](storage-files-identity-auth-active-directory-domain-service-enable.md) koncie magazynu, gdzie usługa domeny [AAD (AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) nie jest tworzona w dzierżawie usługi AAD skojarzonej subskrypcji.  

### <a name="solution"></a>Rozwiązanie

Włącz usługę AAD DS w dzierżawie usługi AAD subskrypcji, w ramach których jest wdrażane konto magazynu. Do utworzenia domeny zarządzanej potrzebne są uprawnienia administratora dzierżawy usługi AAD. Jeśli nie jesteś administratorem dzierżawy usługi Azure AD, skontaktuj się z administratorem i postępuj zgodnie ze wskazówkami krok po kroku, aby [włączyć usługi domenowe usługi platformy Azure Active Directory przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>Wystąpił błąd systemu 1359. Odebrano błąd wewnętrzny w związku z dostępem SMB do udziałów plików z włączoną obsługą usługi domenowej Usługi AAD (AAD DS) usługi Azure Active Directory

### <a name="cause"></a>Przyczyna

Wystąpił błąd systemu 1359. Błąd wewnętrzny" występuje podczas próby nawiązania połączenia z udziałem plików za pomocą uwierzytelniania AAD DS włączonego względem usługi AAD DS z nazwą DNS domeny, zaczynając od znaku numerycznego. Jeśli na przykład nazwa DNS domeny AAD DS ma wartość "1domena", ten błąd zostanie wyświetlony podczas próby zainstalowania udziału plików przy użyciu poświadczeń usługi AAD. 

### <a name="solution"></a>Rozwiązanie

Obecnie można rozważyć ponowne wdrożenie usługi AAD DS przy użyciu nowej nazwy DNS domeny, która ma zastosowanie zgodnie z poniższymi regułami:
- Nazwy nie mogą zaczynać się od znaku liczbowego.
- Nazwy muszą mieć od 3 do 63 znaków.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby szybko rozwiązać problem.
