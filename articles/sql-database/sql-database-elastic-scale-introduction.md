---
title: Skalowanie w poziomie
description: Programiści software as a Service (SaaS) mogą łatwo tworzyć elastyczne, skalowalne bazy danych w chmurze za pomocą tych narzędzi
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 44c6147402cbe05e62c091863cb0bd4f9235bfab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061644"
---
# <a name="scaling-out-with-azure-sql-database"></a>Scaling out with Azure SQL Database (Skalowanie w poziomie za pomocą usługi Azure SQL Database)
Można łatwo skalować w poziomie bazy danych SQL platformy Azure przy użyciu narzędzi **elastycznej bazy danych.** Te narzędzia i funkcje umożliwiają korzystanie z zasobów bazy danych **usługi Azure SQL Database** do tworzenia rozwiązań dla obciążeń transakcyjnych, a zwłaszcza aplikacji Software as a Service (SaaS). Funkcje elastycznej bazy danych składają się z:

* [Biblioteka klienta elastycznej bazy danych:](sql-database-elastic-database-client-library.md)Biblioteka klienta jest funkcją, która umożliwia tworzenie i obsługa podzielonych baz danych.  Zobacz [Wprowadzenie do narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md).
* [Narzędzie do dzielenia i scalania elastycznej bazy danych:](sql-database-elastic-scale-overview-split-and-merge.md)przenosi dane między podzielonymi bazami danych. To narzędzie jest przydatne do przenoszenia danych z bazy danych z wieloma dzierżawami do bazy danych z jedną dzierżawą (lub odwrotnie). Zobacz [elastyczna baza danych Dzielenie scalania narzędzie samouczek](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Zadania elastycznej bazy danych:](elastic-jobs-overview.md)zadania używają zadań do zarządzania dużą liczbą baz danych SQL platformy Azure. Łatwe wykonywanie operacji administracyjnych, takich jak zmiany schematu, zarządzanie poświadczeniami, aktualizacje danych referencyjnych, zbieranie danych o wydajności lub zbieranie danych telemetrycznych dzierżawy (klienta) przy użyciu zadań.
* [Zapytanie elastycznej bazy danych](sql-database-elastic-query-overview.md) (wersja zapoznawcza): Umożliwia uruchomienie kwerendy Transact-SQL obejmującej wiele baz danych. Umożliwia to połączenie z narzędziami do raportowania, takimi jak Excel, Power BI, Tableau itp.
* [Transakcje elastyczne:](sql-database-elastic-transactions-overview.md)Ta funkcja umożliwia uruchamianie transakcji obejmujących kilka baz danych w bazie danych SQL Azure. Transakcje elastycznej bazy danych są dostępne dla aplikacji .NET przy użyciu ADO .NET i integrują się ze znanym doświadczeniem programowania przy użyciu [klasy System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

Na poniższej ilustracji przedstawiono architekturę, która zawiera **funkcje elastycznej bazy danych** w odniesieniu do kolekcji baz danych.

W tej grafice kolory bazy danych reprezentują schematy. Bazy danych o tym samym kolorze współużytkuje ten sam schemat.

1. Zestaw **baz danych SQL platformy Azure** jest hostowany na platformie Azure przy użyciu architektury dzielenia na fragmenty.
2. **Biblioteka klienta elastycznej bazy danych** służy do zarządzania zestawem niezależnego fragmentu.
3. Podzbiór baz danych jest umieszczany w **puli elastycznej**. (Zobacz [Co to jest basen?](sql-database-elastic-pool.md)).
4. **Zadanie elastycznej bazy danych** uruchamia zaplanowane lub ad hoc skrypty T-SQL dla wszystkich baz danych.
5. **Narzędzie do scalania podziału** służy do przenoszenia danych z jednego fragmentu do drugiego.
6. **Zapytanie elastycznej bazy danych** umożliwia pisanie kwerendy, która obejmuje wszystkie bazy danych w zestawie niezależnego fragmentu.
7. **Transakcje elastyczne** umożliwiają uruchamianie transakcji obejmujących kilka baz danych. 

![Narzędzia elastycznych baz danych][1]

## <a name="why-use-the-tools"></a>Dlaczego warto korzystać z narzędzi?
Osiągnięcie elastyczności i skali dla aplikacji w chmurze było proste dla maszyn wirtualnych i magazynu obiektów blob — wystarczy dodać lub odjąć jednostki lub zwiększyć moc. Pozostaje jednak wyzwaniem dla stanowego przetwarzania danych w relacyjnych bazach danych. W tych scenariuszach pojawiły się wyzwania:

* Zwiększanie i zmniejszanie pojemności dla relacyjnej bazy danych części obciążenia.
* Zarządzanie hotspotami, które mogą wystąpić wpływających na określony podzbiór danych — na przykład zajęty klienta końcowego (dzierżawcy).

Tradycyjnie scenariusze takie jak te zostały rozwiązane przez inwestowanie w serwery bazy danych na większą skalę do obsługi aplikacji. Jednak ta opcja jest ograniczona w chmurze, gdzie wszystkie przetwarzanie odbywa się na wstępnie zdefiniowanym sprzęcie towarowym. Zamiast tego dystrybucji danych i przetwarzania w wielu bazach danych o identycznej strukturze (wzorzec skalowania w poziomie znany jako "dzielenie na fragmenty") stanowi alternatywę dla tradycyjnych metod skalowania w górę zarówno pod względem kosztów i elastyczności.

## <a name="horizontal-and-vertical-scaling"></a>Skalowanie poziome i pionowe
Na poniższym rysunku przedstawiono poziome i pionowe wymiary skalowania, które są podstawowymi sposobami skalowania elastycznych baz danych.

![Skalowanie w poziomie a pionowe w poziomie][2]

Skalowanie w poziomie odnosi się do dodawania lub usuwania baz danych w celu dostosowania pojemności lub ogólnej wydajności, nazywane również "skalowanie w poziomie". Dzielenie na fragmenty, w którym dane są podzielone na partycje w kolekcji o identycznie ustrukturyzowanych bazach danych, jest typowym sposobem implementowania skalowania poziomego.  

Skalowanie w pionie odnosi się do zwiększania lub zmniejszania rozmiaru obliczeń pojedynczej bazy danych, znanej również jako "skalowanie w górę".

Większość aplikacji bazy danych w skali chmury używa kombinacji tych dwóch strategii. Na przykład software as a Service aplikacji może używać skalowania poziomego do aprowizowania nowych klientów końcowych i skalowanie pionowe, aby umożliwić każdej bazy danych klienta końcowego do zwiększenia lub zmniejszenia zasobów, zgodnie z potrzebami obciążenia.

* Skalowanie w poziomie jest zarządzane za pomocą [biblioteki klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md).
* Skalowanie w pionie odbywa się za pomocą poleceń cmdlet programu Azure PowerShell, aby zmienić warstwę usług lub umieszczając bazy danych w puli elastycznej.

## <a name="sharding"></a>Dzielenie na fragmenty
*Fragmentowanie* to technika dystrybucji dużej ilości danych o identycznej strukturze do wielu niezależnych baz danych. Jest to szczególnie popularne wśród deweloperów chmury tworzenia oprogramowania jako usługi (SAAS) oferty dla klientów końcowych lub firm. Ci klienci końcowi są często określane jako "najemców". Dzielenie na fragmenty może być wymagane z dowolnej liczby powodów:  

* Całkowita ilość danych jest zbyt duża, aby zmieścić się w ograniczeniach poszczególnych baz danych
* Przepływność transakcji ogólnego obciążenia przekracza możliwości pojedynczej bazy danych
* Dzierżawcy mogą wymagać fizycznej izolacji od siebie, więc oddzielne bazy danych są potrzebne dla każdej dzierżawy
* Różne sekcje bazy danych może być konieczne w różnych lokalizacjach geograficznych ze względu na zgodność, wydajność lub geopolityczne.

W innych scenariuszach, takich jak pozyskiwania danych z urządzeń rozproszonych, dzielenia na fragmenty może służyć do wypełnienia zestawu baz danych, które są zorganizowane czasowo. Na przykład oddzielna baza danych może być dedykowana dla każdego dnia lub tygodnia. W takim przypadku klucz dzielenia na fragmenty może być całkowitej reprezentującej datę (obecny we wszystkich wierszach tabel podzielonych na fragmenty) i kwerendy pobierania informacji dla zakresu dat musi być kierowane przez aplikację do podzbioru baz danych obejmujących zakres, o którym mowa.

Dzielenie na fragmenty działa najlepiej, gdy każda transakcja w aplikacji może być ograniczona do pojedynczej wartości klucza dzielenia na fragmenty. Dzięki temu wszystkie transakcje są lokalne w określonej bazie danych.

## <a name="multi-tenant-and-single-tenant"></a>Wielodostępne i jednodostępne
Niektóre aplikacje używają najprostszego podejścia do tworzenia oddzielnej bazy danych dla każdej dzierżawy. Takie podejście jest **wzorzec dzielenia na fragmenty pojedynczej dzierżawy,** który zapewnia izolację, możliwości tworzenia kopii zapasowych/przywracania zasobów i skalowanie zasobów na szczegółowość dzierżawy. W przypadku dzielenia na fragmenty pojedynczej dzierżawy każda baza danych jest skojarzona z określoną wartością identyfikatora dzierżawy (lub wartością klucza klienta), ale ten klucz nie zawsze musi znajdować się w samych danych. Jest to odpowiedzialność aplikacji do kierowania każdego żądania do odpowiedniej bazy danych — i biblioteki klienta można uprościć to.

![Pojedynczy dzierżawca a wielodostępny][4]

Inne scenariusze spakować wielu dzierżawców razem do baz danych, zamiast izolowania ich do oddzielnych baz danych. Ten wzorzec jest typowym **wzorcem dzielenia na fragmenty wielu dzierżawców** — i może być napędzany przez fakt, że aplikacja zarządza dużą liczbą małych dzierżawców. W dzieleniu na fragmenty z wieloma dzierżawcami wiersze w tabelach bazy danych są przeznaczone do przenoszenia klucza identyfikującego identyfikator dzierżawy lub klucz dzielenia na fragmenty. Ponownie warstwa aplikacji jest odpowiedzialny za routing żądania dzierżawcy do odpowiedniej bazy danych, a to może być obsługiwane przez bibliotekę klienta elastycznej bazy danych. Ponadto zabezpieczenia na poziomie wiersza mogą służyć do filtrowania wierszy, do których każda dzierżawa może uzyskać dostęp — aby uzyskać szczegółowe informacje, zobacz [Aplikacje wielodostępne z narzędziami elastycznej bazy danych i zabezpieczeniami na poziomie wiersza.](sql-database-elastic-tools-multi-tenant-row-level-security.md) Redystrybucja danych między bazami danych może być potrzebne za pomocą wzorca dzielenia na fragmenty wielu dzierżawców i jest ułatwiony przez narzędzie scalania podziału elastycznej bazy danych. Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Przenoszenie danych z wielu do pojedynczych baz danych najmu
Podczas tworzenia aplikacji SaaS, to jest typowe, aby zaoferować potencjalnym klientom wersję próbną oprogramowania. W takim przypadku jest opłacalne użycie bazy danych z wieloma dzierżawami dla danych. Jednak gdy perspektywa staje się klientem, bazy danych z jedną dzierżawą jest lepsza, ponieważ zapewnia lepszą wydajność. Jeśli klient utworzył dane w okresie próbnym, użyj [narzędzia scalania podziału,](sql-database-elastic-scale-overview-split-and-merge.md) aby przenieść dane z wielu dzierżawców do nowej bazy danych z jedną dzierżawą.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać przykładową aplikację, która demonstruje bibliotekę klienta, zobacz [Wprowadzenie do narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md).

Aby przekonwertować istniejące bazy danych na korzystanie z tych narzędzi, zobacz [Migrowanie istniejących baz danych w celu skalowania w poziomie](sql-database-elastic-convert-to-use-elastic-tools.md).

Aby zobaczyć specyfikę puli elastycznej, zobacz [Zagadnienia dotyczące ceny i wydajności dla puli elastycznej](sql-database-elastic-pool.md)lub utwórz nową pulę z [elastycznymi pulami.](sql-database-elastic-pool-manage-portal.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

