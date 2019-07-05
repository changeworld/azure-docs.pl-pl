---
title: Skalowanie klastra Eksploratora danych usługi Azure, aby pomieścić zmiennym obciążeniem
description: W tym artykule opisano kroki, aby skalować w górę lub dół klaster Eksploratora danych usługi Azure, w zależności od zmieniających się żądanie.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 9c3eb82f09c591f313175ef564b1a20075fdcbd4
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537882"
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

Jeśli potrzebujesz pomocy w przypadku problemów skalowanie klastra [Otwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure portal.

## <a name="next-steps"></a>Kolejne kroki

* [Zarządzaj klastra skalowalnego w poziomie](manage-cluster-scale-out.md) do dynamicznego skalowania liczby wystąpień na podstawie metryk, które określisz.

* Monitoruj wykorzystanie zasobów przez podanych w tym artykule: [Monitorowanie wydajności, kondycji i użycia za pomocą metryk Eksploratora danych usługi Azure](using-metrics.md).

