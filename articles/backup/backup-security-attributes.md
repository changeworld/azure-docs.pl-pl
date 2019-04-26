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
ms.openlocfilehash: cf012a452d1e43134d955e03ccf34e153b1282ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60253824"
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
| Szyfrowanie na poziomie kolumny (Azure Data Services)| Nie |  |
| Wywołania interfejsu API szyfrowane| Yes |  |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Nie |  |
| Obsługa iniekcji sieci wirtualnej| Nie |  |
| Izolacja sieci i zapory pomocy technicznej| Yes | Wymuszanie tunelowania jest obsługiwana dla kopii zapasowych maszyn wirtualnych. Wymuszone tunelowanie nie jest obsługiwana w przypadku obciążeń uruchomionych maszyn wirtualnych. |
| Obsługa tunelowania wymuszonego| Nie |  |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Usługa log Analytics jest świadczona za pośrednictwem dzienników diagnostycznych. Zobacz [monitora usługi Azure Backup chronionych obciążeń przy użyciu usługi Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) Aby uzyskać więcej informacji. |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Uwierzytelnianie jest za pomocą usługi Azure Active Directory. |
| Autoryzacja| Yes | Klient utworzył i wbudowane role kontroli RBAC są używane. Zobacz [Use Role-Based kontroli dostępu do zarządzania punkty odzyskiwania usługi Azure Backup](/azure/backup/backup-rbac-rs-vault) Aby uzyskać więcej informacji. |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Yes | Dzienniki aktywności są rejestrowane wszystkie akcje klienta wyzwalane w witrynie Azure portal. |
| Rejestrowanie płaszczyzny danych i inspekcji| Nie | Płaszczyzna danych w usłudze Azure Backup nie można nawiązać połączenia z bezpośrednio.  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Yes|  |