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
ms.openlocfilehash: 71e87a3295a9cd73dca2f97b3fa04a5d5ff76f84
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798685"
---
# <a name="security-attributes-for-azure-sql-database"></a>Atrybuty zabezpieczeń usługi Azure SQL Database

W tym artykule opisano typowe atrybuty zabezpieczeń, które są wbudowane w usłudze Azure SQL Database.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Usługa SQL Database udostępnia zarówno [pojedynczej bazy danych](sql-database-single-index.yml) i [wystąpienia zarządzanego](sql-database-managed-instance.md). Następujące wpisy mają zastosowanie do obu ofert, z wyjątkiem sytuacji, gdy zaznaczono inaczej.

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania, takich jak po stronie klienta lub Always Encrypted</ul>| Tak | O nazwie "szyfrowania używanego,", zgodnie z opisem w artykule [Always Encrypted](sql-database-always-encrypted.md). Szyfrowanie po stronie serwera, używa [technologii transparent data encryption](transparent-data-encryption-azure-sql.md).|
| Szyfrowanie podczas przesyłania:<ul><li>Szyfrowanie usługi Azure ExpressRoute</li><li>Szyfrowanie w sieci wirtualnej</li><li>Szyfrowanie między sieciami wirtualnymi</ul>| Tak | Przy użyciu protokołu HTTPS. |
| Klucz szyfrowania w obsłudze, takie jak CMK lub funkcji BYOK| Yes | Oferowana obsługa klucza zarządzanego przez usługę i zarządzane przez klienta. Drugim jest oferowana za pośrednictwem [usługi Azure Key Vault](../key-vault/index.yml). |
| Szyfrowanie na poziomie kolumny udostępnianego przez usługi danych platformy Azure| Tak | Za pomocą [Always Encrypted](sql-database-always-encrypted.md). |
| Zaszyfrowane wywołań interfejsu API| Tak | Przy użyciu protokołu HTTPS/SSL. |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak | Dotyczy [pojedynczej bazy danych](sql-database-single-index.yml) tylko. |
| Pomoc techniczna iniekcji sieci wirtualnej platformy Azure| Tak | Dotyczy [wystąpienia zarządzanego](sql-database-managed-instance.md) tylko. |
| Izolacja sieci i obsługa zapory| Yes | Zapory, zarówno w poziomie bazy danych, jak i w poziomie serwera. Izolacja sieci jest [wystąpienia zarządzanego](sql-database-managed-instance.md) tylko. |
| Obsługa tunelowania wymuszonego| Yes | [Wystąpienie zarządzane](sql-database-managed-instance.md) za pośrednictwem [ExpressRoute](../expressroute/index.yml) sieci VPN. |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomocy technicznej, takie jak usługa Log Analytics lub usługi Application Insights do monitorowania platformy Azure| Tak | SecureSphere, rozwiązania SIEM od firmy Imperva, jest też świadczona [usługi Azure Event Hubs](../event-hubs/index.yml) integrację za pośrednictwem [inspekcji SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Azure Active Directory (Azure AD) |
| Authorization| Tak | Brak |

## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyznę kontroli i płaszczyzny zarządzania i inspekcji| Tak | Tak, aby tylko niektóre zdarzenia |
| Płaszczyzna danych rejestrowania i inspekcji | Tak | Za pomocą [inspekcji SQL](sql-database-auditing.md) |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją, takich jak przechowywanie wersji konfiguracji| Nie  | Brak |

## <a name="additional-security-attributes-for-sql-database"></a>Atrybuty dodatkowe zabezpieczenia dla bazy danych SQL

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zapobiegawczych: Ocena luk w zabezpieczeniach | Tak | Zobacz [oceny luk w zabezpieczeniach SQL service pomaga zidentyfikować luki w zabezpieczeniach bazy danych](sql-vulnerability-assessment.md). |
| Zapobiegawczych: danych, odnajdowanie i klasyfikację  | Tak | Zobacz [usługi Azure SQL Database i SQL Data Warehouse odnajdywanie i klasyfikacja danych](sql-database-data-discovery-and-classification.md). |
| Wykrywanie: wykrywanie zagrożeń | Tak | Zobacz [Zaawansowana ochrona przed zagrożeniami dla usługi Azure SQL Database](sql-database-threat-detection-overview.md). |
