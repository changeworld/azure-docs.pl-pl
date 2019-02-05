---
title: Skalowanie klastrze Eksplorator danych platformy Azure w celu uwzględnienia zmiennym obciążeniem
description: W tym artykule opisano kroki w celu skalowania w poziomie i klaster Eksploratora danych usługi Azure, w zależności od zmieniających się żądanie w skali.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 213a49d87d5e9f801bb17604a322c231a4e3dee2
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735816"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Zarządzanie w skali klastra do uwzględnienia zmiennym obciążeniem

Odpowiednio rozmiaru klastra ma kluczowe znaczenie dla wydajności Eksploratora danych usługi Azure. Ale nie można przewidzieć zapotrzebowanie na klastrze z dokładnością 100%. Rozmiar klastra statyczne może prowadzić do niepełnego wykorzystania lub nadmiernego wykorzystania, z których żadna nie jest idealnym rozwiązaniem. Lepszym rozwiązaniem jest *skalowania* klastra, dodając i usuwając pojemność i użycie Procesora zmieniającymi się żądanie. W tym artykule dowiesz się, jak zarządzać skalowania klastra się.

Przejdź do klastra, a w obszarze **ustawienia** wybierz **skalowanie w górę**.

Możesz następnie zapewnianą listę jednostek SKU, które są dostępne. Możesz wybrać z listy włączonych kart. Na przykład w poniżej rysunek jest tylko jednej jednostki SKU, które można wybrać wartość D14_vs.

![Skalowanie w górę](media/manage-cluster-scale-up/scale-up.png)

D13_v2 jest wyłączona, ponieważ jest to bieżąca jednostka SKU klastra. P8 i L16 są wyłączone, ponieważ są niedostępne w regionie, w której jest klaster.

Zmiana jednostki SKU, wystarczy kliknąć jednostki SKU, chcesz użyć, a następnie kliknij przycisk **wybierz** przycisku.

[!NOTE] Skala, proces może potrwać kilka minut, a w tym czasie zostanie zawieszony klastra. Należy pamiętać, że skalowanie w dół może negatywnie wpłynąć na wydajność klastra.

Jeśli potrzebujesz pomocy z klastrem skalowanie problemy, otwórz żądanie obsługi w [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).