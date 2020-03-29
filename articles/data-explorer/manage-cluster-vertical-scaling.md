---
title: Zarządzanie skalowaniem pionowym klastra (skalowanie w górę) w celu dopasowania do popytu w Eksploratorze danych platformy Azure
description: W tym artykule opisano kroki skalowania w górę i skalowania w dół klastra usługi Azure Data Explorer na podstawie zmieniającego się popytu.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 95275598febae2b6b0355a7bc3e512490dae500d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560443"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>Zarządzanie skalowaniem pionowym klastra (skalowanie w górę) w Eksploratorze danych platformy Azure w celu uwzględnienia zmieniającego się popytu

Odpowiednie rozmiary klastra ma kluczowe znaczenie dla wydajności Eksploratora danych platformy Azure. Rozmiar klastra statycznego może prowadzić do niepełnego wykorzystania lub nadmiernego wykorzystania, z których żaden nie jest idealny.

Ponieważ popytu na klastrze nie można przewidzieć z dokładnością bezwzględną, lepszym podejściem jest *skalowanie* klastra, dodawanie i usuwanie pojemności i zasobów procesora przy zmieniającym się zapotrzebowaniu. 

Istnieją dwa przepływy pracy do skalowania klastra usługi Azure Data Explorer:

* [Skalowanie poziome](manage-cluster-horizontal-scaling.md), zwane również skalowaniem i wyskakiwem.
* Skalowanie pionowe, nazywane również skalowaniem w górę i w dół.

W tym artykule opisano przepływ pracy skalowania pionowego:

## <a name="configure-vertical-scaling"></a>Konfigurowanie skalowania pionowego

1. W witrynie Azure portal przejdź do zasobu klastra usługi Azure Data Explorer. W obszarze **Ustawienia**wybierz pozycję **Skaluj w górę**.

1. W oknie **Skalowanie w górę** zostanie wyświetlona lista dostępnych jednostek SKU dla klastra. Na przykład na poniższej ilustracji dostępne są tylko cztery jednostki SKU.

    ![Skalowanie w górę](media/manage-cluster-vertical-scaling/scale-up.png)

    Jednostki SKU są wyłączone, ponieważ są bieżącą jednostką SKU lub nie są dostępne w regionie, w którym znajduje się klaster.

1. Aby zmienić jednostkę SKU, wybierz nową jednostkę SKU i kliknij przycisk **Wybierz**.

> [!NOTE]
> * Proces skalowania pionowego może potrwać kilka minut, a w tym czasie klaster zostanie zawieszony. 
> * Skalowanie w dół może uszkodzić wydajność klastra.
> * Cena jest szacunkowa dla maszyn wirtualnych klastra i kosztów usługi Azure Data Explorer. Pozostałe koszty nie są wliczone w cenę. Zobacz stronę [szacowania kosztów](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) usługi Azure Data Explorer, aby uzyskać informacje o oszacowaniu i [stronie cennika](https://azure.microsoft.com/pricing/details/data-explorer/) usługi Azure Data Explorer, aby uzyskać pełne informacje o cenach.

Skonfigurowano skalowanie pionowe dla klastra usługi Azure Data Explorer. Dodaj kolejną regułę skalowania poziomego. Jeśli potrzebujesz pomocy dotyczącej problemów ze skalowaniem klastra, [otwórz żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure portal.

## <a name="next-steps"></a>Następne kroki

* [Zarządzaj skalowaniem w poziomie klastra,](manage-cluster-horizontal-scaling.md) aby dynamicznie skalować liczbę wystąpień w poziomie na podstawie określonych metryk.

* Monitorowanie użycia zasobów przez następującemu artykułem: [Monitorowanie wydajności, kondycji i użycia usługi Azure Data Explorer za pomocą metryk](using-metrics.md).

