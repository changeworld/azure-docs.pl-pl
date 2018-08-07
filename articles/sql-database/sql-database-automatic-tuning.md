---
title: Usługa Azure SQL Database — automatyczne dostrajanie | Dokumentacja firmy Microsoft
description: Usługa Azure SQL Database analizuje zapytanie SQL i automatycznie dostosowuje się do obciążenia użytkownika.
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: 6ed113d11a0c61620ae4a9d48eee957bb979dc11
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526797"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Dostrajanie automatyczne w usłudze Azure SQL Database

Azure dostrajania automatycznego usługi SQL Database oferuje najwyższą wydajność i stałymi obciążeniami za pośrednictwem ciągłej wydajności dostrajania, korzystając ze sztucznej inteligencji.

Dostrajanie automatyczne jest w pełni zarządzana usługa, która korzysta z wbudowanej inteligencji do ciągłego monitorowania zapytania wykonywane w bazie danych i automatycznie zwiększa ich wydajność. Jest to realizowane poprzez dostosowanie dynamicznie zmieniając obciążeń i stosowanie zalecenia dotyczące dostrajania bazą danych. Dostrajanie automatyczne usługi uzyskuje informacje o poziomie ze wszystkich baz danych na platformie Azure za pomocą sztucznej inteligencji i dynamicznie zwiększa akcji dostrajania. Usługi Azure SQL Database korzysta już z automatycznego dostrajania na, lepiej wykonuje.

Usługa Azure dostrajania automatycznego usługi SQL Database może być jednym z najważniejszych funkcji umożliwiających zapewnienie działające stabilnie i ze szczytową wydajnością obciążeń.

## <a name="what-can-automatic-tuning-do-for-you"></a>Co zrobić automatycznego dostrajania dla Ciebie?

- Dostrajanie automatyczne wydajności baz danych SQL Azure
- Automatyczne weryfikacji wzrost wydajności
- Automatyczne wycofywanie w razie i korekty samooceny
- Dostrajanie Historia dziennika
- Dostrajanie akcji skryptów T-SQL do ręcznego wdrożenia
- Monitorowanie wydajności aktywne obciążenie
- Skalowanie w poziomie funkcja na setki tysięcy baz danych
- Pozytywny wpływ na zasoby infrastruktury DevOps i całkowity koszt posiadania

## <a name="safe-reliable-and-proven"></a>Bezpieczne, niezawodne i sprawdzone

Operacje dostrajania stosowane do bazy danych SQL Azure są całkowicie bezpieczne, wydajność najbardziej wymagających obciążeń. System ma zostały zaprojektowane ostrożnie nie zakłóca obciążenia użytkownika. Zalecenia dostrajania automatycznego są stosowane tylko w czasie niewielkiego wykorzystania. System może również tymczasowo wyłączyć automatycznego dostrajania operacje, aby chronić wydajność obciążenia. W takim przypadku komunikat "Wyłączone przez system" będą wyświetlane w witrynie Azure portal. Dostrajanie automatyczne w odniesieniu do obciążeń o najwyższym priorytecie zasobów.

Mechanizmy dostrajania automatycznego są dojrzałe i zostały wedle na setki tysięcy baz danych na platformie Azure. Zautomatyzowanych operacji dostrajania, stosowane są weryfikowane automatycznie, aby upewnić się, że dodatnia poprawy wydajności obciążeń. Zalecenia dotyczące pogorszonej wydajności są wykrywane dynamicznie i szybko przywrócić. Za pomocą dostrajania dziennika historii jest wyczyść śladu dostrajania usprawnienia do każdej bazy danych SQL Azure. 

![Jak działa automatyczne dostrajanie](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure dostrajania automatycznego usługi SQL Database udostępnia swoje podstawowe zasady logiczne z aparatem dostrajania automatycznego programu SQL Server. Aby uzyskać dodatkowe informacje techniczne na mechanizmie wbudowanym funkcjom analizy, zobacz [dostrajania automatycznego usługi SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Użyj automatycznego dostrajania

Automatyczne dostrajanie musi być włączone ręcznie w Twojej subskrypcji. Aby włączyć dostrajania automatycznego za pomocą witryny Azure portal, zobacz [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md).

Dostrajanie automatyczne może działać autonomicznie za pomocą automatycznego stosowania zalecenia dotyczące dostrajania, w tym automatyczne weryfikacji wzrost wydajności. 

Aby uzyskać większą kontrolę można wyłączyć automatyczne stosowanie zalecenia dotyczące dostrajania i zalecenia dotyczące dostrajania można ręcznie stosować za pomocą witryny Azure portal. Jest również możliwe korzystanie z odpowiedniego rozwiązania do wyświetlania automatycznych zalecenia dotyczące dostrajania tylko i zastosować je ręcznie za pomocą skryptów i wybranych przez siebie narzędzi. 

Omówienie sposobu automatycznego dostrajania działa i typowe scenariusze użycia Zobacz osadzone wideo:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Opcje dostrajania automatycznego

Dostępne są następujące opcje dostrajania automatycznego dostępnych w usłudze Azure SQL Database:
 1. **CREATE INDEX** — identyfikuje indeksy, które może poprawić wydajność przetwarzania obciążenia, tworzy indeksy i automatycznie sprawdza, czy wydajność kwerend uległa poprawie.
 2. **Instrukcja DROP INDEX** — identyfikuje nadmiarowe i zduplikowane indeksy i indeksy, które nie były używane dla bardzo długi czas. Należy pamiętać, że ta opcja nie jest zgodny z aplikacjami przy użyciu wskazówek przełączanie i indeksu partycji.
 3. **WYMUŚ OSTATNI dobry PLAN** — identyfikuje zapytań SQL za pomocą planu wykonania, jest mniejsza niż poprzednie dobrego planu, która wysyła kwerendę za pomocą ostatni znany dobry plan zamiast pogorszonej planu.

Usługa Azure SQL Database identyfikuje **CREATE INDEX**, **DROP INDEX**, i **WYMUŚ OSTATNI dobry PLAN** zaleceń, które można zoptymalizować bazy danych i pokazuje, w witrynie Azure portal. Znajdź więcej informacji na temat identyfikacji indeksy, które powinny zostać zmienione w [znaleźć zalecenia dotyczące indeksu w witrynie Azure portal](sql-database-advisor-portal.md). Można ręcznie zastosować zalecenia za pomocą portalu lub umożliwić usłudze Azure SQL Database, aby automatycznie zastosować zalecenia, monitorowania obciążenia po zmianie i sprawdź, czy zalecenie zwiększona wydajność przetwarzania obciążenia. 

Opcje dostrajania automatycznego można niezależnie włączać lub wyłączać na bazę danych lub można je skonfigurować na serwerach logicznych i stosowane w każdej bazie danych, która dziedziczy ustawienia z serwera. Serwerami logicznymi może dziedziczyć ustawień domyślnych platformy Azure dla ustawienia automatycznego dostrajania. Ustawień domyślnych platformy Azure, w tym momencie są ustawione na FORCE_LAST_GOOD_PLAN jest włączona, CREATE_INDEX jest włączona i DROP_INDEX jest wyłączona.

Konfigurowanie automatycznego dostrajania opcji na serwerze i dziedziczenie ustawień dla baz danych należących do nadrzędnego serwera jest zalecaną metodą Konfigurowanie dostrajania automatycznego, ponieważ upraszcza zarządzanie opcje dostrajania automatycznego dla dużej liczby baz danych.

## <a name="next-steps"></a>Kolejne kroki

- Aby włączyć dostrajania automatycznego usługi Azure SQL Database do zarządzania obciążenia, zobacz [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md).
- Aby ręcznie przejrzeć i stosowanie zalecenia dotyczące dostrajania automatycznego, zobacz [Znajdowanie i stosowanie zaleceń dotyczących wydajności](sql-database-advisor-portal.md).
- Aby dowiedzieć się więcej o tworzeniu powiadomienia e-mail dotyczące automatycznego dostrajania zalecenia, zobacz [wiadomości E-mail z powiadomieniami dostrajania automatycznego](sql-database-automatic-tuning-email-notifications.md)
- Informacje na temat wbudowanych funkcji analizy używane w dostrajania automatycznego, zobacz [sztucznej inteligencji Dostraja baz danych SQL Azure](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Aby uzyskać informacje dotyczące sposobu automatycznego dostrajania działa w usłudze Azure SQL Database i SQL server 2017, zobacz [dostrajania automatycznego usługi SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
