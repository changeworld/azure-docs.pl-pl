---
title: Łączenie sieci wirtualnej platformy Azure z chmurąproste przy użyciu usługi ExpressRoute — rozwiązanie Azure VMware firmy CloudSimple
description: W tym artykule opisano sposób uzyskiwania informacji dotyczących komunikacji równorzędnej dla połączenia między siecią wirtualną platformy Azure a środowiskiem CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77563992"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Łączenie sieci wirtualnej platformy Azure z usługą CloudSimple przy użyciu usługi ExpressRoute

Sieć private cloud można rozszerzyć na sieć wirtualną platformy Azure i zasoby platformy Azure. Połączenie usługi ExpressRoute umożliwia dostęp do zasobów uruchomionych w ramach subskrypcji platformy Azure z chmury prywatnej.

## <a name="request-authorization-key"></a>Żądanie klucza autoryzacji

Klucz autoryzacji jest wymagany dla połączenia usługi ExpressRoute między chmurą prywatną a siecią wirtualną platformy Azure. Aby uzyskać klucz, złóż zgłoszenie do <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">pomocy technicznej</a>.  Użyj następujących informacji w żądaniu:

* Typ problemu: **Techniczne**
* Subskrypcja: **wybierz subskrypcję, w której jest wdrażana usługa CloudSimple**
* Usługa: **Rozwiązanie VMware by CloudSimple**
* Typ problemu: **Żądanie usługi**
* Podtyp problemu: **Klucz autoryzacji dla połączenia sieci wirtualnej platformy Azure**
* Temat: **Żądanie klucza autoryzacji dla połączenia sieci wirtualnej platformy Azure**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Uzyskaj informacje o komunikacji równorzędnej z portalu CloudSimple

Aby skonfigurować połączenie, należy ustanowić połączenie między siecią wirtualną platformy Azure a środowiskiem CloudSimple.  W ramach procedury należy podać identyfikator URI obwodu równorzędnego i klucz autoryzacji. Uzyskaj identyfikator URI i klucz autoryzacji z [portalu CloudSimple](access-cloudsimple-portal.md).  Wybierz **polecenie Sieć** w menu bocznym, a następnie wybierz pozycję Połączenie **sieciowe platformy Azure**. Lub wybierz **opcję Konto** w menu bocznym, a następnie wybierz pozycję **Połączenie sieciowe platformy Azure**.

Kopiowanie identyfikatora URI obwodu równorzędnego i klucza autoryzacji dla każdego z regionów przy użyciu ikony *kopiowania.* Dla każdego regionu CloudSimple, z którego chcesz się połączyć:

1. Kliknij **przycisk Kopiuj,** aby skopiować identyfikator URI. Wklej go do pliku, w którym można dodać do witryny Azure portal.  
2. Kliknij **przycisk Kopiuj,** aby skopiować klucz autoryzacji i wkleić go również do pliku.

Skopiuj klucz autoryzacji i identyfikator URI obwodu równorzędnego, który jest w stanie **Dostępne.**  **Stan używany** wskazuje, że klucz został już użyty do utworzenia połączenia sieci wirtualnej.

![Strona Połączenie sieci wirtualnej](media/virtual-network-connection.png)

Aby uzyskać szczegółowe informacje na temat konfigurowania sieci wirtualnej platformy Azure do łącza CloudSimple, zobacz [Łączenie środowiska cloudSimple private cloud z siecią wirtualną platformy Azure przy użyciu usługi ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Następne kroki

* [Połączenie sieci wirtualnej platformy Azure z chmurą prywatną](azure-expressroute-connection.md)
* [Łączenie się z siecią lokalną przy użyciu usługi Azure ExpressRoute](on-premises-connection.md)
