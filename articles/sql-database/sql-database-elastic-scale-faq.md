---
title: Elastyczne skalowanie Azure SQL — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące elastycznego skalowania bazy danych Azure SQL.
services: sql-database
documentationcenter: ''
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 57a5ef07aa8ca6d35085f9b2425cbc1c08c23b36
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="elastic-database-tools-faq"></a>Często zadawane pytania dotyczące narzędzi elastycznej bazy danych
#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Jak wypełnić klucza dzielenia na fragmenty informacji schematu posiadający pojedynczej dzierżawy na niezależnych i żaden klucz dzielenia na fragmenty?
Obiekt informacji schematu służy tylko do dzielenia scenariusze scalania. Jeśli aplikacja jest z założenia pojedynczej dzierżawy, nie jest wymagane narzędzie podziału scalania i w związku z tym nie jest konieczne do wypełniania obiektu informacji schematu.

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Zostały udostępnionych bazy danych i Menedżera Map niezależnego fragmentu mają już, jak zarejestrować tej nowej bazy danych jako niezależnych?
Zobacz  **[Dodawanie niezależnych do aplikacji za pomocą biblioteki klienta elastycznej bazy danych](sql-database-elastic-scale-add-a-shard.md)**. 

#### <a name="how-much-do-elastic-database-tools-cost"></a>Ile koszt narzędzi elastycznej bazy danych?
Za pomocą biblioteki klienta elastycznej bazy danych nie wpływa negatywnie kosztów. Koszty są naliczane tylko w przypadku baz danych Azure SQL używających odłamków i Manager mapy niezależnego fragmentu, jak również ról sieć web/proces roboczy, do których udostępniania dla narzędzia do scalania podziału.

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Dlaczego moje poświadczenia nie działają po dodaniu niezależnego fragmentu z innego serwera?
Nie należy używać poświadczeń w formie "Nazwa użytkownika =username@servername", po prostu użyć zamiast tego "identyfikator użytkownika = nazwa użytkownika".  Upewnij się również, że nazwa logowania "nazwa_użytkownika" ma uprawnienia na niezależnego fragmentu.

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Należy utworzyć Menedżera Map niezależnego fragmentu i wypełnić odłamków w każdym uruchomieniu aplikacje?
Nie — tworzenie Menedżera Map niezależnego fragmentu (na przykład  **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) jest to jednorazowa operacja.  Aplikacja powinna używać wywołania **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** w momencie uruchamiania aplikacji.  Ma takich tylko jedno wywołanie dla domeny aplikacji.

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Pytania dotyczące korzystania z narzędzi elastycznej bazy danych, jak ich odpowiedzi uzyskać?
Sprawdź dotrzeć do nas na [forum usługi Azure SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Gdy otrzymam połączenie z bazą danych przy użyciu klucza dzielenia na fragmenty mogę nadal danych zapytania dla pozostałych kluczy dzielenia na fragmenty na sam identyfikator niezależnego fragmentu.  To jest celowe?
Interfejsy API elastycznego skalowania umożliwiają połączenie z poprawną bazą danych klucza dzielenia na fragmenty, ale nie dostarcza klucza filtrowania dzielenia na fragmenty.  Dodaj **gdzie** klauzul do zapytania, aby ograniczyć zakres do klucza dostarczonego dzielenia na fragmenty, jeśli to konieczne.

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>Czy można użyć innej wersji bazy danych platformy Azure dla każdego niezależnego fragmentu w zestawu niezależnego fragmentu?
Tak, niezależnych jest jedna baza danych, a w związku z tym jednego niezależnego fragmentu może być wersji Premium innego być Standard edition. Ponadto wersji niezależnych można skalować w górę lub w dół wiele razy w ciągu okresu istnienia niezależnego fragmentu.

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Czy udostępnić narzędzia do scalania podziału (lub usunąć) bazy danych podczas operacji dzielenie i scalanie?
Nie. Aby uzyskać **podzielić** operacje, musi istnieć z odpowiedniego schematu docelowej bazy danych i być zarejestrowane przy użyciu Menedżera Map niezależnego fragmentu.  Aby uzyskać **scalania** operacje, musisz usunąć niezależnego fragmentu z Menedżera mapy niezależnego fragmentu, a następnie usunąć bazę danych.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

