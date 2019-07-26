---
title: Atrybuty zabezpieczeń dla Azure SQL Database
description: Lista kontrolna atrybutów zabezpieczeń do oceny Azure SQL Database
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 1318b3e433224b009b76458b12e82c9bcf94bb7a
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444393"
---
# <a name="security-attributes-for-azure-sql-database"></a>Atrybuty zabezpieczeń dla Azure SQL Database

W tym artykule opisano atrybuty zabezpieczeń, które są wbudowane w Azure SQL Database.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

SQL Database obejmuje zarówno [jedną bazę danych](sql-database-single-index.yml) , jak i [wystąpienie zarządzane](sql-database-managed-instance.md). Poniższe wpisy dotyczą obu ofert, z wyjątkiem sytuacji, w których wskazano inaczej.

## <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta</li><li>Inne funkcje szyfrowania, takie jak po stronie klienta lub Always Encrypted</ul>| Tak | Nazywane "szyfrowaniem w użyciu", zgodnie z opisem w artykule [Always Encrypted](sql-database-always-encrypted.md). Szyfrowanie po stronie serwera używa [przezroczystego szyfrowania danych](transparent-data-encryption-azure-sql.md).|
| Szyfrowanie podczas przesyłania:<ul><li>Szyfrowanie ExpressRoute platformy Azure</li><li>Szyfrowanie w sieci wirtualnej</li><li>Szyfrowanie między sieciami wirtualnymi</ul>| Tak | Przy użyciu protokołu HTTPS. |
| Obsługa kluczy szyfrowania, taka jak CMK lub BYOK| Yes | Oferowana jest obsługa klucza zarządzanego przez usługę i zarządzane przez klienta. Ten drugi jest oferowany w [Azure Key Vault](../key-vault/index.yml). |
| Szyfrowanie na poziomie kolumny udostępniane przez usługi danych platformy Azure| Tak | Za [Always Encrypted](sql-database-always-encrypted.md). |
| Zaszyfrowane wywołania interfejsu API| Tak | Przy użyciu protokołu HTTPS/SSL. |

## <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Yes | Dotyczy tylko [pojedynczej bazy danych](sql-database-single-index.yml) . |
| Obsługa iniekcji Virtual Network platformy Azure| Tak | Dotyczy tylko [wystąpienia zarządzanego](sql-database-managed-instance.md) . |
| Izolacja sieci i obsługa zapory| Yes | Zapora zarówno na poziomie bazy danych, jak i na poziomie serwera. Izolacja sieci dotyczy tylko [wystąpienia zarządzanego](sql-database-managed-instance.md) . |
| Obsługa tunelowania wymuszonego| Tak | [Wystąpienie zarządzane](sql-database-managed-instance.md) za pośrednictwem sieci VPN [ExpressRoute](../expressroute/index.yml) . |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure, taka jak Log Analytics lub Application Insights| Tak | SecureSphere rozwiązanie SIEM z Imperva jest również obsługiwane przez integrację z [usługą Azure Event Hubs](../event-hubs/index.yml) przy użyciu funkcji [inspekcji SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Azure Active Directory (Azure AD) |
| Authorization| Yes | Brak |

## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja w płaszczyźnie kontroli i zarządzania| Tak | Tak tylko w przypadku niektórych zdarzeń |
| Rejestrowanie i inspekcja płaszczyzny danych | Tak | Za pośrednictwem [inspekcji SQL](sql-database-auditing.md) |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją, taka jak wersja konfiguracji| Nie  | Brak |

## <a name="additional-security-attributes-for-sql-database"></a>Dodatkowe atrybuty zabezpieczeń dla SQL Database

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Prewencyjne: Ocena luk w zabezpieczeniach | Tak | Zapoznaj się z tematem usługa oceny luk w zabezpieczeniach w [usłudze SQL Database](sql-vulnerability-assessment.md). |
| Prewencyjne: odnajdywanie i Klasyfikacja danych  | Yes | Zobacz [Azure SQL Database i SQL Data Warehouse odnajdywania danych & klasyfikację](sql-database-data-discovery-and-classification.md). |
| Wykrywanie: wykrywanie zagrożeń | Tak | Zobacz [zaawansowaną ochronę przed zagrożeniami dla Azure SQL Database](sql-database-threat-detection-overview.md). |
