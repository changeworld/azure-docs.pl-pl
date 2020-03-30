---
title: Jak przywrócić dane usługi Azure Cosmos DB z kopii zapasowej
description: W tym artykule opisano sposób przywracania danych usługi Azure Cosmos DB z kopii zapasowej, jak skontaktować się z pomocą techniczną platformy Azure w celu przywrócenia danych, kroki, które należy wykonać po przywróceniu danych.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 19ca835ca8211202cd358ac2ec3695675183a372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70240763"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Przywracanie danych z kopii zapasowej w usłudze Azure Cosmos DB 

Jeśli przypadkowo usuniesz bazę danych lub kontener, możesz [zgłosić bilet pomocy technicznej]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) lub zadzwonić do [pomocy technicznej platformy Azure,]( https://azure.microsoft.com/support/options/) aby przywrócić dane z automatycznych kopii zapasowych online. Pomoc techniczna platformy Azure jest dostępna tylko dla wybranych planów, takich jak **Standard**, **Developer**i planów wyższych od nich. Pomoc techniczna platformy Azure nie jest dostępna w planie **basic.** Aby dowiedzieć się więcej o różnych planach pomocy technicznej, zobacz stronę [Plany pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/) 

Aby przywrócić określoną migawkę kopii zapasowej, usługa Azure Cosmos DB wymaga, aby dane były dostępne przez cały cykl tworzenia kopii zapasowej dla tej migawki.

## <a name="request-a-restore"></a>Żądanie przywrócenia

Przed żądaniem przywrócenia należy uzyskać następujące szczegóły:

* Przygotuj swój identyfikator subskrypcji.

* Na podstawie tego, jak dane zostały przypadkowo usunięte lub zmodyfikowane, należy przygotować się do dodatkowych informacji. Zaleca się, że masz informacje dostępne z wyprzedzeniem, aby zminimalizować back-and-forth, które mogą być szkodliwe w niektórych przypadkach wrażliwych czasu.

* Jeśli całe konto usługi Azure Cosmos DB zostanie usunięte, należy podać nazwę usuniętego konta. Jeśli utworzysz inne konto o takiej samej nazwie jak usunięte konto, udostępnij je zespołowi pomocy technicznej, ponieważ pomaga to określić odpowiednie konto do wyboru. Zaleca się, aby złożyć różne bilety pomocy technicznej dla każdego usuniętego konta, ponieważ minimalizuje to błąd stanu przywracania.

* Jeśli jedna lub więcej baz danych zostaną usunięte, należy podać konto usługi Azure Cosmos, a także nazwy bazy danych usługi Azure Cosmos i określić, czy istnieje nowa baza danych o tej samej nazwie.

* Jeśli jeden lub więcej kontenerów zostaną usunięte, należy podać nazwę konta usługi Azure Cosmos, nazwy baz danych i nazwy kontenerów. I określ, czy istnieje kontener o tej samej nazwie.

* Jeśli przypadkowo usunięto lub uszkodziłeś dane, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/) w ciągu 8 godzin, aby zespół usługi Azure Cosmos DB mógł pomóc w przywróceniu danych z kopii zapasowych.
  
  * Jeśli przypadkowo usunięto bazę danych lub kontener, otwórz przypadek pomocy technicznej platformy Sev B lub Sev C Azure. 
  * Jeśli przypadkowo usunięto lub uszkodziłeś niektóre dokumenty w kontenerze, otwórz sprawę pomocy technicznej Sev A. 

Gdy wystąpi uszkodzenie danych i jeśli dokumenty w kontenerze zostaną zmodyfikowane lub usunięte, **usuń kontener tak szybko, jak to możliwe**. Usuwając kontener, można uniknąć usługi Azure Cosmos DB od zastępowania kopii zapasowych. Jeśli z jakiegoś powodu usunięcie nie jest możliwe, należy złożyć bilet tak szybko, jak to możliwe. Oprócz nazwy konta usługi Azure Cosmos, nazwy baz danych, nazwy kontenerów, należy określić punkt w czasie, do którego dane mogą być przywracane do. Ważne jest, aby być tak precyzyjnym, jak to możliwe, aby pomóc nam określić najlepsze dostępne kopie zapasowe w tym czasie. Ważne jest również, aby określić czas w czasie UTC. 

Poniższy zrzut ekranu ilustruje sposób tworzenia żądania pomocy technicznej dla kontenera(kolekcja/wykres/tabela), aby przywrócić dane przy użyciu witryny Azure portal. Podaj dodatkowe szczegóły, takie jak typ danych, cel przywracania, czas, w którym dane zostały usunięte, aby pomóc nam ustalić priorytety żądania.

![Tworzenie żądania pomocy technicznej kopii zapasowej przy użyciu witryny Azure Portal](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>Działania po przywróceniu

Po przywróceniu danych otrzymasz powiadomienie o nazwie nowego konta (zazwyczaj w `<original-name>-restored1`formacie) i czasie, w którym konto zostało przywrócone. Przywrócone konto będzie miało taką samą aprowizowaną przepływność, zasady indeksowania i znajduje się w tym samym regionie co oryginalne konto. Użytkownik, który jest administratorem subskrypcji lub coadmin, może zobaczyć przywrócone konto.

Po przywróceniu danych należy sprawdzić i sprawdzić poprawność danych na przywróconym koncie i upewnić się, że zawiera ona oczekiwaną wersję. Jeśli wszystko wygląda dobrze, należy przeprowadzić migrację danych z powrotem do oryginalnego konta przy użyciu [źródła danych usługi Azure Cosmos DB](change-feed.md) lub usługi Azure Data [Factory.](../data-factory/connector-azure-cosmos-db.md)

Zaleca się usunięcie kontenera lub bazy danych natychmiast po migracji danych. Jeśli nie usuniesz przywróconych baz danych lub kontenerów, poniosą one koszty jednostek żądań, magazynu i ruchu wychodzącego.

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się, jak przeprowadzić migrację danych z powrotem na oryginalne konto, korzystając z następujących artykułów:

* Aby złożyć żądanie przywrócenia, skontaktuj się z pomocą techniczną platformy Azure, [złóż bilet z witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Użyj źródła danych zmian usługi Cosmos DB,](change-feed.md) aby przenieść dane do usługi Azure Cosmos DB.

* [Użyj usługi Azure Data Factory,](../data-factory/connector-azure-cosmos-db.md) aby przenieść dane do usługi Azure Cosmos DB.
