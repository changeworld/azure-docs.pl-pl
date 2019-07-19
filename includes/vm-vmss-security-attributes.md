---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 642f63966533416be7b86bae362d24cd286706b3
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68306119"
---
## <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | Tak | Zobacz [jak szyfrować maszynę wirtualną z systemem Linux na platformie Azure](/azure/virtual-machines/linux/encrypt-disks) i [szyfrować dyski wirtualne na maszynie wirtualnej z systemem Windows](/azure/virtual-machines/windows/encrypt-disks). |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | Usługa Azure Virtual Machines obsługuje szyfrowanie [ExpressRoute](/azure/expressroute) i wirtualne. Zobacz [szyfrowanie w trakcie przesyłania na maszynach wirtualnych](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Tak | Klucze zarządzane przez klienta to obsługiwany scenariusz szyfrowania platformy Azure; Zobacz [Omówienie usługi Azure Encryption](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Yes | Za pośrednictwem protokołów HTTPS i SSL. |

## <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak | |
| Obsługa iniekcji sieci wirtualnej| Tak | . |
| Izolacja sieci i obsługa zapór| Tak |  |
| Obsługa tunelowania wymuszonego| Yes | Zobacz [Konfigurowanie wymuszonego tunelowania przy użyciu Azure Resource Manager model wdrażania](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Zobacz [monitorowanie i aktualizowanie maszyny wirtualnej z systemem Linux na platformie Azure](/azure/virtual-machines/linux/tutorial-monitoring) oraz [monitorowanie i aktualizowanie maszyny wirtualnej z systemem Windows na platformie Azure](/azure/virtual-machines/windows/tutorial-monitoring). |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak |  |
| Authorization| Yes |  |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak |  |
| Rejestrowanie i inspekcja płaszczyzny danych | Nie |  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes |  | 
