---
title: Na użytek urządzenia Data Box i innych metod wprowadzania w trybie offline do usługi Azure File Sync.
description: Obsługuje proces i najlepsze rozwiązania, aby umożliwić migrację zgodne zbiorcze synchronizacji.
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 6781ce4a3cf5f6f883678ac848162790d45a5a0f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669809"
---
# <a name="migrate-to-azure-file-sync"></a>Migracja do usługi Azure File Sync
Dostępnych jest kilka opcji, aby przejść do usługi Azure File Sync:

### <a name="uploading-files-via-azure-file-sync"></a>Przekazywanie plików za pomocą usługi Azure File Sync
Jest to najprostsza opcja do przenoszenia plików lokalnie do wersji Windows Server 2012 R2 lub nowszej i zainstalować agenta usługi Azure File Sync. Po skonfigurowaniu synchronizacji przekazać pliki z serwera. Firma Microsoft napotykasz prędkości średniej przekazywania obecnie we wszystkich naszych klientów 1 TB, o co 2 dni.
Należy wziąć pod uwagę [przepustowości harmonogram](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter) aby upewnić się, Twój serwer jest dobre dla obywateli w centrum danych.

### <a name="offline-bulk-transfer"></a>Przeniesienia zbiorczego w trybie offline
Podczas przekazywania na pewno jest to najprostsza opcja, może nie działać dla Ciebie Jeśli dostępną przepustowość nie pozwoli na synchronizowanie plików na platformę Azure w rozsądnym czasie. Wezwanie do pokonania tutaj jest synchronizacja początkowa całego zestawu plików. Następnie usługi Azure File Sync będzie tylko przenosić zmiany, w jakiej występują w przestrzeni nazw, co zwykle zużywa znacznie mniej przepustowości.
Aby wyeliminować to żądanie przekazywanie początkowej, narzędzi migracji zbiorczego w trybie offline, takich jak Azure [rodziny urządzenia Data Box](https://azure.microsoft.com/services/storage/databox) mogą być używane. Następujący artykuł koncentruje się na temat procesu, które należy wykonać, aby korzystać z migracji w trybie offline w sposób zgodny usługi Azure File Sync. Będzie on opisano najlepsze rozwiązania, które mogą pomóc uniknąć konfliktów plików i zachować swoje listy ACL plików i folderów i sygnatury czasowe, po włączeniu synchronizacji.

### <a name="online-migration-tools"></a>Narzędzia migracji w trybie online
Z poniższą procedurą nie działa tylko dla urządzenia Data Box. Działa to w przypadku dowolnego narzędzia do migracji w trybie offline (na przykład urządzenia Data Box) lub online narzędzi, takich jak narzędzia AzCopy, Robocopy lub innych firm i usługi. Dlatego niezależnie od metody rozwiązywania wyzwanie przekazywanie początkowej jest nadal ważna, wykonaj kroki opisane poniżej, korzystanie z tych narzędzi w synchronizacji sposób zgodne.


## <a name="offline-data-transfer-benefits"></a>Korzyści transferu danych w trybie offline
Najważniejsze zalety migracji w trybie offline, korzystając z urządzenia Data Box są następujące:

- Podczas migracji plików na platformę Azure za pośrednictwem procesu przeniesienia zbiorczego w trybie offline, takich jak pole danych, nie trzeba przekazać wszystkie pliki z serwera za pośrednictwem sieci. Dla dużych przestrzeni nazw może to oznaczać znaczne oszczędności przepustowości sieci i czas.
- Gdy możesz użyć usługi Azure File Sync, a następnie niezależnie od tego trybu transportu używane (urządzenia Data Box, Azure Import itp.), serwer na żywo tylko służy do przekazywania plików, które zostały zmienione, ponieważ dostarczone dane na platformę Azure.
- Usługa Azure File Sync zapewnia, że listy ACL plików i folderów są synchronizowane także — nawet wtedy, gdy produkt migracji zbiorczego w trybie offline nie transportu list ACL.
- Korzystając z usługi Azure Data Box i usługi Azure File Sync, brak przestojów. Transfer danych na platformę Azure za pomocą urządzenia Data Box sprawia, że efektywne wykorzystanie przepustowości sieci przy jednoczesnym zachowaniu jakości pliku. Również zapewnia przestrzeń nazw aktualne, przekazując tylko te pliki, które uległy zmianie od czasu urządzenia Data Box zostało wysłane.

## <a name="plan-your-offline-data-transfer"></a>Planowanie transferu danych w trybie offline
Przed rozpoczęciem należy przejrzeć następujące informacje:

- Wykonaj migrację zbiorczo do jednego lub wielu udziałów plików platformy Azure, przed włączeniem synchronizacji za pomocą usługi Azure File Sync.
- Jeśli planujesz używać urządzenia Data Box do zbiorczego migracji: Przegląd [wymagania wstępne dotyczące wdrażania dla urządzenia Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planowanie topologii końcowego usługi Azure File Sync: [Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md)
- Wybieranie konta magazynu platformy Azure, który będzie przechowywać udziałów plików, które mają być synchronizowane ze. Upewnij się, że migrację zbiorcze stanie się tymczasowe przemieszczania udziałów w ramach tego samego konta magazynu. Zbiorcze migracji można włączyć tylko przy użyciu ostatecznego — a udziałem przemieszczania znajdujących się w tym samym koncie magazynu.
- Migracji zbiorcze być wykorzystywane tylko w przypadku, gdy utworzysz nową relację synchronizacji z lokalizacją serwera. Nie można włączyć migracji zbiorcze za pomocą istniejącej relacji synchronizacji.

## <a name="offline-data-transfer-process"></a>Proces transferu danych w trybie offline
W tej sekcji opisano proces konfigurowania usługi Azure File Sync w sposób zgodny z narzędzia migracji zbiorcze, takie jak Azure Data Box.

![Wizualizacja kroki procesu, który również jest omówiona szczegółowo w następujący akapit](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Krok | Szczegół |
|---|---------------------------------------------------------------------------------------|
| ![Krok 1 procesu](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Zamówienie usługi Data Box](../../databox/data-box-deploy-ordered.md). Istnieją [kilku ofert w ramach rodziny urządzenia Data Box](https://azure.microsoft.com/services/storage/databox/data) do potrzeb. Odbieranie usługi Data Box i postępuj zgodnie z urządzenia Data Box [dokumentacji, aby skopiować dane](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box). Upewnij się, że dane zostały skopiowane do tej ścieżki UNC, na urządzenie Data Box: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>` gdzie `ShareName` to nazwa udziału przemieszczania. Urządzenie Data Box można wysyłać do platformy Azure. |
| ![Proces krok 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Poczekaj, aż pliki pojawiają się w udziałów plików platformy Azure, który wyznaczony jako tymczasowy przemieszczania udziałów. **Nie włączaj synchronizację w tych udziałach!** |
| ![Proces krok 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Utwórz nowy udział, który jest pusty dla każdego udziału plików, które urządzenia Data Box utworzone dla Ciebie. Upewnij się, że ten nowy udział w tym samym koncie magazynu, udziału urządzenia Data Box. [Jak utworzyć nowy udział plików platformy Azure](storage-how-to-create-file-share.md). |
| ![Proces krok 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Tworzenie grupy synchronizacji](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) w usłudze synchronizacji magazynu i odwołanie do pustego udziału jako punktu końcowego w chmurze. Powtórz ten krok dla każdego udziału plików urządzenia Data Box. Przegląd [wdrożenia usługi Azure File Sync](storage-sync-files-deployment-guide.md) przewodnika i wykonaj kroki wymagane do skonfigurowania usługi Azure File Sync. |
| ![Proces krok 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Dodawanie katalogu serwera na żywo jako punkt końcowy serwera](storage-sync-files-deployment-guide.md#create-a-server-endpoint). Określ w procesie już przeniesionych plików na platformę Azure i odwoływać się do akcji przejściowej. Jest możliwość Włączanie lub wyłączanie obsługi warstw zgodnie z potrzebami w chmurze. Podczas tworzenia punktu końcowego serwera na serwerze na żywo, należy odwoływać się do tymczasowej udziału. Włącz "W trybie Offline Transfer danych" (obraz poniżej) w nowym bloku punktu końcowego serwera, a następnie Odwołaj przejściowym udziału, który musi znajdować się w tym samym koncie magazynu jako punkt końcowy w chmurze. Lista dostępnych udziałów są filtrowane według konta magazynu i udziałów, które nie są już synchronizuje. |

![Wizualizowanie interfejsu użytkownika witryny Azure portal, włączania trybu Offline transferu danych podczas tworzenia nowego punktu końcowego serwera.](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Udział synchronizacji
Po utworzeniu punktu końcowego serwera rozpocznie się synchronizacja. Dla każdego pliku, który nie istnieje na serwerze synchronizacji określi, jeśli ten plik już istnieje też w udziale tymczasowej, której urządzenia Data Box złożone pliki, a więc synchronizacji skopiuje plik z przemieszczania udziału, zamiast przekazywać go z serwera. Jeśli plik nie istnieje w udziale tymczasowej lub nowsza wersja jest dostępna na serwerze lokalnym, synchronizacja będzie Przekaż plik z serwera lokalnego.

> [!IMPORTANT]
> Tryb migracji zbiorczego można włączyć tylko podczas tworzenia punktu końcowego serwera. Po ustanowieniu punkt końcowy serwera nie istnieje obecnie sposób zintegrować zbiorcze dane zostały migrowane z już synchronizowanie serwera do przestrzeni nazw.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Listy kontroli dostępu i sygnatury czasowe dla plików i folderów
Usługa Azure File Sync zapewnia, że listy ACL plików i folderów są synchronizowane z serwerem na żywo, nawet wtedy, gdy narzędzie migracji zbiorcze, który został użyty nie transportu list ACL początkowo. Oznacza to, że jest to poprawne przejściowym udziału nie zawiera żadnych list kontroli dostępu do plików i folderów. Po włączeniu funkcji migracji danych w trybie offline podczas tworzenia nowego punktu końcowego serwera, listy ACL zostaną zsynchronizowane w tym czasie dla wszystkich plików na serwerze. To samo dotyczy dla tworzenia — i zmodyfikować-sygnatur czasowych.

## <a name="shape-of-the-namespace"></a>Kształt obszaru nazw
Kształt obszaru nazw jest określany przez co znajduje się na serwerze po włączeniu synchronizacji. Jeśli pliki są usuwane z serwera lokalnego po urządzenia Data Box "-snapshot" i - migracji, a następnie pliki te nie zostać dostosowane do przestrzeni nazw na żywo, synchronizacji. Nadal będą w udziale przemieszczania, ale nigdy nie są kopiowane. Jest żądane zachowanie synchronizacji przechowuje przestrzeń nazw zgodnie z serwerem na żywo. Urządzenie Data Box "snapshot" jest po prostu przemieszczania podstaw związanym z kopiowaniem plików wydajne i nie urzędu dla kształtu na żywo przestrzeni nazw.

## <a name="finishing-bulk-migration-and-clean-up"></a>Kończenie migracji zbiorcze i czyszczenia
Poniższy zrzut ekranu przedstawiono bloku właściwości punktu końcowego serwera w witrynie Azure portal. W trybie offline sekcji transferu danych możesz obserwować stan procesu. Widoczna będzie albo "W toku" lub "Ukończone".

Po ukończeniu jego synchronizacji początkowej całej przestrzeni nazw serwer będzie została zakończona, wykorzystując plik przemieszczania udziału za pomocą zbiorczego urządzenia Data Box migrowane pliki. Sprawdź we właściwościach punktu końcowego serwera transferu danych w trybie offline, który zmienia stan "Ukończone". W tym momencie możesz wyczyścić przejściowym udziału koszty:

1. Odwołań "Wyłącz transferu danych w trybie offline" we właściwościach punktu końcowego serwera, gdy stan to "ukończone".
2. Należy rozważyć usunięcie udziału przemieszczania koszty. Przejściowym udziału jest mało prawdopodobne zawierała plików i folderów ACL i jako takie jest ograniczone. Do wykonywania kopii zapasowych "punktu w czasie", zamiast tworzyć rzeczywistych [migawki udziału plików platformy Azure, synchronizowanie](storage-snapshots-files.md). Możesz [włączyć usługi Azure Backup do robienia migawek]( ../../backup/backup-azure-files.md) zgodnie z harmonogramem.

![Wizualizowanie interfejsu użytkownika witryny Azure portal dla właściwości punktu końcowego serwera, na którym znajdują się stan i Wyłącz formantów w trybie Offline transferu danych.](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

W tym trybie należy wyłączyć tylko w przypadku, gdy stan to "ukończone" lub naprawdę chcesz przerwać z powodu błędnej konfiguracji. Jeśli wyłączasz sposób środku tryb wdrożenia uzasadnione plików zostanie uruchomiona do przekazania z serwera, nawet jeśli przejściowym udziału jest nadal dostępna.

> [!IMPORTANT]
> Po wyłączeniu transferu danych w trybie offline nie ma możliwości jej włączenia, nawet jeśli przejściowym udziału z migracji zbiorczego jest nadal dostępna.

## <a name="next-steps"></a>Kolejne kroki
- [Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md)
- [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
