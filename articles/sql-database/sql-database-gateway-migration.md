---
title: Powiadomienie o migracji bramy dla Azure SQL Database z Gen2 do Gen3 | Microsoft Docs
description: Artykuł zawiera powiadomienie dla użytkowników o migracji adresów IP bram Azure SQL Database Gateway
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 85691464684ff327c01a85bf357514f447564dd7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568111"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database migrację ruchu do nowszych bram

Ze względu na to, że infrastruktura platformy Azure jest ulepszona, firma Microsoft okresowo odświeża sprzęt, aby zapewnić najlepszą obsługę klienta. W najbliższych miesiącach planuje Dodawanie bram opartych na nowszych generacjach sprzętu i likwidowanie bram opartych na starszym sprzęcie w niektórych regionach.  

Klienci będą powiadamiani za pośrednictwem poczty e-mail i w Azure Portal również z wyprzedzeniem o wszelkich zmianach bram dostępnych w poszczególnych regionach. Najbardziej aktualne informacje będą przechowywane w tabeli [adresy IP bramy Azure SQL Database](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) .

## <a name="impact-of-this-change"></a>Wpływ tej zmiany

Pierwsze zaokrąglenie wycofania bramy zostało zaplanowane 1 września 2019 w następujących regionach:

- Zachodnie stany USA
- Europa Zachodnia
- East US
- Środkowe stany USA
- Azja Południowo-Wschodnia
- Środkowo-południowe stany USA
- Europa Północna
- Środkowo-północne stany USA
- Japonia Zachodnia
- Japonia Wschodnia
- Wschodnie stany USA 2
- Azja Wschodnia

Zlikwidowany adres IP przestanie akceptować ruch, a wszystkie nowe próby połączenia będą kierowane do jednej z bram w regionie.

Gdzie nie zobaczysz wpływu tej zmiany:

- Klienci korzystający z przekierowania w ramach zasad połączenia nie zobaczą żadnego wpływu.
- Nie ma to wpływu na połączenia do SQL Database z platformy Azure i używanie tagów usługi.
- Połączenia utworzone przy użyciu obsługiwanych wersji sterownika JDBC dla SQL Server nie będą miały wpływu. Aby uzyskać obsługiwane wersje JDBC, zobacz artykuł [pobieranie sterownika programu Microsoft JDBC dla SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Co zrobić, jeśli chcesz to zrobić

Zalecamy Zezwalanie na ruch wychodzący do adresów IP dla wszystkich [adresów IP bramy Azure SQL Database](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) w regionie na porcie TCP 1433 i zakres portów 11000-11999 na urządzeniu zapory. Aby uzyskać więcej informacji na temat zakresów portów, zobacz [zasady połączeń](sql-database-connectivity-architecture.md#connection-policy).

Połączenia wykonane z aplikacji przy użyciu sterownika programu Microsoft JDBC w wersji 4,0 mogą kończyć się niepowodzeniem weryfikacji certyfikatu. Niższe wersje programu Microsoft JDBC korzystają z nazwy pospolitej (CN) w polu podmiotu certyfikatu. Środki zaradcze polegają na zapewnieniu, że właściwość hostNameInCertificate jest ustawiona na *. database.windows.net. Aby uzyskać więcej informacji na temat sposobu ustawiania właściwości hostNameInCertificate, zobacz [nawiązywanie połączenia przy użyciu szyfrowania SSL](/sql/connect/jdbc/connecting-with-ssl-encryption).

Jeśli powyższe środki zaradcze nie działają, należy wysłać żądanie pomocy technicznej dotyczące SQL Database przy użyciu następującego adresu URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [architekturze łączności usługi Azure SQL](sql-database-connectivity-architecture.md)