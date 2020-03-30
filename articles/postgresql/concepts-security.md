---
title: Zabezpieczenia w usłudze Azure Database dla postgreSql — pojedynczy serwer
description: Omówienie funkcji zabezpieczeń w usłudze Azure Database for PostgreSQL — Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 02dc9e1ad9ee46b1a400e44b6ef737e70571a17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972584"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Zabezpieczenia w usłudze Azure Database dla postgreSql — pojedynczy serwer

Istnieje wiele warstw zabezpieczeń, które są dostępne do ochrony danych na serwerze usługi Azure Database dla postgreSql. W tym artykule opisano te opcje zabezpieczeń.

## <a name="information-protection-and-encryption"></a>Ochrona i szyfrowanie informacji

### <a name="in-transit"></a>Tranzyt
Usługa Azure Database for PostgreSQL zabezpiecza dane, szyfrując dane przesyłane za pomocą zabezpieczeń warstwy transportu. Szyfrowanie (SSL/TLS) jest domyślnie wymuszane.

### <a name="at-rest"></a>W spoczynku
Usługa Azure Database for PostgreSQL używa modułu kryptograficznego fips 140-2 do szyfrowania danych w spoczynku. Dane, w tym kopie zapasowe, są szyfrowane na dysku, z wyjątkiem plików tymczasowych utworzonych podczas uruchamiania kwerend. Usługa używa 256-bitowego szyfrowania AES dołączonego do szyfrowania magazynu platformy Azure, a klucze są zarządzane przez system. Szyfrowanie magazynu jest zawsze włączone i nie można go wyłączyć.


## <a name="network-security"></a>Bezpieczeństwo sieci
Połączenia z usługą Azure Database dla serwera PostgreSQL są najpierw kierowane za pośrednictwem bramy regionalnej. Brama ma publicznie dostępny adres IP, podczas gdy adresy IP serwera są chronione. Aby uzyskać więcej informacji na temat bramy, odwiedź [artykuł architektury łączności](concepts-connectivity-architecture.md).  

Nowo utworzony serwer usługi Azure Database for PostgreSQL ma zaporę, która blokuje wszystkie połączenia zewnętrzne. Chociaż docierają do bramy, nie mogą łączyć się z serwerem. 

### <a name="ip-firewall-rules"></a>Reguły zapory IP
Reguły zapory IP udzielają dostępu do serwerów na podstawie źródłowego adresu IP każdego żądania. Aby uzyskać więcej informacji, [zobacz omówienie reguł zapory.](concepts-firewall-rules.md)

### <a name="virtual-network-firewall-rules"></a>Reguły zapory sieci wirtualnej
Punkty końcowe usługi sieci wirtualnej rozszerzają łączność sieci wirtualnej za pośrednictwem szkieletu platformy Azure. Za pomocą reguł sieci wirtualnej można włączyć usługę Azure Database dla serwera PostgreSQL, aby zezwolić na połączenia z wybranych podsieci w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [omówienie punktu końcowego usługi sieci wirtualnej](concepts-data-access-and-security-vnet.md).

### <a name="private-ip"></a>Prywatny adres IP
Private Link umożliwia łączenie się z usługą Azure Database dla pojedynczego serwera PostgreSQL na platformie Azure za pośrednictwem prywatnego punktu końcowego. Usługa Azure Private Link zasadniczo przynosi usługi platformy Azure wewnątrz prywatnej sieci wirtualnej (VNet). Zasoby PaaS można uzyskać przy użyciu prywatnego adresu IP, podobnie jak każdy inny zasób w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [omówienie łącza prywatnego](concepts-data-access-and-security-private-link.md)


## <a name="access-management"></a>Zarządzanie dostępem

Podczas tworzenia usługi Azure Database dla serwera PostgreSQL, należy podać poświadczenia dla roli administratora. Ta rola administratora może służyć do tworzenia dodatkowych [ról PostgreSQL](https://www.postgresql.org/docs/current/user-manag.html).

Można również połączyć się z serwerem przy użyciu [uwierzytelniania usługi Azure Active Directory (AAD).](concepts-aad-authentication.md)


## <a name="threat-protection"></a>Ochrona przed zagrożeniami

Możesz zdecydować się na [zaawansowaną ochronę przed zagrożeniami,](concepts-data-access-and-security-threat-protection.md) która wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do serwerów lub ich wykorzystania.

[Rejestrowanie inspekcji](concepts-audit.md) jest dostępne do śledzenia aktywności w bazach danych. 


## <a name="next-steps"></a>Następne kroki
- Włączanie reguł zapory dla [adresów IP](concepts-firewall-rules.md) lub [sieci wirtualnych](concepts-data-access-and-security-vnet.md)
- Dowiedz się więcej o [uwierzytelnianiu usługi Azure Active Directory](concepts-aad-authentication.md) w bazie danych Platformy Azure dla postgreSQL
