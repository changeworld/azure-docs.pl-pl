---
title: Włączanie automatycznego dostrajania dla usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Można włączyć automatycznego dostrajania bazy danych SQL Azure łatwe.
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: vvasic
ms.openlocfilehash: e4c3a2c1f21bf14bfc75f20dd18cefca68fd2067
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="enable-automatic-tuning"></a>Włączanie automatycznego dostrajania

Baza danych SQL Azure jest usługą automatycznie zarządzanych danych, która stale monitoruje zapytań i określa działania, które można wykonać, aby poprawić wydajność obciążenia. Możesz przejrzeć zalecenia i ręcznie zastosować je lub pozwól bazy danych SQL Azure automatyczne stosowanie działania naprawcze — jest to nazywane **automatycznego dostrajania tryb**. Dostrajanie automatycznej można włączyć na poziomie bazy danych lub serwera.

## <a name="enable-automatic-tuning-on-server"></a>Włączanie automatycznego dostrajania na serwerze
Na poziomie serwera można wybrać dziedziczenia automatycznego dostrajania konfiguracji na podstawie "Azure domyślne" lub nie można odziedziczyć konfiguracji. Domyślne Azure to FORCE_LAST_GOOD_PLAN jest włączona, CREATE_INDEX jest włączona i DROP_INDEX jest wyłączona.

### <a name="azure-portal"></a>Azure Portal
Aby włączyć automatyczne dostrajanie w bazie danych SQL Azure **serwera**, przejdź do serwera w portalu Azure, a następnie wybierz **automatycznego dostrajania** w menu. Wybierz opcje automatycznego dostrajania chcesz włączyć, a następnie wybierz **Zastosuj**.

![Serwer](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Należy pamiętać, że **DROP_INDEX** opcji w tej chwili jest niezgodny z aplikacji przy użyciu wskazówek indeks i przełączanie partycji i nie powinna włączona w tych przypadkach.
>

Opcje automatycznego dostrajania na serwerze są stosowane do wszystkich baz danych na serwerze. Domyślnie wszystkie bazy danych dziedziczy konfiguracji z ich nadrzędnego serwera, ale to zastąpione i określić osobno dla każdej bazy danych.

### <a name="rest-api"></a>Interfejs API REST
[Kliknij tutaj, aby dowiedzieć się więcej o sposobie włączania automatycznego dostrajania na poziomie serwera za pomocą interfejsu API REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Włączanie automatycznego dostrajania dla poszczególnych bazy danych

Baza danych SQL Azure umożliwia niezależnie Określ automatycznego dostrajania konfiguracji w każdej bazie danych. Na poziomie bazy danych można dziedziczyć automatycznego dostrajania konfiguracji z nadrzędnego serwera, "Azure domyślne" albo nie można odziedziczyć konfiguracji. Azure wartości domyślne są włączone FORCE_LAST_GOOD_PLAN CREATE_INDEX i włączona DROP_INDEX wyłączone.

> [!NOTE]
> Ogólne zaleca się zarządzanie automatycznego dostrajania konfiguracji na poziomie serwera, dlatego te same ustawienia konfiguracji mogą być automatycznie stosowane w każdej bazie danych. Konfigurowanie automatycznego dostrajania na poszczególne bazy danych, jeśli baza danych jest inny tego inne, na tym samym serwerze.
>

### <a name="azure-portal"></a>Azure Portal

Aby włączyć automatyczne dostrajanie na **pojedynczej bazy danych**, przejdź do bazy danych w portalu Azure wybierz a następnie **automatycznego dostrajania**. Można skonfigurować pojedynczy bazy danych, aby odziedziczyć ustawienia z serwera, wybierając opcję lub konfiguracji bazy danych można określić osobno.

![Database (Baza danych)](./media/sql-database-automatic-tuning-enable/database.png)

Po wybraniu odpowiedniej konfiguracji, kliknij przycisk **Zastosuj**.

Należy pamiętać, że opcja DROP_INDEX w tej chwili jest niezgodny z aplikacji przy użyciu wskazówek indeks i przełączanie partycji i powinien nie jest włączona w tych przypadkach.

### <a name="rest-api"></a>Interfejs API REST
[Kliknij tutaj, aby dowiedzieć się więcej o sposobie włączania automatycznego dostrajania dla jednej bazy danych za pośrednictwem interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning)

### <a name="t-sql"></a>T-SQL

Aby włączyć automatycznego dostrajania dla jednej bazy danych za pośrednictwem T-SQL, połączenia z bazą danych i wykonaj następujące zapytanie:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
Ustawienia automatycznego dostrajania automatycznie zastosuje domyślne Azure. Ustawienie dziedziczenia, automatycznego dostrajania konfiguracji zostaną odziedziczone z nadrzędnego serwera. Wybranie NIESTANDARDOWEGO, konieczne będzie ręczne skonfigurowanie automatycznego dostrajania.

Aby skonfigurować poszczególne opcje dostrajania automatycznego za pośrednictwem T-SQL, połączenia z bazą danych i wykonać zapytania, taką jak:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
Ustawienie poszczególnych opcji strojenia ON, zastąpić ustawienie wszystkie odziedziczone bazy danych i włącz opcję dostrajania. Ustawienie jej na wartość OFF, zostanie również zastąpienia ustawienia wszelkie dziedziczone bazy danych i wyłączyć opcję dostrajania. Opcję automatycznego dostrajania, dla którego domyślne ustawienie jest określone, będą dziedziczyć konfiguracji automatycznego dostrajania ustawienie poziomu bazy danych.  

## <a name="disabled-by-the-system"></a>Wyłączone przez system
Automatycznego dostrajania monitorowania wszystkich akcji, jaki zajmuje w bazie danych, a w niektórych przypadkach może ustalić automatycznego dostrajania nie może poprawnie działać w bazie danych. W takiej sytuacji opcji strojenia zostanie wyłączone przez system. W większości przypadków zdarza się, ponieważ Magazyn zapytań nie jest włączony lub jest w stanie tylko do odczytu na określonej bazy danych.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfigurowanie automatycznego dostrajania powiadomienia pocztą e-mail

Zobacz [automatycznego dostrajania powiadomienia pocztą e-mail](sql-database-automatic-tuning-email-notifications.md)

## <a name="next-steps"></a>Kolejne kroki
* Odczyt [automatycznego dostrajania artykułu](sql-database-automatic-tuning.md) Aby dowiedzieć się więcej na temat automatycznego dostrajania i jak go w celu poprawy wydajności.
* Zobacz [zaleceń](sql-database-advisor.md) omówienie zaleceń wydajności bazy danych SQL Azure.
* Zobacz [szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) Aby dowiedzieć się więcej o wyświetlaniu wpływu na wydajność kwerend top.
