---
title: Planowanie wdrożenia Azure File Sync | Microsoft Docs
description: Dowiedz się, co należy wziąć pod uwagę podczas planowania wdrożenia Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 698702e24f1f6dfc6b94b75de77c08156832e566
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177846"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planowanie wdrażania usługi Azure File Sync
Użyj Azure File Sync, aby scentralizować udziały plików w organizacji w Azure Files, utrzymując elastyczność, wydajność i zgodność lokalnego serwera plików. Funkcja Azure File Sync przekształca system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego protokołu, który jest dostępny w systemie Windows Server, aby uzyskać dostęp do danych lokalnie, w tym SMB, NFS i FTPS. Na całym świecie możesz mieć dowolną liczbę pamięci podręcznych.

W tym artykule opisano ważne zagadnienia dotyczące wdrażania Azure File Sync. Zalecamy także zapoznanie się z [planowaniem wdrożenia Azure Files](storage-files-planning.md). 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Terminologia Azure File Sync
Przed zapoznaj się ze szczegółowymi informacjami o planowaniu wdrożenia Azure File Sync, ważne jest zrozumienie terminologii.

### <a name="storage-sync-service"></a>Usługa synchronizacji magazynu
Usługa synchronizacji magazynu jest zasobem platformy Azure najwyższego poziomu dla Azure File Sync. Zasób usługi synchronizacji magazynu jest elementem równorzędnym zasobu konta magazynu i można go również wdrożyć w grupach zasobów platformy Azure. Wymagany jest odrębny zasób najwyższego poziomu z zasobu konta magazynu, ponieważ usługa synchronizacji magazynu może tworzyć relacje synchronizacji z wieloma kontami magazynu za pośrednictwem wielu grup synchronizacji. W subskrypcji może być wdrożonych wiele zasobów usługi synchronizacji magazynu.

### <a name="sync-group"></a>Grupa synchronizacji
Grupa synchronizacji definiuje topologię synchronizacji dla zestawu plików. Punkty końcowe w ramach grupy synchronizacji są synchronizowane ze sobą. Jeśli na przykład istnieją dwa odrębne zestawy plików, którymi chcesz zarządzać za pomocą Azure File Sync, utworzysz dwie grupy synchronizacji i dodasz różne punkty końcowe do każdej grupy synchronizacji. Usługa synchronizacji magazynu może hostować dowolną liczbę grup synchronizacji.  

### <a name="registered-server"></a>Zarejestrowany serwer
Zarejestrowany obiekt serwera reprezentuje relację zaufania między serwerem (lub klastrem) a usługą synchronizacji magazynu. Możesz zarejestrować dowolną liczbę serwerów w wystąpieniu usługi synchronizacji magazynu. Jednak serwer (lub klaster) można zarejestrować tylko w jednej usłudze synchronizacji magazynu jednocześnie.

### <a name="azure-file-sync-agent"></a>Agent Azure File Sync
Agent usługi Azure File Sync to możliwy do pobrania pakiet, który umożliwia synchronizowanie systemu Windows Server z udziałem plików platformy Azure. Agent Azure File Sync ma trzy główne składniki: 
- **FileSyncSvc. exe**: usługa w tle systemu Windows, która jest odpowiedzialna za monitorowanie zmian w punktach końcowych serwera oraz Inicjowanie sesji synchronizacji z platformą Azure.
- **StorageSync. sys**: filtr systemu plików Azure File Sync, który jest odpowiedzialny za pliki warstwowe do Azure Files (w przypadku włączenia obsługi warstw w chmurze).
- **Polecenia cmdlet zarządzania programu PowerShell**: polecenia cmdlet programu PowerShell, które służą do współpracy z dostawcą zasobów Microsoft. StorageSync. Można je znaleźć w następujących (domyślnych) lokalizacjach:
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Punkt końcowy serwera
Punkt końcowy serwera reprezentuje określoną lokalizację na zarejestrowanym serwerze, taką jak folder na woluminie serwera. Wiele punktów końcowych serwera może istnieć na tym samym woluminie, jeśli ich przestrzenie nazw nie nakładają się na siebie (na przykład `F:\sync1` i `F:\sync2`). Zasady dotyczące warstw chmurowych można skonfigurować osobno dla każdego punktu końcowego serwera. 

Punkt końcowy serwera można utworzyć za pomocą mountpoint. Należy pamiętać, że mountpoints w punkcie końcowym serwera są pomijane.  

Można utworzyć punkt końcowy serwera na woluminie systemowym, ale istnieją dwa ograniczenia, jeśli to zrobisz:
* Nie można włączyć obsługi warstw w chmurze.
* Szybkie przywracanie przestrzeni nazw (gdzie system szybko wyłącza całą przestrzeń nazw, a następnie rozpoczyna odwoływanie zawartości) nie jest wykonywane.


> [!Note]  
> Obsługiwane są tylko woluminy niewymienne.  Dyski mapowane z udziału zdalnego nie są obsługiwane dla ścieżki punktu końcowego serwera.  Ponadto punkt końcowy serwera może znajdować się na woluminie systemu Windows, chociaż Obsługa warstw w chmurze nie jest obsługiwana na woluminie systemowym.

W przypadku dodania lokalizacji serwera, która ma istniejący zestaw plików jako punkt końcowy serwera do grupy synchronizacji, te pliki są scalane z innymi plikami, które znajdują się już w innych punktach końcowych w grupie synchronizacji.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
Punkt końcowy w chmurze to udział plików platformy Azure, który jest częścią grupy synchronizacji. Cała synchronizacja udziału plików platformy Azure i udział plików platformy Azure mogą być elementami członkowskimi tylko jednego punktu końcowego w chmurze. W związku z tym udział plików platformy Azure może być członkiem tylko jednej grupy synchronizacji. Jeśli dodasz udział plików platformy Azure, który ma istniejący zestaw plików jako punkt końcowy w chmurze do grupy synchronizacji, istniejące pliki zostaną scalone z innymi plikami, które znajdują się już w innych punktach końcowych w grupie synchronizacji.

> [!Important]  
> Azure File Sync obsługuje wprowadzanie zmian bezpośrednio w udziale plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać odnalezione za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze tylko raz na 24 godziny. Ponadto zmiany wprowadzone w udziale plików platformy Azure za pośrednictwem protokołu REST nie będą aktualizować czasu ostatniej modyfikacji SMB i nie będą widoczne jako zmiany przez synchronizację. Aby uzyskać więcej informacji, zobacz [Azure Files często zadawanych pytań](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze 
Obsługa warstw w chmurze jest opcjonalną funkcją Azure File Sync, w której często używane pliki są buforowane lokalnie na serwerze, podczas gdy wszystkie inne pliki są warstwami do Azure Files na podstawie ustawień zasad. Aby uzyskać więcej informacji, zobacz [Omówienie obsługi warstw w chmurze](storage-sync-cloud-tiering.md).

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Azure File Sync wymagania systemowe i współdziałanie 
W tej części omówiono wymagania systemowe Azure File Sync agenta i współdziałanie z funkcjami i rolami systemu Windows Server oraz rozwiązaniami innych firm.

### <a name="evaluation-cmdlet"></a>Polecenie cmdlet do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu polecenia cmdlet do oceny Azure File Sync. To polecenie cmdlet sprawdza potencjalne problemy z systemem plików i zestawem danych, na przykład nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Należy zauważyć, że sprawdzenia te obejmują większość funkcji wymienionych poniżej: Zalecamy zapoznanie się z pozostałą częścią tej sekcji, aby upewnić się, że wdrożenie przebiega bezproblemowo. 

Polecenie cmdlet do oceny można zainstalować, instalując moduł AZ PowerShell module, który można zainstalować, postępując zgodnie z instrukcjami tutaj: [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Użycie  
Narzędzie do oceny można wywołać na kilka różnych sposobów: można wykonać testy systemowe, zestawy danych lub oba te elementy. Aby przeprowadzić testy systemu i zestawu danych: 

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
- Serwer z jedną z następujących wersji systemu operacyjnego:

    | Wersja | Obsługiwane jednostki SKU | Obsługiwane opcje wdrażania |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Centrum danych i Standard | Pełne i podstawowe |
    | Windows Server 2016 | Centrum danych i Standard | Pełne i podstawowe |
    | Windows Server 2012 R2 | Centrum danych i Standard | Pełne i podstawowe |
    | Windows Server IoT 2019 dla magazynu| Centrum danych i Standard | Pełne i podstawowe |
    | System Windows Storage Server 2016| Centrum danych i Standard | Pełne i podstawowe |
    | Windows Storage Server 2012 R2| Centrum danych i Standard | Pełne i podstawowe |

    Przyszłe wersje systemu Windows Server zostaną dodane po ich wydaniu.

    > [!Important]  
    > Zalecamy przechowywanie wszystkich serwerów, które są używane z programem Azure File Sync z najnowszymi aktualizacjami z Windows Update. 

- Serwer z co najmniej 2 GiB pamięci.

    > [!Important]  
    > Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna mieć skonfigurowaną minimalną 2048 MiB pamięci.
    
- Lokalnie dołączony wolumin sformatowany przy użyciu systemu plików NTFS.

### <a name="file-system-features"></a>Funkcje systemu plików

| Funkcja | Stan pomocy technicznej | Uwagi |
|---------|----------------|-------|
| Listy kontroli dostępu (ACL) | W pełni obsługiwane | Listy ACL systemu Windows są zachowywane przez Azure File Sync i są wymuszane przez system Windows Server w punktach końcowych serwera. Listy ACL systemu Windows nie są jeszcze obsługiwane przez Azure Files, jeśli pliki są dostępne bezpośrednio w chmurze. |
| Twarde linki | Pominięto | |
| Linki symboliczne | Pominięto | |
| Punkty instalacji | Częściowo obsługiwane | Punkty instalacji mogą być głównym punktem końcowym serwera, ale są pomijane, jeśli są zawarte w przestrzeni nazw punktu końcowego serwera. |
| Połączenia | Pominięto | Na przykład rozproszony system plików folderów DfrsrPrivate i DFSRoots. |
| Punkty ponownej analizy | Pominięto | |
| Kompresja NTFS | W pełni obsługiwane | |
| Pliki rozrzedzone | W pełni obsługiwane | Pliki rozrzedzone — synchronizacja (nie są blokowane), ale są synchronizowane z chmurą jako pełny plik. Jeśli zawartość pliku zostanie zmieniona w chmurze (lub na innym serwerze), plik nie jest już rozrzedzony, gdy zmiana zostanie pobrana. |
| Alternatywne strumienie danych (ADS) | Zachowane, ale nie zsynchronizowane | Na przykład Tagi klasyfikacji utworzone przez infrastrukturę klasyfikacji plików nie są synchronizowane. Istniejące znaczniki klasyfikacji dla plików na każdym z punktów końcowych serwera pozostaną niezmienione. |

> [!Note]  
> Obsługiwane są tylko woluminy NTFS. Systemy plików ReFS, FAT, FAT32 i inne nie są obsługiwane.

### <a name="files-skipped"></a>Pominięte pliki

| Plik/folder | Uwaga |
|-|-|
| plik Desktop. ini | Plik specyficzny dla systemu |
| ethumbs. DB $ | Plik tymczasowy dla miniatur |
| \* ~$. \* | Plik tymczasowy pakietu Office |
| \*. tmp | Plik tymczasowy |
| \*. LACCDB | Plik blokowania dostępu do bazy danych|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Plik synchronizacji wewnętrznej|
| Informacje o woluminie systemowym \\ | Folder określony dla woluminu |
| $RECYCLE. OKREŚLONEJ| Folder |
| \\SyncShareState | Folder do synchronizacji |

### <a name="failover-clustering"></a>Klaster trybu failover
Usługa Windows Server Failover Clustering jest obsługiwana przez Azure File Sync dla opcji wdrażania "serwer plików do użytku ogólnego". Klaster trybu failover nie jest obsługiwany na "Serwer plików skalowalny w poziomie dla danych aplikacji" (SOFS) ani na udostępnionych woluminach klastra (CSV).

> [!Note]  
> Aby synchronizacja działała poprawnie, Agent Azure File Sync musi być zainstalowany na każdym węźle w klastrze trybu failover.

### <a name="data-deduplication"></a>Deduplikacja danych
**5.0.2.0 wersja agenta lub nowsza**   
Funkcja deduplikacji danych jest obsługiwana na woluminach z włączoną obsługą warstw w chmurze w systemie Windows Server 2016. Włączenie deduplikacji danych na woluminie z włączonym obsługą usług Cloud Storage umożliwia przechowywanie w pamięci podręcznej większej liczby plików bez udostępniania większej ilości miejsca w magazynie. 

Po włączeniu deduplikacji danych na woluminie z włączonym obsługą obsługi warstw w chmurze pliki zoptymalizowane pod kątem deduplikacji w lokalizacji punktu końcowego serwera będą warstwowo podobne do normalnego pliku na podstawie ustawień zasad dotyczących warstw chmurowych. Po przeprowadzeniu warstwowych plików zoptymalizowanych pod kątem deduplikacji zadanie odzyskiwania pamięci deduplikacji danych zostanie automatycznie uruchomione w celu odtworzenia miejsca na dysku przez usunięcie niepotrzebnych fragmentów, do których nie odwołują się już inne pliki w woluminie.

Zwróć uwagę, że oszczędności woluminu dotyczą tylko serwera programu; Twoje dane w udziale plików platformy Azure nie zostaną deduplikowane.

> [!Note]  
> Deduplikacja danych i Obsługa warstw w chmurze nie są obecnie obsługiwane na tym samym woluminie na serwerze 2019 z powodu błędu, który zostanie rozwiązany w przyszłej aktualizacji.

**Windows Server 2012 R2 lub starsze wersje agenta**  
W przypadku woluminów, które nie mają włączonej obsługi warstw w chmurze, Azure File Sync obsługuje funkcję deduplikacji danych systemu Windows Server na woluminie.

**Uwagi**
- Jeśli Deduplikacja danych jest zainstalowana przed zainstalowaniem agenta Azure File Sync, ponowne uruchomienie jest wymagane do obsługi deduplikacji danych i warstw w chmurze na tym samym woluminie.
- Jeśli Deduplikacja danych jest włączona w woluminie po włączeniu obsługi warstw w chmurze, początkowe zadanie optymalizacji deduplikacji zoptymalizuje pliki na woluminie, które nie są jeszcze warstwowe i będą miały następujący wpływ na obsługę warstw w chmurze:
    - Zasady wolnego miejsca będą nadal korzystać z warstw plików zgodnie z ilością wolnego miejsca na woluminie przy użyciu mapę cieplną.
    - Zasady dotyczące dat spowodują pominięcie warstw plików, które mogą być w inny sposób kwalifikujące się do obsługi warstw z powodu zadania optymalizacji deduplikacji uzyskującego dostęp do plików.
- W przypadku trwających zadań optymalizacji deduplikacji Obsługa warstw w chmurze z zasadami daty zostanie opóźniona przez ustawienie [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) deduplikacji danych, jeśli plik nie jest już warstwowy. 
    - Przykład: Jeśli ustawienie MinimumFileAgeDays to 7 dni, a zasady dotyczące warstw w chmurze to 30 dni, zasady dat będą mieć pliki warstwy po 37 dniach.
    - Uwaga: gdy plik jest warstwą Azure File Sync, zadanie optymalizacji deduplikacji pominie ten plik.
- Jeśli serwer z systemem Windows Server 2012 R2 z zainstalowanym agentem Azure File Sync został uaktualniony do systemu Windows Server 2016 lub Windows Server 2019, należy wykonać następujące czynności w celu zapewnienia obsługi deduplikacji danych i warstw w chmurze na tym samym woluminie:  
    - Odinstaluj agenta Azure File Sync dla systemu Windows Server 2012 R2 i ponownie uruchom serwer.
    - Pobierz agenta Azure File Sync dla nowej wersji systemu operacyjnego serwera (Windows Server 2016 lub Windows Server 2019).
    - Zainstaluj agenta Azure File Sync i ponownie uruchom serwer.  
    
    Uwaga: ustawienia konfiguracji Azure File Sync na serwerze są zachowywane, gdy agent zostanie odinstalowany i ponownie zainstalowany.

### <a name="distributed-file-system-dfs"></a>Rozproszony system plików (system plików DFS)
Azure File Sync obsługuje międzyoperacyjność z przestrzeniami nazw systemu plików DFS (DFS-N) i Replikacja systemu plików DFS (DFS-R).

**Przestrzenie nazw systemu plików DFS (DFS-n)** : Azure File Sync jest w pełni obsługiwany na serwerach systemu plików DFS-n. Aby synchronizować dane między punktami końcowymi serwera i punktem końcowym w chmurze, można zainstalować agenta Azure File Sync na co najmniej jednym członku systemu plików DFS-N. Aby uzyskać więcej informacji, zobacz [Omówienie przestrzeni nazw systemu plików DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replikacja systemu plików DFS (DFS-r)** : ponieważ system plików DFS-R i Azure File Sync są rozwiązaniami replikacji, zalecamy zastępowanie systemu plików DFS-r i Azure File Sync. Istnieje jednak kilka scenariuszy, w których warto użyć systemu plików DFS-R i Azure File Sync ze sobą:

- Przeprowadzasz migrację z wdrożenia DFS-R do wdrożenia Azure File Sync. Aby uzyskać więcej informacji, zobacz [Migrowanie wdrożenia replikacja systemu plików DFS (DFS-R) do Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nie każdy serwer lokalny, który wymaga kopii danych pliku, może być połączony bezpośrednio z Internetem.
- Serwery gałęzi konsolidują dane na jeden serwer centralny, dla którego chcesz używać Azure File Sync.

Aby Azure File Sync i DFS-R działały obok siebie:

1. Azure File Sync Obsługa warstw w chmurze musi być wyłączona na woluminach z replikowanymi folderami systemu plików DFS-R.
2. Punktów końcowych serwera nie należy konfigurować w folderach replikacji tylko do odczytu systemu plików DFS.

Aby uzyskać więcej informacji, zobacz [Replikacja systemu plików DFS przegląd](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Dzia
Korzystanie z programu Sysprep na serwerze, na którym zainstalowano agenta Azure File Sync, nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Po wdrożeniu obrazu serwera i zakończeniu miniinstalacji programu Sysprep należy przeprowadzić instalację agenta i rejestrację serwera.

### <a name="windows-search"></a>Wyszukiwanie w systemie Windows
Jeśli Obsługa warstw w chmurze jest włączona w punkcie końcowym serwera, pliki, które są warstwami, są pomijane i nie są indeksowane przez funkcję wyszukiwania systemu Windows. Pliki niewarstwowe są indeksowane prawidłowo.

### <a name="antivirus-solutions"></a>Rozwiązania antywirusowe
Ponieważ oprogramowanie antywirusowe działa przez skanowanie plików pod kątem znanego złośliwego kodu, produkt antywirusowy może powodować odwoływanie się do plików warstwowych. W wersji 4,0 i powyżej agenta Azure File Sync pliki warstwowe mają ustawiony atrybut Secure Windows FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS. Firma Microsoft zaleca zapoznanie się z dostawcą oprogramowania, aby dowiedzieć się, jak skonfigurować swoje rozwiązanie, aby pominąć odczytywanie plików z tym zestawem atrybutów (wiele do nich jest automatycznie). 

Wewnętrzne rozwiązania firmy Microsoft dotyczące oprogramowania antywirusowego, Windows Defender i System Center Endpoint Protection (SCEP) automatycznie pomijają odczytywanie plików, które mają ten zestaw atrybutów. Przetestowano i zidentyfikowano jeden drobny problem: po dodaniu serwera do istniejącej grupy synchronizacji pliki o rozmiarze mniejszym niż 800 bajtów są ponownie wywoływane (pobrane) na nowym serwerze. Te pliki pozostaną na nowym serwerze i nie zostaną warstwowe, ponieważ nie spełniają wymagań dotyczących rozmiaru warstwowego (> KB).

> [!Note]  
> Dostawcy oprogramowania antywirusowego mogą sprawdzić zgodność swojego produktu i Azure File Sync przy użyciu [zestawu testów zgodności z programem Azure File Sync Antivirus](https://www.microsoft.com/download/details.aspx?id=58322), który jest dostępny do pobrania w centrum pobierania Microsoft.

### <a name="backup-solutions"></a>Rozwiązania do tworzenia kopii zapasowych
Podobnie jak rozwiązania antywirusowe, rozwiązania do tworzenia kopii zapasowych mogą powodować odwoływanie się do plików warstwowych. Zalecamy używanie rozwiązania do tworzenia kopii zapasowych w chmurze w celu utworzenia kopii zapasowej udziału plików platformy Azure, a nie lokalnego produktu do tworzenia kopii zapasowych.

Jeśli używasz lokalnego rozwiązania do tworzenia kopii zapasowych, kopie zapasowe powinny być wykonywane na serwerze w grupie synchronizacji z wyłączonymi warstwami chmury. Podczas przywracania należy użyć opcji przywracania na poziomie woluminu lub na poziomie pliku. Pliki przywrócone przy użyciu opcji przywracania na poziomie pliku zostaną zsynchronizowane ze wszystkimi punktami końcowymi w grupie synchronizacji, a istniejące pliki zostaną zastąpione wersją przywróconą z kopii zapasowej.  Przywrócenie na poziomie woluminu nie spowoduje zastąpienia nowszych wersji plików w udziale plików platformy Azure ani w innych punktach końcowych serwera.

> [!Note]  
> Przywracanie bez systemu operacyjnego (BMR) może spowodować nieoczekiwane wyniki i nie jest obecnie obsługiwane.

> [!Note]  
> Migawki usługi VSS (w tym poprzednie wersje) nie są obecnie obsługiwane na woluminach, na których włączono obsługę warstw w chmurze. Jeśli włączono obsługę warstw w chmurze, użyj migawek udziałów plików platformy Azure, aby przywrócić plik z kopii zapasowej.

### <a name="encryption-solutions"></a>Rozwiązania do szyfrowania
Obsługa rozwiązań szyfrowania zależy od sposobu ich implementacji. Azure File Sync jest znana z:

- Szyfrowanie funkcją BitLocker
- Azure Information Protection, Azure Rights Management Services (Azure RMS) i Active Directory RMS

Azure File Sync nie działa z:

- System szyfrowania plików NTFS (EFS)

Ogólnie rzecz biorąc Azure File Sync powinien obsługiwać współdziałanie z rozwiązaniami szyfrowania, które znajdują się poniżej systemu plików, takich jak funkcja BitLocker, oraz z rozwiązaniami, które są zaimplementowane w formacie pliku, na przykład Azure Information Protection. Nie wprowadzono żadnego specjalnego współdziałania dla rozwiązań znajdujących się powyżej systemu plików (takich jak system plików NTFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Inne rozwiązania do zarządzania magazynem hierarchicznym (HSM)
W przypadku Azure File Sync nie należy używać innych rozwiązań modułu HSM.

## <a name="region-availability"></a>Dostępność w poszczególnych regionach
Azure File Sync jest dostępna tylko w następujących regionach:

| Region | Lokalizacja centrum danych |
|--------|---------------------|
| Australia Wschodnia | Stan Nowa Południowa Walia |
| Australia Południowo-Wschodnia | Stan Wiktoria |
| Brazylia Południowa | Sao Paulo (stan) |
| Kanada Środkowa | Toronto |
| Kanada Wschodnia | Quebec |
| Indie Środkowe | Pune |
| Środkowe stany USA | Iowa |
| Azja Wschodnia | Hongkong SAR |
| Wschodnie stany USA | Wirginia |
| Wschodnie stany USA 2 | Wirginia |
| Francja Środkowa | Paryż |
| Francja Południowa * | Marsylia |
| Korea Środkowa | Seul |
| Korea Południowa | Pusan |
| Japonia Wschodnia | Tokyo, Saitama |
| Japonia Zachodnia | Osaka |
| Północno-środkowe stany USA | Illinois |
| Europa Północna | Irlandia |
| Północna Republika Południowej Afryki | Johannesburg |
| Zachodnia Republika Południowej Afryki * | Kapsztad |
| Południowo-środkowe stany USA | Teksas |
| Indie Południowe | Chennai |
| Azja Południowo-Wschodnia | Singapur |
| Południowe Zjednoczone Królestwo | Londyn |
| Zachodnie Zjednoczone Królestwo | Cardiff |
| US Gov Arizona | Arizona |
| US Gov Teksas | Teksas |
| US Gov Wirginia | Wirginia |
| Północne Zjednoczone Emiraty Arabskie | Dubaj |
| Środkowe Zjednoczone Emiraty Arabskie * | Abu Zabi |
| Europa Zachodnia | Holandia |
| Zachodnio-środkowe stany USA | Wyoming |
| Zachodnie stany USA | Kalifornia |
| Zachodnie stany USA 2 | Waszyngton |

Azure File Sync obsługuje synchronizowanie tylko z udziałem plików platformy Azure, który znajduje się w tym samym regionie co usługa synchronizacji magazynu.

W przypadku regionów oznaczonych gwiazdkami należy skontaktować się z pomocą techniczną platformy Azure, aby zażądać dostępu do usługi Azure Storage w tych regionach. Proces został przedstawiony w [tym dokumencie](https://azure.microsoft.com/global-infrastructure/geographies/).

### <a name="azure-disaster-recovery"></a>Odzyskiwanie po awarii platformy Azure
Aby chronić przed utratą w regionie świadczenia usługi Azure, Azure File Sync integruje się z opcją [nadmiarowości magazynu geograficznie nadmiarowego](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS). Magazyn GRS działa przy użyciu asynchronicznej replikacji bloków między magazynem w regionie podstawowym, z którym zwykle odbywa się praca, i magazynu w sparowanym regionie pomocniczym. W przypadku awarii, która powoduje tymczasowe lub trwałe przejście regionu platformy Azure do trybu offline, firma Microsoft przejdzie w tryb failover do sparowanego regionu. 

> [!Warning]  
> Jeśli używasz udziału plików platformy Azure jako punktu końcowego w chmurze na koncie magazynu GRS, nie należy inicjować trybu failover dla konta magazynu. Wykonanie tej operacji spowoduje, że synchronizacja przestanie działać, a także może spowodować nieoczekiwaną utratę danych w przypadku nowych plików warstwowych. W przypadku utraty regionu platformy Azure firma Microsoft będzie wyzwalać tryb failover na koncie magazynu w sposób zgodny z Azure File Sync.

Aby zapewnić obsługę integracji trybu failover między magazynem geograficznie nadmiarowym i Azure File Sync, wszystkie regiony Azure File Sync są sparowane z regionu pomocniczego, który jest zgodny z regionem pomocniczym używanym przez magazyn. Te pary są następujące:

| Region podstawowy      | Region sparowany      |
|---------------------|--------------------|
| Australia Wschodnia      | Australia Południowo-Wschodnia|
| Australia Południowo-Wschodnia | Australia Wschodnia     |
| Brazylia Południowa        | Południowo-środkowe stany USA   |
| Kanada Środkowa      | Kanada Wschodnia        |
| Kanada Wschodnia         | Kanada Środkowa     |
| Indie Środkowe       | Indie Południowe        |
| Środkowe stany USA          | Wschodnie stany USA 2          |
| Azja Wschodnia           | Azja Południowo-Wschodnia     |
| Wschodnie stany USA             | Zachodnie stany USA            |
| Wschodnie stany USA 2           | Środkowe stany USA         |
| Francja Środkowa      | Francja Południowa       |
| Francja Południowa        | Francja Środkowa     |
| Japonia Wschodnia          | Japonia Zachodnia         |
| Japonia Zachodnia          | Japonia Wschodnia         |
| Korea Środkowa       | Korea Południowa        |
| Korea Południowa         | Korea Środkowa      |
| Europa Północna        | Europa Zachodnia        |
| Północno-środkowe stany USA    | Południowo-środkowe stany USA   |
| Północna Republika Południowej Afryki  | Północna Republika Południowej Afryki  |
| Północna Republika Południowej Afryki   | Północna Republika Południowej Afryki |
| Południowo-środkowe stany USA    | Północno-środkowe stany USA   |
| Indie Południowe         | Indie Środkowe      |
| Azja Południowo-Wschodnia      | Azja Wschodnia          |
| Południowe Zjednoczone Królestwo            | Zachodnie Zjednoczone Królestwo            |
| Zachodnie Zjednoczone Królestwo             | Południowe Zjednoczone Królestwo           |
| US Gov Arizona      | US Gov Teksas       |
| US Gov Iowa         | US Gov Wirginia    |
| US Gov Wirginia      | US Gov Teksas       |
| Europa Zachodnia         | Europa Północna       |
| Zachodnio-środkowe stany USA     | Zachodnie stany USA 2          |
| Zachodnie stany USA             | Wschodnie stany USA            |
| Zachodnie stany USA 2           | Zachodnio-środkowe stany USA    |

## <a name="azure-file-sync-agent-update-policy"></a>Zasady aktualizacji agenta usługi Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Następne kroki
* [Rozważ użycie ustawień zapory i serwera proxy](storage-sync-files-firewall-and-proxy.md)
* [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
* [Wdróż Azure Files](storage-files-deployment-guide.md)
* [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
* [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
