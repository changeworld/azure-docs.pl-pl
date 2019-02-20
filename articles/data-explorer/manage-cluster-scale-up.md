---
title: Skalowanie klastrze Eksplorator danych platformy Azure w celu uwzględnienia zmiennym obciążeniem
description: W tym artykule opisano kroki, aby skalować w górę i w dół klaster Eksploratora danych usługi Azure, w zależności od zmieniających się żądanie.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: bc3f97c798f5e040908e8103c00d3f015f8c824d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415338"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Zarządzaj klastra skalowanie w górę do uwzględnienia zmiennym obciążeniem

Odpowiednio rozmiaru klastra ma kluczowe znaczenie dla wydajności Eksploratora danych usługi Azure. Ale nie można przewidzieć zapotrzebowanie na klastrze z dokładnością 100%. Rozmiar klastra statyczne może prowadzić do niepełnego wykorzystania lub nadmiernego wykorzystania, z których żadna nie jest idealnym rozwiązaniem. Lepszym rozwiązaniem jest *skalowania* klastra, dodając i usuwając pojemność i użycie Procesora zmieniającymi się żądanie. Istnieją dwa przepływy pracy na potrzeby skalowania, skalowanie w górę i skalowania w poziomie. W tym artykule pokazano, jak zarządzać klastrem skalowanie w górę.

1. Przejdź do klastra, a w obszarze **ustawienia** wybierz **skalowanie w górę**.

    Otrzymuje listę dostępnych jednostek SKU. Na przykład na poniższej ilustracji istnieje tylko jeden dostępne jednostki SKU: D14_V2.

    ![Skalowanie w górę](media/manage-cluster-scale-up/scale-up.png)

    D13_V2 jest wyłączona, ponieważ bieżąca jednostka SKU klastra. P8 i L16 są wyłączone, ponieważ są niedostępne w regionie, w której zlokalizowany jest klaster.

1. Aby zmienić jednostki SKU, wybierz jednostkę SKU, a następnie naciśnij **wybierz** przycisku.

> [!NOTE]
> Skala, proces może potrwać kilka minut, a w tym czasie zostanie zawieszony klastra. Należy pamiętać, że skalowanie w dół może negatywnie wpłynąć na wydajność klastra.

Już teraz wykonywać operację skalowania w górę lub w dół dla klastra Eksploratora danych usługi Azure. Można również wykonać [klastra skalowalnego w poziomie](manage-cluster-scale-out.md), znanego również jako automatyczne skalowanie w celu skalowania dynamicznie w oparciu metryki, które określisz.

Jeśli potrzebujesz pomocy z klastrem skalowanie problemy, otwórz żądanie obsługi w [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
