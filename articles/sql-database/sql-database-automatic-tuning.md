---
title: Omówienie dostrajania automatycznego
description: Azure SQL Database analizuje zapytanie SQL i automatycznie dostosowuje się do obciążenia użytkownika.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/06/2019
ms.openlocfilehash: 34f102b43de669b5ea03324db47ac4dfcb554133
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190761"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Dostrajanie automatyczne w Azure SQL Database

Azure SQL Database dostrajanie automatyczne zapewnia wydajność szczytową i stabilną w ramach ciągłego dostrajania wydajności na podstawie systemu AI i uczenia maszynowego.

Dostrajanie automatyczne to w pełni zarządzana usługa Intelligent Performance, która używa wbudowanej analizy do ciągłego monitorowania zapytań wykonywanych w bazie danych i automatycznie usprawnia ich wydajność. Jest to osiągane dzięki dynamicznej adaptacji bazy danych do zmieniających się obciążeń i stosowania zaleceń dostrajania. Dostrajanie automatyczne nauczy się w poziomie od wszystkich baz danych na platformie Azure za pośrednictwem AI i dynamicznie ulepsza jego akcje dostrajania. Im dłużej Azure SQL Database jest uruchamiany z automatycznym dostrajaniem na, tym lepiej.

Azure SQL Database dostrajanie automatyczne może być jedną z najważniejszych funkcji, które można włączyć, aby zapewnić stabilne i szczytowe obciążenia baz danych.

## <a name="what-can-automatic-tuning-do-for-you"></a>Co może zrobić automatyczne dostrajanie?

- Automatyczne dostrajanie wydajności baz danych usługi Azure SQL Database
- Zautomatyzowana weryfikacja zysków z wydajności
- Automatyczne wycofywanie i samokorekcja
- Historia dostrajania
- Skrypty T-SQL akcji dostrajania dla wdrożeń ręcznych
- Aktywne monitorowanie wydajności obciążeń
- Możliwość skalowania w poziomie na setkach tysięcy baz danych
- Pozytywny wpływ na zasoby DevOps i całkowity koszt posiadania

## <a name="safe-reliable-and-proven"></a>Bezpieczne, niezawodne i sprawdzone

Operacje dostrajania zastosowane do baz danych usługi Azure SQL są w pełni bezpieczne dla wydajności najbardziej intensywnych obciążeń. System został zaprojektowany z myślą o tym, aby nie zakłócać obciążeń użytkowników. Zalecenia dotyczące dostrajania automatycznego są stosowane tylko w czasie niskiego użycia. System może również tymczasowo wyłączyć operacje dostrajania automatycznego w celu ochrony wydajności obciążeń. W takim przypadku w Azure Portal zostanie wyświetlony komunikat "wyłączone przez system". Dostrajanie automatyczne uwzględnia obciążenia z najwyższym priorytetem zasobu.

Automatyczne mechanizmy dostrajania są wczesne i idealnie nadaje się do kilku milionów baz danych działających na platformie Azure. Zastosowane operacje dostrajania automatycznego są weryfikowane automatycznie w celu zapewnienia pozytywnego zwiększenia wydajności obciążeń. Zalecenia dotyczące wydajności uległa pogorszeniu są dynamicznie wykrywane i natychmiast przywracane. Po zarejestrowaniu historii dostrajania istnieje jasne śledzenie ulepszeń dostrajania wprowadzonych do poszczególnych Azure SQL Database. 

![Jak działa dostrajanie automatyczne](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database dostrajanie automatyczne udostępnia swoją podstawową logikę przy użyciu aparatu dostrajania automatycznego SQL Server. Aby uzyskać dodatkowe informacje techniczne na temat wbudowanego mechanizmu analizy, zobacz [SQL Server dostrajania automatycznego](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Użyj dostrajania automatycznego

Automatyczne dostrajanie musi być włączone w Twojej subskrypcji. Aby włączyć dostrajanie automatyczne przy użyciu Azure Portal, zobacz [Włączanie dostrajania automatycznego](sql-database-automatic-tuning-enable.md).

Dostrajanie automatyczne może działać autonomicznie poprzez automatyczne stosowanie zaleceń dostrajania, w tym zautomatyzowanej weryfikacji zysków z wydajności. 

Aby uzyskać większą kontrolę, automatyczne stosowanie zaleceń dostrajania można wyłączyć, a wskazówki dotyczące dostrajania można zastosować ręcznie za Azure Portal. Można również użyć rozwiązania, aby wyświetlić tylko zalecenia dotyczące dostrajania automatycznego, i ręcznie zastosować je za pomocą wybranych przez siebie skryptów i narzędzi. 

Aby zapoznać się z omówieniem działania dostrajania automatycznego i dla typowych scenariuszy użycia, zobacz osadzony film wideo:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Opcje dostrajania automatycznego

Opcje dostrajania automatycznego dostępne w Azure SQL Database są następujące:

| Opcja dostrajania automatycznego | Obsługa pojedynczej bazy danych i bazy danych w puli | Obsługa bazy danych wystąpień |
| :----------------------------- | ----- | ----- |
| **Create index** — identyfikuje indeksy, które mogą zwiększyć wydajność obciążenia, tworzy indeksy i automatycznie sprawdzają, czy wydajność zapytań została ulepszona. | Yes | Nie | 
| **Drop index** — identyfikuje nadmiarowe i zduplikowane indeksy codziennie, z wyjątkiem unikatowych indeksów i indeksów, które nie były używane przez długi czas (> 90 dni). Należy pamiętać, że ta opcja nie jest zgodna z aplikacjami korzystającymi z przełączania partycji i wskazówek dotyczących indeksów. Usuwanie nieużywanych indeksów nie jest obsługiwane dla warstw usług premium i Krytyczne dla działania firmy. | Yes | Nie |
| **Wymuś ostatni dobry plan** (automatyczne korekcje planu) — IDENTYFIKUJE zapytania SQL korzystające z planu wykonywania, które jest wolniejsze niż poprzedni dobry plan, i zapytania przy użyciu ostatniego znanego dobrego planu zamiast planu uległa pogorszeniu. | Yes | Yes |

Dostrajanie automatyczne identyfikuje **indeksowanie**, **Drop index**i **wymuszanie ostatniego** zaleceń dotyczących planu, które mogą zoptymalizować wydajność bazy danych i wyświetlać je w [Azure Portal](sql-database-advisor-portal.md), i udostępnia je za poorednictwem [języka T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) i [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning). Aby dowiedzieć się więcej na temat wymuszania ostatniego dobrego planu i konfigurowania opcji dostrajania automatycznego przy użyciu języka T-SQL, zobacz [dostrajanie automatyczne wprowadza automatyczne korekcje planu](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).

Możesz ręcznie zastosować zalecenia strojenia przy użyciu portalu lub zezwolić na automatyczne dostosowywanie w sposób autonomiczny. Korzyści wynikające z zapewnienia, że system autonomicznie stosują zalecenia dostosowawcze, są automatycznie sprawdzane, czy istnieje pozytywny wzrost wydajności obciążeń i czy nie wykryto znaczącej poprawy wydajności. automatycznie Przywróć zalecenie dostrajania. Należy pamiętać, że w przypadku zapytań, na które mają wpływ zalecenia strojenia, które nie są wykonywane często, faza weryfikacji może trwać do 72 godzin.

W przypadku stosowania zaleceń dotyczących dostrajania przy użyciu języka T-SQL, automatyczne sprawdzanie poprawności wydajności i mechanizmy odwrócenia nie są dostępne. Zalecenia zastosowane w ten sposób pozostaną aktywne i wyświetlone na liście zaleceń dotyczących dostrajania dla 24-48 godzin. przed automatycznym wycofaniem ich przez system. Jeśli chcesz usunąć zalecenie wcześniej, możesz je odrzucić z Azure Portal.

Opcje dostrajania automatycznego mogą być niezależnie włączane lub wyłączane na bazę danych albo mogą być konfigurowane na serwerach SQL Database i stosowane do każdej bazy danych, która dziedziczy ustawienia z serwera. Serwery SQL Database mogą dziedziczyć wartości domyślne platformy Azure dla ustawień dostrajania automatycznego. Ustawienia domyślne platformy Azure w tej chwili są ustawione na FORCE_LAST_GOOD_PLAN jest włączona, CREATE_INDEX jest włączona, a DROP_INDEX jest wyłączona.

> [!IMPORTANT]
> Od marca 2020 zmiany ustawień domyślnych platformy Azure na potrzeby automatycznego dostrajania zaczną obowiązywać w następujący sposób:
> - Nowe wartości domyślne platformy Azure będą FORCE_LAST_GOOD_PLAN = włączone, CREATE_INDEX = wyłączone i DROP_INDEX = wyłączone.
> - Istniejące serwery bez skonfigurowanych preferencji automatycznego dostrajania zostaną automatycznie skonfigurowane przy użyciu nowych ustawień domyślnych platformy Azure. Dotyczy to wszystkich klientów, którzy obecnie mają automatyczne dostrajanie w niezdefiniowanym stanie.
> - Nowo utworzone serwery zostaną automatycznie skonfigurowane przy użyciu nowych ustawień domyślnych platformy Azure (w przeciwieństwie do wcześniejszego stanu konfiguracji dostrajania automatycznego podczas tworzenia nowego serwera).
>

Skonfigurowanie opcji dostrajania automatycznego na serwerze i dziedziczenie ustawień dla baz danych należących do serwera nadrzędnego jest zalecaną metodą konfigurowania dostrajania automatycznego, ponieważ upraszcza to Zarządzanie opcjami dostrajania automatycznego dla dużej liczby baz danych.

## <a name="next-steps"></a>Następne kroki

- Aby włączyć dostrajanie automatyczne w Azure SQL Database do zarządzania obciążeniem, zobacz [Włączanie dostrajania automatycznego](sql-database-automatic-tuning-enable.md).
- Aby ręcznie przejrzeć i zastosować zalecenia dotyczące dostrajania automatycznego, zobacz [Znajdowanie i stosowanie zaleceń dotyczących wydajności](sql-database-advisor-portal.md).
- Aby dowiedzieć się, jak używać języka T-SQL do stosowania i wyświetlania zaleceń dotyczących dostrajania automatycznego, zobacz [Manage Automatic Tuning in T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).
- Aby dowiedzieć się więcej o tworzeniu powiadomień e-mail dotyczących zaleceń dotyczących dostrajania automatycznego, zobacz [powiadomienia e-mail dotyczące dostrajania automatycznego](sql-database-automatic-tuning-email-notifications.md).
- Aby dowiedzieć się więcej na temat wbudowanej analizy używanej do dostrajania automatycznego, zobacz [sztuczna inteligencja — Dostraja bazy danych Azure SQL](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)Database.
- Aby dowiedzieć się, jak działa dostrajanie automatyczne w Azure SQL Database i SQL Server 2017, zobacz [SQL Server dostrajania automatycznego](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
