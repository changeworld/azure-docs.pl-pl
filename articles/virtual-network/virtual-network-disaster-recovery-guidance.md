---
title: Ciągłość działania sieci wirtualnej | Dokumenty firmy Microsoft
description: Dowiedz się, co należy zrobić w przypadku zakłóceń w działaniu usługi platformy Azure, które mają wpływ na sieci wirtualne platformy Azure.
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
ms.author: narayan
ms.reviewer: aglick
ms.openlocfilehash: 3f91d24bff0bec540ff0e7964f21c2f47c03638c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876167"
---
# <a name="virtual-network--business-continuity"></a>Sieć wirtualna – ciągłość działania

## <a name="overview"></a>Omówienie
Sieć wirtualna (VNet) jest logiczną reprezentacją sieci w chmurze. Umożliwia zdefiniowanie własnej prywatnej przestrzeni adresowej IP i posegmentowanie sieci na podsieci. Sieci wirtualne służy jako granica zaufania do obsługi zasobów obliczeniowych, takich jak maszyny wirtualne platformy Azure i usługi w chmurze (role sieci web/procesu roboczego). Sieć wirtualna umożliwia bezpośrednią prywatną komunikację IP między zasobami w niej hostowanymi. Sieć wirtualną można połączyć z siecią lokalną za pośrednictwem bramy sieci VPN lub usługi ExpressRoute.

Sieci wirtualnej jest tworzony w zakresie regionu. Sieci wirtualne z tą samą przestrzenią adresową można *utworzyć* w dwóch różnych regionach (na przykład wschodnie stany USA i zachodnie stany USA), ale ponieważ mają tę samą przestrzeń adresową, nie można ich połączyć. 

## <a name="business-continuity"></a>Ciągłość działalności biznesowej

Może istnieć kilka różnych sposobów, że aplikacja może zostać zakłócony. Region może zostać całkowicie odcięty z powodu klęski żywiołowej lub częściowej awarii z powodu awarii wielu urządzeń lub usług. Wpływ na usługę sieci wirtualnej jest różny w każdej z tych sytuacji.

**Pyt.: Jeśli wystąpi awaria dla całego regionu, co mam zrobić? Na przykład, jeśli region jest całkowicie odcięty z powodu klęski żywiołowej? Co się stanie z sieciami wirtualnymi hostowanymi w regionie?**

Odp.: Sieć wirtualna i zasoby w regionie, którego dotyczy problem, pozostają niedostępne w czasie przerwy w świadczeniu usług.

![Prosty diagram sieci wirtualnej](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**Pyt.: Co można zrobić, aby odtworzyć tę samą sieć wirtualną w innym regionie?**

Odp.: Sieci wirtualne są dość lekkimi zasobami. Można wywołać interfejsy API platformy Azure, aby utworzyć sieć wirtualną z tej samej przestrzeni adresowej w innym regionie. Aby ponownie utworzyć to samo środowisko, które było obecne w regionie, którego dotyczy problem, należy wykonać wywołania interfejsu API w celu ponownego rozmieszczenia ról sieci web i procesu roboczego usług w chmurze oraz maszyn wirtualnych. Jeśli masz łączność lokalną, na przykład w przypadku wdrożenia hybrydowego, musisz wdrożyć nową bramę sieci VPN i połączyć się z siecią lokalną.

Aby utworzyć sieć wirtualną, zobacz [Tworzenie sieci wirtualnej](manage-virtual-network.md#create-a-virtual-network).

**Pyt.: Czy replika sieci wirtualnej w danym regionie można odtworzyć w innym regionie z wyprzedzeniem?**

Odp.: Tak, można utworzyć dwie sieci wirtualne przy użyciu tej samej prywatnej przestrzeni adresów IP i zasobów w dwóch różnych regionach z wyprzedzeniem. Jeśli hostujesz usługi internetowe w sieci wirtualnej, możesz skonfigurować Usługę Traffic Manager do kierowania geograficznego ruchu do regionu, który jest aktywny. Nie można jednak połączyć dwóch sieci wirtualnych z tą samą przestrzenią adresową z siecią lokalną, ponieważ spowodowałoby to problemy z routingiem. W momencie awarii i utraty sieci wirtualnej w jednym regionie można połączyć drugą sieć wirtualną w dostępnym regionie z pasującą przestrzenią adresową z siecią lokalną.

Aby utworzyć sieć wirtualną, zobacz [Tworzenie sieci wirtualnej](manage-virtual-network.md#create-a-virtual-network).

