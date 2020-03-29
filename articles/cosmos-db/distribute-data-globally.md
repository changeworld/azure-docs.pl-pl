---
title: Dystrybucja danych globalnie za pomocą usługi Azure Cosmos DB
description: Dowiedz się więcej o replikacji geograficznej na skalę planety, wieloszerko-wzorcowym, pracy awaryjnej i odzyskiwania danych przy użyciu globalnych baz danych z usługi Azure Cosmos DB, globalnie rozproszonej, wielomodelowej usługi bazy danych.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 84cd201cd758293082a61a87528332c7d8c58811
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264309"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Globalna dystrybucja danych przy użyciu usługi Azure Cosmos DB — przegląd

Dzisiejsze aplikacje muszą być bardzo dynamiczne i zawsze w trybie online. Aby zapewnić małe opóźnienia i wysoką dostępność, wystąpienia tych aplikacji powinny być wdrażane w centrach danych, które są blisko ich użytkowników. Te aplikacje są zazwyczaj wdrażane w wielu centrach danych i są wywoływane globalnie rozproszone. Globalnie dystrybuowane aplikacje muszą mieć globalnie dystrybuowaną bazę danych, która może w sposób przezroczysty replikować dane w dowolnym miejscu na świecie, aby umożliwić aplikacjom działanie na kopii danych znajdującej się blisko użytkowników. 

Usługa Azure Cosmos DB to globalnie rozproszona usługa bazy danych, która została zaprojektowana w celu zapewnienia małych opóźnień, elastycznej skalowalności przepływności, dobrze zdefiniowanej semantyki dla spójności danych i wysokiej dostępności. Krótko mówiąc, jeśli aplikacja wymaga gwarantowanego czasu szybkiej odpowiedzi w dowolnym miejscu na świecie, jeśli jest to wymagane, aby być zawsze w trybie online i wymaga nieograniczonej i elastycznej skalowalności przepływności i magazynu, należy utworzyć aplikację na usługi Azure Cosmos DB.

Można skonfigurować bazy danych, aby były dystrybuowane globalnie i dostępne w dowolnym regionie platformy Azure. Aby zmniejszyć opóźnienie, umieść dane w pobliżu miejsca, w którym znajdują się użytkownicy. Wybór wymaganych regionów zależy od globalnego zasięgu aplikacji i miejsca, w którym znajdują się użytkownicy. Usługa Cosmos DB w sposób przejrzysty replikuje dane do wszystkich regionów skojarzonych z kontem usługi Cosmos. Zapewnia pojedynczy obraz systemu globalnej rozproszonej bazy danych usługi Azure Cosmos i kontenerów, które aplikacja może odczytywać i zapisywać lokalnie. 

Za pomocą usługi Azure Cosmos DB można w dowolnym momencie dodać lub usunąć regiony skojarzone z Twoim kontem. Aplikacja nie musi być wstrzymana lub ponownie wydzielona, aby dodać lub usunąć region. Nadal jest bardzo dostępne przez cały czas ze względu na możliwości multi-homing, które usługa natywnie zapewnia.

![Topologia wdrażania o wysokiej dostępności](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Kluczowe korzyści płynące z globalnej dystrybucji

**Tworzenie globalnych aplikacji aktywnych i aktywnych.** Dzięki nowatorskiemu protokołowi replikacji wieloszantowej każdy region obsługuje zarówno zapisy, jak i odczyty. Funkcja multi-master umożliwia również:

- Nieograniczona elastyczna skalowalność zapisu i odczytu. 
- Dostępność i zapisy na całym świecie jest 99,999%.
- Gwarantowane odczyty i zapisy serwowane w mniej niż 10 milisekund na 99 percentylu.

Za pomocą azure Cosmos DB multi-homilii interfejsów API, aplikacja jest świadomy najbliższego regionu i może wysyłać żądania do tego regionu. Najbliższy region jest identyfikowany bez żadnych zmian konfiguracji. Jak dodać i usunąć regiony do i z konta usługi Azure Cosmos, aplikacja nie musi być ponownie rozmieszczony lub wstrzymane, nadal jest wysoce dostępne przez cały czas.

**Twórz wysoce responsywne aplikacje.** Aplikacja może wykonywać odczyty w czasie zbliżonym do rzeczywistego i zapisy względem wszystkich regionów wybranych dla bazy danych. Usługa Azure Cosmos DB wewnętrznie obsługuje replikację danych między regionami z gwarancjami poziomu spójności wybranego poziomu.

**Twórz aplikacje o wysokiej dostępności.** Uruchamianie bazy danych w wielu regionach na całym świecie zwiększa dostępność bazy danych. Jeśli jeden region jest niedostępny, inne regiony automatycznie obsługują żądania aplikacji. Usługa Azure Cosmos DB oferuje dostępność odczytu i zapisu w wielu regionach na 99,999%.

**Zachowaj ciągłość działania podczas regionalnych awarii.** Usługa Azure Cosmos DB obsługuje [automatyczne tryb failover](how-to-manage-database-account.md#automatic-failover) podczas regionalnej awarii. Podczas regionalnej awarii usługa Azure Cosmos DB nadal utrzymuje swoje opóźnienia, dostępność, spójność i łącze SLA przepływności. Aby upewnić się, że cała aplikacja jest wysoce dostępna, usługa Cosmos DB oferuje ręczny interfejs API trybu failover w celu symulowania regionalnej awarii. Za pomocą tego interfejsu API, można przeprowadzić regularne ćwiczenia ciągłości biznesowej.

**Skaluj przepływność odczytu i zapisu globalnie.** Można włączyć każdy region do zapisu i elastycznie skalować odczyty i zapisy na całym świecie. Przepływność, którą aplikacja konfiguruje w bazie danych usługi Azure Cosmos lub kontener jest gwarantowana do dostarczenia we wszystkich regionach skojarzonych z kontem usługi Azure Cosmos. Aprowizowana przepustowość jest gwarantowana przez [zabezpieczone finansowo dl.](https://aka.ms/acdbsla)

**Wybierz jeden z kilku dobrze zdefiniowanych modeli spójności.** Protokół replikacji usługi Azure Cosmos DB oferuje pięć dobrze zdefiniowanych, praktycznych i intuicyjnych modeli spójności. Każdy model ma kompromis między spójności i wydajności. Użyj tych modeli spójności do tworzenia globalnie rozproszonych aplikacji z łatwością.

## <a name="next-steps"></a><a id="Next Steps"></a>Następne kroki

Więcej informacji na temat globalnej dystrybucji można przeczytać w następujących artykułach:

* [Dystrybucja globalna — szczegóły działania](global-dist-under-the-hood.md)
* [Jak skonfigurować multi-master w aplikacjach](how-to-multi-master.md)
* [Konfigurowanie klientów do multihomingu](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Dodawanie lub usuwanie regionów z konta usługi Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Tworzenie niestandardowych zasad rozwiązywania konfliktów dla kont interfejsu API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Programowalne modele spójności w usłudze Cosmos DB](consistency-levels.md)
* [Wybierz odpowiedni poziom spójności dla swojej aplikacji](consistency-levels-choosing.md)
* [Poziomy spójności w interfejsach API bazy danych usługi Azure Cosmos](consistency-levels-across-apis.md)
* [Kompromisy w zakresie dostępności i wydajności dla różnych poziomów spójności](consistency-levels-tradeoffs.md)

