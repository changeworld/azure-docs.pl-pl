---
title: Zarządzanie skalowaniem pionowym klastra (skalowanie w górę) w celu dopasowania do popytu na platformie Azure Eksplorator danych
description: W tym artykule opisano kroki umożliwiające skalowanie klastra Eksplorator danych platformy Azure w górę i w dół w zależności od zmieniających się wymagań.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 95275598febae2b6b0355a7bc3e512490dae500d
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560443"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>Zarządzanie skalowaniem pionowym klastra (skalowanie w górę) w usłudze Azure Eksplorator danych w celu uwzględnienia zmiany zapotrzebowania

Odpowiednio ustalanie wielkości klastra ma kluczowe znaczenie dla wydajności Eksplorator danych platformy Azure. Statyczny rozmiar klastra może prowadzić do użycia lub nadmiernego użycia, nie jest idealnym rozwiązaniem.

Ponieważ zapotrzebowanie na klaster nie może być przewidywane z dokładnością absolutną, lepszym rozwiązaniem jest *skalowanie* klastra, Dodawanie i usuwanie zasobów pojemności i procesora przy użyciu zmieniających się wymagań. 

Istnieją dwa przepływy pracy umożliwiające skalowanie klastra Eksplorator danych platformy Azure:

* [Skalowanie w poziomie](manage-cluster-horizontal-scaling.md), nazywane również skalowaniem do wewnątrz i na zewnątrz.
* Skalowanie w pionie, nazywane również skalowaniem w górę i w dół.

W tym artykule opisano przepływ pracy skalowania w pionie:

## <a name="configure-vertical-scaling"></a>Konfiguruj skalowanie w pionie

1. W Azure Portal przejdź do zasobu klastra usługi Azure Eksplorator danych. W obszarze **Ustawienia**wybierz pozycję **Skaluj w górę**.

1. W oknie **skalowanie w górę** zostanie wyświetlona lista dostępnych jednostek SKU dla danego klastra. Na przykład na poniższej ilustracji są dostępne tylko cztery jednostki SKU.

    ![Skalowanie w górę](media/manage-cluster-vertical-scaling/scale-up.png)

    Jednostki SKU są wyłączone, ponieważ są one bieżącą jednostką SKU lub nie są dostępne w regionie, w którym znajduje się klaster.

1. Aby zmienić jednostkę SKU, wybierz nową jednostkę SKU i kliknij przycisk **Wybierz**.

> [!NOTE]
> * Proces skalowania w pionie może potrwać kilka minut, a w tym czasie klaster zostanie zawieszony. 
> * Skalowanie w dół może uszkodzić wydajność klastra.
> * Cena to oszacowanie maszyn wirtualnych klastra i kosztów usługi Azure Eksplorator danych. Inne koszty nie są uwzględniane. Aby uzyskać pełne informacje o cenach, [zobacz Eksplorator danych stronę](https://azure.microsoft.com/pricing/details/data-explorer/) usługi Azure Eksplorator danych [Cost szacowania](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) .

Skalowanie w pionie dla klastra usługi Azure Eksplorator danych zostało skonfigurowane. Dodaj kolejną regułę skalowania w poziomie. Jeśli potrzebujesz pomocy w rozwiązywaniu problemów ze skalowaniem klastra, [Otwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w Azure Portal.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie skalowaniem w poziomie klastra](manage-cluster-horizontal-scaling.md) w celu dynamicznego skalowania liczby wystąpień na podstawie określonych metryk.

* Monitoruj użycie zasobów, wykonując czynności opisane w tym artykule: [monitorowanie wydajności, kondycji i użycia usługi Azure Eksplorator danych przy użyciu metryk](using-metrics.md).

