---
title: Wspólne atrybuty zabezpieczeń dla usługi Azure Backup
description: Lista kontrolna typowych atrybutów zabezpieczeń do oceny usługi Azure Backup
services: backup
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 93dd5372bffb278894987cd3cc2b8322cbc5e577
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679691"
---
# <a name="common-security-attributes-for-azure-backup"></a>Wspólne atrybuty zabezpieczeń dla usługi Azure Backup

Zabezpieczenia są zintegrowane w każdy aspekt usługi platformy Azure. W tym artykule opisano typowe atrybuty zabezpieczeń wbudowanych w usługę Azure Backup. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Yes | Dzięki szyfrowaniu usługi storage dla kont magazynu. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Nie | Przy użyciu protokołu HTTPS. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Nie |  |
| Szyfrowanie na poziomie kolumny (usługi danych platformy Azure)| Nie |  |
| Wywołania interfejsu API szyfrowane| Yes |  |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Punkt końcowy usługi pomocy technicznej| Nie |  |
| Obsługa iniekcji sieci wirtualnej| Nie |  |
| Izolacja sieci / Zapora pomocy technicznej| Yes | Wymuszanie tunelowania jest obsługiwana dla kopii zapasowych maszyn wirtualnych. Wymuszone tunelowanie nie jest obsługiwana w przypadku obciążeń uruchomionych maszyn wirtualnych. |
| Obsługa wymuszonego tunelowania | Nie |  |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Usługa log Analytics jest świadczona za pośrednictwem dzienników diagnostycznych. Zobacz [monitora usługi Azure Backup chronionych obciążeń przy użyciu usługi Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) Aby uzyskać więcej informacji. |

## <a name="iam-support"></a>Obsługa zarządzania tożsamościami i Dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzanie dostępem — uwierzytelnianie| Yes | Uwierzytelnianie jest za pomocą usługi Azure Active Directory. |
| Zarządzanie dostępem - autoryzacji| Yes | Klient utworzył i wbudowane role kontroli RBAC są używane. Zobacz [Use Role-Based kontroli dostępu do zarządzania punkty odzyskiwania usługi Azure Backup](/azure/backup/backup-rbac-rs-vault) Aby uzyskać więcej informacji. |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzania kontrolą/Plan rejestrowania i inspekcji| Yes | Dzienniki aktywności są rejestrowane wszystkie akcje klienta wyzwalane w witrynie Azure portal. |
| Rejestrowanie i inspekcja na płaszczyźnie danych| Nie | Płaszczyzna danych w usłudze Azure Backup nie można nawiązać połączenia z bezpośrednio.  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Yes|  |