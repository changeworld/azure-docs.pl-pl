---
title: Często zadawane pytania (FAQ) dla usługi Azure Files | Dokumentacja firmy Microsoft
description: Znajdź odpowiedzi na często zadawane pytania na temat usługi Azure Files.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.date: 07/19/2018
ms.author: renash
ms.component: files
ms.openlocfilehash: 31f5b2792aa83d15a1478cf201ca674995816430
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "42056519"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Często zadawane pytania (FAQ) dotyczące usługi Azure Files
[Usługa Azure Files](storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem będące standardami branżowymi [protokołu bloku komunikatów serwera (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx). Udziały plików platformy Azure można zainstalować równolegle na chmurowych lub lokalnych wdrożeń systemu Windows, Linux i macOS. Udziały plików platformy Azure na komputerach z systemem Windows Server, również buforujesz przy użyciu usługi Azure File Sync w celu zapewnienia szybkiego dostępu blisko użycia danych.

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Files funkcje i możliwości, łącznie z użyciem usługi Azure File Sync za pomocą usługi Azure Files. Jeśli nie widzisz odpowiedź na Twoje pytanie, możesz skontaktować się ze nam za pośrednictwem poniższych kanałów (w kolejności szybciej):

1. Komentarze części tego artykułu.
2. [Forum usługi Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [W usłudze pliki Azure UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. pomocą techniczną firmy Microsoft. Aby utworzyć nowe żądanie obsługi w witrynie Azure portal na **pomocy** zaznacz **Pomoc i obsługa techniczna** przycisk, a następnie wybierz **nowe żądanie obsługi**.

## <a name="general"></a>Ogólne
* <a id="why-files-useful"></a>
**Jak usługi Azure Files jest przydatne?**  
   Usługi Azure Files można użyć do tworzenia udziałów plików w chmurze, nie są odpowiedzialni za zarządzanie obciążenie serwera fizycznego, urządzenie lub urządzenia. Robimy monotonii pracy dla Ciebie, uwzględniając stosowanie aktualizacji systemu operacyjnego i wymieniać uszkodzonych dysków. Aby dowiedzieć się więcej na temat scenariuszy, które usługi pliki Azure może Ci pomóc, zobacz [przydaje się dlaczego usługi Azure Files](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
**Jakie są różne sposoby uzyskiwania dostępu do plików w usłudze Azure Files?**  
    Możesz zainstalować udział plików na komputerze lokalnym przy użyciu protokołu SMB 3.0 lub można użyć narzędzi, takich jak [Eksploratora usługi Storage](http://storageexplorer.com/) uzyskiwania dostępu do plików w udziale plików. Z poziomu aplikacji dostęp do Twoich plików w udziale plików platformy Azure można użyć bibliotek klienckich magazynu, interfejsów API REST, programu PowerShell lub wiersza polecenia platformy Azure.

* <a id="what-is-afs"></a>
**Co to jest usługa Azure File Sync?**  
    Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files przy jednoczesnym zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Usługa Azure File Sync przekształca maszyny z systemem Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Można użyć dowolnego protokołu, który jest dostępny w systemie Windows Server oraz dostęp do danych lokalnie, w tym protokołu SMB, Network File System (NFS) i usługi protokołu transferu plików (FTPS). Może mieć dowolną liczbę pamięci podręcznych potrzebnych na całym świecie.

* <a id="files-versus-blobs"></a>
**Dlaczego należy używać udziału plików platformy Azure i usługi Azure Blob storage dla moich danych?**  
    Usługi Azure Files i obiektów Blob platformy Azure, magazynu, oba programy oferują sposoby przechowywania dużych ilości danych w chmurze, ale są one użyteczne do nieco innych celów. 
    
    Usługa Azure Blob storage jest przydatne w przypadku bardzo dużej skali, natywnych dla chmury aplikacji, które muszą przechowywać dane bez określonej struktury. Aby zmaksymalizować wydajność i skalę, usługi Azure Blob storage jest prostsze abstrakcji magazynu niż prawdziwy system plików. Tylko za pośrednictwem biblioteki klienta opartego na protokole REST (lub bezpośrednio za pomocą protokołu opartego na protokole REST) można uzyskać dostęp do usługi Azure Blob storage.

    Usługa Azure Files jest specjalnie systemu plików. Usługa Azure Files ma wszystkie streszczenia plików, które znasz i lubisz na bazie wielu lat pracy z systemów operacyjnych w środowisku lokalnym. Np. usługi Azure Blob storage Azure Files oferuje interfejs REST i biblioteki klienta opartego na protokole REST. W przeciwieństwie do usługi Azure Blob storage Azure Files oferuje SMB dostęp do udziałów plików platformy Azure. Za pomocą protokołu SMB, można zainstalować udział plików platformy Azure bezpośrednio w Windows, Linux lub macOS, lokalnie lub w chmurze w przypadku maszyn wirtualnych bez konieczności pisania kodu lub dołączanie żadnych specjalnych sterowników w systemie plików. Udziały plików platformy Azure na serwerach plików w środowisku lokalnym, również buforujesz przy użyciu usługi Azure File Sync szybkiego dostępu blisko użycia danych. 
   
    Aby uzyskać bardziej szczegółowy opis na temat różnic między plikami platformy Azure i usługi Azure Blob storage, zobacz [przy wyborze rozwiązania, kiedy należy używać usługi Azure Blob storage, Azure Files lub Azure Disks](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Aby dowiedzieć się więcej na temat usługi Azure Blob storage, zobacz [wprowadzenie do usługi Blob storage](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Dlaczego należy używać udziału plików platformy Azure zamiast dysków platformy Azure?**  
    Dysk w usługi Azure Disks to po prostu dysk. Można pobrać wartości z usługi Azure Disks, należy dołączyć dysku do maszyny wirtualnej, która działa na platformie Azure. Dyski platformy Azure może służyć do wszystko, czego użyje dysku dla serwera lokalnego. Można go użyć jako dysk systemu, jako obszar wymiany dla systemu operacyjnego lub jako dedykowanych dla magazynu dla aplikacji. Interesujące dla usługi Azure Disks polega na utworzeniu serwera plików w chmurze do użycia w tych samych miejsc, w którym mogą korzystać z udziału plików platformy Azure. Wdrażanie serwera plików w usłudze Azure Virtual Machines to wydajny sposób uzyskać usługi file storage na platformie Azure, gdy potrzebujesz opcje wdrażania, które obecnie nie są obsługiwane przez usługi Azure Files (np. NFS protokołu pomocy technicznej lub premium storage). 

    Jednak korzystanie z serwera plików za pomocą usługi Azure Disks jako magazynu zaplecza zwykle jest znacznie bardziej kosztowne niż korzystanie z udziału plików platformy Azure dla kilka możliwych przyczyn. Po pierwsze oprócz płacić za ilość miejsca do magazynowania, również musisz zapłacić za koszty uruchamiania maszyn wirtualnych platformy Azure. Po drugie można również zarządzać maszyn wirtualnych, które są używane do uruchamiania serwera plików. Na przykład ponosisz odpowiedzialność za uaktualnień systemu operacyjnego. Na koniec Jeśli potrzebujesz ostatecznie dane mogą być buforowane w środowisku lokalnym, jest użytkownikowi w celu konfigurowania i zarządzania nią technologii replikacji, takich jak Distributed pliku System replikacji (DFSR), aby to zrobić.

    Jedno z podejść do uzyskiwania najlepsze cechy zarówno usługi Azure Files, jak i serwera plików, który jest hostowany w usłudze Azure Virtual Machines (oprócz przy użyciu usługi Azure Disks jako magazynu zaplecza) dotyczy instalacji usługi Azure File Sync na serwerze plików, która jest hostowana w chmurze maszyny Wirtualnej. Jeśli udział plików platformy Azure znajduje się w tym samym regionie co serwer plików, można włączyć chmury warstw, a następnie ustaw ilość procent wolnego miejsca na wartość maksymalną (99%). Dzięki temu minimalny zduplikowanie danych. Możesz również użyć wszystkich aplikacji, które chcesz przy użyciu serwerów plików, takich jak aplikacje, które wymagają protokołu NFS obsługują.

    Aby uzyskać informacji na temat opcji konfigurowania serwera plików o wysokiej wydajności i dostępności na platformie Azure, zobacz [klastrów gościa wdrażanie maszyn wirtualnych IaaS w programie Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Aby uzyskać bardziej szczegółowy opis różnic między usługą Azure Files i dysków platformy Azure, zobacz [przy wyborze rozwiązania, kiedy należy używać usługi Azure Blob storage, Azure Files lub Azure Disks](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Aby dowiedzieć się więcej na temat usługi Azure Disks, zobacz [Omówienie usługi Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
**Jak rozpocząć pracę przy użyciu usługi Azure Files?**  
   Wprowadzenie do usługi Azure Files jest proste. Po pierwsze, [Utwórz udział plików](storage-how-to-create-file-share.md)i zainstalować go w preferowanego systemu operacyjnego: 

    * [Zainstaluj w Windows](storage-how-to-use-files-windows.md)
    * [Instalowanie w systemie Linux](storage-how-to-use-files-linux.md)
    * [Instalowanie w systemie macOS](storage-how-to-use-files-mac.md)

   Bardziej szczegółowe wskazówki dotyczące wdrażania udziału plików platformy Azure do zastąpienia udziałów plików w środowisku produkcyjnym w Twojej organizacji, zobacz [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md).

* <a id="redundancy-options"></a>
**Jakie opcje nadmiarowości magazynu są obsługiwane przez usługi Azure Files?**  
    Obecnie usługa Azure Files obsługuje magazyn lokalnie nadmiarowy (LRS), Magazyn strefowo nadmiarowy (ZRS) i Magazyn geograficznie nadmiarowy (GRS). Planujemy do obsługi magazynu geograficznie nadmiarowego (RA-GRS) magazyn do odczytu w przyszłości, ale nie mamy osi czasu, aby udostępnić w tej chwili.

* <a id="tier-options"></a>
**Jakie warstwy magazynowania są obsługiwane w usłudze Azure Files?**  
    Obecnie usługa Azure Files obsługuje tylko warstwy magazynu w warstwie standardowa. Nie mamy osi czasu, aby udostępnić usługę premium storage i magazynu chłodnego pomocy technicznej w tej chwili. 
    
    > [!NOTE]
    > Nie można utworzyć udziały plików platformy Azure, z konta magazynu tylko dla obiektów blob lub kont usługi premium storage.

* <a id="give-us-feedback"></a>
**Czy na pewno chcesz zobaczyć określoną funkcję, dodany do usługi Azure Files. Możesz dodać go?**  
    Zespół usługi Azure Files jest zainteresowany wysłuchaniu wszelkie opinie, w których masz o naszej usługi. Należy zagłosować na sugestie funkcji w [UserVoice plików Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Chcemy do przodu przykuwających możesz z wieloma nowymi funkcjami.

## <a name="azure-file-sync"></a>Usługa Azure File Sync

* <a id="afs-region-availability"></a>
**Jakie regiony są obsługiwane w przypadku usługi Azure File Sync?**  
    Lista dostępnych regionów można znaleźć na [dostępność w poszczególnych regionach](storage-sync-files-planning.md#region-availability) przewodnik sekcji planowania usługi Azure File Sync. Stale dodamy Obsługa dodatkowych regionów, w tym niepublicznych regionów.

* <a id="cross-domain-sync"></a>
**W tej samej grupie synchronizacji można mieć serwerów przyłączonych do domeny i nieprzyłączonych do domeny?**  
    Tak. Grupa synchronizacji może zawierać punkty końcowe serwera, które mają różne członkostwa w usłudze Active Directory, nawet jeśli nie są przyłączone do domeny. Chociaż ta konfiguracja z technicznego punktu widzenia działa, firma Microsoft nie jest to zalecane jako typowej konfiguracji ponieważ listy kontroli dostępu (ACL), które są zdefiniowane dla plików i folderów na jednym serwerze może nie być w stanie zostaną wymuszone przez inne serwery w grupie synchronizacji. Aby uzyskać najlepsze wyniki zaleca się synchronizacji między serwerami, które znajdują się w tym samym lesie usługi Active Directory, między serwerami, które znajdują się w różnych lasach usługi Active Directory, ale która ustalonymi relacjami zaufania lub między serwerami, które nie znajdują się w domenie. Zaleca się unikać przy użyciu kombinacji tych konfiguracji.

* <a id="afs-change-detection"></a>
**Utworzono plik bezpośrednio w mojej udziału plików platformy Azure przy użyciu protokołu SMB lub w portalu. Jak długo trwa dla pliku, aby zsynchronizować z serwerów w grupie synchronizacji?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Jeśli ten sam plik jest zmieniany na dwa serwery w tym samym czasie, co dzieje?**  
    Usługa Azure File Sync używa strategii Rozwiązywanie prostych konfliktów: przechowujemy obie zmiany do plików, które zostały zmienione na dwóch serwerach, w tym samym czasie. Niedawno napisanych zmiany przechowuje oryginalna nazwa pliku. Starszy plik ma maszyny "źródło" i liczbę konfliktów, dołączany do wybranej nazwy. Jest zgodna z ten taksonomii: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\< ext\>  

    Na przykład pierwszy konflikt CompanyReport.docx staje się CompanyReport CentralServer.docx przypadku CentralServer, gdzie wystąpiło starsze zapisu. Drugi konflikt będą miały postać CompanyReport-CentralServer-1.docx.

* <a id="afs-storage-redundancy"></a>
**Magazyn geograficznie nadmiarowy jest obsługiwane dla usługi Azure File Sync?**  
    Tak, usługi Azure Files obsługuje zarówno magazyn lokalnie nadmiarowy (LRS) i Magazyn geograficznie nadmiarowy (GRS). Występuje GRS pracę awaryjną między sparowane regiony, zaleca się traktowanie nowy region do przechowywania kopii zapasowych danych tylko. Usługa Azure File Sync nie automatycznie rozpocznie się synchronizowanie przy użyciu nowego regionu podstawowego. 

* <a id="sizeondisk-versus-size"></a>
**Dlaczego nie *rozmiar na dysku* właściwość dopasowania plików *rozmiar* właściwości po zakończeniu korzystania z usługi Azure File Sync?**  
    Eksploratora plików Windows udostępnia dwie właściwości do reprezentowania rozmiaru pliku: **rozmiar** i **rozmiar na dysku**. Te właściwości różnią się w niewielkim stopniu w znaczenie. **Rozmiar** reprezentuje pełną rozmiaru pliku. **Rozmiar na dysku** reprezentuje rozmiar strumienia pliku, który jest przechowywany na dysku. Wartości tych właściwości można różnią się z różnych powodów, takich jak kompresja, korzystanie z funkcji deduplikacji danych lub obsługa warstw przy użyciu usługi Azure File Sync w chmurze. Jeśli plik jest warstwowe udziału plików platformy Azure, rozmiar na dysku wynosi zero, ponieważ strumienia pliku są przechowywane w udziale plików platformy Azure, a nie na dysku. Istnieje także możliwość pliku do się częściowo warstwowego (lub częściowo odwołane). W pliku częściowo warstwowego część pliku znajduje się na dysku. Taka sytuacja może wystąpić, częściowo są odczytywane przez aplikacje, takie jak odtwarzaczy multimedialnych lub zip narzędzia plików. 

* <a id="is-my-file-tiered"></a>
**Jak sprawdzić, czy plik został warstwy?**  
    Istnieje kilka sposobów, aby sprawdzić, czy plik został warstwy do udziału plików platformy Azure:
    
   *  **Sprawdź atrybuty pliku do pliku.**
     Aby to zrobić, kliknij prawym przyciskiem myszy w pliku, przejdź do **szczegóły**, a następnie przewiń w dół do **atrybuty** właściwości. Plikami warstwowym ma następujące atrybuty zestawu:     
        
        | Atrybut litera | Atrybut | Definicja |
        |:----------------:|-----------|------------|
        | A | Archiwum | Wskazuje, że plik kopie zapasowe powinny być za pomocą oprogramowania kopii zapasowej. Ten atrybut ma zawsze wartość niezależnie od tego, czy plik jest warstwowe lub pełni przechowywane na dysku. |
        | P | Plik rozrzedzony | Wskazuje, że plik jest plik rozrzedzony. Plik rozrzedzony jest specjalistyczną odmianą pliku, który oferuje systemu plików NTFS do efektywnego wykorzystania, gdy plik w usłudze stream dysku przede wszystkim jest pusta. Usługa Azure File Sync używa plików rozrzedzonych, ponieważ warstwowego w pełni lub częściowo przypomnieć pliku. W pełni warstwowego pliku strumienia pliku są przechowywane w chmurze. W pliku częściowo odwołane, że plik znajduje się już na dysku. Jeśli plik jest w pełni przypomnieć na dysku, usługi Azure File Sync konwertuje ją z plik rozrzedzony zwykły plik. |
        | L | Punkt ponownej analizy | Wskazuje, że plik ma punkt ponownej analizy. Punkt ponownej analizy to specjalne wskaźnika do użytku przez filtr systemu plików. Usługa Azure File Sync używa punktów ponownej analizy do definiowania do filtru systemu plików usługi Azure File Sync (StorageSync.sys) lokalizacji chmury, gdzie plik jest przechowywany. To umożliwia bezproblemowe. Użytkownicy nie będą musieli wiedzieć, że jest używana usługa Azure File Sync lub sposobu uzyskania dostępu do pliku w udziale plików platformy Azure. Gdy plik jest w pełni przypomnieć, usługi Azure File Sync Usuwa punkt ponownej analizy z pliku. |
        | O | Offline | Wskazuje, że część lub całość zawartości nie są przechowywane na dysku. Gdy plik jest w pełni przypomnieć, usługi Azure File Sync usuwa tego atrybutu. |

        ![Okno dialogowe właściwości pliku, z wybraną kartą szczegóły](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Można zobaczyć atrybuty dla wszystkich plików w folderze, dodając **atrybuty** pola do wyświetlenia tabeli Eksploratora plików. Aby to zrobić, kliknij prawym przyciskiem myszy istniejącą kolumnę (na przykład **rozmiar**), wybierz opcję **więcej**, a następnie wybierz pozycję **atrybuty** z listy rozwijanej.
        
   * **Użyj `fsutil` pod kątem punktów ponownej analizy z pliku.**
       Zgodnie z opisem w poprzedniej opcji, plikami warstwowym zawsze ma zestaw punktu ponownej analizy. Wskaźnikiem ponownej analizy to specjalne wskaźnika dla filtru systemu plików usługi Azure File Sync (StorageSync.sys). Aby sprawdzić, czy plik ma punkt ponownej analizy, w oknie wiersza polecenia lub programu PowerShell z podwyższonym poziomem uprawnień, uruchom `fsutil` narzędzie:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Jeśli plik ma punkt ponownej analizy, powinna się pojawić **ponownej analizy, wartość tagu: 0x8000001e**. Ta wartość szesnastkowa to wartość punktu ponownej analizy, która jest właścicielem usługi Azure File Sync. Dane wyjściowe zawierają również dane ponownej analizy, która reprezentuje ścieżkę do pliku w udziale plików platformy Azure.

        > [!WARNING]  
        > `fsutil reparsepoint` Narzędzie polecenie ma również możliwość usunięcia punktów ponownej analizy. Nie wykonuj tego polecenia, chyba, że zespół inżynierów usługi Azure File Sync monit. Uruchomienie tego polecenia może spowodować utratę danych. 

* <a id="afs-recall-file"></a>**Plik, który ma być użyty został warstwowego. Jak można odwołać pliku na dysku, aby używać go lokalnie?**  
    Najprostszym sposobem odwołania pliku na dysku jest można otworzyć pliku. Filtr systemu plików usługi Azure File Sync (StorageSync.sys) bezproblemowo pobiera plik z udziału plików platformy Azure bez konieczności wykonywania pracy ze strony użytkownika. Dla typów plików, które mogą być częściowo odczytu, takich jak pliki multimedialne lub zip, otwieranie pliku nie jest pobierany całego pliku.

    Również umożliwia PowerShell wymuszenie można wycofać. Ta opcja może być przydatne, jeśli chcesz odwołać wielu plików jednocześnie, np. wszystkie pliki w folderze. Otwórz sesję programu PowerShell, aby węzeł serwera, na którym zainstalowano usługę Azure File Sync, a następnie uruchom następujące polecenia środowiska PowerShell:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>
**Jak wymusić, plik lub katalog do umieszczane?**  
    Gdy jest włączona funkcja obsługi warstw w chmurze, obsługi warstw automatycznie pliki warstw w chmurze oparte na ostatni dostęp i zmodyfikuj czas do osiągnięcia procent wolnego miejsca na woluminie określone na punkt końcowy w chmurze. Czasami jednak warto to wymuszone ręcznie plik do warstwy. Może to być przydatne, jeśli zapiszesz dużych plików, które nie będą do ponownego użycia przez długi czas i mają ilość wolnego miejsca na woluminie teraz do użycia dla innych plików i folderów. Można wymusić warstw przy użyciu następujących poleceń programu PowerShell:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-effective-vfs"></a>
**Jak jest *wolne miejsce w woluminie* interpretowany, jeśli mam wiele punktów końcowych serwera na woluminie?**  
    Jeśli istnieje więcej niż jeden punkt końcowy serwera na woluminie, próg wolnego miejsca na efektywne woluminu jest największy wolne miejsce w woluminie określona dla dowolnego punktu końcowego serwera, w tym woluminie. Pliki będą umieszczane w taki sposób, zgodnie z ich wzorce użycia niezależnie od tego, z którym punktem końcowym serwera, do którego należą. Na przykład jeśli masz dwa punkty końcowe serwera na woluminie Punk końcowy 1 i Endpoint2, gdzie Punk końcowy 1 ma próg wolnego miejsca na wolumin o 25%, a Endpoint2 próg wolnego miejsca na woluminie 50% próg wolnego miejsca na woluminie zarówno punkty końcowe serwera będzie 50%.

* <a id="afs-files-excluded"></a>
**Które pliki lub foldery są automatycznie wykluczane przez usługę Azure File Sync?**  
    Domyślnie usługa Azure File Sync nie obejmuje następujące pliki:
    * desktop.ini
    * thumbs.db
    * ehthumbs.dB
    * ~$\*.\*
    * \*.laccdb
    * \*.tmp
    * 635D02A9D91C401B97884B82B3BCDAEA.\*

    Następujące foldery są również wyłączone domyślnie:

    * \System volume Information
    * \$RECYCLE.BIN
    * \SyncShareState

* <a id="afs-os-support"></a>
**Z systemu Windows Server 2008 R2, Linux lub urządzenie magazynu dołączone do sieci (NAS) można używać usługi Azure File Sync?**  
    Obecnie usługa Azure File Sync obsługuje tylko w systemie Windows Server 2016 i Windows Server 2012 R2. W tej chwili nie mamy inne plany, które firma Microsoft może udostępniać, ale jest otwarty w celu obsługi dodatkowych platform, na podstawie oczekiwań klienta. Daj nam znać o [UserVoice plików Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files) platformach mamy do obsługi.

* <a id="afs-tiered-files-out-of-endpoint"></a>
**Dlaczego pliki warstwowe są dostępne poza przestrzeni nazw punktu końcowego serwera**  
    Przed wersją agenta usługi Azure File Sync 3 usługi Azure File Sync zablokowane przeniesienie plików warstwowych, poza punkt końcowy serwera, ale w tym samym woluminie jako punkt końcowy serwera. Operacje kopiowania, przenosi plików warstwowy i przesuwa z warstwowego na inne woluminy nie miała wpływu. Przyczyna tego zachowania była niejawna zakładając, że Eksplorator plików i innych interfejsów API Windows, przeniesienia operacji na tym samym woluminie czy (prawie) instanenous Zmień nazwę operacji. Oznacza to, przenosi spowoduje Eksploratorze plików lub innych metod przenoszenia (na przykład wiersza polecenia lub programu PowerShell) są wyświetlane odpowiadać, podczas gdy usługi Azure File Sync odwołuje dane z chmury. Począwszy od [wersja agenta usługi Azure File Sync 3.0.12.0](storage-files-release-notes.md#agent-version-30120), usługi Azure File Sync pozwala przenieść plik warstwowy poza punktu końcowego serwera. Firma Microsoft należy unikać negatywnego wpływu, wcześniej wspomniano, dzięki czemu plikami warstwowym istnieje jako plik warstwowy poza punkt końcowy serwera, a następnie odwołująca plik w tle. Oznacza to, że przenosi na tym samym woluminie są instaneous i robimy całą pracę, przywołanie pliku na dysku, po ukończeniu przenoszenia. 

* <a id="afs-do-not-delete-server-endpoint"></a>
**Mam problem z usługi Azure File Sync na serwerze (synchronizacja chmury warstw itp). Należy I usunięcie i ponowne utworzenie Mój punkt końcowy serwera?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
**Czy mogę przenieść usługę synchronizacji magazynu i/lub konta magazynu do innej grupy zasobów lub subskrypcji?**  
   Tak, usługa synchronizacji magazynu i/lub konta magazynu można przenieść do innej grupy zasobów lub subskrypcji. Jeśli konto magazynu zostanie przeniesiony, należy udzielić dostępu usługi hybrydowe File Sync do konta magazynu (zobacz [upewnij się, usługa Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

## <a name="security-authentication-and-access-control"></a>Zabezpieczenia, uwierzytelnianie i kontrola dostępu
* <a id="ad-support"></a>
**Jest uwierzytelnianie oparte na usłudze Active Directory i access control, obsługiwane przez usługi Azure Files?**  
    Usługa Azure Files oferuje zarządzanie kontrolą dostępu na dwa sposoby:

    - Sygnatury dostępu współdzielonego (SAS) służy do generowania tokenów, które mają określone uprawnienia i które są ważne przez ustalony czas. Na przykład można wygenerować token z dostępem tylko do odczytu do określonego pliku, który ma wygaśnięcia 10 minut. Każdy, kto posiada token, gdy token jest prawidłowy ma dostęp tylko do odczytu do tego pliku, 10 minut. Obecnie kluczy sygnatury dostępu współdzielonego są obsługiwane tylko za pomocą interfejsu API REST lub bibliotek klienckich. Udziału plików platformy Azure należy zainstalować za pośrednictwem protokołu SMB, używając kluczy konta magazynu.

    - Usługa Azure File Sync zachowuje i replikuje wszystkie DACL lub poufnych list kontroli dostępu, (czy opartej na usłudze Active Directory lub lokalnej) do wszystkich punktów końcowych serwera, które synchronizowanych. Ponieważ systemu Windows Server mogą już uwierzytelniać za pomocą usługi Active Directory, usługi Azure File Sync jest korzystne rozwiązanie uzupełniające aż pełną obsługę dla uwierzytelniania opartego na usłudze Active Directory, a premierą obsługuje listy kontroli dostępu.

    Obecnie usługa Azure Files nie obsługuje bezpośrednio usługi Active Directory.

* <a id="encryption-at-rest"></a>
**Jak zapewnić, że moje udziału plików platformy Azure są szyfrowane, gdy?**  
    Szyfrowanie usługi Azure Storage jest w trakcie jest domyślnie włączona, we wszystkich regionach. W tych regionach nie trzeba podjąć działania, aby włączyć szyfrowanie. Inne regiony, zobacz [szyfrowanie po stronie serwera](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Jak uzyskiwanie dostępu do określonego pliku przy użyciu przeglądarki sieci web?**  
    Sygnatury dostępu współdzielonego służy do generowania tokenów, które mają określone uprawnienia i które są ważne przez ustalony czas. Na przykład można wygenerować token, który zapewnia dostęp tylko do odczytu do określonego pliku na pewien okres czasu. Każdy posiadacz adresu URL dostępu do pliku bezpośrednio z dowolnej przeglądarki sieci web, ale token jest ważny. Z poziomu interfejsu użytkownika, takich jak Eksplorator usługi Storage można łatwo generować klucza sygnatury dostępu współdzielonego.

* <a id="file-level-permissions"></a>
**Czy można określić tylko do odczytu lub uprawnienia tylko do zapisu w folderach w ramach udziału?**  
    Jeśli zainstalujesz udział plików przy użyciu protokołu SMB nie masz folderu poziom kontroli nad uprawnieniami. Jednak jeśli tworzysz sygnatury dostępu współdzielonego przy użyciu interfejsu API REST lub bibliotek klienckich, można określić uprawnienia tylko do odczytu lub tylko do zapisu w folderach w ramach danego udziału.

* <a id="ip-restrictions"></a>
**Czy można zaimplementować ograniczenia adresów IP dla udziału plików platformy Azure?**  
    Tak. Na poziomie konta magazynu można ograniczyć dostęp do udziału plików platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie usługi Azure Storage zapory i sieci wirtualne](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Jakie zasady zgodności danych obsługuje usługa Azure Files?**  
   Usługa pliki systemu Azure działa w oparciu o takiej samej architekturze magazynu, która jest używana w innych usługach magazynu w usłudze Azure Storage. Usługa pliki Azure mają zastosowanie te same zasady zgodności danych, które są używane w innych usługach magazynu platformy Azure. Aby uzyskać więcej informacji na temat zgodności danych usługi Azure Storage mogą odwoływać się do [ofert zgodności usługi Azure Storage](https://docs.microsoft.com/en-us/azure/storage/common/storage-compliance-offerings), a następnie przejdź do [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx).

## <a name="on-premises-access"></a>Dostęp lokalny
* <a id="expressroute-not-required"></a>
**Należy ponownie nawiązać połączenie z usługą Azure Files przy użyciu usługi Azure ExpressRoute lub używania usługi Azure File Sync w środowisku lokalnym?**  
    Nie. Usługa ExpressRoute nie jest wymagane do dostępu do udziału plików platformy Azure. W przypadku instalacji plików platformy Azure będzie udostępniać bezpośrednio w środowisku lokalnym, wszystkie, które są wymagane jest posiadanie port 445 (ruch wychodzący protokołu TCP) otwórz dostęp do Internetu (jest to port, który korzysta z protokołu SMB do komunikacji). Jeśli używasz usługi Azure File Sync, wszystkie opcje, które są wymagane jest port 443 (ruch wychodzący protokołu TCP) dla dostępu protokołu HTTPS (nie SMB wymagane). Jednak możesz *można* korzystać z usługi ExpressRoute za pomocą jednej z tych opcji dostępu.

* <a id="mount-locally"></a>
**Jak zainstalować udział plików platformy Azure na maszynie lokalnej?**  
    Możesz zainstalować udział plików przy użyciu protokołu SMB, jeśli jest otwarty port 445 (ruch wychodzący protokołu TCP), a Twój klient obsługuje protokół SMB 3.0 (na przykład, jeśli używasz systemu Windows 10 lub Windows Server 2016). Jeśli port 445 jest zablokowany przez zasady w organizacji lub przez usługodawcę internetowego, można użyć usługi Azure File Sync na dostęp do udziału plików platformy Azure.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Jak tworzenie kopii zapasowej pliku Azure udostępnić?**  
    Możesz użyć okresowe [udostępniać migawki](storage-snapshots-files.md) ochrony przed przypadkowymi usunięciami. Również służy narzędzie AzCopy, Robocopy lub narzędzie kopii zapasowej innych firm, które można utworzyć kopię zapasową udziału zainstalowanego pliku. Usługa Azure Backup oferuje kopii zapasowej plików platformy Azure. Dowiedz się więcej o [udziałów przez usługę Azure Backup plików — wykonywanie kopii zapasowych Azure](https://docs.microsoft.com/en-us/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Migawki udziału
### <a name="share-snapshots-general"></a>Udostępnianie migawki: Ogólne
* <a id="what-are-snaphots"></a>
**Co to są migawki udziału plików?**  
    Migawki udziału plików platformy Azure służy do tworzenia udziałów plików w wersji tylko do odczytu. Możesz również użyć usługi Azure Files można skopiować starszą wersję kopii zawartości do tego samego udziału w alternatywnej lokalizacji na platformie Azure lub w środowisku lokalnym więcej zmian. Aby dowiedzieć się więcej na temat migawek udziałów, zobacz [udostępniać migawki — omówienie](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Gdzie są przechowywane Moje migawek udziałów**  
    Migawki udziałów są przechowywane na tym samym koncie magazynu, udziału plików.

* <a id="snapshot-perf-impact"></a>
**Czy istnieją dowolnego wpływ na wydajność dla przy użyciu migawek udziału?**  
    Migawki udziału nie ma żadnych zmniejszenie wydajności.

* <a id="snapshot-consistency"></a>
**Migawki udziałów są spójne z aplikacjami?**  
    Nie, migawki udziałów są spójne z aplikacjami. Użytkownik musi opróżnienie zapisu z aplikacji do udziału przed wykonaniem migawki udziału.

* <a id="snapshot-limits"></a>
**Czy istnieją ograniczenia liczby migawek udziałów, których można używać?**  
    Tak. Usługa Azure Files można zachować maksymalnie migawek udziału 200. Migawek udziałów uwzględniane przydziału udziału, więc ma żadnego limitu poszczególnych udziałów całkowita ilość miejsca, który jest używany przez wszystkie migawki udziału. Nadal mają zastosowanie limity konta magazynu. Po migawek udziałów 200 możesz usunąć starsze migawki do utworzenia nowych migawek udziałów.
* <a id="snapshot-cost"></a>
**Jaka udostępnianie koszt migawki?**  
    Standardowa transakcji i koszt magazynu w warstwie standardowa będą dotyczyć migawki. Migawki są przyrostowe charakter. Podstawowy migawka jest sam udział. Wszystkie kolejne migawki są dodawane i zapisze tylko różnice z poprzednią migawkę. Oznacza to, że zmiany różnicowe, które będą widoczne na rachunku będzie minimalny, jeśli jest minimalny, postęp dokonany w obciążeniu. Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/storage/files/) dla standardowe informacje o cenach usługi Azure Files. Obecnie jest sposobem Obejrzyj rozmiar wykorzystywany przez migawkę udziału, porównując rozliczane pojemnością przy użyciu używana pojemność. Pracujemy nad narzędzi w celu raportowania.


### <a name="create-share-snapshots"></a>Tworzenie migawek udziałów
* <a id="file-snaphsots"></a>
**Można utworzyć migawki udziału plików poszczególnych?**  
    Migawki udziałów są tworzone na poziomie udziału plików. Możesz przywrócić pojedyncze pliki z migawki udziału plików, ale nie można utworzyć migawki udziału na poziomie plików. Jednak jeśli chcesz wyświetlić listę migawek udziału, w których uległa zmianie określonego pliku zostały wykonane wszystkie migawki udziału na poziomie udziału, możesz to zrobić w obszarze **poprzednie wersje** udziale zainstalowany Windows. 
    
    Daj nam znać, jeśli potrzebujesz funkcji migawkę plików, na [UserVoice plików Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>
**Można utworzyć migawki udziału plików zaszyfrowanych udziałów?**  
    Można utworzyć migawkę udziału udziałów plików platformy Azure, w których szyfrowanie w spoczynku włączone. Możesz przywrócić pliki z migawki udziału, do udziału plików zaszyfrowanych. Jeśli swój udział jest zaszyfrowany, migawek udziału również są szyfrowane.

* <a id="geo-redundant-snaphsots"></a>
**Czy Moje migawek udziałów magazynu geograficznie nadmiarowego?**  
    Migawki udziału ma ten sam nadmiarowości jako udział plików platformy Azure, dla której zostały pobrane. Jeśli wybrano magazyn geograficznie nadmiarowy dla swojego konta, również migawki udziału są przechowywane nadmiarowo w sparowanym regionie.

### <a name="manage-share-snapshots"></a>Zarządzanie migawkami udziału
* <a id="browse-snapshots-linux"></a>
**Czy można przeglądać Moje migawek udziałów z systemem Linux?**  
    Interfejs wiersza polecenia platformy Azure w wersji 2.0 umożliwia tworzenie, listy, przeglądanie i przywracaj migawek udziału w systemie Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Czy można skopiować migawki udziału, do innego konta magazynu?**  
    Można skopiować pliki z migawki udziału, do innej lokalizacji, ale nie można skopiować migawki udziału, samodzielnie.

### <a name="restore-data-from-share-snapshots"></a>Przywracanie danych z migawki udziału
* <a id="promote-share-snapshot"></a>
**Czy można promować migawkę udziału do podstawowego udziału?**  
    Możesz skopiować dane z migawki udziału, do innego miejsca docelowego. Nie można podwyższyć poziomu migawki udziału do podstawowego udziału.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Czy można przywrócić danych z mojej migawkę udziału do innego konta magazynu?**  
    Tak. Pliki z migawki udziału można skopiować do lokalizacji oryginalnej lub alternatywnej lokalizacji, która obejmuje tego samego konta magazynu lub innego konta magazynu, w tym samym regionie lub w różnych regionach. Możesz także skopiować pliki do lokalizacji lokalnej lub inne chmury.    
  
### <a name="clean-up-share-snapshots"></a>Czyszczenie migawek udziałów
* <a id="delete-share-keep-snapshots"></a>
**Można I Usuń Moje udział, ale nie usunąć Moje migawek udziału?**  
    Jeśli masz migawki aktywnego udziału w udziale, nie można usunąć udziału. Można użyć interfejsu API, można usunąć migawek udziału, wraz z udziału. Można również usunąć migawek udziału i Udostępnij w witrynie Azure portal.

* <a id="delete-share-with-snapshots"></a>
**Co stanie się Moje migawki udziału, jeśli usunę Mój konta magazynu?**  
    Jeśli usuniesz swoje konto magazynu, migawek udziałów również zostaną usunięte.

## <a name="billing-and-pricing"></a>Rozliczeniach i cenach
* <a id="vm-file-share-network-traffic"></a>
**Czy ruch sieciowy między Maszyną wirtualną platformy Azure i liczbą udziału plików platformy Azure jako zewnętrzne użycie przepustowości jest obciążany opłatą za subskrypcję?**  
    Udziału plików i maszyn wirtualnych znajdują się w tym samym regionie platformy Azure, czy brak dodatkowych opłat za ruch między udziału plików i maszyny Wirtualnej. Jeśli udział plików i maszyna wirtualna znajdują się w różnych regionach, ruch między nimi są rozliczane jako zewnętrzne użycie przepustowości.

* <a id="share-snapshot-price"></a>
**Ile korzystają z koszt migawki?**  
     W trakcie okresu zapoznawczego nie ma opłat za pojemność migawki udziału. Koszty danych wychodzących i transakcji magazynu w warstwie standardowa mają zastosowanie. Po ogłoszeniu ogólnej dostępności subskrypcji spowoduje obciążenie opłatami pojemności i transakcji na migawek udziałów.
     
     Migawki udziałów są przyrostowe charakter. Migawkę udziału podstawowy jest sam udział. Wszystkie migawki udziału kolejnych są dodawane i przechowywane tylko różnica w porównaniu z poprzednim migawki udziału. Opłaty są naliczane tylko w przypadku zmiany zawartości. Jeśli udostępniasz 100 GiB danych, ale tylko 5 GiB uległy zmianie od czasu Twojego ostatniego migawki udziału, tylko 5 dodatkowych GiB korzysta z migawki udziału, a opłaty są naliczane za 105 GiB. Aby uzyskać więcej informacji na temat transakcji i opłaty za ruch wychodzący standardowego, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Skalowanie i wydajność
* <a id="files-scale-limits"></a>
**Jakie są limity skalowania usługi Azure Files?**  
    Aby uzyskać informacje na temat cele dotyczące skalowalności i wydajności dla usługi Azure Files, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Files](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Potrzebuję udział plików większych niż usługi pliki Azure oferuje obecnie. Można zwiększyć rozmiar Moje udziału plików platformy Azure?**  
    Nie. Maksymalny rozmiar udziału plików platformy Azure wynosi 5 TiB. Obecnie jest to stały limit, które firma Microsoft nie można dostosować. Pracujemy nad rozwiązaniem, aby zwiększyć rozmiar udziału na 100 TiB, ale nie mamy osi czasu, aby udostępnić w tej chwili.

* <a id="open-handles-quota"></a>
**Ilu klientów mogą uzyskać dostęp do tego samego pliku jednocześnie?**   
    Obowiązuje przydział 2000 otwartymi dojściami w jednym pliku. W przypadku 2 000 otwarte dojścia jest wyświetlany komunikat o błędzie stwierdzający, że osiągnięto limit przydziału.

* <a id="zip-slow-performance"></a>
**Moja wydajność jest niska, gdy I Rozpakowywanie plików w usłudze Azure Files. Co zrobić?**  
    Aby przetransferować dużą liczbę plików do usługi Azure Files, firma Microsoft zaleca korzystanie z narzędzia AzCopy (Windows; w wersji zapoznawczej dla systemu Linux i UNIX) lub programu Azure PowerShell. Te narzędzia są zoptymalizowane pod kątem transferu sieciowego.

* <a id="slow-perf-windows-81-2012r2"></a>
**Dlaczego jest Moja wydajność wolne, po Moje udziału plików platformy Azure I instalowanie w systemie Windows Server 2012 R2 lub Windows 8.1?**  
    Istnieje znany problem w przypadku instalowania udziału plików platformy Azure w systemie Windows Server 2012 R2 i Windows 8.1. Problem został poprawić w aktualizacji zbiorczej kwietnia 2014 r. dla Windows 8.1 i Windows Server 2012 R2. Aby uzyskać optymalną wydajność upewnij się, że wszystkie wystąpienia systemu Windows Server 2012 R2 i Windows 8.1 tej poprawki zastosowane. (Zawsze otrzymasz poprawki Windows za pośrednictwem usługi Windows Update). Aby uzyskać więcej informacji, zobacz skojarzonego artykułu bazy wiedzy Microsoft Knowledge Base [obniżyć wydajność, gdy uzyskujesz dostęp do usługi Azure Files z Server 2012 R2 lub Windows 8.1 z](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funkcje i współdziałanie z innymi usługami
* <a id="cluster-witness"></a>
**Czy mogę używać mojej udziału plików platformy Azure jako *monitora udostępniania plików* dla mojego klastra pracy awaryjnej systemu Windows Server?**  
    Ta konfiguracja nie jest obecnie obsługiwana dla udziału plików platformy Azure. Aby uzyskać więcej informacji na temat ustawiania to dla usługi Azure Blob storage, zobacz [wdrażanie monitora chmury dla klastra trybu Failover](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**W wystąpieniu kontenera platformy Azure mogą zainstalować udział plików platformy Azure?**  
    Tak, udziałów plików platformy Azure są dobrym rozwiązaniem, gdy użytkownik chce utrwalania informacji poza okres istnienia wystąpienia kontenera. Aby uzyskać więcej informacji, zobacz [zainstalować udział plików platformy Azure przy użyciu usługi Azure Container instances](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Jest to operacja zmiany nazwy w interfejsie API REST?**  
    Nie w tej chwili.

* <a id="nested-shares"></a>
**Można skonfigurować zagnieżdżanie? Innymi słowy udział w udziale?**  
    Nie. Udział plików *jest* sterownik wirtualny z możliwością instalacji, dlatego zagnieżdżanie nie są obsługiwane.

* <a id="ibm-mq"></a>
**Jak używać usługi Azure Files z programem IBM MQ?**  
    Firma IBM wydała dokument, który pomaga klientom IBM MQ Konfigurowanie usługi Azure Files przy użyciu usługi firmy IBM. Aby uzyskać więcej informacji, zobacz [jak skonfigurować Menedżera kolejki obejmujące wiele wystąpień IBM MQ przy użyciu usługi Microsoft Azure Files](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Zobacz także
* [Rozwiązywanie problemów z usługi Azure Files w Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Rozwiązywanie problemów z usługi Azure Files w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Rozwiązywanie problemów z usługi Azure File Sync](storage-sync-files-troubleshoot.md)
