---
title: Optymalizacja do tworzenia i testowania w usłudze Azure Cosmos DB
description: W tym artykule wyjaśniono, jak usługa Azure Cosmos DB oferuje wiele opcji tworzenia i testowania usługi bezpłatnie.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: f9cb18b66def144b84de708351743832d1831fbf
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967265"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optymalizacja tworzenia i testowania kosztów w usłudze Azure Cosmos DB

W tym artykule opisano różne opcje do użycia usługi Azure Cosmos DB do tworzenia i testowania za darmo kosztów.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulator usługi Azure Cosmos DB (wersja lokalnie do pobrania)

[Emulator usługi Azure Cosmos DB](local-emulator.md) jest lokalną wersję do pobrania, który naśladuje usługi Azure Cosmos DB w chmurze. Można napisać i testować kod, który używa interfejsów API usługi Azure Cosmos DB, nawet w przypadku braku połączenia sieciowego i bez ponoszenia żadnych kosztów. Emulator usługi Azure Cosmos DB zapewnia środowisko lokalne do celów programistycznych zapewniające wysoką wierność do usługi w chmurze. Można tworzyć i testować swoją aplikację lokalnie, bez tworzenia subskrypcji platformy Azure. Gdy wszystko będzie gotowe do wdrożenia aplikacji w chmurze, zaktualizuj parametry połączenia do łączenia z punktem końcowym usługi Azure Cosmos DB w chmurze, są wymagane nie inne zmiany. Możesz również [Skonfiguruj potok ciągłej integracji/ciągłego wdrażania przy użyciu emulatora usługi Azure Cosmos DB](tutorial-setup-ci-cd.md) zadania w DevOps platformy Azure do uruchamiania testów kompilacji. Możesz rozpocząć pracę, odwiedzając [emulatora usługi Azure Cosmos DB](local-emulator.md) artykułu.

## <a name="try-azure-cosmos-db-for-free"></a>Wypróbuj usługę Azure Cosmos DB bezpłatnie

[Wypróbuj bezpłatnie usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) to bezpłatny środowiska opłata, która pozwala na tworzenie bazy danych i kolekcji i Poeksperymentuj z usługi Azure Cosmos DB w chmurze. Nie masz do rejestracji na platformie Azure lub naliczana jest opłata za wszystkie. Konta try Azure Cosmos DB są dostępne przez ograniczony czas obecnie 30 dni. Można je odnowić w dowolnym momencie. Wypróbuj usługę Azure Cosmos DB kont można łatwo ocenić usługę Azure Cosmos DB, tworzenia i testowania aplikacji, korzystając z przewodników Szybki Start lub samouczki. Można utworzyć pokaz lub wykonać testowanie bez ponoszenia żadnych kosztów jednostek. Za pomocą Wypróbuj usługę Azure Cosmos DB dla bezpłatnych kont, będziesz w stanie ocenić funkcje premium usługi Azure Cosmos DB bezpłatnie, w tym gotowej do użycia modeli globalnej dystrybucji, umowy SLA i spójność. Bazę danych można utworzyć maksymalnie 25 kontenerów w usłudze Azure Cosmos i 10 000 jednostek RU/s przepływności. Przykładową aplikację można uruchomić bez subskrypcji na koncie platformy Azure lub przy użyciu karty kredytowej. Za pomocą Wypróbuj usługę Azure Cosmos DB bezpłatnie, można utworzyć konto usługi Azure Cosmos wielu regionów i uruchamianie aplikacji na nim w ciągu kilku minut. Aby rozpocząć pracę, zobacz [Wypróbuj bezpłatnie usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) strony.

## <a name="azure-free-account"></a>Bezpłatne konto platformy Azure

Usługa Azure Cosmos DB znajduje się w [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free), które oferuje kredytów systemu Azure a zasobami za darmo przez pewien czas. Specjalnie dla usługi Azure Cosmos DB to bezpłatne konto oferuje 5 GB pamięci masowej i 400 jednostek żądania przepływności aprowizowane za cały rok. To środowisko umożliwia Każdy deweloper może je łatwo testować funkcje usługi Azure Cosmos DB lub zintegrować ją z innymi usługami Azure kosztów. Za pomocą bezpłatnego konta platformy Azure otrzymasz środki w wysokości 200 USD do wydania w ciągu pierwszych 30 dni. Opłata zostanie naliczona, nawet, jeśli zaczniesz korzystać z usług, dopóki nie zdecydujesz się. Aby rozpocząć pracę, odwiedź stronę [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free) strony.

## <a name="next-steps"></a>Kolejne kroki

Możesz rozpocząć pracę przy użyciu emulatora lub bezpłatnymi kontami usługi Azure Cosmos DB z następujących artykułów:

* Dowiedz się więcej o [Optymalizacja na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [informacje o rachunku Azure Cosmos DB](understand-your-bill.md)
* Dowiedz się więcej o [optymalizacji kosztów przepustowości](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji koszt odczyty i zapisy](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizacji kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)

