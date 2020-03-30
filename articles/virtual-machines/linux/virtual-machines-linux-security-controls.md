---
title: Mechanizmy kontroli bezpieczeństwa dla maszyn wirtualnych systemu Azure Linux — Linux
description: Lista kontrolna zabezpieczeń do oceny maszyn wirtualnych systemu Azure Linux
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 300acaf4a9d2a11ef107e19df99452c909257d54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190571"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Mechanizmy kontroli bezpieczeństwa dla maszyn wirtualnych systemu Linux

W tym artykule dokumentuje zabezpieczenia wbudowane w maszyny wirtualne systemu Linux.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Sieć)

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak | |
| Obsługa wtrysku sieci wirtualnej| Tak | |
| Obsługa izolacji sieci i zapory sieciowej| Tak |  |
| Wymuszone wsparcie tunelowania| Tak | Zobacz [Konfigurowanie tunelowania wymuszonego przy użyciu modelu wdrażania usługi Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Tak | Zobacz [Monitorowanie i aktualizowanie maszyny wirtualnej systemu Linux na platformie Azure](/azure/virtual-machines/linux/tutorial-monitoring). |
| Rejestrowanie i audyt płaszczyzny kontroli i zarządzania| Tak |  |
| Rejestrowanie i inspekcja płaszczyzny danych | Nie |  |

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Uwierzytelnianie| Tak |  |
| Autoryzacja| Tak |  |

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Zobacz [Szyfrowanie dysków platformy Azure dla maszyn wirtualnych z systemem Linux](disk-encryption-overview.md). |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Tak | Maszyny wirtualne platformy Azure obsługuje [szyfrowanie usługi ExpressRoute](/azure/expressroute) i sieci wirtualnej. Zobacz [Szyfrowanie podczas przesyłania na maszynach wirtualnych](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak | Klucze zarządzane przez klienta to obsługiwany scenariusz szyfrowania platformy Azure; zobacz [omówienie szyfrowania platformy Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Nie dotyczy | |
| Szyfrowane wywołania interfejsu API| Tak | Za pośrednictwem protokołu HTTPS i TLS. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (przechowywanie wersji konfiguracji itp.)| Tak |  | 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../../security/fundamentals/security-controls.md)
