---
title: Włączanie automatycznego dostrajania dla usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Można włączyć dostrajania automatycznego usługi Azure SQL Database łatwe.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 23d696971d49210479c675a0f3e2a0a85d1e8b3b
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241127"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Włączanie automatycznego dostrajania monitorowania zapytań i zwiększyć wydajność obciążeń

Usługa Azure SQL Database jest usługą automatycznie zarządzanych danych, która stale monitoruje zapytań i określa działania, które należy wykonać, aby zwiększyć wydajność przetwarzania obciążenia. Przeglądanie zaleceń i ręcznie zastosować je lub pozwól usłudze Azure SQL Database automatycznie stosować akcje naprawcze — jest to nazywane **automatyczny tryb dostrajania**.

Dostrajanie automatyczne usługi można włączyć na serwerze lub na poziomie bazy danych za pośrednictwem [witryny Azure portal](sql-database-automatic-tuning-enable.md#azure-portal), [interfejsu API REST](sql-database-automatic-tuning-enable.md#rest-api) wywołania i [języka T-SQL](sql-database-automatic-tuning-enable.md#t-sql) poleceń.

## <a name="enable-automatic-tuning-on-server"></a>Włączanie automatycznego dostrajania na serwerze
Na poziomie serwera można wybrać dziedziczą konfigurację dostrajania automatycznego z "Domyślne ustawienia Azure", lub nie dziedziczą konfigurację. Ustawień domyślnych platformy Azure są FORCE_LAST_GOOD_PLAN jest włączona, CREATE_INDEX jest włączona i DROP_INDEX jest wyłączona.

### <a name="azure-portal"></a>Azure Portal
Aby włączyć dostrajania automatycznego usługi Azure SQL Database logiczne **serwera**, przejdź do serwera w witrynie Azure portal, a następnie wybierz **dostrajania automatycznego** w menu.

![Serwer](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Należy pamiętać, że **DROP_INDEX** opcji w tej chwili nie jest zgodny z aplikacji przy użyciu wskazówek dotyczących przełączania i indeks partycji i nie powinna być włączona w tych przypadkach.
>

Wybierz opcje dostrajania automatycznego, aby włączyć, a następnie wybierz pozycję **Zastosuj**.

Opcje dostrajania automatycznego na serwerze są stosowane do wszystkich baz danych na tym serwerze. Domyślnie wszystkie bazy danych dziedziczy konfigurację z ich nadrzędnego serwera, ale to zastąpienia i określona dla każdej bazy danych indywidualnie.

### <a name="rest-api"></a>Interfejs API REST

Dowiedz się więcej o korzystaniu z interfejsu API REST Włączanie automatycznego dostrajania na serwerze, zobacz [serwera dostrajania automatycznego usługi SQL metody aktualizacji i GET HTTP](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).


## <a name="enable-automatic-tuning-on-an-individual-database"></a>Włączanie automatycznego dostrajania na poszczególnych baz danych

Usługi Azure SQL Database można indywidualnie określić konfigurację dostrajania automatycznego dla każdej bazy danych. Na poziomie bazy danych można wybrać dziedziczą konfigurację dostrajania automatycznego z serwera nadrzędnego "Domyślne ustawienia Azure", lub nie dziedziczą konfigurację. Ustawień domyślnych platformy Azure są ustawione na FORCE_LAST_GOOD_PLAN jest włączona, CREATE_INDEX jest włączona i DROP_INDEX jest wyłączona.

> [!NOTE]
> Ogólne zalecenie to zarządzanie konfigurację dostrajania automatycznego w **poziom serwera** więc te same ustawienia konfiguracji mogą być automatycznie stosowane w każdej bazy danych. Konfigurowanie automatycznego dostrajania na poszczególnych baz danych, tylko wtedy, gdy potrzebujesz tej bazy danych ma inne ustawienia niż inne dziedziczy ustawienia z tym samym serwerze.
>

### <a name="azure-portal"></a>Azure Portal

Aby włączyć automatyczne dostrajanie na **pojedynczej bazy danych**, przejdź do bazy danych w witrynie Azure portal i wybierz **dostrajania automatycznego**.

Poszczególne ustawienia automatycznego dostrajania może zostać skonfigurowany oddzielnie dla każdej bazy danych. Można ręcznie skonfigurować poszczególne opcję dostrajania automatycznego lub określ, czy opcja dziedziczy ustawienia z serwera.

![Database (Baza danych)](./media/sql-database-automatic-tuning-enable/database.png)

Należy pamiętać, że opcja DROP_INDEX w tej chwili nie jest zgodny z aplikacji przy użyciu wskazówek dotyczących przełączania i indeks partycji i nie powinna być włączona w tych przypadkach.

Po wybraniu wymaganą konfigurację, kliknij przycisk **Zastosuj**.

### <a name="rest-api"></a>Interfejs API REST

Dowiedz się więcej o Włączanie automatycznego dostrajania na pojedynczej bazy danych za pomocą interfejsu API REST, zobacz [bazy danych dostrajania automatycznego usługi SQL metody aktualizacji i GET HTTP](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Aby włączyć automatyczne dostrajanie na pojedynczej bazy danych za pomocą języka T-SQL, połączenia z bazą danych i wykonaj następujące zapytanie:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
Ustawienia automatycznego dostrajania automatycznie zastosuje domyślne ustawienia Azure. Ustawienie dziedziczenia, konfigurację dostrajania automatycznego będą dziedziczone z nadrzędnego serwera. Wybieranie NIESTANDARDOWEGO, należy ręcznie skonfigurować dostrajania automatycznego.

Aby skonfigurować indywidualne opcje dostrajania automatycznego za pomocą języka T-SQL, połączenia z bazą danych i wykonywanie zapytania, taką jak ta:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
Ustawienie dla poszczególnych opcję dostrajania, zastąpić ustawienie wszystkie dziedziczone bazy danych i włącz opcję dostrajania. Ustawienie go na wartość OFF, będą również zastąpienia ustawienia dowolnej dziedziczone bazy danych i wyłącz opcję dostrajania. Opcję dostrajania automatycznego, dla którego domyślne ustawienie jest określone, będzie dziedziczyć konfiguracji automatycznego dostrajania ustawienie poziomie bazy danych.  

Znajdź więcej naszych dotyczący języka T-SQL umożliwiające konfigurowanie dostrajania automatycznego, zobacz [opcje ALTER DATABASE SET (Transact-SQL) dla serwera logicznego SQL Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Wyłączone przez system
Automatyczne dostrajanie monitorowania wszystkie akcje, jaki zajmuje w bazie danych, a w niektórych przypadkach może ustalić dostrajanie automatyczne nie może poprawnie działać w bazie danych. W takiej sytuacji opcji strojenia zostaną wyłączone przez system. W większości przypadków zdarza się, ponieważ nie włączono Query Store lub jest w stanie tylko do odczytu w określonej bazie danych.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfigurowanie automatycznego dostrajania wiadomości e-mail z powiadomieniami

Zobacz [powiadomienia pocztą e-mail dostrajania automatycznego](sql-database-automatic-tuning-email-notifications.md) przewodnik.

## <a name="next-steps"></a>Kolejne kroki
* Odczyt [automatycznego dostrajania artykułu](sql-database-automatic-tuning.md) Aby dowiedzieć się więcej na temat dostrajania automatycznego i jak może ono pomóc poprawić wydajność.
* Zobacz [zalecenia dotyczące wydajności](sql-database-advisor.md) omówienie zalecenia dotyczące wydajności usługi Azure SQL Database.
* Zobacz [szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) Aby dowiedzieć się więcej o wyświetlaniu wpływ na wydajność najczęściej używane zapytania.
