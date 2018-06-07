---
title: Skalowanie w poziomie przy użyciu usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Oprogramowanie jako usługa (SaaS) deweloperzy mogą łatwo utworzyć elastyczne, skalowalne baz danych w chmurze za pomocą tych narzędzi
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: e8a07f1a1d172a96646a5c71a6267239e159c267
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646572"
---
# <a name="scaling-out-with-azure-sql-database"></a>Scaling out with Azure SQL Database (Skalowanie w poziomie za pomocą usługi Azure SQL Database)
Możesz łatwo skalować w poziomie baz danych Azure SQL przy użyciu **elastycznej bazy danych** narzędzia. Te narzędzia i funkcje pozwala korzystać z zasobów bazy danych **bazy danych SQL Azure** do tworzenia rozwiązań dla obciążeń transakcyjnych, a szczególnie oprogramowania jako aplikacje usługi (SaaS). Elastyczne funkcje bazy danych składają się z:

* [Biblioteka klienta usługi elastycznej bazy danych](sql-database-elastic-database-client-library.md): funkcja, która umożliwia tworzenie i obsługa bazy danych podzielonej jest biblioteka klienta.  Zobacz [wprowadzenie do narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md).
* [Elastyczne narzędzia scalania podziału bazy danych](sql-database-elastic-scale-overview-split-and-merge.md): przenosi dane między podzielonej baz danych. Jest to przydatna do przenoszenia danych z wieloma dzierżawcami bazy danych do bazy danych pojedynczej dzierżawy (lub odwrotnie). Zobacz [samouczek narzędzi elastycznej bazy danych scalania podziału](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Zadania elastyczne bazy danych](sql-database-elastic-jobs-overview.md) (wersja zapoznawcza): umożliwia zadań zarządzania dużą liczbą baz danych Azure SQL. Łatwe wykonywanie operacji administracyjnych, takie jak zmiany schematu, Zarządzanie poświadczeniami, aktualizacje danych odwołania, zbierania danych o wydajności lub przy użyciu zadań gromadzenia danych telemetrii dzierżawy (klienta).
* [Zapytanie elastycznej bazy danych](sql-database-elastic-query-overview.md) (wersja zapoznawcza): umożliwia uruchomienie zapytania języka Transact-SQL, obejmującej wiele baz danych. Umożliwia to połączenie narzędzi do raportowania, takich jak Excel, usługa Power BI, Tableau, itp.
* [Transakcje elastycznej](sql-database-elastic-transactions-overview.md): Ta funkcja umożliwia uruchamianie transakcje obejmujące wiele baz danych w bazie danych SQL Azure. Transakcji elastycznej bazy danych są dostępne dla aplikacji .NET przy użyciu ADO .NET oraz integrują się z znanych programowania środowisko przy użyciu [System.Transaction klasy](https://msdn.microsoft.com/library/system.transactions.aspx).

Na poniższym rysunku przedstawiono architekturę, która obejmuje **elastycznej bazy danych funkcji** w odniesieniu do kolekcji baz danych.

Na rysunku kolory bazy danych reprezentują schematów. Bazy danych o tej samej kolor współużytkować ten sam schemat.

1. Zestaw **baz danych Azure SQL** są hostowane na platformie Azure przy użyciu architektury dzielenia na fragmenty.
2. **Biblioteki klienta elastycznej bazy danych** służy do zarządzania zestawem niezależnego fragmentu.
3. Podzbiór baz danych są umieszczane w **puli elastycznej**. (Zobacz [co to jest pula?](sql-database-elastic-pool.md)).
4. **Zadania elastycznej bazy danych** jest uruchamiana według harmonogramu lub ad hoc skryptów T-SQL dla wszystkich baz danych.
5. **Narzędzia do scalania podziału** służy do przenoszenia danych z jednego niezależnego fragmentu do innej.
6. **Elastycznej bazy danych zapytania** umożliwia Napisz zapytanie, które obejmuje wszystkie bazy danych w zestawie niezależnego fragmentu.
7. **Transakcje elastycznej** zezwalają na uruchamianie transakcje obejmujące wiele baz danych. 

![Narzędzia elastycznych baz danych][1]

## <a name="why-use-the-tools"></a>Dlaczego warto używać narzędzi?
Elastyczność i skalowalność dla aplikacji w chmurze została proste dla maszyn wirtualnych i magazynu obiektów blob — po prostu Dodaj odjąć jednostki lub zwiększyć zasilania. Lecz pozostają wyzwanie dla stanowych przetwarzania danych w relacyjnych baz danych. Wyzwania pojawiło się w następujących scenariuszach:

* Powiększania i zmniejszania pojemności relacyjnej bazy danych części obciążenie.
* Zarządzanie punktami HotSpot, które mogą wystąpić podczas wpływających na konkretnego podzestawu dane — takie jak zajęty end klienta (dzierżawcy).

Tradycyjnie scenariuszy, takich jak te dotyczą zainwestować w serwerach dużą skalę bazy danych do obsługi tej aplikacji. Jednak ta opcja jest ograniczona w chmurze, w którym przetwarzania się dzieje na sprzęcie masowym wstępnie zdefiniowane. Zamiast tego przesyłanie danych i przetwarzania przez wiele baz danych strukturalnych tak samo (ze skalowalnego w poziomie wzorcem znanym jako "dzielenia na fragmenty") stanowi alternatywę do tradycyjnych metod skalowanie w pionie zarówno pod względem kosztów i elastyczność.

## <a name="horizontal-and-vertical-scaling"></a>Skalowanie w poziomie i w pionie
Na poniższej ilustracji przedstawiono wymiary poziome i pionowe skalowania, które są podstawowe sposoby, które mogą być skalowane elastycznych baz danych.

![Poziome i pionowe skalowania w poziomie][2]

Skalowanie w poziomie odwołuje się do dodawania lub usuwania bazy danych, aby dopasować pojemności lub ogólnej wydajności. To jest również nazywany "skalowanie" out. Dzielenia na fragmenty, w którym danych jest podzielonym na partycje w kolekcji identycznie strukturalnych baz danych, jest to często stosowana metoda implementuje skalowanie w poziomie.  

Skalowanie w pionie odwołuje się do zwiększenie lub zmniejszenie poziomu wydajności bazy danych poszczególnych — jest to również nazywane "skalowaniu."

Większość aplikacji baz danych w skali chmury użycie kombinacji tych dwóch strategii. Na przykład aplikacja oprogramowania jako usługi mogą używać skalowanie w poziomie do obsługi administracyjnej nowych klientów końcowych i skalowanie w pionie umożliwia każdego odbiorcy końcowego bazy danych, można zwiększać i zmniejszać zasoby odpowiednio do potrzeb obciążenia.

* Skalowanie w poziomie jest zarządzany przy użyciu [biblioteki klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md).
* Skalowanie w pionie odbywa się przy użyciu poleceń cmdlet programu Azure PowerShell, aby zmienić warstwę usługi lub poprzez umieszczenie bazy danych w puli elastycznej.

## <a name="sharding"></a>Dzielenie na fragmenty
*Dzielenia na fragmenty* to technika do dystrybucji dużych ilości danych strukturalnych tak samo dla pewnej liczby niezależnych baz danych. Jest to szczególnie popularnych z deweloperami chmury tworzenie oprogramowania jako ofert usługi (SAAS) dla klientów końcowych lub firmy. Tych klientów końcowych są często określane jako "dzierżawców". Dzielenia na fragmenty mogą być wymagane dla wielu sytuacjach:  

* Całkowita ilość danych jest za duży dla ograniczenia pojedynczej bazy danych
* Przepływność transakcji ogólną obciążenie przekracza możliwości pojedynczej bazy danych
* Dzierżawcy mogą wymagać fizycznego izolacji od siebie, więc oddzielne bazy danych są wymagane dla każdego dzierżawcy
* Różne sekcje bazy danych może być konieczne znajdują się w różnych lokalizacjach geograficznych dla zgodności, wydajność lub z przyczyn geograficznymi.

W innych sytuacjach, takich jak wprowadzanie danych z urządzeń rozproszonych dzielenia na fragmenty można wypełnić zestaw baz danych, które są zorganizowane tymczasowe. Na przykład oddzielnej bazy danych może być dedykowany dla każdego dnia lub tygodnia. W takim przypadku dzielenia na fragmenty klucz musi być liczbą całkowitą reprezentującą Data (istnieje we wszystkich wierszach podzielonej tabel) i zapytania podczas pobierania informacji dotyczących zakresu dat muszą być kierowane przez aplikację do podzbioru w danym zakresie obejmujących baz danych.

Dzielenia na fragmenty działa najlepiej, gdy każda transakcja w aplikacji można ograniczyć do pojedynczej wartości klucza dzielenia na fragmenty. Która sprawia, że wszystkie transakcje są lokalne z określoną bazą danych.

## <a name="multi-tenant-and-single-tenant"></a>Wielodostępne i pojedynczej dzierżawy
Niektóre aplikacje używają najprostsza metoda tworzenia oddzielnej bazy danych dla każdego dzierżawcy. Jest to **wzorca dzielenia na fragmenty pojedynczej dzierżawy** zapewnia izolację, możliwość wykonywania kopii zapasowej i przywracania i zasobów skalowanie na poziom szczegółowości dzierżawcy. Z pojedynczej dzierżawy dzielenia na fragmenty każda baza danych jest skojarzona z wartość Identyfikatora dzierżawy określonego (lub wartość klucza klienta), ale ten klucz nie muszą zawsze być obecne w samych danych. Odpowiada aplikacji kierować każdego żądania do odpowiedniej bazy danych — i biblioteki klienta może to uprościć.

![Pojedynczej dzierżawy w porównaniu z wieloma dzierżawcami][4]

Inne scenariusze pakietu wielu dzierżawców ze sobą do bazy danych zamiast izolowanie je do oddzielnego baz danych. To jest typowe **wzorca dzielenia na fragmenty wielodostępne** - i może być prowadzony przez fakt, że aplikacja zarządza dużą liczbę małych dzierżaw. W wielodostępnym dzielenia na fragmenty wierszy w tabelach bazy danych wszystkich mają klucz, który identyfikuje identyfikator dzierżawcy lub klucz dzielenia na fragmenty. Ponownie warstwy aplikacji jest odpowiedzialny za routing żądania dzierżawcy z odpowiednią bazą danych, a to może być obsługiwana przez biblioteki klienta elastycznej bazy danych. Ponadto zabezpieczenia na poziomie wierszy może służyć do filtru wierszy, które każdego dzierżawcy można uzyskać dostępu do — Aby uzyskać więcej informacji, zobacz [wielodostępnych aplikacji za pomocą narzędzi elastycznej bazy danych i zabezpieczenia na poziomie wiersza](sql-database-elastic-tools-multi-tenant-row-level-security.md). Redystrybuowanie danych między bazami danych mogą być wymagane ze wzorcem wielodostępne dzielenia na fragmenty, a umożliwiają to narzędzie do scalania podziału elastycznej bazy danych. Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Przenoszenie danych z wielu baz danych z pojedynczej dzierżawy
Podczas tworzenia aplikacji SaaS, jest typowy oferowanie potencjalnych odbiorców wersję próbną oprogramowania. W tym przypadku jest ekonomiczna korzystanie z bazy danych z wieloma dzierżawcami danych. Jednak gdy dot, dzierżawy pojedynczej bazy danych jest lepszym rozwiązaniem, ponieważ zapewnia lepszą wydajność. Jeśli klient ma utworzony danych podczas okresu próbnego, użyj [narzędzia do scalania podziału](sql-database-elastic-scale-overview-split-and-merge.md) przenoszenia danych z wieloma dzierżawcami do nowej bazy danych jednego dzierżawcy.

## <a name="next-steps"></a>Kolejne kroki
Aby przykładową aplikację prezentującą biblioteki klienta, zobacz [wprowadzenie do narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md).

Aby dokonać konwersji istniejących baz danych do korzystania z narzędzi, zobacz [migracji istniejących baz danych do skalowania w poziomie](sql-database-elastic-convert-to-use-elastic-tools.md).

Aby wyświetlić szczegółowe informacje na temat puli elastycznej, zobacz [zagadnienia dotyczące cen i wydajności dla elastycznej puli](sql-database-elastic-pool.md), lub Utwórz nową pulę z [pule elastyczne](sql-database-elastic-pool-manage-portal.md).  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

