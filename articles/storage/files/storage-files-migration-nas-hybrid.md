---
title: Lokalna migracja serwera NAS do usługi Azure File Sync
description: Dowiedz się, jak migrować pliki z lokalnej lokalizacji sieciowego magazynu dołączonego do sieci (NAS) do wdrożenia chmury hybrydowej za pomocą usługi Azure File Sync i udziałów plików platformy Azure.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7b0c7a30580d3863a78e85b8b45287a598bbf394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247354"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migracja z sieciowej pamięci masowej (NAS) do wdrożenia chmury hybrydowej za pomocą usługi Azure File Sync

Usługa Azure File Sync działa w lokalizacjach magazynu bezpośredniego dołączonego (DAS) i nie obsługuje synchronizacji z lokalizacjami sieciowego magazynu (NAS).
Fakt ten sprawia, że migracja plików jest niezbędna, a ten artykuł prowadzi użytkownika przez planowanie i wykonywanie takiej migracji.

## <a name="migration-goals"></a>Cele migracji

Celem jest przeniesienie udziałów posiadane na urządzeniu NAS do systemu Windows Server. Następnie skorzystaj z usługi Azure File Sync do wdrożenia w chmurze hybrydowej. Ta migracja musi być wykonana w sposób, który gwarantuje integralność danych produkcyjnych, a także dostępność podczas migracji. Ten ostatni wymaga utrzymania przestojów do minimum, tak aby mógł zmieścić się w lub tylko nieznacznie przekroczyć regularne okna konserwacji.

## <a name="migration-overview"></a>Przegląd migracji

Jak wspomniano w [artykule omówienie migracji](storage-files-migration-overview.md)plików azure , przy użyciu narzędzia do kopiowania i podejście jest ważne. Urządzenie NAS udostępnia udziały SMB bezpośrednio w sieci lokalnej. RoboCopy, wbudowany w system Windows Server, to najlepszy sposób przenoszenia plików w tym scenariuszu migracji.

- Faza 1: [Określ, ile potrzebnych udziałów plików platformy Azure](#phase-1-identify-how-many-azure-file-shares-you-need)
- Faza 2: [Udostępnienie odpowiedniego systemu Windows Server lokalnie](#phase-2-provision-a-suitable-windows-server-on-premises)
- Faza 3: [Wdrażanie zasobu chmury synchronizacji plików platformy Azure](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- Faza 4: [Wdrażanie zasobów magazynu platformy Azure](#phase-4-deploy-azure-storage-resources)
- Faza 5: [Wdrażanie agenta synchronizacji plików platformy Azure](#phase-5-deploy-the-azure-file-sync-agent)
- Faza 6: [Konfigurowanie synchronizacji plików platformy Azure na serwerze Windows Server](#phase-6-configure-azure-file-sync-on-the-windows-server)
- Faza 7: [RoboCopy](#phase-7-robocopy)
- Faza 8: [Przecięcie użytkownika](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Faza 1: Określ, ile potrzebnych udziałów plików platformy Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>Faza 2: Udostępnienie odpowiedniego systemu Windows Server lokalnie

* Utwórz system Windows Server 2019 — co najmniej 2012R2 — jako maszynę wirtualną lub serwer fizyczny. Obsługiwany jest również klaster trybu failover systemu Windows Server.
* Aprowizuj lub dodaj bezpośrednio podłączoną pamięć masową (DAS w porównaniu z serwerem NAS, który nie jest obsługiwany).

    Ilość udostępninej przestrzeni dyskowej może być mniejsza niż obecnie używana na urządzeniu NAS, jeśli korzystasz z funkcji [warstwowej chmury](storage-sync-cloud-tiering.md) synchronizacji plików azure.
    Jednak podczas kopiowania plików z większego miejsca na serwer NAS do mniejszego woluminu systemu Windows Server w późniejszej fazie należy pracować partiami:

    1. Przenoszenie zestawu plików, które zmieszczą się na dysku
    2. niech synchronizacja plików i tworzenie warstw w chmurze
    3. gdy na woluminie zostanie utworzonych więcej wolnego miejsca, przejdź do następnej partii plików. 
    
    Można uniknąć tego podejścia do przetwarzania wsadowego, inicjując inicjowanie obsługi administracyjnej równoważne miejsce w systemie Windows Server, które pliki zajmują na urządzeniu NAS. Rozważ deduplikację na nas / Windows. Jeśli nie chcesz trwale zatwierdzić tej dużej ilości miejsca do systemu Windows Server, możesz zmniejszyć rozmiar woluminu po migracji i przed dostosowaniem zasad warstwowych w chmurze. W ten sposób powstaje mniejsza lokalna pamięć podręczna udziałów plików platformy Azure.

Konfiguracja zasobów (obliczeniowa i ram) wdrażanego systemu Windows Server zależy głównie od liczby elementów (plików i folderów), które będą synchronizowane. Jeśli masz jakiekolwiek wątpliwości, zalecamy przejście z konfiguracją o wyższej wydajności.

[Dowiedz się, jak rozmiar systemu Windows Server na podstawie liczby elementów (plików i folderów) potrzebnych do synchronizacji.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Wcześniej połączony artykuł przedstawia tabelę z zakresem pamięci serwera (RAM). Można orientować się w kierunku mniejszej liczby dla serwera, ale spodziewać się, że początkowa synchronizacja może zająć znacznie więcej czasu.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Faza 3: Wdrażanie zasobu chmury synchronizacji plików platformy Azure

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Faza 4: Wdrażanie zasobów magazynu platformy Azure

W tej fazie zapoznaj się z tabelą mapowania z fazy 1 i użyj jej do aprowizowania poprawnej liczby kont magazynu platformy Azure i udziałów plików w nich.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Faza 5: Wdrażanie agenta synchronizacji plików platformy Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>Faza 6: Konfigurowanie synchronizacji plików platformy Azure na serwerze Windows Server

Zarejestrowany lokalny system Windows Server musi być gotowy i połączony z Internetem w tym procesie.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Warstwa chmury to funkcja AFS, która umożliwia serwerowi lokalnemu mniejszą pojemność niż jest przechowywana w chmurze, ale ma dostęp do pełnego obszaru nazw. Lokalnie interesujące dane są również buforowane lokalnie w celu uzyskania szybkiej wydajności dostępu. Warstwa w chmurze jest opcjonalną funkcją dla "punktu końcowego serwera" synchronizacji plików platformy Azure.

> [!WARNING]
> Jeśli na woluminach serwera Windows serwera jest większa ilość miejsca niż dane używane na urządzeniu NAS, warstwa w chmurze jest obowiązkowa. Jeśli nie włączysz warstwy chmurowej, serwer nie zwalnia miejsca na przechowywanie wszystkich plików. Ustaw zasady warstwowe, tymczasowo dla migracji, na 99% wolnego miejsca na woluminie. Pamiętaj, aby powrócić do ustawień warstwowych w chmurze po zakończeniu migracji i ustawić go na bardziej długoterminowym poziomie użytecznym.

Powtórz kroki tworzenia grupy synchronizacji i dodawanie pasującego folderu serwera jako punktu końcowego serwera dla wszystkich udziałów plików platformy Azure/ lokalizacji serwera, które muszą być skonfigurowane do synchronizacji.

Po utworzeniu wszystkich punktów końcowych serwera synchronizacja działa. Można utworzyć plik testowy i wyświetlić go zsynchronizować z lokalizacji serwera do połączonego udziału plików platformy Azure (zgodnie z opisem punktu końcowego chmury w grupie synchronizacji).

Obie lokalizacje, foldery serwera i udziały plików platformy Azure są w przeciwnym razie puste i oczekują na dane w obu lokalizacjach. W następnym kroku rozpoczniesz kopiowanie plików do systemu Windows Server for Azure File Sync, aby przenieść je do chmury. W przypadku włączenia warstw w chmurze serwer rozpocznie warstwę plików, jeśli zabraknie pojemności na woluminach lokalnych.

## <a name="phase-7-robocopy"></a>Faza 7: RoboCopy

Podstawowym podejściem do migracji jest RoboCopy z urządzenia NAS do windows server i udziałów plików Usługi Azure File Sync do platformy Azure.

Uruchom pierwszą kopię lokalną do folderu docelowego systemu Windows Server:

* Zidentyfikuj pierwszą lokalizację urządzenia NAS.
* Zidentyfikuj pasujący folder w systemie Windows Server, który ma już skonfigurowaną usługę Azure File Sync.
* Rozpocznij kopiowanie za pomocą robocopy

Następujące polecenie RoboCopy skopiuje pliki z magazynu NAS do folderu docelowego systemu Windows Server. System Windows Server zsynchronizuje go z udziałami plików platformy Azure. 

Jeśli w systemie Windows Server aprowizacji udostępnisz mniej miejsca niż pliki zajmowane przez urządzenie NAS, skonfigurowano warstwowanie w chmurze. W miarę zapełniania lokalnego woluminu systemu Windows Server [warstwowe warstwy](storage-sync-cloud-tiering.md) w chmurze i pliki warstwy, które zostały pomyślnie zsynchronizowane. Warstwowe chmury wygeneruje wystarczająco dużo miejsca, aby kontynuować kopię z urządzenia NAS. Warstwowa chmura sprawdza raz na godzinę, aby zobaczyć, co zostało zsynchronizowane i zwolnić miejsce na dysku, aby osiągnąć 99% wolnego miejsca na woluminie.
Jest możliwe, że RoboCopy przenosi pliki szybciej niż można synchronizować z chmurą i warstwą lokalnie, w ten sposób zabraknie miejsca na dysku lokalnym. RoboCopy zakończy się niepowodzeniem. Zaleca się, aby pracować za pośrednictwem udziałów w sekwencji, która zapobiega. Na przykład nie uruchamianie robocopy zadań dla wszystkich udziałów w tym samym czasie lub tylko przenoszenie udziałów, które mieszczą się na bieżącej ilości wolnego miejsca w systemie Windows Server, aby wspomnieć o kilku.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Tle:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Pozwala roboCopy do uruchamiania wielowątkowych. Wartość domyślna to 8, max to 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:\<nazwa pliku\>
   :::column-end:::
   :::column span="1":::
      Dane wyjściowe do pliku LOG jako UNICODE (zastępuje istniejący dziennik).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Wyjścia do okna konsoli. Używany w połączeniu z wyjściem do pliku dziennika.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Uruchamia RoboCopy w tym samym trybie, którego użyłaby aplikacja do tworzenia kopii zapasowych. Umożliwia RoboCopy przenoszenie plików, do których bieżący użytkownik nie ma uprawnień.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Pozwala uruchomić to polecenie RoboCopy kilka razy, sekwencyjnie na tym samym celu / miejscu docelowym. Identyfikuje to, co zostało skopiowane wcześniej i pomija je. Przetwarzane będą tylko zmiany, dodatki i " deletes ", które*wystąpiły*od ostatniego uruchomienia. Jeśli polecenie nie zostało uruchomione wcześniej, nic nie zostanie pominięte. Flaga */MIR* jest doskonałą opcją dla lokalizacji źródłowych, które są nadal aktywnie używane i zmieniane.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      wierność kopii pliku (domyślnie jest to /COPY:DAT), flagi kopiowania: D=Data, A=Attributes, T=Timestamps, S=Security=NTFS ACL, O=Informacje o właścicielu, U=aUditing info
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      KOPIUJ WSZYSTKIE informacje o pliku (odpowiednik /COPY:DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      wierność kopii katalogów (domyślnie jest to /DCOPY:DA), flagi kopiowania: D=Data, A=Attributes, T=Sygnatury czasowe
   :::column-end:::
:::row-end:::

## <a name="phase-8-user-cut-over"></a>Faza 8: Przecięcie użytkownika

Po uruchomieniu polecenia RoboCopy po raz pierwszy użytkownicy i aplikacje nadal uzyskują dostęp do plików na serwerze NAS i potencjalnie je zmieniają. Jest możliwe, że RoboCopy przetworzył katalog, przechodzi do następnego, a następnie użytkownik w lokalizacji źródłowej (NAS) dodaje, zmienia lub usuwa plik, który nie będzie teraz przetwarzany w tym bieżącym uruchomieniu RoboCopy. Takie zachowanie jest oczekiwane.

Pierwsze uruchomienie dotyczy przenoszenia większości danych do systemu Windows Server i do chmury za pośrednictwem usługi Azure File Sync. Ta pierwsza kopia może zająć dużo czasu, w zależności od:

* przepustowość pobierania
* przepustowość przesyłania
* szybkość sieci lokalnej i liczbę optymalnie dopasowanych wątków RoboCopy
* liczba elementów (plików i folderów), które muszą być przetwarzane przez RoboCopy i Azure File Sync

Po zakończeniu początkowego uruchomienia uruchom polecenie ponownie.

Za drugim razem zakończy się szybciej, ponieważ musi tylko transportować zmiany, które miały miejsce od ostatniego uruchomienia. Podczas tego drugiego biegu, nadal, nowe zmiany mogą gromadzić.

Powtarzaj ten proces, dopóki nie upewnisz się, że czas potrzebny do ukończenia RoboCopy dla określonej lokalizacji mieści się w dopuszczalnym oknie dla przestojów.

Jeśli wziąć pod uwagę czas przestoju dopuszczalne i jesteś przygotowany do podjęcia lokalizacji NAS w trybie offline: W celu podjęcia dostępu użytkownika w trybie offline, masz możliwość zmiany list ACL w katalogu głównym udziału, tak aby użytkownicy nie mogą już uzyskać dostępu do lokalizacji lub podjąć inne odpowiednie kroki zapobiega zmianie zawartości w tym folderze na serwerze NAS.

Uruchom ostatnią rundę RoboCopy. Będzie odebrać wszelkie zmiany, które mogły zostać pominięte.
Jak długo trwa ten ostatni krok, zależy od szybkości skanowania RoboCopy. Możesz oszacować czas (który jest równy przestojowi), mierząc czas poprzedniego biegu.

Utwórz udział w folderze Windows Server i ewentualnie dostosuj wdrożenie systemu DFS-N, aby go wskazać. Pamiętaj, aby ustawić te same uprawnienia na poziomie udziału, co w udziale SMB NAS. Jeśli serwer NAS przyłączony do domeny klasy korporacyjnej został automatycznie dopasowany do identyfikatorów SID użytkowników, ponieważ użytkownicy w usłudze Active Directory i RoboCopy kopiują pliki i metadane z pełną wiernością. Jeśli na serwerze NAS korzystano z użytkowników lokalnych, należy ponownie utworzyć tych użytkowników jako użytkowników lokalnych systemu Windows Server i zamapować istniejące identyfikatory SID RoboCopy przeniesione na system Windows Server na identyfikatory SID nowych użytkowników lokalnych systemu Windows Server.

Migracja akcji/grupy udziałów została zakończona do wspólnego katalogu głównego lub woluminu. (W zależności od mapowania z fazy 1)

Możesz spróbować uruchomić kilka z tych kopii równolegle. Firma Microsoft zaleca przetwarzanie zakresu jednego udziału plików platformy Azure w czasie.

> [!WARNING]
> Po przeniesieniu wszystkich danych z serwera NAS do systemu Windows Server i zakończeniu migracji: Powrót do ***wszystkich*** grup synchronizacji w witrynie Azure portal i dostosowanie wartości procentu wolnego miejsca w warstwie w chmurze do czegoś lepiej dostosowanego do wykorzystania pamięci podręcznej, powiedzmy 20%. 

Zasady wolnego miejsca woluminu warstwowego w chmurze działają na poziomie woluminu z potencjalnie wieloma punktami końcowymi serwera synchronizującymi się z nim. Jeśli zapomnisz dostosować wolne miejsce nawet w jednym punkcie końcowym serwera, synchronizacja będzie nadal stosować najbardziej restrykcyjną regułę i próbować zachować 99% wolnego miejsca na dysku, dzięki czemu lokalna pamięć podręczna nie będzie działać zgodnie z oczekiwaniami. Chyba że twoim celem jest tylko obszar nazw dla woluminu, który zawiera tylko rzadko dostępne, dane archiwalne i rezerwujesz resztę miejsca do magazynowania dla innego scenariusza.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Najbardziej prawdopodobnym problemem, na który można napotkać, jest to, że polecenie RoboCopy kończy się niepowodzeniem z *"Woluminem pełnym"* po stronie systemu Windows Server. Warstwowa chmura działa raz na godzinę, aby ewakuować zawartość z lokalnego dysku systemu Windows Server, który został zsynchronizowany. Jego celem jest osiągnięcie 99% wolnego miejsca na woluminie.

Pozwól postępowi synchronizacji i warstwom w chmurze zwolnić miejsce na dysku. Można to zaobserwować w Eksploratorze plików na serwerze Windows Server.

Gdy system Windows Server ma wystarczającą dostępną pojemność, ponowne uruchomienie polecenia rozwiąże problem. Nic nie pęka, gdy dojdziesz do tej sytuacji i możesz iść do przodu z ufnością. Niedogodności związane z ponownym uruchomieniem polecenia jest jedyną konsekwencją.

Sprawdź łącze w poniższej sekcji, aby rozwiązać problemy z synchronizacją plików platformy Azure.

## <a name="next-steps"></a>Następne kroki

Informacje na temat udziałów plików platformy Azure i synchronizacji plików platformy Azure można znaleźć więcej. Poniższe artykuły pomagają zrozumieć zaawansowane opcje, najlepsze rozwiązania, a także zawierają pomoc dotyczącą rozwiązywania problemów. Te artykuły łącze do [dokumentacji udziału plików platformy Azure,](storage-files-introduction.md) stosownie do przypadku.

* [Omówienie AFS](https://aka.ms/AFS)
* [Przewodnik wdrażania usługi AFS](storage-files-deployment-guide.md)
* [Rozwiązywanie problemów z afs](storage-sync-files-troubleshoot.md)
