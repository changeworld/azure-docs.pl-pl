---
title: Oferta obrazu kontenerów platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie procesu publikowania oferty kontenera w witrynie Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: pbutlerm
ms.openlocfilehash: e40e83e16ab2bfd43c3bb5fa38e52a778694e90e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473114"
---
# <a name="containers"></a>Containers

<table> <tr> <td>W tej sekcji opisano sposób publikowania obrazu kontenera do <a href="https://azuremarketplace.microsoft.com">portalu Azure Marketplace</a>.  
Obsługuje typ oferty kontenera obrazów kontenerów platformy Docker jest inicjowana jak dla <a href="https://docs.microsoft.com/azure/aks/index">usługi Azure Kubernetes Service</a> wystąpień lub <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">usługi Azure Container Instances</a> i hostowany na platformie <a href="https://docs.microsoft.com/azure/container-registry">usługi Azure Container Registry </a> repozytorium. </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Składniki oferty

W tej sekcji przedstawiono elementy publikowania kontenera i ma służyć jako przewodnik dla wydawcy w portalu Azure Marketplace. Publikowanie w podzielona na następujące główne elementy:

- [Wymagania wstępne](./cpp-prerequisites.md) -wymieniono wymagania techniczne i biznesowe przed utworzeniem lub publikowania z oferty opartej na kontenerze.
- [Utwórz ofertę](./cpp-create-offer.md) — zawiera listę czynności wymagane do utworzenia nowego wpisu oferty kontenera przy użyciu portalu Cloud Partner.
- [Przygotuj zasoby techniczne](./cpp-create-technical-assets.md) — jak utworzyć zasoby techniczne dotyczące rozwiązania kontenerów w ramach oferty w portalu Azure Marketplace.
- [Publikowanie oferty](./cpp-publish-offer.md) — jak w celu przesłania oferty do opublikowania w portalu Azure Marketplace.

## <a name="container-publishing-process"></a>Proces publikowania kontenera

Na poniższym diagramie przedstawiono ogólne kroki publikowanie oferty maszyny Wirtualnej.
![Kroki umożliwiające publikowanie oferty](./media/containers-offer-process.png)

Dostępne są następujące ogólne kroki umożliwiające publikowanie oferty kontenera:

1. Utwórz ofertę — zawiera szczegółowe informacje o ofercie. Informacje te obejmują: opis oferty, materiały marketingowe, informacje o pomocy technicznej i specyfikacje zasobów.
2. Tworzenie biznesowych i zasoby techniczne — Tworzenie trwałych biznesowe (dokumenty prawne i materiały marketingowe) i zasoby techniczne dla skojarzonego rozwiązania (obrazy kontenerów hostowanego na platformie usługi Azure Container Registry.
3. Tworzenie jednostki SKU - tworzenie dla jednostek SKU, skojarzone z ofertą. Unikatowe jednostki SKU jest wymagana dla każdego obrazu, w którym planujesz opublikować.
4. Certyfikowanie i opublikować ofertę — po ukończeniu oferta i zasoby techniczne, możesz przesłać ofertę. Ten przesłany rozpoczyna się proces publikowania. W trakcie tego procesu rozwiązanie jest testowany, zweryfikowany, certyfikowane, następnie "miejsce na żywo" w witrynie Azure Marketplace.

## <a name="next-steps"></a>Kolejne kroki

Przed uznaniem następujące kroki, musisz spełnić [wymagania techniczne i biznesowe](./cpp-prerequisites.md) publikowania kontener portalu Microsoft Azure Marketplace.