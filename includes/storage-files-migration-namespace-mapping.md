---
title: Mapowanie struktury folderów do topologii synchronizacji plików platformy Azure
description: Mapowanie istniejącej struktury plików i folderów do udziałów plików platformy Azure do użytku z synchronizacją plików platformy Azure. Wspólny blok tekstu, współużytkowane przez dokumenty migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 25c333aae49bff8d0596d4f5403c18576bf198b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124727"
---
W tym kroku oceniasz, ile udziałów plików platformy Azure jest potrzebnych. Pojedynczy system Windows Server (lub klaster) można zsynchronizować maksymalnie 30 udziałów plików platformy Azure.

Na woluminach może znajdować się więcej folderów, które są obecnie udostępniane lokalnie jako udziały SMB użytkownikom i aplikacjom. Najłatwiej byłoby wyobrazić sobie udział lokalny do mapowania 1:1 do udziału plików platformy Azure. Jeśli masz wystarczająco małą liczbę, poniżej 30 dla jednego systemu Windows Server, zaleca się mapowanie 1:1.

Jeśli masz więcej udziałów niż 30, często nie jest konieczne mapowanie lokalnego udziału 1:1 do udziału plików platformy Azure.
Rozważmy następujące opcje:

#### <a name="share-grouping"></a>Grupowanie udziałów

Na przykład jeśli dział HR ma łącznie 15 udziałów, można rozważyć przechowywanie wszystkich danych hr w jednym udziale plików platformy Azure. Przechowywanie wielu udziałów lokalnych w jednym udziale plików platformy Azure nie uniemożliwia tworzenia zwykłych udziałów 15 SMB w lokalnym systemie Windows Server. Oznacza to tylko, że organizujesz foldery główne tych 15 udziałów jako podfoldery w folderze wspólnym. Następnie zsynchronizować ten wspólny folder z udziałem plików platformy Azure. W ten sposób tylko jeden udział plików platformy Azure w chmurze jest potrzebny dla tej grupy udziałów lokalnych.

#### <a name="volume-sync"></a>Synchronizacja woluminów

Usługa Azure File Sync obsługuje synchronizowanie katalogu głównego woluminu z udziałem plików platformy Azure.
Jeśli zsynchronizujesz folder główny, wszystkie podfoldery i pliki znajdą się w tym samym udziale plików platformy Azure.

Synchronizacja katalogu głównego woluminu nie zawsze będzie najlepszą odpowiedzią. Synchronizowanie wielu lokalizacji ma takie zalety, dzięki czemu liczba elementów jest niższa na zakres synchronizacji. Konfigurowanie synchronizacji plików platformy Azure z mniejszą liczbą elementów jest nie tylko korzystne dla synchronizacji plików. Mniejsza liczba elementów przynosi również korzyści innym scenariuszom, takim jak:

* przywracanie po stronie chmury z migawki udziału plików platformy Azure wykonanej jako kopia zapasowa
* odzyskiwanie po awarii serwera lokalnego może znacznie przyspieszyć
* zmiany wprowadzone bezpośrednio w udziale plików platformy Azure (poza synchronizacją) można wykryć i szybciej zsynchronizować

#### <a name="a-structured-approach-to-a-deployment-map"></a>Ustrukturyzowane podejście do mapy wdrażania

Przed wdrożeniem magazynu w chmurze w następnym kroku należy utworzyć mapę między folderami lokalnymi a udziałami plików platformy Azure. To mapowanie będzie następnie informuje, ile i które zasoby "grupy synchronizacji" usługi Azure File Sync zostaną aprowizowi. Grupa synchronizacji łączy udział plików platformy Azure i folder na serwerze i ustanawia połączenie synchronizacji.

Aby podjąć decyzję o tym, ile udziałów plików platformy Azure jest potrzebnych, należy zapoznać się z następującymi ograniczeniami i najlepszymi rozwiązaniami. Pomoże ci to zoptymalizować mapę:

* Serwer z zainstalowanym agentem usługi Azure File Sync może synchronizować z maksymalnie 30 udziałami plików platformy Azure.
* Udział plików platformy Azure jest wdrażany wewnątrz konta magazynu. Dzięki temu konto magazynu jest celem skali dla numerów wydajności, takich jak We/Wy i przepływność. Dwa standardowe (nie premium) udziały plików platformy Azure teoretycznie mogą nasycić maksymalną wydajność, jaką może zapewnić konto magazynu. Jeśli planujesz dołączyć tylko usługę Azure File Sync do tych udziałów plików, grupowanie kilku udziałów plików platformy Azure na tym samym koncie magazynu nie powoduje problemu. Przejrzyj cele wydajności udziału plików platformy Azure, aby uzyskać lepszy wgląd w odpowiednie metryki, które należy wziąć pod uwagę. Jeśli planujesz podnoszenia aplikacji na platformę Azure, która będzie używać udziału plików platformy Azure natywnie, a następnie może być konieczne więcej wydajności z udziału plików platformy Azure. Jeśli jest to możliwe, nawet w przyszłości, najlepiej jest mapować udział plików platformy Azure na własne konto magazynu.
* Istnieje limit 250 kont magazynu na subskrypcję w jednym regionie platformy Azure.

> [!TIP]
> Mając na uwadze te informacje, często konieczne staje się pogrupowanie wielu folderów najwyższego poziomu na woluminach w wspólny, nowy katalog główny. Następnie zsynchronizujesz ten nowy katalog główny i wszystkie foldery zgrupowane w nim z jednym udziałem plików platformy Azure.                                                    

Ta technika pozwala pozostać w granicach limitu synchronizacji 30 udziałów plików platformy Azure na serwerze.
To grupowanie w typowym katalogu głównym nie ma wpływu na dostęp do danych. Listy ACL pozostają takie, jak jest, wystarczy tylko dostosować wszystkie ścieżki udziału (takie jak udziały SMB lub NFS) w folderach serwera, które zostały teraz zmienione na wspólny katalog główny. Nic się nie zmienia.

Innym ważnym aspektem synchronizacji plików platformy Azure i zrównoważonej wydajności i środowiska jest zrozumienie czynników skalowania wydajności synchronizacji plików platformy Azure. Oczywiście, gdy pliki są synchronizowane przez Internet, większe pliki wymagają więcej czasu i przepustowości, aby zsynchronizować.

> [!IMPORTANT]
> Najważniejszym wektorem skali dla synchronizacji plików platformy Azure jest liczba elementów (plików i folderów), które muszą zostać zsynchronizowane.

Usługa Azure File Sync obsługuje synchronizację do 100 000 elementów z jednym udziałem plików platformy Azure. Ten limit można przekroczona i tylko przedstawia, co zespół usługi Azure File Sync testuje regularnie.

Jest najlepszym rozwiązaniem, aby utrzymać niską liczbę elementów na zakres synchronizacji. Ten aspekt jest ważnym czynnikiem, który należy wziąć pod uwagę w mapowaniu folderów do udziałów plików platformy Azure.

Nawet jeśli w sytuacji zestaw folderów można logicznie synchronizować z tym samym udziałem plików platformy Azure (przy użyciu nowego, wspólnego podejścia do folderów głównych z góry) może być jeszcze lepiej przegrupować foldery, takie, że są synchronizowane z dwoma zamiast jednego udziału plików platformy Azure. Takie podejście może służyć do utrzymania liczby plików i folderów na udział plików zrównoważony na serwerze.

#### <a name="create-a-mapping-table"></a>Tworzenie tabeli mapowania

:::row:::
    :::column:::
        [![](media/storage-files-migration-namespace-mapping/namespace-mapping.png "An example of a mapping table. Download the file below to experience and use the content of this image.")](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Użyj kombinacji poprzednich koncepcji, aby określić, ile udziałów plików platformy Azure jest potrzebnych i które części istniejących danych zakończy się, w którym udział plików platformy Azure.
        
        Utwórz tabelę, która rejestruje twoje myśli, dzięki czemu możesz się do niej odwołać w następnym kroku. Zachowanie porządku jest ważne, ponieważ może być łatwo utracić szczegóły planu mapowania podczas inicjowania obsługi administracyjnej wielu zasobów platformy Azure naraz. Aby ułatwić tworzenie pełnego mapowania, można pobrać plik programu Microsoft Excel jako szablon.

[//]: # (Html jest wyświetlany jako jedyny sposób dodawania zagnieżdżonej tabeli dwukolumnowej z analizowaniem obrazu roboczego i tekstem/hiperłączem w tym samym wierszu.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">Pobierz szablon mapowania obszaru nazw.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
