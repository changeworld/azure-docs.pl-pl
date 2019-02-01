---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ac0072b10489deb1b18ea737301ab698dce9f89
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513505"
---
## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Yes | Wszystkie obiekty są szyfrowane. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Yes | Cała komunikacja jest za pomocą zaszyfrowanych wywołań interfejsu API |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Yes | Klient kontroluje wszystkie klucze w ich usługi Key Vault. Klucze modułu HSM kopii zabezpieczeń sprzętowych przypadku specifiecd FIPS poziom 2 przez sprzętowy moduł zabezpieczeń chroni klucz, certyfikatu lub klucza tajnego. |
| Szyfrowanie na poziomie kolumny (usługi danych platformy Azure)| ND |  |
| Wywołania interfejsu API szyfrowane| Yes | Przy użyciu protokołu HTTPS. |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Punkt końcowy usługi pomocy technicznej| Yes | Za pomocą punktów końcowych usługi sieci wirtualnej (Vnet). |
| Obsługa iniekcji sieci wirtualnej| Nie |  |
| Izolacja sieci / Zapora pomocy technicznej| Yes | Korzystanie z reguł zapory sieci wirtualnej. |
| Obsługa wymuszonego tunelowania | Nie |  |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługiwane (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Za pomocą usługi Log Analytics. |

## <a name="iam-support"></a>Obsługa zarządzania tożsamościami i Dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzanie dostępem — uwierzytelnianie| Yes | Uwierzytelnianie jest za pomocą usługi Azure Active Directory. |
| Zarządzanie dostępem - autoryzacji| Yes | Za pomocą zasad dostępu magazynu kluczy. |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzania kontrolą/Plan rejestrowania i inspekcji| Yes | Za pomocą usługi Log Analytics. |
| Rejestrowanie i inspekcja na płaszczyźnie danych| Yes | Za pomocą usługi Log Analytics. |

## <a name="access-controls"></a>Kontrole dostępu

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Kontroli dostępu do płaszczyzny zarządzania/kontroli | Yes | Kontrola dostępu oparta na rolach (RBAC) przy użyciu usługi Azure Resource Manager |
| Kontroli dostępu do płaszczyzny danych (na każdym poziomie usługi) | Yes | Zasady dostępu magazynu kluczy |