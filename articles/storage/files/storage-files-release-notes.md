---
title: Informacje o wersji dla agenta Azure File Sync | Microsoft Docs
description: Informacje o wersji dla agenta Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 7/24/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: de0482f49e919d0cbb500e9ee8b27ccf8287fb99
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489563"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Informacje o wersji dla agenta Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Instalacje systemów Windows Server są przekształcane w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

W tym artykule udostępniono informacje o obsługiwanych wersjach agenta usługi Azure File Sync.

## <a name="supported-versions"></a>Obsługiwane wersje
Następujące wersje są obsługiwane przez agenta usługi Azure File Sync:

| Rozwoju | Numer wersji agenta | Data wydania | Stan |
|----|----------------------|--------------|------------------|
| 2019 lipca zbiorczych aktualizacji — [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 lipca 2019 | Obsługiwane — obsługa [lotu](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#automatic-agent-lifecycle-management) |
| 2019 lipca zbiorczych aktualizacji — [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 lipca 2019 | Obsługiwane — obsługa [lotu](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#automatic-agent-lifecycle-management) |
| Wersji 7 wydanie — [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 czerwca 2019 | Obsługiwane |
| Czerwiec 2019 — pakiet zbiorczy aktualizacji — [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 27 czerwca 2019 | Obsługiwane |
| Czerwiec 2019 — pakiet zbiorczy aktualizacji — [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13 czerwca 2019 | Obsługiwane |
| 2019 — pakiet zbiorczy aktualizacji — [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 7 maja 2019 | Obsługiwane |
| Wydanie V6 — [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21 kwietnia 2019 | Obsługiwane |
| Kwiecień 2019 — pakiet zbiorczy aktualizacji — [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 4 kwietnia 2019 | Obsługiwane |
| Marzec 2019 — pakiet zbiorczy aktualizacji — [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | 7 marca 2019 | Obsługiwane |
| Wydanie V5 — [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12 lutego 2019 | Obsługiwane |
| Pakiet zbiorczy aktualizacji 2019 styczeń — [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | 14 stycznia 2019 | Obsługiwane |
| Grudzień 2018 — pakiet zbiorczy aktualizacji — [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 10 grudnia 2018 | Obsługiwane |
| Pakiet zbiorczy aktualizacji 2018 grudnia | 4.1.0.0 | 4 grudnia 2018 r. | Obsługiwane |
| Wersja v4 | 4.0.1.0 | 13 listopada 2018 r. | Obsługiwane |
| Pakiet zbiorczy aktualizacji 2018 września | 3.3.0.0 | 24 września 2018 r. | Obsługiwane — wersja agenta wygaśnie 19 lipca 2019 |
| Pakiet zbiorczy aktualizacji 2018 sierpnia | 3.2.0.0 | 15 sierpnia 2018 r. | Obsługiwane — wersja agenta wygaśnie 19 lipca 2019 |
| Ogólna dostępność | 3.1.0.0 | 19 lipca 2018 | Obsługiwane — wersja agenta wygaśnie 19 lipca 2019 |
| Wygasł agenci | 1.1.0.0 — 3.0.13.0 | ND | Nieobsługiwane — wersje agenta wygasły 1 października 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Zasady aktualizacji agenta usługi Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-7200"></a>7\.2.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 7.2.0.0 agenta Azure File Sync wydanej 24 lipca 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 7.0.0.0.

Lista problemów rozwiązanych w tej wersji:  
- Agent synchronizacji magazynu (FileSyncSvc) ulega awarii, jeśli konfiguracja serwera proxy ma wartość null.
- Punkt końcowy serwera zacznie BCDR (Error 0x80c80257-ECS_E_BCDR_IN_PROGRESS), jeśli wiele punktów końcowych na serwerze ma taką samą nazwę.
- Ulepszenia niezawodności warstw w chmurze.

## <a name="agent-version-7100"></a>7\.1.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 7.1.0.0 agenta Azure File Sync wydanej 12 lipca 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 7.0.0.0.

Lista problemów rozwiązanych w tej wersji:  
- Uzyskiwanie dostępu do lub przeglądanie lokalizacji punktu końcowego serwera za pośrednictwem protokołu SMB działa wolno w systemie Windows Server 2012 R2. 
- Zwiększone wykorzystanie procesora po zainstalowaniu agenta Azure File Sync w wersji 6.
- Udoskonalenia danych telemetrycznych dotyczących warstw w chmurze.
- Różne ulepszenia niezawodności dotyczące warstw i synchronizacji w chmurze.

## <a name="agent-version-7000"></a>7\.0.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 7.0.0.0 agenta Azure File Sync (wydanie z 19 czerwca 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Obsługa większych rozmiarów udziałów plików
    - W wersji zapoznawczej większych udziałów plików platformy Azure zwiększamy również limity pomocy technicznej dotyczące synchronizacji plików. W tym pierwszym kroku Azure File Sync obsługuje teraz do 25TB i 50million plików w pojedynczej, synchronizowanej przestrzeni nazw. Aby złożyć udział w wersji zapoznawczej dużego udziału plików, Wypełnij https://aka.ms/azurefilesatscalesurvey ten formularz. 
- Ulepszone Azure Backup przywracanie na poziomie plików
    - Pojedyncze pliki przywrócone przy użyciu Azure Backup są teraz wykrywane i synchronizowane z punktem końcowym serwera.
- Ulepszona niezawodność poleceń cmdlet odwoływania się do warstw w chmurze 
    - Polecenie cmdlet odzyskania warstw w chmurze (Invoke-StorageSyncFileRecall) obsługuje teraz liczbę ponownych prób plików i opóźnienie ponawiania, podobnie jak Robocopy.
- Obsługa tylko protokołu TLS 1,2 (protokół TLS 1,0 i 1,1 jest wyłączony)
    - Azure File Sync teraz obsługuje używanie protokołu TLS 1,2 tylko na serwerach, które mają wyłączone usługi TLS 1,0 i 1,1. Przed wprowadzeniem tej poprawy Rejestracja serwera nie powiedzie się, jeśli na serwerze wyłączono protokoły TLS 1,0 i 1,1.
- Różne ulepszenia dotyczące wydajności i niezawodności dla synchronizacji i warstw w chmurze
    - W tej wersji istnieje kilka ulepszeń dotyczących niezawodności i wydajności. Niektóre z nich umożliwiają wydajniejszą obsługę warstw w chmurze, a Azure File Sync jako całość działają lepiej w tych sytuacjach, gdy ustawiono harmonogram ograniczania przepustowości.

### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny Azure File Sync. To narzędzie jest Azure PowerShell poleceniem cmdlet, które sprawdza potencjalne problemy związane z systemem plików i zestawem danych, na przykład nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Instrukcje dotyczące instalacji i użycia znajdują się w sekcji [Narzędzie do oceny](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta Azure File Sync przy użyciu systemu Windows Server, zobacz [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z podniesionymi uprawnieniami (administratora).
- Agent nie jest obsługiwany w przypadku opcji wdrażania serwera nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna mieć skonfigurowaną minimalną 2048 MiB pamięci.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, który ma skompresowany katalog informacji o woluminie systemowym (SVI). Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów Azure File Sync](storage-sync-files-troubleshoot.md).
- Ekrany plików Menedżer zasobów serwera plików (Menedżer zasobów plików) mogą spowodować nieograniczone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Uruchamianie programu Sysprep na serwerze, na którym zainstalowano agenta Azure File Sync, nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Po wdrożeniu obrazu serwera i zakończeniu miniinstalacji programu Sysprep należy zainstalować agenta Azure File Sync.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwanymi znakami. Zobacz [Przewodnik rozwiązywania problemów](storage-sync-files-troubleshoot.md#handling-unsupported-characters) , aby wyświetlić listę nieobsługiwanych znaków.
- Pliki lub katalogi kończące się kropką.
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
- Pliki warstwowe staną się niedostępne, jeśli nie zostaną one wywołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, Utwórz ponownie punkt końcowy serwera. Jeśli upłynął 30 dni od momentu, gdy punkt końcowy serwera został usunięty lub punkt końcowy w chmurze został usunięty, pliki warstwowe, które nie zostały odwołane, nie będą używane.
- Obsługa warstw w chmurze nie jest obsługiwana na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, Wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana w przypadku zmiany nazwy serwera.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Azure File Sync obsługuje wprowadzanie zmian bezpośrednio w udziale plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać odnalezione za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Ponadto zmiany wprowadzone w udziale plików platformy Azure za pośrednictwem protokołu REST nie będą aktualizować czasu ostatniej modyfikacji SMB i nie będą widoczne jako zmiany przez synchronizację.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu jest przenoszone, musisz udzielić hybrydowej usłudze File Sync dostępu do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nie obsługuje przeniesienia subskrypcji do innej dzierżawy usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików przy użyciu Robocopy, użyj opcji/MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są dostępne wcześniej niż ostatnio używane pliki.

## <a name="agent-version-6300"></a>6\.3.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 6.3.0.0 agenta Azure File Sync wydanej 27 czerwca 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 6.0.0.0.

Lista problemów rozwiązanych w tej wersji:  
- Uzyskiwanie dostępu do lub przeglądanie lokalizacji punktu końcowego serwera za pośrednictwem protokołu SMB jest wolne w systemie Windows Server 2012 R2 
- Zwiększone wykorzystanie procesora po zainstalowaniu agenta Azure File Sync w wersji 6
- Udoskonalenia danych telemetrycznych w warstwach chmury

## <a name="agent-version-6200"></a>6\.2.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 6.2.0.0 agenta Azure File Sync wydanego od 13 czerwca 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 6.0.0.0.

Lista problemów rozwiązanych w tej wersji:  
- Po utworzeniu punktu końcowego serwera może wystąpić duże użycie procesora CPU, gdy funkcja odwoływania w tle pobiera pliki na serwer
- Operacje synchronizacji i obsługi warstw w chmurze mogą zakończyć się niepowodzeniem z powodu błędu ECS_E_SERVER_CREDENTIAL_NEEDED ze względu na wygaśnięcie tokenu
- Wywołanie pliku może się nie powieść, jeśli adres URL pobierania pliku zawiera zastrzeżone znaki 

## <a name="agent-version-6100"></a>6\.1.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 6.1.0.0 agenta Azure File Sync wydanej 6 maja 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 6.0.0.0.

Lista problemów rozwiązanych w tej wersji:  
- Centrum administracyjne systemu Windows nie może wyświetlić wersji agenta i konfiguracji punktu końcowego serwera na serwerach, na których zainstalowano agenta Azure File Sync w wersji 6,0.

## <a name="agent-version-6000"></a>6\.0.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 6.0.0.0 agenta Azure File Sync (wydanie 22 kwietnia 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Obsługa autoaktualizacji agenta
  - Otrzymaliśmy Twoją opinię i dodano funkcję autoaktualizowania do agenta serwera Azure File Sync. Aby uzyskać więcej informacji, zobacz [Azure File Sync zasad aktualizacji agenta](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Obsługa list ACL udziałów plików platformy Azure
  - Azure File Sync zawsze obsługiwał synchronizowanie list ACL między punktami końcowymi serwera, ale listy ACL nie zostały zsynchronizowane z punktem końcowym w chmurze (udziałem plików platformy Azure). W tej wersji dodano obsługę synchronizowania list kontroli dostępu między serwerem a punktami końcowymi w chmurze.
- Równoległe przekazywanie i pobieranie sesji synchronizacji dla punktu końcowego serwera 
  - Punkty końcowe serwera obsługują teraz przekazywanie i pobieranie plików w tym samym czasie. Nie można czekać na ukończenie pobierania, aby umożliwić przekazywanie plików do udziału plików platformy Azure. 
- Nowe polecenia cmdlet do obsługi warstw w chmurze umożliwiające pobieranie stanu woluminu i warstwowego
  - Dwa nowe polecenia cmdlet programu PowerShell w ramach serwera mogą być teraz używane do uzyskiwania informacji o ustawieniach i odwoływania plików w chmurze. Udostępniają one informacje rejestrowania z dwóch kanałów zdarzeń na serwerze:
    - Get-StorageSyncFileTieringResult wyświetli listę wszystkich plików i ich ścieżek, które nie zostały warstwowe i raporty z przyczyn.
    - Get-StorageSyncFileRecallResult raportuje wszystkie zdarzenia odwołania do pliku. Wyświetla wszystkie pliki, które zostały odwołane, oraz jego ścieżkę, a także powodzenie lub błąd dla tego odwołania.
  - Domyślnie oba kanały zdarzeń mogą przechowywać do 1 MB każdego — można zwiększyć ilość plików raportowanych przez zwiększenie rozmiaru kanału zdarzenia.
- Obsługa trybu FIPS
  - Azure File Sync teraz obsługuje Włączanie trybu FIPS na serwerach z zainstalowanym agentem Azure File Sync.
    - Przed włączeniem trybu FIPS na serwerze Zainstaluj agenta Azure File Sync i [moduł PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) na serwerze. Jeśli na serwerze jest już włączona funkcja FIPS, [ręcznie Pobierz](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download) [moduł PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) na serwer.
- Różne ulepszenia niezawodności dotyczące warstw i synchronizacji w chmurze

### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny Azure File Sync. To narzędzie jest Azure PowerShell poleceniem cmdlet, które sprawdza potencjalne problemy związane z systemem plików i zestawem danych, na przykład nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Instrukcje dotyczące instalacji i użycia znajdują się w sekcji [Narzędzie do oceny](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta Azure File Sync przy użyciu systemu Windows Server, zobacz [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z podniesionymi uprawnieniami (administratora).
- Agent nie jest obsługiwany w przypadku opcji wdrażania serwera nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna mieć skonfigurowaną minimalną 2048 MiB pamięci.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, który ma skompresowany katalog informacji o woluminie systemowym (SVI). Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów Azure File Sync](storage-sync-files-troubleshoot.md).
- Ekrany plików Menedżer zasobów serwera plików (Menedżer zasobów plików) mogą spowodować nieograniczone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Uruchamianie programu Sysprep na serwerze, na którym zainstalowano agenta Azure File Sync, nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Po wdrożeniu obrazu serwera i zakończeniu miniinstalacji programu Sysprep należy zainstalować agenta Azure File Sync.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwanymi znakami. Zobacz [Przewodnik rozwiązywania problemów](storage-sync-files-troubleshoot.md#handling-unsupported-characters) , aby wyświetlić listę nieobsługiwanych znaków.
- Pliki lub katalogi kończące się kropką.
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
- Pliki warstwowe staną się niedostępne, jeśli nie zostaną one wywołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, Utwórz ponownie punkt końcowy serwera. Jeśli upłynął 30 dni od momentu, gdy punkt końcowy serwera został usunięty lub punkt końcowy w chmurze został usunięty, pliki warstwowe, które nie zostały odwołane, nie będą używane.
- Obsługa warstw w chmurze nie jest obsługiwana na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, Wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana w przypadku zmiany nazwy serwera.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Azure File Sync obsługuje wprowadzanie zmian bezpośrednio w udziale plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać odnalezione za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Ponadto zmiany wprowadzone w udziale plików platformy Azure za pośrednictwem protokołu REST nie będą aktualizować czasu ostatniej modyfikacji SMB i nie będą widoczne jako zmiany przez synchronizację.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu jest przenoszone, musisz udzielić hybrydowej usłudze File Sync dostępu do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nie obsługuje przeniesienia subskrypcji do innej dzierżawy usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików przy użyciu Robocopy, użyj opcji/MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są dostępne wcześniej niż ostatnio używane pliki.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.

## <a name="agent-version-5200"></a>5\.2.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 5.2.0.0 agenta Azure File Sync wydanej 4 kwietnia 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 5.0.2.0.

Lista problemów rozwiązanych w tej wersji:  
- Ulepszenia niezawodności dotyczące funkcji transferu danych w trybie offline i wznawiania transferu danych
- Udoskonalenia synchronizacji danych telemetrycznych

## <a name="agent-version-5100"></a>5\.1.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 5.1.0.0 agenta Azure File Sync wydanego 7 marca 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 5.0.2.0.

Lista problemów rozwiązanych w tej wersji:  
- Synchronizacja plików może zakończyć się niepowodzeniem z błędem 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED), jeśli Wyliczenie zmian kończy się niepowodzeniem na serwerze
- Jeśli sesja synchronizacji lub plik otrzymuje błąd 0x80072F78 (WININET_E_INVALID_SERVER_RESPONSE), synchronizacja spowoduje teraz ponowną próbę wykonania operacji
- Synchronizacja plików może zakończyć się niepowodzeniem z powodu błędu 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Duże użycie pamięci może wystąpić podczas odwoływania plików
- Udoskonalenia danych telemetrycznych w warstwach chmury 

## <a name="agent-version-5020"></a>5\.0.2.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 5.0.2.0 agenta Azure File Sync (wydane 12 lutego 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Obsługa Azure Government w chmurze
  - Dodaliśmy obsługę wersji zapoznawczej dla chmury Azure Government. Wymaga to białej subskrypcji i specjalnego pobrania agenta od firmy Microsoft. Aby uzyskać dostęp do wersji zapoznawczej, Wyślij wiadomość e-mail [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)bezpośrednio na adres.
- Obsługa deduplikacji danych
    - Funkcja deduplikacji danych jest teraz w pełni obsługiwana przy użyciu obsługi warstw w chmurze w systemach Windows Server 2016 i Windows Server 2019. Włączenie deduplikacji na woluminie z włączonym obsługą warstw w chmurze umożliwia przechowywanie w pamięci podręcznej większej liczby plików bez udostępniania większej ilości miejsca w magazynie.
- Obsługa transferu danych w trybie offline (na przykład za pośrednictwem urządzenie Data Box)
    - Z łatwością Migruj duże ilości danych do Azure File Sync za pośrednictwem dowolnych środków. Można wybrać Azure Data Box, AzCopy, a nawet usług migracji innych firm. Nie ma potrzeby korzystania z dużej ilości przepustowości w celu pobierania danych na platformę Azure, w przypadku urządzenie Data Box — po prostu wyślij ją tam. Aby dowiedzieć się więcej, zobacz [Offline transfer danych docs](https://aka.ms/AFS/OfflineDataTransfer).
- Ulepszona wydajność synchronizacji
    - Klienci z wieloma punktami końcowymi serwera na tym samym woluminie mogą mieć powolną wydajność synchronizacji przed tą wersją. Azure File Sync tworzy tymczasową migawkę usługi VSS raz dziennie na serwerze w celu synchronizowania plików, które mają otwarte dojścia. Usługa Sync obsługuje teraz synchronizację wielu punktów końcowych serwera na woluminie, gdy sesja synchronizacji usługi VSS jest aktywna. Nie można czekać na ukończenie sesji synchronizacji usługi VSS w celu wznowienia synchronizacji w innych punktach końcowych serwera w woluminie.
- Ulepszone monitorowanie w portalu
    - Dodano wykresy do portalu usługi synchronizacji magazynu w celu wyświetlenia:
        - Liczba synchronizowanych plików
        - Rozmiar transferowanych danych
        - Liczba niesynchronizowanych plików
        - Rozmiar danych, które zostały odwołane
        - Stan łączności z serwerem
    - Aby dowiedzieć się więcej, zobacz [Monitor Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Zwiększona skalowalność i niezawodność
    - Maksymalna liczba obiektów systemu plików (katalogów i plików) w katalogu została zwiększona do 1 000 000. Poprzedni limit to 200 000.
    - Funkcja synchronizacji podejmie próbę wznowienia transferu danych zamiast retransmisji w przypadku przerwania transferu dla dużych plików 

### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny Azure File Sync. To narzędzie jest Azure PowerShell poleceniem cmdlet, które sprawdza potencjalne problemy związane z systemem plików i zestawem danych, na przykład nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Instrukcje dotyczące instalacji i użycia znajdują się w sekcji [Narzędzie do oceny](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta Azure File Sync przy użyciu systemu Windows Server, zobacz [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z podniesionymi uprawnieniami (administratora).
- Agent nie jest obsługiwany w opcjach wdrażania systemu Windows Server Core lub nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna mieć skonfigurowaną minimalną 2048 MiB pamięci.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, który ma skompresowany katalog informacji o woluminie systemowym (SVI). Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.
- Tryb FIPS nie jest obsługiwany i musi być wyłączony. 

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów Azure File Sync](storage-sync-files-troubleshoot.md).
- Ekrany plików Menedżer zasobów serwera plików (Menedżer zasobów plików) mogą spowodować nieograniczone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Uruchamianie programu Sysprep na serwerze, na którym zainstalowano agenta Azure File Sync, nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Po wdrożeniu obrazu serwera i zakończeniu miniinstalacji programu Sysprep należy zainstalować agenta Azure File Sync.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwanymi znakami. Zobacz [Przewodnik rozwiązywania problemów](storage-sync-files-troubleshoot.md#handling-unsupported-characters) , aby wyświetlić listę nieobsługiwanych znaków.
- Pliki lub katalogi kończące się kropką.
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
- Pliki warstwowe staną się niedostępne, jeśli nie zostaną one wywołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, Utwórz ponownie punkt końcowy serwera. Jeśli upłynął 30 dni od momentu, gdy punkt końcowy serwera został usunięty lub punkt końcowy w chmurze został usunięty, pliki warstwowe, które nie zostały odwołane, nie będą używane.
- Obsługa warstw w chmurze nie jest obsługiwana na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, Wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana w przypadku zmiany nazwy serwera.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Azure File Sync obsługuje wprowadzanie zmian bezpośrednio w udziale plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać odnalezione za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Ponadto zmiany wprowadzone w udziale plików platformy Azure za pośrednictwem protokołu REST nie będą aktualizować czasu ostatniej modyfikacji SMB i nie będą widoczne jako zmiany przez synchronizację.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu jest przenoszone, musisz udzielić hybrydowej usłudze File Sync dostępu do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nie obsługuje przeniesienia subskrypcji do innej dzierżawy usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików przy użyciu Robocopy, użyj opcji/MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są dostępne wcześniej niż ostatnio używane pliki.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.

## <a name="agent-version-4300"></a>4\.3.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 4.3.0.0 agenta Azure File Sync wydanej 14 stycznia 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 4.0.1.0.

Lista problemów rozwiązanych w tej wersji:  
- Pliki nie są warstwami po uaktualnieniu agenta Azure File Sync do wersji 4. x.
- AfsUpdater. exe jest teraz obsługiwany w systemie Windows Server 2019.
- Różne ulepszenia niezawodności dla synchronizacji. 

## <a name="agent-version-4200"></a>4\.2.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 4.2.0.0 agenta Azure File Sync wydanej 10 grudnia 2018. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 4.0.1.0.

Lista problemów rozwiązanych w tej wersji:  
- Podczas tworzenia migawki usługi VSS może wystąpić błąd zatrzymania 0x3B lub Stop 0x1E.  
- Może wystąpić przeciek pamięci, gdy włączona jest obsługa warstw w chmurze  

## <a name="agent-version-4100"></a>4\.1.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 4.1.0.0 agenta Azure File Sync wydanej 4 grudnia 2018. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 4.0.1.0.

Lista problemów rozwiązanych w tej wersji:  
- Serwer może przestać odpowiadać z powodu przecieku pamięci w warstwach chmury.  
- Instalacja agenta kończy się niepowodzeniem z powodu następującego błędu: Błąd 1921. Nie można zatrzymać agenta synchronizacji magazynu usługi "(FileSyncSvc).  Sprawdź, czy masz wystarczające uprawnienia do zatrzymywania usług systemowych.  
- Usługa agenta synchronizacji magazynu (FileSyncSvc) może ulec awarii, gdy użycie pamięci jest wysokie.  
- Różne ulepszenia niezawodności dotyczące warstw i synchronizacji w chmurze.

## <a name="agent-version-4010"></a>4\.0.1.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 4.0.1.0 agenta Azure File Sync (wydanie 13 listopada 2018).

### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny Azure File Sync. To narzędzie jest Azure PowerShell poleceniem cmdlet, które sprawdza potencjalne problemy związane z systemem plików i zestawem danych, na przykład nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Instrukcje dotyczące instalacji i użycia znajdują się w sekcji [Narzędzie do oceny](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta Azure File Sync przy użyciu systemu Windows Server, zobacz [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z podniesionymi uprawnieniami (administratora).
- Agent nie jest obsługiwany w opcjach wdrażania systemu Windows Server Core lub nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna mieć skonfigurowaną minimalną 2048 MiB pamięci.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, który ma skompresowany katalog informacji o woluminie systemowym (SVI). Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.
- Tryb FIPS nie jest obsługiwany i musi być wyłączony. 
- Podczas tworzenia migawki usługi VSS może wystąpić błąd zatrzymania 0x3B lub Stop 0x1E.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów Azure File Sync](storage-sync-files-troubleshoot.md).
- Ekrany plików Menedżer zasobów serwera plików (Menedżer zasobów plików) mogą spowodować nieograniczone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Uruchamianie programu Sysprep na serwerze, na którym zainstalowano agenta Azure File Sync, nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Po wdrożeniu obrazu serwera i zakończeniu miniinstalacji programu Sysprep należy zainstalować agenta Azure File Sync.
- Deduplikacja danych i obsługa warstw w chmurze nie są obsługiwane w tym samym woluminie.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwanymi znakami. Zobacz [Przewodnik rozwiązywania problemów](storage-sync-files-troubleshoot.md#handling-unsupported-characters) , aby wyświetlić listę nieobsługiwanych znaków.
- Pliki lub katalogi kończące się kropką.
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
- Pliki warstwowe staną się niedostępne, jeśli nie zostaną one wywołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, Utwórz ponownie punkt końcowy serwera. Jeśli upłynął 30 dni od momentu, gdy punkt końcowy serwera został usunięty lub punkt końcowy w chmurze został usunięty, pliki warstwowe, które nie zostały odwołane, nie będą używane.
- Obsługa warstw w chmurze nie jest obsługiwana na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, Wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana w przypadku zmiany nazwy serwera.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Azure File Sync obsługuje wprowadzanie zmian bezpośrednio w udziale plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać odnalezione za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Ponadto zmiany wprowadzone w udziale plików platformy Azure za pośrednictwem protokołu REST nie będą aktualizować czasu ostatniej modyfikacji SMB i nie będą widoczne jako zmiany przez synchronizację.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu jest przenoszone, musisz udzielić hybrydowej usłudze File Sync dostępu do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nie obsługuje przeniesienia subskrypcji do innej dzierżawy usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Ustawienie zasad obsługi warstw w chmurze opartej na dacie służy do określania plików, które powinny być buforowane, jeśli są używane w określonej liczbie dni. Aby dowiedzieć się więcej, zobacz [Omówienie obsługi warstw w chmurze](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering).
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików przy użyciu Robocopy, użyj opcji/MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są dostępne wcześniej niż ostatnio używane pliki.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.

## <a name="agent-version-3300"></a>3\.3.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 3.3.0.0 agenta Azure File Sync wydanego 24 września 2018. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 3.1.0.0.

Lista problemów rozwiązanych w tej wersji:
- Stan zarejestrowanego serwera to "pojawia się w trybie offline" po uaktualnieniu agenta Azure File Sync do wersji 3,1 lub 3,2.
- Awaria usługi agenta synchronizacji magazynu (FileSyncSvc) z powodu plików, które mają długie ścieżki.
- Rejestracja serwera kończy się niepowodzeniem z powodu błędu: Nie można załadować pliku lub zestawu Kailani. AFS. StorageSyncProtocol. v3.

## <a name="agent-version-3200"></a>3\.2.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 3.2.0.0 agenta Azure File Sync wydanej 15 sierpnia 2018. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 3.1.0.0.

Ta wersja zawiera następujące poprawki:
- Synchronizacja nie powiodła się z powodu błędu braku pamięci (0x8007000E) ze względu na przeciek pamięci

## <a name="agent-version-3100"></a>3\.1.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 3.1.0.0 agenta Azure File Sync (wydanie 19 lipca 2018).

### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny Azure File Sync. To narzędzie jest Azure PowerShell poleceniem cmdlet, które sprawdza potencjalne problemy związane z systemem plików i zestawem danych, na przykład nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Instrukcje dotyczące instalacji i użycia znajdują się w sekcji [Narzędzie do oceny](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta Azure File Sync przy użyciu systemu Windows Server, zobacz [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z podniesionymi uprawnieniami (administratora).
- Agent nie jest obsługiwany w opcjach wdrażania systemu Windows Server Core lub nano Server.
- Agent jest obsługiwany wyłącznie w systemach Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GB pamięci fizycznej.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, który ma skompresowany katalog informacji o woluminie systemowym (SVI). Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.
- Tryb FIPS nie jest obsługiwany i musi być wyłączony. 

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów Azure File Sync](storage-sync-files-troubleshoot.md).
- Nie używaj osłon plików Menedżera zasobów serwera plików ani innych osłon plików. Osłony plików mogą powodować nieskończone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Uruchamianie programu Sysprep na serwerze, na którym zainstalowano agenta Azure File Sync, nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Po wdrożeniu obrazu serwera i zakończeniu miniinstalacji programu Sysprep należy przeprowadzić instalację agenta i rejestrację serwera.
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
- Pliki warstwowe staną się bezużyteczne, jeśli pliki nie są ponownie wywoływane przed usunięciem punktu końcowego serwera.
- Obsługa warstw w chmurze nie jest obsługiwana na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, Wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana w przypadku zmiany nazwy serwera.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Azure File Sync obsługuje wprowadzanie zmian bezpośrednio w udziale plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać odnalezione za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Ponadto zmiany wprowadzone w udziale plików platformy Azure za pośrednictwem protokołu REST nie będą aktualizować czasu ostatniej modyfikacji SMB i nie będą widoczne jako zmiany przez synchronizację.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu jest przenoszone, musisz udzielić hybrydowej usłudze File Sync dostępu do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nie obsługuje przeniesienia subskrypcji do innej dzierżawy usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.
