---
title: Informacje na temat obsługi warstw w chmurze Azure File Sync | Microsoft Docs
description: Dowiedz się więcej na temat obsługi warstw w chmurze w usłudze Azure File Sync
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 36b09ce8ece010ff24345ddb96654f75542cc9a5
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098962"
---
# <a name="cloud-tiering-overview"></a>Omówienie obsługi warstw w chmurze
Obsługa warstw w chmurze jest opcjonalną funkcją Azure File Sync, w której często używane pliki są buforowane lokalnie na serwerze, podczas gdy wszystkie inne pliki są warstwami do Azure Files na podstawie ustawień zasad. Gdy plik jest warstwowy, filtr systemu plików Azure File Sync (StorageSync. sys) zastępuje plik lokalnie za pomocą wskaźnika lub punktu ponownej analizy. Punkt ponownej analizy reprezentuje adres URL pliku w Azure Files. Plik warstwowy ma zarówno atrybut "offline", jak i atrybut FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS ustawiony w systemie plików NTFS, aby aplikacje innych firm mogły bezpiecznie identyfikować pliki warstwowe.
 
Gdy użytkownik otwiera plik warstwowy, Azure File Sync bezproblemowo oddzwoni dane pliku od Azure Files bez konieczności znajomości, że plik jest rzeczywiście przechowywany na platformie Azure. 
 
 > [!Important]  
 > Obsługa warstw w chmurze nie jest obsługiwana w przypadku punktów końcowych serwera w woluminach systemu Windows, a tylko pliki o rozmiarze większym niż 64 KiB mogą być warstwami dla Azure Files.
    
Azure File Sync nie obsługuje plików warstwowych mniejszych niż 64 KiB, ponieważ obciążenie wydajnością warstwowe i wywoływanie takich małych plików spowodowałoby zmniejszenie ilości miejsca.

 > [!Important]  
 > Aby można było odwołać pliki, które zostały warstwowe, przepustowość sieci powinna wynosić co najmniej 1 MB/s. Jeśli przepustowość sieci jest mniejsza niż 1 MB/s, pliki mogą zakończyć się niepowodzeniem z błędem przekroczenia limitu czasu.

## <a name="cloud-tiering-faq"></a>Obsługa warstw w chmurze — często zadawane pytania

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Jak działa Obsługa warstw w chmurze?
Filtr systemu Azure File Sync kompiluje "mapę cieplną" przestrzeni nazw na każdym punkcie końcowym serwera. Monitoruje dostęp (operacje odczytu i zapisu) w miarę upływu czasu, a następnie, w zależności od częstotliwości i recency dostępu, przypisuje wynik ciepła do każdego pliku. Plik, który był ostatnio otwarty, jest uznawany za gorącą, podczas gdy plik, który jest stanowią jedynie ułamek i nie jest dostępny przez jakiś czas, będzie uznawany za chłodną. Gdy wolumin plików na serwerze przekracza ustawiony próg ilości wolnego miejsca na woluminie, zostanie podwyższony poziom największej liczby plików do Azure Files, dopóki nie zostanie osiągnięty procent wolnego miejsca.

W wersji 4,0 i nowszych Azure File Sync Agent można dodatkowo określić zasady dotyczące dat dla każdego punktu końcowego serwera, który będzie warstwować wszystkie pliki, które nie są dostępne ani modyfikowane w ciągu określonej liczby dni.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Jak działają zasady warstw wolnego miejsca na woluminie?
Wolne miejsce w woluminie to ilość wolnego miejsca, które chcesz zarezerwować na woluminie, na którym znajduje się punkt końcowy serwera. Na przykład jeśli ilość wolnego miejsca na woluminie jest ustawiona na 20% na woluminie z jednym punktem końcowym serwera, do 80% miejsca na woluminie zostaną zajęte ostatnio używane pliki, z wszelkimi innymi plikami, które nie mieszczą się w tym miejscu na platformie Azure. Wolne miejsce na woluminie jest stosowane na poziomie woluminu, a nie na poziomie poszczególnych katalogów lub grup synchronizacji. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Jak działają zasady warstw wolnego miejsca na woluminie w odniesieniu do nowych punktów końcowych serwera?
Gdy punkt końcowy serwera jest nowo zainicjowany i połączony z udziałem plików platformy Azure, serwer najpierw pobierze przestrzeń nazw, a następnie pobierze rzeczywiste pliki do momentu, aż osiągnie próg wolnego miejsca na woluminie. Ten proces jest również znany jako szybkie odzyskiwanie po awarii lub szybkie przywracanie przestrzeni nazw.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Jak jest interpretowane wolne miejsce na woluminie, gdy mam wiele punktów końcowych serwera na woluminie?
Jeśli na woluminie znajduje się więcej niż jeden punkt końcowy serwera, próg ilości wolnego miejsca na woluminie jest największą ilością wolnego miejsca określonego w dowolnym punkcie końcowym serwera. Pliki będą warstwowo zgodne z wzorcem użycia, niezależnie od tego, który punkt końcowy serwera, do którego należą. Na przykład jeśli masz dwa punkty końcowe serwera w woluminie, Endpoint1 i Endpoint2, gdzie Endpoint1 ma próg wolnego miejsca na woluminie równy 25%, a Endpoint2 ma próg wolnego miejsca na woluminie równy 50%, próg wolnego miejsca na woluminie dla obu punktów końcowych serwera będzie 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Jak zasady dotyczące warstw dat działają w połączeniu z zasadami warstw wolnego miejsca na woluminie? 
Podczas włączania obsługi warstw w chmurze w punkcie końcowym serwera ustawia się zasady wolnego miejsca na woluminie. Zawsze ma pierwszeństwo przed innymi zasadami, w tym zasadami daty. Opcjonalnie można włączyć zasady daty dla każdego punktu końcowego serwera w tym woluminie, co oznacza, że tylko pliki, do których są dostępne (czyli odczytane lub zapisywana), w zakresie dni, w których te zasady opisano, będą przechowywane lokalnie, a wszystkie pliki przestarzałych warstw. Należy pamiętać, że zasady wolnego miejsca na woluminie zawsze mają pierwszeństwo, a w przypadku braku wystarczającej ilości wolnego miejsca na woluminie, aby zachować tyle dni, ile jest w tym samym pliku, jak opisano w zasadach dotyczących dat, Azure File Sync będzie kontynuować obsługę warstw plików Coldest do momentu zwolnienia woluminu osiągnięto procent przestrzeni.

Załóżmy na przykład, że masz zasady warstwowe oparte na dacie wynoszące 60 dni i zasady wolnego miejsca na woluminie o wartości 20%. Jeśli po zastosowaniu zasad dotyczących dat jest mniej niż 20% wolnego miejsca na woluminie, zasady wolnego miejsca na woluminie zostaną rozpoczęte i zastąpią zasady daty. Spowoduje to zwiększenie warstw plików, dzięki czemu ilość danych przechowywanych na serwerze może ulec zmniejszeniu z 60 dni danych do 45 dni. Z kolei te zasady wymuszą przedziały warstw plików, które znajdują się poza zakresem czasu, nawet jeśli nie osiągnięto progu wolnego miejsca — dlatego plik o 61 dni starszej wersji będzie warstwowy nawet wtedy, gdy wolumin jest pusty.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Jak mogę określić odpowiednią ilość wolnego miejsca na woluminie?
Ilość danych, które powinny być przechowywane lokalnie, zależy od kilku czynników: przepustowości, wzorca dostępu zestawu danych i budżetu. Jeśli masz połączenie o niskiej przepustowości, możesz chcieć zachować więcej danych lokalnych, aby zapewnić użytkownikom minimalne opóźnienia. W przeciwnym razie można oprzeć ją na współczynniku zmian w danym okresie. Jeśli na przykład wiesz, że około 10% zmian zestawu danych o pojemności 1 TB lub jest aktywnie dostępna w każdym miesiącu, możesz chcieć zachować lokalne 100 GB, aby nie często odwoływać plików. Jeśli wolumin jest 2 TB, należy pozostawić wartość 5% (lub 100 GB) na komputerze lokalnym, co oznacza, że pozostała liczba 95% to ilość wolnego miejsca na woluminie. Zaleca się jednak dodanie buforu do konta w przypadku okresów o większej liczbie zmian — innymi słowy, zaczynając od mniejszej ilości wolnego miejsca na woluminie, a następnie dostosowując go w razie potrzeby później. 

Utrzymywanie większej ilości danych może prowadzić do obniżenia kosztów ruchu wychodzącego, ponieważ mniejsza liczba plików zostanie odłączona od platformy Azure, ale wymagane jest również, aby zachować większą ilość magazynu lokalnego, który ma własny koszt. Po wdrożeniu wystąpienia Azure File Sync można przyjrzeć się wychodzącemu kontu magazynu, aby określić, czy ustawienia wolnego miejsca na woluminie są odpowiednie do użycia. Założenie, że konto magazynu zawiera tylko Azure File Sync punkt końcowy w chmurze (czyli udział synchronizacji), a następnie wysoki ruch wychodzący oznacza, że wiele plików jest wywoływanych z chmury i należy rozważyć zwiększenie lokalnej pamięci podręcznej.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Dodaliśmy nowy punkt końcowy serwera. Jak długo do moich plików w tej warstwie serwera?
W wersji 4,0 i większej od agenta Azure File Sync, gdy pliki zostały przekazane do udziału plików platformy Azure, zostaną warstwowo zgodnie z zasadami, gdy tylko zostanie uruchomiona kolejna sesja warstwowa, co jest wykonywane raz na godzinę. W przypadku starszych agentów może upłynąć nawet 24 godziny.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Jak mogę sprawdzić, czy plik został warstwowy?
Istnieje kilka sposobów, aby sprawdzić, czy plik został warstwowy w udziale plików platformy Azure:
    
   *  **Sprawdź atrybuty pliku w pliku.**
     Kliknij prawym przyciskiem myszy plik, przejdź do **szczegółów**, a następnie przewiń w dół do właściwości **atrybuty** . Plik warstwowy ma ustawione następujące atrybuty:     
        
        | Litera atrybutu | Atrybut | Definicja |
        |:----------------:|-----------|------------|
        | A | Archiwum | Wskazuje, że należy utworzyć kopię zapasową pliku przez oprogramowanie kopii zapasowej. Ten atrybut jest zawsze ustawiany, niezależnie od tego, czy plik jest warstwowy czy przechowywany w całości na dysku. |
        | P | Plik rozrzedzony | Wskazuje, że plik jest plikiem rozrzedzonym. Plik rozrzedzony to wyspecjalizowany typ pliku, który oferuje system plików NTFS do wydajnego użycia, gdy plik w strumieniu dysku jest w większości pusty. Azure File Sync używa plików rozrzedzonych, ponieważ plik jest w pełni warstwowy lub częściowo ponownie wywoływany. W przypadku w pełni warstwowego pliku strumień plików jest przechowywany w chmurze. W częściowo odwywoływanym pliku, ta część pliku znajduje się już na dysku. Jeśli plik jest w pełni wywoływany na dysk, Azure File Sync konwertuje go z pliku rozrzedzonego na zwykły plik. |
        | L | Punkt ponownej analizy | Wskazuje, że plik ma punkt ponownej analizy. Punkt ponownej analizy to specjalny wskaźnik używany przez filtr systemu plików. Azure File Sync używa punktów ponownej analizy do zdefiniowania do Azure File Sync filtru systemu plików (StorageSync. sys) lokalizacji w chmurze, w której jest przechowywany plik. Zapewnia to bezproblemowe dostęp. Użytkownicy nie muszą wiedzieć, że Azure File Sync są używane, lub jak uzyskać dostęp do pliku w udziale plików platformy Azure. Gdy plik jest w pełni wywoływany, Azure File Sync usuwa punkt ponownej analizy z pliku. |
        | O | W trybie offline | Wskazuje, że część lub cała zawartość pliku nie jest przechowywana na dysku. Gdy plik jest w pełni wywoływany, Azure File Sync usuwa ten atrybut. |

        ![Okno dialogowe właściwości dla pliku, z wybraną kartą szczegóły](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Możesz zobaczyć atrybuty dla wszystkich plików w folderze, dodając pole **atrybuty** do wyświetlania tabeli Eksploratora plików. Aby to zrobić, kliknij prawym przyciskiem myszy istniejącą kolumnę (na przykład **rozmiar**), wybierz pozycję **więcej**, a następnie z listy rozwijanej wybierz pozycję **atrybuty** .
        
   * **Służy `fsutil` do sprawdzania punktów ponownej analizy pliku.**
       Zgodnie z opisem w poprzedniej opcji, plik warstwowy zawsze ma zestaw punktów ponownej analizy. Wskaźnik ponownej analizy jest specjalnym wskaźnikiem filtru systemu plików Azure File Sync (StorageSync. sys). Aby sprawdzić, czy plik ma punkty ponownej analizy, w wierszu polecenia z podwyższonym poziomem uprawnień lub w oknie `fsutil` programu PowerShell uruchom narzędzie:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Jeśli plik ma punkty ponownej analizy, można oczekiwać, że zostanie wyświetlona **wartość tagu ponownej analizy: 0x8000001E**. Ta wartość szesnastkowa to wartość punktu ponownej analizy, która jest własnością Azure File Sync. Dane wyjściowe zawierają również dane dotyczące ponownej analizy, które reprezentują ścieżkę do pliku w udziale plików platformy Azure.

        > [!WARNING]  
        > Polecenie `fsutil reparsepoint` narzędzia ma także możliwość usunięcia punktu ponownej analizy. Nie wykonuj tego polecenia, chyba że zespół inżynierów Azure File Sync prosi o. Uruchomienie tego polecenia może spowodować utratę danych. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Plik, którego chcę użyć, został warstwowy. Jak można przywołać plik na dysk, aby użyć go lokalnie?
Najprostszym sposobem odwoływania pliku na dysk jest otwarcie pliku. Filtr systemu plików Azure File Sync (StorageSync. sys) bezproblemowo pobiera plik z udziału plików platformy Azure bez żadnej pracy z Twoją częścią. W przypadku typów plików, które mogą być częściowo odczytywane z, takich jak pliki multimedialne lub zip, otwarcie pliku nie powoduje pobrania całego pliku.

Można też użyć programu PowerShell, aby wymusić oddzwonienie pliku. Ta opcja może być przydatna, jeśli chcesz odwołać wiele plików jednocześnie, takich jak wszystkie pliki w folderze. Otwórz sesję programu PowerShell w węźle serwera, na którym zainstalowano Azure File Sync, a następnie uruchom następujące polecenia programu PowerShell:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
```

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Dlaczego rozmiar właściwości *dysku* dla pliku jest zgodny z właściwością *size* po użyciu Azure File Sync? 
Eksplorator plików systemu Windows udostępnia dwie właściwości, aby reprezentować rozmiar pliku: **Rozmiar** i **rozmiar na dysku**. Te właściwości różnią się w znaczenie. **Rozmiar** reprezentuje całkowity rozmiar pliku. **Rozmiar na dysku** reprezentuje rozmiar strumienia pliku przechowywanego na dysku. Wartości tych właściwości mogą się różnić z różnych powodów, takich jak kompresja, korzystanie z deduplikacji danych lub Obsługa warstw w chmurze przy użyciu Azure File Sync. Jeśli plik jest warstwą udziału plików platformy Azure, rozmiar na dysku wynosi zero, ponieważ strumień plików jest przechowywany w udziale plików platformy Azure, a nie na dysku. Istnieje również możliwość, że plik jest częściowo warstwowy (lub częściowo odwołujący). W pliku częściowo warstwowego część pliku znajduje się na dysku. Taka sytuacja może wystąpić, gdy pliki są częściowo odczytywane przez aplikacje, takie jak odtwarzacze multimedialne lub narzędzia zip. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Jak mogę wymusić przeprowadzenie warstwowego pliku lub katalogu?
Po włączeniu funkcji obsługi warstw w chmurze usługa Cloud warstws automatycznie warstwuje pliki na podstawie ostatniego dostępu i modyfikuje czasy, aby osiągnąć procent wolnego miejsca na woluminie określony w punkcie końcowym w chmurze. Czasami może być konieczne ręczne wymuszenie pliku w warstwie. Może to być przydatne, jeśli zapiszesz duży plik, którego nie zamierzasz ponownie używać przez dłuższy czas, i chcesz, aby wolne miejsce na woluminie było teraz używane dla innych plików i folderów. Można wymusić obsługę warstw przy użyciu następujących poleceń programu PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="next-steps"></a>Następne kroki
* [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md)
