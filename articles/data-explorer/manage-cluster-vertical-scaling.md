---
title: Skalowanie klastra Eksploratora danych usługi Azure, aby pomieścić zmiennym obciążeniem
description: W tym artykule opisano kroki, aby skalować w górę lub dół klaster Eksploratora danych usługi Azure, w zależności od zmieniających się żądanie.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: dc9ca8bb592e699d19835efeafb91e81408ae297
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571532"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Zarządzaj klastra skalowanie w górę do uwzględnienia zmiennym obciążeniem

Istnieją dwa przepływy pracy na potrzeby skalowania klastra Eksploratora danych usługi Azure:
1. [Skalowanie w poziomie](manage-cluster-horizontal-scaling.md), nazywane również skalowaniem na zewnątrz i do wewnątrz.
2. Skalowanie w pionie, nazywane również skalowaniem w górę i w dół.

W tym artykule przedstawiono sposób zarządzania, klastra skalowanie w pionie.

Odpowiednio rozmiaru klastra ma kluczowe znaczenie dla wydajności Eksploratora danych usługi Azure. Jednak żądanie w klastrze nie można przewidzieć z dokładnością bezwzględne. Rozmiar klastra statyczne może prowadzić do niepełnego lub overutilization, z których żadna nie jest idealnym rozwiązaniem. Lepszym rozwiązaniem jest *skalowania* klastra, dodając i usuwając pojemności i zasobów procesora CPU zmieniającymi się żądanie. 

## <a name="steps-to-configure-vertical-scaling"></a>Kroki, aby skonfigurować skalowanie w pionie

1. Przejdź do klastra. W obszarze **ustawienia**, wybierz opcję **skalowanie w górę**.

    Jest wyświetlana lista dostępnych jednostek SKU. Na przykład na poniższej ilustracji, tylko czterech jednostek SKU są dostępne.

    ![Skalowanie w górę](media/manage-cluster-vertical-scaling/scale-up.png)

    Jednostki SKU są wyłączone, ponieważ są one bieżąca jednostka SKU lub nie są dostępne w regionie, w której zlokalizowany jest klaster.

1. Aby zmienić jednostki SKU, wybierz jednostkę SKU ma i wybierz polecenie **wybierz** przycisku.

> [!NOTE]
> Pionowe proces skalowania może potrwać kilka minut, a w tym czasie zostanie zawieszony klastra. Należy pamiętać, że skalowanie w dół może negatywnie wpłynąć na wydajność klastra.

Teraz wykonano operację skalowania w górę lub w dół dla klastra Eksploratora danych usługi Azure.

Jeśli potrzebujesz pomocy w przypadku problemów skalowanie klastra [Otwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure portal.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie, skalowanie w poziomie klaster](manage-cluster-horizontal-scaling.md) do dynamicznego skalowania liczby wystąpień na podstawie metryk, które określisz.

* Monitoruj wykorzystanie zasobów przez podanych w tym artykule: [Monitorowanie wydajności, kondycji i użycia za pomocą metryk Eksploratora danych usługi Azure](using-metrics.md).

