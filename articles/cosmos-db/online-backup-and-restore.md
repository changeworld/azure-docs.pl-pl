---
title: Tworzenie kopii zapasowej online i przywracania bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonać automatycznego tworzenia kopii zapasowej i przywracanie bazy danych z bazy danych Azure rozwiązania Cosmos.
keywords: Kopia zapasowa i przywracanie, tworzenie kopii zapasowej online
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: dddb3311ff5db964494697d76967f74c863d84e1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34615040"
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Automatyczne tworzenie kopii zapasowej online i przywracania bazy danych Azure rozwiązania Cosmos
Azure DB rozwiązania Cosmos automatycznie wykonuje kopie zapasowe wszystkich danych w regularnych odstępach czasu. Automatycznego tworzenia kopii zapasowych woluminów bez wpływu na wydajność i dostępność operacji w bazie danych. Kopie zapasowe są przechowywane oddzielnie w innej usłudze magazynu, a te kopie zapasowe globalnie są replikowane w celu odporność regionalnej awarii. Automatyczne kopie zapasowe są przeznaczone dla scenariuszy przypadkowego usunięcia z kontenera DB rozwiązania Cosmos i później wymagają danych odzyskiwania lub rozwiązanie odzyskiwania po awarii.  

W tym artykule rozpoczyna się od szybkie drużyn danych nadmiarowości i dostępności w bazie danych rozwiązania Cosmos, a następnie omówiono tworzenie kopii zapasowych. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Wysoka dostępność rozwiązania Cosmos DB — podsumowanie
Rozwiązania cosmos bazy danych została zaprojektowana jako [globalnie rozproszone](distribute-data-globally.md) — umożliwia skalowanie przepływności w różnych regionach platformy Azure oraz zasad zmiennych przezroczysty wielu interfejsów API i pracy awaryjnej. Udostępnia bazę danych systemu Azure rozwiązania Cosmos [dostępności 99,99% SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) dla wszystkich kont w pojedynczym regionie i wszystkich kont w przypadku z rozluźnić spójności i 99,999% odczytu dostępności na wszystkich kontach w przypadku bazy danych. Wszystkie operacje zapisu w usłudze Azure DB rozwiązania Cosmos są trwale zatwierdzone na dyskach lokalnych przez kworum replik w obrębie centrum danych lokalnych przed potwierdzeniem do klienta. Należy pamiętać, że wysokiej dostępności rozwiązania Cosmos DB korzysta z magazynu lokalnego i działa niezależnie od wszelkich technologie magazynu zewnętrznego. Ponadto jeśli konta bazy danych jest skojarzony z więcej niż jeden region platformy Azure, programu zapisy są replikowane za pomocą innych regionów, a także. Aby skalować przepływność i dostępu do danych w małych opóźnień, może mieć wiele regionów skojarzonych z Twoim kontem bazy danych, według uznania w trybie odczytu. W każdym regionie odczytu () danych trwale jest trwała dla zestawu replik.  

Jak pokazano na poniższym diagramie, jeden kontener DB rozwiązania Cosmos jest [poziomie partycjonowanej](partition-data.md). "Partycji" jest oznaczona okrąg na poniższym diagramie, a każda partycja jest zyskuje dużą dostępność za pośrednictwem zestawu replik. To jest lokalne dystrybucji w pojedynczym regionie Azure (wskazywane przez oś X). Ponadto każda partycja (z jego odpowiedniego zestawu replik) jest globalnie dystrybuowane w różnych regionach skojarzonych z Twoim kontem bazy danych (na przykład, w tym ilustracji trzech regionach — wschodnie stany USA, zachodnie stany USA i Indie środkowe). "Ustaw partycji" jest globalnie rozproszone jednostki składającej się z wielu kopii danych w każdym regionie (wskazywane przez osi Y). Regiony skojarzonych z Twoim kontem bazy danych można przypisać priorytet i DB rozwiązania Cosmos niewidocznie zostaną przełączone awaryjnie do następnego regionu w przypadku awarii. Można też ręcznie symulujące pracę awaryjną do testowania dostępności end-to-end aplikacji.  

Na poniższym obrazie przedstawiono wysoki stopień nadmiarowość DB rozwiązania Cosmos.

![Wysoki stopień nadmiarowość DB rozwiązania Cosmos](./media/online-backup-and-restore/redundancy.png)

![Wysoki stopień nadmiarowość DB rozwiązania Cosmos](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>Kopie zapasowe pełne, automatyczne, online
Niestety po usunięciu Moje kontener lub bazy danych! Rozwiązania Cosmos DB nie tylko dane, ale kopie zapasowe danych są również bardzo nadmiarowe i odporność do regionalnej awarii. Te automatyczne kopie zapasowe są obecnie wykonane co cztery godziny, a najnowszy dwie kopie zapasowe są przechowywane przez cały czas. Jeśli dane są przypadkowo usunięte lub uszkodzony, skontaktuj się z [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) w ośmiu godzin. 

Tworzenie kopii zapasowych woluminów bez wpływu na wydajność i dostępność operacji w bazie danych. Rozwiązania cosmos bazy danych trwa tworzenie kopii zapasowej w tle bez korzystanie z RUs elastycznie i wpływu na wydajność i bez wpływu na dostępność bazy danych. 

W przeciwieństwie do danych, która znajduje się wewnątrz rozwiązania Cosmos DB automatycznego tworzenia kopii zapasowych są przechowywane w usłudze magazyn obiektów Blob Azure. W celu zagwarantowania Niskie opóźnienie/wydajne przekazywania, przekazaniu migawki kopii zapasowej na wystąpienie magazynu obiektów Blob platformy Azure, w tym samym regionie co bieżącego obszaru zapisu konta bazy danych DB rozwiązania Cosmos. Dla odporność regionalnej awarii każdej migawki kopii zapasowej danych w magazynie obiektów Blob Azure są ponownie replikowane za pomocą magazynu geograficznie nadmiarowego (GRS) do innego regionu. Na poniższym diagramie przedstawiono całego kontenera DB rozwiązania Cosmos (ze wszystkich trzech partycje podstawowe w zachodnie stany USA, w tym przykładzie) kopia zapasowa jest tworzona w zdalnym konta magazynu obiektów Blob Azure w zachodnie stany USA, a następnie GRS są replikowane do wschodnie stany USA. 

Na poniższym obrazie przedstawiono okresowe pełne kopie zapasowe wszystkich jednostek rozwiązania Cosmos bazy danych grs w warstwie usługi Azure Storage.

![Okresowe pełne kopie zapasowe wszystkich jednostek rozwiązania Cosmos bazy danych grs w warstwie usługi Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Okres przechowywania kopii zapasowych
Zgodnie z powyższym opisem bazy danych Azure rozwiązania Cosmos przyjmuje migawki danych na poziomie partycji co cztery godziny. W dowolnym momencie tylko ostatnie dwa migawki są zachowywane. Jednak usunięcie/bazie danych kolekcji możemy zachować istniejące migawek dla wszystkich usuniętych partycji w danej kolekcji/bazy danych przez 30 dni.

Dla interfejsu API SQL, jeśli chcesz zachować swoje własne migawki służy eksportu opcji JSON w usłudze Azure DB rozwiązania Cosmos [narzędzia migracji danych](import-data.md#export-to-json-file) można zaplanować dodatkowych kopii zapasowych.

> [!NOTE]
> Jeśli możesz "Przepływności udostępniania dla zestawu kontenerów na poziomie bazy danych" — należy pamiętać o przywracania odbywa się na poziomie pełnym konta bazy danych. Należy również upewnić dotrzeć w 8 godzin do pomocy, jeśli przypadkowo usunięto pozycję kontener — kolekcji/tabeli/graph korzystając z tej nowej możliwości. 


## <a name="restoring-a-database-from-an-online-backup"></a>Przywracanie bazy danych z kopii zapasowej online
Jeśli został przypadkowo usunięty z bazy danych lub kolekcji, możesz [pliku biletu pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) lub [z działem pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) przywrócić dane z ostatnich automatycznej kopii zapasowej. Pomoc techniczna platformy Azure jest dostępna dla wybranych planów tylko takich jak Standard, deweloperów, pomocy technicznej nie jest dostępna z podstawowego planu. Aby uzyskać informacje dotyczące obsługi różnych planów, zobacz [plany pomocy technicznej platformy Azure](https://azure.microsoft.com/en-us/support/plans/) strony. Jeśli trzeba przywrócić bazę danych z powodu problemu z uszkodzenie danych (w tym przypadków, w których są usuwane dokumentów w kolekcji), zobacz [obsługi uszkodzenie danych](#handling-data-corruption) jak należy wykonać dodatkowe czynności, aby zapobiec uszkodzonych danych zastąpienie istniejących kopii zapasowych. Określoną migawkę kopii zapasowej do przywrócenia bazy danych rozwiązania Cosmos wymaga dane były dostępne w czasie trwania cyklu tworzenia kopii zapasowych dla tej migawki.

## <a name="handling-data-corruption"></a>Obsługa uszkodzenie danych
Azure DB rozwiązania Cosmos zachowuje ostatnich dwóch kopii zapasowych każdej partycji w ramach konta bazy danych. Ten model dobrze działa w przypadku kontenerze (kolekcję dokumentów, wykres, tabelę) lub od momentu ostatniej wersji można przywrócić bazy danych zostaną przypadkowo usunięte. Jednak w przypadku, gdy użytkownicy mogą wprowadzać problem uszkodzenie danych, bazy danych Azure rozwiązania Cosmos może nie mieć świadomości uszkodzenie danych i jest możliwe, mogą zostać zastąpione istniejące kopie zapasowe uszkodzenia. Jak wykryto uszkodzenie użytkownika należy usunąć uszkodzony kontenera (kolekcji wykres/tabeli), aby tworzenie kopii zapasowych są chronione przed zastąpieniem z uszkodzone dane.

## <a name="next-steps"></a>Kolejne kroki

Replikację bazy danych w wielu centrach danych, zobacz [dystrybucji danych globalnie DB rozwiązania Cosmos](distribute-data-globally.md). 

Do pliku skontaktuj się z pomocą techniczną platformy Azure [założyć zgłoszenie w portalu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

