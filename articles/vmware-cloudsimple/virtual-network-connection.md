---
title: Łączenie sieci wirtualnej platformy Azure z usługą CloudSimple przy użyciu usługi ExpressRoute
description: Zawiera opis sposobu uzyskiwania informacji o komunikacji równorzędnej dla połączenia między usługą Azure Virtual Network a środowiskiem CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ba2d2fd9c943fe55e82956d022f6ba9840a550f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536601"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Łączenie sieci wirtualnej platformy Azure z usługą CloudSimple przy użyciu usługi ExpressRoute

Sieć prywatną chmury można zwiększyć do sieci wirtualnej platformy Azure i zasobów platformy Azure. Połączenie ExpressRoute umożliwia dostęp do zasobów uruchomionych w ramach subskrypcji platformy Azure z chmury prywatnej.

## <a name="request-authorization-key"></a>Żądaj klucza autoryzacji

Klucz autoryzacji jest wymagany dla połączenia ExpressRoute między chmurą prywatną i siecią wirtualną platformy Azure. Aby uzyskać klucz, należy skorzystać z <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">pomocy technicznej</a>.  W żądaniu Skorzystaj z następujących informacji:

* Typ problemu: **Naukow**
* Subskrypcja: Wybierz subskrypcję, w której wdrożono usługę CloudSimple * *
* Usługa: **Rozwiązanie VMware według CloudSimple**
* Typ problemu: **Żądanie obsługi**
* Podtyp problemu: **Klucz autoryzacji dla połączenia sieci wirtualnej platformy Azure**
* Temat: **Żądanie klucza autoryzacji dla połączenia sieci wirtualnej platformy Azure**

## <a name="obtain-peering-information-for-azure-virtual-network-to-cloudsimple-connection"></a>Uzyskaj informacje o komunikacji równorzędnej dla usługi Azure Virtual Network z połączeniem CloudSimple

Aby skonfigurować połączenie, należy ustanowić łącze między usługą Azure Virtual Network i środowiskiem CloudSimple.  W ramach procedury należy podać identyfikator URI obwodu równorzędnego i klucz autoryzacji. Uzyskaj identyfikator URI i klucz autoryzacji z [portalu CloudSimple](access-cloudsimple-portal.md).  Wybierz pozycję **Sieć** w menu po stronie, a następnie wybierz pozycję **połączenie sieciowe platformy Azure**. Lub wybierz pozycję **konto** w menu po stronie, a następnie wybierz pozycję **połączenie sieciowe platformy Azure**.

Zwróć uwagę na ikony kopiowania dla identyfikatora URI obwodu równorzędnego oraz dla klucza autoryzacji dla każdego regionu. Dla każdej chmury prywatnej, którą chcesz połączyć:

* Kliknij przycisk **Kopiuj** , aby skopiować identyfikator URI. Wklej ją do pliku, który może być dostępny do dodania do Azure Portal.  
* Kliknij przycisk **Kopiuj** , aby skopiować klucz autoryzacji i wkleić go do pliku.

![Strona połączenia Virtual Network](media/network-virt-conn-page.png)

Aby uzyskać szczegółowe informacje na temat konfigurowania linku sieci wirtualnej platformy Azure do usługi CloudSimple, zobacz [łączenie środowiska chmury prywatnej CloudSimple z siecią wirtualną platformy Azure przy użyciu usługi ExpressRoute](azure-expressroute-connection.md).
