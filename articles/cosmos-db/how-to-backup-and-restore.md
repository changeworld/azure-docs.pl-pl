---
title: Jak przywrócić dane z usługi Azure Cosmos DB z kopii zapasowej
description: W tym artykule opisano sposób przywracania danych usługi Azure Cosmos DB z kopii zapasowej, skontaktuj się z działem pomocy technicznej platformy Azure, aby przywrócić dane, jak kroki do wykonania po przywróceniu danych.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1d886e146e9e18eb735e6f88d2cb2c1a4a472924
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059640"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Przywracanie danych z kopii zapasowej w usłudze Azure Cosmos DB 

Jeśli przypadkowo usuniesz bazę danych lub kontener, możesz to zrobić [bilet pomocy technicznej]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) lub [z działem pomocy technicznej platformy Azure]( https://azure.microsoft.com/support/options/) do przywrócenia danych z automatycznych kopii zapasowych online. Pomoc techniczna platformy Azure jest dostępny dla wybranych planów tylko takie jak **standardowa**, **dla deweloperów**i planów wyższa niż ich. Pomoc techniczna platformy Azure nie jest dostępna z **podstawowe** planu. Aby dowiedzieć się więcej o planach pomocy technicznej w różnych, zobacz [plany pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/) strony. 

Aby przywrócić migawki określonej kopii zapasowej, usługi Azure Cosmos DB wymaga, czy dane są dostępne na czas trwania cyklu tworzenia kopii zapasowych dla tej migawki.

## <a name="request-a-restore"></a>Żądanie przywracania

Przed zażądaniem przywracania, powinny mieć następujące informacje:

* Mieć gotowy identyfikator swojej subskrypcji.

* Oparte na sposób danych została przypadkowo usunięta albo zmodyfikowana, należy przygotować mają dodatkowe informacje. Zaleca się, ma dostępnych informacji o wyprzedzeniem zminimalizować Wstecz i w przód, mogą być szkodliwe w niektórych przypadkach wrażliwe na czas.

* Usunięcie całego konta usługi Azure Cosmos DB, musisz podać nazwę usuniętego konta. Jeśli tworzysz innego konta z taką samą nazwę jak konto usunięte, udostępnianie ich zespołu pomocy technicznej ponieważ pomaga ono ustalić odpowiednie konto do wyboru. Zaleca się biletów pomocy technicznej w różnych plików dla każdego konta usuniętego ponieważ minimalizuje pomyłek stanu przywracania.

* Usunięcie co najmniej jednej bazy danych należy podać konto usługi Cosmos Azure, a także nazwy bazy danych Azure Cosmos i określ, czy nowej bazy danych o takiej samej nazwie istnieje.

* Jeśli co najmniej jeden kontener zostaną usunięte, należy podać nazwę konta usługi Azure Cosmos, nazwy baz danych i nazwy kontenera. I określ, czy kontener o takiej samej nazwie istnieje.

* Jeśli przypadkowo usunięty lub uszkodzone dane, należy skontaktować się ze [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) w 8 godzin, aby zespół usługi Azure Cosmos DB może pomagać przywracania danych z kopii zapasowych.
  
  * Jeśli została przypadkowo usunięta z bazy danych lub kontenera, należy otworzyć zgłoszenie do pomocy technicznej ważność B lub ważność C Azure. 
  * Jeśli przypadkowo usunięty lub uszkodzony niektórych dokumentów w kontenerze, należy otworzyć zgłoszenie do pomocy technicznej ważność A. 

Gdy wystąpi uszkodzenie danych, a dokumenty z kontenera zostaną zmodyfikowane lub usunięte, **jak najszybciej usunąć kontener**. Przez usunięcie kontenera, można uniknąć zastępowaniu kopie zapasowe usługi Azure Cosmos DB. Jeśli z jakiegoś powodu usunięcia nie jest możliwe, należy jak najszybciej założyć zgłoszenie. Oprócz nazwy konta usługi Azure Cosmos nazwy baz danych w przypadku nazw kolekcji należy określić punkt w czasie, do której można przywrócić dane do. Należy być dokładny jak to możliwe pomóc nam określić najlepszych dostępnych kopii zapasowych, w tym czasie. Jest również ważne określić godzinę w formacie UTC. 

Poniższy zrzut ekranu przedstawia sposób utworzenia żądania pomocy technicznej dla container(collection/graph/table) przywrócić dane przy użyciu witryny Azure portal. Zawierają dodatkowe szczegóły, takie jak typ danych, w celu przywracania, czas podczas dane zostały usunięte, aby pomóc nam określić priorytety żądania.

![Utwórz żądanie obsługi kopii zapasowych przy użyciu witryny Azure portal](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>Wykonywane po przywróceniu działania

Po przywróceniu danych otrzymasz powiadomienie o nazwę nowego konta (zwykle jest w formacie `<original-name>-restored1`) i godziny, jeśli konto zostało przywrócone do. Przywrócona konto będzie mieć ten sam aprowizowanej przepływności zasady indeksowania i jest w tym samym regionie co oryginalnego konta. Użytkownik będący administratorem subskrypcji lub coadmin widoczne przywróconych kont.

Po przywróceniu danych należy sprawdzić i sprawdzanie poprawności danych na koncie przywrócona i upewnij się, że zawiera on wersji, które są oczekiwane. Jeśli wszystko wygląda dobrze, należy przeprowadzić migrację danych do usługi oryginalnego konta przy użyciu [zestawienia zmian usługi Azure Cosmos DB](change-feed.md) lub [usługi Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

Zaleca się usunięcie kontenera lub bazy danych natychmiast po przeprowadzeniu migracji danych. Jeśli nie usuniesz przywróconej bazy danych lub kontenerów, spowoduje naliczenie kosztów, jednostek żądania, magazynu i ruchu wychodzącego.

## <a name="next-steps"></a>Kolejne kroki

Następnie możesz dowiedzieć się jak przeprowadzić migrację danych do oryginalnego konta za pomocą następujących artykułach:

* Aby Przywracanie żądania, skontaktuj się z działem pomocy technicznej systemu Azure, [bilet w witrynie Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Zestawienia zmian usługi Cosmos DB użyj](change-feed.md) do przenoszenia danych do usługi Azure Cosmos DB.

* [Usługa Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) do przenoszenia danych do usługi Azure Cosmos DB.
