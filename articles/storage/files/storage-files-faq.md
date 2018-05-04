---
title: Często zadawane pytania dotyczące usługi pliki Azure | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące usługi pliki Azure.
services: storage
documentationcenter: ''
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/04/2017
ms.author: renash
ms.openlocfilehash: ef8b5b30edaef61eca1be0cf80c5defd09c4dac2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="frequently-asked-questions-about-azure-files"></a>Często zadawane pytania dotyczące usługi pliki Azure
[Usługa pliki Azure](storage-files-introduction.md) oferuje pełni zarządzanych udziałów plików w chmurze, które są dostępne za pośrednictwem standardu branżowego [protokołu bloku komunikatów serwera (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (znanej także jako Common Internet File System, lub CIFS). Udziały plików platformy Azure można zainstalować w chmurze lub lokalnie wdrożeń systemu Windows, Linux i macOS jednocześnie. Możesz również buforować udziały plików platformy Azure na komputerach z systemem Windows Server przy użyciu synchronizacji plików Azure (wersja zapoznawcza) zapewniania szybkiego dostępu bliski gdzie dane są używane.

Ten artykuł zawiera odpowiedzi na często zadawane pytania na temat funkcji usługi pliki Azure, łącznie z użyciem synchronizacji plików Azure przy użyciu plików Azure. Jeśli nie widzisz odpowiedź na swoje pytanie, użytkownik może skontaktuj się z nami za pośrednictwem następujących kanałów (w kolejności rosnące):

1. W sekcji komentarzy w tym artykule.
2. [Forum usługi Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Azure pliki UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Pomoc techniczna firmy Microsoft. Aby utworzyć nowe żądanie pomocy technicznej, w portalu Azure na **pomocy** wybierz opcję **Pomoc i obsługa techniczna** przycisk, a następnie wybierz **nowy obsługuje żądania**.

## <a name="general"></a>Ogólne
* <a id="why-files-useful"></a>
**Jak pliki Azure jest przydatne?**  
   Pliki Azure służy do tworzenia udziałów plików w chmurze, bez konieczności odpowiedzialny za zarządzanie obciążenie serwera fizycznego, urządzenie lub urządzenia. Jak monotonii pracy dla Ciebie, w tym stosować aktualizacje systemu operacyjnego i wymiany uszkodzonych dysków. Aby dowiedzieć się więcej o scenariuszach, w których pliki Azure może Ci pomóc, zobacz [dlaczego usługi pliki Azure jest przydatne](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
**Jakie są różne sposoby na dostęp do plików w plikach Azure?**  
    Przy użyciu protokołu SMB 3.0 można zainstalować udział plików na komputerze lokalnym lub za pomocą narzędzi takich jak [Eksploratora usługi Storage](http://storageexplorer.com/) na dostęp do plików w udziale plików. Z aplikacji można użyć bibliotek klienckich magazynu, interfejsy API REST, programu PowerShell lub interfejsu wiersza polecenia Azure do dostępu do plików w udziale plików na platformę Azure.

* <a id="what-is-afs"></a>
**Co to jest synchronizacja plików Azure?**  
    Synchronizacji plików Azure umożliwia scentralizowanie udziałów plików w organizacji w plikach Azure, przy zachowaniu elastyczności, wydajności i zgodności serwera plików lokalnych. Synchronizacja programu Azure pliku przekształca maszynach w systemie Windows Server do szybkiego pamięci podręcznej udziału plików na platformę Azure. Można użyć każdego protokołu, który jest dostępny w systemie Windows Server dostępu do danych lokalnie, w tym protokołu SMB, sieciowego systemu plików (NFS) i usługi protokołu transferu plików (FTPS). Może mieć dowolną liczbę pamięci podręcznych zgodnie z potrzebami na całym świecie.

* <a id="files-versus-blobs"></a>
**Dlaczego użyje udział plików Azure w porównaniu z magazynu obiektów Blob platformy Azure dla danych**  
    Usługa pliki Azure i obiektów Blob platformy Azure magazynu dostępne metody przechowywania dużych ilości danych w chmurze, ale są przydatne do nieco innych celów. 
    
    Magazyn obiektów Blob Azure jest przydatne w przypadku bardzo dużej skali, chmury natywnych aplikacji, które muszą do przechowywania danych bez struktury. Aby zmaksymalizować wydajność i skalę, magazynu obiektów Blob platformy Azure jest prostsze abstrakcji magazynu niż system plików wartość true. Tylko za pośrednictwem bibliotek klienta opartego na interfejsie REST (lub bezpośrednio za pomocą protokołu opartego na interfejsie REST) można uzyskać dostępu do magazynu obiektów Blob platformy Azure.

    Usługa pliki Azure jest specjalnie systemu plików. Usługa pliki Azure ma wszystkie streszczenia plików, które możesz znają i lubią wieloletnim doświadczeniu Praca z lokalnymi systemami operacyjnymi. Przykład do magazynu obiektów Blob platformy Azure plików Azure oferuje interfejsu REST i biblioteki klienta opartego na interfejsie REST. W przeciwieństwie do magazynu obiektów Blob platformy Azure plików Azure oferuje SMB dostęp do udziałów plików na platformę Azure. Przy użyciu protokołu SMB, można zainstalować udział plików Azure bezpośrednio na systemu Windows, Linux lub system macOS — lokalnie lub w chmurze maszyn wirtualnych, bez pisania żadnego kodu lub dołączania wszelkie specjalne sterowniki systemu plików. Możesz również buforować udziały plików platformy Azure na lokalnych serwerach plików przy użyciu synchronizacji plików Azure szybki dostęp bliski gdzie dane są używane. 
   
    Więcej informacji na temat opis na temat różnic między plikami Azure i magazynu obiektów Blob platformy Azure, zobacz [decydowania, kiedy należy używać magazynu obiektów Blob platformy Azure, Azure plików lub dysków Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Aby dowiedzieć się więcej na temat magazynu obiektów Blob platformy Azure, zobacz [wprowadzenie do magazynu obiektów Blob](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Dlaczego użyje udziału plików na platformę Azure zamiast dysków Azure**  
    Dysku w przypadku dysków Azure to po prostu dysk. Dysk autonomiczny samodzielnie nie jest zbyt przydatne. Można pobrać wartości z dysków Azure, można dołączyć dysku do maszyny wirtualnej, która działa na platformie Azure. Dyski Azure mogą być używane dla wszystkich, który ma zostać użyty dysku na serwerze lokalnym. Służy on jako dysku systemu operacyjnego, obszaru wymiany w systemie operacyjnym lub jako dedykowanych dla magazynu dla aplikacji. Interesujące dla dysków Azure polega na utworzeniu serwera plików w chmurze do użycia w tym samym miejsc, w której może używać udziału plików na platformę Azure. Wdrażanie serwera plików w maszynach wirtualnych platformy Azure jest wydajnym sposobem uzyskania magazyn plików na platformie Azure, potrzebują opcje wdrażania, które aktualnie nie są obsługiwane przez usługi pliki Azure (takie jak NFS protokołu pomocy technicznej lub premium magazynu). 

    Jednak uruchomiona na serwerze plików z dyskami Azure jako magazynu zaplecza zwykle jest znacznie droższe niż przy użyciu udziału plików na platformę Azure kilka przyczyn. Po pierwsze oprócz płatności dla magazynu danych na dysku, również musi płacisz za wydatków uruchomionych maszyn wirtualnych platformy Azure. Po drugie można również zarządzać maszyn wirtualnych, które są używane do uruchamiania serwera plików. Na przykład ponosisz odpowiedzialność za uaktualnień systemu operacyjnego. Ponadto jeśli potrzebujesz ostatecznie dane do lokalnej pamięci podręcznej, to do konfigurowania i zarządzania nią technologii replikacji, takich jak rozproszonych replikacji systemu plików (DFSR), aby to zrobić.

    Jeden ze sposobów uzyskiwania najlepiej plików Azure oraz serwer plików, który znajduje się w maszynach wirtualnych platformy Azure (oprócz za pomocą dysków Azure jako magazynu zaplecza) jest instalowanie synchronizacji plików Azure na serwerze plików, który znajduje się w chmurze maszyny Wirtualnej. Jeśli udział plików na platformę Azure znajduje się w tym samym regionie co serwer plików, można włączyć w chmurze warstwy i ustaw dany wolumin procent wolnego miejsca na maksymalną (99%). Dzięki temu minimalnego zduplikowanie danych. Można też użyć wszystkie aplikacje, które mają z serwerów plików, takich jak aplikacje, które wymagają protokołu NFS obsługuje.

    Aby uzyskać informacje na temat opcji konfiguracji serwera plików o wysokiej wydajności i dostępności na platformie Azure, zobacz [wdrażanie maszyn wirtualnych IaaS gościa klastrów w systemie Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Więcej informacji na temat opis różnic między plikami Azure i dysków Azure, zobacz [decydowania, kiedy należy używać magazynu obiektów Blob platformy Azure, Azure plików lub dysków Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Aby dowiedzieć się więcej na temat dysków Azure, zobacz [omówienie dysków zarządzanych Azure](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
**Jak rozpocząć pracę przy użyciu plików Azure?**  
   Wprowadzenie do usługi pliki Azure jest bardzo proste. Najpierw [utworzyć udział plików](storage-how-to-create-file-share.md), a następnie zainstalowanie systemu operacyjnego preferowanych: 

    * [Zainstaluj w systemie Windows](storage-how-to-use-files-windows.md)
    * [Zainstaluj w systemie Linux](storage-how-to-use-files-linux.md)
    * [Zainstaluj w macOS](storage-how-to-use-files-mac.md)

   Aby uzyskać więcej informacji na temat Przewodnik o wdrażaniu udziału plików na platformę Azure zastąpić produkcji udziałów plików w organizacji, zobacz [planowania wdrożenia usługi pliki Azure](storage-files-planning.md).

* <a id="redundancy-options"></a>
**Jakie opcje nadmiarowość magazynu są obsługiwane przez usługi pliki Azure?**  
    Obecnie plików Azure obsługuje magazyn lokalnie nadmiarowy (LRS), strefy magazyn geograficznie nadmiarowy (ZRS) i magazynu geograficznie nadmiarowego (GRS). Firma Microsoft planuje obsługiwać magazynu geograficznie nadmiarowego (RA-GRS) dostęp do odczytu w przyszłości, ale osi czasu, aby udostępnić w tej chwili nie mamy.

* <a id="tier-options"></a>
**Jakie warstwy magazynowania są obsługiwane w plikach Azure?**  
    Obecnie plików Azure obsługuje tylko warstwy magazynu w warstwie standardowa. Nie mamy osi czasu, aby udostępnić magazyn w warstwie premium i magazynu chłodnego obsługuje w tej chwili. 
    
    > [!NOTE]
    > Nie można utworzyć udziały plików platformy Azure, z konta magazynu tylko do obiektów blob lub konta premium magazynu.

* <a id="give-us-feedback"></a>
**Czy na pewno chcesz Zobacz określoną funkcję dodane do usługi pliki Azure. Można dodać, go?**  
    Zespół usługi pliki Azure jest zainteresowana wysłuchaniu wszystkie opinie, które masz o naszej usługi. Głosowania żądania funkcji [UserVoice plików Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Firma Microsoft jest czekamy na delighting możesz z wielu nowych funkcji.

## <a name="azure-file-sync"></a>Usługa Azure File Sync

* <a id="afs-region-availability"></a>
**Jakie regiony są obsługiwane w przypadku synchronizacji plików Azure (wersja zapoznawcza)?**  
    Obecnie synchronizacji plików Azure jest dostępna w Australia Wschodnia Kanada centralnej, wschodnie stany USA, Azja południowo-wschodnia, Wielka Brytania Południowa, Europa Zachodnia, zachodnie stany USA. Obsługa kolejnych regionach zostanie dodany jako pracujemy kierunku ogólnej dostępności. Aby uzyskać więcej informacji, zobacz [dostępność w danym regionie](storage-sync-files-planning.md#region-availability).

* <a id="cross-domain-sync"></a>
**Czy można mieć serwery przyłączone do domeny i przyłączone do domeny w tej samej grupie synchronizacji?**  
    Tak. Grupa synchronizacji może zawierać punkty końcowe serwera, które mają różne członkostwa w usłudze Active Directory, nawet jeśli nie są przyłączone do domeny. Chociaż ta konfiguracja działa pod względem technicznym, zaleca się to jako typowej konfiguracji ponieważ list kontroli dostępu (ACL), które są zdefiniowane dla plików i folderów na jednym serwerze może nie móc zostać wymuszone przez inne serwery w grupie synchronizacji. Aby uzyskać najlepsze wyniki zaleca się synchronizację między serwerami, które znajdują się w tym samym lesie usługi Active Directory, między serwerami, które znajdują się w różnych lasach usługi Active Directory, ale które zostały ustalone relacje zaufania lub między serwerami, które nie znajdują się w domenie. Zaleca się unikać przy użyciu kombinacji tych konfiguracji.

* <a id="afs-change-detection"></a>
**Utworzono plik bezpośrednio w mojej udziału plików na platformę Azure przy użyciu protokołu SMB lub w portalu. Jak długo trwa pliku do synchronizacji serwerów w grupie synchronizacji?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Jeśli tego samego pliku została zmieniona na dwóch serwerach w tym samym czasie, co się stanie?**  
    Synchronizacja programu Azure pliku używa strategii proste rozwiązywania konfliktów: Firma Microsoft zachować zarówno zmiany do plików, które są zmieniane na dwóch serwerach w tym samym czasie. Zmiana niedawno napisanych zachowuje oryginalna nazwa pliku. Starszy plik ma maszyny "source" i liczby konfliktów dołączonym do nazwy. Wynika to taksonomii: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\< numer wewnętrzny\>  

    Na przykład pierwszy konfliktu CompanyReport.docx może stać się CompanyReport CentralServer.docx CentralServer którym wystąpił starsze zapisu. Drugi konflikt będą miały postać CompanyReport-CentralServer-1.docx.

* <a id="afs-storage-redundancy"></a>
**Magazyn geograficznie nadmiarowy jest obsługiwana dla synchronizacji plików Azure?**  
    Tak, pliki Azure obsługuje magazyn lokalnie nadmiarowy (LRS) i magazynu geograficznie nadmiarowego (GRS). Jeśli do GRS między regionami sparowanego pracy awaryjnej, zaleca się nowy region można traktować jako kopia zapasowa tylko danych. Synchronizacja programu Azure pliku nie automatycznie rozpoczyna synchronizację z nowego regionu podstawowego. 

* <a id="sizeondisk-versus-size"></a>
**Dlaczego nie *rozmiaru na dysku* właściwości przeznaczony do porównywania plików *rozmiar* właściwości po użyciu synchronizacji plików Azure?**  
    Odszyfrowywanie udostępnia dwie właściwości do reprezentowania rozmiar pliku: **rozmiar** i **rozmiaru na dysku**. Te właściwości różnią się w niewielkim stopniu w znaczenie. **Rozmiar** reprezentuje pełną rozmiar pliku. **Rozmiar dysku** reprezentuje rozmiar strumienia pliku, który jest przechowywany na dysku. Wartości tych właściwości można różnią się z różnych powodów, takich jak kompresja, korzystanie z funkcji deduplikacji danych lub Obsługa poziomów z synchronizacji plików Azure w chmurze. Jeśli plik jest warstwowa do udziału plików na platformę Azure, rozmiaru na dysku wynosi zero, ponieważ strumień pliku jest przechowywana w udziale plików na platformę Azure, a nie na dysku. Istnieje również możliwość dla pliku to częściowo warstwowych (lub częściowo odwołane). W pliku częściowo warstwowych części pliku znajduje się na dysku. Taka sytuacja może wystąpić, częściowo są odczytywane przez aplikacje, takie jak odtwarzacze multimedialne lub zip narzędzia plików. 

* <a id="is-my-file-tiered"></a>
**Jak sprawdzić, czy plik został warstwowej?**  
    Istnieje kilka sposobów, aby sprawdzić, czy plik został warstwy do udziału plików na platformę Azure:
    
   *  **Sprawdź atrybutów pliku.**
     Aby to zrobić, kliknij prawym przyciskiem myszy plik, przejdź do **szczegóły**, a następnie przewiń w dół do **atrybuty** właściwości. Warstwowych plik ma ustawiony atrybut następujące:     
        
        | Atrybut list | Atrybut | Definicja |
        |:----------------:|-----------|------------|
        | A | Archiwum | Wskazuje, że plik kopii zapasowych za pomocą oprogramowania kopii zapasowej. Ten atrybut jest zawsze ustawiony, niezależnie od tego, czy plik jest warstwowej lub pełni przechowywane na dysku. |
        | P | Plik rozrzedzony | Wskazuje, że plik jest plik rozrzedzony. Plik rozrzedzony jest specjalistyczną odmianą pliku, który oferuje NTFS na efektywne wykorzystanie, gdy plik na dysku strumienia przede wszystkim jest pusta. Synchronizacja programu Azure pliku używa plików rozrzedzonych, ponieważ warstwowej w pełni lub częściowo przypomnieć pliku. W pełni warstwowych pliku strumienia pliku są przechowywane w chmurze. W pliku częściowo odwołane, że plik znajduje się już na dysku. Jeśli plik jest w pełni przypomnieć na dysku, synchronizacji plików Azure konwertuje go z pliku rozrzedzonego zwykły plik. |
        | L | Punkt ponownej analizy | Wskazuje, że plik ma punkt ponownej analizy. Punkt ponownej analizy to specjalne wskaźnika do użycia przez filtru systemu plików. Synchronizacja programu Azure pliku używa punktów ponownej analizy w celu zdefiniowania do synchronizacji plików Azure filtru systemu plików (StorageSync.sys) chmurze lokalizację przechowywania pliku. W ten sposób realizowany bezproblemowy dostęp. Użytkownicy nie muszą znać synchronizacji plików Azure jest używana lub jak uzyskać dostęp do plików w udziale plików na platformę Azure. Gdy pełni przypomina plik, synchronizacji plików Azure usuwa punktu ponownej analizy z pliku. |
        | O | Offline | Wskazuje, że niektóre lub wszystkie zawartość pliku nie są zapisywane na dysku. Pełni przypomina plik, synchronizacji plików Azure usuwa tego atrybutu. |

        ![Okno dialogowe właściwości pliku, z wybraną kartą szczegóły](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Widać atrybuty dla wszystkich plików w folderze, dodając **atrybuty** pola do wyświetlenia tabeli Eksploratora plików. Aby to zrobić, kliknij prawym przyciskiem myszy istniejącą kolumnę (na przykład **rozmiar**), wybierz pozycję **więcej**, a następnie wybierz **atrybuty** z listy rozwijanej.
        
   * **Użyj `fsutil` do sprawdzenia punkty ponownej analizy w pliku.**
       Zgodnie z opisem w poprzedniej opcji, warstwowy plik ma zawsze punktu zestaw ponownej analizy. Wskaźnik ponownej analizy to specjalne wskaźnik synchronizacji plików Azure filtru systemu plików (StorageSync.sys). Aby sprawdzić, czy plik zawiera punktu ponownej analizy, w oknie wiersza polecenia lub programu PowerShell z podwyższonym poziomem uprawnień, uruchom `fsutil` narzędzie:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Jeśli plik zawiera punktu ponownej analizy, można było się spodziewać **ponownej analizy, wartość tagu: 0x8000001e**. Ta wartość szesnastkową jest wartość punktu ponownej analizy, należącej do synchronizacji plików Azure. Dane wyjściowe zawierają również dane ponownej analizy, który reprezentuje ścieżkę do pliku w udziale plików na platformę Azure.

        > [!WARNING]  
        > `fsutil reparsepoint` Narzędzie polecenie ma również możliwości usunięcia punktów ponownej analizy. Nie wykonuj tego polecenia, chyba że zespołu inżynieryjnego synchronizacji plików Azure monituje o. Uruchomienie tego polecenia może spowodować utratę danych. 

* <a id="afs-recall-file"></a>**Plik, który ma być użyty został warstwy. Jak można odwołać pliku na dysku, aby użyć go lokalnie?**  
    Najprostszym sposobem odwołanie pliku na dysku jest można otworzyć pliku. Filtr systemu plików (StorageSync.sys) synchronizacji plików Azure bezproblemowo pobiera plik z udziału plików platformy Azure bez konieczności wykonywania działań ze strony użytkownika. Dla typów plików, które mogą być częściowo odczytu, takich jak pliki multimedialne lub zip, otwieranie pliku nie jest pobierany cały plik.

    Za pomocą programu PowerShell można również wymusić plik można wycofać. Ta opcja może być przydatna, jeśli chcesz odwołać wielu plików jednocześnie, takich jak wszystkie pliki w folderze. Otwórz sesję programu PowerShell do węzła serwera zainstalowanym synchronizacji plików Azure, a następnie uruchom następujące polecenia programu PowerShell:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>
**Jak wymusić plik lub katalog do należeć do warstwy**  
    Po włączeniu funkcji warstw chmury warstwy automatycznie pliki warstwy chmury oparte na ostatniego dostępu i zmodyfikować razy, aby osiągnąć procent wolnego miejsca na wolumin określony dla punktu końcowego w chmurze. Czasami jednak można ręcznie wymusić plik do warstwy. Może to być przydatne, jeśli zapiszesz dużych plików, które nie będą ponownie użyć przez długi czas, i chcesz ilość wolnego miejsca na woluminie teraz używać do innych plików i folderów. Można wymusić stosowanie warstw za pomocą następujących poleceń programu PowerShell:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-effective-vfs"></a>
**Jak jest *wolne miejsce w woluminie* interpretowane, gdy użytkownik ma wiele punktów końcowych serwera na woluminie?**  
    Jeśli istnieje więcej niż jeden punkt końcowy serwera na woluminie, próg wolnego miejsca skuteczne woluminu jest największy wolne miejsce w woluminie określony przez dowolnego punktu końcowego serwera na tym woluminie. Pliki będą należeć do warstwy zgodnie z ich niezależnie od tego, który punkt końcowy serwera, do którego należą te wzorce użycia. Na przykład, mając dwa punkty końcowe serwera na woluminie Punk końcowy 1 i Punk końcowy 2, gdzie Punk końcowy 1 ma progu wolnego miejsca na woluminie 25% i Punk końcowy 2 ma progu wolnego miejsca na woluminie 50% woluminu próg wolnego miejsca dla obu punktów końcowych serwera będą 50%.

* <a id="afs-files-excluded"></a>
**Które pliki i foldery, automatycznie są wyłączone przez synchronizacji plików Azure?**  
    Domyślnie synchronizacja plików Azure nie obejmuje następujące pliki:
    * desktop.ini
    * thumbs.db
    * ehthumbs.dB
    * ~$\*.\*
    * \*.laccdb
    * \*.tmp
    * 635D02A9D91C401B97884B82B3BCDAEA.\*

    Następujące foldery są również wykluczone domyślnie:

    * \System volume Information
    * \$RECYCLE.BIN
    * \SyncShareState

* <a id="afs-os-support"></a>
**Windows Server 2008 R2, Linux lub urządzenie magazynu dołączone do sieci (NAS) można używać synchronizacji plików Azure?**  
    Obecnie synchronizacji plików Azure obsługuje tylko systemu Windows Server 2016 i Windows Server 2012 R2. W tej chwili nie mamy żadnych innych planów, które firma Microsoft może udostępniać, ale jest otwarty, aby obsługa dodatkowych platform na życzenie klientów. Daj nam znać w [UserVoice plików Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files) platform mamy do obsługi.

## <a name="security-authentication-and-access-control"></a>Zabezpieczenia, uwierzytelniania i kontroli dostępu
* <a id="ad-support"></a>
**To jest uwierzytelnianie oparte na usłudze Active Directory i kontroli dostępu, obsługiwane przez usługi pliki Azure?**  
    Usługa pliki Azure oferuje zarządzanie kontrolą dostępu na dwa sposoby:

    - Sygnatury dostępu współdzielonego (SAS) służy do generowania tokenów, który ma określone uprawnienia, a które są ważne przez ustalony czas. Można na przykład wygenerować token z dostępem tylko do odczytu do określonego pliku, który ma upływie 10 minut. Każdy, kto posiada token, gdy token jest ważny do niego dostęp tylko do odczytu tego pliku tych 10 minut. Obecnie klucze sygnatury dostępu współdzielonego są obsługiwane tylko przy użyciu interfejsu API REST lub bibliotek klienckich. Udział plików na platformę Azure przy użyciu protokołu SMB musi być zainstalowany przy użyciu kluczy konta magazynu.

    - Synchronizacja programu Azure pliku zachowuje i replikuje wszystkie listy DACL lub poufnych list kontroli dostępu, (zarówno opartej na usłudze Active Directory i lokalne) do wszystkich punktów końcowych serwera, które synchronizowanych. Ponieważ systemu Windows Server mogą już uwierzytelniać za pomocą usługi Active Directory, synchronizacji plików Azure to efektywne opcja uzupełniające do pełną obsługę uwierzytelniania opartego na usłudze Active Directory i dociera obsługuje listy kontroli dostępu.

    Obecnie plików Azure nie obsługuje bezpośredniego usługi Active Directory.

* <a id="encryption-at-rest"></a>
**Jak zapewnić, że moje udziału plików na platformę Azure jest szyfrowane, gdy?**  
    Szyfrowanie usługi Magazyn Azure jest w trakcie jest domyślnie włączone we wszystkich regionach. Dla tych regionów nie trzeba podjąć działania, aby włączyć szyfrowanie. Dla innych regionów, zobacz [szyfrowania po stronie serwera](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Jak zapewnić dostęp do określonego pliku za pomocą przeglądarki sieci web**  
    Sygnatury dostępu współdzielonego służy do generowania tokenów, który ma określone uprawnienia, a które są ważne przez ustalony czas. Można na przykład wygenerować token, który umożliwia dostęp tylko do odczytu do określonego pliku, na wybrany okres czasu. Każdy, kto posiada adres URL dostępu do pliku bezpośrednio z dowolnej przeglądarki sieci web, ale token jest ważny. Z poziomu interfejsu użytkownika, takich jak Eksplorator usługi Storage, można łatwo wygenerować klucz sygnatury dostępu współdzielonego.

* <a id="file-level-permissions"></a>
**Jest to, można określić tylko do odczytu lub uprawnienia tylko do zapisu w folderach w ramach udziału?**  
    Jeśli udziałów plików za pomocą protokołu SMB, nie masz folderu poziom kontroli nad uprawnieniami. Jednak w przypadku utworzenia sygnatury dostępu współdzielonego przy użyciu interfejsu API REST lub bibliotek klienckich, na foldery w obrębie udziału można określić uprawnienia tylko do odczytu lub w trybie tylko do zapisu.

* <a id="ip-restrictions"></a>
**Czy można zaimplementować ograniczenia adresów IP do udziału plików na platformę Azure?**  
    Tak. Na poziomie konta magazynu można ograniczyć dostęp do udziału plików na platformę Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapory magazynu Azure i sieci wirtualne](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Jakie zasady zgodności danych obsługuje plików Azure?**  
   Usługa pliki Azure działa w oparciu o takiej samej architekturze magazynu, która jest używana w innych usługach magazynu w usłudze Azure Storage. Usługa pliki Azure stosuje się te same zasady zgodności danych, które są używane w innych usług magazynu Azure. Aby uzyskać więcej informacji o zgodności danych usługi Azure Storage, można pobrać i odwoływać się do [dokumentu ochrony danych Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409)i przejdź do [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

## <a name="on-premises-access"></a>Dostęp do lokalnego
* <a id="expressroute-not-required"></a>
**Czy masz łączenia do usługi pliki Azure za pomocą usługi Azure ExpressRoute lub użycie usługi synchronizacji programu Azure pliku lokalnego?**  
    Nie. Usługi ExpressRoute nie jest wymagane dostępu do udziału plików na platformę Azure. Jeśli instalacji plików na platformę Azure udziału bezpośrednio w infrastrukturze lokalnej, wszystkie które ma wymagane jest port 445 (ruch wychodzący protokołu TCP) otwórz dostęp do Internetu (jest to port, który korzysta z protokołu SMB do komunikacji). Jeśli używasz synchronizacji usługi Azure pliku, wszystkie, która jest wymagana jest port 443 (ruch wychodzący protokołu TCP) dla dostępu HTTPS (nie SMB wymagane). Jednak możesz *można* używać usługi ExpressRoute z jednej z tych opcji dostępu.

* <a id="mount-locally"></a>
**Jak zainstalować udział plików na platformę Azure na komputerze lokalnym?**  
    Udziałów plików za pomocą protokołu SMB, jeśli jest otwarty port 445 (ruch wychodzący protokołu TCP), a klient obsługuje protokół SMB 3.0 (na przykład, jeśli używasz systemu Windows 10 lub Windows Server 2016). Zablokowanie portu 445 w organizacji zasad lub przez Usługodawcę umożliwia synchronizację plików Azure dostęp do udziału plików platformy Azure.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Jak wykonywanie kopii zapasowych pliku Azure udostępnić?**  
    Można użyć okresowe [udostępnić migawki](storage-snapshots-files.md) ochrony przed przypadkowym usunięciu. Ponadto można AzCopy, Robocopy lub narzędzie kopii zapasowych innych firm, które można wykonać kopię zapasową udziału zainstalowanego pliku. 

## <a name="share-snapshots"></a>Udostępnianie migawki
### <a name="share-snapshots-general"></a>Udostępnianie migawki: Ogólne
* <a id="what-are-snaphots"></a>
**Co to są migawki udziału plików?**  
    Przy użyciu migawek udziału plików na platformę Azure do utworzenia wersji tylko do odczytu z udziałów plików. Możesz także użyć Azure plików do skopiowania do tego samego udziału, do innej lokalizacji w Azure lub lokalnie więcej modyfikacji wcześniejszej wersji zawartości kopii. Aby dowiedzieć się więcej na temat migawek udziału, zobacz [udostępnić migawki omówienie](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Gdzie są przechowywane Moje migawki udziału?**  
    Udziału migawki są przechowywane w tym samym kontem magazynu do udziału plików.

* <a id="snapshot-perf-impact"></a>
**Czy istnieją wszystkie wpływ na wydajność podczas używania migawek udziału?**  
    Udział migawki nie mają żadnych zmniejszenie wydajności.

* <a id="snapshot-consistency"></a>
**Czy udziału migawek spójnych z aplikacją?**  
    Nie, udziału migawki nie są spójne z aplikacjami. Użytkownik musi opróżnić zapisami z aplikacji do udziału przed wykonaniem migawki udziału.

* <a id="snapshot-limits"></a>
**Czy istnieją ograniczenia dotyczące liczby migawek udziału, których można użyć?**  
    Tak. Usługa pliki Azure można zachować maksymalnie 200 udziału migawki. Migawki udziału nie są traktowane kierunku przydziału udziału więc ma żadnego limitu poszczególnych udziałów całkowita ilość miejsca, który jest używany przez wszystkie migawki udziału. Limity konta magazynu jest nadal mają zastosowanie. Po 200 udziału migawki należy usunąć starsze migawek do tworzenia nowego udziału migawek.
* <a id="snapshot-cost"></a>
**Jaka udostępnić migawki kosztów?**  
    Standardowa transakcji i koszty magazynu w warstwie standardowa dotyczą migawki. Migawki są przyrostowe charakter. Podstawowy migawka jest udziału. Wszystkie kolejne migawki są przyrostowych i zapisze tylko różnic z wcześniejszej migawki. Oznacza to, że zmiany różnicowe, które będą widoczne w zestawieniu będzie minimalny, jeśli użytkownika zmian obciążenia jest minimalny. Zobacz [strony cennik](https://azure.microsoft.com/pricing/details/storage/files/) dla standardowe informacje o cenach usługi pliki Azure. Obecnie jest sposób, aby przyjrzeć się rozmiar używane przez migawki udziału porównując pojemność rachunku z używane pojemności. Pracujemy nad narzędzi, aby poprawić raportowania.


### <a name="create-share-snapshots"></a>Tworzenie migawek udziału
* <a id="file-snaphsots"></a>
**Można utworzyć migawki udziału poszczególnych plików?**  
    Udział migawki są tworzone na poziomie udziału plików. Można przywrócić pojedyncze pliki z migawki udziału pliku, ale nie można utworzyć migawki pliku na poziomie udziału. Jednak miały migawki udział w poziomie należy wyświetlić migawki udziału, gdzie określony plik został zmieniony, można to zrobić w obszarze **poprzednie wersje** w udziale zainstalowane z systemem Windows. 
    
    Daj nam znać, jeśli potrzebujesz funkcji migawki plików na [UserVoice plików Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>
**Można utworzyć migawki udziału udziału zaszyfrowany plik?**  
    Należy utworzyć migawkę udziału udziały plików platformy Azure, których magazynowane włączone szyfrowanie. Można przywrócić pliki z migawki udziału do udziału plików zaszyfrowanych. Jeśli na udział jest zaszyfrowany, migawki udziału również są szyfrowane.

* <a id="geo-redundant-snaphsots"></a>
**Czy Moje migawki udziału geograficznie nadmiarowego?**  
    Udział migawki mają tego samego nadmiarowość jako udział plików na platformę Azure, dla której zostały pobrane. Jeśli wybrano magazyn geograficznie nadmiarowy dla Twojego konta, na udział również migawki są przechowywane nadmiarowo w regionie sparowany.

### <a name="manage-share-snapshots"></a>Zarządzanie migawki udziału
* <a id="browse-snapshots-linux"></a>
**Czy można przeglądać Moje migawek udziału z systemem Linux?**  
    Azure CLI 2.0 służy do tworzenia, listy, przeglądanie i przywracania migawki udziału w systemie Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Czy można skopiować migawki udział do innego konta magazynu**  
    Możesz skopiować pliki z migawek udziału w innej lokalizacji, ale nie można skopiować migawki udziału samodzielnie.

### <a name="restore-data-from-share-snapshots"></a>Przywróć dane z migawki udziału
* <a id="promote-share-snapshot"></a>
**Czy można podwyższyć migawki udziału do udziału podstawowej?**  
    Można skopiować danych z migawki udział do innego miejsca docelowego. Nie można podwyższyć poziomu migawki udziału do udziału podstawowej.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Czy można przywrócić dane z mojej migawki udział do innego konta magazynu?**  
    Tak. Pliki z migawki udziału mogą być kopiowane do lokalizacji oryginalnej lub alternatywnej lokalizacji, która obejmuje tego samego konta magazynu lub innego konta magazynu, w tym samym regionie lub w różnych regionach. Możesz również skopiować pliki do lokalizacji lokalnej lub innych chmury.    
  
### <a name="clean-up-share-snapshots"></a>Wyczyść migawki udziału
* <a id="delete-share-keep-snapshots"></a>
**Można I Usuń Moje udział, ale nie Usuń Moje migawki udziału?**  
    Jeśli masz migawki aktywnego udziału w udziale, nie można usunąć udziału użytkownika. Aby usunąć migawki udziału, wraz z udziałem, można użyć interfejsu API. Można również usunąć zarówno migawki udziału, jak i w udziale w portalu Azure.

* <a id="delete-share-with-snapshots"></a>
**Co się dzieje z mojej migawki udziału I usunięcia konta magazynu?**  
    Usunięcie konta magazynu migawek udziału również są usuwane.

## <a name="billing-and-pricing"></a>Rozliczeniach i cenach
* <a id="vm-file-share-network-traffic"></a>
**Czy ruch sieciowy między maszyny Wirtualnej platformy Azure i liczba udziału plików na platformę Azure jako zewnętrzne użycie przepustowości i rozliczany subskrypcji?**  
    Jeśli udział plików i maszyna wirtualna znajdują się w tym samym regionie Azure, jest bezpłatne ruch między udziału plików i maszyny Wirtualnej. Jeśli udział plików i maszyna wirtualna znajdują się w różnych regionach, ruch między nimi są naliczane jako zewnętrzne użycie przepustowości.

* <a id="share-snapshot-price"></a>
**Ile korzystają koszt migawki?**  
     Podczas (wersja zapoznawcza) nie jest bezpłatne pojemności migawki udziału. Zastosuj koszty magazynu w warstwie standardowa Wyjście i transakcji. Po udostępnieniu wersji ogólnodostępnej należy subskrypcji będą naliczane opłaty pojemności i transakcji na udział migawki.
     
     Udział migawki są przyrostowe charakter. Migawka podstawowego udziału jest udziału. Wszystkie kolejne udziału migawki są przyrostowych i przechowywać różnica z poprzednim migawki udziału. Rozliczenie jest przeprowadzane tylko w przypadku zmiany zawartości. Jeśli udział z 100 GiB danych, ale tylko 5 GiB uległa zmianie od ostatniego migawki udziału, migawki udziału wykorzystuje tylko 5 GiB dodatkowe i są rozliczane za 105 GiB. Aby uzyskać więcej informacji o transakcji, a opłaty za wyjście standardowe, zobacz [strony cennik](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Skalowalność i wydajność
* <a id="files-scale-limits"></a>
**Jakie są limity skalowania plików Azure?**  
    Aby uzyskać informacje dotyczące skalowalności i wydajności usługi pliki Azure, zobacz [elementy docelowe skalowalności i wydajności usługi pliki Azure](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Potrzebuję udział plików większych niż aktualnie oferuje usługi pliki Azure. Można zwiększyć rozmiar mój udział plików na platformę Azure?**  
    Nie. Maksymalny rozmiar udziału plików na platformę Azure to 5 TiB. Obecnie jest nienaruszalne ograniczenie, które firma Microsoft nie można dostosować. Pracujemy nad rozwiązaniem do zwiększenia rozmiaru udziału do 100 TiB, ale nie mamy osi czasu, aby udostępnić w tej chwili.

* <a id="open-handles-quota"></a>
**Ilu klientów można uzyskać dostępu do tego samego pliku równocześnie?**   
    Brak przydziału 2000 otwartymi dojściami w jednym pliku. Jeśli masz 2000 otwartych dojść, jest wyświetlany komunikat o błędzie z informacją, że osiągnięto limit przydziału.

* <a id="zip-slow-performance"></a>
**Moje wydajność jest niska, gdy I Rozpakuj pliki w plikach Azure. Co zrobić?**  
    Aby przetransferować dużą liczbę plików do usługi pliki Azure, firma Microsoft zaleca korzystanie z narzędzia AzCopy (dla systemu Windows; w wersji zapoznawczej dla systemów Linux i UNIX) lub Azure PowerShell. Narzędzia te zostały zoptymalizowane pod kątem transferu sieciowego.

* <a id="slow-perf-windows-81-2012r2"></a>
**Dlaczego jest Mój wydajności powolne po mój udział plików Azure można zainstalować w systemie Windows Server 2012 R2 lub Windows 8.1?**  
    Istnieje znany problem w przypadku instalowania udziału plików na platformę Azure w systemie Windows Server 2012 R2 i Windows 8.1. Problem został poprawiono w aktualizacji zbiorczej kwietnia 2014 r. dla Windows 8.1 i Windows Server 2012 R2. Aby uzyskać optymalną wydajność zapewnia wszystkich wystąpień systemu Windows Server 2012 R2 i Windows 8.1 tej poprawki stosowane. (Zawsze otrzymasz poprawki systemu Windows za pośrednictwem usługi Windows Update). Aby uzyskać więcej informacji, zobacz skojarzonym artykułem w bazie wiedzy Microsoft Knowledge Base [powolne działanie, gdy uzyskujesz dostęp do usługi pliki Azure z Windows 8.1 lub Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funkcje i współdziałanie z innymi usługami
* <a id="cluster-witness"></a>
**Można użyć Moje udziału plików na platformę Azure jako *monitora udostępniania plików* dla moich klastra pracy awaryjnej systemu Windows Server?**  
    Obecnie ta konfiguracja nie jest obsługiwana dla udziału plików na platformę Azure. Aby uzyskać więcej informacji na temat sposobu ustawiania to konto magazynu obiektów Blob platformy Azure, zobacz [wdrażanie monitora chmury dla klastra trybu Failover](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**W wystąpieniu kontenera Azure mogą zainstalować udział plików na platformę Azure?**  
    Tak, udziały plików platformy Azure są dobrym rozwiązaniem, jeśli chcesz zachować informacji poza okres istnienia wystąpienia kontenera. Aby uzyskać więcej informacji, zobacz [instalowanie udziału plików na platformę Azure z wystąpień kontenera Azure](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Jest to operacja zmiany nazwy w interfejsie API REST?**  
    Nie w tej chwili.

* <a id="nested-shares"></a>
**Można skonfigurować zagnieżdżanie? Innymi słowy udział w udziale?**  
    Nie. Udział plików *jest* sterownik wirtualny z możliwością instalacji, dlatego zagnieżdżanie nie są obsługiwane.

* <a id="ibm-mq"></a>
**Jak używać usługi pliki Azure z programem IBM MQ?**  
    Firma IBM wydała dokument, który pomaga skonfigurować usługi pliki Azure z usługą IBM klientom IBM MQ. Aby uzyskać więcej informacji, zobacz [jak skonfigurować Menedżera kolejki wielu wystąpieniach IBM MQ z usługą Microsoft Azure pliki](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Zobacz także
* [Rozwiązywanie problemów z plików Azure w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Rozwiązywanie problemów z plików Azure w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Rozwiązywanie problemów z synchronizacji plików Azure (wersja zapoznawcza)](storage-sync-files-troubleshoot.md)
