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
ms.openlocfilehash: 4aa5e4ff46eeaa4e8d8c723f626dd1f1193fd12a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281612"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Dane globalne dystrybucji za pomocą usługi Azure Cosmos DB

Uruchom wiele współczesnych aplikacji w skali globalnej. Te aplikacje są zawsze włączone i dostępne dla użytkowników na całym świecie. Zarządzanie globalna dystrybucja danych używanego przez te aplikacje, jednocześnie zapewniając wysoką wydajność i wysoka dostępność jest twardych problem. Usługa Azure Cosmos DB to usługa globalnie rozproszonej bazy danych, która zapewnia wysoką wydajność i wysokiej dostępności. Z tych powodów usługi Azure Cosmos DB jest najbardziej odpowiednia dla tych aplikacji w czasie rzeczywistym.

Usługa cosmos DB jest podstawowe usługi platformy Azure i jest on dostępny w wszystkich [regionów świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/) domyślnie. Firma Microsoft utrzymuje innowacyjne centra danych platformy Azure w regionach 54 + na całym świecie i nadal rosnących potrzeb klientów. Podczas tworzenia konta usługi Cosmos DB, możesz zdecydować, regiony, które powinny zostać wdrożone w. Firma Microsoft obsługuje usługi Cosmos DB usługi 24/7, dzięki czemu możesz skupić się na swoich aplikacjach.

Można skonfigurować jako globalnie rozproszone i dostępny we wszystkich regionach platformy Azure ponad 50 baz danych. Aby zmniejszyć czas oczekiwania, należy umieścić przybliża dane do lokalizacji użytkownika. Wybór wymaganych regionach zależy od globalny zasięg Twojej aplikacji i gdzie są Twoi użytkownicy. Usługa cosmos DB w sposób przezroczysty replikuje dane w ramach konta usługi skonfigurowanego regionów. Zapewnia pojedynczy obraz systemu bazy danych Cosmos i kontenerów, przez które aplikacji mogą odczytywać i zapisywać lokalnie. Za pomocą usługi Cosmos DB można dodać lub usunąć regiony skojarzony z Twoim kontem w dowolnym momencie. Aplikacji nie musi być wstrzymane lub ponownego wdrażania, aby dodać lub usunąć region. Kontynuuje zapewniające wysoką dostępność przez cały czas w związku z możliwościami multihostingu, które zapewnia usługa.

## <a name="key-benefits-of-global-distribution"></a>Do najważniejszych korzyści zapewnianych dystrybucja globalna

**Twórz aplikacje globalne aktywne aktywne**: możliwości wielu wzorców, co region to region zapisu (tylko do odczytu). Wzorzec wielu funkcji również gwarancje — nieograniczone zapisu elastycznej skalowalności, 99,999% dostępność odczytu i zapisu obsługiwane przez wszystkie wokół świata oraz gwarantowaną operacji odczytu/zapisu w mniej niż 10 milisekund w 99. percentylu.  

Dzięki za pomocą usługi Azure Cosmos DB międzyregionalnych interfejsów API aplikacji zna znajduje się najbliższy region i jest w stanie wysyłać żądań do tego regionu. Znajduje się najbliższy region jest identyfikowany bez wprowadzania żadnych zmian konfiguracji. Jak dodać i usunąć regiony z konta usługi Cosmos DB, aplikacja nie musi być ponownie wdrażana i kontynuuje zapewniające wysoką dostępność.

**Twórz aplikacje o wysokiej dynamice**: aplikacji można łatwo zaprojektować do wykonywania niemal w czasie rzeczywistym odczyty i zapisy, za pomocą milisekundowe opóźnienia względem wszystkich regionów, które zostały wybrane dla bazy danych.  Usługa Azure Cosmos DB obsługuje wewnętrznie replikacji danych między regionami w taki sposób, że zagwarantowane jest poziomu spójności, wybrany dla konta usługi Cosmos.

Wiele aplikacji będą mogli korzystać z ulepszeniami wydajności, które pochodzą z możliwością wykonania wielu regionów zapisu (lokalnie). Niektóre aplikacje, które wymagają wysokiego poziomu spójności wolą lejka wszystkie operacje zapisu w pojedynczym regionie. Aby zapewnić obsługę tych aplikacji, Cosmos DB obsługuje zarówno w jednym regionie, jak i konfiguracje w wielu regionach.

**Twórz aplikacje o wysokiej dostępności**: uruchamianie bazy danych w wielu regionach, zwiększa to dostępność bazy danych. Jeśli jeden region jest niedostępny, innych regionach będzie automatycznie obsługiwać żądań aplikacji. Usługa Azure Cosmos DB oferuje 99,999% dostępność odczytu i zapisu dla baz danych w wielu regionach.

**Ciągłość działania podczas awarii regionalnego**: usługi Azure Cosmos DB obsługuje [automatycznej pracy awaryjnej](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account) podczas regionalnej awarii. Ponadto podczas regionalnej awarii usługi Cosmos DB w dalszym ciągu Obsługa opóźnienia, dostępności, spójności i przepływności umowy SLA. Aby zagwarantować, że całej aplikacji o wysokiej dostępności, usługi Azure Cosmos DB oferuje ręcznej pracy awaryjnej z interfejsu API w celu symulacji awarii regionalnej. Za pomocą tego interfejsu API, można wykonać ćwiczenia ciągłości zwykłe czynności biznesowe.

**Globalne Odczyt i zapis skalowalność**: możliwości wielu wzorców, można elastycznie skalowanie odczytu i zapisu przepływności na całym świecie. Funkcja wielu wzorców gwarantuje, że przepływność, którą aplikacja konfiguruje się w bazie danych Azure Cosmos DB lub kontener jest wydana we wszystkich regionach i chroniony przez [finansowo umowy SLA](https://aka.ms/acdbsla).

**Wiele dokładnie zdefiniowanych modeli spójności**: protokołu replikacji usługi Azure Cosmos DB zaprojektowano w celu oferuje pięć modeli spójności dobrze zdefiniowanych, praktycznych i intuicyjnych. Każdy model spójności ma zależność między wydajnością a spójnością. Te modele spójności umożliwiają tworzenie aplikacji dystrybuowanych globalnie łatwe.

## <a id="Next Steps"></a>Następne kroki

Dowiedz się więcej o globalnej dystrybucji w następujących artykułach:

* [Dystrybucja globalna - kulisy](global-dist-under-the-hood.md)
* [Jak skonfigurować klientów dla wielu](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Jak usunąć regiony z konta usługi Cosmos](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Jak utworzyć zasady rozpoznawania konfliktu niestandardowego konta interfejsu API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)