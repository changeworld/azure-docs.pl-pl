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
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: bbe757ccd1d6a37cbcf04f3ecd6dd088ef1ff211
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343138"
---
# <a name="virtual-machine-offer"></a>Oferta maszyny wirtualnej

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| W tej sekcji opisano sposób publikowania nowej oferty maszyny wirtualnej do [portalu Azure Marketplace](https://azuremarketplace.microsoft.com). Pomoc techniczna jest świadczona systemem Windows i opartych na systemie Linux maszyn wirtualnych, zawierający system operacyjny wirtualnego dysku twardego (VHD) i zero lub więcej danych wirtualnych dysków twardych. | ![Ikona maszyny wirtualnej](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Omówienie publikowania

Poniższy klip wideo [zoptymalizować Your Azure Marketplace oferuje](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player), przedstawia ogólne omówienie w portalu Azure Marketplace, w tym sposób publikowania w tym portalu marketplace (przy użyciu rozwiązania maszyny wirtualnej), jak optymalizować środowisko użytkownika Strona produktu i opcjonalnie środowisko wersji testowej jak prowadzi użytkownika są generowane i jak można korzystać z nich i zoptymalizować zaangażowania użytkowników.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>Przepływ procesu publikowania maszyny Wirtualnej

Na poniższym diagramie przedstawiono ogólne kroki publikowanie oferty maszyny Wirtualnej. 

![Proces publikowania maszyny Wirtualnej](./media/publishvm_001.png)

1. Utwórz ofertę — wszystkie informacje i informacje o ofercie są skonfigurowane, w tym opis oferty, marketing, materiałów, informacje prawne, specyfikacje informacji i zasobów pomocy technicznej.

2. Tworzenie biznesowych i zasobów technicznych, — Tworzenie trwałych biznesowe (dokumenty prawne i materiały marketingowe) i zasoby techniczne dla skojarzonego rozwiązania (tutaj, maszyny wirtualne i dołączonych dysków). 

3. Tworzenie jednostki SKU — Utwórz skojarzone dla jednostek SKU, skojarzone z ofertą i przesłać je.  Unikatowe jednostki SKU jest wymagana dla każdego obrazu, który zamierzasz opublikować. 
 
4. Certyfikowanie i opublikować ofertę — po ukończeniu oferta i zasoby techniczne, można przesłać ofertę. Ten przesłany rozpocznie się proces publikowania, w którym rozwiązanie jest testowany, zweryfikowany, certyfikat, następnie "miejsce na żywo" w witrynie marketplace.  

## <a name="next-steps"></a>Kolejne kroki

Przed uznaniem następujące kroki, musisz spełnić [wymagania techniczne i biznesowe](./cpp-prerequisites.md) do publikowania maszyny Wirtualnej z systemem Microsoft Azure Marketplace. 
