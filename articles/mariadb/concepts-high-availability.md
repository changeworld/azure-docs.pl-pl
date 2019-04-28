---
title: Pojęcia wysokiej dostępności w usłudze Azure Database dla serwera MariaDB
description: Ten temat zawiera informacje o wysokiej dostępności, korzystając z usługi Azure Database dla serwera MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: ba561cba21b0b83b6a19fffc2fdfebe8ec8b8ed9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61042150"
---
# <a name="high-availability-concepts-in-azure-database-for-mariadb"></a>Pojęcia wysokiej dostępności w usłudze Azure Database dla serwera MariaDB
Usługa Azure Database for MariaDB oferuje gwarantowaną wysoką dostępność. Finansowo umowy dotyczącej poziomu usług (SLA) wynosi 99,99% po ogólnym udostępnieniu. Nie ma praktycznie nie aplikacji, czas przestoju podczas korzystania z tej usługi.

## <a name="high-availability"></a>Wysoka dostępność
Model o wysokiej dostępności (HA) opiera się na wbudowane mechanizmy trybu failover, po wystąpieniu przerwania poziomu węzła. Przerwanie poziomu węzła może wystąpić z powodu awarii sprzętu lub w odpowiedzi na wdrożenie usługi.

Przez cały czas zmiany wprowadzone do usługi Azure Database dla serwera bazy danych MariaDB odbywają się w kontekście transakcji. Zmiany są rejestrowane w synchronicznie w usłudze Azure storage, gdy transakcja została zatwierdzona. W przypadku przerwania poziomu węzła serwera bazy danych powoduje utworzenie nowego węzła i automatycznie dołącza magazynu danych do nowego węzła. Wszystkie aktywne połączenia są odrzucane i wszystkich transakcji porządkowych nie są zatwierdzone.

## <a name="application-retry-logic-is-essential"></a>Istotne jest Logika ponawiania aplikacji
Ważne jest, MariaDB, aplikacji baz danych zostały opracowane w celu wykrycia, a następnie spróbuj ponownie usunąć połączenia i nie powiodło się transakcji. Ponawia próbę aplikacji, połączenie aplikacji w sposób niewidoczny dla użytkownika jest przekierowywany do nowo utworzone wystąpienie przejmuje dla tego wystąpienia nie powiodło się.

Na platformie Azure, bramy jest używana wewnętrznie do przekierowania połączenia w nowym wystąpieniu. Po przerwaniu całego procesu pracy awaryjnej zwykle trwa dziesiątki sekund. Ponieważ przekierowanie odbywa się wewnętrznie przez bramę, ciąg połączenia zewnętrznego nie zmienia się dla aplikacji klienckich.

## <a name="scaling-up-or-down"></a>Skalowanie w górę lub w dół
Podobnie jak w modelu wysokiej dostępności usługi Azure Database dla serwera MariaDB jest skalowany w górę lub w dół, tworzone jest nowe wystąpienie serwera o określonym rozmiarze. Istniejący magazyn danych jest odłączona od oryginalnego wystąpienia i dołączyć do nowego wystąpienia.

Podczas operacji skalowania odbywa się przerwę połączenia bazy danych. Aplikacje klienckie są odłączone, a następnie otwórz niezatwierdzone transakcje są anulowane. Gdy aplikacja kliencka ponawia próbę połączenia lub tworzy nowe połączenie, bramy kieruje połączenia z wystąpieniem o nowym rozmiarze.

## <a name="next-steps"></a>Kolejne kroki
- Aby zapoznać się z omówieniem usługi, zobacz [— Azure Database for MariaDB — omówienie](overview.md)
