---
title: Automatyczne online kopię zapasową danych na żądanie przywracanie w usłudze Azure Cosmos DB
description: W tym artykule opisano sposób automatycznego, online kopii zapasowej i danych na żądanie przywracania w usłudze Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6ed968b1613a96a2f4ab449c7b52488e066a38ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60930250"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Tworzenie kopii zapasowej online i danych na żądanie przywrócenia w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB automatycznie wykonuje kopie zapasowe danych w regularnych odstępach czasu. Automatyczne kopie zapasowe są wykonywane bez wywierania wpływu na wydajność i dostępność operacji bazy danych. Wszystkie kopie zapasowe są przechowywane osobno w usłudze magazynu, a te kopie zapasowe globalnie są replikowane w celu zapewnienia odporności na regionalnej awarii. Automatyczne kopie zapasowe są przydatne w scenariuszach, gdy przypadkowo usunąć lub zaktualizować konto usługi Azure Cosmos, bazy danych lub kontenera i później wymagają odzyskiwania danych.

## <a name="automatic-and-online-backups"></a>Automatyczne i w trybie online kopii zapasowych

Za pomocą usługi Azure Cosmos DB nie tylko dane, ale również kopie zapasowe danych są mocno nadmiarowy i odporna na regionalnej awarii. Poniższe kroki pokazują, jak usługa Azure Cosmos DB wykonuje kopię zapasową danych:

* Usługa Azure Cosmos DB automatycznie wykonuje kopię zapasową bazy danych co 4 godziny, i w dowolnym momencie, są przechowywane tylko najnowsze 2 kopie zapasowe. Jednak usunięcie kontenera lub bazy danych Azure Cosmos DB zachowuje istniejące migawki wybranego kontenera lub bazy danych przez 30 dni.

* Usługa Azure Cosmos DB przechowuje te kopie zapasowe w usłudze Azure Blob storage, podczas gdy rzeczywisty znajdują się dane lokalnie w usłudze Azure Cosmos DB.

*  W celu zagwarantowania małych opóźnień, migawki kopii zapasowej znajduje się w usłudze Azure Blob storage, w tym samym regionie, co bieżącego regionu zapisu (lub jeden z regionów zapisu w przypadku, gdy używana jest Konfiguracja wielu wzorców) z usługi Cosmos Azure konto bazy danych. Aby zachować odporność względem regionalnej awarii każdej migawki kopii zapasowej danych w usłudze Azure Blob storage ponownie są replikowane do innego regionu za pośrednictwem magazyn geograficznie nadmiarowy (GRS). Region, na którym kopii zapasowej są replikowane zależy od regionu źródłowego i pary regionalnej skojarzony w regionie źródłowym. Aby dowiedzieć się więcej, zobacz [lista magazynu geograficznie nadmiarowego par regionów świadczenia usługi Azure](../best-practices-availability-paired-regions.md) artykułu. Nie masz dostępu do tej kopii zapasowej bezpośrednio. Usługa Azure Cosmos DB będzie używać tej kopii zapasowej, tylko wtedy, gdy jest inicjowana przywracania kopii zapasowej.

* Kopie zapasowe są wykonywane bez wywierania wpływu na wydajność i dostępność aplikacji. Usługa Azure Cosmos DB wykonuje kopii zapasowej danych w tle bez używania żadnych dodatkowych aprowizowana przepływność (ru) lub mające wpływ na wydajność i dostępność bazy danych.

* Jeśli przypadkowo usunięty lub uszkodzone dane, należy skontaktować się ze [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) w 8 godzin, aby zespół usługi Azure Cosmos DB może pomagać przywracania danych z kopii zapasowych.

Na poniższej ilustracji przedstawiono sposób tworzenia kopii zapasowej w zdalnym konta usługi Azure Blob Storage w regionie zachodnie stany USA i następnie replikowane w regionie wschodnie stany USA kontenera usługi Azure Cosmos za pomocą wszystkie trzy podstawowe fizyczne partycje w regionie zachodnie stany USA:

![Okresowe pełne kopie zapasowe wszystkich jednostek usługi Cosmos DB w magazynie geograficznie Nadmiarowym usługi Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Funkcje umożliwiające zarządzanie własnymi kopiami zapasowymi

Dzięki kontom usługi interfejsu API SQL usługi Azure Cosmos DB można także utrzymać własnymi kopiami zapasowymi przy użyciu jednej z następujących metod:

* Użyj [usługi Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) okresowo przenoszenie danych do magazynu w wybranej.

* Użyj usługi Azure Cosmos DB [zestawienia zmian](change-feed.md) okresowo odczytywanie danych dla pełnych kopii zapasowych, a także zmiany przyrostowe i zapisz go w magazynie.

## <a name="backup-retention-period"></a>Okres przechowywania kopii zapasowej

Usługa Azure Cosmos DB trwa migawki danych co cztery godziny. W dowolnym momencie tylko dwie ostatnie migawki zostaną zachowane. Jednak usunięcie kontenera lub bazy danych Azure Cosmos DB zachowuje istniejące migawki wybranego kontenera lub bazy danych przez 30 dni.

## <a name="restoring-data-from-online-backups"></a>Przywracanie danych z kopii zapasowych online

Przypadkowe usunięcie lub modyfikowanie danych może się zdarzyć w jednym z następujących scenariuszy:  

* Całe konto usługi Azure Cosmos jest usuwany.

* Jeden lub więcej baz danych Azure Cosmos zostaną usunięte.

* Co najmniej jeden kontener Azure Cosmos zostaną usunięte.

* Usługa Azure Cosmos elementów (na przykład dokumenty) w kontenerze zostanie usunięty lub zmodyfikowany. Tym konkretnym przypadku jest zwykle określane jako "uszkodzenie danych".

* Usunięty lub uszkodzony oferty udostępnionej bazy danych lub kontenerów w ramach oferty udostępnionej bazy danych

Usługa Azure Cosmos DB można przywrócić dane w powyższych scenariuszy. Proces przywracania zawsze tworzy nowe konto usługi Azure Cosmos, aby pomieścić przywracane dane. Nazwa nowego konta, jeśli nie zostanie określony, będzie mieć format `<Azure_Cosmos_account_original_name>-restored1`. Ostatnia cyfra jest zwiększany, jeśli wiele operacje przywracania są próby. Nie można przywrócić dane do wstępnie utworzonego konta usługi Azure Cosmos.

Po usunięciu konta usługi Azure Cosmos firma Microsoft można przywrócić dane na konto o takiej samej nazwie, pod warunkiem, że nazwa konta nie jest używany. W takich przypadkach zaleca się nie ponownie utwórz konto po usunięciu, ponieważ nie tylko zapobiega przywróconych danych, aby użyć tej samej nazwie, ale sprawia, że odnajdywania odpowiednie konto można przywrócić z trudniejsze. 

Po usunięciu bazy danych Azure Cosmos jest możliwe przywrócenie całej bazy danych lub ich część kontenerów w ramach tej bazy danych. Jest również możliwe, aby zaznaczyć kontenerach w bazach danych i przywracanie ich, a wszystkie przywróconych danych znajduje się na nowym koncie usługi Azure Cosmos.

Gdy jeden lub więcej elementów w kontenerze zostaną przypadkowo usunięte lub zmienione (przypadek uszkodzenie danych), należy określić czas do przywrócenia. Czas jest essence dla tej sprawy. Ponieważ kontenera odbywa się na żywo, kopia zapasowa jest nadal uruchomione, więc Jeśli czekasz po upływie okresu przechowywania (wartość domyślna to osiem godzin) kopii zapasowych zostaną zastąpione. W przypadku usuwania jest nie jest już przechowywane dane ponieważ one nie zostać zastąpione przez cyklu tworzenia kopii zapasowych. Wykonywanie kopii zapasowych dla usuniętych baz danych lub kontenery są zapisywane przez 30 dni.

Jeśli zaprowizować przepływność na poziomie bazy danych (oznacza to, gdzie zestaw kontenerów udostępnia aprowizowanej przepływności), proces tworzenia kopii zapasowych i przywracania w tym przypadku się tak zdarzyć na poziomie całej bazy danych, a nie na poziomie poszczególnych kontenerów. W takich przypadkach wybranie podzestawu kontenerów na potrzeby przywracania nie jest opcją.

## <a name="migrating-data-to-the-original-account"></a>Migrowanie danych do oryginalnego konta

Podstawowym celem przywracanie danych jest zapewnienie odzyskać dane, które zostały usunięte lub zmodyfikowane przypadkowo. Dlatego zaleca się, najpierw sprawdzić zawartość odzyskane dane, aby upewnić się, że zawiera on są oczekiwane. Następnie pracy dotyczące migrowania danych do konta podstawowego. Chociaż jest możliwe użycie przywróconej konta jako konta na żywo, nie jest zalecana opcja w przypadku obciążeń produkcyjnych.  

Poniżej przedstawiono różne sposoby, aby przeprowadzić migrację danych do oryginalnego konta usługi Azure Cosmos:

* Za pomocą [narzędzia migracji danych usługi Cosmos DB](import-data.md)
* Za pomocą [usługi Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Za pomocą [zestawienia zmian](change-feed.md) w usłudze Azure Cosmos DB 
* Pisanie kodu niestandardowego

Usuń przywróconej kont zaraz po zakończeniu migracji, ponieważ będą one spowodować stałego naliczania opłat.

## <a name="next-steps"></a>Kolejne kroki

Następnie możesz dowiedzieć się o tym, jak przywrócić dane z konta usługi Azure Cosmos lub Dowiedz się, jak przeprowadzić migrację danych z kontem usługi Azure Cosmos

* Aby Przywracanie żądania, skontaktuj się z działem pomocy technicznej systemu Azure, [bilet w witrynie Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Jak przywrócić dane z konta usługi Azure Cosmos](how-to-backup-and-restore.md)
* [Zestawienia zmian usługi Cosmos DB użyj](change-feed.md) do przenoszenia danych do usługi Azure Cosmos DB.
* [Usługa Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) do przenoszenia danych do usługi Azure Cosmos DB.

