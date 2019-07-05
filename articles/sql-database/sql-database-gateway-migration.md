---
title: Brama migracji ogłoszenia dla usługi Azure SQL Database z Gen2 Gen3 | Dokumentacja firmy Microsoft
description: Artykuł zawiera powiadomienia użytkowników o migracji adresów IP bramy bazy danych SQL platformy Azure
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
manager: craigg
ms.date: 07/01/2019
ms.openlocfilehash: 5894579c62c524394c7fea044b96885f7c8e8204
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538382"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migracji ruchu bazy danych SQL platformy Azure do nowszych bram

Jak zwiększa infrastruktury platformy Azure, Microsoft będzie okresowo odświeżać sprzętu, aby upewnić się, że firma Microsoft zapewnia najlepsze możliwe wrażenia użytkowników. W najbliższych miesiącach będziemy planu można dodać bramy oparte na nowszej generacji sprzętu i zlikwidować bramy oparte na starsze sprzętu w niektórych regionach.  

Klienci zostaną powiadomieni za pośrednictwem poczty e-mail i w witrynie Azure portal w odpowiednim wyprzedzeniem wszelkie zmiany bram, które są dostępne w każdym regionie. Najbardziej aktualne informacje, które będzie przechowywany w [adresów IP bramy usługi Azure SQL Database](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) tabeli.

## <a name="impact-of-this-change"></a>Wpływ tej zmiany

Pierwsze działanie likwidacji bramy zaplanowano do 1 września 2019 w następujących regionach:

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

Adres IP zlikwidować będzie akceptować ruch i próby połączenia będą kierowane do jednej z bram w regionie.

Gdy nie będziesz widzieć wpływ tej zmiany:

- Za pomocą przekierowania, zgodnie z ich zasadami połączenia nie będziesz widzieć żadnego wpływu na klientów.
- Połączenia z usługą SQL Database z wewnątrz platformy Azure i przy użyciu tagów usługi nie będzie mieć wpływ.
- Połączenia nawiązywane przy użyciu obsługiwanych wersjach sterownik JDBC dla programu SQL Server będą widzieć żadnego wpływu. Dla obsługiwanych wersji sterownika JDBC, zobacz [pobrać sterownik JDBC firmy Microsoft dla programu SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Co zrobić, możesz zrobić, jeśli w przypadku których to dotyczy

Firma Microsoft zaleca, aby zezwolić na ruch wychodzący do adresów IP dla wszystkich [adresów IP bramy usługi Azure SQL Database](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) w regionie protokołu TCP portu 1433, a port zakresu 11000-11999 w urządzeniu zapory. Aby uzyskać więcej informacji na temat zakresów portów, zobacz [zasady połączenia](sql-database-connectivity-architecture.md#connection-policy).

Połączenia nawiązywane z poziomu aplikacji przy użyciu sterownika JDBC firmy Microsoft w wersji starszej niż 4.0, może się nie powieść weryfikację certyfikatu. Niższe wersje JDBC firmy Microsoft oparte na Nazwa pospolita (CN) w polu podmiotu certyfikatu. Środki zaradcze jest zapewnienie, że właściwość hostNameInCertificate jest ustawiona na *. database.windows.net. Aby uzyskać więcej informacji na temat sposobu ustawiania właściwości hostNameInCertificate, zobacz [połączenie przy użyciu szyfrowania SSL](/sql/connect/jdbc/connecting-with-ssl-encryption).

Jeśli powyższe ograniczenia nie rozwiąże problemu, pliku żądania pomocy technicznej dla usługi SQL Database przy użyciu następującego adresu URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [architektura łączności SQL Azure](sql-database-connectivity-architecture.md)