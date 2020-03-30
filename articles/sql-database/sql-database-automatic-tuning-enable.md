---
title: Włączanie automatycznego dostrajania
description: Można włączyć automatyczne dostrajanie w bazie danych SQL Azure łatwo.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/03/2019
ms.openlocfilehash: eed839c277156046ff9b7d97c6e87636a0822889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299332"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Włącz automatyczne dostrajanie w celu monitorowania zapytań i zwiększenia wydajności obciążenia

Usługa Azure SQL Database to automatycznie zarządzana usługa danych, która stale monitoruje zapytania i identyfikuje akcję, którą można wykonać, aby zwiększyć wydajność obciążenia. Można przejrzeć zalecenia i ręcznie je zastosować lub pozwolić, aby usługa Azure SQL Database automatycznie stosła akcje naprawcze — jest to nazywane **trybem automatycznego strojenia.**

Automatyczne dostrajanie można włączyć na serwerze lub poziomie bazy danych za pośrednictwem [portalu Azure,](sql-database-automatic-tuning-enable.md#azure-portal)wywołań [interfejsu API REST](sql-database-automatic-tuning-enable.md#rest-api) i poleceń [T-SQL.](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current)

> [!NOTE]
> Dla wystąpienia zarządzanego obsługiwaną opcję FORCE_LAST_GOOD_PLAN można skonfigurować tylko za pośrednictwem [języka T-SQL.](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) Konfiguracja oparta na portalu i opcje automatycznego dostrajania indeksu opisane w tym artykule nie mają zastosowania do wystąpienia zarządzanego.

> [!NOTE]
> Konfigurowanie opcji automatycznego dostrajania za pomocą szablonu ARM (Azure Resource Manager) nie jest obecnie obsługiwane.

## <a name="enable-automatic-tuning-on-server"></a>Włącz automatyczne dostrajanie na serwerze

Na poziomie serwera można dziedziczyć konfigurację automatycznego dostrajania z "Azure Defaults" lub nie dziedziczyć konfiguracji. Domyślne ustawienia platformy Azure są FORCE_LAST_GOOD_PLAN jest włączona, CREATE_INDEX jest włączona, a DROP_INDEX jest wyłączona.

> [!IMPORTANT]
> Od marca 2020 r. zmiany domyślne platformy Azure dla automatycznego dostrajania będą obowiązywać w następujący sposób:
>
> - Nowe ustawienia domyślne platformy Azure zostaną FORCE_LAST_GOOD_PLAN = włączone, CREATE_INDEX = wyłączone i DROP_INDEX = wyłączone.
> - Istniejące serwery bez skonfigurowanych preferencji dostrajania automatycznego zostaną automatycznie skonfigurowane do dziedziczenia nowych ustawień domyślnych platformy Azure. Dotyczy to wszystkich klientów, którzy mają obecnie ustawienia serwera do automatycznego dostrajania w stanie niezdefiniowanym.
> - Nowe utworzone serwery zostaną automatycznie skonfigurowane do inherit nowych ustawień domyślnych platformy Azure (w przeciwieństwie do wcześniejszych, gdy konfiguracja automatycznego dostrajania była w stanie nieokreślonym podczas tworzenia nowego serwera).

### <a name="azure-portal"></a>Portal Azure

Aby włączyć automatyczne dostrajanie na **serwerze**logicznym usługi Azure SQL Database, przejdź do serwera w witrynie Azure Portal, a następnie wybierz **polecenie Automatyczne dostrajanie** w menu.

![Serwer](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Należy pamiętać, że **DROP_INDEX** opcja w tej chwili nie jest zgodna z aplikacjami korzystającymi z przełączania partycji i wskazówek dotyczących indeksu i nie powinna być włączona w takich przypadkach. Upuszczanie nieużywanych indeksów nie jest obsługiwane w warstwach usług Premium i Business Critical.
>

Wybierz opcje automatycznego strojenia, które chcesz włączyć, i wybierz pozycję **Zastosuj**.

Opcje automatycznego dostrajania na serwerze są stosowane do wszystkich baz danych na tym serwerze. Domyślnie wszystkie bazy danych dziedziczą konfigurację z serwera nadrzędnego, ale można ją zastąpić i określić dla każdej bazy danych indywidualnie.

### <a name="rest-api"></a>Interfejs API REST

Dowiedz się więcej o używaniu interfejsu API REST w celu włączenia automatycznego dostrajania na serwerze, zobacz [Metody automatycznego dostrajania programu SQL Server i GET HTTP](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Włączanie automatycznego dostrajania w pojedynczej bazie danych

Usługa Azure SQL Database umożliwia indywidualne określenie konfiguracji automatycznego dostrajania dla każdej bazy danych. Na poziomie bazy danych można dziedziczyć konfigurację automatycznego dostrajania z serwera nadrzędnego "Azure Defaults" lub nie dziedziczyć konfiguracji. Ustawienia domyślne platformy Azure są ustawione na FORCE_LAST_GOOD_PLAN jest włączone, CREATE_INDEX jest włączona, a DROP_INDEX jest wyłączona.

> [!TIP]
> Ogólne zalecenie jest zarządzanie konfiguracją automatycznego dostrajania na **poziomie serwera,** dzięki czemu te same ustawienia konfiguracji mogą być stosowane w każdej bazie danych automatycznie. Skonfiguruj automatyczne dostrajanie w pojedynczej bazie danych tylko wtedy, gdy ta baza danych ma inne ustawienia niż inne ustawienia dziedziczące z tego samego serwera.
>

### <a name="azure-portal"></a>Portal Azure

Aby włączyć automatyczne dostrajanie w **pojedynczej bazie danych,** przejdź do bazy danych w witrynie Azure portal i wybierz **opcję Automatyczne dostrajanie**.

Poszczególne ustawienia automatycznego strojenia można konfigurować oddzielnie dla każdej bazy danych. Można ręcznie skonfigurować indywidualną opcję automatycznego dostrajania lub określić, że opcja dziedziczy jej ustawienia z serwera.

![baza danych](./media/sql-database-automatic-tuning-enable/database.png)

Należy pamiętać, że DROP_INDEX opcja w tej chwili nie jest zgodna z aplikacjami korzystającymi z przełączania partycji i wskazówek dotyczących indeksu i nie powinna być włączona w takich przypadkach.

Po wybraniu żądanej konfiguracji kliknij przycisk **Zastosuj**.

### <a name="rest-api"></a>Interfejs API odpoczynku

Dowiedz się więcej o używaniu interfejsu API REST, aby włączyć automatyczne dostrajanie w pojedynczej bazie danych, zobacz [Metody automatycznego dostrajania bazy danych SQL i METODY GET HTTP](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Aby włączyć automatyczne dostrajanie w pojedynczej bazie danych za pośrednictwem protokołu T-SQL, należy połączyć się z bazą danych i wykonać następującą kwerendę:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Ustawienie automatycznego dostrajania do AUTO spowoduje zastosowanie ustawień domyślnych platformy Azure. Ustawienie go na INHERIT, konfiguracja automatycznego dostrajania będą dziedziczone z serwera nadrzędnego. Wybierając CUSTOM, należy ręcznie skonfigurować automatyczne strojenie.

Aby skonfigurować poszczególne opcje automatycznego dostrajania za pośrednictwem protokołu T-SQL, połącz się z bazą danych i wykonaj kwerendę, taką jak ta:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

Ustawienie indywidualnej opcji strojenia na ON spowoduje zastąpienie dowolnego ustawienia odziedziczonego przez bazę danych i włączenie opcji dostrajania. Ustawienie go na OFF, spowoduje również zastąpienie dowolnego ustawienia, które odziedziczyła baza danych i wyłączenie opcji dostrajania. Opcja automatycznego dostrajania, dla której określono wartość DEFAULT, odziedziczy konfigurację automatycznego strojenia z ustawień poziomu serwera.  

> [!IMPORTANT]
> W przypadku [aktywnej replikacji geograficznej](sql-database-auto-failover-group.md)automatyczne dostrajanie musi być skonfigurowane tylko w podstawowej bazie danych. Automatycznie stosowane akcje dostrajania, takie są na przykład indeks tworzenia lub usuwania będą automatycznie replikowane do odczytu tylko pomocnicze. Próba włączenia automatycznego dostrajania za pośrednictwem protokołu T-SQL w pomocniczej tylko do odczytu spowoduje niepowodzenie, ponieważ posiadanie innej konfiguracji strojenia w pomocniczej tylko do odczytu nie jest obsługiwane.
>

Znajdź nasze bardziej abut T-SQL opcje, aby skonfigurować automatyczne dostrajanie, zobacz [ALTER DATABASE SET Options (Transact-SQL) dla serwera bazy danych SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Wyłączone przez system

Automatyczne dostrajanie monitoruje wszystkie akcje, które wykonuje w bazie danych i w niektórych przypadkach można określić, że automatyczne dostrajanie nie może poprawnie działać w bazie danych. W tej sytuacji opcja strojenia zostanie wyłączona przez system. W większości przypadków dzieje się tak, ponieważ Query Store nie jest włączony lub jest w stanie tylko do odczytu w określonej bazie danych.

## <a name="permissions"></a>Uprawnienia

Ponieważ funkcja automatycznego dostrajania jest funkcją platformy Azure, aby z niej korzystać, musisz użyć wbudowanych ról RBAC platformy Azure. Użycie uwierzytelniania SQL tylko nie będzie wystarczające do korzystania z funkcji z witryny Azure portal.

Aby użyć automatycznego dostrajania, minimalne wymagane uprawnienia do udzielenia użytkownikowi jest wbudowana rola [współautora bazy danych SQL](../role-based-access-control/built-in-roles.md#sql-db-contributor) platformy Azure. Można również rozważyć użycie ról wyższych uprawnień, takich jak SQL Server Contributor, Współautor i Właściciel.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfigurowanie automatycznego dostrajania powiadomień e-mail

Zobacz [przewodnik po automatycznym dostrajaniu powiadomień e-mail.](sql-database-automatic-tuning-email-notifications.md)

## <a name="next-steps"></a>Następne kroki

* Przeczytaj [artykuł Automatyczne dostrajanie,](sql-database-automatic-tuning.md) aby dowiedzieć się więcej o automatycznym dostrajaniu i o tym, jak może pomóc ci zwiększyć wydajność.
* Zobacz [zalecenia dotyczące wydajności,](sql-database-advisor.md) aby zapoznać się z zaleceniami dotyczącymi wydajności usługi Azure SQL Database.
* Zobacz [Statystyki wydajności kwerendy,](sql-database-query-performance.md) aby dowiedzieć się więcej o wyświetlaniu wpływu na wydajność najważniejszych zapytań.
