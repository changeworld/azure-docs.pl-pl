---
title: Funkcja elastyczne skalowanie SQL platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi Azure SQL Database funkcja elastyczne skalowanie.
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
ms.openlocfilehash: b5ba5fadd229fa7119f9af791f7eaedbc984c92a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60584951"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Narzędzia elastycznych baz danych — często zadawane pytania (FAQ)

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Jak wypełnić klucz fragmentowania tak, aby uzyskać informacje o schemacie jeśli korzystam z jedną dzierżawą za fragmentów, a nie klucza fragmentowania,

Obiekt informacji schematu tylko jest używany do dzielenia scenariuszy scalania. Jeśli aplikacja jest natury pojedynczej dzierżawy, nie wymaga Split-Merge tool i związku z tym nie ma potrzeby do wypełnienia obiektu informacji o schemacie.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Po aprowizowaniu bazy danych i Menedżera mapowań fragmentów mają już, jak zarejestrować z tej nowej bazy danych jako fragmentów

Zobacz [dodanie fragmentu do aplikacji za pomocą biblioteki klienckiej elastic database](sql-database-elastic-scale-add-a-shard.md).

## <a name="how-much-do-elastic-database-tools-cost"></a>Ile koszt narzędzi elastycznej bazy danych

Za pomocą biblioteki klienckiej elastycznej bazy danych nie spowoduje naliczenia żadnych kosztów. Koszty są naliczane tylko w przypadku baz danych Azure SQL, używanych do fragmentów i Menedżera mapowań fragmentów, jak również ról sieć web/proces roboczy, do których aprowizowany dla Split-Merge tool.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Dlaczego są moje poświadczenia nie działa po dodaniu fragmentu z innego serwera

Nie używaj poświadczeń w formie "Nazwa użytkownika =username@servername", zamiast tego po prostu użyć "identyfikator użytkownika = nazwa użytkownika".  Upewnij się również, czy nazwa logowania "username" ma uprawnienia na fragmentu.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Należy utworzyć Menedżera mapowań fragmentów i wypełnić fragmentów, za każdym razem, gdy mogę uruchomić Moje aplikacje

Nie — tworzenie Menedżera mapowań fragmentów (na przykład [ShardMapManagerFactory.CreateSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) jest to jednorazowa operacja.  Aplikacja powinna używać wywołania [ShardMapManagerFactory.TryGetSqlShardMapManager()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) w momencie uruchamiania aplikacji.  Ma tylko jedno takie wywołanie dla domeny aplikacji.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Mam pytania dotyczące korzystania z narzędzi elastycznych baz danych, jak je odpowiedzi uzyskać

Skontaktuj się z nami na [forum bazy danych SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Gdy pojawia się połączenie z bazą danych przy użyciu klucza fragmentowania, nadal można zapytania o dane dla innych kluczy fragmentowania na tym samym fragmencie.  Jest to celowe

Interfejsy API elastycznego skalowania umożliwiają połączenie z odpowiednią bazą danych dla swojego klucza fragmentowania, ale nie zapewniają filtrowanie klucza fragmentowania.  Dodaj **gdzie** klauzule zapytania w taki sposób, aby ograniczyć zakres klucz fragmentowania podany, jeśli to konieczne.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Czy można użyć innej wersji bazy danych SQL dla każdego fragmentu w Mój zestaw fragmentów

Tak, fragment jest poszczególnych baz danych, a zatem jednego fragmentu może być w wersji Premium, choć innej wersji standardowej. Ponadto wersja fragmentu skalować w górę lub w dół wiele razy w okresie istnienia fragmentu.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Jest zapewnienie narzędzie do dzielenia scalania (lub usuń) bazę danych podczas operacji dzielenia ani scalania

Nie. Aby uzyskać **podziału** operacje, musi istnieć przy użyciu odpowiedniego schematu docelowej bazy danych i być zarejestrowane przy użyciu Menedżera mapowań fragmentów.  Aby uzyskać **scalania** operacji, należy usunąć fragment z Menedżera map fragmentów, a następnie usunąć bazy danych.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]