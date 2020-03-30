---
title: Oferta obrazów kontenerów platformy Azure | Azure Marketplace
description: Omówienie procesu publikowania oferty kontenerów w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: 9c0b4ca6e9a26f13d1539845ca9fb43f31a1a9dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281718"
---
# <a name="containers"></a>Kontenery

<table> <tr> <td>W tej sekcji wyjaśniono, jak opublikować obraz kontenera w <a href="https://azuremarketplace.microsoft.com">portalu Azure Marketplace.</a>  
Typ oferty kontenera obsługuje obrazy kontenerów platformy Docker aprowizowane jako wystąpienia <a href="https://docs.microsoft.com/azure/aks/index">usługi Azure Kubernetes</a> lub <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">wystąpienia kontenera platformy Azure</a> i hostowane w repozytorium rejestru <a href="https://docs.microsoft.com/azure/container-registry">kontenerów platformy Azure.</a> </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Oferuj komponenty

W tej sekcji opisano elementy publikowania kontenera i jest przeznaczony jako przewodnik dla wydawcy do portalu Azure Marketplace. Wydawnictwo jest podzielone na następujące główne części:

- [Wymagania wstępne](./cpp-prerequisites.md) — wyświetla wymagania techniczne i biznesowe przed utworzeniem lub opublikowaniem oferty kontenera.
- [Utwórz ofertę](./cpp-create-offer.md) - wyświetla kroki wymagane do utworzenia nowego wpisu oferty kontenera przy użyciu portalu Cloud Partner Portal.
- [Przygotowanie zasobów technicznych](./cpp-create-technical-assets.md) — jak utworzyć zasoby techniczne dla rozwiązania kontenera jako oferty w portalu Azure Marketplace.
- [Opublikuj ofertę](./cpp-publish-offer.md) — jak przesłać ofertę do publikowania w portalu Azure Marketplace.

## <a name="container-publishing-process"></a>Proces publikowania kontenerów

Na poniższym diagramie przedstawiono kroki wysokiego poziomu w publikowaniu oferty maszyny Wirtualnej.
![Kroki publikowania oferty](./media/containers-offer-process.png)

Kroki wysokiego poziomu publikowania oferty kontenera są następujące:

1. Utwórz ofertę — podaj szczegółowe informacje o ofercie. Informacje te obejmują: opis oferty, materiały marketingowe, informacje o pomocy technicznej i specyfikacje zasobów.
2. Tworzenie zasobów biznesowych i technicznych — tworzenie zasobów biznesowych (dokumentów prawnych i materiałów marketingowych) i zasobów technicznych dla skojarzonego rozwiązania (obrazy kontenerów hostowane w rejestrze kontenerów platformy Azure.
3. Utwórz jednostkę SKU — utwórz jednostki SKU skojarzone z ofertą. Unikatowa jednostka SKU jest wymagana dla każdego obrazu, który planujesz opublikować.
4. Poświadczaj i publikuj ofertę - Po zakończeniu oferty i aktywów technicznych można złożyć ofertę. To przesłanie rozpoczyna proces publikowania. Podczas tego procesu rozwiązanie jest testowane, sprawdzane, certyfikowane, a następnie "przechodzi na rynek" w portalu Azure Marketplace.

## <a name="next-steps"></a>Następne kroki

Przed rozważeniem tych kroków należy spełnić [wymagania techniczne i biznesowe dotyczące](./cpp-prerequisites.md) publikowania kontenera w portalu Microsoft Azure Marketplace.
