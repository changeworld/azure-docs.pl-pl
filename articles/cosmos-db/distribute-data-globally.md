---
title: Globalna dystrybucja danych za pomocą usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Więcej informacji na temat skalowana w skali replikacji geograficznej, Multi-Master, trybu failover oraz odzyskiwanie danych przy użyciu globalne bazy danych z usługi Azure Cosmos DB, to usługa globalnie dystrybuowanej, wielomodelowej bazy danych.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 181a8ad7291a8e8a0aa2a8373985c8747bd4569b
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446827"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Dane globalne dystrybucji za pomocą usługi Azure Cosmos DB

Dzisiejsze aplikacje wymagają reagujących i zawsze w trybie online. Uzyskanie z małymi opóźnieniami i wysoką dostępność, wystąpień te aplikacje muszą zostać wdrożone w centrach danych, które znajdują się blisko swoich użytkowników. Te aplikacje są zazwyczaj wdrożone w wielu centrach danych i są nazywane globalnie rozproszonych. Globalnie dystrybuowane aplikacje muszą globalnie rozproszonej bazy danych, które przezroczyste można replikować dane w dowolnym miejscu na świecie aplikacji do działania na kopii danych, który znajduje się w pobliżu użytkowników. Usługa Azure Cosmos DB to usługa globalnie rozproszonej bazy danych, która zapewnia małe opóźnienia, elastyczną skalowalność przepływności, semantyka dobrze zdefiniowanych wyjaśnienie pojęcia spójności danych i wysoką dostępność. Krótko mówiąc Jeśli wymagania w zakresie aplikacji zagwarantować czasu szybką odpowiedź w dowolnym miejscu na świecie, jeśli wymaga, aby zawsze być w trybie online, a musi elastycznych i nieograniczonej skalowalności przepływność oraz Magazyn, należy rozważyć tworzenie aplikacji przy użyciu usługi Azure Cosmos DB.

Usługa Azure Cosmos DB jest podstawowe usługi platformy Azure i jest on dostępny w wszystkich [regionów świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/) domyślnie. Firma Microsoft utrzymuje innowacyjne centra danych platformy Azure w regionach 54 + na całym świecie i nadal regionalnych obecności w celu spełnienia rosnących potrzeb klientów. Podczas tworzenia konta usługi Azure Cosmos możesz zdecydować, regiony, które powinny zostać wdrożone w. Firma Microsoft obsługuje usługi Azure Cosmos DB usługi 24/7, dzięki czemu możesz skupić się na swoich aplikacjach.

Można skonfigurować bazy danych jako globalnie rozproszone i dostępny we wszystkich regionach platformy Azure. Aby zmniejszyć czas oczekiwania, należy umieścić dane bliżej gdzie są Twoi użytkownicy. Wybór wymaganych regionach zależy od globalny zasięg Twojej aplikacji i gdzie są Twoi użytkownicy. Usługa Azure Cosmos DB w sposób przezroczysty replikuje dane w ramach Twojego konta do wszystkich regionów skojarzonych z Twoim kontem. Zapewnia pojedynczy obraz systemu globalnie rozproszonej bazy danych Azure Cosmos i kontenerów, które aplikacja może odczytywać i zapisywać lokalnie. Za pomocą usługi Azure Cosmos DB można dodać lub usunąć regiony skojarzony z Twoim kontem w dowolnym momencie. Aplikacji nie musi być wstrzymane lub ponownego wdrażania, aby dodać lub usunąć region. Kontynuuje zapewniające wysoką dostępność przez cały czas w związku z możliwościami multihostingu, które zapewnia usługa.

## <a name="key-benefits-of-global-distribution"></a>Do najważniejszych korzyści zapewnianych dystrybucja globalna

**Twórz aplikacje globalne aktywne aktywne**: możliwości wielu wzorców, co region to region zapisu (tylko do odczytu). Wzorzec wielu funkcji również gwarancje — nieograniczone zapisu elastycznej skalowalności, 99,999% dostępność odczytu i zapisu obsługiwane przez wszystkie wokół świata oraz gwarantowaną operacji odczytu/zapisu w mniej niż 10 milisekund w 99. percentylu.  

Dzięki za pomocą usługi Azure Cosmos DB międzyregionalnych interfejsów API aplikacji zna znajduje się najbliższy region i jest w stanie wysyłać żądań do tego regionu. Znajduje się najbliższy region jest identyfikowany bez wprowadzania żadnych zmian konfiguracji. Jak dodać i usunąć regiony z konta usługi Azure Cosmos DB, aplikacja nie musi być ponownie wdrażana i kontynuuje zapewniające wysoką dostępność.

**Twórz aplikacje o wysokiej dynamice**: aplikacji można łatwo zaprojektować do wykonywania niemal w czasie rzeczywistym odczyty i zapisy, za pomocą milisekundowe opóźnienia względem wszystkich regionów, które zostały wybrane dla bazy danych.  Usługa Azure Cosmos DB obsługuje wewnętrznie replikacji danych między regionami w taki sposób, że zagwarantowane jest poziomu spójności, wybrany dla konta usługi Azure Cosmos.

Wiele aplikacji będą mogli korzystać z ulepszeniami wydajności, które pochodzą z możliwością wykonania wielu regionów zapisu (lokalnie). Niektóre aplikacje, które wymagają wysokiego poziomu spójności wolą lejka wszystkie operacje zapisu w pojedynczym regionie. Aby obsługiwać te aplikacje, usługi Azure Cosmos DB obsługuje zarówno w jednym regionie, jak i konfiguracje w wielu regionach.

**Twórz aplikacje o wysokiej dostępności**: uruchamianie bazy danych w wielu regionach, zwiększa to dostępność bazy danych. Jeśli jeden region jest niedostępny, innych regionach będzie automatycznie obsługiwać żądań aplikacji. Usługa Azure Cosmos DB oferuje 99,999% dostępność odczytu i zapisu dla baz danych w wielu regionach.

**Ciągłość działania podczas awarii regionalnego**: usługi Azure Cosmos DB obsługuje [automatycznej pracy awaryjnej](how-to-manage-database-account.md#automatic-failover) podczas regionalnej awarii. Ponadto podczas regionalnej awarii usługi Azure Cosmos DB w dalszym ciągu Obsługa opóźnienia, dostępności, spójności i przepływności umowy SLA. Aby zagwarantować, że całej aplikacji o wysokiej dostępności, usługi Azure Cosmos DB oferuje ręcznej pracy awaryjnej z interfejsu API w celu symulacji awarii regionalnej. Za pomocą tego interfejsu API, można wykonać ćwiczenia ciągłości zwykłe czynności biznesowe.

**Globalne Odczyt i zapis skalowalność**: możliwości wielu wzorców, można elastycznie skalowanie odczytu i zapisu przepływności na całym świecie. Funkcja wielu wzorców gwarantuje, że przepływność, którą aplikacja konfiguruje się w bazie danych Azure Cosmos DB lub kontener jest wydana we wszystkich regionach i chroniony przez [finansowo umowy SLA](https://aka.ms/acdbsla).

**Wiele dokładnie zdefiniowanych modeli spójności**: protokołu replikacji usługi Azure Cosmos DB zaprojektowano w celu oferuje pięć modeli spójności dobrze zdefiniowanych, praktycznych i intuicyjnych. Każdy model spójności ma zależność między wydajnością a spójnością. Te modele spójności umożliwiają tworzenie aplikacji dystrybuowanych globalnie łatwe.

## <a id="Next Steps"></a>Następne kroki

Dowiedz się więcej o globalnej dystrybucji w następujących artykułach:

* [Dystrybucja globalna - kulisy](global-dist-under-the-hood.md)
* [Jak skonfigurować klientów dla wielu](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Jak usunąć regiony z Twojego konta usługi Azure Cosmos](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Jak utworzyć zasady rozpoznawania konfliktu niestandardowego konta interfejsu API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)