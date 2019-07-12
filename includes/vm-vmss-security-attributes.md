---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: df11493fa9663d3fcbf0a2f74a5acbead55a25fb
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800278"
---
## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (na przykład szyfrowanie po stronie serwera, szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta i inne funkcje szyfrowania) | Tak | Zobacz [sposób szyfrowania maszyny wirtualnej z systemem Linux na platformie Azure](/azure/virtual-machines/linux/encrypt-disks.md) i [Szyfruj dyski wirtualne na maszynie Wirtualnej Windows](/azure/virtual-machines/windows/encrypt-disks.md). |
| Szyfrowanie podczas transferu (np. szyfrowanie usługi ExpressRoute w sieci wirtualnej, szyfrowania i szyfrowania sieć wirtualna-sieć wirtualna)| Tak | Platforma Azure obsługuje maszyny wirtualne [ExpressRoute](/azure/expressroute) i szyfrowania sieci Wirtualnej. Zobacz [szyfrowania na maszynach wirtualnych podczas przesyłania](/azure/security/security-azure-encryption-overview.md#in-transit-encryption-in-vms). |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Yes | Kluczy zarządzanych przez klienta jest scenariusz obsługiwanych szyfrowania platformy Azure; zobacz [usługa Azure encryption overview](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms.md).|
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND | |
| Wywołania interfejsu API szyfrowane| Tak | Przy użyciu protokołu HTTPS i SSL. |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak | |
| Obsługa iniekcji sieci wirtualnej| Tak | . |
| Izolacja sieci i Firewalling pomocy technicznej| Tak |  |
| Obsługa tunelowania wymuszonego| Tak | Zobacz [Konfigurowanie wymuszonego tunelowania przy użyciu modelu wdrażania usługi Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Zobacz [monitorowania i aktualizowania maszyny wirtualnej z systemem Linux na platformie Azure](/azure/virtual-machines/linux/tutorial-monitoring.md) i [monitorowania i aktualizowania maszyny wirtualnej Windows na platformie Azure](/azure/virtual-machines/windows/tutorial-monitoring.md). |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak |  |
| Authorization| Yes |  |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Tak |  |
| Rejestrowanie płaszczyzny danych i inspekcji | Nie |  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Tak |  | 
