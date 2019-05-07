---
title: Globalna dystrybucja danych za pomocą usługi Azure Cosmos DB
description: Więcej informacji na temat skalowana w skali replikacji geograficznej, Multi-Master, trybu failover oraz odzyskiwanie danych przy użyciu globalne bazy danych z usługi Azure Cosmos DB, to usługa globalnie dystrybuowanej, wielomodelowej bazy danych.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: b90e495e0901c0caa14d9451f365e17f6c075e2b
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65070804"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Dane globalne dystrybucji za pomocą usługi Azure Cosmos DB — omówienie

Dzisiejsze aplikacje muszą być bardzo dynamiczne i zawsze w trybie online. Aby zapewnić małe opóźnienia i wysoką dostępność, wystąpienia tych aplikacji powinny być wdrażane w centrach danych, które są blisko ich użytkowników. Te aplikacje są zazwyczaj wdrożone w wielu centrach danych i są nazywane globalnie rozproszonych. Globalnie dystrybuowane aplikacje muszą globalnie rozproszonej bazy danych, w sposób niewidoczny dla użytkownika można replikować dane w dowolnym miejscu na świecie aplikacji do działania na kopii danych, który znajduje się w pobliżu użytkowników. 

Usługa Azure Cosmos DB to usługa globalnie rozproszonej bazy danych, która została zaprojektowana w celu zapewnienia małe opóźnienia, elastyczną skalowalność przepływności, semantyka dobrze zdefiniowanych wyjaśnienie pojęcia spójności danych i wysoką dostępność. Krótko mówiąc Jeśli aplikacja wymaga gwarantowane krótki czas reakcji dowolnym miejscu na świecie, jeśli są wymagane, aby zawsze być w trybie online, a elastycznych i nieograniczonej skalowalności przepływność oraz Magazyn, powinien kompilujesz aplikację w usłudze Azure Cosmos DB.

Można skonfigurować bazy danych jako globalnie rozproszone i dostępny we wszystkich regionach platformy Azure. Aby zmniejszyć czas oczekiwania, umieść dane blisko gdzie są Twoi użytkownicy. Wybór wymaganych regionach zależy od globalny zasięg Twojej aplikacji i gdzie są Twoi użytkownicy. Usługa cosmos DB w sposób przezroczysty replikuje dane do wszystkich regionów skojarzonych z Twoim kontem Cosmos. Zapewnia pojedynczy obraz systemu globalnie rozproszonej bazy danych Azure Cosmos i kontenerów, które aplikacja może odczytywać i zapisywać lokalnie. 

Za pomocą usługi Azure Cosmos DB można dodać lub usunąć regiony skojarzony z Twoim kontem w dowolnym momencie. Aplikacja nie musi być wstrzymane lub ponownego wdrażania, aby dodać lub usunąć region. Kontynuuje zapewniające wysoką dostępność przez cały czas w związku z możliwościami multihostingu, które natywnie udostępnianych przez usługę.

![Topologia wdrożenia o wysokiej dostępności](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Do najważniejszych korzyści zapewnianych dystrybucja globalna

**Twórz aplikacje globalne aktywny aktywny.** Protokołu nowej replikacji wielu wzorców co region obsługuje odczytów i zapisów. Umożliwia także możliwości wielu wzorców:

- Nieograniczone elastyczne zapisu i odczytu skalowalności. 
- 99,999% dostępność odczytu i zapisu na całym świecie.
- Gwarantowana odczytów i zapisów w mniej niż 10 milisekund w 99. percentylu.

Za pomocą usługi Azure Cosmos DB multihosting interfejsów API, aplikacja zna znajduje się najbliższy region i mogą wysyłać żądania do tego regionu. Znajduje się najbliższy region jest identyfikowany bez wprowadzania żadnych zmian konfiguracji. Jak dodać i usunąć regiony z Twojego konta usługi Azure Cosmos i, aplikacja nie musi być ponownego wdrożenia lub wstrzymany, kontynuuje były wysoko dostępne przez cały czas.

**Twórz aplikacje o wysokiej dynamice.** Twoja aplikacja może wykonywać niemal w czasie rzeczywistym operacji odczytu i zapisu we wszystkich regionach, został wybrany dla bazy danych. Usługa Azure Cosmos DB obsługuje wewnętrznie replikacji danych między regionami przy użyciu poziomu gwarancji spójności poziomu, które wybrałeś.

**Twórz aplikacje o wysokiej dostępności.** Uruchamianie bazy danych w wielu regionach na całym świecie, zwiększa to dostępność bazy danych. Jeśli jeden region jest niedostępny, w innych regionach będzie automatycznie obsługiwać żądań aplikacji. Usługa Azure Cosmos DB oferuje 99,999% dostępność odczytu i zapisu dla baz danych w wielu regionach.

**Zachować ciągłość działania podczas awarii regionalnego.** Usługa Azure Cosmos DB obsługuje [automatycznej pracy awaryjnej](how-to-manage-database-account.md#automatic-failover) podczas regionalnej awarii. Podczas regionalnej awarii usługi Azure Cosmos DB w dalszym ciągu Obsługa opóźnienia, dostępności, spójności i przepływności umowy SLA. Aby upewnić się, że całej aplikacji o wysokiej dostępności, Cosmos DB oferuje ręczna praca awaryjna interfejsu API do symulacji awarii regionalnej. Za pomocą tego interfejsu API, można wykonywać zwykłe czynności biznesowe ciągłości awarii.

**Skalowanie odczytu i zapisu przepływności globalnie.** Można włączyć każdego regionu można było zapisywać i elastycznie Skaluj operacji odczytu i zapisu na całym świecie. Przepływność, którą aplikacji konfiguruje się dla bazy danych Azure Cosmos lub kontener jest gwarantowane do dostarczenia we wszystkich regionach skojarzonych z Twoim kontem usługi Azure Cosmos. Aprowizowana przepływność jest gwarantowany czas się [finansowo umowy SLA](https://aka.ms/acdbsla).

**Wybierz z wiele dokładnie zdefiniowanych modeli spójności.** Protokół replikacji usługi Azure Cosmos DB oferuje pięć modeli spójności dobrze zdefiniowanych, praktycznych i intuicyjnych. Każdy model zawiera zależność między wydajnością a spójnością. Użyj tych modeli spójności, aby twórz globalnie dystrybuowane aplikacje z łatwością.

## <a id="Next Steps"></a>Następne kroki

Dowiedz się więcej o globalnej dystrybucji w następujących artykułach:

* [Dystrybucja globalna - kulisy](global-dist-under-the-hood.md)
* [Jak skonfigurować Multi-Master w swoich aplikacjach](how-to-multi-master.md)
* [Konfigurowanie klientów w ramach wieloadresowości](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Dodać lub usunąć regiony z Twojego konta usługi Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Tworzenie zasad rozwiązania konfliktu niestandardowe dla konta interfejsu API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)