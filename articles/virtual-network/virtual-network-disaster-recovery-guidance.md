---
title: Ciągłość prowadzenia działalności biznesowej sieci wirtualnej | Dokumentacja firmy Microsoft
description: Dowiedz się, co należy zrobić w przypadku przerw w działaniu usługi Azure wpływających na sieciach wirtualnych platformy Azure.
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
ms.openlocfilehash: d993144006d1fb17d79ffee4f2da538264a309a4
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29854159"
---
# <a name="virtual-network--business-continuity"></a>Sieć wirtualna — ciągłość prowadzenia działalności biznesowej

## <a name="overview"></a>Przegląd
Sieć wirtualną (VNet) jest logiczną reprezentacja sieci w chmurze. Umożliwia definiowanie własnych prywatnych przestrzeni adresów IP i podzielić sieć na podsieci. Sieci wirtualne służy jako granicę zaufania, aby obsługiwać zasoby obliczeniowe, takich jak maszyny wirtualne platformy Azure i usługi w chmurze (role sieć web/proces roboczy). Sieć wirtualną umożliwia bezpośrednie prywatnej komunikacji IP między zasobami znajdujące się w nim. Możesz połączyć sieć wirtualną do sieci lokalnej za pośrednictwem bramy sieci VPN lub usługi ExpressRoute.

Sieci wirtualnej jest tworzony w zakresie regionu. Można utworzyć sieci wirtualnych o tej samej przestrzeni adresów w dwóch różnych regionach (na przykład nam wschodnie i nam zachód), ale nie można połączyć je ze sobą. 

## <a name="business-continuity"></a>Ciągłość działania

Może istnieć kilka różnych sposobów, aplikacja może zostać zerwane. Region można całkowicie obcięty z powodu klęski żywiołowej lub częściowe po awarii z powodu błędu wielu urządzeń lub usług. Wpływ na usługi sieci wirtualnej różni się w każdej z tych sytuacji.

**Pytanie: w przypadku wystąpienia awarii dla całego regionu, co należy zrobić? Na przykład, jeśli region jest całkowicie obcięty z powodu klęski żywiołowej? Co się dzieje z sieci wirtualnych hostowanych w regionie?**

Odpowiedź: sieci wirtualnej i zasoby w regionie, których dotyczy pozostaje niedostępne podczas przerw w działaniu usługi.

![Diagram prostego sieci wirtualnej](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**Pytanie: jakie czy można ponownie utworzyć tej samej sieci wirtualnej w innym regionie?**

A: sieci wirtualne są dość lekkie zasobów. Mogą wywoływać interfejsy API Azure w celu utworzenia sieci wirtualnej z tą samą przestrzenią adresów w innym regionie. Aby ponownie utworzyć w tym samym środowisku, która znajdowała się w regionie, których dotyczy, upewnij się wywołania interfejsu API ponownego wdrożenia usługi w chmurze w sieci web i roli proces roboczy i maszyn wirtualnych, które były. Jeśli masz połączenie lokalne, takie jak w ramach wdrożenia hybrydowego należy wdrożenia nowej bramy sieci VPN i nawiązanie połączenia z siecią lokalną.

Aby utworzyć sieć wirtualną, zobacz [utworzyć sieć wirtualną](manage-virtual-network.md#create-a-virtual-network).

**Pytanie: czy replika sieci wirtualnej w danym regionie zostać ponownie utworzone w innym regionie wcześniejsze?**

Odpowiedź: tak, można utworzyć dwie sieci wirtualnych za pomocą tego samego prywatnych przestrzeni adresów IP i zasobów w dwóch różnych regionach wcześniejsze. Jeśli prowadzą hosting usług internetowych w sieci wirtualnej, może wybrano Konfigurowanie Menedżera ruchu geograficznie kierowanie ruchem do regionu, który jest aktywny. Jednak nie można nawiązać połączenia dwóch sieci wirtualnych z tą samą przestrzenią adresów sieci lokalnej, ponieważ spowodowałoby to problemy routingu. W czasie awarii i utratę sieci wirtualnej w jednym regionie można połączyć sieć wirtualną w regionie dostępne z pasujących przestrzeni adresowej dla sieci lokalnej.

Aby utworzyć sieć wirtualną, zobacz [utworzyć sieć wirtualną](manage-virtual-network.md#create-a-virtual-network).

