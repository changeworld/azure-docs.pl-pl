---
title: Powiadomienie o migracji ruchu bramy
description: Artykuł zawiera informacje dla użytkowników o migracji adresów IP bram bazy danych SQL azure
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: fe35dc4c22f3852934cde0d6f33084b56266d514
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73807705"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migracja ruchu usługi Azure SQL Database do nowszych bram

Wraz z ulepszaniem infrastruktury platformy Azure firma Microsoft będzie okresowo odświeżać sprzęt, aby zapewnić najlepszą możliwą obsługę klienta. W nadchodzących miesiącach planujemy dodać bramy oparte na nowszych generacjach sprzętu, przenieść do nich ruch, a ostatecznie zlikwidować bramy zbudowane na starszym sprzęcie w niektórych regionach.  

Klienci będą powiadamiani za pośrednictwem poczty e-mail i w witrynie Azure portal z dużym wyprzedzeniem o wszelkich zmianach bram dostępnych w każdym regionie. Najbardziej aktualne informacje będą przechowywane w tabeli [adresów IP bramy usługi Azure SQL Database.](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)

## <a name="impact-of-this-change"></a>Wpływ tej zmiany

Pierwsza runda migracji ruchu do nowszych bram jest zaplanowana na **14 października 2019** r. w następujących regionach:
- Brazylia Południowa
- Zachodnie stany USA
- Europa Zachodnia
- Wschodnie stany USA
- Środkowe stany USA
- Azja Południowo-Wschodnia
- Południowo-środkowe stany USA
- Europa Północna
- Północno-środkowe stany USA
- Japonia Zachodnia
- Japonia Wschodnia
- Wschodnie stany USA 2
- Azja Wschodnia

Migracja ruchu spowoduje zmianę publicznego adresu IP rozpoznawanego przez system DNS dla bazy danych SQL.
Będzie to miało wpływ, jeśli masz:
- Zakodowany na dysku adres IP dla określonej bramy w zaporze lokalnej
- Wszystkie podsieci używające programu Microsoft.SQL jako punktu końcowego usługi, ale nie mogą komunikować się z adresami IP bramy

Nie będzie to miało wpływu, jeśli masz:
- Przekierowanie jako zasady połączenia
- Połączenia z bazą danych SQL z wewnętrznej platformy Azure i przy użyciu tagów usług
- Połączenia nawiązywania przy użyciu obsługiwanych wersji sterownika JDBC dla programu SQL Server nie będą miały wpływu. Aby zapoznać się z obsługiwanymi wersjami JDBC, zobacz [Pobieranie sterownika JDBC firmy Microsoft dla programu SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Co zrobić, jeśli dotyczy cię problem

Zaleca się zezwolenie ruchu wychodzącego na adresy IP dla wszystkich [adresów IP bramy bazy danych SQL azure](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) w regionie na porcie TCP 1433 i zakresie portów 11000-11999. To zalecenie ma zastosowanie do klientów łączących się z lokalnego, a także tych, którzy łączą się za pośrednictwem punktów końcowych usługi. Aby uzyskać więcej informacji na temat zakresów portów, zobacz [Zasady połączeń](sql-database-connectivity-architecture.md#connection-policy).

Połączenia nawiązywane z aplikacji przy użyciu sterownika JDBC firmy Microsoft poniżej wersji 4.0 mogą zakończyć się niepowodzeniem sprawdzania poprawności certyfikatu. Niższe wersje microsoft JDBC opierają się na nazwie pospolitej (CN) w polu Temat certyfikatu. Ograniczenie jest zapewnienie, że właściwość hostNameInCertificate jest ustawiona na *.database.windows.net. Aby uzyskać więcej informacji na temat ustawiania właściwości hostNameInCertificate, zobacz [Łączenie się z szyfrowaniem SSL](/sql/connect/jdbc/connecting-with-ssl-encryption).

Jeśli powyższe środki zaradcze nie zadziałają, złóż żądanie pomocy technicznej dla bazy danych SQL przy użyciu następującego adresu URL:https://aka.ms/getazuresupport

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [architekturze łączności SQL platformy Azure](sql-database-connectivity-architecture.md)
