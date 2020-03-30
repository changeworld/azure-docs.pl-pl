---
title: Zabezpieczenia — usługa Azure Database dla mariadb
description: Omówienie funkcji zabezpieczeń w usłudze Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8f41fe1005e96b428337bc73b9d468962a079596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527830"
---
# <a name="security-in-azure-database-for-mariadb"></a>Zabezpieczenia w usłudze Azure Database for MariaDB

Istnieje wiele warstw zabezpieczeń, które są dostępne do ochrony danych na serwerze usługi Azure Database dla MariaDB. W tym artykule opisano te opcje zabezpieczeń.

## <a name="information-protection-and-encryption"></a>Ochrona i szyfrowanie informacji

### <a name="in-transit"></a>Tranzyt
Usługa Azure Database for MariaDB zabezpiecza dane, szyfrując dane przesyłane za pomocą zabezpieczeń warstwy transportu. Szyfrowanie (SSL/TLS) jest domyślnie wymuszane.

### <a name="at-rest"></a>W spoczynku
Usługa Azure Database for MariaDB używa modułu kryptograficznego FIPS 140-2 do szyfrowania danych w spoczynku. Dane, w tym kopie zapasowe, są szyfrowane na dysku, z wyjątkiem plików tymczasowych utworzonych podczas uruchamiania kwerend. Usługa używa 256-bitowego szyfrowania AES dołączonego do szyfrowania magazynu platformy Azure, a klucze są zarządzane przez system. Szyfrowanie magazynu jest zawsze włączone i nie można go wyłączyć.


## <a name="network-security"></a>Bezpieczeństwo sieci
Połączenia z serwerem usługi Azure Database dla mariadb są najpierw kierowane za pośrednictwem bramy regionalnej. Brama ma publicznie dostępny adres IP, podczas gdy adresy IP serwera są chronione. Aby uzyskać więcej informacji na temat bramy, odwiedź [artykuł architektury łączności](concepts-connectivity-architecture.md).  

Nowo utworzony serwer usługi Azure Database for MariaDB ma zaporę, która blokuje wszystkie połączenia zewnętrzne. Chociaż docierają do bramy, nie mogą łączyć się z serwerem. 

### <a name="ip-firewall-rules"></a>Reguły zapory IP
Reguły zapory IP udzielają dostępu do serwerów na podstawie źródłowego adresu IP każdego żądania. Aby uzyskać więcej informacji, [zobacz omówienie reguł zapory.](concepts-firewall-rules.md)

### <a name="virtual-network-firewall-rules"></a>Reguły zapory sieci wirtualnej
Punkty końcowe usługi sieci wirtualnej rozszerzają łączność sieci wirtualnej za pośrednictwem szkieletu platformy Azure. Za pomocą reguł sieci wirtualnej można włączyć usługę Azure Database dla serwera MariaDB, aby zezwolić na połączenia z wybranych podsieci w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [omówienie punktu końcowego usługi sieci wirtualnej](concepts-data-access-security-vnet.md).


## <a name="access-management"></a>Zarządzanie dostępem

Podczas tworzenia usługi Azure Database dla serwera MariaDB, należy podać poświadczenia dla użytkownika administratora. Tego administratora można użyć do utworzenia dodatkowych użytkowników MariaDB.


## <a name="threat-protection"></a>Ochrona przed zagrożeniami

Możesz zdecydować się na [zaawansowaną ochronę przed zagrożeniami,](concepts-data-access-and-security-threat-protection.md) która wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do serwerów lub ich wykorzystania.

[Rejestrowanie inspekcji](concepts-audit-logs.md) jest dostępne do śledzenia aktywności w bazach danych. 


## <a name="next-steps"></a>Następne kroki
- Włączanie reguł zapory dla [adresów IP](concepts-firewall-rules.md) lub [sieci wirtualnych](concepts-data-access-security-vnet.md)