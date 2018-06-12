---
title: Replikowanie danych do bazy danych Azure dla programu MySQL.
description: W tym artykule opisano w danych replikacji bazy danych Azure dla programu MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: 14ed3ef57da28b6929115cf3e5746653d199b140
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263752"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikowanie danych do bazy danych Azure dla programu MySQL

Funkcja replikacji danych pozwala na synchronizowanie danych z serwera MySQL, uruchamiane lokalnie, na maszynach wirtualnych lub bazy danych usług hostowanych przez innych dostawców chmury do bazy danych Azure dla usługi MySQL. W danych replikacji jest oparta na dziennik binarny (binlog) na podstawie pozycji replikacji macierzysty MySQL. Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Kiedy należy używać w danych replikacji
Główne scenariusze wziąć pod uwagę przy użyciu replikacji w danych są następujące:

- **Synchronizacja danych hybrydowego:** z replikacją danych, można zachować dane synchronizowane między serwerami lokalnymi i bazy danych platformy Azure dla programu MySQL. Synchronizacja jest przydatne w przypadku tworzenia aplikacji hybrydowych. Ta metoda jest atrakcyjne, gdy istniejący serwer lokalnej bazy danych, ale chcesz przenieść dane do regionu bliżej dla użytkowników końcowych.
- **Synchronizacja chmury wielu:** rozwiązań chmury złożonych, użyj replikacji danych w celu synchronizacji danych między bazą danych Azure dla programu MySQL i dostawców inną chmurę, w tym maszyny wirtualne i usługi bazy danych obsługiwane w tych chmurach.

## <a name="limitations-and-considerations"></a>Ograniczenia i zagadnienia

### <a name="data-not-replicated"></a>Dane nie są replikowane
[ *Bazy danych mysql systemu* ](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) nie jest replikowany na serwerze podstawowym. Zmiany kont i uprawnień na serwerze podstawowym nie są replikowane. Jeśli utworzysz konto na serwerze podstawowym i to konto wymaga dostępu do serwera repliki, następnie utwórz ręcznie konto po stronie serwera repliki. Aby zrozumieć, jakie tabele są zawarte w systemowej bazie danych, zobacz [ręcznego MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Wymagania
- Wersja serwera podstawowego musi być co najmniej wersja 5.6 MySQL. 
- Wersje serwera podstawowego i repliki muszą być takie same. Na przykład muszą mieć jednocześnie MySQL wersja 5.6 lub obie wartości muszą być MySQL wersji 5.7.
- Każda tabela musi mieć klucz podstawowy.
- Podstawowy serwer powinien używać aparatu MySQL InnoDB.
- Użytkownik musi mieć uprawnienia do konfigurowania rejestrowania binarnego i tworzenia nowych użytkowników na serwerze podstawowym.

### <a name="other"></a>Inne
- Identyfikatory transakcji globalnej (GTID) nie są obsługiwane.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [replikacji danych w](howto-data-in-replication.md)
