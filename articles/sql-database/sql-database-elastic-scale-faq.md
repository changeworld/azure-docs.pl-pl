---
title: Często zadawane pytania dotyczące skali elastycznej
description: Często zadawane pytania dotyczące skali elastycznej bazy danych SQL platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 3eedfb1e9ec59fbe12ee94a65d3702a7ef8ca95a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823635"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Narzędzia elastycznej bazy danych często zadawane pytania (CZĘSTO ZADAWANE PYTANIA)

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Jeśli mam jedną dzierżawę na fragment i nie ma klucza dzielenia na fragmenty, jak wypełnić klucz dzielenia na fragmenty dla informacji o schemacie

Obiekt informacji o schemacie jest używany tylko do dzielenia scenariuszy scalania. Jeśli aplikacja jest z natury pojedynczej dzierżawy, a następnie nie wymaga narzędzia scalanie podziału i w związku z tym nie ma potrzeby wypełniania obiektu informacji o schemacie.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Udostępniłem bazę danych i mam już Menedżera map niezależnego fragmentu, jak zarejestrować tę nową bazę danych jako niezależnego fragmentu

Zobacz [Dodawanie fragmentu do aplikacji przy użyciu biblioteki klienta elastycznej bazy danych](sql-database-elastic-scale-add-a-shard.md).

## <a name="how-much-do-elastic-database-tools-cost"></a>Ile kosztują elastyczne narzędzia baz danych

Korzystanie z biblioteki klienta elastycznej bazy danych nie wiąże się z żadnymi kosztami. Koszty są naliczane tylko dla baz danych SQL platformy Azure, które są używane dla fragmentów i Menedżera map niezależnego fragmentu, a także ról sieci web/procesu roboczego, które są inicjowane dla narzędzia scalania podziału.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Dlaczego moje poświadczenia nie działają, gdy dodam fragment z innego serwera

Nie używaj poświadczeń w postaciusername@servername"User ID= ", zamiast tego po prostu użyj "User ID = username".  Ponadto upewnij się, że login "nazwa użytkownika" ma uprawnienia do niezależnego fragmentu.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Czy muszę utworzyć Menedżera map niezależnego fragmentu i wypełniać odłamki przy każdym uruchomieniu aplikacji

Nie — utworzenie Menedżera map niezależnego fragmentu (na przykład [ShardMapManagerFactory.CreateSqlShardMapManager)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)jest operacją jednorazową.  Aplikacja powinna używać wywołania [ShardMapMapgerFactory.TryGetSqlShardMapManager()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) w czasie uruchamiania aplikacji.  Powinno być tylko jedno takie wywołanie dla domeny aplikacji.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Mam pytania dotyczące korzystania z elastycznych narzędzi baz danych, jak mogę uzyskać odpowiedź na nie

Skontaktuj się z nami na [forum bazy danych SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Gdy otrzymuję połączenie z bazą danych przy użyciu klucza dzielenia na fragmenty, nadal mogę wysyłać zapytania o dane dla innych kluczy dzielenia na fragmenty na tym samym niezależnużyku.  Czy jest to zgodnie z projektem

Interfejsy API skali elastycznej zapewniają połączenie z poprawną bazą danych dla klucza dzielenia na fragmenty, ale nie zapewniają filtrowania klucza dzielenia na fragmenty.  Dodaj **klauzule WHERE** do kwerendy, aby ograniczyć zakres do dostarczonego klucza dzielenia na fragmenty, jeśli to konieczne.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Czy mogę używać innej wersji bazy danych SQL dla każdego niezależnego fragmentu w zestawie niezależnego fragmentu

Tak, niezależnego fragmentu jest indywidualną bazą danych, a zatem jeden fragment może być wersja Premium, podczas gdy inny jest edycją standardową. Ponadto wydanie niezależnego fragmentu można skalować w górę lub w dół wiele razy w okresie istnienia niezależnego fragmentu.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Czy narzędzie Dzielenie scalania aprowizuje (lub usuwa) bazę danych podczas operacji dzielenia lub scalania

Nie. W przypadku operacji **podziału** docelowa baza danych musi istnieć z odpowiednim schematem i być zarejestrowana w Menedżerze map niezależnego fragmentu.  W przypadku operacji **scalania** należy usunąć fragment z menedżera mapy niezależnego fragmentu, a następnie usunąć bazę danych.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]