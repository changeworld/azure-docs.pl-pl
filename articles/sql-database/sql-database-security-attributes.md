---
title: Atrybuty zabezpieczeń usługi Azure SQL Database
description: Lista kontrolna atrybutów zabezpieczeń do oceny usługi Azure SQL Database
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: df1ffa07c9b813ee3da4952bbcc394f43c69b7ac
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204237"
---
# <a name="security-attributes-for-azure-sql-database"></a>Atrybuty zabezpieczeń usługi Azure SQL Database

W tym artykule opisano typowe atrybuty zabezpieczeń wbudowane w usłudze Azure SQL Database.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Usługa Azure SQL Database obejmuje zarówno [pojedynczej bazy danych](sql-database-single-index.yml) i [wystąpienia zarządzanego](sql-database-managed-instance.md). Pozycje poniżej mają zastosowanie do obu ofert, z wyjątkiem w przypadku, gdy zaznaczono inaczej.

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Yes | Określane jako "szyfrowania używanego", zgodnie z opisem w artykule [Always Encrypted](sql-database-always-encrypted.md). Szyfrowanie po stronie usługi używa [technologii transparent data encryption](transparent-data-encryption-azure-sql.md) (TDE).|
| Szyfrowanie podczas przesyłania:<ul><li>Szyfrowanie usługi ExpressRoute</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Yes | Przy użyciu protokołu HTTPS. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Yes | Oferowana obsługa klucza zarządzanego przez usługę i zarządzane przez klienta (ostatnie za pośrednictwem [usługi Azure Key Vault](../key-vault/index.yml). |
| Szyfrowanie na poziomie kolumny (usługi danych platformy Azure)| Yes | Za pomocą [Always Encrypted](sql-database-always-encrypted.md). |
| Wywołania interfejsu API szyfrowane| Yes | Przy użyciu protokołu HTTPS/SSL. |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Punkt końcowy usługi pomocy technicznej| Yes | Dotyczy [pojedynczej bazy danych](sql-database-single-index.yml) tylko. |
| Obsługa iniekcji sieci wirtualnej| Yes | Dotyczy [wystąpienia zarządzanego](sql-database-managed-instance.md) tylko. |
| Izolacja sieci / Zapora pomocy technicznej| Yes | Zapora w obu bazy danych i serwera poziomie; Izolacja sieci [wystąpienia zarządzanego](sql-database-managed-instance.md) tylko |
| Obsługa wymuszonego tunelowania | Yes | [wystąpienie zarządzane](sql-database-managed-instance.md) za pośrednictwem [usługi Azure ExpressRoute](../expressroute/index.yml) sieci VPN |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Rozwiązanie SIEM innej firmy od firmy Imperva (SecureSphere) jest również obsługiwana, za pomocą [usługi Azure Event Hubs](../event-hubs/index.yml) integrację za pośrednictwem [inspekcji SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzanie dostępem — uwierzytelnianie| Yes | Azure Active Directory. |
| Zarządzanie dostępem - autoryzacji| Yes |  |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzania kontrolą/Plan rejestrowania i inspekcji| Yes | Tak, aby tylko niektóre zdarzenia. |
| Rejestrowanie i inspekcja na płaszczyźnie danych | Yes | Za pomocą [inspekcji SQL](sql-database-auditing.md). |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Nie  | | 

## <a name="additional-security-attributes-for-sql-database"></a>Atrybuty dodatkowe zabezpieczenia dla bazy danych SQL

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zapobiegawczych: Ocena luk w zabezpieczeniach | Yes | Zobacz [oceny luk w zabezpieczeniach SQL service pomaga zidentyfikować luki w zabezpieczeniach bazy danych](sql-vulnerability-assessment.md). |
| Zapobiegawczych: danych, odnajdowanie i klasyfikację  | Yes | Zobacz [usługi Azure SQL Database i SQL Data Warehouse odnajdywanie i klasyfikacja danych](sql-database-data-discovery-and-classification.md). |
| Wykrywanie: wykrywanie zagrożeń | Yes | Zobacz [Zaawansowana ochrona przed zagrożeniami dla usługi Azure SQL Database](sql-database-threat-detection-overview.md). |
