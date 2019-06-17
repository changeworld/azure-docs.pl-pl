---
title: Ciągłość prowadzenia działalności biznesowej w sieci wirtualnej | Dokumentacja firmy Microsoft
description: Dowiedz się, co należy zrobić w przypadku przerwy w działaniu usługi platformy Azure wpływających na sieciach wirtualnych platformy Azure.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick
ms.openlocfilehash: 68a9523dcc9c4dd84399c68fc7e31a692c011487
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523257"
---
# <a name="virtual-network--business-continuity"></a>Sieć wirtualna — ciągłość prowadzenia działalności biznesowej

## <a name="overview"></a>Omówienie
Virtual Network (VNet) jest logicznym odzwierciedleniem Twojej sieci w chmurze. Umożliwia definiowanie własnych przestrzeń prywatnych adresów IP i podzielić sieć na podsieci. Sieci wirtualne służy jako granicy zaufania, aby hostować zasoby obliczeniowe, takie jak Azure Virtual Machines i Cloud Services (role sieć web/proces roboczy). Sieci wirtualnej umożliwia bezpośrednią komunikację prywatnych adresów IP w między zasobami w nim. Możesz połączyć sieć wirtualną z siecią lokalną za pośrednictwem bramy sieci VPN lub usługi ExpressRoute.

Sieci wirtualnej jest tworzony w zakresie region. Możesz *tworzenie* sieci wirtualnych z tym samym przestrzeni adresów w dwóch różnych regionach (na przykład wschodnie stany USA i zachodnie stany USA), ale ponieważ mają tę samą przestrzeń adresową, nie możesz się połączyć je ze sobą. 

## <a name="business-continuity"></a>Ciągłość działalności biznesowej

Może istnieć kilka różnych sposobów, aplikacja może zostać przerwane. Region można całkowicie obcięte z powodu klęski żywiołowe lub częściowej awarii z powodu błędu wielu urządzeń i usług. Wpływ na usługi sieci wirtualnej różni się w każdej z tych sytuacji.

**Pyt.: Jeśli wystąpi awaria dla całego regionu, co należy zrobić? Na przykład, jeśli region jest całkowicie obcięte z powodu klęski żywiołowe? Co się dzieje z sieciami wirtualnymi, hostowane w regionie?**

Odp.: Sieć wirtualną i zasobów w regionie, których to dotyczy pozostają niedostępne podczas przerw w działaniu usługi.

![Diagram proste sieci wirtualnej](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**Pyt.: Co mogę ponownego tworzenia tej samej sieci wirtualnej w innym regionie?**

Odp.: Sieci wirtualne są stosunkowo lekkie zasobów. Możesz wywołać interfejsy API usługi Azure, aby utworzyć sieć wirtualną z tą samą przestrzenią adresów w innym regionie. Aby ponownie utworzyć środowisko, w którym znajdowała się w regionie, których dotyczy problem, upewnij się wywołań interfejsu API do ponownego wdrożenia usług Cloud Services w sieci web i ról procesów roboczych oraz maszyn wirtualnych, które trzeba było. W przypadku połączeń lokalnych, takich jak w ramach wdrożenia hybrydowego należy wdrożenia nowej bramy sieci VPN i nawiązanie połączenia z siecią lokalną.

Aby utworzyć sieć wirtualną, zobacz [tworzenie sieci wirtualnej](manage-virtual-network.md#create-a-virtual-network).

**Pyt.: Repliki sieci wirtualnej w danym regionie można utworzyć ponownie w innym regionie wcześniejsze?**

Odp.: Tak, możesz utworzyć dwie sieci wirtualne przy użyciu tego samego przestrzeń prywatnych adresów IP i zasobów w dwóch różnych regionach wcześniej. Jeśli hostujesz usługi połączone z Internetem w sieci wirtualnej może ustawiono Konfigurowanie Menedżera ruchu geograficzne kierowanie ruchu do regionu, który jest aktywny. Jednak nie można nawiązać połączenia dwóch sieci wirtualnych z tą samą przestrzenią adresów sieci lokalnej, ponieważ spowodowałoby to problemów z routingiem. W czasie awarii i straty w jednym regionie sieci wirtualnej sieci wirtualnej w regionie, dostępna, można połączyć z przestrzeni adresowej pasujące do sieci lokalnej.

Aby utworzyć sieć wirtualną, zobacz [tworzenie sieci wirtualnej](manage-virtual-network.md#create-a-virtual-network).

