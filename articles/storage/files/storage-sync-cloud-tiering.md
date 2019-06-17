---
title: Obsługa warstw w chmurze opis usługi Azure File Sync | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o funkcji usługi Azure File Sync, Obsługa poziomów w chmurze
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 09/21/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1851e9b2bb5ff86583228136dee977001cf0a3fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64714954"
---
# <a name="cloud-tiering-overview"></a>Omówienie obsługi warstw w chmurze
Chmura warstw to opcjonalna funkcja usługi Azure File Sync, w których często używanych plików są buforowane lokalnie na serwerze, podczas gdy inne pliki są organizowane w warstwy do usługi Azure Files na podstawie ustawień zasad. Gdy plik jest warstwowe, filtru systemu plików usługi Azure File Sync (StorageSync.sys) zamienia plik lokalnie wskaźnik lub punkt ponownej analizy. Punkt ponownej analizy reprezentuje adres URL do pliku w usłudze Azure Files. Plik warstwowy zawiera zarówno atrybut "offline", jak i atrybut FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS ustawiony w systemie plików NTFS, dzięki czemu aplikacje innych firm można bezpiecznie zidentyfikować pliki warstwowe.
 
Gdy użytkownik otwiera plik warstwowy, usługi Azure File Sync bezproblemowo odwołania do danych plików z usługi Azure Files użytkownik nie musi wiedzieć, że plik rzeczywiście jest przechowywana w usłudze Azure. 
 
 > [!Important]  
 > Chmura obsługi warstw nie jest obsługiwana dla punkty końcowe serwera w woluminach systemu Windows, a tylko pliki większe niż 64 KiB rozmiarze mogą należeć do warstwy do usługi Azure Files.
    
Usługa Azure File Sync nie obsługuje warstw pliki mniejsze niż 64 KiB, zmniejszenie wydajności warstw i odwoływania takich małych plików może być większe niż oszczędności miejsca.

 > [!Important]  
 > Przywołanie pliki, które zostały warstwowe, przepustowości sieci należy co najmniej 1 MB/s. Jeśli przepustowość sieci jest mniejszy niż 1 MB/s, plików może zakończyć się niepowodzeniem do odwołania z błędem przekroczenia limitu czasu.

## <a name="cloud-tiering-faq"></a>Chmury warstw — często zadawane pytania

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Jak pracy obsługi warstw w chmurze?
Filtr systemu Azure File Sync mapy "cieplnej" przestrzeni nazw jest oparta na każdy punkt końcowy serwera. Monitoruje ona dostęp (operacje odczytu i zapisu) wraz z upływem czasu, a następnie w oparciu o częstotliwość i powracający dostępu, przypisuje na ciepło wyniku do każdego pliku. Często używanych plików, który niedawno został otwarty będą uznawane za gorąca, plik, który jest ledwie modyfikacji i nie była używana przez pewien czas zostanie uznane za superpaska. Gdy woluminu plików na serwerze przekroczy próg wolnego miejsca na woluminie, ustawione, będzie warstwy niezbędnym pliki do usługi Azure Files, dopóki nie zostanie spełniony swoje procent wolnego miejsca.

W wersjach 4.0 i powyżej agenta usługi Azure File Sync można również określić zasad daty w każdym punkcie końcowym serwera będzie warstwy wszystkie pliki, które nie otwierane ani modyfikowane w ciągu określonej liczby dni.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Jak działa zasad obsługi poziomów warstw wolnego miejsca na woluminie?
Wolne miejsce w woluminie jest ilość wolnego miejsca, którą chcesz aby zarezerwować na woluminie, na którym znajduje się punkt końcowy serwera. Na przykład jeśli wolne miejsce w woluminie jest ustawiona na 20% na woluminie, który ma jeden punkt końcowy serwera, się do 80 procent miejsca na woluminie będą zajmowany przez ostatnio używanych plików, przy użyciu pozostałych plików, które nie mieszczą się w tym obszarze warstwy do platformy Azure. Wolne miejsce w woluminie ma zastosowanie na poziomie woluminu, a nie na poziomie poszczególnych katalogów lub grup synchronizacji. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Jak działa zasad obsługi poziomów warstw wolnego miejsca na woluminie w odniesieniu do nowych punkty końcowe serwera
Gdy punkt końcowy serwera jest nowo aprowizowany i podłączony do udziału plików platformy Azure, serwer zostanie najpierw ściągnąć przestrzeni nazw, a następnie pobierze rzeczywiste pliki do momentu trafienia progu wolnego miejsca na woluminie. Ten proces jest nazywany także szybkiego odzyskiwania lub przywracania szybkie przestrzeni nazw.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Jak wolne miejsce w woluminie interpretowania mam wiele punktów końcowych serwera na woluminie?
Jeśli istnieje więcej niż jeden punkt końcowy serwera na woluminie, próg wolnego miejsca na efektywne woluminu jest największy wolne miejsce w woluminie określona dla dowolnego punktu końcowego serwera, w tym woluminie. Pliki będą umieszczane w taki sposób, zgodnie z ich wzorce użycia niezależnie od tego, z którym punktem końcowym serwera, do którego należą. Na przykład jeśli masz dwa punkty końcowe serwera na woluminie Punk końcowy 1 i Endpoint2, gdzie Punk końcowy 1 ma próg wolnego miejsca na wolumin o 25%, a Endpoint2 próg wolnego miejsca na woluminie 50% próg wolnego miejsca na woluminie zarówno punkty końcowe serwera będzie 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Jak zasad obsługi poziomów warstw Data działa w połączeniu z ilością wolnego miejsca wolumin, zasady warstw? 
Podczas włączania obsługi warstw na punkt końcowy serwera w chmurze, możesz ustawić zasady wolnego miejsca na woluminie. Zawsze pierwszeństwo przed wszystkie inne zasady, w tym zasady Data. Opcjonalnie można włączyć daty, które zasady dla każdego punktu końcowego serwera na czy wolumin, co oznacza że tylko pliki dostępne (to znaczy odczytać lub zapisywane) w zakresie dni, w których te zasady opisują będą przechowywane lokalne, ze wszystkimi plikami staler warstwowego. Należy pamiętać, że zasad wolnego miejsca na woluminie zawsze pierwszeństwo i gdy nie ma wystarczającej ilości wolnego miejsca na woluminie, aby zachować dowolną liczbę dni, przez które plików zgodnie z opisem w zasadach daty, usługi Azure File Sync będzie nadal warstw najzimniejszym plików do woluminu bezpłatne procent miejsca jest spełniony.

Na przykład załóżmy, że zasady obsługi warstw na podstawie daty 60 dni i zasady wolnego miejsca na woluminie 20%. Jeśli po zastosowaniu zasad daty, jest mniej niż 20% wolnego miejsca na wolumin, zasady wolnego miejsca na woluminie zaczną działać, a zastępują zasady daty. W rezultacie zostanie więcej plików z zasadami warstwowej, taki sposób, że ilość danych przechowywane na serwerze może zostać skrócony z 60 dni dla danych do 45 dni. Z drugiej strony tych zasad spowoduje to wymuszenie obsługi warstw na pliki, które wykroczy poza swój zakres czasu, nawet jeżeli nie Osiągnięto próg wolnego miejsca — dzięki czemu pliku, który jest 61 dni będą umieszczane, nawet jeśli wolumin jest pusty.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Jak określić odpowiednią ilość wolnego miejsca na woluminie?
Ilość danych, należy zachować lokalnego zależy od kilku czynników: przepustowości, wzorzec dostępu do zestawu danych i budżetu. W przypadku połączeń o niskiej przepustowości można przechowywać więcej danych lokalnych, aby upewnić się, że ma minimalne opóźnienie dla użytkowników. W przeciwnym razie możesz oprzeć je na współczynnik zmian danych w danym okresie. Na przykład jeśli poinformować, że około 10% zmian zestaw danych 1 TB lub aktywnie jest dostępny co miesiąc, a następnie może chcesz zachować 100 GB lokalnego więc użytkownik są nie często odwoływania plików. Jeśli wolumin jest 2TB, a następnie chcesz zachować 5% (lub 100 GB) lokalnych, co oznacza, pozostałe 95% jest swoje procent wolnego miejsca na woluminie. Jednak zalecamy sytuacja: dodajesz buforu dla okresów wyższe zmian — innymi słowy, począwszy od niższy procent wolnego miejsca na woluminie, a następnie dostosowując go w razie potrzeby później. 

Przechowywanie większej ilości danych lokalnych oznacza niższe koszty ruchu wychodzącego zgodnie z mniejszą liczbę plików będzie można wycofać z platformy Azure, ale wymaga również obsługa większej ilości magazynu w środowisku lokalnym, który zawiera swój własny kosztów. Po utworzeniu wystąpienia usługi Azure File Sync wdrożone, można sprawdzić ruch wychodzący konta magazynu w przybliżeniu ocenić, czy ustawienia wolnego miejsca na woluminie są odpowiednie dla Twojego użycia. Zakładając, że konto magazynu zawiera tylko usługi Azure pliku synchronizacji punkt końcowy w chmurze (czyli Twojej udziału synchronizacji), a następnie wyjście wysoki oznacza, że wiele plików są odwoływanego z chmury, należy rozważyć zwiększenie lokalnej pamięci podręcznej.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Ja dodałem jeszcze nowy punkt końcowy serwera. Jak długo, aż do moich plików w tej warstwie serwera?
W wersjach 4.0 i powyżej agenta usługi Azure File Sync, gdy pliki zostały przekazane do udziału plików platformy Azure one będą umieszczane zgodnie z zasadami tak szybko, jak następnego uruchomienia sesji warstw, co się stanie, co godzinę. Starsze wersje agentów warstw może trwać do 24 godzin do wykonania.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Jak sprawdzić, czy plik został warstwy?
Istnieje kilka sposobów, aby sprawdzić, czy plik został warstwy do udziału plików platformy Azure:
    
   *  **Sprawdź atrybuty pliku do pliku.**
     Kliknij prawym przyciskiem myszy w pliku, przejdź do **szczegóły**, a następnie przewiń w dół do **atrybuty** właściwości. Plikami warstwowym ma następujące atrybuty zestawu:     
        
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
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Jeśli plik ma punkt ponownej analizy, powinna się pojawić **ponownej analizy, wartość tagu: 0x8000001e**. Ta wartość szesnastkowa to wartość punktu ponownej analizy, która jest właścicielem usługi Azure File Sync. Dane wyjściowe zawierają również dane ponownej analizy, która reprezentuje ścieżkę do pliku w udziale plików platformy Azure.

        > [!WARNING]  
        > `fsutil reparsepoint` Narzędzie polecenie ma również możliwość usunięcia punktów ponownej analizy. Nie wykonuj tego polecenia, chyba, że zespół inżynierów usługi Azure File Sync monit. Uruchomienie tego polecenia może spowodować utratę danych. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Plik, który ma być użyty został warstwowego. Jak można odwołać pliku na dysku, aby używać go lokalnie?
Najprostszym sposobem odwołania pliku na dysku jest można otworzyć pliku. Filtr systemu plików usługi Azure File Sync (StorageSync.sys) bezproblemowo pobiera plik z udziału plików platformy Azure bez konieczności wykonywania pracy ze strony użytkownika. Dla typów plików, które mogą być częściowo odczytu, takich jak pliki multimedialne lub zip, otwieranie pliku nie jest pobierany całego pliku.

Również umożliwia PowerShell wymuszenie można wycofać. Ta opcja może być przydatne, jeśli chcesz odwołać wielu plików jednocześnie, np. wszystkie pliki w folderze. Otwórz sesję programu PowerShell, aby węzeł serwera, na którym zainstalowano usługę Azure File Sync, a następnie uruchom następujące polecenia środowiska PowerShell:
    
    ```powershell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Dlaczego nie *rozmiar na dysku* właściwość dopasowania plików *rozmiar* właściwości po zakończeniu korzystania z usługi Azure File Sync? 
Eksploratora plików Windows udostępnia dwie właściwości do reprezentowania rozmiaru pliku: **Rozmiar** i **rozmiar na dysku**. Te właściwości różnią się w niewielkim stopniu w znaczenie. **Rozmiar** reprezentuje pełną rozmiaru pliku. **Rozmiar na dysku** reprezentuje rozmiar strumienia pliku, który jest przechowywany na dysku. Wartości tych właściwości można różnią się z różnych powodów, takich jak kompresja, korzystanie z funkcji deduplikacji danych lub obsługa warstw przy użyciu usługi Azure File Sync w chmurze. Jeśli plik jest warstwowe udziału plików platformy Azure, rozmiar na dysku wynosi zero, ponieważ strumienia pliku są przechowywane w udziale plików platformy Azure, a nie na dysku. Istnieje także możliwość pliku do się częściowo warstwowego (lub częściowo odwołane). W pliku częściowo warstwowego część pliku znajduje się na dysku. Taka sytuacja może wystąpić, częściowo są odczytywane przez aplikacje, takie jak odtwarzaczy multimedialnych lub zip narzędzia plików. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Jak wymusić, plik lub katalog do umieszczane?
Gdy jest włączona funkcja obsługi warstw w chmurze, obsługi warstw automatycznie pliki warstw w chmurze oparte na ostatni dostęp i zmodyfikuj czas do osiągnięcia procent wolnego miejsca na woluminie określone na punkt końcowy w chmurze. Czasami jednak warto to wymuszone ręcznie plik do warstwy. Może to być przydatne, jeśli zapiszesz dużych plików, które nie będą do ponownego użycia przez długi czas i mają ilość wolnego miejsca na woluminie teraz do użycia dla innych plików i folderów. Można wymusić warstw przy użyciu następujących poleceń programu PowerShell:

    ```powershell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

## <a name="next-steps"></a>Następne kroki
* [Planowanie wdrażania usługi synchronizacji plików platformy Azure](storage-sync-files-planning.md)
