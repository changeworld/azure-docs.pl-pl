---
author: msmbaldwin
ms.service: storage
ms.topic: include
ms.date: 03/15/2019
ms.author: mbaldwin
ms.openlocfilehash: b242bda524c747b28453061c797afde02cf6f455
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007565"
---
## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Yes |  |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Yes | Obsługa standardowych mechanizmów HTTPS/TLS.  Użytkownicy mogą także szyfrować dane przed ich wysłaniem do usługi. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Yes | Zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](../articles/storage/common/storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
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