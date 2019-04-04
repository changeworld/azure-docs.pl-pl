---
title: Planowanie wdrożenia usługi Azure File Sync | Dokumentacja firmy Microsoft
description: Dowiedz się, co należy wziąć pod uwagę podczas planowania wdrożenia usługi Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 2/7/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: ad3b5a1d684c500eff3d20832d7aa290a13849b9
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918641"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planowanie wdrażania usługi Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files przy jednoczesnym zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Usługa Azure File Sync przekształca systemu Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Można użyć dowolnego protokołu, który jest dostępny w systemie Windows Server oraz dostęp do danych lokalnie, w tym protokołu SMB, systemu plików NFS i protokołu FTPS. Może mieć dowolną liczbę pamięci podręcznych potrzebnych na całym świecie.

W tym artykule opisano istotne zagadnienia dotyczące wdrożenia usługi Azure File Sync. Firma Microsoft zaleca się również przeczytanie [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md). 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Terminologii platformy Azure File Sync
Przed przejściem do szczegółów Planowanie wdrażania usługi Azure File Sync, koniecznie zapoznaj się z terminologią.

### <a name="storage-sync-service"></a>Usługa synchronizacji magazynu
Usługa synchronizacji magazynu jest najwyższego poziomu zasobów platformy Azure dla usługi Azure File Sync. Zasób usługi synchronizacji magazynu jest równorzędny zasób konta magazynu i podobnie można wdrożyć dla grup zasobów platformy Azure. Odrębne zasobem najwyższego poziomu z zasobu konta magazynu jest wymagana, ponieważ usługa synchronizacji magazynu można utworzyć relacji synchronizacji z wieloma kontami magazynu przy użyciu wielu grup synchronizacji. Subskrypcja może mieć wiele zasobów Usługa synchronizacji magazynu wdrożone.

### <a name="sync-group"></a>Grupa synchronizacji
Grupa synchronizacji definiuje topologię synchronizacji dla zestawu plików. Punkty końcowe w ramach grupy synchronizacji są synchronizowane ze sobą. Jeśli na przykład masz dwa odrębne rodzaje plików, które mają być zarządzane za pomocą usługi Azure File Sync, czy utworzyć dwie grupy synchronizacji i dodać różne punkty końcowe do każdej grupy synchronizacji. Usługa synchronizacji magazynu może obsługiwać dowolną liczbę grup synchronizacji.  

### <a name="registered-server"></a>Zarejestrowany serwer
Obiekt zarejestrowanego serwera reprezentuje relację zaufania między serwerem (lub klastra) i usługę synchronizacji magazynu. Możesz zarejestrować dowolną liczbę serwerów do wystąpienia usługi synchronizacji magazynu jak chcesz. Jednak serwer (lub klastrowy) można zarejestrować przy użyciu tylko jednej usługi synchronizacji magazynu w danym momencie.

### <a name="azure-file-sync-agent"></a>Agent usługi Azure File Sync
Agent usługi Azure File Sync to możliwy do pobrania pakiet, który umożliwia synchronizowanie systemu Windows Server z udziałem plików platformy Azure. Agent usługi Azure File Sync ma trzy główne składniki: 
- **FileSyncSvc.exe**: Tło, usługa Windows, który jest odpowiedzialny za monitorowanie zmian na punkty końcowe serwera i inicjowania sesji synchronizacji na platformie Azure.
- **StorageSync.sys**: Filtr systemu plików usługi Azure File Sync, która jest odpowiedzialna za warstw pliki do usługi Azure Files (po chmurze warstw jest włączona).
- **Polecenia cmdlet programu PowerShell do zarządzania**: Polecenia cmdlet programu PowerShell, który jest używany do interakcji z dostawcy zasobów platformy Microsoft.StorageSync Azure. Dane można znaleźć w następujących lokalizacjach (ustawienie domyślne):
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Punkt końcowy serwera
Punkt końcowy serwera reprezentuje określoną lokalizację na zarejestrowanym serwerze, taką jak folder na woluminie serwera. Wiele punktów końcowych serwera może istnieć na tym samym woluminie, jeśli nie pokrywają się ich przestrzenie nazw (na przykład `F:\sync1` i `F:\sync2`). Można skonfigurować zasad obsługi warstw chmury osobno dla każdego punktu końcowego serwera. 

Możesz utworzyć punkt końcowy serwera za pośrednictwem punktu instalacji. Należy zauważyć, że punkty instalacji, w ramach punktu końcowego serwera są pomijane.  

Możesz utworzyć punkt końcowy serwera na woluminie systemowym, ale istnieją dwa ograniczenia, jeśli możesz to zrobić:
* Chmura nie można włączyć obsługi warstw.
* Nie jest wykonywane Przywracanie szybkie przestrzeni nazw (gdzie system szybko Przesuwa w dół całej przestrzeni nazw, a następnie uruchamia przywołanie zawartości).


> [!Note]  
> Obsługiwane są tylko woluminy niewymienne.  Dyski mapowane z udziału zdalnego nie są obsługiwane dla ścieżkę punktu końcowego serwera.  Ponadto punkt końcowy serwera może znajdować się na Windows wolumin systemowy jednak chmury obsługi warstw nie jest obsługiwane na woluminie systemowym.

Jeśli dodasz lokalizacji serwera, z istniejącego zestawu plików jako punkt końcowy serwera z grupą synchronizacji, te pliki są scalane z innymi plikami, które już znajdują się na inne punkty końcowe w grupie synchronizacji.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
Punkt końcowy w chmurze jest udział plików platformy Azure, która jest częścią grupy synchronizacji. Synchronizacje udziału plików platformy Azure cały i udziału plików platformy Azure mogą należeć do punktu końcowego tylko jedna chmura. W związku z tym udziału plików platformy Azure mogą należeć do tylko jednej grupy synchronizacji. Jeśli dodasz udział plików platformy Azure z istniejącej grupy plików jako punktu końcowego w chmurze z grupą synchronizacji, istniejące pliki są scalane z innymi plikami, które już znajdują się na inne punkty końcowe w grupie synchronizacji.

> [!Important]  
> Usługa Azure File Sync nie obsługuje bezpośrednio wprowadzania zmian do udziału plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw konieczne ich odnalezienie przez zadanie wykrywania zmian usługi Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze tylko raz na 24 godziny. Ponadto zmiany wprowadzone do udziału plików platformy Azure za pośrednictwem protokołu REST nie może zaktualizować SMB godzina ostatniej modyfikacji i nie będą widoczne jako zmiany przez sync. Aby uzyskać więcej informacji, zobacz [usługi Azure Files — często zadawane pytania](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze 
Chmura warstw to opcjonalna funkcja usługi Azure File Sync, w których często używanych plików są buforowane lokalnie na serwerze, podczas gdy inne pliki są organizowane w warstwy do usługi Azure Files na podstawie ustawień zasad. Aby uzyskać więcej informacji, zobacz [Obsługa poziomów w chmurze opis](storage-sync-cloud-tiering.md).

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Wymagania systemowe w usłudze Azure File Sync i współdziałanie 
Tej sekcji omówiono wymagania systemowe agenta usługi Azure File Sync i współdziałanie z funkcji systemu Windows Server oraz role i rozwiązań innych firm.

### <a name="evaluation-tool"></a>Narzędzie oceny
Przed wdrożeniem usługi Azure File Sync, należy sprawdzić, czy jest on zgodny z systemu za pomocą narzędzia oceny usługi Azure File Sync. To narzędzie jest polecenie cmdlet programu Azure PowerShell, która sprawdza pod kątem potencjalnych problemów za pomocą systemu plików i zestaw danych, takimi jak nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Należy zauważyć, że najbardziej obejmują jego sprawdzanie, ale nie wszystkie funkcje wymienione poniżej; Zalecamy przeczytanie pozostałej części tej sekcji, aby upewnić się, że Twoje wdrożenie zostanie umieszczone sprawnie. 

#### <a name="download-instructions"></a>Instrukcje pobierania
1. Upewnij się, że masz najnowszą wersję modułu PackageManagement i zainstalowany moduł PowerShellGet (umożliwia to zainstalować moduły z wersji zapoznawczej)
    
    ```powershell
        Install-Module -Name PackageManagement -Repository PSGallery -Force
        Install-Module -Name PowerShellGet -Repository PSGallery -Force
    ```
 
2. Ponowne uruchomienie programu PowerShell
3. Instalacja modułów
    
    ```powershell
        Install-Module -Name Az.StorageSync -AllowPrerelease -AllowClobber -Force
    ```

#### <a name="usage"></a>Sposób użycia  
Narzędzie oceny można wywołać na kilka różnych sposobów: możesz wykonać testy systemu i/lub sprawdzenia zestawu danych. Aby wykonać testy systemu i zestaw danych: 

```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Aby przetestować tylko zestaw danych:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Aby przetestować tylko wymagania systemowe:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Aby wyświetlić wyniki w formacie CSV:
```powershell
    $errors = Invoke-AzStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>Wymagania systemu
- Serwer z systemem Windows Server 2012 R2, Windows Server 2016 lub Windows Server 2019:

    | Wersja | Obsługiwane jednostki SKU | Obsługiwane opcje wdrażania |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Wersja Datacenter i Standard | Pełne (serwera z interfejsem użytkownika) |
    | Windows Server 2016 | Wersja Datacenter i Standard | Pełne (serwera z interfejsem użytkownika) |
    | Windows Server 2012 R2 | Wersja Datacenter i Standard | Pełne (serwera z interfejsem użytkownika) |

    Przyszłych wersjach systemu Windows Server zostanie dodana po ich wydaniu. Wcześniejszych wersjach systemu Windows mogą być dodawane w oparciu o opinie użytkowników.

    > [!Important]  
    > Zaleca się pozostawienie wszystkich serwerów, które używają usługi Azure File Sync na bieżąco z najnowszymi aktualizacjami z witryny Windows Update. 

- Serwer z co najmniej 2 GiB pamięci.

    > [!Important]  
    > Jeśli serwer jest uruchomiony na maszynie wirtualnej z obsługą pamięci dynamicznej, minimalna MiB 2048 pamięci należy skonfigurować maszynę Wirtualną.
    
- Podłączonych lokalnie woluminie sformatowanym w systemie plików NTFS.

### <a name="file-system-features"></a>Funkcje systemu plików

| Cecha | Stan obsługi | Uwagi |
|---------|----------------|-------|
| Listy kontroli dostępu (ACL) | W pełni obsługiwane | Listy kontroli dostępu Windows są zachowywane przez usługę Azure File Sync i są wymuszane przez system Windows Server w punktach końcowych serwera. Windows list ACL nie są (jeszcze) obsługiwane przez usługi Azure Files, jeśli pliki są dostępne bezpośrednio w chmurze. |
| Twarde linki | Pominięte | |
| Linki symboliczne | Pominięte | |
| Punkty instalacji | Częściowo obsługiwane | Punkty instalacji może być katalog główny punkt końcowy serwera, ale są one pomijane, jeśli są one w przestrzeni nazw punktu końcowego serwera. |
| Punktach transferu | Pominięte | Na przykład Distributed pliku System DfrsrPrivate i DFSRoots folderów. |
| Punkty ponownej analizy | Pominięte | |
| Kompresja NTFS | W pełni obsługiwane | |
| Pliki rozrzedzone | W pełni obsługiwane | Synchronizacja plików rozrzedzonych (nie są blokowane), ale synchronizacja z chmurą jako cały plik. Zmiana zawartości pliku w chmurze (lub na innym serwerze), plik nie jest już rozrzedzony, gdy zmiany zostały pobrane. |
| Alternatywne strumienie danych (AD) | Zachowane, ale nie zsynchronizowano | Na przykład klasyfikacji tagów, utworzonych przez infrastrukturę klasyfikacji plików, nie są synchronizowane. Istniejące tagi klasyfikacji plików na każdym punkty końcowe serwera są lewej bez zmian. |

> [!Note]  
> Obsługiwane są tylko woluminy systemu plików NTFS. System plików reFS, FAT, FAT32 i innych systemów plików nie są obsługiwane.

### <a name="files-skipped"></a>Zostały pominięte

| Plik lub folder | Uwaga |
|-|-|
| Desktop.ini | Specyficzne dla systemu plików |
| ethumbs.db$ | Plik tymczasowy dla miniatur |
| ~$\*.\* | Plik tymczasowy pakietu Office |
| \*.tmp | Plik tymczasowy |
| \*.laccdb | Dostęp do bazy danych, blokowanie pliku|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Wewnętrzny plik synchronizacji|
| \\Informacji o woluminie systemowym | Folder specyficzne dla woluminu |
| $RECYCLE.BIN| Folder |
| \\SyncShareState | Folder synchronizacji |

### <a name="failover-clustering"></a>Klaster trybu failover
Windows Server Failover Clustering jest obsługiwany przez usługę Azure File Sync w opcji "Serwer plików do użytku ogólnego". Klaster trybu failover nie jest obsługiwana w "Serwer plików skalowalny w poziomie dla danych aplikacji" (SOFS) lub na udostępnionych woluminów klastra (CSV).

> [!Note]  
> W każdym węźle w klastrze trybu Failover w celu synchronizacji działała prawidłowo, musi być zainstalowany agent usługi Azure File Sync.

### <a name="data-deduplication"></a>Funkcja deduplikacji danych
**Wersja agenta 5.0.2.0**   
Funkcja deduplikacji danych jest obsługiwana w woluminach z obsługi warstw włączone w systemie Windows Server 2016 i Windows Server 2019 w chmurze. Włączanie deduplikacji na woluminie z obsługi warstw włączone w chmurze pozwala bez inicjowania obsługi administracyjnej więcej miejsca w pamięci podręcznej więcej plików lokalnych.

**Windows Server 2012 R2 lub starszych wersji agenta**  
W przypadku woluminów, które nie mają obsługi warstw włączone w chmurze usługi Azure File Sync obsługuje deduplikacji danych systemu Windows Server z włączane na woluminie.

### <a name="distributed-file-system-dfs"></a>Rozproszony System plików (DFS)
Usługa Azure File Sync obsługuje współdziałanie z przestrzeni nazw systemu plików DFS (DFS-N) i replikacja systemu plików DFS (DFS-R).

**Przestrzenie nazw systemu plików DFS (DFS-N)**: Usługa Azure File Sync jest w pełni obsługiwany na serwerach z systemem plików DFS-N. Na co najmniej jednego członka systemu plików DFS-N, na synchronizowanie danych między punkty końcowe serwera i punkt końcowy w chmurze, można zainstalować agenta usługi Azure File Sync. Aby uzyskać więcej informacji, zobacz [Przegląd przestrzeni nazw systemu plików DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replikacja systemu plików DFS (DFS-R)**: Systemu plików DFS-R i usługi Azure File Sync są oba rozwiązania replikacji w większości przypadków zalecamy zastąpienie systemu plików DFS-R z usługi Azure File Sync. Istnieje jednak kilka scenariuszy, w których chcesz korzystać ze sobą systemu plików DFS-R i usługi Azure File Sync:

- W przypadku migracji z wdrożenia systemu plików DFS-R do wdrożenia usługi Azure File Sync. Aby uzyskać więcej informacji, zobacz [migracji wdrożenia replikacji systemu plików DFS (DFS-R) do usługi Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nie każdy na lokalnym serwerze, który potrzebuje kopii swoich danych z plików mogą być podłączone bezpośrednio do Internetu.
- Serwery w oddziale firmy Konsolidowanie danych na jednym serwerem centralnym, dla którego chcesz używać usługi Azure File Sync.

W przypadku usługi Azure File Sync i systemu plików DFS-R do pracy side-by-side:

1. Usługa Azure File Sync w chmurze warstw musi być wyłączone w woluminach systemu plików DFS-R replikowane foldery.
2. Punkty końcowe serwera nie powinno być skonfigurowane w folderach tylko do odczytu replikacji systemu plików DFS-R.

Aby uzyskać więcej informacji, zobacz [Omówienie replikacji systemu plików DFS](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Narzędzie Sysprep
Na serwerze, na którym jest zainstalowany agent usługi Azure File Sync za pomocą programu sysprep nie jest obsługiwana i może prowadzić do nieoczekiwanych wyników. Rejestracja agenta instalacji i serwer powinien występować po wdrażanie obrazu serwera i zakończeniu miniinstalacji programu sysprep.

### <a name="windows-search"></a>Windows Search
Jeśli chmura warstw jest włączona w punkcie końcowym serwera, pliki, które są rozmieszczone warstwowo są pominięty i nie są indeksowane przez Windows Search. Pliki warstwowe nie są poprawnie indeksowane.

### <a name="antivirus-solutions"></a>Programy antywirusowe
Ponieważ oprogramowanie antywirusowe polega na skanowanie plików do znanego złośliwego kodu, antywirusowe może powodować odwołania pliki warstwowe. W wersjach 4.0 i powyżej agenta usługi Azure File Sync pliki warstwowe bezpiecznego zestaw FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS atrybutów Windows. Firma Microsoft zaleca konsultacji z dostawcą oprogramowania, aby dowiedzieć się, jak skonfigurować swoje rozwiązanie, aby pominąć odczytywanie plików za pomocą tego zestawu atrybutów (wiele zrobiła to automatycznie).

Rozwiązania firmy Microsoft wewnętrznych oprogramowania antywirusowego, usługa Windows Defender i System Center Endpoint Protection (SCEP), zarówno automatycznie pominięcia odczytu plików, które mają ustawiony ten atrybut. Firma Microsoft zostały one przetestowane i zidentyfikować niewielki problem w jednym: po dodaniu serwera do istniejącej grupy synchronizacji, pliki mniejsze niż 800 bajtów zostaną odwołane (pobieranego) na nowym serwerze. Pliki te pozostaną na nowym serwerze i nie będą umieszczane, ponieważ nie spełniają warstw wymagany rozmiar (> 64kb).

### <a name="backup-solutions"></a>Rozwiązania tworzenia kopii zapasowych
Takich jak rozwiązania antywirusowe rozwiązania tworzenia kopii zapasowych może spowodować wycofanie plików warstwowych. Zalecamy używanie rozwiązania tworzenia kopii zapasowych w chmurze do tworzenia kopii zapasowej udziału plików platformy Azure, a nie lokalnie instalowanym produktem kopii zapasowej.

Jeśli używasz rozwiązania tworzenia kopii zapasowych w środowisku lokalnym, należy wykonać kopie zapasowe na serwerze w grupie synchronizacji, która ma obsługi warstw wyłączone w chmurze. Podczas przywracania, użyj opcji przywracania na poziomie woluminu lub poziomie plików. Przywrócić za pomocą opcji przywracania na poziomie pliku pliki będą synchronizowane z wszystkich punktów końcowych w grupie synchronizacji, a istniejące pliki zostaną zastąpione wersją przywróconej z kopii zapasowej.  Przywracanie na poziomie woluminu nie spowoduje zastąpienia nowsze wersje plików w udziale plików platformy Azure lub inne punkty końcowe serwera.

> [!Note]  
> Przywracanie zera (BMR) może spowodować nieoczekiwane rezultaty i nie jest obecnie obsługiwane.

> [!Note]  
> Migawki VSS (w tym karty poprzednie wersje) nie są obecnie obsługiwane w przypadku woluminów, których obsługi warstw włączone w chmurze. Jeśli chmura warstw jest włączona, użyj migawki udziału plików platformy Azure, aby przywrócić plik z kopii zapasowej.

### <a name="encryption-solutions"></a>Rozwiązań do szyfrowania
Obsługa rozwiązań do szyfrowania zależy od tego, jak są one wykonywane. Wiadomo, że usługa Azure File Sync pracować:

- Szyfrowanie funkcją BitLocker
- Usługa Azure Information Protection, Azure Rights Management Services (Azure RMS) i usługi Active Directory RMS

Usługa Azure File Sync wiadomo, że nie będą działać przy użyciu:

- Systemu plików NTFS System szyfrowania plików (EFS)

Ogólnie rzecz biorąc usługi Azure File Sync powinien obsługiwać współdziałanie z rozwiązań do szyfrowania, które znajdują się poniżej systemu plików, takich jak funkcja BitLocker, a dzięki rozwiązaniom, które są implementowane w formacie pliku, na przykład usługi Azure Information Protection. Nie współdziałania stało się rozwiązania, które znajdują się powyżej systemu plików (takich jak NTFS EFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Inne rozwiązania zarządzania magazynu hierarchicznych (HSM)
Inne rozwiązania sprzętowego modułu zabezpieczeń należy używać usługi Azure File Sync.

## <a name="region-availability"></a>Dostępność w danym regionie
Usługa Azure File Sync jest dostępna tylko w następujących regionach:

| Region | Lokalizacja centrum danych |
|--------|---------------------|
| Australia Wschodnia | Stan Nowa Południowa Walia |
| Australia Południowo-Wschodnia | Stan Wiktoria |
| Brazylia Południowa | Sao Paolo stanu |
| Kanada Środkowa | Toronto |
| Kanada Wschodnia | Miasto Quebec |
| Indie Środkowe | Pune |
| Środkowe stany USA | Iowa |
| Azja Wschodnia | SRA Hongkong |
| Wschodnie stany USA | Wirginia |
| Wschodnie stany USA 2 | Wirginia |
| Japonia Wschodnia | Tokyo, Saitama |
| Japonia Zachodnia | Osaka |
| Środkowo-północne stany USA | Illinois |
| Europa Północna | Irlandia |
| Środkowo-południowe stany USA | Teksas |
| Indie Południowe | Chennai |
| Azja Południowo-Wschodnia | Singapur |
| Południowe Zjednoczone Królestwo | Londyn |
| Zachodnie Zjednoczone Królestwo | Cardiff |
| Europa Zachodnia | Holandia |
| Zachodnie stany USA | Kalifornia |

Usługa Azure File Sync obsługuje synchronizowanie tylko z udziału plików platformy Azure, która znajduje się w tym samym regionie co usługa synchronizacji magazynu.

### <a name="azure-disaster-recovery"></a>Odzyskiwanie po awarii platformy Azure
Aby chronić przed utratą w regionie platformy Azure, usługi Azure File Sync integruje się z [nadmiarowości magazynu geograficznie nadmiarowego](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) opcji (GRS). Magazyn GRS działa przy użyciu replikacji asynchronicznej bloku między magazynu i magazynu w regionie podstawowym, za pomocą którego można normalnie wchodzić w interakcje, w sparowanym regionie pomocniczym. W przypadku awarii, co powoduje, że region platformy Azure tymczasowo lub trwale w trybie offline firma Microsoft będzie magazynu trybu failover do regionu sparowanego. 

> [!Warning]  
> Korzystając z udziału plików platformy Azure jako punktu końcowego w chmurze w ramach konta magazynu GRS, nie należy zainicjować trybu failover z konta magazynu. Ten sposób synchronizacji Przyczyna zatrzymania pracy i może również spowoduje nieoczekiwanej utraty danych w przypadku plików nowo warstwowych. W przypadku utraty region platformy Azure Microsoft spowodują uruchomienie trybu failover konta magazynu w sposób, który jest zgodny z usługi Azure File Sync.

Aby obsługiwać integrację trybu failover dla magazynu geograficznie nadmiarowego i usługi Azure File Sync, we wszystkich regionach usługi Azure File Sync są skojarzone z regionu pomocniczego, który pasuje do regionu pomocniczego, używane przez Magazyn. Te pary są następujące:

| Region podstawowy      | Region sparowany      |
|---------------------|--------------------|
| Australia Wschodnia      | Australia Południowo-Wschodnia |
| Australia Południowo-Wschodnia | Australia Wschodnia     |
| Kanada Środkowa      | Kanada Wschodnia        |
| Kanada Wschodnia         | Kanada Środkowa     |
| Indie Środkowe       | Indie Południowe        |
| Środkowe stany USA          | Wschodnie stany USA 2          |
| Azja Wschodnia           | Azja Południowo-Wschodnia     |
| Wschodnie stany USA             | Zachodnie stany USA            |
| Wschodnie stany USA 2           | Środkowe stany USA         |
| Korea Środkowa       | Korea Południowa        |
| Korea Południowa         | Korea Środkowa      |
| Europa Północna        | Europa Zachodnia        |
| Środkowo-północne stany USA    | Środkowo-południowe stany USA   |
| Indie Południowe         | Indie Środkowe      |
| Azja Południowo-Wschodnia      | Azja Wschodnia          |
| Południowe Zjednoczone Królestwo            | Zachodnie Zjednoczone Królestwo            |
| Zachodnie Zjednoczone Królestwo             | Południowe Zjednoczone Królestwo           |
| Europa Zachodnia         | Europa Północna       |
| Zachodnie stany USA             | Wschodnie stany USA            |

## <a name="azure-file-sync-agent-update-policy"></a>Zasady aktualizacji agenta usługi Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Kolejne kroki
* [Należy wziąć pod uwagę ustawień zapory i serwera proxy](storage-sync-files-firewall-and-proxy.md)
* [Planowanie wdrażania usługi Pliki Azure](storage-files-planning.md)
* [Wdrażanie usługi Pliki Azure](storage-files-deployment-guide.md)
* [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
* [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
