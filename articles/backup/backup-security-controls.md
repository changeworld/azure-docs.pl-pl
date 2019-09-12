---
title: Kontrolki zabezpieczeń dla Azure Backup
description: Lista kontrolna kontroli zabezpieczeń dla oceny Azure Backup
ms.reviewer: mbaldwin
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: dacurwin
ms.openlocfilehash: 6c4fbdf378b1981e28f493e5a4e232ebbc0bbda0
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886614"
---
# <a name="security-controls-for-azure-backup"></a>Kontrolki zabezpieczeń dla Azure Backup

W tym artykule opisano kontrolki zabezpieczeń wbudowane w Azure Backup. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Nie |  |
| Obsługa iniekcji sieci wirtualnej| Nie |  |
| Izolacja sieci i obsługa zapór| Tak | Wymuszone tunelowanie jest obsługiwane w przypadku kopii zapasowej maszyny wirtualnej. Wymuszone tunelowanie nie jest obsługiwane w przypadku obciążeń uruchamianych wewnątrz maszyn wirtualnych. |
| Obsługa tunelowania wymuszonego| Nie |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Log Analytics jest obsługiwana za pośrednictwem dzienników diagnostycznych. Aby uzyskać więcej informacji, zobacz [monitorowanie Azure Backup chronionych obciążeń przy użyciu log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) . |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Wszystkie akcje wyzwalane przez klienta z Azure Portal są rejestrowane w dziennikach aktywności. |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie | Nie można bezpośrednio połączyć Azure Backup płaszczyzny danych.  |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Uwierzytelnianie odbywa się za Azure Active Directory. |
| Authorization| Tak | Są używane wbudowane role RBAC klienta. Aby uzyskać więcej informacji, zobacz temat [używanie Access Control opartych na rolach w celu zarządzania Azure Backup punktów odzyskiwania](/azure/backup/backup-rbac-rs-vault) . |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: Klucze zarządzane przez firmę Microsoft | Tak | Korzystanie z szyfrowania usługi Storage dla kont magazynu. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie |  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Nie |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Nie | Przy użyciu protokołu HTTPS. |
| Wywołania interfejsu API są szyfrowane| Tak |  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak|  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).