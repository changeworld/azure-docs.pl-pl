---
title: Replikuj dane do usługi Azure Database for MySQL.
description: W tym artykule opisano danych replikacji dla usługi Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: f91a6da9a305c6620e4e01ab7aa3c554374cb5d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996826"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikuj dane do usługi Azure Database for MySQL

Replikacji danych umożliwia synchronizowanie danych z zewnętrznego serwera MySQL do usługi Azure Database for MySQL usługi. Zewnętrznego serwera może być lokalnie w maszynach wirtualnych lub usługa bazy danych, pracujących w chmurze innych dostawców. W danych replikacji jest oparty na dziennik binarny (binlog) na podstawie pozycji replikacji plików natywnej do bazy danych MySQL. Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji usługi MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Kiedy należy używać replikacji danych
Główne scenariusze, warto rozważyć użycie replikacji danych są następujące:

- **Synchronizacja danych hybrydowych:** Za pomocą replikacji danych możesz przechowywać dane synchronizowane między serwerami lokalnymi i usługi Azure Database for MySQL. Wykonanie synchronizacji jest przydatne podczas tworzenia aplikacji hybrydowych. Ta metoda jest atrakcyjne, gdy masz istniejącego serwera lokalnej bazy danych, ale chcesz przenieść dane do regionu bliżej użytkowników końcowych.
- **Synchronizacja wielu chmur:** Złożonych rozwiązań w chmurze umożliwia synchronizowanie danych między Azure Database for MySQL i różnych dostawców w chmurze, łącznie z maszynami wirtualnymi i usługami bazy danych hostowanej w tych chmurach replikacji danych.

## <a name="limitations-and-considerations"></a>Ograniczenia i zagadnienia

### <a name="data-not-replicated"></a>Dane nie są replikowane
[ *Bazy danych mysql systemu* ](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) nie jest replikowana na serwerze głównym. Nie są replikowane zmiany konta i uprawnienia na serwerze głównym. Jeśli utworzysz konto na serwerze głównym, a to konto musi mieć dostęp do serwera repliki, należy ręcznie utworzyć tego samego konta po stronie serwera repliki. Aby zrozumieć, jakie tabele są zawarte w systemowej bazie danych, zobacz [MySQL manual](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Wymagania
- Wersja serwera głównego musi wynosić co najmniej MySQL w wersji 5.6. 
- Wersje serwera głównego i replika musi być taka sama. Na przykład obie wartości muszą być MySQL w wersji 5.6 lub obie wartości muszą być MySQL w wersji 5.7.
- Każda tabela musi mieć klucz podstawowy.
- Główny serwer powinien używać aparat MySQL InnoDB.
- Użytkownik musi mieć uprawnienia do konfigurowania rejestrowania binarnego i tworzenia nowych użytkowników na serwerze głównym.

### <a name="other"></a>Inne
- Replikacji danych jest tylko do ogólnie rzecz biorąc obsługiwana przeznaczenia i zoptymalizowana pod kątem pamięci, warstw cenowych.
- Identyfikatory transakcji globalnej (GTID) nie są obsługiwane.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [Konfigurowanie replikacji danych](howto-data-in-replication.md)
- Dowiedz się więcej o [replikowanie na platformie Azure przy użyciu odczytu replik](concepts-read-replicas.md)
