---
title: Tworzenie baz danych w chmurze skalowalne | Dokumentacja firmy Microsoft
description: Tworzenie skalowalnych aplikacji bazy danych platformy .NET za pomocą biblioteki klienckiej elastycznej bazy danych
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
ms.date: 09/25/2018
ms.openlocfilehash: c0d50f3a66d940618f2bc421537b113120a2eaca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475868"
---
# <a name="building-scalable-cloud-databases"></a>Tworzenie skalowalnych baz danych w chmurze

Skalowanie w poziomie bazy danych można łatwo osiągnąć za pomocą skalowalnej narzędzi i funkcji usługi Azure SQL Database. W szczególności można użyć **Biblioteka kliencka Elastic Database** tworzenie i zarządzanie nimi baz danych skalowanych w poziomie. Ta funkcja umożliwia łatwe tworzenie aplikacji podzielonej na fragmenty, za pomocą setki — lub nawet tysięcy — baz danych Azure SQL. [Zadania elastyczne](sql-database-elastic-jobs-powershell.md) następnie może służyć do pomocy ułatwiają zarządzanie tymi bazami danych.

Aby pobrać:

* Wersja języka Java, biblioteki, zobacz [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* .NET w wersji biblioteki, zobacz [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentacja

1. [Wprowadzenie do narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md)
2. [Funkcje elastycznej bazy danych](sql-database-elastic-scale-introduction.md)
3. [Zarządzanie mapami fragmentów](sql-database-elastic-scale-shard-map-management.md)
4. [Migrowanie istniejących baz danych do skalowania w poziomie](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md)
6. [Zapytania z wieloma fragmentami](sql-database-elastic-scale-multishard-querying.md)
7. [Dodawanie fragmentu, za pomocą narzędzi elastycznych baz danych](sql-database-elastic-scale-add-a-shard.md)
8. [Wielodostępne aplikacje za pomocą narzędzi elastycznych baz danych i zabezpieczenia na poziomie wiersza](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Uaktualnianie aplikacji biblioteki klienta](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Omówienie zapytań elastycznych](sql-database-elastic-query-overview.md)
11. [Słownik narzędzi elastycznych baz danych](sql-database-elastic-scale-glossary.md)
12. [Biblioteka kliencka elastic Database przy użyciu platformy Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Biblioteka kliencka elastic database w połączeniu z programem Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Narzędzie do dzielenia i scalania](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Liczniki wydajności dla menedżera map fragmentów](sql-database-elastic-database-client-library.md) 
16. [Często zadawane pytania dotyczące narzędzi elastycznej bazy danych](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Możliwości klienta

Skalowanie aplikacji przy użyciu *fragmentowania* przedstawia wyzwania zarówno projektanta, a także administrator. Biblioteki klienta upraszcza zadania zarządzania, udostępniając narzędzia, które umożliwiają zarówno deweloperom i administratorom zarządzanie skalowanymi bazami danych. W typowym przykładem są wiele baz danych, nazywane "fragmentów," do zarządzania. Klienci wspólnie znajdują się w tej samej bazy danych i ma jedną bazę danych na klienta (schemat jednej dzierżawy). Biblioteka klienta zawiera następujące funkcje:

- **Zarządzanie mapami fragmentów**: Specjalne bazy danych o nazwie "Menedżera mapowań fragmentów" jest tworzony. Zarządzanie mapami fragmentów jest możliwość dla aplikacji do zarządzania metadane dotyczące jego fragmentów. Deweloperzy mogą używać tej funkcji do rejestrowania bazy danych jako fragmentów, opisz mapowania fragmentowania poszczególnych kluczy lub kluczy zakresach tych baz danych i Obsługa metadanych jako numer i kompozycji baz danych rozwoju w celu odzwierciedlenia zmian pojemności. Bez Biblioteka klienta elastycznej bazy danych należy poświęcić dużo czasu na pisanie kodu zarządzania przy implementowaniu fragmentowania. Aby uzyskać więcej informacji, zobacz [procesu zarządzania mapą fragmentów](sql-database-elastic-scale-shard-map-management.md).

- **Routing zależny od danych**: Wyobraź sobie żądania do aplikacji. Oparte na wartości klucza fragmentowania żądania, aplikację Aby określić prawidłową bazę danych na podstawie wartości klucza. Otwiera połączenie z bazą danych, aby przetworzyć żądanie. Routing zależny od danych zapewnia możliwość otwierania połączenia za pomocą pojedynczego wywołania łatwy do mapowania fragmentów w aplikacji. Routing zależny od danych była inna część kodu infrastruktury, który jest teraz objęta funkcji w bibliotece klienckiej elastycznej bazy danych. Aby uzyskać więcej informacji, zobacz [routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md).
- **Zapytania z wieloma fragmentami (MSQ)**: Wykonywanie zapytań w wielu fragmentów działa, gdy żądanie obejmuje kilka (lub wszystkie) fragmentów. Zapytania wielu fragmentów wykonuje ten sam kod języka T-SQL na wszystkich fragmentów lub zestaw fragmentów. Wyniki z uczestniczących w programie fragmentów są scalane w ogólny wynik, można ustawić przy użyciu semantyki UNION ALL. Funkcji dostępnych za pośrednictwem biblioteki klienta obsługuje wiele zadań, takich jak: Zarządzanie połączeniami, zarządzanie wątkami, obsługi błędów i przetwarzanie wyników pośrednich. MSQ mogą wysyłać zapytania do setek fragmentów. Aby uzyskać więcej informacji, zobacz [zapytań z wieloma fragmentami](sql-database-elastic-scale-multishard-querying.md).

Ogólnie rzecz biorąc klienci korzystający z narzędzi elastycznych baz danych mogą oczekiwać uzyskanie pełnej funkcjonalności języka T-SQL podczas przesyłania operacje lokalnego fragmentu w przeciwieństwie do operacji obejmujących wiele fragmentów, które mają własne semantyki.



## <a name="next-steps"></a>Kolejne kroki

- Biblioteka kliencka elastic Database ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) — aby **Pobierz** biblioteki.

- [Rozpocznij pracę z narzędziami elastycznej bazy danych](sql-database-elastic-scale-get-started.md) — próby **przykładową aplikację** którym przedstawiono funkcje klienta.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) — do swojego wkładu do kodu.
- [Omówienie zapytania elastycznego usługi Azure SQL Database](sql-database-elastic-query-overview.md) — Aby korzystać z elastycznych zapytań.

- [Przenoszenie danych między bazami danych w chmurze skalowanych w poziomie](sql-database-elastic-scale-overview-split-and-merge.md) — Aby uzyskać instrukcje na temat korzystania z **narzędzie do dzielenia i scalania**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

