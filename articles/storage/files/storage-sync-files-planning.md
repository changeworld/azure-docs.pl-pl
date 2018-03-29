---
title: Planowanie wdrożenia synchronizacji plików Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, co należy wziąć pod uwagę podczas planowania wdrożenia usługi pliki Azure.
services: storage
documentationcenter: ''
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 401542bf61aa27138d26cce522e24078503b77e0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Planowanie wdrożenia synchronizacji plików Azure (wersja zapoznawcza)
Umożliwia synchronizacji plików Azure (wersja zapoznawcza) scentralizowanie udziałów plików w organizacji w plikach Azure, przy zachowaniu elastyczności, wydajności i zgodności serwera plików lokalnych. Synchronizacja programu Azure pliku przy użyciu systemu Windows Server do szybkiego pamięci podręcznej udziału plików na platformę Azure. Można użyć każdego protokołu, który jest dostępny w systemie Windows Server dostępu do danych lokalnie, w tym protokołu SMB, systemu plików NFS i FTPS. Może mieć dowolną liczbę pamięci podręcznych zgodnie z potrzebami na całym świecie.

W tym artykule opisano ważne kwestie dotyczące wdrażania synchronizacji plików Azure. Zaleca się również przeczytanie [planowania wdrożenia usługi pliki Azure](storage-files-planning.md). 

## <a name="azure-file-sync-terminology"></a>Terminologia synchronizacji plików platformy Azure
Przed przejściem do szczegółów planowania wdrożenia synchronizacji plików Azure, koniecznie zapoznaj się z terminologią.

### <a name="storage-sync-service"></a>Usługa synchronizacji magazynu
Usługa synchronizacji magazynu jest najwyższego poziomu zasobów platformy Azure dla synchronizacji plików Azure. Zasób usługi synchronizacji magazynu jest elementu równorzędnego zasobów konta magazynu i podobnie można wdrożyć dla grup zasobów platformy Azure. Ponieważ usługa synchronizacji magazynu można utworzyć relacji synchronizacji z wieloma kontami magazynu za pośrednictwem grupy synchronizacji wielu różnych zasobów najwyższego poziomu z zasobów konta magazynu jest wymagana. Subskrypcja może mieć wdrożono wiele zasobów magazynu usługi synchronizacji.

### <a name="sync-group"></a>Grupa synchronizacji
Grupy synchronizacji definiuje topologia synchronizacji dla grupy plików. Punkty końcowe w ramach grupy synchronizacji są zsynchronizowane ze sobą. Jeśli na przykład zawiera dwa różne zestawy plików, które mają być zarządzane za pomocą synchronizacji usługi Azure pliku, czy utworzyć dwie grupy synchronizacji i dodać do każdej grupy synchronizacji różnych punktów końcowych. Usługa synchronizacji magazynu może obsługiwać dowolną liczbę grup synchronizacji.  

### <a name="registered-server"></a>Zarejestrowany serwer
Obiekt zarejestrowanego serwera reprezentuje relację zaufania między serwerem (lub klastra) i usługi synchronizacji magazynu. Możesz zarejestrować dowolną liczbę serwerów do wystąpienia usługi synchronizacji magazynu ma. Jednak serwera (lub klastra) można zarejestrować z tylko jedną usługę synchronizacji magazynu naraz.

### <a name="azure-file-sync-agent"></a>Agent synchronizacji plików Azure
Agent synchronizacji plików Azure jest pobrania pakietu, który umożliwia systemu Windows Server można synchronizować z udziałem plików na platformę Azure. Agent synchronizacji plików Azure ma trzy główne składniki: 
- **FileSyncSvc.exe**: tła usługi systemu Windows, który jest odpowiedzialny za monitorowanie zmian w punktach końcowych serwera i inicjowania sesji synchronizacji na platformie Azure.
- **StorageSync.sys**: Filtr systemu plików synchronizacji plików Azure, która jest odpowiedzialna za warstw plików do usługi pliki Azure (usług w chmurze, gdy jest włączona obsługa poziomów).
- **Polecenia cmdlet programu PowerShell do zarządzania**: polecenia cmdlet programu PowerShell, używanej do interakcji z Microsoft.StorageSync Azure dostawcy zasobów. Dane można znaleźć w następujących lokalizacjach (domyślnie):
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Punkt końcowy serwera
Punkt końcowy serwera reprezentuje konkretnej lokalizacji na zarejestrowanego serwera, takie jak folder na serwerze woluminu. Może istnieć wiele punktów końcowych serwera na tym samym woluminie, jeśli ich przestrzenie nazw nie mogą się pokrywać (na przykład `F:\sync1` i `F:\sync2`). Można skonfigurować zasady warstw chmury indywidualnie dla każdego punktu końcowego serwera. Obecnie nie jest możliwe utworzenie punktu końcowego serwera dla katalogu głównego woluminu (na przykład `F:\` lub `C:\myvolume`, jeśli wolumin jest zainstalowany jako punkt instalacji).

> [!Note]  
> Obsługiwane są tylko woluminy z systemem innym niż wymienne.  Dyski mapowane z udziału zdalnego nie są obsługiwane dla punktu końcowego ścieżki serwera.  Ponadto punktu końcowego serwera może znajdować się w systemie Windows, wolumin systemowy jednak chmury warstwy nie jest obsługiwana w woluminie systemowym.

Jeśli dodasz lokalizacji serwera, która ma istniejącego zestawu plików jako punktu końcowego serwera do grupy synchronizacji te pliki są łączone z innymi plikami, które znajdują się już na innych punktów końcowych w grupie synchronizacji.

### <a name="cloud-endpoint"></a>Punktu końcowego w chmurze
Punkt końcowy w chmurze jest na udział plików na platformę Azure, który jest częścią grupy synchronizacji. Synchronizacje udziału plików na platformę Azure całego i udziału plików na platformę Azure może być elementem członkowskim tylko jednej chmury punktu końcowego. W związku z tym udziałem plików Azure może być członkiem tylko jednej grupy synchronizacji. Jeśli dodasz na udział plików na platformę Azure, który ma istniejącego zestawu plików jako punktu końcowego w chmurze do grupy synchronizacji, istniejące pliki są łączone z innymi plikami, które znajdują się już na innych punktów końcowych w grupie synchronizacji.

> [!Important]  
> Synchronizacja programu Azure pliku nie obsługuje bezpośrednio wprowadzania zmian do udziału plików na platformę Azure. Jednak wszelkie zmiany wprowadzone w udziale plików na platformę Azure najpierw konieczne ich odnalezienie przez zadanie wykrywania zmian synchronizacji plików Azure. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze tylko raz na 24 godziny. Ponadto zmiany wprowadzone do udziału plików na platformę Azure przy użyciu protokołu REST nie może zaktualizować SMB godzina ostatniej modyfikacji i nie będą widoczne jako zmiana przy synchronizacji. Aby uzyskać więcej informacji, zobacz [plików Azure — często zadawane pytania](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze 
Obsługa poziomów w chmurze jest opcjonalna funkcja synchronizacji plików Azure w którym rzadko używane lub uzyskać dostępu do plików większych niż 64 KiB rozmiar może należeć do warstwy do usługi pliki Azure. Gdy plik jest warstwowa, filtr systemu plików Azure plik synchronizacji (StorageSync.sys) zastępuje plik lokalnie wskaźnika ani punktu ponownej analizy. Punkt ponownej analizy reprezentuje adres URL do pliku w plikach Azure. Warstwowych plik ma ustawiony w systemie plików NTFS, identyfikacji plików warstwowych aplikacji innych firm, atrybut "offline". Po otwarciu pliku warstwowych synchronizacji plików Azure bezproblemowo odwołania do danych plików z plików Azure bez konieczności wiedzieć, że plik nie jest przechowywany lokalnie na komputerze użytkownika. Ta funkcja jest nazywana zarządzania magazynu hierarchicznych (HSM).

> [!Important]  
> Chmura obsługi nie jest obsługiwane dla punktów końcowych serwera w woluminach systemu Windows.

## <a name="azure-file-sync-interoperability"></a>Współdziałanie synchronizacji plików platformy Azure 
W tej sekcji omówiono synchronizacji plików Azure współdziałanie z funkcji systemu Windows Server oraz ról i rozwiązania innych firm.

### <a name="supported-versions-of-windows-server"></a>Obsługiwane wersje systemu Windows Server
Obecnie są obsługiwane wersje systemu Windows Server przez synchronizacji plików Azure:

| Wersja | Obsługiwane wersje produktu | Opcje wdrożenia obsługiwany |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter i Standard | Pełna (serwer z interfejsem użytkownika) |
| Windows Server 2012 R2 | Datacenter i Standard | Pełna (serwer z interfejsem użytkownika) |

Przyszłych wersji systemu Windows Server zostanie dodana po ich wydaniu. Wcześniejsze wersje systemu Windows mogą być dodane oparte na opinie użytkowników.

> [!Important]  
> Zaleca się pozostawienie wszystkich serwerów używanych z synchronizacji plików Azure na bieżąco z najnowszymi aktualizacjami z usługi Windows Update. 

### <a name="file-system-features"></a>Funkcje systemu plików
| Cecha | Obsługuje stanu | Uwagi |
|---------|----------------|-------|
| Listy kontroli dostępu (ACL) | W pełni obsługiwane | Listy ACL systemu Windows są zachowywane przez synchronizacji plików Azure i są wymuszane przez system Windows Server na serwerze punktów końcowych. Listy ACL systemu Windows nie są (jeszcze) obsługiwany przez pliki Azure, jeśli pliki są udostępniane bezpośrednio w chmurze. |
| Twarde linki | Pominięto | |
| Łącza symbolicznego | Pominięto | |
| Punkty instalacji | Częściowo obsługiwanej | Punkty instalacji może być katalogiem głównym serwera punktu końcowego, ale są one pomijane, jeśli są one w przestrzeni nazw punktu końcowego serwera. |
| Skrzyżowania | Pominięto | Na przykład rozproszonych DfrsrPrivate System plików i DFSRoots folderów. |
| Punkty ponownej analizy | Pominięto | |
| Kompresja NTFS | W pełni obsługiwane | |
| Plików rozrzedzonych | W pełni obsługiwane | Synchronizacja plików rozrzedzonych (nie są blokowane), ale synchronizacji w chmurze jako całego pliku. Zawartość pliku zmiany w chmurze (lub na innym serwerze), plik nie jest już rozrzedzony, gdy zmiana zostały pobrane. |
| Alternatywne strumienie danych (AD) | Zachowane, ale nie zsynchronizowano | Na przykład tagów klasyfikacji utworzonych przez infrastrukturę klasyfikacji plików nie są zsynchronizowane. Istniejące znaczniki klasyfikacji plików na każdym serwerze punkty końcowe są lewej bez zmian. |

> [!Note]  
> Obsługiwane są tylko woluminy NTFS. System plików reFS, systemie plików FAT, FAT32 i innych systemów plików nie są obsługiwane.

### <a name="failover-clustering"></a>Klaster trybu failover
Windows Server Failover Clustering jest obsługiwany przez synchronizacji plików Azure w opcji wdrażania "Serwer plików do użytku ogólnego". Klaster trybu failover nie jest obsługiwana w "Serwer plików skalowalny w poziomie dla danych aplikacji" (SOFS) lub na udostępnionych woluminów klastra (CSV).

> [!Note]  
> Na każdym węźle w klastrze pracy awaryjnej dla synchronizacji działał poprawnie, musi być zainstalowany agent synchronizacji plików Azure.

### <a name="data-deduplication"></a>Funkcja deduplikacji danych
Dla woluminów, które nie mają chmury, dodając funkcje warstw włączone synchronizacji plików Azure obsługuje deduplikacji danych serwera systemu Windows jest włączona na tym woluminie. Współdziałanie między synchronizacji plików Azure z chmurą, dodając funkcje warstw włączone i deduplikacji danych nie jest obecnie obsługiwana.

### <a name="distributed-file-system-dfs"></a>Rozproszony System plików (DFS)
Synchronizacja programu Azure pliku obsługuje współdziałanie z przestrzeni nazw systemu plików DFS (DFS-N) i replikacja systemu plików DFS (DFS-R), zaczynając od [agent synchronizacji plików Azure 1.2](https://go.microsoft.com/fwlink/?linkid=864522).

**Przestrzenie nazw systemu plików DFS (DFS-N)**: synchronizacja plików Azure jest w pełni obsługiwana na serwerach systemu plików DFS-N. Agent synchronizacji plików Azure można zainstalować na co najmniej jednego członka systemu plików DFS-N do synchronizacji danych między punktami końcowymi serwera i punktu końcowego w chmurze. Aby uzyskać więcej informacji, zobacz [omówienie przestrzenie nazw systemu plików DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replikacja systemu plików DFS (DFS-R)**: ponieważ systemu plików DFS-R i synchronizacji plików Azure są oba rozwiązania replikacji w większości przypadków, firma Microsoft zaleca, zastępując systemu plików DFS-R synchronizacji plików Azure. Istnieje jednak kilka scenariuszy, w miejscu do systemu plików DFS-R i synchronizacji plików Azure jednocześnie używać:

- W przypadku migracji z wdrożenia systemu plików DFS-R do wdrożenia usługi synchronizacji plików Azure. Aby uzyskać więcej informacji, zobacz [migracji wdrożenia replikacji systemu plików DFS (DFS-R) do synchronizacji usługi Azure pliku](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nie każdy serwer lokalny, wymagający kopię danych pliku można podłączyć bezpośrednio do Internetu.
- Serwerami w oddziale konsolidację danych na jednym serwerem centralnym, dla którego chcesz użyć synchronizacji plików Azure.

W przypadku synchronizacji plików Azure i systemu plików DFS-R pracy side-by-side:

1. Synchronizacja programu Azure plików w chmurze warstw musi być wyłączone w woluminach z folderami Replikacja systemu plików DFS-R.
2. Punkty końcowe serwera nie powinno być skonfigurowane w folderach tylko do odczytu replikacji systemu plików DFS-R.

Aby uzyskać więcej informacji, zobacz [Omówienie replikacji systemu plików DFS](https://technet.microsoft.com/library/jj127250).

### <a name="antivirus-solutions"></a>Programy antywirusowe
Ponieważ oprogramowanie antywirusowe polega na skanowanie plików do znanego złośliwego kodu, antywirusowe może spowodować odwołania warstwowych plików. Ponieważ pliki warstwowych ma ustawiony atrybut "offline", firma Microsoft zaleca konsultacji z dostawcą oprogramowania, aby dowiedzieć się, jak skonfigurować ich rozwiązanie, aby pominąć odczytywania plików trybu offline. 

Znane są następujące rozwiązania do obsługi pomijanie plików trybu offline:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [Zabezpieczenia punktu końcowego McAfee](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (zobacz "Skanuj tylko najważniejsze" na stronie 90 PDF)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos programu Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Rozwiązania kopii zapasowej
Na przykład oprogramowania antywirusowego rozwiązań tworzenia kopii zapasowych może spowodować odwołania warstwowych plików. Firma Microsoft zaleca, aby utworzyć kopię zapasową udziału plików na platformę Azure zamiast lokalnie instalowanym produktem kopii zapasowej za pomocą rozwiązania kopii zapasowych w chmurze.

### <a name="encryption-solutions"></a>Rozwiązania
Obsługa szyfrowania rozwiązań zależy od sposobu implementacji. Synchronizacja programu Azure pliku jest znany do pracy z:

- Szyfrowanie funkcją BitLocker
- Azure Information Protection, Azure Rights Management Services (Azure RMS) i usługi Active Directory RMS

Synchronizacja programu Azure pliku wiadomo, że nie będą działać z:

- NTFS System szyfrowania plików (EFS)

Ogólnie rzecz biorąc synchronizacji plików Azure powinien obsługiwać współdziałanie z rozwiązaniami szyfrowania, które znajdują się poniżej systemu plików, takich jak funkcja BitLocker oraz z rozwiązania, które zostały wdrożone w formacie pliku, na przykład funkcji BitLocker. Nie współdziałania wprowadzono rozwiązań, które znajdują się powyżej systemu plików (takich jak system szyfrowania plików NTFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Innych rozwiązań do zarządzania magazynu hierarchicznych (HSM)
Inne rozwiązania sprzętowego modułu zabezpieczeń powinien być używany z synchronizacji plików Azure.

## <a name="region-availability"></a>Dostępność w danym regionie
Synchronizacja programu Azure plik jest dostępny tylko w następujących regionach w wersji zapoznawczej:

| Region | Lokalizacja centrum danych |
|--------|---------------------|
| Australia Wschodnia | Stan Nowa Południowa Walia |
| Kanada Środkowa | Toronto |
| Środkowe stany USA | Iowa |
| Azja Wschodnia | Hongkong |
| Wschodnie stany USA | Wirginia |
| Wschodnie US2 | Wirginia |
| Europa Północna | Irlandia |
| Azja Południowo-Wschodnia | Singapur |
| Południowe Zjednoczone Królestwo | Londyn |
| Cental zachodnie stany USA |
| Europa Zachodnia | Holandia |
| Zachodnie stany USA | Kalifornia |

W wersji zapoznawczej firma Microsoft obsługuje synchronizację tylko z udziału plików na platformę Azure, który znajduje się w tym samym regionie co magazyn usługi synchronizacji.

## <a name="azure-file-sync-agent-update-policy"></a>Zasady aktualizacji agenta usługi Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Kolejne kroki
* [Należy wziąć pod uwagę ustawienia zapory i serwera proxy](storage-sync-files-firewall-and-proxy.md)
* [Planowanie wdrożenia usługi pliki Azure](storage-files-planning.md)
* [Wdrażanie plików platformy Azure](storage-files-deployment-guide.md)
* [Wdrażanie synchronizacji plików na platformę Azure](storage-sync-files-deployment-guide.md)
