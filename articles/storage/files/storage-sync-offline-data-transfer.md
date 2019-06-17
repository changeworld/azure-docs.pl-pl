---
title: Przeprowadzić migrację danych do usługi Azure File Sync za pomocą usługi Azure Data Box i innymi metodami
description: Migracja danych zbiorczego w sposób, który jest zgodny z usługi Azure File Sync.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d1ec5168b898d0aa75c12e6eb435e20c09de1929
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64700270"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Migracja danych zbiorczo do usługi Azure File Sync
Zbiorcze danych można migrować do usługi Azure File Sync na dwa sposoby:

* **Przekazywanie plików za pomocą usługi Azure File Sync.** Jest to najprostsza metoda. Przenieś swoje pliki lokalnie do wersji Windows Server 2012 R2 lub nowszej, a następnie zainstaluj agenta usługi Azure File Sync. Po skonfigurowaniu synchronizacji plików zostanie przekazany z serwera. (Naszych klientów obecnie doświadczenie prędkości średniej przekazywania 1 TiB o określony na 2 dni). Aby upewnić się, że serwer nie używa zbyt dużej ilości przepustowości dla centrum danych, możesz chcieć skonfigurować [przepustowości harmonogram](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Transfer plików w trybie offline.** Jeśli nie masz wystarczającą przepustowość nie można przekazać pliki na platformie Azure w rozsądnym czasie. Wyzwanie polega na synchronizacji początkowej całego zestawu plików. Aby stawić czoła temu wyzwaniu, użycie narzędzi migracji w trybie offline zbiorcze takich jak [rodziny Azure Data Box](https://azure.microsoft.com/services/storage/databox). 

W tym artykule opisano sposób migracji plików w trybie offline w sposób, który jest zgodny z usługi Azure File Sync. Wykonaj te instrukcje, aby uniknąć konfliktów plików i zachowanie usługi plików i listy kontroli dostępu folderu (kontroli dostępu ACL) i sygnatury czasowe, po włączeniu synchronizacji.

## <a name="online-migration-tools"></a>Narzędzia migracji w trybie online
Ten proces opisano w tym artykule działa nie tylko dla urządzenia Data Box, ale także inne narzędzia migracji w trybie offline. Działa także dla narzędzia online, takich jak narzędzia AzCopy, Robocopy, lub partnera narzędzi i usług. Jednak przezwyciężyć początkowego przesłać żądanie, wykonaj kroki opisane w tym artykule, aby użyć tych narzędzi w sposób, który jest zgodny z usługi Azure File Sync.


## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Zalety używania narzędzia do przenoszenia danych w trybie offline
Poniżej przedstawiono główne korzyści z używania narzędzie do transferu takich jak urządzenia Data Box dla migracji w trybie offline:

- Nie masz do przekazania wszystkich plików za pośrednictwem sieci. Dla dużych przestrzeni nazw to narzędzie można zapisać znaczące przepustowości oraz czas.
- Gdy używasz usługi Azure File Sync, niezależnie od tego, w których narzędzie do transferu używasz (urządzenia Data Box, usługa Azure Import/Export itd.), serwer na żywo wysyła tylko te pliki, które zmieniają się po przeniesieniu danych do platformy Azure.
- Usługa Azure File Sync synchronizuje listy ACL plików i folderów, nawet wtedy, gdy narzędzie do migracji w trybie offline zbiorczego nie transportować list ACL.
- Urządzenie Data Box i usługi Azure File Sync wymagają bez przestojów. Podczas transferu danych na platformę Azure za pomocą urządzenia Data Box efektywnie używać przepustowości sieci i zachować wierność pliku. Możesz również przestrzeni nazw na bieżąco, przekazując tylko te pliki, które zmieniają się po przeniesieniu danych do platformy Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Wymagania wstępne dotyczące transferu danych w trybie offline
Nie należy włączać synchronizacji na serwerze, który jest przeprowadzana migracja przed ukończeniem transferu danych w trybie offline. Innych rzeczy, aby wziąć pod uwagę przed przystąpieniem do wykonywania są następujące:

- Jeśli planujesz używać urządzenia Data Box do zbiorczego migracji: Przegląd [wymagania wstępne dotyczące wdrażania dla urządzenia Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planowanie topologii końcowego usługi Azure File Sync: [Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md)
- Wybieranie konta magazynu platformy Azure, który będzie przechowywać udziałów plików, które mają być synchronizowane ze. Upewnij się, że migrację zbiorcze stanie się tymczasowe przemieszczania udziałów w ramach tego samego konta magazynu. Zbiorcze migracji można włączyć tylko przy użyciu ostatecznego — a udziałem przemieszczania znajdujących się w tym samym koncie magazynu.
- Migracji zbiorcze być wykorzystywane tylko w przypadku, gdy utworzysz nową relację synchronizacji z lokalizacją serwera. Nie można włączyć migracji zbiorcze za pomocą istniejącej relacji synchronizacji.


## <a name="process-for-offline-data-transfer"></a>Proces transferu danych w trybie offline
Poniżej przedstawiono sposób konfigurowania usługi Azure File Sync w sposób, który jest zgodny z narzędzia migracji zbiorcze, takie jak Azure Data Box:

![Diagram przedstawiający sposób konfigurowania usługi Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Krok | Szczegóły |
|---|---------------------------------------------------------------------------------------|
| ![Krok 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Zamówienie usługi Data Box](../../databox/data-box-deploy-ordered.md). Urządzenie Data Box oferty rodziny [kilka produktów](https://azure.microsoft.com/services/storage/databox/data) stosownie do potrzeb. Gdy pojawi się usługi Data Box, postępuj zgodnie z jego [dokumentacji, aby skopiować dane](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) do ścieżki UNC na urządzenie Data Box:  *\\< DeviceIPAddres\>\<StorageAccountName_AzFile\> \<ShareName\>* . W tym miejscu *ShareName* to nazwa udziału przemieszczania. Urządzenie Data Box można wysyłać do platformy Azure. |
| ![Krok 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Poczekaj, aż pliki pojawiają się w udziałów plików platformy Azure, wybranych przez Ciebie jako tymczasowy udziały przemieszczania. *Nie włączaj synchronizację w tych udziałach.* |
| ![Krok 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Utwórz nowy pusty udział dla każdego udziału plików, które urządzenia Data Box utworzone dla Ciebie. Ten nowy udział powinien być na tym samym koncie magazynu, udziału urządzenia Data Box. [Jak utworzyć nowy udział plików platformy Azure](storage-how-to-create-file-share.md). |
| ![Krok 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Tworzenie grupy synchronizacji](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) w usłudze synchronizacji magazynu. Odwołanie do pustego udziału jako punktu końcowego w chmurze. Powtórz ten krok dla każdego udziału plików urządzenia Data Box. [Konfigurowanie usługi Azure File Sync](storage-sync-files-deployment-guide.md). |
| ![Krok 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Dodawanie katalogu serwera na żywo jako punkt końcowy serwera](storage-sync-files-deployment-guide.md#create-a-server-endpoint). W procesie Określ, przeniesionych plików na platformę Azure i odwoływać się do akcji przejściowej. Można włączyć lub wyłączyć obsługi warstw zgodnie z potrzebami w chmurze. Podczas tworzenia punktu końcowego serwera na serwerze na żywo odwoływać się do tymczasowej udziału. Na **Dodaj punkt końcowy serwera** bloku, w obszarze **transferu danych w trybie Offline**, wybierz opcję **włączone**, a następnie wybierz pozycję udział przemieszczania, który musi znajdować się w tym samym koncie magazynu jako chmury punkt końcowy. W tym miejscu listę dostępnych udziałów jest filtrowana według konta magazynu i udziałów, które nie są już synchronizowanie. |

![Zrzut ekranu przedstawiający interfejs użytkownika witryny Azure portal pokazujący sposób umożliwia transfer danych w trybie offline podczas tworzenia nowego punktu końcowego serwera](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Udział synchronizacji
Po utworzeniu punktu końcowego serwera, rozpocznie się synchronizacja. Proces synchronizacji Określa, czy każdy plik na serwerze występuje także w tymczasowej udziału, w których urządzenie Data Box nadawane pliki. Jeśli plik istnieje procesu synchronizacji kopiuje plik z przemieszczania udziału, zamiast przekazywać go z serwera. Jeśli plik nie istnieje w udziale tymczasowej lub nowsza wersja jest dostępna na serwerze lokalnym, proces synchronizacji służy do przekazywania plików z serwera lokalnego.

> [!IMPORTANT]
> Tryb migracji zbiorczego można włączyć tylko wtedy, gdy tworzysz punkt końcowy serwera. Po ustaleniu punktu końcowego serwera nie można zintegrować zbiorczego, migracji danych z serwera już synchronizacji przestrzeni nazw.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Listy kontroli dostępu i sygnatury czasowe dla plików i folderów
Usługa Azure File Sync zapewnia, że listy ACL plików i folderów są synchronizowane z serwerem na żywo, nawet wtedy, gdy narzędzia migracji zbiorcze, którego używano początkowo nie transportować list ACL. W związku z tym przejściowym udziału nie musi zawierać wszystkie listy ACL dla plików i folderów. Po włączeniu funkcji migracji danych w trybie offline podczas tworzenia nowego punktu końcowego serwera, wszystkie listy ACL plików są synchronizowane na serwerze. Również są synchronizowane z nowo utworzonych lub zmodyfikowanych sygnatur czasowych.

## <a name="shape-of-the-namespace"></a>Kształt obszaru nazw
Po włączeniu synchronizacji, zawartość serwera określa kształt obszaru nazw. Jeśli pliki zostaną usunięte z serwera lokalnego, po zakończeniu migawki urządzenia Data Box i migracji, pliki te nie przenoś do przestrzeni nazw na żywo, synchronizacji. Te komputery pozostaną w udziale przemieszczania, ale nie są one kopiowane. Jest to konieczne, ponieważ synchronizacja zapewnia przestrzeń nazw zgodnie z serwerem na żywo. Urządzenie Data Box *migawki* jest po prostu przemieszczania podstaw związanym z kopiowaniem plików wydajne. Nie ma uprawnienia dla kształtu na żywo przestrzeni nazw.

## <a name="cleaning-up-after-bulk-migration"></a>Czyszczenie po migracji zbiorcze 
Jako serwer zakończy synchronizację początkową przestrzeni nazw, pliki usługi Box danych migracji zbiorcze Użyj przejściowym udziału plików. Na **właściwości punktu końcowego serwera** bloku w witrynie Azure portal w **transferu danych w trybie Offline** sekcji stan zmieni się z **w toku** do **ukończone** . 

![Zrzut ekranu przedstawiający blok właściwości punktu końcowego serwera, gdzie znajdują się stan i Wyłącz formanty do transferu danych w trybie offline](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Teraz możesz wyczyścić przejściowym udziału w celu obniżenia kosztów:

1. Na **właściwości punktu końcowego serwera** bloku, jeśli jest w stanie **Ukończono**, wybierz opcję **wyłączyć transferu danych w trybie offline**.
2. Należy rozważyć usunięcie udziału tymczasowej w celu obniżenia kosztów. Przejściowym udziału prawdopodobnie nie zawiera listy ACL plików i folderów, dlatego nie jest to bardzo przydatne. Do celów tworzenia kopii zapasowych w momencie tworzenia rzeczywistych [migawki udziału plików platformy Azure, synchronizowanie](storage-snapshots-files.md). Możesz [skonfigurować usługę Azure Backup do robienia migawek]( ../../backup/backup-azure-files.md) zgodnie z harmonogramem.

Wyłącz tryb transferu danych w trybie offline, tylko wtedy, gdy stan **Ukończono** lub gdy chcesz anulować z powodu błędnej konfiguracji. Po wyłączeniu trybu podczas wdrażania plików rozpocznie się do przekazania z serwera, nawet jeśli przejściowym udziału jest nadal dostępna.

> [!IMPORTANT]
> Po wyłączeniu trybu transferu danych w trybie offline, nie można włączyć ją ponownie, nawet jeśli przejściowym udziału z migracji zbiorczego jest nadal dostępna.

## <a name="next-steps"></a>Kolejne kroki
- [Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md)
- [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
