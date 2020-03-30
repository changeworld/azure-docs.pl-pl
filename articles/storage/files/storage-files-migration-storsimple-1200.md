---
title: Migracja storsimple 1200 do synchronizacji plików platformy Azure
description: Dowiedz się, jak przeprowadzić migrację urządzenia wirtualnego z serii StorSimple 1200 do usługi Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 69225da1506ced879363b10b098d939df93cbfba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502375"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>Migracja storsimple 1200 do synchronizacji plików platformy Azure

StorSimple serii 1200 to urządzenie wirtualne, które jest uruchamiane w lokalnym centrum danych. Istnieje możliwość migracji danych z tego urządzenia do środowiska synchronizacji plików platformy Azure. Usługa Azure File Sync to domyślna i strategiczna długoterminowa usługa platformy Azure, do którą można migrować urządzenia StorSimple.

Seria StorSimple 1200 dobiegnie [końca](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) w grudniu 2022 roku.  Ważne jest, aby rozpocząć planowanie migracji tak szybko, jak to możliwe. Ten artykuł zawiera niezbędną wiedzę w tle i kroki migracji dla pomyślnej migracji do usługi Azure File Sync. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> Firma Microsoft dokłada wszelkich starań, aby pomóc klientom w ich migracji. Wyślij AzureFilesMigration@microsoft wiadomość e-mail do .com w celu uzyskania dostosowanego planu migracji oraz pomocy podczas migracji.

Usługa Azure File Sync to usługa w chmurze firmy Microsoft oparta na dwóch głównych składnikach:

* Synchronizacja plików i warstwowanie w chmurze.
* Udziały plików jako magazynu macierzystego na platformie Azure, które mogą być dostępne za pośrednictwem wielu protokołów, takich jak SMB i rest pliku. Udział plików platformy Azure jest porównywalny z udziałem plików w systemie Windows Server, który można natywnie zainstalować jako dysk sieciowy. Obsługuje ważne aspekty wierności plików, takie jak atrybuty, uprawnienia i sygnatury czasowe. W przeciwieństwie do StorSimple, żadna aplikacja/usługa nie jest wymagana do interpretacji plików i folderów przechowywanych w chmurze. Idealne i najbardziej elastyczne podejście do przechowywania danych serwera plików ogólnego przeznaczenia, a także niektórych danych aplikacji w chmurze.

W tym artykule skupiono się na krokach migracji. Jeśli przed migracją chcesz dowiedzieć się więcej o usłudze Azure File Sync, zalecamy następujące artykuły:

* [Usługa Azure File Sync — omówienie](https://aka.ms/AFS "Omówienie")
* [Usługa Azure File Sync — przewodnik po wdrażaniu](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Cele migracji

Celem jest zagwarantowanie integralności danych produkcyjnych, a także zagwarantowanie dostępności. Ten ostatni wymaga utrzymania przestojów do minimum, tak aby mógł zmieścić się w lub tylko nieznacznie przekroczyć regularne okna konserwacji.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>Ścieżka migracji StorSimple 1200 do synchronizacji plików platformy Azure

Do uruchomienia agenta usługi Azure File Sync wymagany jest lokalny serwer Windows Server. System Windows Server może być co najmniej serwerem 2012R2, ale najlepiej jest z systemem Windows Server 2019.

Istnieje wiele alternatywnych ścieżek migracji i stworzyłoby to zbyt długi artykuł, aby udokumentować wszystkie z nich i zilustrować, dlaczego ponoszą ryzyko lub wady trasy, którą zalecamy jako najlepszą praktykę w tym artykule.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Omówienie trasy migracji kroków poniżej w tym artykule.":::

Poprzedni obraz przedstawia kroki, które odpowiadają sekcjom w tym artykule.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>Krok 1: Aprowizowanie lokalnego systemu Windows Server i magazynu

1. Utwórz system Windows Server 2019 — co najmniej 2012R2 — jako maszynę wirtualną lub serwer fizyczny. Obsługiwany jest również klaster trybu failover systemu Windows Server.
2. Aprowizuj lub dodaj bezpośrednio podłączoną pamięć masową (DAS w porównaniu z serwerem NAS, który nie jest obsługiwany). Rozmiar magazynu systemu Windows Server musi być równy lub większy niż rozmiar dostępnej pojemności wirtualnego urządzenia StorSimple 1200.

### <a name="step-2-configure-your-windows-server-storage"></a>Krok 2: Konfigurowanie magazynu systemu Windows Server

W tym kroku należy zamapować strukturę magazynu StorSimple (woluminy i udziały) do struktury magazynu systemu Windows Server.
Jeśli planowane jest wprowadzenie zmian w strukturze magazynu, czyli liczby woluminów, skojarzenia folderów danych z woluminami lub struktury podfolderu powyżej lub poniżej bieżących udziałów SMB/NFS, teraz jest czas, aby wziąć te zmiany pod uwagę.
Zmiana struktury plików i folderów po skonfigurowaniu usługi Azure File Sync jest uciążliwa i należy jej unikać.
W tym artykule przyjęto założenie, że mapujesz 1:1, dlatego należy wziąć pod uwagę zmiany mapowania, postępując zgodnie z instrukcjami opisanymi w tym artykule.

* Żadne dane produkcyjne nie powinny znajdować się na woluminie systemowym systemu Windows Server. Warstwy w chmurze nie są obsługiwane na woluminach systemowych. Jednak ta funkcja jest wymagana dla migracji, a także operacji ciągłych jako storsimple zastąpienie.
* Aprowizować taką samą liczbę woluminów w systemie Windows Server, jak na urządzeniu wirtualnym StorSimple 1200.
* Skonfiguruj wszystkie potrzebne role, funkcje i ustawienia systemu Windows Server. Zalecamy wybranie aktualizacji systemu Windows Server, aby zapewnić bezpieczeństwo i aktualnieszego systemu operacyjnego. Podobnie zaleca się wybranie witryny Microsoft Update w celu aktualizowania aplikacji firmy Microsoft, w tym agenta usługi Azure File Sync.
* Nie należy konfigurować żadnych folderów ani udziałów przed przeczytaniem następujących kroków.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>Krok 3: Wdrażanie pierwszego zasobu chmury synchronizacji plików platformy Azure

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>Krok 4: Dopasuj strukturę woluminu lokalnego i folderów do zasobów synchronizacji plików platformy Azure i udziału plików platformy Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>Krok 5: Aprowizuj udziały plików platformy Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>Krok 6: Konfigurowanie folderów docelowych systemu Windows Server

W poprzednich krokach zostały uwzględnione wszystkie aspekty, które określą składniki topologii synchronizacji. Nadszedł czas, aby przygotować serwer do odbierania plików do przesłania.

Utwórz **wszystkie** foldery, które będą synchronizowane z własnym udziałem plików platformy Azure.
Ważne jest, aby postępować zgodnie ze strukturą folderów, którą wcześniej udokumentowano. Jeśli na przykład zdecydowano się zsynchronizować wiele lokalnych udziałów SMB razem w jeden udział plików platformy Azure, należy umieścić je pod wspólnym folderem głównym na woluminie. Utwórz ten docelowy folder główny na woluminie teraz.

Liczba udostępnionych plików platformy Azure powinna odpowiadać liczbie folderów utworzonych w tym kroku oraz liczbie woluminów, które zostaną zsynchronizowane na poziomie głównym.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>Krok 7: Wdrażanie agenta synchronizacji plików platformy Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>Krok 8: Konfigurowanie synchronizacji

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Pamiętaj, aby włączyć warstwy w chmurze!** Jest to wymagane, jeśli serwer lokalny nie ma wystarczającej ilości miejsca do przechowywania całkowitego rozmiaru danych w storsimple magazynu w chmurze. Ustaw zasady warstwowe, tymczasowo dla migracji, na 99% wolnego miejsca na woluminie.

Powtórz kroki tworzenia grupy synchronizacji i dodawanie pasującego folderu serwera jako punktu końcowego serwera dla wszystkich udziałów plików platformy Azure/ lokalizacji serwera, które muszą być skonfigurowane do synchronizacji.

### <a name="step-9-copy-your-files"></a>Krok 9: Kopiowanie plików

Podstawowe podejście migracji jest RoboCopy z urządzenia wirtualnego StorSimple do systemu Windows Server i azure file sync do udziału plików platformy Azure.

Uruchom pierwszą kopię lokalną do folderu docelowego systemu Windows Server:

* Zidentyfikuj pierwszą lokalizację na wirtualnym urządzeniu StorSimple.
* Zidentyfikuj pasujący folder w systemie Windows Server, który ma już skonfigurowaną usługę Azure File Sync.
* Rozpocznij kopiowanie za pomocą robocopy

Następujące polecenie RoboCopy spowoduje przywołanie plików z magazynu StorSimple na platformie Azure do lokalnego storproste, a następnie przeniesienie ich do folderu docelowego systemu Windows Server. System Windows Server zsynchronizuje go z udziałami plików platformy Azure. W miarę zapełniania lokalnego woluminu systemu Windows Server warstwowe warstwy w chmurze i pliki warstwy, które zostały pomyślnie zsynchronizowane. Warstwowa chmura wygeneruje wystarczająco dużo miejsca, aby kontynuować kopiowanie z urządzenia wirtualnego StorSimple. Warstwowa chmura sprawdza raz na godzinę, aby zobaczyć, co zostało zsynchronizowane i zwolnić miejsce na dysku, aby osiągnąć 99% wolnego miejsca na woluminie.

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
      /UNILOG:<file name>
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
      Pozwala uruchomić to polecenie RoboCopy kilka razy, sekwencyjnie na tym samym celu / miejscu docelowym. Identyfikuje to, co zostało skopiowane wcześniej i pomija je. Przetwarzane będą tylko zmiany, dodatki i " deletes ", które*wystąpiły*od ostatniego uruchomienia. Jeśli polecenie nie zostało uruchomione wcześniej, nic nie zostanie pominięte. Jest to doskonała opcja dla lokalizacji źródłowych, które są nadal aktywnie używane i zmienia.
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

Po uruchomieniu polecenia RoboCopy po raz pierwszy użytkownicy i aplikacje nadal uzyskują dostęp do plików i folderów StorSimple i potencjalnie je zmieniają. Jest możliwe, że RoboCopy przetworzył katalog, przechodzi do następnego, a następnie użytkownik w lokalizacji źródłowej (StorSimple) dodaje, zmienia lub usuwa plik, który nie będzie teraz przetwarzany w tym bieżącym uruchomieniu RoboCopy. To jest w porządku.

Pierwsze uruchomienie dotyczy przenoszenia większości danych z powrotem do środowiska lokalnego, do systemu Windows Server i tworzenia kopii zapasowych w chmurze za pośrednictwem usługi Azure File Sync. Może to zająć dużo czasu, w zależności od:

* przepustowość pobierania
* szybkość wycofywania usługi storsimple w chmurze
* przepustowość przesyłania
* liczbę elementów (plików i folderów), które muszą być przetwarzane przez

Po zakończeniu początkowego uruchomienia uruchom polecenie ponownie.

Za drugim razem zakończy się szybciej, ponieważ musi tylko transportować zmiany, które miały miejsce od ostatniego uruchomienia. Te zmiany są prawdopodobnie lokalne storsimple już, ponieważ są one najnowsze. To jeszcze bardziej skracają czas, ponieważ zmniejsza się potrzeba wycofywania z chmury. Podczas tego drugiego biegu, nadal, nowe zmiany mogą gromadzić.

Powtarzaj ten proces, dopóki nie upewnisz się, że czas potrzebny do ukończenia jest dopuszczalnym przestojem.

Jeśli wziąć pod uwagę czas przestoju dopuszczalne i jesteś przygotowany do podjęcia StorSimple lokalizacji w trybie offline, a następnie zrobić to teraz: Na przykład, usuń udział SMB, tak aby żaden użytkownik nie może uzyskać dostępu do folderu lub podjąć inny odpowiedni krok, który uniemożliwia zawartość do zmiany w tym folderu na StorSimple.

Uruchom ostatnią rundę RoboCopy. Spowoduje to odebranie wszelkich zmian, które mogły zostać pominięte.
Jak długo trwa ten ostatni krok, zależy od szybkości skanowania RoboCopy. Możesz oszacować czas (który jest równy przestojowi), mierząc czas poprzedniego biegu.

Utwórz udział w folderze Windows Server i ewentualnie dostosuj wdrożenie systemu DFS-N, aby go wskazać. Pamiętaj, aby ustawić te same uprawnienia na poziomie udziału, jak w udziale StorSimple SMB.

Migracja akcji/grupy udziałów została zakończona do wspólnego katalogu głównego lub woluminu. (W zależności od tego, co zostało zamapowane i zdecydowano, że trzeba przejść do tego samego udziału plików platformy Azure.)

Możesz spróbować uruchomić kilka z tych kopii równolegle. Firma Microsoft zaleca przetwarzanie zakresu jednego udziału plików platformy Azure w czasie.

> [!WARNING]
> Po przeniesieniu wszystkich danych z systemu StorSimple do systemu Windows Server i zakończeniu migracji: Powrót do ***wszystkich*** grup synchronizacji w witrynie Azure portal i dostosowanie wartości procentu wolnego miejsca woluminu w warstwie w chmurze do czegoś lepiej dostosowanego do wykorzystania pamięci podręcznej, powiedzmy 20%. 

Zasady wolnego miejsca woluminu warstwowego w chmurze działają na poziomie woluminu z potencjalnie wieloma punktami końcowymi serwera synchronizującymi się z nim. Jeśli zapomnisz dostosować wolne miejsce nawet w jednym punkcie końcowym serwera, synchronizacja będzie nadal stosować najbardziej restrykcyjną regułę i próbować zachować 99% wolnego miejsca na dysku, dzięki czemu lokalna pamięć podręczna nie będzie działać zgodnie z oczekiwaniami. Chyba że twoim celem jest mieć tylko obszar nazw dla woluminu, który zawiera tylko rzadko dostępne dane archiwalne.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Najbardziej prawdopodobnym problemem, na który można napotkać, jest to, że polecenie RoboCopy kończy się niepowodzeniem z *"Woluminem pełnym"* po stronie systemu Windows Server. Jeśli tak jest, szybkość pobierania jest prawdopodobnie lepsza niż szybkość wysyłania. Warstwowa chmura działa raz na godzinę, aby ewakuować zawartość z lokalnego dysku systemu Windows Server, który został zsynchronizowany.

Pozwól postępowi synchronizacji i warstwom w chmurze zwolnić miejsce na dysku. Można to zaobserwować w Eksploratorze plików na serwerze Windows Server.

Gdy system Windows Server ma wystarczającą dostępną pojemność, ponowne uruchomienie polecenia rozwiąże problem. Nic nie pęka, gdy dojdziesz do tej sytuacji i możesz iść do przodu z ufnością. Niedogodności związane z ponownym uruchomieniem polecenia jest jedyną konsekwencją.

Można również uruchomić na inne problemy z synchronizacją plików platformy Azure.
Tak mało prawdopodobne, jak mogą być, jeśli tak się stanie, zapoznaj się z **przewodnikiem rozwiązywania problemów z synchronizacją plików LINK Azure**.

## <a name="relevant-links"></a>Odpowiednie linki

Zawartość migracji:

* [Przewodnik po migracji serii StorSimple 8000](storage-files-migration-storsimple-8000.md)

Zawartość usługi Azure File Sync:

* [Omówienie AFS](https://aka.ms/AFS)
* [Przewodnik wdrażania usługi AFS](storage-files-deployment-guide.md)
* [Rozwiązywanie problemów z afs](storage-sync-files-troubleshoot.md)
