---
title: Wysoka dostępność — funkcja do skalowania (Citus) — Azure Database for PostgreSQL
description: Pojęcia dotyczące wysokiej dostępności i odzyskiwania po awarii
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975537"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Wysoka dostępność w Azure Database for PostgreSQL — skalowanie (Citus)

Duża dostępność zapobiega przerwom w działaniu baz danych dzięki przechowywaniu rezerwowych replik każdego węzła w grupie serwerów. Jeśli węzeł ulegnie awarii, Hiperskala przełączy połączenia przychodzące z uszkodzonego węzła na jego rezerwę. Przez kilka minut węzeł będzie działał w trybie failover, a dzięki synchronicznej replikacji przesyłania strumieniowego PostgreSQL awansowane węzły zawsze będą zawierały świeże dane.

Aby korzystać z wysokiej dostępności na węźle koordynatora, aplikacje bazy danych muszą wykrywać i ponawiać próby porzucenia połączeń i transakcji zakończonych niepowodzeniem. Nowo podwyższony koordynator będzie dostępny z tymi samymi parametrami połączenia.

Odzyskiwanie można podzielić na trzy etapy: wykrywanie, przełączanie do trybu failover i pełne odzyskiwanie.  Funkcja wieloskalowania uruchamia okresowe kontrole kondycji w każdym węźle, a po czterech nieprawidłowych sprawdzeniach określa, że węzeł nie działa. Skalowanie następnie promuje stan wstrzymania do stanu węzła podstawowego (tryb failover) i Inicjuje nowe wstrzymanie.
Rozpocznie się replikacja przesyłania strumieniowego, co spowoduje, że nowy węzeł jest aktualny.  Gdy wszystkie dane zostały zreplikowane, węzeł osiągnął pełne odzyskiwanie.

### <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [włączyć wysoką dostępność](howto-hyperscale-high-availability.md) w grupie serwerów w skali.
