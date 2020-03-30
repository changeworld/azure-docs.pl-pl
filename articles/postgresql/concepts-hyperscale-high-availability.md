---
title: Wysoka dostępność — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: Wysoka dostępność i koncepcje odzyskiwania po awarii
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975537"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Wysoka dostępność w usłudze Azure Database dla postgreSQL — hiperskala (Citus)

Wysoka dostępność (HA) pozwala uniknąć przestojów bazy danych, utrzymując repliki wstrzymania każdego węzła w grupie serwerów. Jeśli węzeł ulegnie awarii, hiperskala przełącza połączenia przychodzące z węzła, który uległ awarii, do jego gotowości. Tryb failover odbywa się w ciągu kilku minut, a promowane węzły zawsze mają świeże dane za pośrednictwem synchronicznym przesyłania strumieniowego replikacji PostgreSQL.

Aby skorzystać z usługi wysokiej jakości w węźle koordynatora, aplikacje bazy danych muszą wykrywać i ponawiać próby porzucania połączeń i nieudanych transakcji. Nowo promowany koordynator będzie dostępny z tym samym ciągiem połączenia.

Odzyskiwanie można podzielić na trzy etapy: wykrywanie, praca awaryjna i pełne odzyskiwanie.  Hiperskala przebiega okresowe kontrole kondycji w każdym węźle, a po czterech nieudanych sprawdzeniach określa, że węzeł jest w dół. Hiperskala następnie promuje stan węzła podstawowego (pracy awaryjnej) i przepisuje nowy tryb gotowości.
Rozpoczyna się replikacja przesyłania strumieniowego, dzięki którym nowy węzeł jest aktualny.  Po zrepliknięciu wszystkich danych węzeł osiągnął pełne odzyskiwanie.

### <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [włączyć wysoką dostępność](howto-hyperscale-high-availability.md) w grupie serwerów w hiperskali.
