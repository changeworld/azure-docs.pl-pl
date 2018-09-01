---
title: Informacje o wersji agenta usługi Azure File Sync | Dokumentacja firmy Microsoft
description: Informacje o wersji agenta usługi Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 08/30/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: cc1b89ff94b4d4dc0b191512b110521d5fa05a7a
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344370"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Informacje o wersji agenta usługi Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Instalacje systemów Windows Server są przekształcane w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

W tym artykule udostępniono informacje o obsługiwanych wersjach agenta usługi Azure File Sync.

## <a name="supported-versions"></a>Obsługiwane wersje
Następujące wersje są obsługiwane przez agenta usługi Azure File Sync:

| Punkt kontrolny | Numer wersji agenta | Data wydania | Stan |
|----|----------------------|--------------|------------------|
| Pakiet zbiorczy aktualizacji z sierpnia | 3.2.0.0 | 15 sierpnia 2018 r. | Obsługiwane (zalecana wersja) |
| Ogólna dostępność | 3.1.0.0 | 19 lipca 2018 r. | Obsługiwane |
| Pakiet zbiorczy aktualizacji czerwca | 3.0.13.0 | 29 czerwca 2018 r. | Wersja agenta wygaśnie w dniu 1 października 2018 r. |
| Odśwież 2 | 3.0.12.0 | 22 maja 2018 r. | Wersja agenta wygaśnie w dniu 1 października 2018 r. |
| Pakiet zbiorczy aktualizacji kwietnia | 2.3.0.0 | 8 maja 2018 r. | Wersja agenta wygaśnie w dniu 1 października 2018 r. |
| Pakiet zbiorczy aktualizacji z marca | 2.2.0.0 | 12 marca 2018 r. | Wersja agenta wygaśnie w dniu 1 października 2018 r. |
| Pakiet zbiorczy aktualizacji z lutego | 2.1.0.0 | 28 lutego 2018 r. | Wersja agenta wygaśnie w dniu 1 października 2018 r. |
| Odśwież 1 | 2.0.11.0 | 8 lutego 2018 r. | Wersja agenta wygaśnie w dniu 1 października 2018 r. |
| Pakiet zbiorczy aktualizacji stycznia | 1.4.0.0 | 8 stycznia 2018 r. | Wersja agenta wygaśnie w dniu 1 października 2018 r. |
| Pakiet zbiorczy aktualizacji z listopada | 1.3.0.0 | 30 listopada 2017 r. | Wersja agenta wygaśnie w dniu 1 października 2018 r. |
| Pakiet zbiorczy aktualizacji października | 1.2.0.0 | 31 października 2017 r. | Wersja agenta wygaśnie w dniu 1 października 2018 r. |
| Początkowa wersja zapoznawcza | 1.1.0.0 | 26 września 2017 r. | Wersja agenta wygaśnie w dniu 1 października 2018 r. |

### <a name="azure-file-sync-agent-update-policy"></a>Zasady aktualizacji agenta usługi Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-3200"></a>Wersja agenta 3.2.0.0
Poniższe informacje o wersji dotyczą wersji 3.2.0.0 agenta usługi Azure File Sync wydanej 15 sierpnia 2018. Te informacje są uzupełnieniem informacji o wersji 3.1.0.0 informacje o wersji.

Ta wersja zawiera następujące poprawki:
- Synchronizacji kończy się niepowodzeniem błąd braku pamięci (0x8007000e) z powodu przeciek pamięci

## <a name="agent-version-3100"></a>Wersja agenta 3.1.0.0
Poniższe informacje o wersji dotyczą wersji 3.1.0.0 agenta usługi Azure File Sync (wydanej 19 lipca 2018 r.).

### <a name="evaluation-tool"></a>Narzędzie oceny
Przed wdrożeniem usługi Azure File Sync, należy sprawdzić, czy jest on zgodny z systemu za pomocą narzędzia oceny usługi Azure File Sync. To narzędzie jest AzureRM PowerShell polecenia cmdlet, która sprawdza pod kątem potencjalnych problemów za pomocą systemu plików i zestaw danych, takimi jak nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. I instrukcje dotyczące instalacji, zobacz [uruchamiania narzędzia oceny](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-planning#evaluation-tool) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat sposobu instalowania i konfigurowania agenta usługi Azure File Sync z systemem Windows Server, zobacz [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania usługi Azure File Sync](storage-sync-files-deployment-guide.md).

- Musi być zainstalowany pakiet instalacyjny agenta przy użyciu uprawnień podniesionych uprawnień (Administrator).
- Agent nie jest obsługiwany w opcjach wdrażania systemu Windows Server Core lub Nano Server.
- Agent jest obsługiwany wyłącznie w systemach Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GB pamięci fizycznej.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punkty końcowe serwera znajduje się na woluminie, który zawiera katalog informacji (SVI) woluminu systemowego skompresowany. Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługi Azure File Sync](storage-sync-files-troubleshoot.md).
- Nie używaj osłon plików Menedżera zasobów serwera plików ani innych osłon plików. Osłony plików mogą powodować nieskończone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Uruchamianie programu sysprep na serwerze, na którym jest zainstalowany agent usługi Azure File Sync nie jest obsługiwana i może prowadzić do nieoczekiwanych wyników. Rejestracja agenta instalacji i serwer powinien występować po wdrażanie obrazu serwera i zakończeniu miniinstalacji programu sysprep.
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
 
### <a name="server-endpoint"></a>Punkt końcowy serwera
- Punkt końcowy serwera można tworzyć tylko na woluminie NTFS. Systemy plików ReFS, FAT, FAT32 i inne nie są obecnie obsługiwane przez usługę Azure File Sync.
- Pliki warstwowe będzie ono używane, jeśli pliki nie zostaną odwołane przed usunięciem punktu końcowego serwera.
- Chmura obsługi warstw nie jest obsługiwana na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, należy wyłączyć obsługi warstw podczas tworzenia punktu końcowego serwera w chmurze.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach punktu końcowego serwera.
- Nazwa serwera w portalu nie są aktualizowane po zmianie nazwy serwera. Aby zaktualizować nazwę serwera w portalu, wyrejestrować, a następnie ponownie zarejestrować serwer.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Usługa Azure File Sync nie obsługuje bezpośrednio wprowadzania zmian do udziału plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw konieczne ich odnalezienie przez zadanie wykrywania zmian usługi Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze, co 24 godziny. Ponadto zmiany wprowadzone do udziału plików platformy Azure za pośrednictwem protokołu REST nie może zaktualizować SMB godzina ostatniej modyfikacji i nie będą widoczne jako zmiany przez sync.
- Usługa synchronizacji magazynu i/lub konta magazynu można przenieść do innej grupy zasobów lub subskrypcji. Jeśli konto magazynu zostanie przeniesiony, należy udzielić dostępu usługi hybrydowe File Sync do konta magazynu (zobacz [upewnij się, usługa Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.

## <a name="agent-version-30130"></a>Wersja agenta 3.0.13.0
Poniższe informacje o wersji dotyczą wersji 3.0.13.0 agenta usługi Azure File Sync wydanej 29 czerwca 2018 r. Te informacje są uzupełnieniem informacji o wersji 3.0.12.0 informacje o wersji.

Ta wersja zawiera następujące poprawki:
- Punkty ponownej analizy, Synchronizacja zakończy się niepowodzeniem, jeśli serwer jest dodawany do istniejącej grupy synchronizacji, jeśli istnieje w lokalizacji punktu końcowego serwera na serwerze.

## <a name="agent-version-30120"></a>Wersja agenta 3.0.12.0
Poniższe informacje o wersji dotyczą wersji 3.0.12.0 agenta usługi Azure File Sync (wydanej 22 maja 2018 r.).

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat sposobu instalowania i konfigurowania agenta usługi Azure File Sync z systemem Windows Server, zobacz [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania usługi Azure File Sync](storage-sync-files-deployment-guide.md).

- Musi być zainstalowany pakiet instalacyjny agenta przy użyciu uprawnień podniesionych uprawnień (Administrator).
- Agent nie jest obsługiwany w opcjach wdrażania systemu Windows Server Core lub Nano Server.
- Agent jest obsługiwany wyłącznie w systemach Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GB pamięci fizycznej.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punkty końcowe serwera znajduje się na woluminie, który zawiera katalog informacji (SVI) woluminu systemowego skompresowany. Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługi Azure File Sync](storage-sync-files-troubleshoot.md).
- Nie używaj osłon plików Menedżera zasobów serwera plików ani innych osłon plików. Osłony plików mogą powodować nieskończone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Uruchamianie programu sysprep na serwerze, na którym jest zainstalowany agent usługi Azure File Sync nie jest obsługiwana i może prowadzić do nieoczekiwanych wyników. Rejestracja agenta instalacji i serwer powinien występować po wdrażanie obrazu serwera i zakończeniu miniinstalacji programu sysprep.
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
- Chmura obsługi warstw nie jest obsługiwana na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, należy wyłączyć obsługi warstw podczas tworzenia punktu końcowego serwera w chmurze.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach punktu końcowego serwera.
- Pliki warstwowe będzie ono używane, jeśli pliki nie zostaną odwołane przed usunięciem punktu końcowego serwera.
 
### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.

## <a name="agent-version-2300"></a>Wersja agenta 2.3.0.0
Poniższe informacje o wersji dotyczą wersji 2.3.0.0 agenta usługi Azure File Sync wydana 8 maja 2018 r. Te informacje są uzupełnieniem informacji o wersji 2.0.11.0.

Ta wersja zawiera następujące poprawki:
- Aktualizacje agenta mogą wykraczać, jeśli nie spowoduje usunięcia sterownika filtru obsługi warstw w chmurze.
- Podczas synchronizowania wielu plików, może zmniejszyć wydajność synchronizacji.

## <a name="agent-version-2200"></a>Wersja agenta 2.2.0.0
Poniższe informacje o wersji dotyczą wersji 2.2.0.0 agenta usługi Azure File Sync wydanej 12 marca 2018 r.  Te informacje są uzupełnieniem informacji o wersji 2.1.0.0 i 2.0.11.0 informacje o wersji

Instalacja v2.1.0.0 dla niektórych klientów zakończy się niepowodzeniem z powodu FileSyncSvc nie zatrzymanie. Ta aktualizacja umożliwia rozwiązanie tego problemu.

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
Aby uzyskać więcej informacji na temat sposobu instalowania i konfigurowania agenta usługi Azure File Sync z systemem Windows Server, zobacz [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania usługi Azure File Sync](storage-sync-files-deployment-guide.md).

- Musi być zainstalowany pakiet instalacyjny agenta (MSI) z podniesionymi uprawnieniami (administrator).
- Agent nie jest obsługiwany w opcjach wdrażania systemu Windows Server Core lub Nano Server.
- Agent jest obsługiwany wyłącznie w systemach Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GB pamięci fizycznej.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługi Azure File Sync](storage-sync-files-troubleshoot.md).
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
- Wydajność synchronizacji szybkie przestrzeni nazw odzyskiwania po awarii została znacznie zwiększona.
- Usuwanie dużej liczby (ponad 10 000) katalogów nie musi być wykonana w partiach, za pomocą wersji 2 *.
 
### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Zmiany względem poprzedniej wersji: nowe pliki są organizowane w warstwy w ciągu 1 godziny (wcześniej w ciągu 32 godzin). To ustawienie zasad obsługi warstw można zmieniać. Udostępniono też polecenie cmdlet programu PowerShell służące do umieszczania w warstwie na żądanie. Dzięki temu poleceniu cmdlet możliwa jest bardziej wydajna obsługa warstw bez konieczności oczekiwania na proces w tle.
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.
- Zmiany względem poprzedniej wersji: pliki są teraz pobierane jako pliki warstwowe na innych serwerach, pod warunkiem, że są nowe lub były wcześniej plikami warstwowym.

## <a name="agent-version-1100"></a>Wersja agenta 1.1.0.0
Poniższe informacje o wersji dotyczą wersji 1.1.0.0 agenta usługi Azure File Sync (wydanej 9 września 2017 r. — początkowa wersja zapoznawcza). 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat sposobu instalowania i konfigurowania agenta usługi Azure File Sync z systemem Windows Server, zobacz [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania usługi Azure File Sync](storage-sync-files-deployment-guide.md).

- Musi być zainstalowany pakiet instalacyjny agenta (MSI) z podniesionymi uprawnieniami (administrator).
- Agent nie jest obsługiwany w opcjach wdrażania systemu Windows Server Core lub Nano Server.
- Agent jest obsługiwany tylko w systemie Windows Server 2016 i 2012 R2.
- Agent wymaga co najmniej 2 GB pamięci fizycznej.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługi Azure File Sync](storage-sync-files-troubleshoot.md).
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
