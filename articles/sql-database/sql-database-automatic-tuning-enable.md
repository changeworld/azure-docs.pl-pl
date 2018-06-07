---
title: Włączanie automatycznego dostrajania dla usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Można włączyć automatycznego dostrajania bazy danych SQL Azure łatwe.
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: vvasic
ms.openlocfilehash: d4d3b7f54c7393b57339ea149e8a79f97891dc20
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646035"
---
# <a name="enable-automatic-tuning"></a>Włączanie automatycznego dostrajania

Baza danych SQL Azure jest usługą automatycznie zarządzanych danych, która stale monitoruje zapytań i określa działania, które można wykonać, aby poprawić wydajność obciążenia. Możesz przejrzeć zalecenia i ręcznie zastosować je lub pozwól bazy danych SQL Azure automatyczne stosowanie działania naprawcze — jest to nazywane **automatycznego dostrajania tryb**. Dostrajanie automatycznej można włączyć na poziomie bazy danych lub serwera.

## <a name="enable-automatic-tuning-on-server"></a>Włączanie automatycznego dostrajania na serwerze
Na poziomie serwera można wybrać dziedziczenia automatycznego dostrajania konfiguracji na podstawie "Azure domyślne" lub nie można odziedziczyć konfiguracji. Domyślne Azure to FORCE_LAST_GOOD_PLAN jest włączona, CREATE_INDEX jest włączona i DROP_INDEX jest wyłączona.

### <a name="azure-portal"></a>Azure Portal
Aby włączyć automatyczne dostrajanie na bazy danych SQL Azure logicznej **serwera**, przejdź do serwera w portalu Azure, a następnie wybierz **automatycznego dostrajania** w menu.

![Serwer](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Należy pamiętać, że **DROP_INDEX** opcji w tej chwili nie jest zgodny z aplikacjami przy użyciu wskazówek indeks i przełączanie partycji i nie powinna być włączona w tych przypadkach.
>

Wybierz opcje automatycznego dostrajania chcesz włączyć, a następnie wybierz **Zastosuj**.

Opcje automatycznego dostrajania na serwerze są stosowane do wszystkich baz danych na tym serwerze. Domyślnie wszystkie bazy danych odziedziczyć konfiguracji z ich nadrzędnego serwera, ale to zastąpione i określić osobno dla każdej bazy danych.

### <a name="rest-api"></a>Interfejs API REST
[Kliknij tutaj, aby dowiedzieć się więcej o sposobie włączania automatycznego dostrajania na poziomie serwera za pomocą interfejsu API REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Włączanie automatycznego dostrajania dla poszczególnych bazy danych

Baza danych SQL Azure można określić indywidualnie automatycznego dostrajania konfiguracji dla każdej bazy danych. Na poziomie bazy danych można dziedziczyć automatycznego dostrajania konfiguracji z nadrzędnego serwera, "Azure domyślne" albo nie można odziedziczyć konfiguracji. Ustawienia domyślne Azure umożliwiają FORCE_LAST_GOOD_PLAN jest włączona, CREATE_INDEX jest włączona i DROP_INDEX jest wyłączona.

> [!NOTE]
> Ogólne zalecenie to zarządzanie automatycznego dostrajania konfiguracji **poziom serwera** , te same ustawienia konfiguracji mogą być automatycznie stosowane w każdej bazie danych. Konfigurowanie automatycznego dostrajania dla poszczególnych bazy danych tylko wtedy, gdy potrzebujesz tej bazy danych ma inne ustawienia niż inne dziedziczy ustawienia z tego samego serwera.
>

### <a name="azure-portal"></a>Azure Portal

Aby włączyć automatyczne dostrajanie na **pojedynczej bazy danych**, przejdź do bazy danych w portalu Azure i wybierz **automatycznego dostrajania**.

Poszczególne automatycznego dostrajania ustawienia można skonfigurować osobno dla każdej bazy danych. Można ręcznie skonfigurować poszczególne opcje automatycznego dostrajania lub określ, czy opcja dziedziczy ustawienia z serwera.

![Database (Baza danych)](./media/sql-database-automatic-tuning-enable/database.png)

Należy pamiętać, że opcja DROP_INDEX w tej chwili nie jest zgodny z aplikacji przy użyciu wskazówek indeks i przełączanie partycji i nie powinna być włączona w tych przypadkach.

Po wybraniu wymaganą konfigurację, kliknij przycisk **Zastosuj**.

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
