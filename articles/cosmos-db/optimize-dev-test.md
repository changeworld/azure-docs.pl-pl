---
title: Optymalizacja pod kątem programowania i testowania w usłudze Azure Cosmos DB
description: W tym artykule wyjaśniono, jak usługa Azure Cosmos DB oferuje wiele opcji tworzenia i testowania usługi za darmo.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: d2ca7b7e4b637802df6a78c2493e3cc088f09881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246684"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optymalizacja kosztów tworzenia i testowania w usłudze Azure Cosmos DB

W tym artykule opisano różne opcje korzystania z usługi Azure Cosmos DB do tworzenia i testowania bezpłatnie, a także technik optymalizacji kosztów w projektowaniu lub testowaniu kont.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulator usługi Azure Cosmos DB (wersja do pobrania lokalnie)

[Emulator usługi Azure Cosmos DB](local-emulator.md) to lokalna wersja do pobrania, która naśladuje usługę w chmurze usługi Azure Cosmos DB. Można napisać i przetestować kod, który używa interfejsów API usługi Azure Cosmos DB, nawet jeśli nie masz połączenia sieciowego i bez ponoszenia żadnych kosztów. Emulator usługi Azure Cosmos DB zapewnia środowisko lokalne do celów programisty z wysoką wiernością usługi w chmurze. Możesz tworzyć i testować aplikację lokalnie, bez tworzenia subskrypcji platformy Azure. Gdy będziesz gotowy do wdrożenia aplikacji w chmurze, zaktualizuj parametry połączenia, aby połączyć się z punktem końcowym usługi Azure Cosmos DB w chmurze, nie są potrzebne żadne inne modyfikacje. Można również [skonfigurować potok ciągłej integracji/ciągłego wdrażania za pomocą zadania kompilacji emulatora usługi Azure Cosmos DB](tutorial-setup-ci-cd.md) w usłudze Azure DevOps w celu uruchamiania testów. Możesz rozpocząć, odwiedzając artykuł [emulatora usługi Azure Cosmos DB.](local-emulator.md)

## <a name="azure-cosmos-db-free-tier"></a>Warstwa bezpłatna usługi Azure Cosmos DB 
Warstwa bezpłatna usługi Azure Cosmos DB ułatwia uruchamianie, opracowywanie i testowanie aplikacji, a nawet bezpłatne uruchamianie małych obciążeń produkcyjnych. Gdy warstwa bezpłatna jest włączona na koncie, otrzymasz pierwsze 400 RU/s i 5 GB miejsca na koncie za darmo. Można również utworzyć udostępnionej bazy danych przepływności z 25 kontenerów, które współużytkują 400 RU/s na poziomie bazy danych, wszystkie objęte warstwy bezpłatnej (limit 5 udostępnionych baz danych przepływności na koncie warstwy bezpłatnej). Warstwa bezpłatna trwa przez czas nieokreślony przez cały okres istnienia konta i zawiera wszystkie [zalety i funkcje](introduction.md#key-benefits) zwykłego konta usługi Azure Cosmos DB, w tym nieograniczoną ilość miejsca do magazynowania i przepływności (RU/s), łata, wysoką dostępność, globalną dystrybucję pod klucz we wszystkich regionach platformy Azure i inne. Możesz mieć maksymalnie jedno bezpłatne konto warstwy na subskrypcję platformy Azure i musisz wyrazić zgodę podczas tworzenia konta. Aby rozpocząć, [utwórz nowe konto z włączoną warstwą bezpłatną](create-cosmosdb-resources-portal.md). Więcej szczegółów można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/cosmos-db/). 

## <a name="try-azure-cosmos-db-for-free"></a>Wypróbuj usługę Azure Cosmos DB bezpłatnie

[Wypróbuj usługę Azure Cosmos DB za darmo](https://azure.microsoft.com/try/cosmosdb/) to bezpłatne środowisko, które umożliwia eksperymentowanie z usługą Azure Cosmos DB w chmurze bez konieczności logowania się do konta platformy Azure lub korzystania z karty kredytowej. Konta try usługi Azure Cosmos DB są dostępne przez ograniczony czas, obecnie 30 dni. Możesz je odnowić w dowolnym momencie. Wypróbuj konta usługi Azure Cosmos DB ułatwia ocenę usługi Azure Cosmos DB, tworzenie i testowanie aplikacji lub korzystanie z przewodników Szybki start lub samouczków. Można również utworzyć prezentację, przeprowadzić testy jednostkowe, a nawet utworzyć konto wieloregionowe i uruchomić na nim aplikację bez ponoszenia żadnych kosztów. Na koncie Try Azure Cosmos DB możesz mieć jedną udostępnioną bazę danych przepływności z maksymalnie 25 kontenerami i 20 000 przepływności lub jeden kontener z maksymalnie 5000 usługami RU/s. Aby rozpocząć, zobacz [Wypróbuj usługę Azure Cosmos DB, aby uzyskać bezpłatną](https://azure.microsoft.com/try/cosmosdb/) stronę.

## <a name="azure-free-account"></a>Bezpłatne konto platformy Azure

Usługa Azure Cosmos DB jest uwzględniona w [bezpłatnym koncie platformy Azure,](https://azure.microsoft.com/free)które oferuje bezpłatne środki i zasoby platformy Azure przez określony czas. W szczególności dla usługi Azure Cosmos DB to bezpłatne konto oferuje 5 GB pamięci masowej i 400 procesorów administracyjnych aprowizowanej przepływności przez cały rok. To środowisko umożliwia każdemu deweloperowi łatwe testowanie funkcji usługi Azure Cosmos DB lub integrowanie go z innymi usługami platformy Azure bez żadnych kosztów. Dzięki bezpłatnemu kontu platformy Azure otrzymasz środki w wysokości 200 USD do wydania w ciągu pierwszych 30 dni. Opłata nie zostanie naliczona, nawet jeśli zaczniesz korzystać z usług, dopóki nie zdecydujesz się na uaktualnienie. Aby rozpocząć, odwiedź stronę [bezpłatnego konta platformy Azure.](https://azure.microsoft.com/free)

## <a name="use-shared-throughput-databases"></a>Korzystanie z udostępnionych baz danych przepływności

W [udostępnionej bazie danych przepływności](set-throughput.md#set-throughput-on-a-database)wszystkie kontenery wewnątrz bazy danych współużytkują aprowizowaną przepływność (RU/s) bazy danych. Na przykład jeśli aprowizujesz bazę danych z 400 RU/s i mają cztery kontenery, wszystkie cztery kontenery będą współużytkować 400 RU/s. W środowisku deweloperskim lub testowym, w którym każdy kontener może być dostępny rzadziej, a zatem wymagają mniej niż minimum 400 ru/s, umieszczenie kontenerów w bazie danych przepływności udostępnionej może pomóc zoptymalizować koszt. 

Załóżmy na przykład, że konto dewelopera lub testu ma cztery kontenery. Jeśli utworzysz cztery kontenery z dedykowaną przepustowością (minimum 400 RU/s), łączna wartość RU/s wyniesie 1600 RU/s. Natomiast jeśli utworzysz udostępnioną bazę danych przepływności (minimum 400 RU/s) i umieścisz tam kontenery, łączna wartość RU/s wyniesie tylko 400 ru/s. Ogólnie rzecz biorąc udostępnione bazy danych przepływności są idealne dla scenariuszy, w których nie potrzebujesz gwarantowanej przepływności w dowolnym kontenerze.  Dowiedz się więcej o [udostępnionych bazach danych przepływności.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Następne kroki

Możesz rozpocząć korzystanie z emulatora lub bezpłatnych kont usługi Azure Cosmos DB z następującymi artykułami:

* Dowiedz się więcej o [optymalizacji pod kątem rozwoju i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [opisie rachunku za usługę Azure Cosmos DB](understand-your-bill.md)
* Dowiedz się więcej o [optymalizacji kosztów przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji kosztów odczytów i zapisów](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizowaniu kosztów wieloregionowych kont usługi Azure Cosmos](optimize-cost-regions.md)

