---
title: Oferta maszyny wirtualnej w portalu Azure Marketplace
description: Przegląd procesu publikowania oferty maszyny wirtualnej w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/04/2018
ms.author: pabutler
ms.openlocfilehash: 0313c4f2581615e1d1a3a57fc191db7d7cc6e825
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808556"
---
# <a name="virtual-machine-offer"></a>Oferta maszyny wirtualnej

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| W tej sekcji opisano sposób publikowania nowej oferty maszyny wirtualnej w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com). Pomoc techniczna jest świadczona zarówno dla maszyn wirtualnych opartych na systemie Windows, jak i Linux, zawierających wirtualny dysk twardy (VHD) systemu operacyjnego i zero lub więcej dysków VHD z danymi. | ![ikona maszyny wirtualnej](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Przegląd publikowania

Poniższy film wideo [optymalizuje ofertę portalu Azure Marketplace](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player), przedstawia szerokie Omówienie portalu Azure Marketplace, w tym informacje na temat publikowania w tym portalu Marketplace (przy użyciu rozwiązania maszyny wirtualnej), jak zoptymalizować środowisko użytkownika na stronie produktu opcjonalne środowisko testowania, sposób generowania potencjalnych klientów i ich użycia oraz optymalizacja zaangażowania klientów.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>Przepływ procesu publikowania maszyny wirtualnej

Na poniższym diagramie przedstawiono ogólne kroki publikowania oferty maszyn wirtualnych. 

![Proces publikowania maszyn wirtualnych](./media/publishvm_001.png)

1. Utwórz ofertę — wszystkie szczegóły i informacje o ofercie zostały skonfigurowane, w tym opis oferty, materiały marketingowe, informacje o pomocy technicznej i specyfikacje zasobów.

2. Twórz zasoby biznesowe i techniczne — Twórz zasoby biznesowe (dokumenty prawne i materiały marketingowe) oraz zasoby techniczne dla skojarzonego rozwiązania (tutaj: maszyny wirtualne i dołączone dyski). 

3. Utwórz jednostkę SKU — Utwórz skojarzone jednostki SKU skojarzone z ofertą i prześlij je.  Dla każdego obrazu, który ma zostać opublikowany, wymagana jest unikatowa jednostka SKU. 
 
4. Certyfikowanie i publikowanie oferty — po zakończeniu oferty i zasobów technicznych można przesłać ofertę. To zgłoszenie rozpocznie proces publikowania, w którym rozwiązanie zostanie przetestowane, zweryfikowane, certyfikowane, a następnie "trafia na żywo" w portalu Marketplace.  

## <a name="next-steps"></a>Następne kroki

Przed zainstalowaniem tych kroków należy spełnić [wymagania techniczne i biznesowe](./cpp-prerequisites.md) dotyczące publikowania maszyny wirtualnej w Microsoft Azure Marketplace. 
