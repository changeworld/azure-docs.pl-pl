---
title: Wysoka dostępność — usługa Azure Database for MySQL
description: Ten temat zawiera informacje o wysokiej dostępności podczas korzystania z usługi Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a793de35ffff84009d362f005e599b4419f0763f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532777"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Pojęcia o wysokiej dostępności w usłudze Azure Database for MySQL
Usługa Azure Database for MySQL zapewnia gwarantowany wysoki poziom dostępności. Umowa dotyczącej poziomu usług wspierana finansowo (SLA) wynosi 99,99% przy ogólnej dostępności. Nie ma praktycznie żadnego czasu przestoju aplikacji podczas korzystania z tej usługi.

## <a name="high-availability"></a>Wysoka dostępność
Model wysokiej dostępności (HA) jest oparty na wbudowanych mechanizmach awaryjnych w przypadku przerwania na poziomie węzła. Przerwanie na poziomie węzła może wystąpić z powodu awarii sprzętu lub w odpowiedzi na wdrożenie usługi.

Przez cały czas zmiany wprowadzone do usługi Azure Database dla serwera bazy danych MySQL występują w kontekście transakcji. Zmiany są rejestrowane synchronicznie w magazynie platformy Azure, gdy transakcja jest zatwierdzona. Jeśli wystąpi przerwanie na poziomie węzła, serwer bazy danych automatycznie tworzy nowy węzeł i dołącza magazyn danych do nowego węzła. Wszystkie aktywne połączenia są odrzucane, a wszelkie transakcje pokładowe nie są zatwierdzane.

## <a name="application-retry-logic-is-essential"></a>Logika ponawiania aplikacji jest niezbędna
Ważne jest, aby aplikacje bazy danych MySQL były tworzone do wykrywania i ponawiania prób porzucania połączeń i nieudanych transakcji. Gdy aplikacja ponawia próby, połączenie aplikacji jest przekierowywane w sposób nieprzejrzysty do nowo utworzonego wystąpienia, które przejmuje dla wystąpienia nie powiodło się.

Wewnętrznie na platformie Azure brama jest używana do przekierowywania połączeń do nowego wystąpienia. Po przerwie cały proces pracy awaryjnej trwa zwykle dziesiątki sekund. Ponieważ przekierowanie jest obsługiwane wewnętrznie przez bramę, zewnętrzny ciąg połączenia pozostaje taki sam dla aplikacji klienckich.

## <a name="scaling-up-or-down"></a>Skalowanie w górę lub w dół
Podobnie jak model wysokiej jakości, gdy usługa Azure Database for MySQL jest skalowana w górę lub w dół, tworzone jest nowe wystąpienie serwera o określonym rozmiarze. Istniejący magazyn danych jest odłączony od oryginalnego wystąpienia i dołączony do nowego wystąpienia.

Podczas operacji skalowania występuje przerwanie połączeń z bazą danych. Aplikacje klienckie są rozłączane, a otwarte niezakończone transakcje są anulowane. Gdy aplikacja kliencka ponawia ponawia połączenie lub nawiązuje nowe połączenie, brama kieruje połączenie do wystąpienia o nowym rozmiarze. 

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [radzeniu sobie z błędami łączności przejściowej](concepts-connectivity.md)
- Dowiedz się, jak [replikować dane za pomocą replik odczytu](howto-read-replicas-portal.md)
