---
title: Oferta maszyny wirtualnej w portalu Azure Marketplace
description: Omówienie procesu publikowania oferty maszyny Wirtualnej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: dsindona
ms.openlocfilehash: 939a5f6a4c70a8a1229507e0357cb588c17152fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288771"
---
# <a name="virtual-machine-offer"></a>Oferta maszyny wirtualnej

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| W tej sekcji wyjaśniono, jak opublikować nową ofertę maszyny wirtualnej w [portalu Azure Marketplace.](https://azuremarketplace.microsoft.com) Obsługa jest dostępna zarówno dla maszyn wirtualnych opartych na systemie Windows, jak i linux, zawierających wirtualny dysk twardy systemu operacyjnego (VHD) i zero lub więcej dysków VHD danych. | ![ikona maszyny wirtualnej](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Omówienie publikowania

Poniższy klip [wideo: Optymalizuj ofertę portalu Azure Marketplace](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player), przedstawiono ogólny przegląd portalu Azure Marketplace, w tym sposób publikowania w tym portalu marketplace (przy użyciu rozwiązania maszyny wirtualnej), jak zoptymalizować środowisko użytkownika za pomocą strony produktu i opcjonalne środowisko z dysku testowego, jak generowane są potencjalni klienci i jak można z nich korzystać, a także zoptymalizować zaangażowanie klientów.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>Przepływ procesu publikowania maszyn wirtualnych

Na poniższym diagramie przedstawiono kroki wysokiego poziomu w publikowaniu oferty maszyny Wirtualnej. 

![Proces publikowania maszyn wirtualnych](./media/publishvm_001.png)

1. Utwórz ofertę — wszystkie szczegóły i informacje o ofercie są konfigurowane, w tym opis oferty, materiały marketingowe, informacje prawne, pomoc techniczna i specyfikacje zasobów.

2. Tworzenie zasobów biznesowych i technicznych — tworzenie zasobów biznesowych (dokumentów prawnych i materiałów marketingowych) oraz zasobów technicznych dla skojarzonego rozwiązania (tutaj maszyny wirtualne i dołączone dyski). 

3. Utwórz jednostkę SKU — utwórz skojarzone jednostki SKU skojarzone z ofertą i prześlij je.  Unikatowa jednostka SKU jest wymagana dla każdego obrazu, który planujesz opublikować. 
 
4. Poświadczaj i publikuj ofertę - Po zakończeniu oferty i aktywach technicznych można przesłać ofertę. To przesyłanie rozpocznie proces publikowania, w którym rozwiązanie jest testowane, weryfikowane, certyfikowane, a następnie "idzie na żywo" na rynku.  

## <a name="next-steps"></a>Następne kroki

Przed rozważeniem tych kroków należy spełnić [wymagania techniczne i biznesowe dotyczące](./cpp-prerequisites.md) publikowania maszyny wirtualnej w portalu Microsoft Azure Marketplace. 
