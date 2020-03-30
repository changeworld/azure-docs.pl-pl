---
title: Mechanizmy zabezpieczeń
description: Lista kontrolna zabezpieczeń do oceny bazy danych SQL usługi Azure
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: ce7f3eafa57cbd993be98f4a2da3d89cb312f9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190688"
---
# <a name="security-controls-for-azure-sql-database"></a>Kontrolki zabezpieczeń dla bazy danych SQL usługi Azure

Ten artykuł dokumentuje kontrolki zabezpieczeń wbudowane w usługę Azure SQL Database.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

Baza danych SQL zawiera zarówno [pojedynczą bazę danych,](sql-database-single-index.yml) jak i [wystąpienie zarządzane](sql-database-managed-instance.md). Poniższe wpisy mają zastosowanie do obu ofert, chyba że zaznaczono inaczej.

## <a name="network"></a>Network (Sieć)

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak | Dotyczy tylko [pojedynczej bazy danych.](sql-database-single-index.yml) |
| Obsługa iniekcji sieci wirtualnej platformy Azure| Tak | Dotyczy tylko [wystąpienia zarządzanego.](sql-database-managed-instance.md) |
| Izolacja sieci i obsługa zapory| Tak | Zapora na poziomie bazy danych i serwera. Izolacja sieci jest tylko dla [wystąpienia zarządzanego.](sql-database-managed-instance.md) |
| Wymuszone wsparcie tunelowania| Tak | [Wystąpienie zarządzane](sql-database-managed-instance.md) za pośrednictwem sieci VPN [usługi ExpressRoute.](../expressroute/index.yml) |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa monitorowania platformy Azure, taka jak usługa Log Analytics lub usługa Application Insights| Tak | SecureSphere, rozwiązanie SIEM firmy Imperva, jest również obsługiwane przez integrację [usługi Azure Event Hubs](../event-hubs/index.yml) za pośrednictwem [inspekcji SQL.](sql-database-auditing.md) |
| Rejestrowanie i audyt płaszczyzny sterowania i zarządzania| Tak | Tak tylko w przypadku niektórych zdarzeń |
| Rejestrowanie i audyt płaszczyzny danych | Tak | Za pomocą [audytu SQL](sql-database-auditing.md) |

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Uwierzytelnianie| Tak | Azure Active Directory (Azure AD) |
| Autoryzacja| Tak | Brak |

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft | Tak | O nazwie "szyfrowanie w użyciu", jak opisano w artykule [Zawsze zaszyfrowane](sql-database-always-encrypted.md). Szyfrowanie po stronie serwera wykorzystuje [przezroczyste szyfrowanie danych](transparent-data-encryption-azure-sql.md).|
| Szyfrowanie podczas przesyłania:<ul><li>Szyfrowanie usługi Azure ExpressRoute</li><li>Szyfrowanie w sieci wirtualnej</li><li>Szyfrowanie między sieciami wirtualnymi</ul>| Tak | Korzystanie z protokołu HTTPS. |
| Obsługa kluczy szyfrowania, takich jak CMK lub BYOK| Tak | Oferowane są zarówno zarządzanie usługami, jak i obsługa kluczy zarządzane przez klienta. Ten ostatni jest oferowany za pośrednictwem [usługi Azure Key Vault](../key-vault/index.yml). |
| Szyfrowanie na poziomie kolumny dostarczane przez usługi danych platformy Azure| Tak | Za pośrednictwem [zawsze zaszyfrowane](sql-database-always-encrypted.md). |
| Szyfrowane wywołania interfejsu API| Tak | Korzystanie z protokołu HTTPS/TLS. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją, taka jak przechowywanie wersji konfiguracji| Nie  | Brak |

## <a name="additional-security-controls-for-sql-database"></a>Dodatkowe zabezpieczenia bazy danych SQL

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Profilaktyczna: ocena podatności na zagrożenia | Tak | Zobacz [Usługa oceny luk w zabezpieczeniach SQL pomaga zidentyfikować luki w zabezpieczeniach bazy danych](sql-vulnerability-assessment.md). |
| Profilaktyczne: odnajdowanie i klasyfikacja danych  | Tak | Zobacz [klasyfikacja & odnajdowania danych bazy danych SQL i usługi SQL Data Warehouse](sql-database-data-discovery-and-classification.md). |
| Wykrywanie: wykrywanie zagrożeń | Tak | Zobacz [Zaawansowana ochrona przed zagrożeniami dla usługi Azure SQL Database](sql-database-threat-detection-overview.md). |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../security/fundamentals/security-controls.md)
