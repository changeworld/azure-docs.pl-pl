---
title: Globalna dystrybucja danych za pomocą usługi Azure Cosmos DB
description: Więcej informacji na temat skalowana w skali replikacji geograficznej, Multi-Master, trybu failover oraz odzyskiwanie danych przy użyciu globalne bazy danych z usługi Azure Cosmos DB, to usługa globalnie dystrybuowanej, wielomodelowej bazy danych.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.openlocfilehash: 3599875f96c6bd79ecace1d59c3580027fab3168
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040357"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Dane globalne dystrybucji za pomocą usługi Azure Cosmos DB

Dzisiejsze aplikacje muszą być wysoce elastyczny i zawsze w trybie online. Uzyskanie z małymi opóźnieniami i wysoką dostępność, wystąpień te aplikacje muszą zostać wdrożone w centrach danych, które znajdują się blisko ich użytkowników. Te aplikacje są zazwyczaj wdrożone w wielu centrach danych i są nazywane globalnie rozproszonych. Globalnie dystrybuowane aplikacje muszą globalnie rozproszonej bazy danych, w sposób niewidoczny dla użytkownika można replikować dane w dowolnym miejscu na świecie aplikacji do działania na kopii danych, który znajduje się w pobliżu użytkowników. 

Usługa Azure Cosmos DB to usługa globalnie rozproszonej bazy danych, która została zaprojektowana w celu zapewnienia małe opóźnienia, elastyczną skalowalność przepływności, semantyka dobrze zdefiniowanych wyjaśnienie pojęcia spójności danych i wysoką dostępność. Krótko mówiąc Jeśli aplikacja wymaga gwarantowane krótki czas reakcji dowolnym miejscu na świecie, jeśli są wymagane, aby zawsze być w trybie online, a elastycznych i nieograniczonej skalowalności przepływność oraz Magazyn, należy wziąć pod uwagę tworzenia aplikacji za pomocą usługi Azure Cosmos DB.

Można skonfigurować bazy danych jako globalnie rozproszone i dostępny we wszystkich regionach platformy Azure. Aby zmniejszyć czas oczekiwania, umieść dane bliżej gdzie są Twoi użytkownicy. Wybór wymaganych regionach zależy od globalny zasięg Twojej aplikacji i gdzie są Twoi użytkownicy. Usługa Azure Cosmos DB w sposób przezroczysty replikuje dane w ramach Twojego konta do wszystkich regionów skojarzonych z Twoim kontem. Zapewnia pojedynczy obraz systemu globalnie rozproszonej bazy danych Azure Cosmos i kontenerów, które aplikacja może odczytywać i zapisywać lokalnie. 

Za pomocą usługi Azure Cosmos DB można dodać lub usunąć regiony skojarzony z Twoim kontem w dowolnym momencie. Aplikacja nie musi być wstrzymane lub ponownego wdrażania, aby dodać lub usunąć region. Kontynuuje zapewniające wysoką dostępność przez cały czas ze względu na możliwości funkcji wieloadresowości, które zapewnia usługa.

## <a name="key-benefits-of-global-distribution"></a>Do najważniejszych korzyści zapewnianych dystrybucja globalna

**Twórz aplikacje globalne aktywny aktywny.** Korzystając z funkcji wielu wzorców co region to region zapisu. Jest również do odczytu. Gwarantuje również funkcję wielu wzorców:

- Nieograniczone zapisu elastycznej skalowalności. 
- 99,999% dostępność odczytu i zapisu na całym świecie.
- Gwarantowana odczytów i zapisów w mniej niż 10 milisekund w 99. percentylu.

Za pomocą usługi Azure Cosmos DB wieloadresowości interfejsów API, aplikacji zapoznała znajduje się najbliższy region. Następnie może wysłać żądania do tego regionu. Znajduje się najbliższy region jest identyfikowany bez wprowadzania żadnych zmian konfiguracji. Jak dodać i usunąć regiony z konta usługi Azure Cosmos DB, aplikacja nie wymaga przeprowadzić ponowne wdrożenie. Aplikacja jest nadal o wysokiej dostępności.

**Twórz aplikacje o wysokiej dynamice.** Aplikację można łatwo przeznaczony do wykonywania niemal w czasie rzeczywistym operacji odczytu i zapisu. Może używać milisekundowe opóźnienia względem wszystkich regionów, który został wybrany dla bazy danych. Usługa Azure Cosmos DB obsługuje wewnętrznie replikacji danych między regionami. W rezultacie poziomu spójności, zaznaczone dla konta usługi Azure Cosmos DB jest gwarantowana.

Wiele aplikacji korzystają z ulepszeniami wydajności, które pochodzą z możliwością wykonania wielu regionów zapisu (lokalnie). Niektóre aplikacje, które wymagają wysokiego poziomu spójności wolą lejka wszystkie operacje zapisu w pojedynczym regionie. W przypadku tych aplikacji usługi Azure Cosmos DB obsługuje jednego regionu i konfiguracje w wielu regionach.

**Twórz aplikacje o wysokiej dostępności.** Uruchamianie bazy danych w wielu regionach, zwiększa to dostępność bazy danych. Jeśli jeden region jest niedostępny, w innych regionach będzie automatycznie obsługiwać żądań aplikacji. Usługa Azure Cosmos DB oferuje 99,999% dostępność odczytu i zapisu dla baz danych w wielu regionach.

**Zachować ciągłość działania podczas awarii regionalnego.** Usługa Azure Cosmos DB obsługuje [automatycznej pracy awaryjnej](how-to-manage-database-account.md#automatic-failover) podczas regionalnej awarii. Podczas regionalnej awarii usługi Azure Cosmos DB w dalszym ciągu Obsługa opóźnienia, dostępności, spójności i przepływności umowy SLA. Aby upewnić się, że całej aplikacji o wysokiej dostępności, usługi Azure Cosmos DB oferuje ręczna praca awaryjna interfejsu API do symulacji awarii regionalnej. Za pomocą tego interfejsu API, można wykonywać zwykłe czynności biznesowe ciągłości awarii.

**Skalowanie odczytu i zapisu przepływności globalnie.** Korzystając z funkcji wielu wzorców, można elastycznie skalowanie odczytu i zapisu przepływności na całym świecie. Funkcja wielu wzorców gwarantuje przepływność, którą aplikacja konfiguruje się w bazie danych Azure Cosmos DB lub kontener została wydana we wszystkich regionach. Przepływność jest również chroniony przez [finansowo umowy SLA](https://aka.ms/acdbsla).

**Wybierz z wiele dokładnie zdefiniowanych modeli spójności.** Protokół replikacji usługi Azure Cosmos DB oferuje pięć modeli spójności dobrze zdefiniowanych, praktycznych i intuicyjnych. Każdy model zawiera zależność między wydajnością a spójnością. Użyj tych modeli spójności, aby twórz globalnie dystrybuowane aplikacje z łatwością.

## <a id="Next Steps"></a>Następne kroki

Dowiedz się więcej o globalnej dystrybucji w następujących artykułach:

* [Dystrybucja globalna - kulisy](global-dist-under-the-hood.md)
* [Konfigurowanie klientów w ramach wieloadresowości](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Dodać lub usunąć regiony z Twojego konta usługi Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Tworzenie zasad rozwiązania konfliktu niestandardowe dla konta interfejsu API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)