---
title: Migrowanie danych do usługi Azure File Sync za pomocą usługi Azure Data Box
description: Migruj dane zbiorcze w sposób zgodny z synchronizacją plików platformy Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9398aceeb7465392e82aeaa5760f6c0504f8e33d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159527"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Migrowanie danych zbiorczych do synchronizacji plików platformy Azure za pomocą usługi Azure DataBox
Dane zbiorcze można migrować do usługi Azure File Sync na dwa sposoby:

* **Przekaż swoje pliki przy użyciu usługi Azure File Sync.** Jest to najprostsza metoda. Przenieś swoje pliki lokalnie do systemu Windows Server 2012 R2 lub nowszego i zainstaluj agenta usługi Azure File Sync. Po skonfigurowaniu synchronizacji pliki zostaną przesłane z serwera. (Nasi klienci doświadczają obecnie średniej prędkości wysyłania 1 TiB około co dwa dni). Aby upewnić się, że serwer nie zużywa zbyt dużej przepustowości dla centrum danych, można skonfigurować [harmonogram ograniczania przepustowości.](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)
* **Przenieś swoje pliki w tryb offline.** Jeśli nie masz wystarczającej przepustowości, możesz nie być w stanie przekazać plików na platformę Azure w rozsądnym czasie. Wyzwaniem jest początkowa synchronizacja całego zestawu plików. Aby sprostać temu wyzwaniu, użyj narzędzi migracji zbiorczej w trybie offline, takich jak [rodzina azure data box](https://azure.microsoft.com/services/storage/databox). 

W tym artykule wyjaśniono, jak migrować pliki w trybie offline w sposób zgodny z synchronizacją plików platformy Azure. Postępuj zgodnie z tymi instrukcjami, aby uniknąć konfliktów plików i zachować listy kontroli dostępu do plików i folderów (Listy kontroli dostępu do plików) i sygnatury czasowe po włączeniu synchronizacji.

## <a name="migration-tools"></a>Narzędzia migracji
Proces, który opisujemy w tym artykule działa nie tylko dla data box, ale także dla innych narzędzi migracji w trybie offline. Działa również dla narzędzi, takich jak AzCopy, Robocopy, lub narzędzi i usług partnerskich, które działają prosto przez Internet. Jednak aby przezwyciężyć początkowe wyzwanie przekazywania, wykonaj kroki opisane w tym artykule, aby użyć tych narzędzi w sposób zgodny z synchronizacją plików platformy Azure.

W niektórych przypadkach należy przenieść się z jednego systemu Windows Server do innego systemu Windows Server przed przyjęciem usługi Azure File Sync. [Usługa migracji pamięci masowej](https://aka.ms/storagemigrationservice) (SMS) może w tym pomóc. Niezależnie od tego, czy chcesz przeprowadzić migrację do wersji systemu operacyjnego Serwera obsługiwanej przez usługę Azure File Sync (Windows Server 2012R2 i nowsze), czy po prostu musisz przeprowadzić migrację, ponieważ kupujesz nowy system synchronizacji plików platformy Azure, program SMS ma wiele funkcji i zalet, które pomogą ci uzyskać migracji odbywa się płynnie.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Korzyści z używania narzędzia do przesyłania danych w trybie offline
Oto główne zalety korzystania z narzędzia transferu, takiego jak Data Box do migracji w trybie offline:

- Nie musisz przesyłać wszystkich plików przez sieć. W przypadku dużych obszarów nazw to narzędzie może zaoszczędzić znaczną przepustowość i czas sieci.
- Podczas korzystania z usługi Azure File Sync, bez względu na to, którego narzędzia transferu używasz (Data Box, Usługa importu/eksportu platformy Azure i tak dalej), serwer na żywo przekazuje tylko pliki, które zmieniają się po przeniesieniu danych na platformę Azure.
- Usługa Azure File Sync synchronizuje listy ACL plików i folderów, nawet jeśli narzędzie do migracji zbiorczej w trybie offline nie przenosi list ACL.
- Data Box i Azure File Sync nie wymagają przestojów. Korzystając z pola danych do przesyłania danych na platformę Azure, można wydajnie efektywnie korzystać z przepustowości sieci i zachować wierność pliku. Możesz również aktualizować obszar nazw, przekazując tylko pliki, które zmieniają się po przeniesieniu danych na platformę Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Wymagania wstępne dotyczące transferu danych w trybie offline
Nie należy włączać synchronizacji na serwerze, który przeprowadzasz migrację przed zakończeniem transferu danych w trybie offline. Inne rzeczy, które należy wziąć pod uwagę przed rozpoczęciem są następujące:

- Jeśli planujesz używać pola danych do migracji zbiorczej: przejrzyj [wymagania wstępne dotyczące wdrożenia dla pola danych](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planowanie ostatecznej topologii synchronizacji plików platformy Azure: [planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md)
- Wybierz konta magazynu platformy Azure, które będą zawierać udziały plików, z którymi chcesz synchronizować. Upewnij się, że migracja zbiorcza odbywa się z tymczasowymi udziałami przejściowym na tych samych kontach magazynu. Migracja zbiorcza może być włączona tylko przy użyciu udziału końcowego i przejściowego, które znajdują się na tym samym koncie magazynu.
- Migracja zbiorcza może być wykorzystywana tylko podczas tworzenia nowej relacji synchronizacji z lokalizacją serwera. Nie można włączyć migracji zbiorczej z istniejącą relacją synchronizacji.


## <a name="process-for-offline-data-transfer"></a>Proces przesyłania danych w trybie offline
Aby skonfigurować synchronizację plików platformy Azure w sposób zgodny z narzędziami do migracji zbiorczej, takimi jak Azure Data Box:

![Diagram przedstawiający sposób konfigurowania synchronizacji plików platformy Azure](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Krok | Szczegół |
|---|---------------------------------------------------------------------------------------|
| ![Krok 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Zamów swoje pole danych](../../databox/data-box-deploy-ordered.md). Rodzina Data Box oferuje [kilka produktów](https://azure.microsoft.com/services/storage/databox/data) spełnianych Przez Państwa potrzeby. Po otrzymaniu pola danych należy postępować zgodnie z jego [dokumentacją, aby skopiować dane](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) do tej ścieżki UNC w polu data: * \\<DeviceIPAddres\>\<StorageAccountName_AzFile\>\<ShareName\>*. W tym miejscu *ShareName* jest nazwą udziału przemieszczania. Wyślij pole danych z powrotem na platformę Azure. |
| ![Krok 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Poczekaj, aż pliki pojawią się w udziałach plików platformy Azure, które zostały wybrane jako tymczasowe udziały przejściowe. *Nie należy włączać synchronizacji z tymi udziałami.* |
| ![Krok 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Utwórz nowy pusty udział dla każdego udziału plików utworzonego dla Ciebie. Ten nowy udział powinien znajdować się na tym samym koncie magazynu, co udział data box. [Jak utworzyć nowy udział plików platformy Azure](storage-how-to-create-file-share.md). |
| ![Krok 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Utwórz grupę synchronizacji](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) w usłudze synchronizacji magazynu. Odwołanie się do pustego udziału jako punktu końcowego chmury. Powtórz ten krok dla każdego udziału plików Data Box. [Konfigurowanie usługi Azure File Sync](storage-sync-files-deployment-guide.md). |
| ![Krok 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Dodaj katalog serwerów na żywo jako punkt końcowy serwera](storage-sync-files-deployment-guide.md#create-a-server-endpoint). W procesie określ, że pliki zostały przeniesione na platformę Azure i odwołaj się do udziałów przejściowych. W razie potrzeby można włączyć lub wyłączyć warstwowanie w chmurze. Podczas tworzenia punktu końcowego serwera na serwerze na żywo odwołaj się do udziału przejściowego. W bloku **Dodaj punkt końcowy serwera** w obszarze Transfer danych w **trybie offline**wybierz pozycję Włączone , a następnie wybierz udział **przejściowy,** który musi znajdować się na tym samym koncie magazynu co punkt końcowy w chmurze. W tym miejscu lista dostępnych udziałów jest filtrowana według konta magazynu i udziałów, które nie są jeszcze synchronizowane. |

![Zrzut ekranu przedstawiający interfejs użytkownika portalu Azure, przedstawiający sposób włączania transferu danych w trybie offline podczas tworzenia nowego punktu końcowego serwera](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Synchronizowanie udziału
Po utworzeniu punktu końcowego serwera rozpocznie się synchronizacja. Proces synchronizacji określa, czy każdy plik na serwerze istnieje również w udziale przejściowym, w którym data box zdeponowane pliki. Jeśli plik istnieje, proces synchronizacji kopiuje plik z udziału przemieszczania, a nie przesyła go z serwera. Jeśli plik nie istnieje w udziale przejściowym lub jeśli nowsza wersja jest dostępna na serwerze lokalnym, proces synchronizacji przekazuje plik z serwera lokalnego.

> [!IMPORTANT]
> Tryb migracji zbiorczej można włączyć tylko podczas tworzenia punktu końcowego serwera. Po ustanowieniu punktu końcowego serwera nie można zintegrować danych zmigrowanych zbiorczo z już synchronizującego się serwera z obszarem nazw.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Listy ACL i sygnatury czasowe plików i folderów
Usługa Azure File Sync zapewnia, że listy ACL plików i folderów są synchronizowane z serwera na żywo, nawet jeśli narzędzie do migracji zbiorczej, którego użyto, początkowo nie transportowało list ACL. Z tego powodu udział przejściowy nie musi zawierać żadnych list ACL dla plików i folderów. Po włączeniu funkcji migracji danych w trybie offline podczas tworzenia nowego punktu końcowego serwera wszystkie listy ACL plików są synchronizowane na serwerze. Nowo utworzone i zmodyfikowane sygnatury czasowe są również synchronizowane.

## <a name="shape-of-the-namespace"></a>Kształt obszaru nazw
Po włączeniu synchronizacji zawartość serwera określa kształt obszaru nazw. Jeśli pliki zostaną usunięte z serwera lokalnego po zakończeniu migawki i migracji pola danych, pliki te nie zostaną przeniesione do obszaru nazw na żywo, synchronizując go. Pozostają w udziale przejściowym, ale nie są kopiowane. Jest to konieczne, ponieważ synchronizacja utrzymuje obszar nazw zgodnie z serwerem na żywo. *Migawka* pola danych jest tylko miejscem przemieszczania dla wydajnej kopii plików. To nie jest autorytet dla kształtu żywej przestrzeni nazw.

## <a name="cleaning-up-after-bulk-migration"></a>Czyszczenie po migracji zbiorczej 
Gdy serwer kończy początkową synchronizację obszaru nazw, pliki migrowane zbiorczo w polu danych używają udziału plików przemieszczania. W bloku **Właściwości punktu końcowego serwera** w witrynie Azure portal w sekcji Transfer danych w **trybie offline** stan zmienia się z W **toku** na **Ukończony**. 

![Zrzut ekranu przedstawiający blok Właściwości punktu końcowego serwera, w którym znajdują się elementy sterujące stanem i wyłączaniem przesyłania danych w trybie offline](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Teraz możesz wyczyścić udział przejściowy, aby zaoszczędzić koszty:

1. W bloku **Właściwości punktu końcowego serwera** po **zakończeniu**stanu wybierz pozycję Wyłącz transfer danych **w trybie offline**.
2. Należy rozważyć usunięcie udziału przemieszczania, aby zaoszczędzić koszty. Udział przejściowy prawdopodobnie nie zawiera list ACL plików i folderów, więc jest mało prawdopodobne, aby był przydatny. Aby utworzyć kopię zapasową w czasie, utwórz prawdziwą [migawkę synchronizacji udziału plików platformy Azure](storage-snapshots-files.md). Można [skonfigurować usługę Azure Backup, aby robić migawki]( ../../backup/backup-afs.md) zgodnie z harmonogramem.

Wyłącz tryb transferu danych w trybie offline tylko wtedy, gdy stan jest **zakończony** lub gdy chcesz anulować z powodu błędnej konfiguracji. Jeśli tryb zostanie wyłączony podczas wdrażania, pliki zaczną być przekazywane z serwera, nawet jeśli udział przejściowy jest nadal dostępny.

> [!IMPORTANT]
> Po wyłączeniu trybu transferu danych w trybie offline nie można go ponownie włączyć, nawet jeśli udział przejściowy z migracji zbiorczej jest nadal dostępny.

## <a name="next-steps"></a>Następne kroki
- [Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md)
- [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
