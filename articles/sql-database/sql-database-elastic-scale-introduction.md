---
title: Skalowanie w poziomie za pomocą usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Oprogramowanie deweloperów usługi (SaaS) można łatwo utworzyć elastyczne, skalowalne bazy danych w chmurze za pomocą tych narzędzi
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 59701c31e461bbd5d73ec708504139347f6075f2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241869"
---
# <a name="scaling-out-with-azure-sql-database"></a>Scaling out with Azure SQL Database (Skalowanie w poziomie za pomocą usługi Azure SQL Database)
Można łatwo skalować w poziomie baz danych Azure SQL przy użyciu **Elastic Database** narzędzia. Te narzędzia i funkcje pozwalają korzystać z zasobów bazy danych **usługi Azure SQL Database** nad tworzeniem rozwiązań dla obciążeń transakcyjnych, a szczególnie oprogramowanie jako usługa (SaaS) aplikacji. Funkcje elastycznej bazy danych składają się:

* [Biblioteka kliencka elastic Database](sql-database-elastic-database-client-library.md): Biblioteka klienta jest funkcją, która pozwala na tworzenie i obsługa baz danych podzielonych na fragmenty.  Zobacz [Rozpocznij pracę z narzędziami elastycznej bazy danych](sql-database-elastic-scale-get-started.md).
* [Narzędzie do dzielenia i scalania elastycznych baz danych](sql-database-elastic-scale-overview-split-and-merge.md): przenosi dane między bazami danych podzielonych na fragmenty. To narzędzie jest przydatne w przypadku przenoszenia danych z wielodostępną bazą danych do bazy danych jedną dzierżawą (lub odwrotnie). Zobacz [Elastic database Split-Merge tool samouczek](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Zadania elastic Database](elastic-jobs-overview.md): Używanie zadań do zarządzania dużą liczbą baz danych Azure SQL. Łatwo wykonywać operacje administracyjne, takie jak zmiany schematu, Zarządzanie poświadczeniami, aktualizacje danych referencyjnych, zbierania danych o wydajności lub zbieranie danych telemetrycznych dzierżaw (klientów) przy użyciu zadań.
* [Zapytanie elastic Database](sql-database-elastic-query-overview.md) (wersja zapoznawcza): Umożliwia uruchamianie zapytanie Transact-SQL, która obejmuje wiele baz danych. Umożliwia to połączenie narzędzi do raportowania, takie jak Excel, Power BI, Tableau, itp.
* [Transakcje elastyczne](sql-database-elastic-transactions-overview.md): Ta funkcja umożliwia uruchamianie transakcje obejmujące wiele baz danych w usłudze Azure SQL Database. Transakcje elastyczne bazy danych są dostępne dla aplikacji .NET za pomocą ADO .NET i integracji z dobrze znanych programowania środowisko przy użyciu [klasy System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

Na poniższym rysunku przedstawiono architekturę, która obejmuje **funkcje Elastic Database** względem kolekcji baz danych.

Na poniższej ilustracji kolory, bazy danych reprezentują schematów. Bazy danych przy użyciu tego samego koloru współużytkują ten sam schemat.

1. Zbiór **baz danych Azure SQL** znajduje się na platformie Azure przy użyciu architecture dzielenie na fragmenty.
2. **Biblioteka kliencka Elastic Database** służy do zarządzania zestawem fragmentu.
3. Podzbiór baz danych są umieszczane w **puli elastycznej**. (Zobacz [co to jest pula?](sql-database-elastic-pool.md)).
4. **Zadania Elastic Database** uruchamia skrypty T-SQL według harmonogramu ani ad hoc dla wszystkich baz danych.
5. **Narzędzie do dzielenia i scalania** służy do przenoszenia danych z jednego fragmentu do innego.
6. **Zapytaniu Elastic Database** można napisać zapytanie, które obejmuje wszystkie bazy danych w zestawie fragmentu.
7. **Transakcje elastyczne** umożliwiają uruchamianie transakcje obejmujące wiele baz danych. 

![Narzędzia elastycznych baz danych][1]

## <a name="why-use-the-tools"></a>Dlaczego warto używać narzędzi?
Osiągnięcie elastyczności i skalowania dla aplikacji w chmurze zostało proste dla maszyn wirtualnych i magazynu obiektów blob — po prostu Dodaj usuwaj jednostki lub zwiększenia mocy. Ale pozostają wyzwanie dla stanowych przetwarzania danych w relacyjnych baz danych. Wyzwania pojawiło się w następujących scenariuszach:

* Rozwija i zmniejszanie pojemności dla relacyjnej bazy danych części obciążenia.
* Zarządzanie hotspotami, które mogą wystąpić podczas wpływających na określony podzbiór danych — takich jak zajęty klientów końcowych (dzierżawa).

Tradycyjnie scenariusze, takie jak te dotyczą inwestować w serwery bazy danych w większej skali, do obsługi tej aplikacji. Jednak ta opcja jest ograniczona w chmurze, w którym wszystkie przetwarzanie odbywa się na sprzęcie masowym wstępnie zdefiniowane. Zamiast tego dystrybucję danych i przetwarzania w wielu bazach danych strukturalnych identycznie (znana jako "fragmentowania" wzorca skalowalnego w poziomie) stanowi alternatywę dla tradycyjnego podejścia skalowanie w górę, zarówno pod względem kosztów i elastyczność.

## <a name="horizontal-and-vertical-scaling"></a>Skalowanie w poziomie i pionie
Na poniższej ilustracji przedstawiono poziome i pionowe wymiary skalowania, które są podstawowe sposoby, które mogą być skalowane elastycznych baz danych.

![Poziome i pionowe skalowalnego w poziomie][2]

Skalowanie w poziomie odnosi się do dodawania lub usuwania bazy danych w celu dostosowania pojemności lub ogólnej wydajności, również przywołane "skalowanie". Dzielenie na fragmenty, w którym dane są partycjonowane na zbiór identycznie ze strukturą baz danych, jest typowym sposobem wdrożenia, skalowanie w poziomie.  

Skalowanie w pionie odwołuje się do zwiększenie lub zmniejszenie rozmiaru obliczeń poszczególnych baz danych, znany także jako "skalowanie w górę."

Większość aplikacji baz danych w skali chmury użyć kombinacji tych dwóch strategii. Na przykład aplikacja oprogramowania jako usługi może użyć skalowanie w poziomie do aprowizowania nowych klientów końcowych i skalowanie w pionie umożliwia bazy danych każdego klienta końcowego zwiększać i zmniejszać zasoby zgodnie z potrzebami przez obciążenie.

* Skalowanie w poziomie jest zarządzane przy użyciu [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md).
* Skalowanie w pionie odbywa się przy użyciu poleceń cmdlet programu Azure PowerShell, aby zmienić warstwę usługi lub poprzez umieszczenie bazy danych w puli elastycznej.

## <a name="sharding"></a>Dzielenie na fragmenty
*Dzielenie na fragmenty* to technika, aby rozdystrybuować dużych ilości danych strukturalnych identycznie wiele niezależnych baz danych. Jest to szczególnie popularne wśród deweloperów rozwiązań w chmurze tworzenia oprogramowania jako oferty usługi (SAAS) dla klientów końcowych lub firmy. Ci klienci końcowi często są określane jako "dzierżawy". Dzielenie na fragmenty może być wymagany przez dowolną liczbę powodów:  

* Łączna ilość danych jest zbyt duży, aby zmieścić w ramach ograniczeń poszczególnych baz danych
* Przepływność transakcji całkowitego obciążenia przekracza możliwości poszczególnych baz danych
* Dzierżawcy mogą wymagać fizyczne odizolowanie od siebie, więc osobne bazy danych są wymagane dla każdego dzierżawcy
* Poszczególne sekcje bazy danych może być konieczne znajdują się w różnych lokalizacjach geograficznych dla zgodności, wydajność lub z przyczyn geopolitycznych.

W innych scenariuszach, takich jak pozyskiwania danych z urządzeń rozproszonych dzielenia na fragmenty może służyć do wypełniania zestawu baz danych, które są zorganizowane czasowo. Na przykład oddzielnej bazy danych mogą być przeznaczone do obsługi każdego dnia lub tygodnia. W takim przypadku klucz fragmentowania może być liczbą całkowitą reprezentującą datę (istnieje we wszystkich wierszach tabel podzielonej na fragmenty) i pobieranie informacji o zakres dat zapytania muszą być kierowane przez aplikację do podzbioru baz danych obejmujących zakres w danym.

Dzielenie na fragmenty działa najlepiej, gdy uwzględniające każdą czynność w aplikacji można ograniczyć do pojedynczej wartości klucza fragmentowania. Która gwarantuje, że wszystkie transakcje są lokalne w konkretnej bazy danych.

## <a name="multi-tenant-and-single-tenant"></a>Wielodostępne i pojedynczej dzierżawy
Niektóre aplikacje używają najprostszą metodą tworzenia oddzielnej bazy danych dla każdego dzierżawcy. To podejście jest **wzorzec fragmentowania pojedynczej dzierżawy** zapewniająca izolację, możliwość wykonywania kopii zapasowej/przywracania i zasobów, skalowanie na poziomie szczegółowości dzierżawy. Za pomocą fragmentowania pojedynczej dzierżawy każda baza danych jest skojarzona z wartością Identyfikatora dzierżawy określonego (lub wartość klucza klienta), ale ten klucz nie muszą zawsze być obecne w samych danych. Odpowiada za aplikację do rozsyłania każdego żądania do odpowiedniej bazy danych — i biblioteki klienta można uproszczenie tej informacji.

![Pojedynczej dzierżawy w porównaniu z wieloma dzierżawcami][4]

Inne scenariusze pakietu wielu dzierżaw razem do bazy danych zamiast izolowanie ich do osobnych baz danych. Ten wzorzec jest typowa **wzorzec fragmentowania wielodostępnych** — i opartych na fakt, że aplikacja zarządza dużą liczbę małych dzierżaw. W fragmentowania wielodostępnych wierszy w tabelach bazy danych wszystkich służą do przenoszenia klucz, który identyfikuje identyfikator dzierżawy lub klucz fragmentowania. Ponownie w warstwie aplikacji jest odpowiedzialny za routing żądanie dzierżawy z odpowiednią bazą danych i to może być obsługiwany przez Biblioteka kliencka elastic database. Ponadto, zabezpieczenia na poziomie wierszy może służyć do filtra wierszy, które każdego dzierżawcy mogą uzyskiwać dostęp do — Aby uzyskać szczegółowe informacje, zobacz [wielodostępnych aplikacji za pomocą narzędzi elastycznych baz danych i zabezpieczenia na poziomie wiersza](sql-database-elastic-tools-multi-tenant-row-level-security.md). Ponowne dystrybuowanie danych w bazach danych mogą być potrzebne przy użyciu wzorzec fragmentowania wielu dzierżawców i jest zapewniana przez narzędzie do dzielenia i scalania elastycznej bazy danych. Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Przenoszenie danych z wielu dzierżawców z pojedynczej bazy danych
Podczas tworzenia aplikacji SaaS, jest zazwyczaj oferują potencjalnych klientów z wersji próbnej oprogramowania. W tym przypadku jest ekonomiczna do użycia z wielodostępną bazą danych dla danych. Jednak gdy z dot, pojedynczej dzierżawy baza danych jest lepiej, ponieważ zapewnia lepszą wydajność. Jeśli klient utworzona danych w trakcie okresu próbnego, użyj [narzędzie do dzielenia i scalania](sql-database-elastic-scale-overview-split-and-merge.md) przenoszenia danych z wieloma dzierżawami, z jedną dzierżawą bazą danych.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać przykładową aplikację, która pokazuje biblioteki klienta, zobacz [Rozpocznij pracę z narzędziami elastycznej bazy danych](sql-database-elastic-scale-get-started.md).

Aby dokonać konwersji istniejących baz danych w celu korzystania z narzędzi, zobacz [Migrowanie istniejących baz danych do skalowania w poziomie](sql-database-elastic-convert-to-use-elastic-tools.md).

Aby wyświetlić szczegółowe informacje na temat elastycznej puli, zobacz [zagadnienia dotyczące cen i wydajności dla puli elastycznej](sql-database-elastic-pool.md), lub utworzyć nową pulę z [pul elastycznych](sql-database-elastic-pool-manage-portal.md).  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

