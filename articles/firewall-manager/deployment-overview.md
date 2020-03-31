---
title: Omówienie wdrażania usługi Azure Firewall Manager Preview
description: Zapoznaj się z krokami wdrażania wysokiego poziomu wymaganymi dla usługi Azure Firewall Manager Preview
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: c3a94cea838609f65511a21ee2f64e8782a6adea
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77443129"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Omówienie wdrażania usługi Azure Firewall Manager Preview

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Istnieje więcej niż jeden sposób wdrażania usługi Azure Firewall Manager Preview, ale zaleca się następujący proces ogólny.

## <a name="general-deployment-process"></a>Ogólny proces wdrażania

### <a name="hub-virtual-networks"></a>Sieci wirtualne koncentratora

1.  Tworzenie zasad zapory

    - Tworzenie nowych zasad
<br>*Lub*<br>
    - Wywoż podstawowe zasady i dostosuj zasady lokalne
<br>*Lub*<br>
    - Importuj reguły z istniejącej Zapory platformy Azure. Pamiętaj, aby usunąć reguły NAT z zasad, które powinny być stosowane w wielu zapór
1. Stwórz swoją architekturę koncentratora i szprychy
   - Tworzenie sieci wirtualnej centrum przy użyciu usługi Azure Firewall Manager i sieci wirtualnych z głosami równorzędnych przy użyciu komunikacji równorzędnej sieci wirtualnej
<br>*Lub*<br>
    - Tworzenie sieci wirtualnej i dodawanie połączeń sieci wirtualnej i sieci wirtualnych z głosów równorzędnych przy użyciu komunikacji równorzędnej w sieci wirtualnej

3. Wybierz dostawców zabezpieczeń i skojarz zasady zapory. Obecnie obsługiwanym dostawcą jest tylko Zapora platformy Azure.

   - Odbywa się to podczas tworzenia sieci wirtualnej centrum
<br>*Lub*<br>
    - Konwertuj istniejącą sieć wirtualną na sieć wirtualną koncentratora. Możliwe jest również konwersja wielu sieci wirtualnych.

4. Skonfiguruj trasy definiowania tras przez użytkowników, aby kierować ruch do zapory sieci wirtualnej centrum.


### <a name="secured-virtual-hubs"></a>Zabezpieczone koncentratory wirtualne

1. Stwórz swoją architekturę koncentratora i szprychy

   - Utwórz zabezpieczoną usługę Virtual Hub przy użyciu usługi Azure Firewall Manager i dodaj połączenia z siecią wirtualną.<br>*Lub*<br>
   - Utwórz koncentrator wirtualnej sieci WAN i dodaj połączenia sieci wirtualnej.
2. Wybierz dostawców zabezpieczeń

   - Gotowe podczas tworzenia zabezpieczonego centrum wirtualnego.<br>*Lub*<br>
   - Konwertuj istniejące centrum wirtualnej sieci WAN na bezpieczne centrum wirtualne.
3. Tworzenie zasad zapory i kojarzenie jej z koncentratorem

   - Ma zastosowanie tylko w przypadku korzystania z Zapory platformy Azure.
   - Zasady zabezpieczeń innych firm jako usługi (SECaaS) są konfigurowane za pośrednictwem środowiska zarządzania partnerami.
4. Konfigurowanie ustawień trasy w celu kierowania ruchu do zabezpieczonego koncentratora

   - Łatwe kierowanie ruchu do zabezpieczonego koncentratora w celu filtrowania i rejestrowania bez tras zdefiniowanych przez użytkownika (UDR) w sieciach wirtualnych szprych za pomocą strony Ustawienia trasy zabezpieczonego koncentratora wirtualnego.

> [!NOTE]
> - Nie można mieć więcej niż jednego koncentratora na wirtualny wan na region. Ale można dodać wiele wirtualnych sieci WAN w regionie, aby to osiągnąć.
> - Nie można mieć nakładających się przestrzeni IP dla koncentratorów w vWAN.
> - Połączenia sieci wirtualnej koncentratora muszą znajdować się w tym samym regionie co koncentrator.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Zabezpiecz sieć w chmurze za pomocą usługi Azure Firewall Manager Preview przy użyciu portalu Azure](secure-cloud-network.md)