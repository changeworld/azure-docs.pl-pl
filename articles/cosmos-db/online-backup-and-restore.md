---
title: Tworzenie kopii zapasowej online i przywracanie za pomocą usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonywać automatyczne wykonywanie kopii zapasowej i przywracanie bazy danych Azure Cosmos DB.
keywords: tworzenia kopii zapasowych i przywracania kopii zapasowej online
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: cf4579705e5910f62ca07223cb16405140926119
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859204"
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Automatyczne tworzenie kopii zapasowej online i przywracanie za pomocą usługi Azure Cosmos DB
Usługa Azure Cosmos DB automatycznie wykonuje kopie zapasowe wszystkich danych w regularnych odstępach czasu. Automatyczne kopie zapasowe są wykonywane bez wywierania wpływu na wydajność lub dostępności operacje bazy danych. Wszystkie kopie zapasowe są przechowywane osobno w innej usługi storage, a te kopie zapasowe globalnie są replikowane w celu zapewnienia odporności na regionalnej awarii. Automatyczne kopie zapasowe są przeznaczone dla scenariuszy, gdy przypadkowego usunięcia kontenera usługi Cosmos DB i później wymagają odzyskiwanie danych lub rozwiązanie odzyskiwania po awarii.  

W tym artykule rozpoczyna się od krótkie przypomnienie nadmiarowości danych i dostępności w usłudze Cosmos DB, a następnie w tym artykule omówiono tworzenie kopii zapasowych. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Wysoka dostępność za pomocą usługi Cosmos DB — podsumowanie
Usługa cosmos DB została zaprojektowana jako [globalnie dystrybuowane](distribute-data-globally.md) — pozwala na skalowanie przepływności w wielu regionach platformy Azure wraz z zasady na podstawie trybu failover i przejrzyste międzyregionalnych interfejsów API. Usługa Azure Cosmos DB oferuje [umów SLA dotyczących dostępności 99,99%](https://azure.microsoft.com/support/legal/sla/cosmos-db) dla wszystkich kont w obrębie jednego regionu i wszystkich kont w wielu regionach za pomocą rozluźnionej spójności i dostępności na wszystkich multiregionalne konta baz danych do odczytu przez 99,999% czasu. Wszystkie operacje zapisu w usłudze Azure Cosmos DB są trwale zatwierdzana na dyskach lokalnych przez kworum replik w lokalnym centrum danych przed potwierdzeniem do klienta. Wysoką dostępność usługi Cosmos DB opiera się na magazyn lokalny i nie zależy od dowolnej technologii magazynu zewnętrznego. Ponadto jeśli Twoje konto bazy danych jest skojarzony z więcej niż jednego regionu platformy Azure, zapisu są replikowane w innych regionach, jak również. Aby skalować przepływność i dostęp do danych w małych opóźnień, może mieć wiele odczytywać regiony skojarzony z Twoim kontem bazy danych, jak chcesz. W każdym regionie odczytu (replikowanych) danych jest trwale trwała dla zestawu replik.  

Jak pokazano na poniższym diagramie, jednego kontenera usługi Cosmos DB jest [partycjonowanej w poziomie](partition-data.md). "Partycji" jest oznaczona koła na poniższym diagramie, a każda partycja dokonuje wysokiej dostępności za pomocą zestawu replik. Jest to Dystrybucja lokalna w jednym regionie platformy Azure (wskazywane przez oś X). Ponadto każda partycja (z jego odpowiedniego zestawu replik) to globalnie dystrybuowane w wielu regionach skojarzonych z Twoim kontem bazy danych (na przykład, na tej ilustracji trzech regionów — wschodnie stany USA, zachodnie stany USA i Indie środkowe). Zestawu"partycji" jest globalnie rozproszonych jednostek wchodzących w skład wielu kopii danych w każdym regionie (wskazywane przez oś Y). Priorytet można przypisać do określonych regionów skojarzonych z Twoim kontem bazy danych, i Cosmos DB będzie przezroczysty tryb failover do następnego regionu, w razie awarii. Można też ręcznie symulujące pracę awaryjną do testowania dostępności end-to-end aplikacji.  

Na poniższym obrazie przedstawiono wysokiego stopnia redundancy za pomocą usługi Cosmos DB.

![Wysoki stopień nadmiarowości, za pomocą usługi Cosmos DB](./media/online-backup-and-restore/redundancy.png)

![Wysoki stopień nadmiarowości, za pomocą usługi Cosmos DB](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>Kopie zapasowe pełne, automatyczne, online
Niestety po usunięciu Moje kontenera lub bazy danych! Za pomocą usługi Cosmos DB nie tylko dane, ale kopie zapasowe danych są również bardzo nadmiarowy i odporny na błędy do awarii regionalnej. Te zautomatyzowane kopie zapasowe są obecnie pobierane co cztery godziny i najnowszych dwie kopie zapasowe są przechowywane przez cały czas. Jeśli dane są przypadkowo usunięty lub uszkodzony, skontaktuj się z [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) w ciągu ośmiu godzin. 

Kopie zapasowe są wykonywane bez wywierania wpływu na wydajność lub dostępności operacje bazy danych. Usługa cosmos DB trwa kopii zapasowej w tle, bez używania usługi zainicjowanych jednostek zarezerwowanych lub wpływu na wydajność i bez wywierania wpływu na dostępność bazy danych. 

W przeciwieństwie do danych przechowywanych w usłudze Cosmos DB automatyczne kopie zapasowe są przechowywane w usłudze Azure Blob Storage. Aby zagwarantować niskie opóźnienia/wydajne przekazywania, migawki kopii zapasowej jest przekazywany do wystąpienia usługi Azure Blob storage, w tym samym regionie, co bieżącego regionu zapisu w programu Twojego konta bazy danych Cosmos DB. Aby zachować odporność względem regionalnej awarii każdej migawki kopii zapasowej danych w usłudze Azure Blob Storage ponownie jest replikowana magazyn geograficznie nadmiarowy (GRS) do innego regionu. Na poniższym diagramie przedstawiono, że całego kontenera usługi Cosmos DB (przy użyciu wszystkich trzech partycji podstawowych w regionie zachodnie stany USA, w tym przykładzie) kopia zapasowa jest tworzona w zdalnym konta usługi Azure Blob Storage w regionie zachodnie stany USA, a następnie GRS są replikowane w regionie wschodnie stany USA. 

Na poniższym obrazie przedstawiono okresowe pełne kopie zapasowe wszystkich jednostek usługi Cosmos DB w magazynie geograficznie Nadmiarowym usługi Azure Storage.

![Okresowe pełne kopie zapasowe wszystkich jednostek usługi Cosmos DB w magazynie geograficznie Nadmiarowym usługi Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Okres przechowywania kopii zapasowej
Zgodnie z powyższym opisem usługi Azure Cosmos DB trwa migawki danych na poziomie partycji co cztery godziny. W dowolnym momencie tylko dwie ostatnie migawki zostaną zachowane. Jednak usunięcie kontenera/bazy danych Azure Cosmos DB zachowuje istniejące migawki dla wszystkich usuniętych partycji w danym kontenerze/bazy danych przez 30 dni.

Interfejsu API SQL, jeśli chcesz zachować swoje własne migawki, możesz użyć eksportowania do formatu JSON opcji w usłudze Azure Cosmos DB [narzędzia migracji danych](import-data.md#export-to-json-file) do planowania dodatkowych kopii zapasowych.

> [!NOTE]
> Jeśli użytkownik "Aprowizowanie przepływności dla zestawu kontenerów na poziomie bazy danych" — należy pamiętać o przywracania odbywa się na poziomie pełnym konta bazy danych. Należy również upewnij się, że się w obrębie 8 godzin do zespołu pomocy technicznej, jeśli przypadkowo usunięto kontenera — zbierania/tabeli/wykresu, korzystając z tej nowej możliwości. 


## <a name="restoring-a-database-from-an-online-backup"></a>Przywracanie bazy danych z kopii zapasowej online

Jeśli przypadkowo usuniesz bazy danych lub kontenera, możesz to zrobić [bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) lub [z działem pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) przywrócić dane z ostatnich automatycznej kopii zapasowej. Pomoc techniczna platformy Azure jest dostępny dla wybranych planów tylko np. Standard, dla deweloperów, pomocy technicznej nie jest dostępna z planem Basic. Aby dowiedzieć się więcej o planach pomocy technicznej w różnych, zobacz [plany pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/) strony. 

Jeśli trzeba przywrócić bazę danych z powodu problemu z uszkodzeniem danych (w tym przypadki, w których są usuwane dokumenty znajdujące się w kontenerze), zobacz [obsługi uszkodzenie danych](#handling-data-corruption) jak należy wykonać dodatkowe kroki w celu zapobieżenia uszkodzone dane zastąpienie istniejących kopii zapasowych. Dla określonej migawki kopii zapasowej do przywrócenia Cosmos DB wymaga danych był dostępny na czas trwania cyklu tworzenia kopii zapasowych dla tej migawki.

## <a name="handling-data-corruption"></a>Obsługa uszkodzenie danych

Usługa Azure Cosmos DB zachowa ostatnie dwie kopie zapasowe wszystkich partycji w ramach konta bazy danych. Ten model działa lepiej, gdy kontener (zbiór dokumentów, wykres i tabela) lub bazy danych zostanie przypadkowo usunięty, ponieważ jedna z ostatniej wersji można przywrócić. Jednak w przypadku użytkowników może stanowić problem uszkodzenia danych, usługi Azure Cosmos DB może być uszkodzenie danych świadomości i jest możliwe, mogą zostać zastąpione istniejące kopie zapasowe uszkodzenia. 

Jak najszybciej wykryto uszkodzenie skontaktowanie pracownikowi pomocy technicznej za pomocą konta i kontenera informacji o bazie danych za pomocą przybliżony czas uszkodzenia. Kolejną akcję, które użytkownik może wykonać w przypadku programu uszkodzony (usuwanie danych/aktualizacja) użytkownika, należy usunąć uszkodzony kontenera (kolekcja/wykres/table) tak, aby kopie zapasowe są chronione przed zastąpieniem z uszkodzonych danych.  

Na poniższym obrazie przedstawiono tworzenie żądania pomocy technicznej do przywrócenia container(collection/graph/table) za pośrednictwem witryny Azure portal do przypadkowego usunięcia lub aktualizowania danych w kontenerze

![Przywróć kontenera omyłkowe aktualizacji lub usuwania danych w usłudze Cosmos DB](./media/online-backup-and-restore/backup-restore-support.png)

Po zakończeniu przywracania dla tego rodzaju scenariuszy — dane są przywracane na inne konto (z sufiksem "-przywrócić") i kontener. Przywracanie nie odbywa się mające na celu zapewnienie Państwo klienta, aby wykonać sprawdzanie poprawności danych i Przenieś dane zgodnie z potrzebami. Kontener przywróconej znajduje się w tym samym regionie przy użyciu tych samych (RUS) i zasad indeksowania. 

## <a name="next-steps"></a>Kolejne kroki

Aby replikować bazę danych w wielu centrach danych, zobacz [dystrybuować dane globalnie za pomocą usługi Cosmos DB](distribute-data-globally.md). 

Do pliku skontaktuj się z pomocą techniczną platformy Azure [bilet w witrynie Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

