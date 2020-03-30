---
title: Tworzenie skalowalnych baz danych w chmurze
description: Tworzenie skalowalnych aplikacji bazy danych platformy .NET za pomocą biblioteki klienta elastycznej bazy danych
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/25/2018
ms.openlocfilehash: ae26f669ddbe2cc2c5b6e25a9c1c0229e88dc2e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823922"
---
# <a name="building-scalable-cloud-databases"></a>Tworzenie skalowalnych baz danych w chmurze

Skalowanie baz danych w poziomie można łatwo osiągnąć za pomocą skalowalnych narzędzi i funkcji usługi Azure SQL Database. W szczególności można użyć **biblioteki klienta elastycznej bazy danych** do tworzenia skalowane w poziomie baz danych i zarządzania nimi. Ta funkcja umożliwia łatwe tworzenie aplikacji podzielonych na fragmenty przy użyciu setek lub nawet tysięcy baz danych SQL platformy Azure.

Aby pobrać:

* Wersja Java biblioteki, zobacz [Maven Central Repozytorium](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* W wersji .NET biblioteki zobacz [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentacja

1. [Wprowadzenie do narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md)
2. [Funkcje elastycznej bazy danych](sql-database-elastic-scale-introduction.md)
3. [Zarządzanie mapami fragmentów](sql-database-elastic-scale-shard-map-management.md)
4. [Migrowanie istniejących baz danych w celu skalowania w poziomie](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md)
6. [Zapytania z wieloma fragmentami](sql-database-elastic-scale-multishard-querying.md)
7. [Dodawanie fragmentu przy użyciu narzędzi elastycznej bazy danych](sql-database-elastic-scale-add-a-shard.md)
8. [Aplikacje wielodostępne z elastycznymi narzędziami bazy danych i zabezpieczeniami na poziomie wiersza](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Uaktualnianie aplikacji biblioteki klienta](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Omówienie zapytań elastycznych](sql-database-elastic-query-overview.md)
11. [Słownik narzędzi elastycznych baz danych](sql-database-elastic-scale-glossary.md)
12. [Biblioteka klienta elastycznej bazy danych z platformą entity framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Elastyczna biblioteka klienta bazy danych z dapperem](sql-database-elastic-scale-working-with-dapper.md)
14. [Narzędzie scalanie dzielenia](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Liczniki wydajności dla menedżera map fragmentów](sql-database-elastic-database-client-library.md) 
16. [Często zadawane pytania dotyczące narzędzi elastycznej bazy danych](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Możliwości klienta

Skalowanie w poziomie aplikacji przy użyciu *dzielenia na fragmenty* stanowi wyzwanie zarówno dla dewelopera, jak i administratora. Biblioteka klientów upraszcza zadania zarządzania, udostępniając narzędzia, które umożliwiają zarówno deweloperom, jak i administratorom zarządzanie skalowane w poziomie baz danych. W typowym przykładzie istnieje wiele baz danych, znany jako "fragmenty", do zarządzania. Klienci znajdują się w tej samej bazie danych i istnieje jedna baza danych na klienta (schemat jednego dzierżawcy). Biblioteka klienta zawiera następujące funkcje:

- **Zarządzanie mapami niezależnego fragmentu:** tworzona jest specjalna baza danych zwana "menedżerem map niezależnego fragmentu". Zarządzanie mapami niezależnego fragmentu jest możliwość aplikacji do zarządzania metadanych o jego fragmenty. Deweloperzy mogą używać tej funkcji do rejestrowania baz danych jako fragmenty, opisać mapowania poszczególnych kluczy dzielenia na fragmenty lub zakresów kluczy do tych baz danych i obsługi tych metadanych, jak liczba i skład baz danych ewoluuje w celu odzwierciedlenia zmian pojemności. Bez biblioteki klienta elastycznej bazy danych, trzeba poświęcić dużo czasu na pisanie kodu zarządzania podczas implementowania dzielenia na fragmenty. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie mapami niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md).

- **Routing zależny od danych:** Wyobraź sobie żądanie przychodzące do aplikacji. Na podstawie wartości klucza dzielenia na fragmenty żądania, aplikacja musi określić poprawną bazę danych na podstawie wartości klucza. Następnie otwiera połączenie z bazą danych do przetwarzania żądania. Routing zależny od danych umożliwia otwieranie połączeń za pomocą jednego łatwego wywołania na mapie niezależnego fragmentu aplikacji. Routing zależny od danych był kolejnym obszarem kodu infrastruktury, który jest teraz objęty funkcją w bibliotece klienta elastycznej bazy danych. Aby uzyskać szczegółowe informacje, zobacz [Routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md).
- **Wiele-shard kwerendy (MSQ)**: Multi-shard kwerendy działa, gdy żądanie obejmuje kilka (lub wszystkie) fragmenty. Kwerenda wielodyskładowa wykonuje ten sam kod T-SQL na wszystkich fragmentach lub zestaw fragmentów. Wyniki z uczestniczących fragmentów są scalane w ogólny zestaw wyników przy użyciu semantyki UNION ALL. Funkcje udostępniane za pośrednictwem biblioteki klienta obsługuje wiele zadań, w tym: zarządzanie połączeniami, zarządzanie wątkami, obsługę błędów i przetwarzanie wyników pośrednich. MSQ może wysyłać kwerendy do setek fragmentów. Aby uzyskać szczegółowe informacje, zobacz [Kwerendy z wieloma fragmentami](sql-database-elastic-scale-multishard-querying.md).

Ogólnie rzecz biorąc klienci korzystający z narzędzi elastycznej bazy danych mogą oczekiwać, aby uzyskać pełną funkcjonalność T-SQL podczas przesyłania operacji lokalnych niezależnego fragmentu, w przeciwieństwie do operacji między fragmentami, które mają własne semantyki.



## <a name="next-steps"></a>Następne kroki

- Biblioteka klienta elastycznej bazy danych ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) - aby **pobrać** bibliotekę.

- [Wprowadzenie do narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md) — aby wypróbować **przykładową aplikację,** która demonstruje funkcje klienta.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) - do wniesienia wkładu do kodu.
- [Omówienie elastycznych zapytań usługi Azure SQL Database](sql-database-elastic-query-overview.md) — do używania zapytań elastycznych.

- [Przenoszenie danych między skalowane w poziomie baz danych w chmurze](sql-database-elastic-scale-overview-split-and-merge.md) — instrukcje dotyczące korzystania z **narzędzia do scalania podziału**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

