---
title: Mechanizmy zabezpieczeń
description: Lista kontrolna kontroli zabezpieczeń dla oceny Azure SQL Database
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: ce7f3eafa57cbd993be98f4a2da3d89cb312f9b7
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190688"
---
# <a name="security-controls-for-azure-sql-database"></a>Kontrolki zabezpieczeń dla Azure SQL Database

W tym artykule opisano mechanizmy kontroli zabezpieczeń, które są wbudowane w Azure SQL Database.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL Database obejmuje zarówno [jedną bazę danych](sql-database-single-index.yml) , jak i [wystąpienie zarządzane](sql-database-managed-instance.md). Poniższe wpisy dotyczą obu ofert, z wyjątkiem sytuacji, w których wskazano inaczej.

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Yes | Dotyczy tylko [pojedynczej bazy danych](sql-database-single-index.yml) . |
| Obsługa iniekcji Virtual Network platformy Azure| Yes | Dotyczy tylko [wystąpienia zarządzanego](sql-database-managed-instance.md) . |
| Izolacja sieci i obsługa zapory| Yes | Zapora zarówno na poziomie bazy danych, jak i na poziomie serwera. Izolacja sieci dotyczy tylko [wystąpienia zarządzanego](sql-database-managed-instance.md) . |
| Obsługa tunelowania wymuszonego| Yes | [Wystąpienie zarządzane](sql-database-managed-instance.md) za pośrednictwem sieci VPN [ExpressRoute](../expressroute/index.yml) . |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure, taka jak Log Analytics lub Application Insights| Yes | SecureSphere rozwiązanie SIEM z Imperva jest również obsługiwane przez integrację z [usługą Azure Event Hubs](../event-hubs/index.yml) przy użyciu funkcji [inspekcji SQL](sql-database-auditing.md). |
| Rejestrowanie i inspekcja w płaszczyźnie kontroli i zarządzania| Yes | Tak tylko w przypadku niektórych zdarzeń |
| Rejestrowanie i inspekcja płaszczyzny danych | Yes | Za pośrednictwem [inspekcji SQL](sql-database-auditing.md) |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Uwierzytelnianie| Yes | Azure Active Directory (Azure AD) |
| Autoryzacja| Yes | None |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Yes | Nazywane "szyfrowaniem w użyciu", zgodnie z opisem w artykule [Always Encrypted](sql-database-always-encrypted.md). Szyfrowanie po stronie serwera używa [przezroczystego szyfrowania danych](transparent-data-encryption-azure-sql.md).|
| Szyfrowanie podczas przesyłania:<ul><li>Szyfrowanie ExpressRoute platformy Azure</li><li>Szyfrowanie w sieci wirtualnej</li><li>Szyfrowanie między sieciami wirtualnymi</ul>| Yes | Przy użyciu protokołu HTTPS. |
| Obsługa kluczy szyfrowania, taka jak CMK lub BYOK| Yes | Oferowana jest obsługa klucza zarządzanego przez usługę i zarządzane przez klienta. Ten drugi jest oferowany w [Azure Key Vault](../key-vault/index.yml). |
| Szyfrowanie na poziomie kolumny udostępniane przez usługi danych platformy Azure| Yes | Za [Always Encrypted](sql-database-always-encrypted.md). |
| Zaszyfrowane wywołania interfejsu API| Yes | Przy użyciu protokołu HTTPS/TLS. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją, taka jak wersja konfiguracji| Nie  | None |

## <a name="additional-security-controls-for-sql-database"></a>Dodatkowe opcje zabezpieczeń dla SQL Database

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Prewencyjne: Ocena luk w zabezpieczeniach | Yes | Zapoznaj się z tematem [Usługa oceny luk w zabezpieczeniach w usłudze SQL Database](sql-vulnerability-assessment.md). |
| Prewencyjne: odnajdywanie i Klasyfikacja danych  | Yes | Zobacz [Azure SQL Database i SQL Data Warehouse odnajdywania danych & klasyfikację](sql-database-data-discovery-and-classification.md). |
| Wykrywanie: wykrywanie zagrożeń | Yes | Zobacz [zaawansowaną ochronę przed zagrożeniami dla Azure SQL Database](sql-database-threat-detection-overview.md). |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).
