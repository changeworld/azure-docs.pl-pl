---
title: Optymalizacja pod kątem programowania i testowania w Azure Cosmos DB
description: W tym artykule wyjaśniono, jak Azure Cosmos DB oferuje wiele opcji tworzenia i testowania usługi bezpłatnie.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 992d165d323aab79bb7b5475aa396d4432691530
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754897"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optymalizacja kosztów tworzenia i testowania w Azure Cosmos DB

W tym artykule opisano różne opcje używania Azure Cosmos DB do programowania i testowania pod kątem bezpłatnego kosztu.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulator Azure Cosmos DB (wersja do pobrania lokalnego)

[Azure Cosmos DB emulator](local-emulator.md) to lokalna wersja do pobrania, która naśladuje Azure Cosmos DB usługi w chmurze. Możesz pisać i testować kod, który używa interfejsów API Azure Cosmos DB, nawet jeśli nie masz połączenia sieciowego i nie poniesiesz żadnych kosztów. Emulator Azure Cosmos DB zapewnia środowisko lokalne do celów deweloperskich z wysoką dokładnością do usługi w chmurze. Możesz tworzyć i testować aplikację lokalnie, bez tworzenia subskrypcji platformy Azure. Gdy wszystko będzie gotowe do wdrożenia aplikacji w chmurze, zaktualizuj parametry połączenia w celu nawiązania połączenia z punktem końcowym Azure Cosmos DB w chmurze, a żadne inne modyfikacje nie są wymagane. Możesz również skonfigurować potok ciągłej integracji/ciągłego wdrażania [za pomocą zadania kompilacji emulatora Azure Cosmos DB](tutorial-setup-ci-cd.md) w usłudze Azure DevOps, aby uruchomić testy. Możesz zacząć od odwiedzenia artykułu [emulatora Azure Cosmos DB](local-emulator.md) .

## <a name="try-azure-cosmos-db-for-free"></a>Wypróbuj usługę Azure Cosmos DB bezpłatnie

Usługa [Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/) to bezpłatna usługa umożliwiająca tworzenie baz danych i kolekcji oraz eksperymentowanie z Azure Cosmos DB w chmurze. Nie musisz logować się na platformie Azure ani płacić za nie. Konta usługi try Azure Cosmos DB są dostępne przez ograniczony czas, obecnie przez 30 dni. Można je odnowić w dowolnym momencie. Wypróbuj Azure Cosmos DB konta, które ułatwiają ocenę Azure Cosmos DB, kompilowanie i testowanie aplikacji przy użyciu przewodników Szybki Start i samouczków. Możesz utworzyć demonstrację lub wykonać testy jednostkowe bez ponoszenia kosztów. Korzystając z usługi try Azure Cosmos DB w przypadku bezpłatnych kont, możesz bezpłatnie oszacować możliwości Azure Cosmos DB Premium, w tym gotowe Global Distribution, umowy SLA i model spójności. Można utworzyć bazę danych z maksymalnie 25 kontenerami Cosmos platformy Azure i 10 000 RU/s przepływności. Możesz uruchomić przykładową aplikację bez subskrybowania konta platformy Azure lub korzystania z karty kredytowej. Korzystając z bezpłatnych Azure Cosmos DB, możesz utworzyć konto platformy Azure Cosmos z obsługą wielu regionów i uruchomić aplikację w ciągu zaledwie kilku minut. Aby rozpocząć, zobacz sekcję [wypróbuj Azure Cosmos DB dla bezpłatnej](https://azure.microsoft.com/try/cosmosdb/) strony.

## <a name="azure-free-account"></a>Bezpłatne konto platformy Azure

Azure Cosmos DB jest uwzględniona na [koncie bezpłatnej platformy Azure](https://azure.microsoft.com/free), które oferuje bezpłatnie środki na korzystanie z platformy Azure i za pewien czas. W odniesieniu do Azure Cosmos DB to bezpłatne konto oferuje 5 GB pamięci masowej i 400 jednostek ru przepływności dla całego roku. Dzięki temu każdy deweloper może łatwo przetestować funkcje Azure Cosmos DB lub zintegrować ją z innymi usługami platformy Azure bez ponoszenia kosztów. Bezpłatne konto platformy Azure pozwala uzyskać środki na korzystanie z $200 USD za pierwsze 30 dni. Opłata nie zostanie naliczona, nawet jeśli zaczniesz korzystać z usług, dopóki nie zdecydujesz się na uaktualnienie. Aby rozpocząć, odwiedź stronę [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free) .

## <a name="next-steps"></a>Następne kroki

Możesz rozpocząć korzystanie z emulatora lub bezpłatnych kont Azure Cosmos DB z następującymi artykułami:

* Dowiedz się więcej [na temat optymalizacji na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [zrozumieniu Azure Cosmos DB rachunku](understand-your-bill.md)
* Dowiedz się więcej na temat [optymalizowania kosztu przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizowaniu kosztów magazynu](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizowaniu kosztów operacji odczytu i zapisu](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizowaniu kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej [na temat optymalizowania kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)

