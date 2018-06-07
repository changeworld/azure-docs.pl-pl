---
title: Tworzenie baz danych w chmurze skalowalne | Dokumentacja firmy Microsoft
description: Tworzenie skalowalnych aplikacji .NET bazy danych za pomocą biblioteki klienta elastycznej bazy danych
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 4a29af7d92dbf83672907af42d2222d9c7b1ef39
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645042"
---
# <a name="building-scalable-cloud-databases"></a>Tworzenie skalowalnych baz danych w chmurze
Skalowanie w poziomie baz danych można łatwo osiągnąć za pomocą funkcji i narzędzi skalowalne bazy danych SQL Azure. W szczególności można użyć **biblioteki klienta elastycznej bazy danych** tworzenie i zarządzanie nimi skalowalnych w poziomie baz danych. Ta funkcja pozwala łatwo tworzyć aplikacje podzielonej przy użyciu setki —, a nawet tysiące — baz danych Azure SQL. [Zadania elastyczne](sql-database-elastic-jobs-powershell.md) mogą następnie służyć do pomocy ułatwiają zarządzanie tych baz danych.

Aby pobrać:
* wersja języka Java biblioteki, zobacz [Maven centralnym repozytorium](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* .NET w wersji biblioteki, zobacz [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentacja
1. [Wprowadzenie do narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md)
2. [Elastyczne funkcje bazy danych](sql-database-elastic-scale-introduction.md)
3. [Zarządzanie mapami fragmentów](sql-database-elastic-scale-shard-map-management.md)
4. [Migrowanie istniejących baz danych do skalowania w poziomie](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md)
6. [Wiele niezależnych zapytań](sql-database-elastic-scale-multishard-querying.md)
7. [Dodawanie niezależnych, za pomocą narzędzi elastycznej bazy danych](sql-database-elastic-scale-add-a-shard.md)
8. [Aplikacje wielodostępne z narzędzi elastycznej bazy danych i zabezpieczenia na poziomie wiersza](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Uaktualnij klienta biblioteki aplikacji](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Elastyczne zapytań — omówienie](sql-database-elastic-query-overview.md)
11. [Słownik narzędzi elastycznych baz danych](sql-database-elastic-scale-glossary.md)
12. [Biblioteka klienta usługi elastycznej bazy danych z programu Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Biblioteki klienta elastycznej bazy danych z Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Narzędzie do scalania podziału](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Liczniki wydajności dla menedżera map fragmentów](sql-database-elastic-database-client-library.md) 
16. [Często zadawane pytania dotyczące narzędzi elastycznej bazy danych](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Możliwości klienta
Skalowanie w poziomie aplikacji przy użyciu *dzielenia na fragmenty* przedstawia wyzwania zarówno dewelopera, a także przez administratora. Biblioteki klienckiej upraszcza zadania zarządzania, udostępniając narzędzi umożliwiających zarówno deweloperzy i Administratorzy Zarządzanie bazami danych skalowalnych w poziomie. W typowym przykładem istnieje wiele baz danych, nazywane "odłamków", aby zarządzać. Klienci są wspólnie w tej samej bazy danych, a jest jedną bazę danych na klienta (schemat pojedynczej dzierżawy). Biblioteka klienta zawiera następujące funkcje:

- **Zarządzanie mapy niezależnego fragmentu**: utworzono specjalne bazy danych o nazwie "Menedżer mapy niezależnego fragmentu". Zarządzanie mapy niezależnego fragmentu jest zdolność do zarządzania metadane dotyczące jego odłamków aplikacji. Deweloperzy mogą używać tej funkcji do rejestrowania baz danych jako odłamków, opisu mapowania poszczególnych dzielenia na fragmenty kluczy lub kluczy zakresów do tych baz danych i Obsługa metadanych jako numer i kompozycji baz danych rozwoju środowisko w celu odzwierciedlenia zmian pojemności. Bez biblioteki klienta elastycznej bazy danych konieczne będzie poświęcają mnóstwo czasu pisanie kodu zarządzania podczas wdrażania dzielenia na fragmenty. Aby uzyskać więcej informacji, zobacz [zarządzania mapy niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md).

- **Routing zależnych danych**: Wyobraź sobie żądanie do aplikacji. Oparte na wartości klucza dzielenia na fragmenty żądania, aplikacja musi określić prawidłową bazę danych na podstawie wartości klucza. Otwiera połączenie z bazą danych do przetwarzania żądania. Routing zależnych danych zapewnia możliwość otwierania połączenia przy użyciu jednego wywołania łatwe do mapy niezależnych aplikacji. Routing zależnych danych był inny obszar kodu infrastruktury, który jest teraz objęte funkcje biblioteki klienta elastycznej bazy danych. Aby uzyskać więcej informacji, zobacz [danych zależnych routingu](sql-database-elastic-scale-data-dependent-routing.md).
- **Wiele niezależnych kwerendy (MSQ)**: badania wielu niezależnych działa, gdy żądanie obejmuje odłamków kilka (lub wszystkie). Zapytania wielu niezależnych wykonuje ten sam kod T-SQL na wszystkich odłamków lub zbiór fragmentów. Wyniki z uczestniczących odłamków są scalane w ogólnej zestawu, używając semantyki UNION ALL wyników. Funkcji za pośrednictwem biblioteki klienta obsługuje wiele zadań, takich jak: Zarządzanie połączeniami, zarządzanie wątkami Obsługa błędów i pośrednich wyników przetwarzania. MSQ można badać setki fragmentów. Aby uzyskać więcej informacji, zobacz [zapytań wielu niezależnych](sql-database-elastic-scale-multishard-querying.md).

Ogólnie rzecz biorąc klientów przy użyciu narzędzi elastycznej bazy danych można oczekiwać, że uzyskać pełną funkcjonalność T-SQL podczas przesyłania operacji niezależnych lokalnego zamiast niezależnego fragmentu między operacje, które mają własne semantyki.



## <a name="next-steps"></a>Kolejne kroki

- Biblioteka klienta usługi elastycznej bazy danych ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) — aby **Pobierz** biblioteki.

- [Wprowadzenie do narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md) — spróbuj **Przykładowa aplikacja** pokazuje, że funkcje klienta.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) — aby wkład do kodu.
- [Omówienie zapytania elastycznej bazy danych SQL Azure](sql-database-elastic-query-overview.md) — umożliwia elastyczne zapytania.

- [Przenoszenie danych między bazami danych w chmurze skalowalnych w poziomie](sql-database-elastic-scale-overview-split-and-merge.md) — instrukcje dotyczące używania **narzędzia do scalania podziału**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

