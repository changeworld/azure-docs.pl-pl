---
title: Atrybuty zabezpieczeń usługi Azure Storage
description: Lista kontrolna atrybutów zabezpieczeń do oceny usługi Azure Storage
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 16ec2757955b53a8bfa73ba724100f7fa61d2867
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444296"
---
# <a name="security-attributes-for-azure-storage"></a>Atrybuty zabezpieczeń usługi Azure Storage

W tym artykule opisano atrybuty zabezpieczeń wbudowane w usługę Azure Storage. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania)| Tak |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | Obsługa standardowych mechanizmów protokołu HTTPS/TLS.  Użytkownicy mogą także szyfrować dane przed ich przesłaniem do usługi. |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Tak | Zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w programie Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND |  |
| Wywołania interfejsu API są szyfrowane| Tak |  |

## <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak |  |
| Obsługa iniekcji sieci wirtualnej| ND |  |
| Izolacja sieci i obsługa zapór| Tak | |
| Obsługa tunelowania wymuszonego| ND |  |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Dostępne są teraz metryki Azure Monitor, dzienniki rozpoczynające Podgląd |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Azure Active Directory, klucz współużytkowany, token dostępu współdzielonego. |
| Authorization| Yes | Obsługa autoryzacji za pośrednictwem list kontroli dostępu RBAC, POSIX i tokenów SAS |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania | Yes | Azure Resource Manager dziennik aktywności |
| Rejestrowanie i inspekcja płaszczyzny danych| Yes | Dzienniki diagnostyczne usługi i rejestrowanie Azure Monitor rozpoczynające się w wersji zapoznawczej  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | Obsługa wersji dostawcy zasobów przy użyciu interfejsów API Azure Resource Manager |