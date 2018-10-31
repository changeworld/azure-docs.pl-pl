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
ms.openlocfilehash: 68ac603964593892dfcbc3488b4e6f2c91a0eb92
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238278"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Dane globalne dystrybucji za pomocą usługi Azure Cosmos DB

Wiele współczesnych aplikacji ani ambicji do uruchamiania w skali globalnej. One są zawsze włączone i są dostępne dla użytkowników na całym świecie. Zarządzanie globalna dystrybucja danych używanego przez te aplikacje, jednocześnie zapewniając wysoką wydajność i wysoka dostępność jest twardych problem. Usługa cosmos DB to usługa globalnie rozproszonej bazy danych, która ma została zaprojektowana od podstaw aby spełnić te wyzwania.

Usługa cosmos DB jest podstawowe usługi platformy Azure i jest on dostępny w wszystkich [regionów świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/) domyślnie. Firma Microsoft utrzymuje innowacyjne centra danych platformy Azure w więcej niż 50 regionach na całym świecie i nadal rosnących potrzeb klientów. Firma Microsoft obsługuje usługi Cosmos DB usługi 24/7, dzięki czemu możesz skupić się na swoich aplikacjach. Podczas tworzenia konta usługi Cosmos DB, możesz zdecydować, regiony, które powinny zostać wdrożone w.

Cosmos DB klienci mogą skonfigurować swoje bazy danych jako globalnie rozproszone i dostępne w dowolnym miejscu z zakresu od 1 do ponad 50 regionach świadczenia usługi Azure. Aby zmniejszyć czas oczekiwania, możesz umieścić przybliża dane do lokalizacji użytkownika, więc podejmowania decyzji o ile regionów i w który region należy uruchomić zależy od globalnej dotrzeć do aplikacji i gdzie są Twoi użytkownicy. Usługa cosmos DB w sposób przezroczysty replikuje wszystkie dane w ramach konta usługi Cosmos skonfigurowanego regionów. Usługa cosmos DB zapewnia pojedynczego obrazu systemu bazy danych Cosmos i kontenerów, dzięki czemu aplikacji mogą odczytywać i zapisywać lokalnie. Za pomocą usługi Cosmos DB można dodać lub usunąć regiony skojarzony z Twoim kontem w dowolnym momencie. Aplikacja nie musi być wstrzymane lub ponownego wdrażania i kontynuuje zapewniające wysoką dostępność, przekazujący dane przez cały czas dzięki możliwościami multihostingu, które zapewnia usługa.

## <a name="key-benefits-of-global-distribution"></a>Do najważniejszych korzyści zapewnianych dystrybucja globalna

**Łatwo Twórz aplikacje globalne aktywne aktywne,**: możliwości wielu wzorców każdego regionu zapisu (tylko do odczytu), włączenie nieograniczone elastyczne zapisu skalowalność odczytu przez 99,999% czasu i zapisu, dostępność na całym świecie, a gwarantowaną szybkie odczyty i zapisy w więcej niż 10 milisekund w 99. percentylu.  

Za pomocą usługi Azure Cosmos DB międzyregionalnych interfejsów API aplikacji zawsze wie, znajduje się najbliższy region i wysyła żądania do tego regionu. Znajduje się najbliższy region jest identyfikowany bez wprowadzania żadnych zmian konfiguracji. Jak dodać i usunąć regiony z konta usługi Cosmos DB, aplikacja nie musi być ponownie wdrażana i kontynuuje zapewniające wysoką dostępność.

**Twórz aplikacje o wysokiej dynamice**: aplikacji można łatwo zaprojektować do wykonywania niemal w czasie rzeczywistym odczyty i zapisy, za pomocą milisekundowe opóźnienia, względem we wszystkich regionach zostały wybrane dla bazy danych.  Usługa Azure Cosmos DB obsługuje wewnętrznie replikacji danych między regionami w sposób zapewniający poziomu spójności, wybrany dla konta usługi Cosmos.

Wiele aplikacji będą mogli korzystać z ulepszeniami wydajności, które pochodzą z możliwością wykonania wielu regionów zapisu (lokalnie). Niektóre aplikacje, takie jak te, które wymagają wysoki poziom spójności, będzie łatwiej lejka wszystkie operacje zapisu w pojedynczym regionie. Usługa cosmos DB obsługuje konfiguracje, jednym regionie i wielu regionów.

**Twórz aplikacje o wysokiej dostępności**: uruchamianie bazy danych w wielu regionach poprawia dostępność bazy danych. Jeśli jeden region jest niedostępny, innych regionach będzie automatycznie obsługiwać żądań aplikacji. Usługa Azure Cosmos DB oferuje 99,999% dostępność odczytu i zapisu dla baz danych w wielu regionach.

**Ciągłość działania podczas awarii regionalnego**: usługi Azure Cosmos DB obsługuje [automatycznej pracy awaryjnej](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account) podczas regionalnej awarii. Ponadto podczas regionalnej awarii usługi Cosmos DB w dalszym ciągu Obsługa opóźnienia, dostępności, spójności i przepływności umowy SLA. Aby zagwarantować, że całej aplikacji o wysokiej dostępności, usługi Azure Cosmos DB oferuje ręcznej pracy awaryjnej z interfejsu API w celu symulacji awarii regionalnej. Za pomocą tego interfejsu API, można przeprowadzanie ciągłości zwykłe czynności biznesowe i będzie gotowa.

**Globalne Odczyt i zapis skalowalność**: możliwości wielu wzorców, można elastycznie skalowanie odczytu i zapisu przepływności na całym świecie. Funkcja wielu wzorców gwarantuje, że przepływność, którą aplikacja konfiguruje się w bazie danych Azure Cosmos DB lub kontener jest wydana we wszystkich regionach i chroniony przez [finansowo umowy SLA](https://aka.ms/acdbsla).

**Wiele dokładnie zdefiniowanych modeli spójności**: protokołu replikacji usługi Azure Cosmos DB zaprojektowano w celu oferuje pięć dobrze zdefiniowanych, praktycznych, intuicyjny spójności modele i każdy z wyczyść zależność między wydajnością a spójnością. Te modele spójności umożliwiają tworzenie aplikacji dystrybuowanych globalnie poprawne z łatwością.

## <a id="Next Steps"></a>Następne kroki

Dowiedz się więcej o globalnej dystrybucji w następujących artykułach:

* [Dystrybucja globalna - kulisy](global-dist-under-the-hood.md)
* [Jak skonfigurować klientów dla wielu](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Jak dodawanie/usuwanie regionów ze swojej bazy danych](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Jak utworzyć zasady rozpoznawania konfliktu niestandardowego konta interfejsu API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)