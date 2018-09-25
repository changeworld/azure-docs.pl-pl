---
title: Za pomocą usługi Azure Cosmos DB Multi-Master z bazami danych NoSQL typu open source
description: ''
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ea3a16cf6b5aa4e46ad401e59aacb4d936a7429a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963904"
---
# <a name="using-azure-cosmos-db-multi-master-with-open-source-nosql-databases"></a>Za pomocą usługi Azure Cosmos DB Multi-Master z bazami danych NoSQL typu open source

Obsługa wielu wzorców usługi Azure Cosmos DB stanowi implementację natywnych, po stronie serwera, która jest dostępna dla wszystkich ofert NoSQL typu open source, obsługiwane przez usługi Cosmos DB. Jest również dostępny dla wszystkich usługi Cosmos DB obsługiwanych zestawów SDK.

Usługa Azure Cosmos DB to pierwsza usługa na świecie oferują wielu wzorców pomoc techniczną dla tych baz danych NoSQL typu open source.

|Modelowanie  |Pomoc techniczna  |
|---------|---------|
|MongoDB  | Konfiguracja aktywne-aktywne  |
|Graph  | Konfiguracja aktywne-aktywne |
|Cassandra  | Konfiguracja aktywne-aktywne   |

## <a name="use-mongodb-clients-with-multi-master"></a>Klientów bazy danych MongoDB za pomocą Multi-Master

Funkcja wielu wzorców jest włączona dla konta interfejsu API usługi MongoDB w taki sam sposób, który jest włączony dla innych interfejsów API usługi Azure Cosmos DB. Po włączeniu Multi-Master dla interfejsu API usługi MongoDB kont każdego wystąpienia aplikacji klienckiej mogą wybrać jej preferowany region dla operacji odczytu i zapisu. Z punktu widzenia sterownik bazy danych MongoDB preferowany region klienta pojawi się jako zestawu głównej replik. W ten sposób każdy region rozproszonej bazy danych może działać jako zestawu podstawowej repliki. Multi-Master usługi Azure Cosmos DB można znacznie zmniejszyć opóźnienia zapisu dla globalnie rozproszonych aplikacji bazy danych MongoDB. 

### <a name="set-the-primary-region"></a>Ustaw dla regionu podstawowego

Każde wystąpienie klienta bazy danych MongoDB można wybrać regionu podstawowego, dodając `@<preferred_primary_region_name>` do pola "Nazwa aplikacji" zaakceptowane przez sterownik klienta bazy danych MongoDB. Większość sterowników zaakceptować w parametrach połączenia, takich jak:

`mongodb://fabrikam:KEY@fabrikam.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

Po nawiązaniu połączenia może wystąpić jeden z następujących przypadkach:

* Jeśli nazwa preferowany region jest dostępna jako regionu zapisu, następnie usługi Azure Cosmos DB wybiera go jako regionu podstawowego.

* Jeśli nie podano nazwy preferowanego regionu, następnie usługi Azure Cosmos DB wybiera domyślny region regionu podstawowego.

* Jeśli nie podano nazwy preferowany region, ale nie jest dostępna jako region zapisu dla konta bazy danych, usługi Azure Cosmos DB wybierze najbliższego regionu zapisu, która jest dostępna jako regionu podstawowego.

Niektóre sterowniki, takie jak sterownik NodeJS czy zawsze pierwszą kwestię zapisuje do hosta określony ciąg połączenia początkowego. Takie sterowniki aby upewnić się, że zapisy są kierowane do preferowanego regionu, wraz z nazwą aplikacji należy zmodyfikować nazwę DNS, w ciągu połączenia, aby zawierał on nazwę regionu. Upewnij się, że podajesz nazwę region, bez spacji. Na przykład:

`mongodb://fabrikam:KEY@fabrikam-westus.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

### <a name="conflict-resolution-mode"></a>Tryb rozwiązywania konfliktów

Tryb rozwiązywania konfliktów dla kont z interfejsem API MongoDB usługi Azure Cosmos DB jest zawsze ostatni składnik zapisywania — usługi wins, przy użyciu sygnatury czasowej serwera regionalnego, akceptowane zapisu.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule omówiono wielu wzorców obsługi dla kont interfejsem API MongoDB usługi Azure Cosmos DB. Następnie Przyjrzyj się następujące zasoby:

* [Jak włączyć Multi-Master dla kont usługi Azure Cosmos DB](enable-multi-master.md)

* [Opis rozwiązywania konfliktów w usłudze Azure Cosmos DB](multi-master-conflict-resolution.md)
