---
title: Mechanizmy zabezpieczeń
description: Dowiedz się więcej o kontrolach zabezpieczeń używanych w usłudze Azure Backup. Te formanty pomagają usłudze zapobiegać lukom w zabezpieczeniach, wykrywać je i reagować na nie.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 0e3f5ce942ea8aef9bf5eb98883ae1e72a7ab239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172136"
---
# <a name="security-controls-for-azure-backup"></a>Środki kontroli zabezpieczeń dla usługi Azure Backup

Ten artykuł dokumentuje mechanizmy zabezpieczeń wbudowane w usługę Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Sieć)

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi | Dokumentacja
|---|---|--|
| Obsługa punktu końcowego usługi| Nie |  |  |
| Obsługa wtrysku sieci wirtualnej| Nie |  |  |
| Obsługa izolacji sieci i zaporowania| Tak | Wymuszone tunelowanie jest obsługiwane dla kopii zapasowej maszyny Wirtualnej. Wymuszone tunelowanie nie jest obsługiwane dla obciążeń działających wewnątrz maszyn wirtualnych. |  |
| Wymuszone wsparcie tunelowania| Nie |  |  |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi| | Dokumentacja
|---|---|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Tak | Usługa Log Analytics jest obsługiwana za pośrednictwem dzienników zasobów. Aby uzyskać więcej informacji, zobacz [Monitorowanie obciążeń chronionych przez usługę Azure Backup przy użyciu usługi Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Rejestrowanie i audyt płaszczyzny kontroli i zarządzania| Tak | Wszystkie akcje wyzwalane przez klienta z witryny Azure Portal są rejestrowane w dziennikach aktywności. |  |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie | Nie można uzyskać bezpośredniego dotarciu do płaszczyzny danych usługi Azure Backup.  |  |

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi| | Dokumentacja
|---|---|--|
| Uwierzytelnianie| Tak | Uwierzytelnianie odbywa się za pośrednictwem usługi Azure Active Directory. |  |
| Autoryzacja| Tak | Używane są utworzone i wbudowane role RBAC klienta. Aby uzyskać więcej informacji, zobacz [Zarządzanie punktami odzyskiwania kopii zapasowej usługi Azure za pomocą kontroli dostępu opartej na rolach.](/azure/backup/backup-rbac-rs-vault) |  |

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi | | Dokumentacja
|---|---|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Korzystanie z szyfrowania usługi magazynu dla kont magazynu. |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie |  |  |
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Nie |  |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Nie | Korzystanie z protokołu HTTPS. |  |
| Szyfrowane wywołania interfejsu API| Tak |  |  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi| | Dokumentacja
|---|---|--|
| Obsługa zarządzania konfiguracją (przechowywanie wersji konfiguracji itp.)| Tak|  |  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../security/fundamentals/security-controls.md)
