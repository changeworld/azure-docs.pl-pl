---
title: Migrowanie do udziałów plików platformy Azure
description: Dowiedz się więcej o migracjach do udziałów plików platformy Azure i znajdź przewodnik po migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 903ce52120fce7c23c6a3754498b81fc6fc2430f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247320"
---
# <a name="migrate-to-azure-file-shares"></a>Migrowanie do udziałów plików platformy Azure

W tym artykule opisano podstawowe aspekty migracji do udziałów plików platformy Azure.

Obok podstaw migracji ten artykuł zawiera listę istniejących, zindywidualizowanych przewodników migracji. Te przewodniki migracji ułatwiają przenoszenie plików do udziałów plików platformy Azure i są zorganizowane według miejsca, w którym dane znajdują się obecnie i jaki model wdrażania (tylko w chmurze lub hybrydowy) planujesz przenieść do.

## <a name="migration-basics"></a>Podstawy migracji

Istnieje wiele różnych typów magazynu w chmurze dostępnych na platformie Azure. Podstawowym aspektem migracji plików na platformę Azure jest określenie, która opcja magazynu platformy Azure jest odpowiednia dla twoich danych.

Udziały plików platformy Azure są idealne dla danych plików ogólnego przeznaczenia. Naprawdę wszystko, czego używasz lokalnego udziału SMB lub NFS. Za [pomocą usługi Azure File Sync](storage-sync-files-planning.md)można opcjonalnie buforować zawartość kilku udziałów plików platformy Azure na kilku serwerach systemu Windows lokalnie.

Jeśli masz aplikację aktualnie uruchomię na serwerze lokalnym, a następnie przechowywanie plików w udziałach plików platformy Azure może być właśnie dla Ciebie, w zależności od aplikacji. Możesz podnieść aplikację do uruchamiania na platformie Azure i używać udziałów plików platformy Azure jako udostępnionego magazynu. Można również wziąć pod uwagę [dyski azure](../../virtual-machines/windows/managed-disks-overview.md) dla tego scenariusza. W przypadku aplikacji opartych na chmurze, które nie zależą od SMB lub komputera lokalnego dostępu do swoich danych lub dostępu współużytkowanegodłonu, magazynu obiektów, takich jak [obiekty blob platformy Azure,](../blobs/storage-blobs-overview.md)jest często najlepszym wyborem.

Kluczem w każdej migracji jest przechwycenie całej odpowiedniej wierności plików podczas migracji plików z ich bieżącej lokalizacji magazynu na platformę Azure. Pomoc w wyborze odpowiedniego magazynu platformy Azure jest również aspektem, jak bardzo wierność obsługiwana przez opcję magazynu platformy Azure i jest wymagana przez scenariusz. Dane plików ogólnego przeznaczenia tradycyjnie zależą od metadanych plików. Dane aplikacji mogą nie. Istnieją dwa podstawowe składniki do pliku:

- **Strumień danych:** Strumień danych pliku przechowuje zawartość pliku.
- **Metadane pliku**: Meta data pliku ma kilka podskładów:
   * Atrybuty plików: na przykład tylko do odczytu.
   * Uprawnienia do plików: określane jako *uprawnienia NTFS* lub *listy ACL plików i folderów*.
   * Sygnatury czasowe: przede wszystkim sygnatury czasowe *tworzenia* i *ostatniej modyfikacji.*
   * Alternatywny strumień danych: miejsce do przechowywania większych ilości niestandardowych właściwości.

Wierność plików w migracji można zatem zdefiniować jako możliwość przechowywania wszystkich odpowiednich informacji o plikach w źródle, możliwość przenoszenia ich za pomocą narzędzia migracji i możliwość przechowywania ich w docelowym magazynie migracji.

Aby zapewnić jak najpłynniejsze przeprowadzenie migracji, zidentyfikuj [najlepsze narzędzie do kopiowania do swoich potrzeb](#migration-toolbox) i dopasuj cel pamięci masowej do źródła.

Biorąc pod uwagę poprzednie informacje, staje się jasne, czym jest magazyn docelowy dla plików ogólnego przeznaczenia na platformie Azure: [udziały plików platformy Azure.](storage-files-introduction.md) W porównaniu do magazynu obiektów w obiektach blob platformy Azure metadane plików mogą być przechowywane natywnie na plikach w udziale plików platformy Azure.

Udziały plików platformy Azure zachowują również hierarchię plików i folderów. Dodatkowo:
* Uprawnienia NTFS mogą być przechowywane w plikach i folderach, ponieważ są one lokalne.
* Użytkownicy usługi AD (lub użytkownicy usług Ad DS platformy Azure) mogą natywnie uzyskiwać dostęp do udziału plików platformy Azure. 
    Używają swojej bieżącej tożsamości i uzyskać dostęp na podstawie uprawnień udziału, a także plików i folderów ACL. Zachowanie nie inaczej niż wtedy, gdy użytkownicy łączą się z lokalnym udziałem plików.
*  Alternatywny strumień danych jest podstawowym aspektem wierności plików, który obecnie nie może być przechowywany w pliku w udziale plików platformy Azure.
   Jest zachowywany lokalnie, gdy usługa Azure File Sync jest zaangażowany.

* [Dowiedz się więcej o uwierzytelnianiu usługi AD dla udziałów plików platformy Azure](storage-files-identity-auth-active-directory-enable.md)
* [Dowiedz się więcej o uwierzytelnianiu usług domenowych Usługi active directory (AAD DS) platformy Azure dla udziałów plików platformy Azure](storage-files-identity-auth-active-directory-domain-service-enable.md)

## <a name="migration-guides"></a>Przewodniki po migracji

W poniższej tabeli wymieniono szczegółowe przewodniki dotyczące migracji.

Nawiguj po nim:
1. Znajdź wiersz systemu źródłowego, na który są aktualnie przechowywane pliki.
2. Zdecyduj, czy docelowe wdrożenie hybrydowe, w którym używasz usługi Azure File Sync do buforowania zawartości jednego lub więcej udziałów plików platformy Azure lokalnie, lub jeśli chcesz używać udziałów plików platformy Azure bezpośrednio w chmurze. Wybierz kolumnę docelową, która odzwierciedla Twoją decyzję.
3. W obrębie przecięcia źródła i obiektu docelowego komórka tabeli wyświetla dostępne scenariusze migracji. Wybierz jeden z nich, aby bezpośrednio połączyć się ze szczegółowym przewodnikiem migracji.

Scenariusz bez łącza nie ma jeszcze opublikowanego przewodnika po migracji. Od czasu do czasu sprawdzaj tę tabelę pod kątem aktualizacji. Nowe przewodniki zostaną opublikowane, gdy będą dostępne.

| Element źródłowy | Cel: </br>Wdrożenie hybrydowe | Cel: </br>Wdrażanie tylko w chmurze |
|:---|:--|:--|
| | Kombinacja narzędzi:| Kombinacja narzędzi: |
| Windows Server 2012 R2 i nowsze | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Synchronizacja plików platformy Azure + databox](storage-sync-offline-data-transfer.md)</li><li>Usługa migracji magazynu + synchronizacja plików platformy Azure</li></ul> | <ul><li>Azure File Sync</li><li>Synchronizacja plików platformy Azure + databox</li><li>Usługa migracji magazynu + synchronizacja plików platformy Azure</li><li>Robocopy</li></ul> |
| Windows Server 2012 i starsze | <ul><li>Synchronizacja plików platformy Azure + databox</li><li>Usługa migracji magazynu + synchronizacja plików platformy Azure</li></ul> | <ul><li>Usługa migracji magazynu + synchronizacja plików platformy Azure</li><li>Robocopy</li></ul> |
| Sieć pamięci masowej (NAS) | <ul><li>[Synchronizacja plików platformy Azure + RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Linux / Samba | <ul><li>[RoboCopy + Synchronizacja plików platformy Azure](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| StorSimple 8100 / 8600 | <ul><li>[Azure File Sync + 8020 Virtual Appliance](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple 1200 | <ul><li>[Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Przybornik migracji

### <a name="file-copy-tools"></a>Narzędzia do kopiowania plików

Dostępnych jest kilka narzędzi do kopiowania plików firmy Microsoft i innych firm. Aby wybrać odpowiednie narzędzie do kopiowania plików dla scenariusza migracji, należy wziąć pod uwagę trzy podstawowe pytania:

* Czy narzędzie do kopiowania obsługuje źródło i lokalizację docelową dla danej kopii pliku? 
    * Czy obsługuje ścieżkę sieciową i/lub dostępne protokoły (na przykład REST/SMB/NFS) do i z lokalizacji magazynu źródłowego i docelowego?
* Czy narzędzie kopiowania zachowuje niezbędną wierność plików obsługiwana przez lokalizację źródłową/docelową? W niektórych przypadkach magazyn docelowy nie obsługuje takiej samej wierności jak źródło. Podjęto już decyzję, że magazyn docelowy jest wystarczający do twoich potrzeb, dlatego narzędzie do kopiowania musi być zgodne tylko z możliwościami wierności plików docelowych.
* Czy narzędzie do kopiowania ma funkcje, które pasują do mojej strategii migracji? 
    * Rozważmy na przykład, czy ma opcje, które pozwalają zminimalizować przestoje. Dobrym pytaniem jest: Czy mogę uruchomić tę kopię wiele razy na tym samym, przez użytkowników aktywnie dostępnej lokalizacji? Jeśli tak, można znacznie zmniejszyć ilość przestojów. Porównaj to z sytuacją, w której można uruchomić kopię tylko wtedy, gdy źródło przestaje się zmieniać, aby zagwarantować pełną kopię.

W poniższej tabeli klasyfikuje się narzędzia firmy Microsoft i ich bieżącą przydatność do udziału plików platformy Azure:

| Zalecane | Narzędzie | Obsługuje udziały plików platformy Azure | Zachowuje wierność plików |
| :-: | :-- | :---- | :---- |
|![Tak, zalecane.](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Obsługiwane. Udziały plików platformy Azure mogą być montowane jako dyski sieciowe. | Pełna wierność* |
|![Tak, zalecane.](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Natywnie zintegrowane z udziałami plików platformy Azure. | Pełna wierność* |
|![Tak, zalecane.](media/storage-files-migration-overview/circle-green-checkmark.png)| Usługa migracji pamięci masowej (SMS) | Pośrednio obsługiwane. Udziały plików platformy Azure mogą być montowane jako dyski sieciowe na serwerze docelowym programu SMS. | Pełna wierność* |
|![Nie w pełni zalecane.](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Skrzynka danych platformy Azure | Obsługiwane. | Nie kopiuje metadanych. [Może być używany w połączeniu z synchronizacją plików azure](storage-sync-offline-data-transfer.md). |
|![Niezalecane.](media/storage-files-migration-overview/circle-red-x.png)| Narzędzie AzCopy | Obsługiwane. | Nie kopiuje metadanych. |
|![Niezalecane.](media/storage-files-migration-overview/circle-red-x.png)| Eksplorator usługi Azure Storage | Obsługiwane. | Nie kopiuje metadanych. |
|![Niezalecane.](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Obsługiwane. | Nie kopiuje metadanych. |
|||||

*\*Pełna wierność: spełnia lub przekracza możliwości udostępniania plików platformy Azure.*

### <a name="migration-helper-tools"></a>Narzędzia pomocnicze migracji

W tej sekcji wymieniono narzędzia ułatwiające planowanie i wykonywanie migracji.

* **RoboCopy z firmy Microsoft Corporation**

    Jednym z najbardziej odpowiednich narzędzi kopiowania do migracji plików, jest częścią systemu Microsoft Windows. Ze względu na wiele opcji w tym narzędziu, główną [dokumentację RoboCopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) jest pomocne źródło.

* **TreeSize od JAM Software GmbH**

    Usługa Azure File Sync skaluje się głównie z liczbą elementów (plików i folderów) i mniej z całkowitą kwotą TiB. Narzędzie może służyć do określania liczby plików i folderów na woluminach systemu Windows Server. Ponadto może służyć do tworzenia perspektywy przed [wdrożeniem usługi Azure File Sync](storage-sync-files-deployment-guide.md) — ale także po, gdy warstwa w chmurze jest zaangażowana i chcesz zobaczyć nie tylko liczbę elementów, ale także w których katalogach pamięci podręcznej serwera jest najczęściej używany.
    To narzędzie (testowana wersja 4.4.1) jest zgodne z plikami warstwowymi w chmurze. Nie spowoduje to wycofania plików warstwowych podczas normalnego działania.


## <a name="next-steps"></a>Następne kroki

1. Utwórz plan, dla którego wdrożenia udziałów plików platformy Azure (tylko w chmurze lub hybrydowe), do których dążysz.
2. Przejrzyj listę dostępnych przewodników migracji, aby znaleźć szczegółowy przewodnik, który pasuje do źródła i wdrożenia udziałów plików platformy Azure.

Więcej informacji na temat technologii usługi Azure Files wymienionych w tym artykule:

* [Omówienie udziału plików platformy Azure](storage-files-introduction.md)
* [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
* [Synchronizacja plików platformy Azure: warstwa w chmurze](storage-sync-cloud-tiering.md)
