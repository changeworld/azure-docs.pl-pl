---
title: Pojęcia wysokiej dostępności w usłudze Azure Database for MySQL
description: Ten temat zawiera informacje o wysokiej dostępności, gdy przy użyciu usługi Azure Database for MySQL
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 82d6106b7cebf47e6d68347857dd3d8a2d22dc3c
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2018
ms.locfileid: "51298691"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Pojęcia wysokiej dostępności w usłudze Azure Database for MySQL
Usługa Azure Database for MySQL oferuje gwarantowaną wysoką dostępność. Finansowo umowy dotyczącej poziomu usług (SLA) wynosi 99,99% po ogólnym udostępnieniu. Nie ma praktycznie nie aplikacji, czas przestoju podczas korzystania z tej usługi.

## <a name="high-availability"></a>Wysoka dostępność
Model o wysokiej dostępności (HA) opiera się na wbudowane mechanizmy trybu failover, po wystąpieniu przerwania poziomu węzła. Przerwanie poziomu węzła może wystąpić z powodu awarii sprzętu lub w odpowiedzi na wdrożenie usługi.

Przez cały czas zmiany wprowadzone do usługi Azure Database dla serwera bazy danych MySQL odbywają się w kontekście transakcji. Zmiany są rejestrowane w synchronicznie w usłudze Azure storage, gdy transakcja została zatwierdzona. W przypadku przerwania poziomu węzła serwera bazy danych powoduje utworzenie nowego węzła i automatycznie dołącza magazynu danych do nowego węzła. Wszystkie aktywne połączenia są odrzucane i wszystkich transakcji porządkowych nie są zatwierdzone.

## <a name="application-retry-logic-is-essential"></a>Istotne jest Logika ponawiania aplikacji
Ważne jest, MySQL, aplikacji baz danych zostały opracowane w celu wykrycia, a następnie spróbuj ponownie usunąć połączenia i nie powiodło się transakcji. Ponawia próbę aplikacji, połączenie aplikacji w sposób niewidoczny dla użytkownika jest przekierowywany do nowo utworzone wystąpienie przejmuje dla tego wystąpienia nie powiodło się.

Na platformie Azure, bramy jest używana wewnętrznie do przekierowania połączenia w nowym wystąpieniu. Po przerwaniu całego procesu pracy awaryjnej zwykle trwa dziesiątki sekund. Ponieważ przekierowanie odbywa się wewnętrznie przez bramę, ciąg połączenia zewnętrznego nie zmienia się dla aplikacji klienckich.

## <a name="scaling-up-or-down"></a>Skalowanie w górę lub w dół
Podobnie jak w modelu wysokiej dostępności usługi Azure Database for MySQL jest skalowany w górę lub w dół, tworzone jest nowe wystąpienie serwera o określonym rozmiarze. Istniejący magazyn danych jest odłączona od oryginalnego wystąpienia i dołączyć do nowego wystąpienia.

Podczas operacji skalowania odbywa się przerwę połączenia bazy danych. Aplikacje klienckie są odłączone, a następnie otwórz niezatwierdzone transakcje są anulowane. Gdy aplikacja kliencka ponawia próbę połączenia lub tworzy nowe połączenie, bramy kieruje połączenia z wystąpieniem o nowym rozmiarze. 

## <a name="next-steps"></a>Kolejne kroki
- Aby zapoznać się z omówieniem usługi, zobacz [— Azure Database for MySQL — omówienie](overview.md)
- Aby uzyskać omówienie logikę ponawiania próby, zobacz [obsługi błędów przejściowych łączności dla usługi Azure Database for MySQL](concepts-connectivity.md)
