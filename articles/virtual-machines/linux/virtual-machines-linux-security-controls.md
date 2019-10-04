---
title: Mechanizmy kontroli zabezpieczeń dla systemu Azure Linux Virtual Machines — Linux
description: Lista kontrolna zabezpieczeń na potrzeby oceny Linux Virtual Machines platformy Azure
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 00753d885985e2734f0d87fdad9f219f44277d5a
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828346"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Kontrolki zabezpieczeń dla Linux Virtual Machines

W tym artykule opisano kontrolki zabezpieczeń wbudowane w Linux Virtual Machines.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak | |
| Obsługa iniekcji sieci wirtualnej| Tak | |
| Izolacja sieci i obsługa zapór| Tak |  |
| Obsługa tunelowania wymuszonego| Tak | Zobacz [Konfigurowanie wymuszonego tunelowania przy użyciu Azure Resource Manager model wdrażania](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Zobacz [monitorowanie i aktualizowanie maszyny wirtualnej z systemem Linux na platformie Azure](/azure/virtual-machines/linux/tutorial-monitoring). |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak |  |
| Rejestrowanie i inspekcja płaszczyzny danych | Nie |  |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Uwierzytelnianie| Tak |  |
| Autoryzacja| Tak |  |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Zobacz [Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](disk-encryption-overview.md). |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | Usługa Azure Virtual Machines obsługuje szyfrowanie [ExpressRoute](/azure/expressroute) i wirtualne. Zobacz [szyfrowanie w trakcie przesyłania na maszynach wirtualnych](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak | Klucze zarządzane przez klienta to obsługiwany scenariusz szyfrowania platformy Azure; Zobacz [Omówienie usługi Azure Encryption](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Brak | |
| Wywołania interfejsu API są szyfrowane| Tak | Za pośrednictwem protokołów HTTPS i SSL. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak |  | 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../../security/fundamentals/security-controls.md).
