---
title: Oferty maszyny wirtualnej w witrynie Azure Marketplace | Dokumentacja firmy Microsoft
description: Omówienie procesu publikowania oferty maszyny Wirtualnej w witrynie Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/28/2018
ms.author: pbutlerm
ms.openlocfilehash: d3682d18fb849b2d851bae0986f9e61f216aaf2c
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639947"
---
# <a name="virtual-machine-offer"></a>Oferty maszyny wirtualnej

W tej sekcji przedstawiono elementy publikowanie maszyny wirtualnej (VM) i ma służyć jako przewodnik dla wydawcy w celu [portalu Azure Marketplace](https://azuremarketplace.microsoft.com).  Z tego punktu widzenia jest ona podzielony na następujące główne elementy:

- [Wymagania wstępne](./cpp-prerequisites.md) -wymieniono wymagania techniczne i biznesowe przed utworzeniem lub publikowanie oferty maszyny Wirtualnej
- [Tworzenie oferty maszyny Wirtualnej](./cpp-create-offer.md) -list kroki wymagane do utworzenia nowej maszyny Wirtualnej oferują przy użyciu wpisu [portalu Cloud Partner](https://cloudpartner.azure.com)
- [Tworzenie maszyny Wirtualnej, zasoby techniczne](./cpp-create-technical-assets.md) — w jaki sposób utworzyć technicznych zasobów dla rozwiązania maszyny Wirtualnej i sposobie konfigurowania tego pakietu jako Maszynę wirtualną oferty w portalu Azure Marketplace
- [Publikowanie oferty maszyny Wirtualnej](./cpp-publish-offer.md) — jak w celu przesłania oferty do opublikowania w portalu Azure Marketplace


## <a name="vm-publishing-process-flow"></a>Przepływ procesu publikowania maszyny Wirtualnej

Na poniższym diagramie przedstawiono ogólne kroki publikowanie oferty maszyny Wirtualnej. 

![Proces publikowania maszyny Wirtualnej](./media/publishvm_001.png)

1. Utwórz ofertę — wszystkie informacje i informacje o ofercie są skonfigurowane, w tym opis oferty, marketing, materiałów, informacje prawne, specyfikacje informacji i zasobów pomocy technicznej.

2. Tworzenie biznesowych i zasobów technicznych, — Tworzenie trwałych biznesowe (dokumenty prawne i materiały marketingowe) i zasoby techniczne dla skojarzonego rozwiązania (tutaj, maszyny wirtualne i dołączonych dysków). 

3. Tworzenie jednostki SKU — Utwórz skojarzone dla jednostek SKU, skojarzone z ofertą i przesłać je.  Unikatowe jednostki SKU jest wymagana dla każdego obrazu, który zamierzasz opublikować. 
 
4. Certyfikowanie i opublikować ofertę — po ukończeniu oferta i zasoby techniczne, można przesłać ofertę. Ten przesłany rozpocznie się proces publikowania, w którym rozwiązanie jest testowany, zweryfikowany, certyfikat, następnie "miejsce na żywo" w witrynie marketplace.  

## <a name="next-steps"></a>Kolejne kroki

Przed uznaniem następujące kroki, musisz spełnić [wymagania techniczne i biznesowe](./cpp-prerequisites.md) do publikowania maszyny Wirtualnej z systemem Microsoft Azure Marketplace. 
