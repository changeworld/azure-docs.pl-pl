---
title: Rozwiązywanie problemów z usługą Azure Files w Windows | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z usługą Azure Files w Windows
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 3e40e72a4cbb154a7ccef153959e81f7054ea003
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926379"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Rozwiązywanie problemów z usługą Azure Files w Windows

W tym artykule wymieniono typowe problemy, które są powiązane z plików pakietu Microsoft Azure, po nawiązaniu połączenia z klientami Windows. Zapewnia także możliwe przyczyny i rozwiązania tych problemów. Oprócz kroki rozwiązywania problemów, w tym artykule, można również użyć [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) aby upewnić się, że w środowisku klienta Windows ma poprawne warunki wstępne. AzFileDiagnostics automatyzuje wykrywania większość objawy wymienionych w tym artykule i ułatwia konfigurowanie środowiska w celu uzyskania optymalnej wydajności. Można również znaleźć te informacje w [udziałów plików platformy Azure do rozwiązywania problemów z](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) zawierający kroki, aby pomóc z problemami z udziałów plików platformy Azure łączenie/mapowania/instalowanie.

<a id="error5"></a>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="error-5-when-you-mount-an-azure-file-share"></a>Błąd 5 w przypadku zainstalowania udziału plików platformy Azure

Podczas próby instalacji udziału plików, może pojawić się następujący błąd:

- Wystąpił błąd systemowy 5. Odmowa dostępu.

### <a name="cause-1-unencrypted-communication-channel"></a>Przyczyny 1: Nieszyfrowana komunikacja kanału

Ze względów bezpieczeństwa połączenia z udziałami plików platformy Azure są blokowane, jeśli nie jest szyfrowany kanał komunikacyjny, a w tym samym centrum danych nie jest podejmowana próba połączenia, gdzie znajdują się udziałów plików platformy Azure. Również może zostać zablokowany nieszyfrowanego połączenia, w tym samym centrum danych, jeśli [Wymagany bezpieczny transfer](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) ustawienie jest włączone na koncie magazynu. Kanału komunikacji szyfrowanej znajduje się tylko wtedy, gdy system operacyjny klienta użytkownika obsługuje szyfrowanie protokołu SMB.

Windows 8, Windows Server 2012 i nowszych wersjach każdego systemu negocjowania żądań, które obejmują protokół SMB 3.0, który obsługuje szyfrowanie.

### <a name="solution-for-cause-1"></a>Rozwiązanie przyczyny 1

1. Nawiązywanie połączenia z klienta, który obsługuje szyfrowanie protokołu SMB (system Windows 8, Windows Server 2012 lub nowszy) lub połączyć się z maszyną wirtualną, w tym samym centrum danych jako konta usługi Azure storage, które służy do udziału plików platformy Azure.
2. Sprawdź [Wymagany bezpieczny transfer](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) ustawienie jest wyłączone na koncie magazynu, jeśli klient nie obsługuje szyfrowania protokołu SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Przyczyny 2: Wirtualne reguły sieci lub zapory są włączone na koncie magazynu 

Jeśli sieć wirtualną (VNET) i reguły zapory są skonfigurowane na koncie magazynu, ruch sieciowy nastąpi odmowa dostępu, chyba, że adres IP klienta lub sieci wirtualnej ma mieć dostęp.

### <a name="solution-for-cause-2"></a>Rozwiązanie przyczyny 2

Sprawdź, czy wirtualnej sieci i reguł zapory są poprawnie skonfigurowane na koncie magazynu. Aby sprawdzić, czy wirtualny zasady sieci lub zapory jest przyczyną problemu, tymczasowo zmienić ustawienia na koncie magazynu **zezwolić na dostęp ze wszystkich sieci**. Aby dowiedzieć się więcej, zobacz [zapory Konfigurowanie usługi Azure Storage i sieci wirtualne](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Błąd 53, błąd 67 lub 87 błąd podczas instalowania lub odinstalowania udziału plików platformy Azure

Podczas próby instalacji udziału plików ze środowiska lokalnego lub z innego centrum danych, może pojawić się następujące błędy:

- Wystąpił błąd systemowy 53. Nie można odnaleźć ścieżki sieciowej.
- Wystąpił błąd systemowy 67. Nie można odnaleźć nazwy sieci.
- Wystąpił błąd systemowy 87. Parametr jest nieprawidłowy.

### <a name="cause-1-port-445-is-blocked"></a>Przyczyny 1: Port 445 jest zablokowany

Błąd 53 lub błąd 67 może wystąpić, jeśli port 445 komunikacja wychodząca centrum danych usługi Azure Files jest zablokowany. Aby wyświetlić podsumowanie usługodawców internetowych, które blokują lub nie zezwalaj na dostęp z portu 445, przejdź do [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Aby sprawdzić, czy zapory lub usługodawcy internetowego blokuje portu 445, użyj [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) narzędzia lub `Test-NetConnection` polecenia cmdlet. 

Aby użyć `Test-NetConnection` polecenia cmdlet programu Azure PowerShell module muszą być zainstalowane, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps) Aby uzyskać więcej informacji. Pamiętaj, aby zastąpić wyrażenia `<your-storage-account-name>` i `<your-resource-group-name>` nazwami odpowiednimi dla konta magazynu.

   
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

### <a name="solution-for-cause-1"></a>Rozwiązanie przyczyny 1

#### <a name="solution-1---use-azure-file-sync"></a>Rozwiązanie 1. Użyj usługi Azure File Sync
Usługa Azure File Sync może przekształca lokalnego systemu Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Można użyć dowolnego protokołu, który jest dostępny w systemie Windows Server oraz dostęp do danych lokalnie, w tym protokołu SMB, systemu plików NFS i protokołu FTPS. Usługa Azure File Sync działa za pośrednictwem portu 443 i dlatego można obejść ten problem dostęp do usługi Azure Files z klientów, którzy mają portu 445 zablokowane. [Dowiedz się, jak skonfigurować usługę Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Rozwiązanie 2 — użycie sieci VPN
Konfigurując sieci VPN do określonego konta magazynu, ruch zaczną za pośrednictwem bezpiecznego tunelu, w przeciwieństwie do za pośrednictwem Internetu. Postępuj zgodnie z [instrukcjami, aby skonfigurować sieci VPN](https://github.com/Azure-Samples/azure-files-samples/tree/master/point-to-site-vpn-azure-files
) dostęp do usługi Azure Files z Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Rozwiązanie 3 - Odblokuj port 445 przy pomocy z usługodawcą Internetowym / administrator IT
Praca z personelem informatycznym lub usługodawcy internetowego, aby otworzyć port 445 ruch wychodzący do [zakresów adresów IP platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Rozwiązanie 4 — Korzystanie z interfejsu API REST oparte narzędzi takich jak Storage Explorer/programu Powershell
Usługa pliki systemu Azure obsługuje również REST, oprócz protokołu SMB. Dostęp interfejsu REST działa za pośrednictwem portu 443 (standardowy protokół tcp). Istnieją różne narzędzia, które są zapisywane z użyciem interfejsu API REST, umożliwiających bogate funkcje interfejsu użytkownika. [Eksplorator usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) jest jednym z nich. [Pobieranie i instalowanie Eksploratora usługi Storage](https://azure.microsoft.com/features/storage-explorer/) i nawiąż połączenie z udziałem plików obsługiwane przez usługi Azure Files. Można również użyć [PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) który również użytkowników interfejsu API REST.


### <a name="cause-2-ntlmv1-is-enabled"></a>Przyczyny 2: Włączono NTLMv1

Błąd 53 lub błąd systemowy 87 może wystąpić, jeśli NTLMv1 komunikacji jest włączona na komputerze klienckim. Usługa pliki systemu Azure obsługuje tylko z uwierzytelniania NTLMv2. Mających włączone NTLMv1 tworzy mniej bezpiecznych klienta. W związku z tym komunikacja jest zablokowana dla usługi Azure Files. 

Aby ustalić, czy jest to przyczyną błędu, sprawdź, że następujący podklucz rejestru jest ustawiona na wartość 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Aby uzyskać więcej informacji, zobacz [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) temat w witrynie TechNet.

### <a name="solution-for-cause-2"></a>Rozwiązanie przyczyny 2

Przywróć **LmCompatibilityLevel** wartość domyślną 3 w następującym podkluczu rejestru:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Błąd 1816 "za mało zasobów jest dostępna do przetworzenia tego polecenia" przy kopiowaniu do udziału plików platformy Azure

### <a name="cause"></a>Przyczyna

Błąd 1816 ma miejsce, gdy osiągną górny limit współbieżnych otwartych dojść, które są dozwolone dla pliku na komputerze, w którym jest zainstalowany udział plików.

### <a name="solution"></a>Rozwiązanie

Zmniejsz liczbę jednoczesnych otwarte dojścia przez zamknięcie niektórych uchwyty, a następnie spróbuj ponownie. Aby uzyskać więcej informacji, zobacz [Lista kontrolna wydajności i skalowalności usługi Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

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

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Wolne kopiowania plików do i z usługi Azure Files w Windows

Niska wydajność mogą pojawiać się podczas próby transferu plików do usługi plików platformy Azure.

- Jeśli nie masz określonych minimalny wymagany rozmiar operacji We/Wy, zalecamy użycie 1 MiB jako rozmiar operacji We/Wy w pod kątem optymalnej wydajności.
-   Jeśli wiesz, końcowy rozmiar pliku, który powiększa się zapisu oraz oprogramowania nie ma problemów ze zgodnością, gdy niepisane tail do pliku zawiera zera wartość rozmiaru pliku wcześniej dokonywane co zapisu rozszerzanie zapisu.
-   Użyj metody kopiowania prawa:
    -   Użyj [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) wszelkie transferu między dwoma udziałami plików.
    -   Użyj [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) między udziałów plików na komputerze lokalnym.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Zagadnienia dotyczące Windows 8.1 lub Windows Server 2012 R2

Dla klientów z systemem Windows 8.1 lub Windows Server 2012 R2, upewnij się, że [KB3114025](https://support.microsoft.com/help/3114025) zainstalowano poprawkę. Ta poprawka zwiększa wydajność tworzenia i zamknąć dojścia.

Można uruchomić następujący skrypt, aby sprawdzić, czy zainstalowano poprawkę:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Jeśli zainstalowana jest poprawka, jest wyświetlane następujące dane wyjściowe:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Obrazy systemu Windows Server 2012 R2 w witrynie Azure Marketplace mają poprawkę KB3114025 instalowane domyślnie począwszy od grudnia 2015 r.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>Żaden folder z literą dysku w **Mój komputer**

Jeśli mapujesz udziału plików platformy Azure jako administrator przy użyciu polecenie net use wrażenie nieobecnego udziału.

### <a name="cause"></a>Przyczyna

Domyślnie Eksploratora plików Windows nie działa jako administrator. Jeśli uruchamiasz polecenie net use z wiersza polecenia z uprawnieniami administracyjnymi, mapowania dysku sieciowego jako administrator. Ponieważ mapowanymi są skoncentrowane na użytkowniku, konto użytkownika, który jest zalogowany nie wyświetla dyski, jeśli są zainstalowane w ramach konta innego użytkownika.

### <a name="solution"></a>Rozwiązanie
Instalowanie udziału z wiersza polecenia bez uprawnień administratora. Alternatywnie, można wykonać [w tym temacie w witrynie TechNet](https://technet.microsoft.com/library/ee844140.aspx) skonfigurować **EnableLinkedConnections** wartości rejestru.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Polecenie net use kończy się niepowodzeniem, jeśli konto magazynu zawiera ukośnik

### <a name="cause"></a>Przyczyna

Polecenie net use interpretuje ukośnika (/) jako opcji wiersza polecenia. Nazwa konta użytkownika rozpoczyna się od ukośnika, mapowanie dysku nie powiedzie się.

### <a name="solution"></a>Rozwiązanie

Aby obejść ten problem, można użyć jednej z następujących czynności:

- Uruchom następujące polecenie programu PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Z pliku wsadowego można uruchomić polecenie w ten sposób:

  `Echo new-smbMapping ... | powershell -command –`

- Umieść podwójne znaki cudzysłowu otaczające klawisz w celu obejścia tego problemu —, chyba że pierwszym znakiem kreski ułamkowej. Jeśli tak jest, użyć trybu interakcyjnego i wprowadź hasło, oddzielnie lub ponownie wygenerować klucze można pobrać klucza, który nie zaczyna się od ukośnika.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Aplikacja lub usługa nie może uzyskać dostępu zainstalowanego dysku usługi Azure Files

### <a name="cause"></a>Przyczyna

Dyski są instalowane dla poszczególnych użytkowników. Jeśli Twoja aplikacja lub usługa jest uruchomiona w ramach konta użytkownika innego niż ten, który zainstalował dysk, dysk nie będą widoczne w aplikacji.

### <a name="solution"></a>Rozwiązanie

Zastosuj jedno z następujących rozwiązań:

-   Zainstaluj dysk z tego samego konta użytkownika, który zawiera aplikację. Można użyć narzędzia, takiego jak narzędzie PsExec.
- Przekazać nazwę konta magazynu i klucz w polu Nazwa użytkownika i hasło Parametry sieci, użyj polecenia.
- Polecenie cmdkey można dodać poświadczeń do Menedżera poświadczeń. Robienia tego z poziomu wiersza polecenia w kontekście konta usługi, za pomocą logowania interaktywnego lub przy użyciu polecenia Uruchom jako.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mapuj udział bezpośrednio bez konieczności używania litery dysku zmapowanego. Niektóre aplikacje mogą nie ponownie literę dysku prawidłowo, więc w pełnej ścieżce UNC, może być bardziej niezawodne. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Po tych instrukcji może zostać wyświetlony następujący komunikat o błędzie, gdy uruchomisz polecenie net use dla konta usługi systemu i sieci: "Wystąpił błąd systemowy 1312. Określona sesja logowania nie istnieje. Może już mieć została zakończona." Jeśli ten problem wystąpi, upewnij się, że nazwa użytkownika, który jest przekazywany do polecenie net use zawiera informacje dotyczące domeny (na przykład: "[nazwa konta magazynu]. file.core.windows .net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Błąd "Kopiujesz plik do miejsca docelowego, który nie obsługuje szyfrowania"

Po skopiowaniu pliku za pośrednictwem sieci, plik odszyfrowany na komputerze źródłowym, przekazywane w postaci zwykłego tekstu i ponownie szyfrowane w miejscu docelowym. Jednak może zostać wyświetlony następujący błąd, gdy w przypadku próby skopiowania zaszyfrowanego pliku: "Kopiujesz plik do miejsca docelowego, który nie obsługuje szyfrowania."

### <a name="cause"></a>Przyczyna
Ten problem może wystąpić, jeśli używasz systemu szyfrowania plików (EFS). Pliki zaszyfrowane przez funkcję BitLocker mogą być kopiowane do usługi Azure Files. Jednak usługi Azure Files nie obsługuje systemu szyfrowania plików NTFS.

### <a name="workaround"></a>Obejście
Aby skopiować plik za pośrednictwem sieci, należy najpierw musisz go odszyfrować. Użyj jednej z następujących metod:

- Użyj **skopiuj /d** polecenia. Umożliwia zaszyfrowane pliki do zapisania jako zaszyfrowanych plików w lokalizacji docelowej.
- Ustaw następujący klucz rejestru:
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Typ wartości = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Wartość = 1

Należy pamiętać, że ustawienie klucza rejestru ma wpływ na wszystkie operacje kopiowania, które zostały wprowadzone do udziałów sieciowych.

## <a name="slow-enumeration-of-files-and-folders"></a>Powolne wyliczenie plików i folderów

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli na komputerze klienckim na potrzeby dużych katalogów jest brak wystarczającej ilości pamięci podręcznej.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, dostosowując **DirectoryCacheEntrySizeMax** wartości rejestru, aby umożliwić buforowanie większej listach zawartości katalogów w komputerze klienckim:

- Lokalizacja: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Wartość mane: DirectoryCacheEntrySizeMax 
- Wartość typ: DWORD
 
 
Można na przykład, ustaw ją na 0x100000 i sprawdzić, czy wydajność stają się lepsze.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Błąd AadDsTenantNotFound włączania uwierzytelniania usługi Azure Active Directory dla usługi Azure Files "Nie można zlokalizować aktywnych dzierżaw z dzierżawą identyfikator aad-tenant-id"

### <a name="cause"></a>Przyczyna

Błąd AadDsTenantNotFound się dzieje, gdy użytkownik próbuje [Włącz uwierzytelnianie usługi Azure Active Directory (AAD) dla usługi Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable) na koncie magazynu gdzie [AAD Domain Service(AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) nie jest tworzony w usłudze AAD dzierżawy skojarzonej subskrypcji.  

### <a name="solution"></a>Rozwiązanie

Włącz DS usługi AAD w dzierżawie usługi AAD, subskrypcji, wdrożoną konta magazynu. Niezbędne są uprawnienia administratora dzierżawy usługi AAD, aby utworzyć domeny zarządzanej. Jeśli nie jesteś administratorem dzierżawy usługi Azure AD, skontaktuj się z administratorem i postępuj zgodnie z szczegółowymi wskazówkami dotyczącymi [włączyć usługi Azure Active Directory Domain Services w witrynie Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybko rozwiązać problem.
