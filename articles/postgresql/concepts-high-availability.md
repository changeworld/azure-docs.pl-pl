---
title: Pojęcia dotyczące wysokiej dostępności w bazie danych PostgreSQL Azure
description: Ten artykuł zawiera informacje o wysokiej dostępności, korzystając z bazy danych Azure PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 203a142a21153935e172508e62b813dca95468cb
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql"></a>Pojęcia dotyczące wysokiej dostępności w bazie danych PostgreSQL Azure
Baza danych Azure usługi PostgreSQL zapewnia gwarantowane wysoki poziom dostępności. Finansowo kopii umowy dotyczącej poziomu usług (SLA) jest 99,99% po ogólnej dostępności. Nie ma praktycznie żadnej aplikacji czas przestoju podczas korzystania z tej usługi.

## <a name="high-availability"></a>Wysoka dostępność
Model o wysokiej dostępności (HA) opiera się na wbudowane mechanizmy awaryjnej po wystąpieniu przerwy poziomu węzła. Przerwanie węzeł na poziomie może wystąpić z powodu awarii sprzętu lub w odpowiedzi na wdrożenie usługi.

Przez cały czas zmiany wprowadzone do bazy danych Azure dla serwera bazy danych PostgreSQL występuje w kontekście transakcji. Zmiany są rejestrowane w synchronicznie w magazynie Azure, gdy transakcja została przekazana. W przypadku przerwania węzeł na poziomie serwera bazy danych automatycznie tworzy nowy węzeł i dołącza magazynu danych do nowego węzła. Wszystkie aktywne połączenia są usuwane i wszystkich transakcji porządkowych nie są przekazywane.

## <a name="application-retry-logic-is-essential"></a>Ważne jest Logika ponawiania aplikacji
Jest PostgreSQL aplikacje baz danych są przeznaczone do wykrywania i spróbuj ponownie usunąć połączenia i nie powiodło się transakcji. Ponawia próby aplikacji, połączenia aplikacji niewidocznie jest przekierowywany do nowo utworzone wystąpienie, które ma dla tego wystąpienia nie powiodło się.

Na platformie Azure, brama jest używana wewnętrznie przekierowywanie połączeń do nowego wystąpienia. Po przerwaniu cały proces awaryjnej zwykle trwa dziesiątki sekund. Ponieważ przekierowania jest obsługiwany wewnętrznie przez bramę, ciąg połączenia zewnętrzne jest taka sama dla aplikacji klienckich.

## <a name="scaling-up-or-down"></a>Skalowanie w górę lub w dół
Podobnie jak w modelu wysokiej dostępności bazy danych Azure programu PostgreSQL skalowania w górę lub w dół, nowe wystąpienie serwera o określonym rozmiarze jest tworzony. Istniejący magazyn danych jest odłączona od oryginalnego wystąpienia i dołączona do nowego wystąpienia.

Podczas operacji skalowania odbywa się zakłócenia połączenia bazy danych. Aplikacje klienckie są odłączone i otwórz niezatwierdzone transakcje są anulowane. Gdy aplikacja kliencka ponowi próbę połączenia lub tworzy nowe połączenie, brama kieruje połączenia z wystąpieniem o nowym rozmiarze. 

## <a name="next-steps"></a>Kolejne kroki
- Aby zapoznać się z omówieniem usługi, zobacz [Azure bazy danych PostgreSQL — omówienie](overview.md)
