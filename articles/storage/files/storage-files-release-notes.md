---
title: Informacje o wersji agenta usługi Azure File Sync | Dokumentacja firmy Microsoft
description: Informacje o wersji agenta usługi Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 10/10/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 6f39088416f4b2c3d9b04a3d83c41c8738b2c595
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079501"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Informacje o wersji agenta usługi Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Instalacje systemów Windows Server są przekształcane w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

W tym artykule udostępniono informacje o obsługiwanych wersjach agenta usługi Azure File Sync.

## <a name="supported-versions"></a>Obsługiwane wersje
Następujące wersje są obsługiwane przez agenta usługi Azure File Sync:

| Punkt kontrolny | Numer wersji agenta | Data wydania | Stan |
|----|----------------------|--------------|------------------|
| Pakiet zbiorczy aktualizacji września | 3.3.0.0 | 24 września 2018 r. | Obsługiwane (zalecana wersja) |
| Pakiet zbiorczy aktualizacji z sierpnia | 3.2.0.0 | 15 sierpnia 2018 r. | Obsługiwane |
| Ogólna dostępność | 3.1.0.0 | 19 lipca 2018 r. | Obsługiwane |
| Pakiet zbiorczy aktualizacji czerwca | 3.0.13.0 | 29 czerwca 2018 r. | Nie jest obsługiwana — wersja agenta wygasła w dniu 1 października 2018 r. |
| Odśwież 2 | 3.0.12.0 | 22 maja 2018 r. | Nie jest obsługiwana — wersja agenta wygasła w dniu 1 października 2018 r. |
| Pakiet zbiorczy aktualizacji kwietnia | 2.3.0.0 | 8 maja 2018 r. | Nie jest obsługiwana — wersja agenta wygasła w dniu 1 października 2018 r. |
| Pakiet zbiorczy aktualizacji z marca | 2.2.0.0 | 12 marca 2018 r. | Nie jest obsługiwana — wersja agenta wygasła w dniu 1 października 2018 r. |
| Pakiet zbiorczy aktualizacji z lutego | 2.1.0.0 | 28 lutego 2018 r. | Nie jest obsługiwana — wersja agenta wygasła w dniu 1 października 2018 r. |
| Odśwież 1 | 2.0.11.0 | 8 lutego 2018 r. | Nie jest obsługiwana — wersja agenta wygasła w dniu 1 października 2018 r. |
| Pakiet zbiorczy aktualizacji stycznia | 1.4.0.0 | 8 stycznia 2018 r. | Nie jest obsługiwana — wersja agenta wygasła w dniu 1 października 2018 r. |
| Pakiet zbiorczy aktualizacji z listopada | 1.3.0.0 | 30 listopada 2017 r. | Nie jest obsługiwana — wersja agenta wygasła w dniu 1 października 2018 r. |
| Pakiet zbiorczy aktualizacji października | 1.2.0.0 | 31 października 2017 r. | Nie jest obsługiwana — wersja agenta wygasła w dniu 1 października 2018 r. |
| Początkowa wersja zapoznawcza | 1.1.0.0 | 26 września 2017 r. | Nie jest obsługiwana — wersja agenta wygasła w dniu 1 października 2018 r. |

### <a name="azure-file-sync-agent-update-policy"></a>Zasady aktualizacji agenta usługi Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-3300"></a>Wersja agenta 3.3.0.0
Poniższe informacje o wersji dotyczą wersji 3.3.0.0 agenta usługi Azure File Sync wydanej 24 września 2018 r. Te informacje są uzupełnieniem informacji o wersji 3.1.0.0 informacje o wersji.

Ta wersja zawiera następujące poprawki:
- Stan zarejestrowanego serwera to "Pojawia się w trybie offline" od usługi Azure File Sync, agent jest uaktualniony do wersji 3.1 lub 3.2.
- Usługa agenta synchronizacji (FileSyncSvc) magazynu kończy się niepowodzeniem ze względu na pliki, których długie ścieżki.
- Rejestracja serwera nie powiodło się z powodu błędu: nie można załadować pliku lub zestawu Kailani.Afs.StorageSyncProtocol.V3.

## <a name="agent-version-3200"></a>Wersja agenta 3.2.0.0
Poniższe informacje o wersji dotyczą wersji 3.2.0.0 agenta usługi Azure File Sync wydanej 15 sierpnia 2018. Te informacje są uzupełnieniem informacji o wersji 3.1.0.0 informacje o wersji.

Ta wersja zawiera następujące poprawki:
- Synchronizacji kończy się niepowodzeniem błąd braku pamięci (0x8007000e) z powodu przeciek pamięci

## <a name="agent-version-3100"></a>Wersja agenta 3.1.0.0
Poniższe informacje o wersji dotyczą wersji 3.1.0.0 agenta usługi Azure File Sync (wydanej 19 lipca 2018 r.).

### <a name="evaluation-tool"></a>Narzędzie oceny
Przed wdrożeniem usługi Azure File Sync, należy sprawdzić, czy jest on zgodny z systemu za pomocą narzędzia oceny usługi Azure File Sync. To narzędzie jest AzureRM PowerShell polecenia cmdlet, która sprawdza pod kątem potencjalnych problemów za pomocą systemu plików i zestaw danych, takimi jak nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. I instrukcje dotyczące instalacji, zobacz [uruchamiania narzędzia oceny](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) w przewodniku planowania. 

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
- Usługa synchronizacji magazynu i/lub konta magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu zostanie przeniesiony, należy udzielić dostępu usługi hybrydowe File Sync do konta magazynu (zobacz [upewnij się, usługa Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Usługa Azure File Sync nie obsługuje przenoszenia subskrypcji Azure innej dzierżawy usługi AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.
