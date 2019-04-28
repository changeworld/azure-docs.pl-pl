---
title: Skalowanie klastra Eksploratora danych usługi Azure, aby pomieścić zmiennym obciążeniem
description: W tym artykule opisano kroki, aby skalować w górę lub dół klaster Eksploratora danych usługi Azure, w zależności od zmieniających się żądanie.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 1f130f79b6b6924559e1693e1eef8ced2972b3d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60758711"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Zarządzaj klastra skalowanie w górę do uwzględnienia zmiennym obciążeniem

Istnieją dwa przepływy pracy na potrzeby skalowania klastra Eksploratora danych usługi Azure: skalowanie w górę i [skalowalnego w poziomie](manage-cluster-scale-out.md). W tym artykule przedstawiono sposób zarządzania klastrem skalowanie w górę.

Odpowiednio rozmiaru klastra ma kluczowe znaczenie dla wydajności Eksploratora danych usługi Azure. Jednak żądanie w klastrze nie można przewidzieć z dokładnością bezwzględne. Rozmiar klastra statyczne może prowadzić do niepełnego lub overutilization, z których żadna nie jest idealnym rozwiązaniem. Lepszym rozwiązaniem jest *skalowania* klastra, dodając i usuwając pojemności i zasobów procesora CPU zmieniającymi się żądanie. 

## <a name="steps-to-scale-up"></a>Kroki, aby skalować w górę
1. Przejdź do klastra. W obszarze **ustawienia**, wybierz opcję **skalowanie w górę**.

    Jest wyświetlana lista dostępnych jednostek SKU. Na przykład na poniższej ilustracji, tylko czterech jednostek SKU są dostępne.

    ![Skalowanie w górę](media/manage-cluster-scale-up/scale-up.png)

    Jednostki SKU są wyłączone, ponieważ są one bieżąca jednostka SKU lub nie są dostępne w regionie, w której zlokalizowany jest klaster.

1. Aby zmienić jednostki SKU, wybierz jednostkę SKU ma i wybierz polecenie **wybierz** przycisku.

> [!NOTE]
> Skalowanie w górę może potrwać kilka minut, a w tym czasie zostanie zawieszony klastra. Należy pamiętać, że skalowanie w dół może negatywnie wpłynąć na wydajność klastra.

Teraz wykonano operację skalowania w górę lub w dół dla klastra Eksploratora danych usługi Azure.

## <a name="next-steps"></a>Kolejne kroki
Możesz również [Zarządzanie klastra skalowalnego w poziomie](manage-cluster-scale-out.md) do dynamicznego skalowania liczby wystąpień na podstawie metryk, które określisz.

Jeśli potrzebujesz pomocy w przypadku problemów skalowanie klastra [Otwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure portal.

Monitoruj wykorzystanie zasobów przez podanych w tym artykule: [Monitorowanie wydajności, kondycji i użycia za pomocą metryk Eksploratora danych usługi Azure](using-metrics.md).
