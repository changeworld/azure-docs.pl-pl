---
title: Łączenie usługi Azure Virtual Network z programem ExpressRoute
description: Zawiera opis sposobu uzyskiwania informacji o komunikacji równorzędnej dla połączenia między usługą Azure Virtual Network a środowiskiem automatycznej synchronizacji
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1ff11bbafe6f9057ce70c799e0437691d89ccb40
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014406"
---
# <a name="connect-azure-virtual-network-to-avs-using-expressroute"></a>Łączenie usługi Azure Virtual Network z programem ExpressRoute

Możesz przyciągnąć swoją sieć prywatną w chmurze automatycznej synchronizacji do sieci wirtualnej platformy Azure i zasobów platformy Azure. Połączenie ExpressRoute pozwala uzyskać dostęp do zasobów uruchomionych w ramach subskrypcji platformy Azure z chmury prywatnej automatycznej synchronizacji.

## <a name="request-authorization-key"></a>Żądaj klucza autoryzacji

Klucz autoryzacji jest wymagany dla połączenia ExpressRoute między chmurą prywatną automatycznej synchronizacji a siecią wirtualną platformy Azure. Aby uzyskać klucz, należy skorzystać z <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">pomocy technicznej</a>. W żądaniu Skorzystaj z następujących informacji:

* Typ problemu: **techniczne**
* Subskrypcja: **Wybierz subskrypcję, w której jest wdrażana usługa automatycznej synchronizacji**
* Usługa: **rozwiązania VMware (Automatyczna synchronizacja)**
* Typ problemu: **żądanie obsługi**
* Podtyp problemu: **klucz autoryzacji dla połączenia sieci wirtualnej platformy Azure**
* Podmiot: **żądanie klucza autoryzacji dla połączenia sieci wirtualnej platformy Azure**

## <a name="get-peering-information-from-avs-portal"></a>Pobierz informacje o komunikacji równorzędnej z portalu automatycznej synchronizacji

Aby skonfigurować połączenie, należy nawiązać połączenie między usługą Azure Virtual Network a środowiskiem automatycznej synchronizacji. W ramach procedury należy podać identyfikator URI obwodu równorzędnego i klucz autoryzacji. Uzyskaj identyfikator URI i klucz autoryzacji z [portalu automatycznej synchronizacji](access-cloudsimple-portal.md). Wybierz pozycję **Sieć** w menu po stronie, a następnie wybierz pozycję **połączenie sieciowe platformy Azure**. Lub wybierz pozycję **konto** w menu po stronie, a następnie wybierz pozycję **połączenie sieciowe platformy Azure**.

Kopiuj identyfikator URI obwodu równorzędnego oraz klucz autoryzacji dla każdego z regionów przy użyciu ikony *kopiowania* . Dla każdego regionu automatycznej synchronizacji, który chcesz połączyć:

1. Kliknij przycisk **Kopiuj** , aby skopiować identyfikator URI. Wklej ją do pliku, który może być dostępny do dodania do Azure Portal. 
2. Kliknij przycisk **Kopiuj** , aby skopiować klucz autoryzacji i wkleić go do pliku.

Skopiuj klucz autoryzacji i identyfikator URI obwodu równorzędnego, który jest w stanie **dostępny** . **Użycie** stanu wskazuje, że klucz został już użyty do utworzenia połączenia sieci wirtualnej.

![Strona połączenia Virtual Network](media/virtual-network-connection.png)

Aby uzyskać szczegółowe informacje na temat konfigurowania linku sieci wirtualnej platformy Azure na potrzeby automatycznej synchronizacji, zobacz [łączenie środowiska chmury prywatnej w systemie Azure przy użyciu usługi ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Następne kroki

* [Połączenie sieci wirtualnej platformy Azure z chmurą prywatną](azure-expressroute-connection.md)
* [Nawiązywanie połączenia z siecią lokalną za pomocą usługi Azure ExpressRoute](on-premises-connection.md)
