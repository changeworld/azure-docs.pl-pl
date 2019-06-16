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
ms.openlocfilehash: 922273e3805004f6af068ea748c16f5675810144
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66001459"
---
# <a name="security-attributes-for-azure-storage"></a>Atrybuty zabezpieczeń dla usługi Azure Storage

W tym artykule opisano atrybuty zabezpieczeń wbudowane w usłudze Azure Storage. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Yes |  |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Yes | Obsługa standardowych mechanizmów HTTPS/TLS.  Użytkownicy mogą także szyfrować dane przed ich wysłaniem do usługi. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Tak | Zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND |  |
| Wywołania interfejsu API szyfrowane| Tak |  |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak |  |
| Obsługa iniekcji sieci wirtualnej| ND |  |
| Izolacja sieci i zapory pomocy technicznej| Tak | |
| Obsługa tunelowania wymuszonego| ND |  |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Tak | Metryki usługi Azure Monitor dostępny teraz dzienniki począwszy od wersji zapoznawczej |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Usługa Azure Active Directory, klucz współużytkowany, token dostępu współdzielonego. |
| Autoryzacja| Tak | Obsługuje autoryzacji RBAC, listy ACL modelu POSIX i tokeny sygnatur dostępu Współdzielonego |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji | Tak | Dziennik aktywności platformy Azure Resource Manager |
| Rejestrowanie płaszczyzny danych i inspekcji| Yes | Dzienniki diagnostyczne usługi i rejestrowanie usługi Azure Monitor począwszy od wersji zapoznawczej  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Tak | Obsługa wersji dostawcy zasobów za pośrednictwem interfejsów API usługi Azure Resource Manager |