---
title: Co to jest bezpieczny koncentrator wirtualny?
description: Informacje o bezpiecznych koncentratorach wirtualnych
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518439"
---
# <a name="what-is-a-secured-virtual-hub"></a>Co to jest bezpieczny koncentrator wirtualny?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Centrum wirtualne to zarządzana przez firmę Microsoft sieć wirtualna, która umożliwia łączność z innymi zasobami. Gdy centrum wirtualne jest tworzony z wirtualnej sieci WAN w witrynie Azure portal, wirtualna sieć wirtualna centrum i bramy (opcjonalnie) są tworzone jako jego składniki.

*Zabezpieczone* centrum wirtualne to [centrum wirtualnej sieci WAN platformy Azure](../virtual-wan/virtual-wan-about.md#resources) z skojarzonymi zasadami zabezpieczeń i routingu skonfigurowanym przez usługę Azure Firewall Manager. Bezpieczne koncentratory wirtualne umożliwia łatwe tworzenie architektur typu hub-and-spoke i przechodnie z natywnymi usługami zabezpieczeń dla zarządzania ruchem i ochrony. 

Zabezpieczonego koncentratora wirtualnego można użyć jako zarządzanej centralnej sieci wirtualnej bez łączności on-prem. Zastępuje centralną witrynę wirtualną, która była wcześniej wymagana dla wdrożenia zapory platformy Azure. Ponieważ zabezpieczony koncentrator wirtualny zapewnia automatyczne routing, nie ma potrzeby konfigurowania własnych UDR (tras zdefiniowanych przez użytkownika) do kierowania ruchu przez zaporę.

Istnieje również możliwość korzystania z zabezpieczonych koncentratorów wirtualnych w ramach pełnej architektury wirtualnej sieci WAN. Ta architektura zapewnia bezpieczną, zoptymalizowaną i zautomatyzowaną łączność z gałęzią do platformy Azure i za pośrednictwem platformy Azure. Można wybrać usługi do ochrony i zarządzania ruchem sieciowym, w tym Zapory Azure i innych zabezpieczeń innych firm jako usługi (SECaaS) dostawców.

## <a name="create-a-secured-virtual-hub"></a>Tworzenie zabezpieczonego koncentratora wirtualnego

Korzystając z Menedżera zapór w witrynie Azure portal, można utworzyć nowe zabezpieczone centrum wirtualne lub przekonwertować istniejące centrum wirtualne, które zostały wcześniej utworzone przy użyciu wirtualnej sieci WAN platformy Azure.

## <a name="next-steps"></a>Następne kroki

Aby utworzyć zabezpieczone centrum wirtualne i używać go do zabezpieczania i zarządzania siecią koncentratora i szprychy, zobacz [Samouczek: Zabezpiecz sieć w chmurze za pomocą usługi Azure Firewall Manager przy użyciu witryny Azure portal](secure-cloud-network.md).