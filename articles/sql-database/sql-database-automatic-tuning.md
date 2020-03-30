---
title: Omówienie dostrajania automatycznego
description: Usługa Azure SQL Database analizuje kwerendę SQL i automatycznie dostosowuje się do obciążenia użytkownika.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: d4762c86268353ff0464ff3919250cd86f0038d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214102"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatyczne dostrajanie w bazie danych SQL platformy Azure

Automatyczne dostrajanie usługi Azure SQL Database zapewnia najwyższą wydajność i stabilne obciążenia dzięki ciągłemu dostrajaniu wydajności w oparciu o sztuczną inteligencję i uczenie maszynowe.

Automatyczne dostrajanie to w pełni zarządzana inteligentna usługa wydajności, która używa wbudowanej analizy do ciągłego monitorowania zapytań wykonywanych w bazie danych i automatycznie poprawia ich wydajność. Osiąga się to poprzez dynamiczne dostosowywanie bazy danych do zmieniających się obciążeń i stosowanie zaleceń dotyczących dostrajania. Automatyczne dostrajanie uczy się poziomo ze wszystkich baz danych na platformie Azure za pośrednictwem sztucznej inteligencji i dynamicznie poprawia swoje akcje dostrajania. Im dłużej baza danych działa z automatycznym dostrajaniem, tym lepiej działa.

Automatyczne dostrajanie usługi Azure SQL Database może być jedną z najważniejszych funkcji, które można włączyć, aby zapewnić stabilne i osiągające szczytowe obciążenia bazy danych.

## <a name="what-can-automatic-tuning-do-for-you"></a>Co może zrobić automatyczne strojenie za Ciebie

- Automatyczne dostrajanie wydajności baz danych SQL platformy Azure
- Automatyczna weryfikacja przyrostu wydajności
- Automatyczne wycofywanie i autopoprawa
- Dostrajanie historii
- Dostrajanie skryptów T-SQL akcji do ręcznego wdrażania
- Proaktywne monitorowanie wydajności obciążenia
- Skalowanie w poziomie w setkach tysięcy baz danych
- Pozytywny wpływ na zasoby DevOps i całkowity koszt posiadania

## <a name="safe-reliable-and-proven"></a>Bezpieczne, niezawodne i sprawdzone

Operacje dostrajania stosowane do baz danych w usłudze Azure SQL Database są w pełni bezpieczne dla wydajności najbardziej intensywnych obciążeń. System został zaprojektowany z dbałością, aby nie zakłócać obciążeń użytkowników. Zalecenia dotyczące automatycznego dostrajania są stosowane tylko w czasie niskiego wykorzystania. System może również tymczasowo wyłączyć operacje automatycznego dostrajania w celu ochrony wydajności obciążenia. W takim przypadku komunikat "Wyłączone przez system" zostanie wyświetlony w witrynie Azure portal. Automatyczne dostrajanie dotyczy obciążeń o najwyższym priorytecie zasobów.

Mechanizmy automatycznego dostrajania są dojrzałe i zostały udoskonalone w kilku milionach baz danych uruchomionych na platformie Azure. Zastosowane automatyczne operacje dostrajania są weryfikowane automatycznie, aby zapewnić pozytywną poprawę wydajności obciążenia. Zalecenia dotyczące wydajności regressed są dynamicznie wykrywane i szybko przywracane. Za pośrednictwem historii dostrajania rejestrowane, istnieje wyraźny ślad dostrajania ulepszeń wprowadzonych do każdej bazy danych SQL azure.

![Jak działa automatyczne strojenie](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Automatyczne dostrajanie usługi Azure SQL Database udostępnia swoją podstawową logikę z aparatem automatycznego dostrajania programu SQL Server. Aby uzyskać dodatkowe informacje techniczne dotyczące wbudowanego mechanizmu analizy, zobacz [automatyczne dostrajanie programu SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

Aby uzyskać omówienie działania automatycznego dostrajania i typowych scenariuszy użycia, zobacz osadzony klip wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Włączanie automatycznego dostrajania

Można [włączyć automatyczne dostrajanie dla pojedynczych i puli baz danych w witrynie Azure portal](sql-database-automatic-tuning-enable.md) lub przy użyciu [alter database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) T-SQL instrukcji. Włącz automatyczne dostrajanie baz danych wystąpienia we wdrożeniu wystąpienia zarządzanego przy użyciu instrukcji [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL.

## <a name="automatic-tuning-options"></a>Opcje automatycznego strojenia

Opcje automatycznego dostrajania dostępne w usłudze Azure SQL Database to:

| Opcja automatycznego strojenia | Obsługa pojedynczej bazy danych i puli baz danych | Obsługa bazy danych wystąpień |
| :----------------------------- | ----- | ----- |
| **TWORZENIE INDEKSU** — identyfikuje indeksy, które mogą zwiększyć wydajność obciążenia, tworzy indeksy i automatycznie weryfikuje, że wydajność zapytań została ulepszona. | Tak | Nie |
| **DROP INDEX** — identyfikuje indeksy nadmiarowe i zduplikowane codziennie, z wyjątkiem unikatowych indeksów i indeksów, które nie były używane przez długi czas (>90 dni). Należy pamiętać, że ta opcja nie jest zgodna z aplikacjami korzystającymi z przełączania partycji i wskazówek dotyczących indeksu. Upuszczanie nieużywanych indeksów nie jest obsługiwane w warstwach usług Premium i Business Critical. | Tak | Nie |
| **FORCE LAST GOOD PLAN** (automatyczna korekta planu) — identyfikuje zapytania SQL przy użyciu planu wykonania, który jest wolniejszy niż poprzedni dobry plan, i kwerend przy użyciu ostatniego znanego dobrego planu zamiast planu regressed. | Tak | Tak |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>Automatyczne dostrajanie pojedynczych i pulowanych baz danych

Automatyczne dostrajanie pojedynczych i branych baz danych używa rekomendacji **programu CREATE INDEX,** **DROP INDEX**i FORCE LAST **GOOD PLAN** w celu optymalizacji wydajności bazy danych. Aby uzyskać więcej informacji, zobacz [Zalecenia doradcy bazy danych w witrynie Azure portal](sql-database-advisor-portal.md), w programie [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)i interfejsie [API REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

Można ręcznie zastosować zalecenia dotyczące dostrajania za pomocą witryny Azure portal lub umożliwić automatyczne dostrajanie autonomicznie zastosować zalecenia dotyczące dostrajania dla Ciebie. Korzyści z umożliwienia systemowi autonomicznego stosowania zaleceń dotyczących dostrajania jest to, że automatycznie weryfikuje istnieje pozytywny zysk na wydajności obciążenia, a jeśli nie zostanie wykryte znaczące ulepszenia wydajności, będzie to automatycznie przywrócić zalecenie strojenia. Należy pamiętać, że w przypadku zapytań, których dotyczą zalecenia dotyczące strojenia, które nie są wykonywane często, faza sprawdzania poprawności może potrwać do 72 godzin zgodnie z projektem.

W przypadku stosowania zaleceń dostrajania za pośrednictwem języka T-SQL, automatyczne sprawdzanie poprawności wydajności i mechanizmy wycofywania nie są dostępne. Zalecenia stosowane w ten sposób pozostaną aktywne i przedstawione na liście zaleceń dotyczących tuningu przez 24-48 godzin. zanim system automatycznie je wycofa. Jeśli chcesz usunąć zalecenie wcześniej, możesz go odrzucić z witryny Azure portal.

Opcje automatycznego dostrajania można niezależnie włączyć lub wyłączyć na bazę danych lub można je skonfigurować na serwerach bazy danych SQL i zastosować w każdej bazie danych, która dziedziczy ustawienia z serwera. Serwery bazy danych SQL mogą dziedziczyć domyślne ustawienia platformy Azure dla ustawień automatycznego dostrajania. Ustawienia domyślne platformy Azure w tej chwili są ustawione na FORCE_LAST_GOOD_PLAN jest włączona, CREATE_INDEX jest włączona, a DROP_INDEX jest wyłączona.

> [!IMPORTANT]
> Od marca 2020 r. zmiany domyślne platformy Azure dla automatycznego dostrajania będą obowiązywać w następujący sposób:
>
> - Nowe ustawienia domyślne platformy Azure zostaną FORCE_LAST_GOOD_PLAN = włączone, CREATE_INDEX = wyłączone i DROP_INDEX = wyłączone.
> - Istniejące serwery bez skonfigurowanych preferencji dostrajania automatycznego zostaną automatycznie skonfigurowane do dziedziczenia nowych ustawień domyślnych platformy Azure. Dotyczy to wszystkich klientów, którzy mają obecnie ustawienia serwera do automatycznego dostrajania w stanie niezdefiniowanym.
> - Nowe utworzone serwery zostaną automatycznie skonfigurowane do inherit nowych ustawień domyślnych platformy Azure (w przeciwieństwie do wcześniejszych, gdy konfiguracja automatycznego dostrajania była w stanie nieokreślonym podczas tworzenia nowego serwera).

Konfigurowanie opcji automatycznego dostrajania na serwerze i dziedziczenia ustawień baz danych należących do serwera nadrzędnego jest zalecaną metodą konfigurowania automatycznego dostrajania, ponieważ upraszcza zarządzanie opcjami automatycznego dostrajania dla dużej liczby baz danych.

Aby dowiedzieć się więcej o tworzeniu powiadomień e-mail dla zalecanych automatycznego dostrajania, zobacz [Powiadomienia e-mail do automatycznego dostrajania](sql-database-automatic-tuning-email-notifications.md).

### <a name="automatic-tuning-for-instance-databases"></a>Automatyczne dostrajanie baz danych

Automatyczne dostrajanie baz danych na przykład w przypadku wystąpienia zarządzanego we wdrożeniu obsługuje tylko **FORCE LAST GOOD PLAN**. Aby uzyskać więcej informacji na temat konfigurowania opcji automatycznego dostrajania za pomocą funkcji T-SQL, zobacz [Automatyczne dostrajanie wprowadza automatyczną korektę planu](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) i [automatyczną korektę planu](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o wbudowanej inteligencji używanej w automatycznym dostrajaniu, zobacz [Sztuczna inteligencja dostraja bazy danych SQL platformy Azure.](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)
