---
title: Rozwiązywanie problemów plików Azure w systemie Windows | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów plików Azure w systemie Windows
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: wgries
ms.openlocfilehash: 18f594586aa95afaa01bfda712dfc23c8aad3a36
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738551"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Rozwiązywanie problemów plików Azure w systemie Windows

W tym artykule wymieniono typowe problemy, które są powiązane pliki programu Microsoft Azure podczas nawiązywania połączenia przez klientów systemu Windows. Zapewnia także możliwe przyczyny i rozwiązania tych problemów. Oprócz kroków w tym artykule, można również użyć [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) aby upewnić się, że środowisko klienta systemu Windows ma poprawne warunki wstępne. AzFileDiagnostics automatyzuje wykrywania większość objawy wymienionych w tym artykule i ułatwia konfigurowanie środowiska, aby uzyskać optymalną wydajność. Możesz także te informacje można znaleźć w [Rozwiązywanie problemów z udziałów plików Azure](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) zapewnia czynności, aby ułatwić z problemami z udziałów plików Azure łączenie/mapowanie/instalowanie.


<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Błąd 53, 67 błąd lub 87 błąd podczas instalowania lub odinstalowania udziału plików na platformę Azure

Podczas próby zainstalowania udziału plików z lokalnymi lub z różnych centrach danych, może się pojawić następujące błędy:

- Wystąpił błąd systemowy 53. Nie można odnaleźć ścieżki sieciowej.
- Wystąpił błąd systemowy 67. Nie można odnaleźć nazwy sieci.
- Wystąpił błąd systemowy 87. Parametr jest nieprawidłowy.

### <a name="cause-1-unencrypted-communication-channel"></a>Przyczyny 1: Kanał komunikacyjny niezaszyfrowane

Ze względów bezpieczeństwa połączeń z udziałów plików na platformę Azure są zablokowane, jeśli nie jest szyfrowany kanał komunikacji, a próba połączenia nie jest wprowadzone z tym samym centrum danych, gdzie znajdują się udziały plików platformy Azure. Szyfrowanie kanału komunikacji znajduje się tylko wtedy, gdy system operacyjny klienta użytkownika obsługuje szyfrowanie protokołu SMB.

Windows 8, Windows Server 2012 i nowsze wersje każdego systemu negocjowania żądania zawierające SMB 3.0, który obsługuje szyfrowanie.

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

Połączenie z klienta, który wykonuje następujące czynności:

- Spełnia wymagania systemu Windows 8 i Windows Server 2012 lub nowszy
- Nawiązuje połączenie z maszyną wirtualną, w tym samym centrum danych, co konto magazynu platformy Azure, które służy do udziału plików na platformę Azure

### <a name="cause-2-port-445-is-blocked"></a>Przyczyny 2: Port 445 jest zablokowany.

Błąd systemowy 53 lub błąd systemu 67 może wystąpić, jeśli port 445 komunikacja wychodząca datacenter pliki Azure jest zablokowany. Aby wyświetlić podsumowanie usługodawców internetowych, które Zezwalaj lub nie zezwalaj na dostęp z portu 445, przejdź do [TechNet](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Aby zrozumieć, czy jest to powód za komunikat "Błąd systemu 53", umożliwia Portqry zapytania TCP:445 punktu końcowego. Jeśli TCP:445 punkt końcowy jest wyświetlany jako filtrowane, TCP port jest zablokowany. Oto przykładowe zapytanie:

  `g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Jeśli port TCP 445 jest zablokowany przez regułę wzdłuż ścieżki sieciowej, zostaną wyświetlone następujące dane wyjściowe:

  `TCP port 445 (microsoft-ds service): FILTERED`

Aby uzyskać więcej informacji o sposobie używania polecenia Portqry, zobacz artykuł [Description of the Portqry.exe command-line utility (Opis narzędzia wiersza polecenia Portqry.exe)](https://support.microsoft.com/help/310099).

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Praca ze swoim działem IT, aby otworzyć port 445 ruch wychodzący do [zakresów adresów IP Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="cause-3-ntlmv1-is-enabled"></a>Przyczyny 3: NTLMv1 jest włączona.

Błąd systemowy 53 lub błąd systemu: 87 może wystąpić, jeśli włączono komunikację NTLMv1 na kliencie. Usługa pliki Azure obsługuje tylko z uwierzytelniania NTLMv2. Mających włączone NTLMv1 tworzy mniej bezpiecznych klienta. W związku z tym komunikacja jest zablokowana dla plików Azure. 

Aby ustalić, czy jest to przyczyną błędu, sprawdź, czy następujący podklucz rejestru jest ustawiony na wartość 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Aby uzyskać więcej informacji, zobacz [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) temacie w witrynie TechNet.

### <a name="solution-for-cause-3"></a>Rozwiązanie dla przyczyny 3

Przywróć **LmCompatibilityLevel** wartość domyślną 3 w następującym podkluczu rejestru:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Błąd 1816 "za mało zasobów jest niedostępna do przetworzenia tego polecenia" podczas kopiowania do udziału plików na platformę Azure

### <a name="cause"></a>Przyczyna

Błąd 1816 odbywa się po osiągnięciu górny limit współbieżnych otwartych dojść, które są dozwolone dla pliku na komputerze, w którym jest zainstalowany udział plików.

### <a name="solution"></a>Rozwiązanie

Zmniejsz liczbę równoczesnych otwartych dojść zamykając niektóre dojść, a następnie spróbuj ponownie. Aby uzyskać więcej informacji, zobacz [Lista kontrolna wydajności i skalowalności magazynu Microsoft Azure](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Powolna kopiowanie plików do i z plików Azure w systemie Windows

Niska wydajność napotkać podczas przesyłania plików do usługi Azure plików.

- Jeśli użytkownik nie ma określonego minimalny wymagany rozmiar operacji We/Wy, zalecane jest użycie 1 MB jako rozmiaru we/wy do uzyskania optymalnej wydajności.
-   Jeśli znasz końcowego rozmiar pliku, który powiększa się zapisy, a oprogramowanie nie ma problemów ze zgodnością, gdy niezapisanych tail na plik zawiera zera, następnie ustaw rozmiar pliku z wyprzedzeniem zamiast wprowadzania każdego zapisu rozszerzanie zapisu.
-   Użyj metody copy prawa:
    -   Użyj [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) żadnych transferu między dwoma udziałami plików.
    -   Użyj [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) między udziałami plików na komputerze lokalnym.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Zagadnienia dotyczące Windows 8.1 lub Windows Server 2012 R2

Dla klientów z systemem Windows 8.1 lub Windows Server 2012 R2, upewnij się, że [KB3114025](https://support.microsoft.com/help/3114025) poprawka jest zainstalowana. Ta poprawka zwiększa wydajność tworzenia i zamknąć dojścia.

Można uruchomić poniższy skrypt, aby sprawdzić, czy zainstalowano poprawkę:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Jeśli zainstalowano poprawkę, wyświetlane są następujące dane wyjściowe:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Obrazy systemu Windows Server 2012 R2 w portalu Azure Marketplace mają poprawki KB3114025 instalowane domyślnie począwszy od grudnia 2015 r.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>Nie folderu z literą dysku w **Mój komputer**

Jeśli mapujesz udziału plików na platformę Azure jako administrator przy użyciu polecenie net use udział sprawia wrażenie nieobecnego.

### <a name="cause"></a>Przyczyna

Domyślnie Eksploratorze plików systemu Windows nie działa jako administrator. Jeśli uruchomisz polecenie net use z wiersza polecenia z uprawnieniami administracyjnymi, mapowania dysku sieciowego jako administrator. Ponieważ zamapowanych dysków są skoncentrowane na użytkowniku, konta użytkownika, który jest zalogowany nie wyświetla dyski, jeśli są one zainstalowane przy użyciu konta innego użytkownika.

### <a name="solution"></a>Rozwiązanie
Instalowanie udziału z wiersza polecenia bez uprawnień administratora. Alternatywnie, można wykonać [w tym temacie w witrynie TechNet](https://technet.microsoft.com/library/ee844140.aspx) skonfigurować **EnableLinkedConnections** wartości rejestru.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Polecenie net use zakończy się niepowodzeniem, jeśli konto magazynu zawiera ukośnik

### <a name="cause"></a>Przyczyna

Polecenie net use ukośnika (/) są interpretowane jako opcji wiersza polecenia. Nazwa konta użytkownika rozpoczyna się od ukośnika, mapowanie dysku nie powiedzie się.

### <a name="solution"></a>Rozwiązanie

Aby obejść ten problem, można użyć jednej z następujących czynności:

- Uruchom następujące polecenie programu PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc" `

  Z pliku wsadowego można uruchomić polecenie w ten sposób:

  `Echo new-smbMapping ... | powershell -command –`

- Umieść znaki cudzysłowu wokół klawisz, aby obejść ten problem —, chyba że ukośnik to pierwszy znak. Jeśli tak jest, użyj trybie interakcyjnym i wprowadź swoje hasło oddzielnie lub ponownie wygenerować klucze, aby uzyskać klucz, który nie zaczyna się od ukośnika.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Aplikacja lub usługa nie może uzyskać dostępu zainstalowanego dysku plików Azure

### <a name="cause"></a>Przyczyna

Dyski są zainstalowane dla poszczególnych użytkowników. Jeśli aplikacji lub usługi jest uruchomiony w ramach innego konta użytkownika niż konto, którego zainstalowany dysk, dysk nie będą widoczne w aplikacji.

### <a name="solution"></a>Rozwiązanie

Zastosuj jedno z następujących rozwiązań:

-   Zainstaluj dysk z tego samego konta użytkownika, który zawiera aplikację. Można użyć narzędzia, takiego jak narzędzia PsExec.
- Podaj nazwę konta magazynu i klucz w nazwie użytkownika i hasło Parametry sieci, użyj polecenia.
- Polecenie cmdkey umożliwia dodawanie poświadczeń do Menedżera poświadczeń. Wykonać z poziomu wiersza polecenia w kontekście konta usługi, za pomocą logowania interaktywnego lub za pomocą polecenia Uruchom jako.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mapowanie udziału bezpośrednio, bez za pomocą litery dysku zmapowanego. Niektóre aplikacje mogą nie ponownie literę dysku poprawnie, dlatego przy użyciu pełnej ścieżki UNC może być bardziej niezawodne. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Po wykonaniu tych instrukcji, zostanie zgłoszony następujący komunikat o błędzie podczas uruchamiania polecenie net use dla konta usługi systemu i sieci: "Wystąpił błąd systemowy 1312. Określona sesja logowania nie istnieje. Go może została już zakończona." W takim przypadku upewnij się, że nazwa użytkownika, która została przekazana do metody net use zawiera informacje o domenie (na przykład: "[nazwa konta magazynu]. file.core.windows .net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Błąd "Kopiujesz plik do lokalizacji docelowej, która nie obsługuje szyfrowania"

Po skopiowaniu pliku za pośrednictwem sieci, plik jest odszyfrowywany na komputerze źródłowym, przekazywane w postaci zwykłego tekstu i ponownie szyfrowane w miejscu docelowym. Jednak jeśli próbujesz skopiować zaszyfrowany plik może być wyświetlony następujący błąd: "Kopiujesz plik do lokalizacji docelowej, która nie obsługuje szyfrowania."

### <a name="cause"></a>Przyczyna
Ten problem może wystąpić, jeśli używasz systemu szyfrowania plików (EFS). Mogą zostać skopiowane pliki zaszyfrowane przez funkcję BitLocker do usługi pliki Azure. Jednak usługi pliki Azure nie obsługuje systemu szyfrowania plików NTFS.

### <a name="workaround"></a>Obejście
Aby skopiować pliku za pośrednictwem sieci, należy najpierw musisz go odszyfrować. Użyj jednej z następujących metod:

- Użyj **skopiuj /d** polecenia. Umożliwia on zaszyfrowane pliki można zapisać jako zaszyfrowanych plików w lokalizacji docelowej.
- Należy ustawić następujący klucz rejestru:
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Typ wartości = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Wartość = 1

Należy pamiętać, że ustawienie klucza rejestru ma wpływ na wszystkie operacje kopiowania, wprowadzone w udziałach sieciowych.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
