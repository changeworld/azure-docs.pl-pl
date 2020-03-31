---
title: Opis warstwowej chmury synchronizacji plików platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o usłudze Azure File Sync cloud tiering
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 11f9097fc4875f0a4300ac56dafe7af9a0b00c97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454622"
---
# <a name="cloud-tiering-overview"></a>Omówienie warstw w chmurze
Warstwa chmury to opcjonalna funkcja synchronizacji plików platformy Azure, w której często używane pliki są buforowane lokalnie na serwerze, podczas gdy wszystkie inne pliki są warstwowe do usługi Azure Files na podstawie ustawień zasad. Gdy plik jest warstwowy, filtr systemu plików synchronizacji plików azure (StorageSync.sys) zastępuje plik lokalnie wskaźnikiem lub punktem ponownejparacji. Punkt ponownejparieny reprezentuje adres URL pliku w usłudze Azure Files. Plik warstwowy ma zarówno atrybut "offline", jak i atrybut FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS w systemie plików NTFS, dzięki czemu aplikacje innych firm mogą bezpiecznie identyfikować pliki warstwowe.
 
Gdy użytkownik otworzy plik warstwowy, usługa Azure File Sync bezproblemowo przywołuje dane pliku z plików platformy Azure bez konieczności wienia, że plik jest przechowywany na platformie Azure. 
 
 > [!Important]  
 > Warstwowe przetwarzanie w chmurze nie jest obsługiwane w woluminie systemowym systemu Windows.
    
 > [!Important]  
 > Aby przywołać pliki, które zostały warstwowe, przepustowość sieci powinna wynosić co najmniej 1 Mb/s. Jeśli przepustowość sieci jest mniejsza niż 1 Mb/s, pliki mogą nie zostać odwołane z powodu błędu limitu czasu.

## <a name="cloud-tiering-faq"></a>Często zadawane pytania dotyczące warstw w chmurze

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Jak działa warstwowanie w chmurze?
Filtr systemu Azure File Sync tworzy "mapę cieplną" obszaru nazw w każdym punkcie końcowym serwera. Monitoruje dostęp (operacje odczytu i zapisu) w czasie, a następnie, na podstawie zarówno częstotliwości, jak i częstotliwości dostępu, przypisuje wynik ciepła do każdego pliku. Często używany plik, który został niedawno otwarty, będzie uważany za gorący, podczas gdy plik, który jest ledwo dotknął i nie był dostępny przez jakiś czas, będzie uważany za fajny. Gdy wolumin pliku na serwerze przekracza ustawiony próg wolnego miejsca woluminu, będzie warstwy najchłodniejszych plików do usługi Azure Files, dopóki nie zostanie osiągnięty procent wolnego miejsca.

W wersjach 4.0 i powyżej agenta usługi Azure File Sync można dodatkowo określić zasady daty dla każdego punktu końcowego serwera, który będzie warstwy żadnych plików, które nie są dostępne lub modyfikowane w ciągu określonej liczby dni.

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>Jaki jest minimalny rozmiar pliku do warstwy?
W przypadku wersji agenta 9.x i nowszych minimalny rozmiar pliku do warstwy jest oparty na rozmiarze klastra systemu plików (dwukrotnie większy rozmiar klastra systemu plików). Na przykład jeśli rozmiar klastra systemu plików NTFS wynosi 4 KB, wynikowy minimalny rozmiar pliku dla warstwy pliku to 8 KB. W przypadku agenta w wersji 8.x i starszej minimalny rozmiar pliku do warstwy to 64 KB.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Jak działają zasady obsługi warstw wolnego miejsca na woluminie?
Wolne miejsce na woluminie to ilość wolnego miejsca, którą chcesz zarezerwować na woluminie, na którym znajduje się punkt końcowy serwera. Na przykład jeśli wolne miejsce na wolumin jest ustawiona na 20% na woluminie, który ma jeden punkt końcowy serwera, do 80% miejsca na woluminie będą zajmowane przez ostatnio dostępne pliki, z wszystkich pozostałych plików, które nie mieszczą się w tym miejscu warstwowych do platformy Azure. Wolne miejsce na woluminie ma zastosowanie na poziomie woluminu, a nie na poziomie poszczególnych katalogów lub grup synchronizacji. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Jak działają zasady obsługi warstw wolnego miejsca na woluminie w odniesieniu do nowych punktów końcowych serwera?
Gdy punkt końcowy serwera jest nowo aprowizować i połączony z udziałem plików platformy Azure, serwer najpierw ściągnie obszar nazw, a następnie pociągnie rzeczywiste pliki, dopóki nie osiągnie progu wolnego miejsca na woluminie. Ten proces jest również znany jako szybkie odzyskiwanie po awarii lub szybkie przywracanie przestrzeni nazw.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Jak jest traktowane wolne miejsce w woluminie, gdy znajduje się w nim wiele punktów końcowych serwera?
Gdy na woluminie znajduje się więcej niż jeden punkt końcowy serwera, efektywny próg wolnego miejsca na woluminie jest największym wolnym miejscem woluminu określonym w dowolnym punkcie końcowym serwera na tym woluminie. Pliki będą warstwowe zgodnie z ich wzorców użycia, niezależnie od tego, który punkt końcowy serwera, do którego należą. Na przykład jeśli masz dwa punkty końcowe serwera na woluminie, Endpoint1 i Endpoint2, gdzie punkt końcowy1 ma próg wolnego miejsca woluminu 25%, a punkt końcowy2 ma próg wolnego miejsca woluminu 50%, próg wolnego miejsca woluminu dla obu punktów końcowych serwera będzie wynosić 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Jak działają zasady obsługi warstw dat w połączeniu z zasadami obsługi poziomów wolnego miejsca na woluminie? 
Po włączeniu warstw w chmurze w punkcie końcowym serwera, można ustawić zasady wolnego miejsca woluminu. Zawsze ma pierwszeństwo przed innymi zasadami, w tym zasadami dotyczącymi dat. Opcjonalnie można włączyć zasady daty dla każdego punktu końcowego serwera na tym woluminie, co oznacza, że tylko pliki, do których uzyskuje się dostęp (czyli do odczytu lub zapisane) w ciągu dni, które opisano w tej zasadie, będą przechowywane lokalnie, przy czym wszystkie pliki stali będą warstwowe. Należy pamiętać, że zasady wolnego miejsca na woluminie zawsze mają pierwszeństwo, a gdy nie ma wystarczającej ilości wolnego miejsca na woluminie, aby zachować tyle dni plików, zgodnie z zasadami daty, usługa Azure File Sync będzie kontynuować tworzenie warstw najzimniejszych plików, dopóki wolumin nie będzie wolny. procent miejsca.

Załóżmy na przykład, że masz zasadę warstwową opartą na dacie wynoszącą 60 dni i zasady wolnego miejsca na woluminach wynoszące 20%. Jeśli po zastosowaniu zasad daty na woluminie jest mniej niż 20% wolnego miejsca, zasady wolnego miejsca woluminu zostaną włączone i zastąpią zasady daty. Spowoduje to, że więcej plików jest warstwowych, tak, że ilość danych przechowywanych na serwerze może zostać zmniejszona z 60 dni danych do 45 dni. Z drugiej strony ta zasada wymusi warstwowanie plików, które mieszczą się poza zakresem czasu, nawet jeśli nie osiągnąłeś progu wolnego miejsca — więc plik, który ma 61 dni, będzie warstwowy, nawet jeśli wolumin jest pusty.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Jak mogę określić odpowiednią ilość wolnego miejsca na woluminie?
Ilość danych, które należy zachować lokalnie zależy od kilku czynników: przepustowości, wzorca dostępu zestawu danych i budżetu. Jeśli masz połączenie o niskiej przepustowości, możesz zachować więcej danych lokalnych, aby upewnić się, że jest minimalne opóźnienie dla użytkowników. W przeciwnym razie można oprzeć go na współczynniku zmian w danym okresie. Na przykład jeśli wiesz, że około 10% 1 TB zmiany zestawu danych lub jest aktywnie dostępny co miesiąc, a następnie można zachować 100 GB lokalne, więc nie są często przywoływanie plików. Jeśli wolumin ma 2 TB, będziesz chciał zachować 5% (lub 100 GB) lokalnego, co oznacza, że pozostałe 95% to procent wolnego miejsca. Zaleca się jednak dodanie buforu do uwzględnienia okresów wyższych zmian — innymi słowy, począwszy od niższego procentu wolnego miejsca woluminu, a następnie dopasowywania go w razie potrzeby później. 

Utrzymywanie większej liczby danych lokalnych oznacza niższe koszty wyjścia, ponieważ mniej plików zostanie wycofanych z platformy Azure, ale wymaga również utrzymania większej ilości magazynu lokalnego, co wiąże się z własnym kosztem. Po wdrożeniu wystąpienia usługi Azure File Sync możesz sprawdzić, czy wyjście konta magazynu jest w przybliżeniu możliwe do oszacowania, czy ustawienia wolnego miejsca woluminu są odpowiednie do użycia. Zakładając, że konto magazynu zawiera tylko punkt końcowy chmury synchronizacji plików platformy Azure (czyli udział synchronizacji), a następnie wysoki ruch wychodzący oznacza, że wiele plików są przywoływane z chmury i należy rozważyć zwiększenie lokalnej pamięci podręcznej.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Dodałem nowy punkt końcowy serwera. Jak długo moje pliki w tej warstwie serwera?
W wersjach 4.0 i powyżej agenta usługi Azure File Sync po przekazaniu plików do udziału plików platformy Azure będą one warstwowe zgodnie z zasadami, gdy tylko zostanie uruchomiona następna sesja warstwowa, co nastąpi raz na godzinę. W przypadku starszych agentów warstwowanie może potrwać do 24 godzin.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Jak sprawdzić, czy plik został warstwowy?
Istnieje kilka sposobów, aby sprawdzić, czy plik został warstwowy do udziału plików platformy Azure:
    
   *  **Sprawdź atrybuty pliku w pliku.**
     Kliknij prawym przyciskiem myszy plik, przejdź do **strony Szczegóły**, a następnie przewiń w dół do właściwości **Atrybuty.** Plik warstwowy ma ustawiony następujący zestaw atrybutów:     
        
        | Litera atrybutu | Atrybut | Definicja |
        |:----------------:|-----------|------------|
        | A | Archiwum | Wskazuje, że kopia zapasowa pliku powinna być zabezpieczona przez oprogramowanie do tworzenia kopii zapasowych. Ten atrybut jest zawsze ustawiony, niezależnie od tego, czy plik jest warstwowy lub przechowywane w pełni na dysku. |
        | P | Rozrzedzony plik | Wskazuje, że plik jest plikiem rozrzedzonym. Plik rozrzedzony jest wyspecjalizowanym typem pliku, który ntfs oferuje do efektywnego wykorzystania, gdy plik na strumieniu dysku jest w większości pusty. Usługa Azure File Sync używa rozrzedzonych plików, ponieważ plik jest w pełni warstwowy lub częściowo odwołany. W pliku w pełni warstwowym strumień plików jest przechowywany w chmurze. W częściowo odwołanym pliku ta część pliku jest już na dysku. Jeśli plik jest w pełni odwołany na dysk, usługa Azure File Sync konwertuje go z pliku rozrzedzone na zwykły plik. Ten atrybut jest ustawiony tylko w systemie Windows Server 2016 i starszych.|
        | M | Przywoływanie dostępu do danych | Wskazuje, że dane pliku nie są w pełni obecne w magazynie lokalnym. Odczytywanie pliku spowoduje co najmniej część zawartości pliku, które mają być pobierane z udziału pliku platformy Azure, do którego jest połączony punkt końcowy serwera. Ten atrybut jest ustawiony tylko w systemie Windows Server 2019. |
        | L | Punkt ponownej analizy | Wskazuje, że plik ma punkt ponownejparek. Punkt ponownejparowania jest specjalnym wskaźnikiem do użycia przez filtr systemu plików. Usługa Azure File Sync używa punktów ponownejparieni do zdefiniowania w filtrze systemu plików synchronizacji plików platformy Azure (StorageSync.sys) lokalizacji w chmurze, w której przechowywany jest plik. Obsługuje to bezproblemowy dostęp. Użytkownicy nie muszą wiedzieć, że usługa Azure File Sync jest używana ani jak uzyskać dostęp do pliku w udziale plików platformy Azure. Gdy plik jest w pełni odwołany, usługa Azure File Sync usuwa punkt ponownejparacji z pliku. |
        | O | W trybie offline | Wskazuje, że część lub całość zawartości pliku nie jest przechowywana na dysku. Gdy plik jest w pełni odwołany, usługa Azure File Sync usuwa ten atrybut. |

        ![Okno dialogowe Właściwości pliku z zaznaczoną kartą Szczegóły](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Możesz zobaczyć atrybuty wszystkich plików w folderze, dodając pole **Atrybuty** do tabeli wyświetlanej Eksploratora plików. Aby to zrobić, kliknij prawym przyciskiem myszy istniejącą kolumnę (na przykład **Rozmiar**), wybierz pozycję **Więcej**, a następnie wybierz **polecenie Atrybuty** z listy rozwijanej.
        
   * **Służy `fsutil` do sprawdzania punktów ponownejparowania w pliku.**
       Zgodnie z opisem w poprzedniej opcji plik warstwowy zawsze ma ustawiony punkt ponownejparacji. Wskaźnik ponownejparacji jest specjalnym wskaźnikiem dla filtru systemu plików synchronizacji plików azure (StorageSync.sys). Aby sprawdzić, czy plik ma punkt ponownejparacji, w wierszu polecenia z `fsutil` podwyższonym poziomem uprawnień lub w oknie programu PowerShell uruchom narzędzie:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Jeśli plik ma punkt ponownejparie, można oczekiwać, aby zobaczyć **wartość znacznika ponownej próby: 0x8000001e**. Ta wartość szesnastkowa jest wartością punktu ponownejparowania, która jest własnością usługi Azure File Sync. Dane wyjściowe zawiera również dane ponownejparie, który reprezentuje ścieżkę do pliku w udziale plików platformy Azure.

        > [!WARNING]  
        > Polecenie `fsutil reparsepoint` narzędzia ma również możliwość usunięcia punktu ponownej próby. Nie należy wykonywać tego polecenia, chyba że zespół inżynierów usługi Azure File Sync prosi o to. Uruchomienie tego polecenia może spowodować utratę danych. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Plik, którego chcę użyć, został warstwowy. Jak mogę przywołać plik na dysku, aby używać go lokalnie?
Najprostszym sposobem przywołania pliku na dysk jest otwarcie pliku. Filtr systemu plików synchronizacji plików platformy Azure (StorageSync.sys) bezproblemowo pobiera plik z udziału plików platformy Azure bez żadnej pracy z Twojej strony. W przypadku typów plików, które można częściowo odczytać, takich jak multimedia lub pliki zip, otwarcie pliku nie pobiera całego pliku.

Można również użyć programu PowerShell, aby wymusić odwołanie pliku. Ta opcja może być przydatna, jeśli chcesz odwołać wiele plików jednocześnie, takich jak wszystkie pliki w folderze. Otwórz sesję programu PowerShell do węzła serwera, w którym jest zainstalowana usługa Azure File Sync, a następnie uruchom następujące polecenia programu PowerShell:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Parametry opcjonalne:
* `-Order CloudTieringPolicy`najpierw przypomni najnowsze zmodyfikowane pliki.  
* `-ThreadCount`określa, ile plików można przywoływania równolegle.
* `-PerFileRetryCount`określa, jak często będzie podejmowana próba wycofania pliku, który jest aktualnie zablokowany.
* `-PerFileRetryDelaySeconds`określa czas w sekundach między ponowną próbą odwołania i zawsze powinien być używany w połączeniu z poprzednim parametrem.

> [!Note]  
> Jeśli wolumin lokalny obsługujący serwer nie ma wystarczającej ilości wolnego `Invoke-StorageSyncFileRecall` miejsca, aby odwołać wszystkie dane warstwowe, polecenie cmdlet kończy się niepowodzeniem.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Dlaczego właściwość *Rozmiar na dysku* dla pliku nie jest zgodna z właściwością *Size* po użyciu usługi Azure File Sync? 
Eksplorator plików systemu Windows udostępnia dwie właściwości reprezentujące rozmiar pliku: **Rozmiar** i **Rozmiar na dysku**. Właściwości te różnią się subtelnie znaczeniem. **Rozmiar** reprezentuje pełny rozmiar pliku. **Rozmiar na dysku** reprezentuje rozmiar strumienia plików, który jest przechowywany na dysku. Wartości dla tych właściwości mogą się różnić z różnych powodów, takich jak kompresja, użycie deduplikacji danych lub warstwy w chmurze za pomocą usługi Azure File Sync. Jeśli plik jest warstwowy do udziału plików platformy Azure, rozmiar na dysku wynosi zero, ponieważ strumień plików jest przechowywany w udziale plików platformy Azure, a nie na dysku. Jest również możliwe dla pliku, które mają być częściowo warstwowe (lub częściowo odwołane). W pliku częściowo warstwowym część pliku znajduje się na dysku. Może to nastąpić, gdy pliki są częściowo odczytywane przez aplikacje, takie jak odtwarzacze multimedialne lub narzędzia zip. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Jak wymusić warstwowe tworzenie pliku lub katalogu?
Gdy funkcja warstwowa chmury jest włączona, warstwowanie chmury automatycznie warstw plików na podstawie ostatniego dostępu i zmodyfikować razy, aby osiągnąć procent wolnego miejsca woluminu określonego w punkcie końcowym chmury. Czasami jednak można ręcznie wymusić plik do warstwy. Może to być przydatne, jeśli zapiszesz duży plik, którego nie zamierzasz używać ponownie przez długi czas, a chcesz, aby wolne miejsce na woluminie było teraz używane dla innych plików i folderów. Można wymusić warstwowanie przy użyciu następujących poleceń programu PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>Dlaczego w Eksploratorze Windows nie są wyświetlane miniatury ani podglądy warstwowe?
W przypadku plików warstwowych miniatury i podglądy nie będą widoczne w punkcie końcowym serwera. To zachowanie jest oczekiwane, ponieważ funkcja pamięci podręcznej miniatur w systemie Windows celowo pomija odczytywanie plików z atrybutem offline. Po włączeniu warstwy chmury odczytywanie plików warstwowych spowodowałoby ich pobranie (przypomniane).

To zachowanie nie jest specyficzne dla usługi Azure File Sync, Eksplorator Windows wyświetla "szary X" dla wszystkich plików, które mają zestaw atrybutów w trybie offline. Ikona X będzie widoczna podczas uzyskiwania dostępu do plików za sprawą małych i średnich programów. Szczegółowe wyjaśnienie tego zachowania można znaleźć w[https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)


## <a name="next-steps"></a>Następne kroki
* [Planowanie wdrożenia synchronizacji plików platformy Azure](storage-sync-files-planning.md)
