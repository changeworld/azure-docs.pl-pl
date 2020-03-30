---
title: Tworzenie kopii zapasowych i przywracanie danych na żądanie w usłudze Azure Cosmos DB
description: W tym artykule opisano, jak działa automatyczne, tworzenie kopii zapasowych online i przywracanie danych na żądanie w usłudze Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: f5cc4339d7d6dce6d49c8d3eb744fca7fa5774d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240435"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Tworzenie kopii zapasowych i przywracanie danych na żądanie w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB automatycznie wykonuje kopie zapasowe danych w regularnych odstępach czasu. Automatyczne kopie zapasowe są pobierane bez wpływu na wydajność lub dostępność operacji bazy danych. Wszystkie kopie zapasowe są przechowywane oddzielnie w usłudze magazynu, a te kopie zapasowe są replikowane globalnie w celu zapewnienia odporności na awarie regionalne. Automatyczne kopie zapasowe są przydatne w scenariuszach, gdy przypadkowo usunąć lub zaktualizować konto usługi Azure Cosmos, bazy danych lub kontenera, a później wymagają odzyskiwania danych.

## <a name="automatic-and-online-backups"></a>Automatyczne i tworzenie kopii zapasowych online

Dzięki usłudze Azure Cosmos DB nie tylko twoje dane, ale także kopie zapasowe danych są wysoce nadmiarowe i odporne na awarie regionalne. Poniższe kroki pokazują, jak usługa Azure Cosmos DB wykonuje tworzenie kopii zapasowych danych:

* Usługa Azure Cosmos DB automatycznie wykonuje kopię zapasową bazy danych co 4 godziny, a w dowolnym momencie są przechowywane tylko 2 ostatnie kopie zapasowe. Jeśli jednak kontener lub baza danych zostanie usunięta, usługa Azure Cosmos DB zachowuje istniejące migawki danego kontenera lub bazy danych przez 30 dni.

* Usługa Azure Cosmos DB przechowuje te kopie zapasowe w magazynie obiektów Blob platformy Azure, podczas gdy rzeczywiste dane znajdują się lokalnie w usłudze Azure Cosmos DB.

*  Aby zagwarantować małe opóźnienia, migawka kopii zapasowej jest przechowywana w magazynie obiektów Blob platformy Azure w tym samym regionie co bieżący region zapisu (lub jeden z regionów zapisu, w przypadku gdy masz konfigurację wielu wzorców) konta bazy danych usługi Azure Cosmos. W przypadku odporności na awarie regionalne każda migawka danych kopii zapasowej w magazynie obiektów Blob platformy Azure jest ponownie replikowana do innego regionu za pośrednictwem magazynu geograficznie nadmiarowego (GRS). Region, do którego replikowana jest kopia zapasowa, jest oparty na regionie źródłowym i parze regionalnej skojarzonej z regionem źródłowym. Aby dowiedzieć się więcej, zobacz [listę par geograficznie nadmiarowych regionów platformy Azure.](../best-practices-availability-paired-regions.md) Nie można uzyskać dostępu do tej kopii zapasowej bezpośrednio. Usługa Azure Cosmos DB użyje tej kopii zapasowej tylko wtedy, gdy zainicjowane jest przywracanie kopii zapasowej.

* Kopie zapasowe są pobierane bez wpływu na wydajność lub dostępność aplikacji. Usługa Azure Cosmos DB wykonuje tworzenie kopii zapasowych danych w tle bez konieczności korzystania z dodatkowej aprowizowanej przepływności (RUs) lub wpływu na wydajność i dostępność bazy danych.

* Jeśli przypadkowo usunięto lub uszkodziłeś dane, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/) w ciągu 8 godzin, aby zespół usługi Azure Cosmos DB mógł pomóc w przywróceniu danych z kopii zapasowych.

Na poniższej ilustracji pokazano, jak kontener usługi Azure Cosmos ze wszystkimi trzema podstawowymi partycjami fizycznymi w zachodnie stany USA jest archiwizowana na zdalnym koncie usługi Azure Blob Storage w zachodnie stany USA, a następnie replikowana do wschodnich stanów USA:

![Okresowe pełne kopie zapasowe wszystkich encji usługi Cosmos DB w usłudze GRS Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Opcje zarządzania własnymi kopiami zapasowymi

Za pomocą kont interfejsu API SQL usługi Azure Cosmos DB można również obsługiwać własne kopie zapasowe przy użyciu jednego z następujących metod:

* Użyj [usługi Azure Data Factory,](../data-factory/connector-azure-cosmos-db.md) aby okresowo przenosić dane do wybranego magazynu.

* Użyj źródła danych [zmian](change-feed.md) usługi Azure Cosmos DB, aby okresowo odczytywać dane w celu uzyskania pełnych kopii zapasowych, a także zmian przyrostowych i przechowywać je we własnym magazynie.

## <a name="backup-retention-period"></a>Okres przechowywania kopii zapasowej

Usługa Azure Cosmos DB wykonuje migawki danych co cztery godziny. W danym momencie tylko dwa ostatnie migawki są zachowywane. Jeśli jednak kontener lub baza danych zostanie usunięta, usługa Azure Cosmos DB zachowuje istniejące migawki danego kontenera lub bazy danych przez 30 dni.

## <a name="restoring-data-from-online-backups"></a>Przywracanie danych z kopii zapasowych online

Przypadkowe usunięcie lub modyfikacja danych może nastąpić w jednym z następujących scenariuszy:  

* Całe konto usługi Azure Cosmos zostanie usunięte

* Co najmniej jedna baza danych usługi Azure Cosmos zostanie usunięta

* Jeden lub więcej kontenerów usługi Azure Cosmos jest usuwanych

* Elementy usługi Azure Cosmos (na przykład dokumenty) w kontenerze są usuwane lub modyfikowane. Ten konkretny przypadek jest zazwyczaj określany jako "uszkodzenie danych".

* Baza danych ofert udostępnionych lub kontenery w bazie danych oferty udostępnionej są usuwane lub uszkodzone

Usługa Azure Cosmos DB można przywrócić dane we wszystkich powyższych scenariuszach. Proces przywracania zawsze tworzy nowe konto usługi Azure Cosmos na potrzeby przechowywania przywróconych danych. Nazwa nowego konta, jeśli nie zostanie określona, będzie miała format `<Azure_Cosmos_account_original_name>-restored1`. Ostatnia cyfra jest zwiększana, jeśli zostanie podjęta próba wielokrotnego przywracania. Nie można przywrócić danych do wstępnie utworzonego konta usługi Azure Cosmos.

Po usunięciu konta usługi Azure Cosmos możemy przywrócić dane do konta o tej samej nazwie, pod warunkiem, że nazwa konta nie jest używana. W takich przypadkach zaleca się, aby nie ponownie utworzyć konto po usunięciu, ponieważ nie tylko uniemożliwia przywrócone dane do używania tej samej nazwy, ale także sprawia, że odkrywanie odpowiedniego konta, aby przywrócić z trudniejsze. 

Po usunięciu bazy danych usługi Azure Cosmos można przywrócić całą bazę danych lub podzbiór kontenerów w tej bazie danych. Istnieje również możliwość wybrania kontenerów w bazach danych i przywrócenia ich, a wszystkie przywrócone dane są umieszczane na nowym koncie usługi Azure Cosmos.

Gdy jeden lub więcej elementów w kontenerze są przypadkowo usunięte lub zmienione (przypadek uszkodzenia danych), należy określić czas, aby przywrócić do. Czas jest w tym przypadku najważniejszy. Ponieważ kontener jest na żywo, kopia zapasowa jest nadal uruchomiona, więc jeśli czekać poza okres przechowywania (domyślnie jest osiem godzin) kopie zapasowe zostaną zastąpione. W przypadku usuwania dane nie są już przechowywane, ponieważ nie zostaną zastąpione przez cykl tworzenia kopii zapasowej. Kopie zapasowe usuniętych baz danych lub kontenerów są zapisywane przez 30 dni.

Jeśli aprowizujesz przepływność na poziomie bazy danych (czyli gdy zestaw kontenerów współumieje aprowizowaną przepływność), proces tworzenia kopii zapasowych i przywracania w tym przypadku odbywa się na poziomie całej bazy danych, a nie na poziomie poszczególnych kontenerów. W takich przypadkach wybranie podzbioru kontenerów do przywrócenia nie jest opcją.

## <a name="migrating-data-to-the-original-account"></a>Migrowanie danych na oryginalne konto

Głównym celem przywracania danych jest zapewnienie sposobu na odzyskanie wszelkich danych, które można usunąć lub zmodyfikować przypadkowo. Dlatego zaleca się, aby najpierw sprawdzić zawartość odzyskanych danych, aby upewnić się, że zawiera to, czego oczekujesz. Następnie pracuj nad migracją danych z powrotem na konto podstawowe. Chociaż można użyć przywróconego konta jako konta na żywo, nie jest to zalecana opcja, jeśli masz obciążenia produkcyjne.  

Poniżej przedstawiono różne sposoby migracji danych z powrotem do oryginalnego konta usługi Azure Cosmos:

* Korzystanie z [narzędzia do migracji danych usługi Cosmos DB](import-data.md)
* Korzystanie z [usługi Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Korzystanie z [kanału informacyjnego o zmianach](change-feed.md) w usłudze Azure Cosmos DB 
* Pisanie kodu niestandardowego

Usuń przywrócone konta zaraz po zakończeniu migracji, ponieważ będą one ponosić bieżące opłaty.

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się, jak przywrócić dane z konta usługi Azure Cosmos lub dowiedzieć się, jak migrować dane do konta usługi Azure Cosmos

* Aby złożyć żądanie przywrócenia, skontaktuj się z pomocą techniczną platformy Azure, [złóż bilet z witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Jak przywrócić dane z konta usługi Azure Cosmos](how-to-backup-and-restore.md)
* [Użyj źródła danych zmian usługi Cosmos DB,](change-feed.md) aby przenieść dane do usługi Azure Cosmos DB.
* [Użyj usługi Azure Data Factory,](../data-factory/connector-azure-cosmos-db.md) aby przenieść dane do usługi Azure Cosmos DB.

