---
title: Usługa Azure SQL Database — automatyczne dostrajanie | Dokumentacja firmy Microsoft
description: Usługa Azure SQL Database analizuje zapytanie SQL i automatycznie dostosowuje się do obciążenia użytkownika.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 03/06/2019
ms.openlocfilehash: 028c69294d693202b626044cb903dc3124b5d7b7
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863223"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Dostrajanie automatyczne w usłudze Azure SQL Database

Azure dostrajania automatycznego usługi SQL Database zapewnia wzrosty obciążeń wydajności i stabilności za pośrednictwem ciągłej wydajności dostrajania, na podstawie sztucznej Inteligencji i uczenia maszynowego.

Dostrajanie automatyczne jest usługą w pełni zarządzane, inteligentne wydajności, korzystającą z wbudowanych funkcji analizy w celu ciągłego monitorowania zapytania wykonywane w bazie danych i automatycznie udoskonala ich wydajność. Jest to realizowane poprzez dostosowanie dynamicznie zmieniając obciążeń i stosowanie zalecenia dotyczące dostrajania bazą danych. Dostrajanie automatyczne usługi uzyskuje informacje o poziomie ze wszystkich baz danych na platformie Azure za pomocą sztucznej Inteligencji i dynamicznie zwiększa akcji dostrajania. Usługi Azure SQL Database korzysta już z automatycznego dostrajania na, lepiej wykonuje.

Azure dostrajania automatycznego usługi SQL Database może być jednym z najważniejszych funkcji umożliwiających zapewnienie działające stabilnie i ze szczytową wydajnością obciążeń bazy danych.

## <a name="what-can-automatic-tuning-do-for-you"></a>Co zrobić automatycznego dostrajania dla Ciebie?

- Dostrajanie automatyczne wydajności baz danych Azure SQL
- Automatyczne weryfikacji wzrost wydajności
- Automatyczne wycofywanie w razie i korekty samooceny
- Historia dostrajania
- Dostrajanie akcji skryptów T-SQL do ręcznego wdrożenia
- Monitorowanie wydajności aktywne obciążenie
- Skalowanie w poziomie funkcja na setki tysięcy baz danych
- Pozytywny wpływ na zasoby infrastruktury DevOps i całkowity koszt posiadania

## <a name="safe-reliable-and-proven"></a>Bezpieczne, niezawodne i sprawdzone

Operacje zastosowane do bazy danych SQL Azure dostrajania bazy danych są pełni bezpieczne do wykonywania swoich najbardziej wymagających obciążeń. System ma zostały zaprojektowane ostrożnie nie zakłóca obciążenia użytkownika. Zalecenia dostrajania automatycznego są stosowane tylko w czasie niewielkiego wykorzystania. System może również tymczasowo wyłączyć automatycznego dostrajania operacje, aby chronić wydajność obciążenia. W takim przypadku komunikat "Wyłączone przez system" będą wyświetlane w witrynie Azure portal. Dostrajanie automatyczne w odniesieniu do obciążeń o najwyższym priorytecie zasobów.

Mechanizmy dostrajania automatycznego są dojrzałe i zostały wedle na kilka mln baz danych na platformie Azure. Zautomatyzowanych operacji dostrajania, stosowane są weryfikowane automatycznie, aby upewnić się, że dodatnia poprawy wydajności obciążeń. Zalecenia dotyczące pogorszonej wydajności są wykrywane dynamicznie i szybko przywrócić. Za pomocą historii dostrajania rejestrowane istnieje wyczyść śladu dostrajania usprawnienia do każdej bazy danych SQL Azure. 

![Jak działa automatyczne dostrajanie](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure dostrajania automatycznego usługi SQL Database udostępnia swoje podstawowe zasady logiczne z aparatem dostrajania automatycznego programu SQL Server. Aby uzyskać dodatkowe informacje techniczne na mechanizmie wbudowanym funkcjom analizy, zobacz [dostrajania automatycznego usługi SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Użyj automatycznego dostrajania

Automatyczne dostrajanie musi być włączona w Twojej subskrypcji. Aby włączyć dostrajania automatycznego za pomocą witryny Azure portal, zobacz [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md).

Dostrajanie automatyczne może działać autonomicznie za pomocą automatycznego stosowania zalecenia dotyczące dostrajania, w tym automatyczne weryfikacji wzrost wydajności. 

Aby uzyskać większą kontrolę można wyłączyć automatyczne stosowanie zalecenia dotyczące dostrajania i zalecenia dotyczące dostrajania można ręcznie stosować za pomocą witryny Azure portal. Jest również możliwe korzystanie z odpowiedniego rozwiązania do wyświetlania automatycznych zalecenia dotyczące dostrajania tylko i zastosować je ręcznie za pomocą skryptów i wybranych przez siebie narzędzi. 

Omówienie sposobu automatycznego dostrajania działa i typowe scenariusze użycia Zobacz osadzone wideo:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Opcje dostrajania automatycznego

Dostępne są następujące opcje dostrajania automatycznego dostępnych w usłudze Azure SQL Database:

| Opcję dostrajania automatycznego | Pojedynczą bazę danych i obsługi technicznej baza danych w puli | Wystąpienie obsługi bazy danych |
| :----------------------------- | ----- | ----- |
| **CREATE INDEX** — identyfikuje indeksy, które może poprawić wydajność przetwarzania obciążenia, tworzy indeksy i automatycznie sprawdza, czy wydajność kwerend uległa poprawie. | Yes | Nie | 
| **Instrukcja DROP INDEX** — identyfikuje indeksy nadmiarowy i zduplikowane raz dziennie, z wyjątkiem indeksy unikatowe i indeksy, które nie były używane przez długi czas (> 90 dni). Należy pamiętać, że w tej chwili opcja nie jest zgodne z aplikacji przy użyciu wskazówek przełączanie i indeksu partycji. | Yes | Nie |
| **WYMUŚ OSTATNI dobry PLAN** — zapytań SQL identyfikuje za pomocą planu wykonania, jest mniejsza niż poprzednie dobrego planu, która wysyła kwerendę za pomocą ostatni znany dobry plan zamiast pogorszonej planu. | Yes | Yes |

Automatyczne dostrajanie identyfikuje **CREATE INDEX**, **DROP INDEX**, i **WYMUŚ OSTATNI dobry PLAN** zaleceń, które można zoptymalizować wydajność bazy danych i przedstawia je w [Witryny azure portal](sql-database-advisor-portal.md)i udostępnia je za pośrednictwem [języka T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) i [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning). 

Można ręcznie zastosować zalecenia dotyczące dostrajania za pomocą portalu lub umożliwić dostrajania automatycznego autonomicznie stosowanie zalecenia dotyczące dostrajania za Ciebie. Korzyści wynikające z informacją o systemie autonomicznie stosowanie zalecenia dotyczące dostrajania dla Ciebie jest automatycznie weryfikuje istnieje dodatnią korzyści na wydajność obciążeń, a w przypadku braku poprawy istotnie poprawiającą wydajność wykryto będzie Przywróć automatycznie zalecenia dostrajania. Należy pamiętać, w przypadku zapytań dotyczy dostosowywania zaleceń, które nie są często wykonywane, fazy weryfikacji może potrwać do 72 godzin zgodnie z projektem.

W przypadku, gdy chcesz ręcznie zastosować dostrajania zalecenia, wydajność automatycznego sprawdzania poprawności i mechanizmy wycofanie nie są dostępne. Ponadto Ręczne stosowanie zalecenia pozostaną aktywne i widoczne na liście zaleceń przez 24-48 godzin. zanim system automatycznie wycofuje je. Jeśli chcesz usunąć wcześniej zalecenia, możesz ręcznie odrzucić je.

Opcje dostrajania automatycznego można niezależnie włączać lub wyłączać na bazę danych lub mogą być skonfigurowane na serwerze bazy danych SQL i stosowane w każdej bazie danych, która dziedziczy ustawienia z serwera. Serwery usługi SQL Database może dziedziczyć ustawień domyślnych platformy Azure dla ustawienia automatycznego dostrajania. Ustawień domyślnych platformy Azure, w tym momencie są ustawione na FORCE_LAST_GOOD_PLAN jest włączona, CREATE_INDEX jest włączona i DROP_INDEX jest wyłączona.

Konfigurowanie automatycznego dostrajania opcji na serwerze i dziedziczenie ustawień dla baz danych należących do nadrzędnego serwera jest zalecaną metodą Konfigurowanie dostrajania automatycznego, ponieważ upraszcza zarządzanie opcje dostrajania automatycznego dla dużej liczby baz danych.

## <a name="next-steps"></a>Kolejne kroki

- Aby włączyć dostrajania automatycznego usługi Azure SQL Database do zarządzania obciążenia, zobacz [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md).
- Aby ręcznie przejrzeć i stosowanie zalecenia dotyczące dostrajania automatycznego, zobacz [Znajdowanie i stosowanie zaleceń dotyczących wydajności](sql-database-advisor-portal.md).
- Aby dowiedzieć się, jak stosowanie i wyświetlanie zalecenia dotyczące dostrajania automatycznego za pomocą języka T-SQL, zobacz [Zarządzanie dostrajania automatycznego za pomocą języka T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).
- Aby dowiedzieć się więcej o tworzeniu powiadomienia e-mail dotyczące automatycznego dostrajania zalecenia, zobacz [wiadomości E-mail z powiadomieniami dostrajania automatycznego](sql-database-automatic-tuning-email-notifications.md).
- Informacje na temat wbudowanych funkcji analizy używane w dostrajania automatycznego, zobacz [sztucznej inteligencji Dostraja baz danych Azure SQL](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Aby uzyskać informacje dotyczące sposobu automatycznego dostrajania działa w usłudze Azure SQL Database i SQL server 2017, zobacz [dostrajania automatycznego usługi SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
