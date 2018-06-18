---
title: Informacje o wersji agenta usługi Azure File Sync (wersja zapoznawcza) | Microsoft Docs
description: Informacje o wersji dla agenta synchronizacji plików Azure (wersja zapoznawcza).
services: storage
author: wmgries
manager: aungoo
ms.service: storage
ms.topic: article
ms.date: 05/31/2018
ms.author: wgries
ms.openlocfilehash: 946311c42088d3a5840eb35387c8a552d3d5d70f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34735648"
---
# <a name="release-notes-for-the-azure-file-sync-agent-preview"></a>Informacje o wersji agenta usługi Azure File Sync (wersja zapoznawcza)
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Instalacje systemów Windows Server są przekształcane w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

W tym artykule udostępniono informacje o obsługiwanych wersjach agenta usługi Azure File Sync.

## <a name="supported-versions"></a>Obsługiwane wersje
Następujące wersje są obsługiwane przez agenta usługi Azure File Sync:

| Punkt kontrolny | Numer wersji agenta | Data wydania | Stan |
|----|----------------------|--------------|------------------|
| Odśwież 2 | 3.0.12.0 | 22 maja 2018 r. | Obsługiwane (zalecana wersja) |
| Pakiet zbiorczy aktualizacji kwietnia | 2.3.0.0 | 8 maja 2018 | Obsługiwane |
| Pakiet zbiorczy aktualizacji z marca | 2.2.0.0 | 12 marca 2018 | Obsługiwane |
| Pakiet zbiorczy aktualizacji lutego | 2.1.0.0 | 28 lutego 2018 r. | Obsługiwane |
| Odśwież 1 | 2.0.11.0 | 8 lutego 2018 r. | Obsługiwane |
| Pakiet zbiorczy aktualizacji stycznia | 1.4.0.0 | 8 stycznia 2018 | Obsługiwane |
| Pakiet zbiorczy aktualizacji z listopada | 1.3.0.0 | 30 listopada 2017 r. | Obsługiwane |
| Pakiet zbiorczy aktualizacji z października | 1.2.0.0 | 31 października 2017 r. | Obsługiwane |
| Początkowa wersja zapoznawcza | 1.1.0.0 | 26 września 2017 r. | Obsługiwane |

### <a name="azure-file-sync-agent-update-policy"></a>Zasady aktualizacji agenta usługi Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-30120"></a>Wersja agenta 3.0.12.0
Poniższe informacje o wersji dotyczą wersji 3.0.12.0 agent synchronizacji plików Azure (22 maja 2018 wydane).

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat sposobu instalowania i konfigurowania agenta usługi Azure File Sync z systemem Windows Server, zobacz [Planning for an Azure File Sync (preview) deployment (Planowanie wdrożenia usługi Azure File Sync — wersja zapoznawcza)](storage-sync-files-planning.md) i [How to deploy Azure File Sync (preview) (Jak wdrożyć usługę Azure File Sync — wersja zapoznawcza)](storage-sync-files-deployment-guide.md).

- Musi być zainstalowany pakiet instalacyjny agenta z podwyższonym poziomem uprawnień (Administrator) uprawnieniami.
- Agent nie jest obsługiwana w opcji wdrażania systemu Windows Server Core lub Nano Server.
- Agent jest obsługiwany wyłącznie w systemach Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GB pamięci fizycznej.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punkty końcowe serwera znajduje się na woluminie, który ma skompresowane katalogu informacji (SVI) woluminu systemowego. Tej konfiguracji będzie prowadzić do nieoczekiwanych wyników.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Troubleshoot Azure File Sync (preview) (Rozwiązywanie problemów z usługą Azure File Sync — wersja zapoznawcza)](storage-sync-files-troubleshoot.md).
- Nie używaj osłon plików Menedżera zasobów serwera plików ani innych osłon plików. Osłony plików mogą powodować nieskończone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Program sysprep na serwerze, na którym jest zainstalowany agent synchronizacji plików Azure nie jest obsługiwana i może prowadzić do nieoczekiwanych wyników. Rejestracja agenta instalacji i serwer powinien wystąpić po wdrażania obrazu serwera i zakończeniu miniinstalacji programu sysprep.
- Deduplikacja danych i obsługa warstw w chmurze nie są obsługiwane w tym samym woluminie.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Ścieżki dłuższe niż 2048 znaków.
- Część DACL (poufna lista kontroli dostępu) deskryptora zabezpieczeń, jeśli jest większa niż 2 KB. Ten problem występuje tylko wtedy, gdy masz więcej niż około 40 wpisów kontroli dostępu (ACE) dla jednego elementu.
- Część SACL (systemowa lista kontroli dostępu) deskryptora zabezpieczeń, która jest używana do przeprowadzania inspekcji.
- Atrybuty rozszerzone.
- Alternatywne strumienie danych.
- Punkty ponownej analizy.
- Twarde linki.
- Kompresja (jeśli jest ustawiona w pliku serwera) nie jest zachowywana, gdy zmiany z innych punktów końcowych są synchronizowane z tym plikiem.
- Każdy plik zaszyfrowany za pomocą systemu szyfrowania plików (lub innego szyfrowania trybu użytkownika), który uniemożliwia usłudze odczytywanie danych. 
    
    > [!Note]  
    > Usługa Azure File Sync zawsze szyfruje dane podczas przesyłania. Dane magazynowane na platformie Azure są zawsze zaszyfrowane.
 
### <a name="server-endpoints"></a>Punkty końcowe serwera
- Punkt końcowy serwera można tworzyć tylko na woluminie NTFS. Systemy plików ReFS, FAT, FAT32 i inne nie są obecnie obsługiwane przez usługę Azure File Sync.
- Chmura obsługi nie jest obsługiwana w woluminie systemowym. Można utworzyć punktu końcowego serwera w woluminie systemowym, wyłącz chmury, dodając funkcje warstw podczas tworzenia serwera punktu końcowego.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach punktu końcowego serwera.
- Pliki warstwowych będzie ono używane, jeśli pliki nie zostaną odwołane przed usunięciem serwera punktu końcowego.
 
### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.

## <a name="agent-version-2300"></a>Wersja agenta 2.3.0.0
Poniższe informacje o wersji dotyczą wersji 2.3.0.0 agent synchronizacji plików Azure wydanej 8 maja 2018. Te informacje są uzupełnieniem informacji o wersji 2.0.11.0.

W tej wersji dodano następujące poprawki:
- Mogą wykraczać aktualizacji agenta, jeśli nie spowoduje usunięcia sterownik filtru warstw w chmurze.
- Podczas synchronizowania wiele plików, może zmniejszyć wydajność synchronizacji.

## <a name="agent-version-2200"></a>Wersja agenta 2.2.0.0
Poniższe informacje o wersji dotyczą wersji 2.2.0.0 wydane 12 marca 2018 agent synchronizacji plików Azure.  Te informacje są oprócz wymienionych wersji 2.1.0.0 i 2.0.11.0 informacje o wersji

Instalacja v2.1.0.0 niektórzy klienci będą się kończyć niepowodzeniem z powodu FileSyncSvc nie zatrzymywanie. Ta aktualizacja umożliwia rozwiązanie tego problemu.

## <a name="agent-version-2100"></a>Wersja agenta 2.1.0.0
Poniższe informacje o wersji dotyczą wersji 2.1.0.0 agenta usługi Azure File Sync wydanej 28 lutego 2018 r. Te informacje są uzupełnieniem informacji o wersji 2.0.11.0.

W tej wersji dokonano następujących zmian:
- Poprawa obsługi klastra trybu failover.
- Poprawa niezawodnej obsługi plików warstwowych.
- Obsługa instalacji agenta na maszynach kontrolera domeny dodanych do środowiska domeny systemu Windows Server 2008 R2.
- Rozwiązane w tej wersji: rozwiązanie problemu związanego z generowaniem nadmiernej ilości danych diagnostycznych na serwerach zawierających wiele plików.
- Poprawa obsługi błędów związanych z błędami sesji.
- Poprawa obsługi błędów związanych z problemami dotyczącymi transferu plików.
- Zmiany w tej wersji: domyślny interwał uruchamiania obsługi warstw w chmurze (jeśli jest włączona w punkcie końcowym serwera) wynosi teraz 1 godzinę. 
- Problem związany z tymczasowym blokowaniem: przenoszenie zasobów usługi Azure File Sync (usługa synchronizacji magazynu) do nowej subskrypcji platformy Azure.

## <a name="agent-version-20110"></a>Wersja agenta 2.0.11.0
Poniższe informacje o wersji dotyczą wersji 2.0.11.0 agenta usługi Azure File Sync (wydanej 9 lutego 2018 r.). 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat sposobu instalowania i konfigurowania agenta usługi Azure File Sync z systemem Windows Server, zobacz [Planning for an Azure File Sync (preview) deployment (Planowanie wdrożenia usługi Azure File Sync — wersja zapoznawcza)](storage-sync-files-planning.md) i [How to deploy Azure File Sync (preview) (Jak wdrożyć usługę Azure File Sync — wersja zapoznawcza)](storage-sync-files-deployment-guide.md).

- Musi być zainstalowany pakiet instalacyjny agenta (MSI) z podniesionymi uprawnieniami (administrator).
- Agent nie jest obsługiwany w opcjach wdrażania systemu Windows Server Core lub Nano Server.
- Agent jest obsługiwany wyłącznie w systemach Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GB pamięci fizycznej.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Troubleshoot Azure File Sync (preview) (Rozwiązywanie problemów z usługą Azure File Sync — wersja zapoznawcza)](storage-sync-files-troubleshoot.md).
- W tej wersji dodano obsługę usługi DFS-R. Aby uzyskać więcej informacji, zobacz [przewodnik planowania](storage-sync-files-planning.md#distributed-file-system-dfs).
- Nie używaj osłon plików Menedżera zasobów serwera plików ani innych osłon plików. Osłony plików mogą powodować nieskończone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Duplikowanie zarejestrowanych serwerów (w tym klonowanie maszyny wirtualnej) może prowadzić do nieoczekiwanych wyników. W szczególności synchronizacja może nigdy nie być zbieżna.
- Deduplikacja danych i obsługa warstw w chmurze nie są obsługiwane w tym samym woluminie.
 
### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Ścieżki dłuższe niż 2048 znaków.
- Część DACL (poufna lista kontroli dostępu) deskryptora zabezpieczeń, jeśli jest większa niż 2 KB. Ten problem występuje tylko wtedy, gdy masz więcej niż około 40 wpisów kontroli dostępu (ACE) dla jednego elementu.
- Część SACL (systemowa lista kontroli dostępu) deskryptora zabezpieczeń, która jest używana do przeprowadzania inspekcji.
- Atrybuty rozszerzone.
- Alternatywne strumienie danych.
- Punkty ponownej analizy.
- Twarde linki.
- Kompresja (jeśli jest ustawiona w pliku serwera) nie jest zachowywana, gdy zmiany z innych punktów końcowych są synchronizowane z tym plikiem.
- Każdy plik zaszyfrowany za pomocą systemu szyfrowania plików (lub innego szyfrowania trybu użytkownika), który uniemożliwia usłudze odczytywanie danych. 
    
    > [!Note]  
    > Usługa Azure File Sync zawsze szyfruje dane podczas przesyłania. Dane magazynowane na platformie Azure są zawsze zaszyfrowane.
 
### <a name="server-endpoints"></a>Punkty końcowe serwera
- Punkt końcowy serwera można tworzyć tylko na woluminie NTFS. Systemy plików ReFS, FAT, FAT32 i inne nie są obecnie obsługiwane przez usługę Azure File Sync.
- Punkt końcowy serwera nie może być na woluminie systemowym. Na przykład ścieżka C:\MojFolder nie jest dopuszczalna, chyba że C:\MojFolder jest punktem instalacji.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- W tej wersji dodano obsługę katalogu głównego synchronizacji będącego katalogiem głównym woluminu.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach punktu końcowego serwera.
- Zmiany w tej wersji: dodano nowe zdarzenia do śledzenia całkowitego czasu wykonania dla obsługi warstw w chmurze (EventID: 9016); postęp przekazywania synchronizacji (EventID: 9302); lista plików, które nie zostały zsynchronizowane (EventID: 9900).
- Ulepszone w tej wersji: 
- Znacznie zwiększa się wydajność synchronizacji szybkiego DR przestrzeni nazw.
- Usuwanie katalogów dużą liczbą (ponad 10 000) nie musi być przeprowadzane w partiach o v2 *.
 
### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Zmiany względem poprzedniej wersji: nowe pliki są organizowane w warstwy w ciągu 1 godziny (wcześniej w ciągu 32 godzin). To ustawienie zasad obsługi warstw można zmieniać. Udostępniono też polecenie cmdlet programu PowerShell służące do umieszczania w warstwie na żądanie. Dzięki temu poleceniu cmdlet możliwa jest bardziej wydajna obsługa warstw bez konieczności oczekiwania na proces w tle.
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.
- Zmiany względem poprzedniej wersji: pliki są teraz pobierane jako pliki warstwowe na innych serwerach, pod warunkiem, że są nowe lub były wcześniej plikami warstwowym.

## <a name="agent-version-1100"></a>Wersja agenta 1.1.0.0
Poniższe informacje o wersji dotyczą wersji 1.1.0.0 agenta usługi Azure File Sync (wydanej 9 września 2017 r. — początkowa wersja zapoznawcza). 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat sposobu instalowania i konfigurowania agenta usługi Azure File Sync z systemem Windows Server, zobacz [Planning for an Azure File Sync (preview) deployment (Planowanie wdrożenia usługi Azure File Sync — wersja zapoznawcza)](storage-sync-files-planning.md) i [How to deploy Azure File Sync (preview) (Jak wdrożyć usługę Azure File Sync — wersja zapoznawcza)](storage-sync-files-deployment-guide.md).

- Musi być zainstalowany pakiet instalacyjny agenta (MSI) z podniesionymi uprawnieniami (administrator).
- Agent nie jest obsługiwany w opcjach wdrażania systemu Windows Server Core lub Nano Server.
- Agent jest obsługiwany tylko w systemie Windows Server 2016 i 2012 R2.
- Agent wymaga co najmniej 2 GB pamięci fizycznej.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Troubleshoot Azure File Sync (preview) (Rozwiązywanie problemów z usługą Azure File Sync — wersja zapoznawcza)](storage-sync-files-troubleshoot.md).
- Nie używaj osłon plików Menedżera zasobów serwera plików ani innych osłon plików. Osłony plików mogą powodować nieskończone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Duplikowanie zarejestrowanych serwerów (w tym klonowanie maszyny wirtualnej) może prowadzić do nieoczekiwanych wyników. W szczególności synchronizacja może nigdy nie być zbieżna.
- Deduplikacja danych i obsługa warstw w chmurze nie są obsługiwane w tym samym woluminie.
 
### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Ścieżki dłuższe niż 2048 znaków.
- Część DACL deskryptora zabezpieczeń, jeśli jest większa niż 2 KB. Ten problem występuje tylko wtedy, gdy masz więcej niż około 40 wpisów kontroli dostępu (ACE) dla jednego elementu.
- Część SACL deskryptora zabezpieczeń, która jest używana do przeprowadzania inspekcji.
- Atrybuty rozszerzone.
- Alternatywne strumienie danych.
- Punkty ponownej analizy.
- Twarde linki.
- Kompresja (jeśli jest ustawiona w pliku serwera) nie jest zachowywana, gdy zmiany z innych punktów końcowych są synchronizowane z tym plikiem.
- Każdy plik zaszyfrowany za pomocą systemu szyfrowania plików (lub innego szyfrowania trybu użytkownika), który uniemożliwia usłudze odczytywanie danych. 
    
    > [!Note]  
    > Usługa Azure File Sync zawsze szyfruje dane podczas przesyłania. Dane magazynowane na platformie Azure są zawsze zaszyfrowane.
 
### <a name="server-endpoints"></a>Punkty końcowe serwera
- Punkt końcowy serwera można tworzyć tylko na woluminie NTFS. Systemy plików ReFS, FAT, FAT32 i inne nie są obecnie obsługiwane przez usługę Azure File Sync.
- Punkt końcowy serwera nie może być na woluminie systemowym. Na przykład ścieżka C:\MojFolder nie jest dopuszczalna, chyba że C:\MojFolder jest punktem instalacji.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z woluminami CSV.
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Jednorazowe usuwanie dużej liczby (ponad 10 000) katalogów z serwera może powodować błędy synchronizacji. Katalogi należy usuwać partiami zawierającymi mniej niż 10 000 katalogów. Przed usunięciem następnej partii należy się upewnić, że poprzednia operacja usuwania została pomyślnie zsynchronizowana.
- Punkt końcowy serwera w folderze głównym woluminu nie jest obecnie obsługiwany.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach punktu końcowego serwera.
 
### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- W celu zapewnienia, że pliki można poprawnie odwołać, przez maksymalnie 32 godziny system może nie być w stanie automatycznie obsługiwać warstw nowych lub zmienionych plików. Ten proces obejmuje obsługę warstw po raz pierwszy po skonfigurowaniu nowego punktu końcowego serwera. Udostępniono też polecenie cmdlet programu PowerShell służące do umieszczania w warstwie na żądanie. Dzięki temu poleceniu cmdlet możliwa jest bardziej wydajna obsługa warstw bez konieczności oczekiwania na proces w tle.
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.
