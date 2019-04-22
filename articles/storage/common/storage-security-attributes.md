---
title: Wspólne atrybuty zabezpieczeń dla usługi Azure Storage
description: Lista kontrolna typowych atrybutów zabezpieczeń do oceny usługi Azure Storage
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 33765d57f1a0e5788011b2d9d9c2f57d06713ddb
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680693"
---
# <a name="common-security-attributes-for-azure-storage"></a>Wspólne atrybuty zabezpieczeń dla usługi Azure Storage

Zabezpieczenia są zintegrowane w każdy aspekt usługi platformy Azure. W tym artykule opisano typowe atrybuty zabezpieczeń wbudowane w usłudze Azure Storage. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Yes |  |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Yes | Obsługa standardowych mechanizmów HTTPS/TLS.  Użytkownicy mogą także szyfrować dane przed ich wysłaniem do usługi. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Yes | Zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Szyfrowanie na poziomie kolumny (usługi danych platformy Azure)| ND |  |
| Wywołania interfejsu API szyfrowane| Yes |  |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Punkt końcowy usługi pomocy technicznej| Yes |  |
| Obsługa iniekcji sieci wirtualnej| ND |  |
| Izolacja sieci / Zapora pomocy technicznej| Yes | |
| Obsługa wymuszonego tunelowania | ND |  |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Metryki usługi Azure Monitor dostępny teraz dzienniki począwszy od wersji zapoznawczej |

## <a name="iam-support"></a>Obsługa zarządzania tożsamościami i Dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzanie dostępem — uwierzytelnianie| Yes | Usługa Azure Active Directory, klucz współużytkowany, token dostępu współdzielonego. |
| Zarządzanie dostępem - autoryzacji| Yes | Obsługuje autoryzacji RBAC, listy ACL modelu POSIX i tokeny sygnatur dostępu Współdzielonego |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzania kontrolą/Plan rejestrowania i inspekcji | Yes | Dziennik aktywności platformy Azure Resource Manager |
| Rejestrowanie i inspekcja na płaszczyźnie danych| Yes | Dzienniki diagnostyczne usługi i rejestrowanie usługi Azure Monitor począwszy od wersji zapoznawczej  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Yes | Obsługa wersji dostawcy zasobów za pośrednictwem interfejsów API usługi Azure Resource Manager |