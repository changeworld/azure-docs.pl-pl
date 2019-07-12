---
title: Replikuj dane do usługi Azure Database dla serwera MariaDB.
description: W tym artykule opisano danych replikacji dla usługi Azure Database dla serwera MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: c19ec06ce353d653086fa693dde975a55f51f823
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839251"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replikuj dane do usługi Azure Database dla serwera MariaDB

Dane replikacji umożliwia synchronizowanie danych z serwera MariaDB, działających w środowisku lokalnym, w maszynach wirtualnych lub bazy danych usług hostowanych przez innych dostawców chmury do usługi Azure Database dla usługi MariaDB. Replikacji danych opiera się na dziennik binarny (binlog) na podstawie pozycji replikacji plików natywne MariaDB. Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Kiedy należy używać replikacji danych
Główne scenariusze, warto rozważyć użycie replikacji danych są następujące:

- **Synchronizacja danych hybrydowych:** Za pomocą replikacji danych możesz przechowywać dane synchronizowane między serwerami lokalnymi i usługi Azure Database dla serwera MariaDB. Wykonanie synchronizacji jest przydatne podczas tworzenia aplikacji hybrydowych. Ta metoda jest atrakcyjne, gdy masz istniejącego serwera lokalnej bazy danych, ale chcesz przenieść dane do regionu bliżej użytkowników końcowych.
- **Synchronizacja wielu chmur:** Złożonych rozwiązań w chmurze umożliwia synchronizowanie danych między Azure Database dla serwera MariaDB i różnych dostawców w chmurze, łącznie z maszynami wirtualnymi i usługami bazy danych hostowanej w tych chmurach replikacji danych.

## <a name="limitations-and-considerations"></a>Ograniczenia i zagadnienia

### <a name="data-not-replicated"></a>Dane nie są replikowane
[ *Bazy danych mysql systemu* ](https://mariadb.com/kb/en/library/the-mysql-database-tables/) nie jest replikowany na serwerze głównym. Nie są replikowane zmiany konta i uprawnienia na serwerze głównym. Jeśli utworzysz konto na serwerze głównym, a to konto musi mieć dostęp do serwera repliki, następnie utwórz ręcznie tego samego konta po stronie serwera repliki. Aby zrozumieć, jakie tabele są zawarte w systemowej bazie danych, zobacz [dokumentacji MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Wymagania
- Wersja serwera głównego musi wynosić co najmniej wersji 10.2 MariaDB.
- Wersje serwera głównego i replika musi być taka sama. Na przykład zarówno musi być MariaDB wersji 10.2.
- Każda tabela musi mieć klucz podstawowy.
- Aparat InnoDB korzystała aplikacja serwer główny.
- Użytkownik musi mieć uprawnienia do konfigurowania rejestrowania binarnego i tworzenia nowych użytkowników na serwerze głównym.

### <a name="other"></a>Inne
- Replikacji danych jest tylko do ogólnie rzecz biorąc obsługiwana przeznaczenia i zoptymalizowana pod kątem pamięci, warstw cenowych.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [Konfigurowanie replikacji danych](howto-data-in-replication.md).
